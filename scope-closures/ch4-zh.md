# 你不了解JS然而:范围与关闭 - 第二版
# 第4章:围绕全球范围

第三章多次提到"全球范围",但你可能仍然想知道为什么一个节目最外在的范围在现代联署材料中都是那么重要. 现在绝大多数工作是在职能和模块范围内而不是在全球范围内完成的。

仅仅断言“避免利用全球范围”并用它做是否足够好?

JS计划的全球范围是一个丰富的话题,其效用和细微度远比你想象的要大得多. 本章首先探讨全球范围如何(仍然)有用和与今天编写联署材料方案相关,然后探讨在不同联署材料环境中,全球范围在何地和如何获取方面的差异。

充分理解全球范围对于你运用词汇范围来构建程序至关重要。

## 为什么是全球范围?

读者可能不会感到惊讶,大多数应用程序是由多个(有时是很多)个人JS文件组成的. 所以JS引擎如何在单个运行时将所有这些独立的文件缝合在一起?

关于浏览器执行的应用程序,主要有三种方式.

首先,如果你直接使用ES模块(没有将它们转录到其他模块-捆绑格式),这些文件是由JS环境单独加载的. 那么每个模块`import`提及它需要访问的任何其他模块。 单独的模块文件完全通过这些共享的导入而相互合作,而不需要任何共享的外部范围。

其次,如果你在构建过程中使用一个捆绑器,那么所有的文件一般在交付到浏览器和JS引擎之前都会被拼凑在一起,然后只处理一个大文件. 即使应用程序的所有部件都放在一个文件内,但每个部件都需要某种机制来登记由其他部件提及的*名称*,以及某种设施来进行访问。

在一些构建设置中,文件的全部内容被包在一个单一的附着范围中,例如一个包裹功能,通用模块(UMD-见附录A)等. 每块都可以通过该共享范围的局部变量来注册,以便从其他块中访问. 例如:

```js
(function wrappingOuterScope(){
    var moduleOne = (function one(){
        // ..
    })();

    var moduleTwo = (function two(){
        // ..

        function callModuleOne() {
            moduleOne.someMethod();
        }

        // ..
    })();
})();
```

如所示,`moduleOne`和`moduleTwo`内置本地变量`wrappingOuterScope()`函数范围被宣布,以便这些模块能够互相访问以进行合作。

范围`wrappingOuterScope()`是一个函数,而不是整个环境的全球范围,它确实起到某种“应用全范围”的作用,一个可以储存所有顶级标识的桶,尽管不是真正的全球范围。 这有点像一个站 在全球范围在这方面。

And finally, the third way: whether a bundler tool is used for an application, or whether the (non-ES module) files are simply loaded in the browser individually (via `<script>` tags or other dynamic JS resource loading), if there is no single surrounding scope encompassing all these pieces, the **global scope** is the only way for them to cooperate with each other:

这样的捆绑文件往往看起来是这样的:

```js
var moduleOne = (function one(){
    // ..
})();
var moduleTwo = (function two(){
    // ..

    function callModuleOne() {
        moduleOne.someMethod();
    }

    // ..
})();
```

在这里,由于没有周围的功能范围,`moduleOne`和`moduleTwo`宣言只是落入全球范围。 这实际上和文件没有被整理,而是单独装入一样:

模块1.js:

```js
var moduleOne = (function one(){
    // ..
})();
```

模块2.js:

```js
var moduleTwo = (function two(){
    // ..

    function callModuleOne() {
        moduleOne.someMethod();
    }

    // ..
})();
```

如果在浏览器环境中将这些文件分别作为普通的独立.js文件加载,每个顶级的可变声明都会最终成为全局变量,因为全局范围是这两个独立文件之间唯一的共享资源——从JS引擎的角度来看,它们是独立的程序.

除了(可能)说明应用程序代码在运行期间的所在地,以及每个部件如何能够访问其他部件进行合作外,全球范围还包括:

* JS揭露其内在:

    - primitives: `undefined`, `null`, `Infinity`, `NaN`
    - natives: `Date()`, `Object()`, `String()`, etc.
    - global functions: `eval()`, `parseInt()`, etc.
    - namespaces: `Math`, `Atomics`, `JSON`
    - friends of JS: `Intl`, `WebAssembly`

* JS引擎的托管环境暴露了自己的内在:

    - `console` (and its methods)
    - the DOM (`window`, `document`, etc)
    - timers (`setTimeout(..)`, etc)
    - web platform APIs: `navigator`, `history`, geolocation, WebRTC, etc.

这些只是许多“全球”中的一部分 你的程序将与之互动。

| 注: |
| :--- |
| 节点也暴露了"全球"的几个元素,但从技术上讲,它们不在`global`范围:`require()`, `__dirname`, `module`, `URL`,还有其他的 |

大部分开发商都同意,全球范围不应该仅仅是对您应用程序中每个变量的倾销场. 虫子一团糟等着发生 但同样不可否认的是,全球范围对于几乎所有的JS应用来说都是一个重要的*glue*.

## 全球范围究竟在哪里?

似乎很明显,全球范围位于一个文件的最外侧部分,即不在任何功能或其他块内。 但事情没那么简单

不同的JS环境处理你们的节目范围,特别是全球范围,不同. JS开发者在不意识到的情况下 隐藏误解是很常见的

### 浏览器"Window"

关于全球范围的处理,可以运行的最*纯*环境JS是作为一个独立的.js文件在浏览器的网页环境中装入. 我并不是说“纯粹”,

考虑一下这个.js文件:

```js
var studentName = "Kyle";

function hello() {
    console.log(`Hello, ${ studentName }!`);
}

hello();
// Hello, Kyle!
```

This code may be loaded in a web page environment using an inline `<script>` tag, a `<script src=..>` script tag in the markup, or even a dynamically created `<script>` DOM element. In all three cases, the `studentName` and `hello` identifiers are declared in the global scope.

这意味着如果你访问全球对象(通常,`window`在浏览器中,你会发现这些同名的属性:

```js
var studentName = "Kyle";

function hello() {
    console.log(`Hello, ${ window.studentName }!`);
}

window.hello();
// Hello, Kyle!
```

这是从阅读JS规格中可以期待的默认行为: 外在范围 * 是全球范围,`studentName`被合法地创造为全球变量。

我就是这个意思 但不幸的是,你遇到的所有JS环境并不总是如此,这常常令JS开发者感到惊讶.

#### 全球阴影化全球

回顾第3章关于影子化(和全球无阴影化)的讨论,在该章中,一个可变声明可以推翻并防止从外部获取同名声明。

全球变量与同名全球属性之间的差异的一个不同寻常的后果是,仅在全球范围内,一个全球对象属性可以被一个全球变量遮蔽:

```js
window.something = 42;

let something = "Kyle";

console.log(something);
// Kyle

console.log(window.something);
// 42
```

那个`let`声明添加a`something`全局变量,但不是全局对象属性(见第3章)。 那么,效果是,我们`something`逻辑标识符阴影`something`全局对象属性。

造成全球目标与全球范围之间的分歧,几乎肯定是一个坏主意. 你密码的读者几乎肯定会被绊倒

一个简单的避免全球宣言的简单方法:永远使用`var`用于全球。 准备金`let`和`const`关于块范围(见第6章“与块一道界定”)。

#### DOM 全球

我断言,浏览器托管的JS环境 拥有最*纯粹* 全球范围的行为,我们会看到。 然而,它并不完全*纯粹*.

全球范围的一个令人惊讶的行为,你可能会遇到基于浏览器的JS应用程序:一个DOM元素,带有一个`id`属性自动创建一个引用它的全球变量。

考虑这个标记:

```text
<ul id="my-todo-list">
   <li id="first">Write a book</li>
   ..
</ul>
```

该页的联署材料可包括:

```js
first;
// <li id="first">..</li>

window["my-todo-list"];
// <ul id="my-todo-list">..</ul>
```

如果`id`值是一个有效的词典名称( 如`first`),词典变量被创建. 如果不是,获取这一全球信息的唯一途径就是通过全球目标`window[..]`).

全部自动登记`id`- 将DOM元素作为全局变量是旧的遗留浏览器行为, 我的建议是永远不要使用这些全球变量,尽管它们总是会默默地产生.

#### (温道)名字里有什么?

在基于浏览器的JS中,

```js
var name = 42;

console.log(name, typeof name);
// "42" string
```

`window.name`是一个浏览器上下文中预先定义的"Global";它是全球对象上的一个属性,所以它看起来就像一个普通的Global变量(除了"正常"之外,它还不算什么).

我们用过`var`对于我们的声明,** 不** 阴影预先定义`name`全球财产。 这意味着,实际上,`var`声明被忽略了,因为已经有这个名字的全球范围对象属性. 正如我们之前讨论过的, 如果我们使用了`let name`我们本来会跟踪`window.name`具有单独的全球`name`变量。

但真正令人惊讶的行为是,尽管我们分配了数字`42`改为:`name`(因此,`window.name`),当我们找到它的值,它是一个字符串`"42"`来啊! 在这种情况下,怪怪的是因为`name`实际上是一个预定义的获取器/生成器`window`对象,它坚持其值为字符串值。 哎哟!

除了DOM元素ID和`window.name`JS作为独立文件在浏览器页面上运行,

### 网络工人

Web Workers是浏览器-JS行为之上的网络平台扩展,它允许JS文件在运行主要JS程序的线程(操作系统明智)中完全分开运行.

由于这些Web Worker程序运行在一个单独的线程上,因此在与主应用线程的通信中受到限制,以避免/限制种族条件和其他并发症. 例如Web Worker代码无法访问DOM. 不过,一些网络API提供给工人,例如:`navigator`.

由于Web Worker被视为一个完全独立的程序,因此它不与主要JS方案共享全球范围. 然而,浏览器的JS引擎仍在运行代码,因此我们可以期待其全球范围行为的类似*纯度*. 由于没有DOM进入,`window`全球范围的别名并不存在.

在 Web Worker 中,全球对象的引用通常使用`self`:

```js
var studentName = "Kyle";
let studentID = 42;

function hello() {
    console.log(`Hello, ${ self.studentName }!`);
}

self.hello();
// Hello, Kyle!

self.studentID;
// undefined
```

与主要联署项目一样,`var`和`function`声明在全球对象(aka,`self`(a) 其他声明(`let`页:1

所以,我们在这里看到的全球范围行为 大约是*纯粹* 因为它得到了管理JS程序; 也许它更*纯粹* 因为没有DOM来搅乱事情!

### 开发工具控制台/REPL

回顾在*Get Started*中的第一章,开发者工具不会创造一个完全坚持的JS环境. 他们确实处理JS代码,但他们也倾向于UX交互对开发者最为友好(aka,开发者经验,或DX).

在某些情况下,在输入 JS 短片段时,优于处理完整 JS 程序预期的正常严格步骤,在程序和工具之间的代码行为上产生了明显差异。 例如,适用于JS程序的某些错误条件可以放宽,在代码输入开发者工具时不显示.

关于我们在此讨论范围问题,这种明显的行为差异可能包括:

* 全球范围的行为

* 乘车(见第5章)

* 区块界定申报人`let` / `const`,见第6章,在最外部范围使用时

虽然似乎在使用控制台/REPL时,最外部范围输入的语句正在真正全球范围处理,但这并不十分准确。 这种工具通常在一定程度上模仿全球范围的地位;它是一种模仿,而不是严格遵守。 这些工具环境优先考虑开发商的便利,这意味着有时(如我们目前关于范围的讨论)观察到的行为可能偏离联合来文的规定。

取而代之的是,开发者工具虽然被优化,对各种开发者活动来说既方便又有用,但并非**** 适合确定或核实实际JS程序背景的明显和细微行为的环境。

### ES模块(ESM)

ES6引入了模块模式的一流支持(涵盖于第8章). 使用无害环境管理最明显的影响之一是它如何改变一个文件中可观察到的顶级范围的行为。

回顾先前的这个代码片断(我们将通过使用该代码调整到无害环境管理格式)`export`关键词:

```js
var studentName = "Kyle";

function hello() {
    console.log(`Hello, ${ studentName }!`);
}

hello();
// Hello, Kyle!

export hello;
```

如果那个代码在作为ES模块加载的文件里,它仍将运行完全一样. 然而,从总体应用的角度来看,可观察到的效果将有所不同。

尽管在(模块)文件的顶层被宣布, 在最外表的范畴,`studentName`和`hello`非全局变量。 相反,它们是整个模块, 或者如果你喜欢,“模块全球”。

然而,在一个模块中,并没有隐含的"模块全范围范围范围对象"来将这些顶级声明作为属性添加,就像在非模块JS文件的顶级出现时一样. 这并不是说全球变量不可能存在或无法在此类程序中访问。 只是全球变量不会通过在一个模块的顶级范围中宣告变量来得到*创建*.

该模块的顶级范围从全球范围降级,几乎仿佛模块的全部内容被包裹在一个函数中. 因此,全球范围内存在的所有变量(无论它们是否位于全球对象上!)都可以作为词汇识别符从模块范围内获取.

无害环境管理鼓励最大限度地减少对全球范围的依赖,即输入当前模块运行所需的任何模块。 因此,你不太经常看到对全球范围或其全球目标的利用。

然而,如前所述,仍然有许多联署材料和网络全球,你们将继续从全球范围访问,无论你们是否意识到这一点!

### 节点

节点经常捕捉JS开发者的一个方面是节点将它装入的每个单个的.js文件,包括你以一个*模块*(ES模块或CommonJS模块,参见第8章)启动节点进程的主要文件. 实际效果是,您的节点程序的顶层**实际上从来不是全局范围**,在浏览器中加载非模块文件的方式.

截至本文编写之时,节点最近增加了对ES模块的支持. 但另外,节点从一开始就支持一个被称为"CommonJS"的模块格式,这个格式看起来是这样:

```js
var studentName = "Kyle";

function hello() {
    console.log(`Hello, ${ studentName }!`);
}

hello();
// Hello, Kyle!

module.exports.hello = hello;
```

在处理之前,节点会有效地将这样的代码包裹在一个函数中,这样就可以使`var`和`function`声明载于包干函数的范围,** 不**作为全球变量处理。

将前面的代码视为节点所认为的这个(无意义而非实际):

```js
function Module(module,require,__dirname,...) {
    var studentName = "Kyle";

    function hello() {
        console.log(`Hello, ${ studentName }!`);
    }

    hello();
    // Hello, Kyle!

    module.exports.hello = hello;
}
```

节点然后基本上引用添加的`Module(..)`函数来运行您的模块。 你明白为什么`studentName`和`hello`标识符不是全球性的,而是在模块范围内宣布的。

如前所述,节点定义了一些"全球"如:`require()`,但它们实际上不是全球范围内的标识符(全球对象的特性). 它们被注入到每个模块的范围, 基本上有点像 包含在参数`Module(..)`函数声明。

那么,如何定义节点中的实际全球变量? 唯一这样做的方法是将属性添加到另一个自动提供的"Globals"中,这被讽刺地称作"Globals".`global`. `global`是指真正的全球范围对象,有些像使用`window`在浏览器JS环境中。

考虑:

```js
global.studentName = "Kyle";

function hello() {
    console.log(`Hello, ${ studentName }!`);
}

hello();
// Hello, Kyle!

module.exports.hello = hello;
```

在这里,我们添加`studentName`作为财产`global`对象,然后在`console.log(..)`声明,我们可以访问`studentName`作为正常的全球变量。

记住,标识符`global`JS没有定义;它由节点具体定义.

## 全球 这个

在回顾我们迄今所研究的JS环境时,一个程序可能或不会:

* 宣布顶级范围的全局变量`var`或`function`声明——或`let`, `const`,以及`class`.

* 还加上全球变量声明,作为全球范围对象的属性,如果`var`或`function`用于宣布。

* 指全局范围对象(用于添加或检索全局变量,作为属性)`window`, `self`,或`global`.

我认为公平地说,全球范围访问和行为比大多数开发者所假设的要复杂,如前几节所说明的. 但是,其复杂性从未比试图将普遍适用的对全球范围目标的提法更明显。

获取全球范围对象参考文献的另一个“trick”看起来是:

```js
const theGlobalScopeObject =
    (new Function("return this"))();
```

| 注: |
| :--- |
| 函数可以从存储在字符串值中的代码中动态构造`Function()`构造器,类似于`eval(..)`(参见第1章中的"Cheating:runtime Professionals"修改). 在正常情况下,这种功能将自动在非限制性模式下运行(出于遗留原因)。`()`函数引用显示;其`this`将指向全球对象。 欲了解更多关于确定`this`装订。 |

所以,我们有`window`, `self`, `global`和这个丑陋`new Function(..)`骗局。 试图达到这个全球目标的方法有很多不同. 每一个都有它的利弊。

为什么不再介绍一次?

截至ES2020年,联署材料最终确定了全球范围目标的标准参考,称为`globalThis`所以,根据JS引擎的调整 你的代码运行,你可以使用`globalThis`取代任何其他办法。

我们甚至可以尝试定义一个跨环境的多填充物 这样在...`globalThis`联合来文环境,例如:

```js
const theGlobalScopeObject =
    (typeof globalThis != "undefined") ? globalThis :
    (typeof global != "undefined") ? global :
    (typeof window != "undefined") ? window :
    (typeof self != "undefined") ? self :
    (new Function("return this"))();
```

嘘! 这当然不是理想的,但是如果你发现自己需要可靠的全球范围参考物,那就奏效了.

(拟议名称)`globalThis`在联署材料中增加这一特征时,存在相当的争议。 具体地说,我和许多其他人认为它的名字中的“这个”提法有误导性,因为你提到这个目标的原因是进入全球范围,从不访问某种全球/默认`this`装订。 曾考虑过许多其他名字,但由于各种原因被排除. 不幸的是,所选的名字最后成了最后的手段。 如果你计划与你程序中的全球范围对象互动,以减少混乱,我强烈建议选择更好的名称,比如(可笑的长而准确!)`theGlobalScopeObject`此处使用. )

## 全球认识

每一个联署材料方案都具有全球范围,而且具有相关性,尽管将代码组织成模块的现代模式不强调对在该命名空间中储存标识的依赖。

然而,随着我们的代码越来越超出浏览器的范畴,我们尤其要牢牢把握全球范围(和全球范围对象)在不同联署机构环境中的表现差异。

随着全球范围的大局现在更加突出重点,下一章再次进入更深入的词汇范围细节,审查如何和何时可以使用变量。
