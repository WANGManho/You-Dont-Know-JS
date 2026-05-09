# 你不了解JS然而:范围与关闭 - 第二版
# 第5章:变量的秘密生命周期(不是如此)

从全球范围向下——即所谓的方案范围链——到现在,你应该能很好地掌握范围网。

但只要知道变量来自哪个范围 仅仅是故事的一部分 如果一个可变声明出现于第一个范围声明之后,在* 声明之前,任何提及该标识符之处将如何运作? 如果你试图在范围中两次声明同一变量,会怎么样?

联合来文对词汇范围的特殊口味,在变量如何和何时出现以及何时可供方案使用方面,有着丰富的细微差别。

## 什么时候可以用变数?

一个变量何时可以在其范围内使用? 似乎有一个明显的答案:*在*变量被宣布/创建之后。 对吧? 没有

考虑:

```js
greeting();
// Hello!

function greeting() {
    console.log("Hello!");
}
```

这个代码是好的。 你可能以前见过甚至写过这样的代码 但是,你有没有想过 它是如何或为什么工作? 具体来说,为什么你可以访问标识符`greeting`从行 1 (检索并执行函数引用),即使`greeting()`函数声明要到行4才发生?

回顾第1章指出,所有识别器在编译期间都登记在各自的范围。 此外,每个识别符都在其所属范围开始时被*创建*,**每次输入范围**。

最常用的术语是指从附着范围开始就可见的变量,尽管其声明可能进一步出现在范围之下,但称为**hoisting**。

但单打独斗并不能完全回答问题. 我们可以看到一个叫`greeting`从范围开始,但为什么我们能够** 呼唤**`greeting()`在它被宣布之前的函数?

换句话说,变量是如何`greeting`从范围开始运行时起, 是否有指定给它的任何值( 函数引用)? 答案是正式的`function`声明,称为 " 功能提升 "。 当 a`function`声明的名称标识符是登记在其范围的顶端,它额外的自动初始化为该函数的引用. 所以整个范围都可以调用这个功能!

一个关键细节是,既要保持功能,又要保持功能。`var`- 喜欢的 * 可变悬挂 * 将其名称识别符附在最近的附件** 函数范围**(如果没有,为全球范围),而不是块范围。

| 注: |
| :--- |
| 声明`let`和`const`仍举起(见本章后面的TDZ讨论)。 但是,这两种声明表都附着在它们的附件块上,而不是仅仅附着一个功能,如:`var`和`function`声明。 更多信息见第6章中"与块一起界定". |

### 欢呼:宣言与表达

* 功能吊销* 仅适用于正式`function`声明(具体指在区块之外出现的声明——见第6章中的“FiB”),而不是:`function`表达式任务。 考虑:

```js
greeting();
// TypeError

var greeting = function greeting() {
    console.log("Hello!");
};
```

1号线(`greeting();`) 扔出一个错误。 但抛出错误的*类型* 是非常重要的注意。 页:1`TypeError`意味着我们试图做一些 价值是不允许的。 根据你的JS环境,错误信息会说"'未定义'不是一个函数",或者更有用的是"'greeting'不是一个函数".

通知错误**不** a`ReferenceError`JS没有告诉我们它找不到`greeting`作为范围中的标识符。 它告诉我们`greeting`被找到,但当时没有函数参考。 只能引用函数,所以试图引用一些非函数值会导致错误.

但是怎样`greeting`如果不是函数参考符,请按住?

除了被抬起之外,还用`var`也自动初始化为`undefined`其范围开始时 —— 又是最近的环绕函数, 或者是全局函数。 初始化后,可以全程使用(指定使用,检索等).

所以在第一线,`greeting`已存在, 但只持有默认值`undefined`数值。 在4号线之前`greeting`获得函数引用。

密切注意这里的区别。 A级`function`声明被竖起**并初始化到其函数值**(又称为“函数竖起*”。 页:1`var`变量也被挂起,然后自动初始化为`undefined`。随后的任何`function`该变量的表达式任务在运行时处理之前不会发生。

在这两种情况下,标识符的名称都竖立起来. 但功能参考关联在初始化时间(范围开始)没有处理,除非标识符是正式创建的`function`声明。

### 可变同步

让我们来看看另一个例子,“可变的升起”

```js
greeting = "Hello!";
console.log(greeting);
// Hello!

var greeting = "Howdy!";
```

虽然`greeting`在5号线通车前没有申报,它早在1号线就可分配使用. 为什么?

解释有两个必要的部分:

* 记号是竖起的,
* ** 自动初始化为值`undefined`从瞄准镜的顶部。

| 注: |
| :--- |
| 使用这种“可变的升起 ” 可能感觉不自然, 许多读者也许想在他们的节目中避免依赖它。 但是,是否应该避免一切竖起(包括*功能竖起*)? 我们将在附录A中更详细地探讨这些不同的观点。 |

## 呼唤:然而又一个变数

第2章充满了比喻(以说明范围),但在这里,我们面临另一个:举起自己。 与其说是JS引擎所执行的具体执行步骤,不如认为是将JS在设立程序时采取的各种行动作为可视化的,**在执行前** 更有用。

举起什么的典型论断:*举起像举起重的重量——任何识别器都一直到一个范围的最高点。 通常的解释是,JS引擎实际上会在执行前重写*程序,这样看起来更像:

```js
var greeting;           // hoisted declaration
greeting = "Hello!";    // the original line 1
console.log(greeting);  // Hello!
greeting = "Howdy!";    // `var` is gone!
```

升起(metaphor)提议JS预先处理原始程序,再稍作安排,以便所有声明都移到各自范围的顶端,执行前. 此外,上升的比喻声称:`function`声明全部竖到每个范围的顶端。 考虑:

```js
studentName = "Suzy";
greeting();
// Hello Suzy!

function greeting() {
    console.log(`Hello ${ studentName }!`);
}
var studentName;
```

竖起的比喻的"规则"是函数声明首先被竖起,然后在所有函数之后立即被竖起变量. 因此,升起的故事表明,节目是由JS引擎重新安排的,这样子:

```js
function greeting() {
    console.log(`Hello ${ studentName }!`);
}
var studentName;

studentName = "Suzy";
greeting();
// Hello Suzy!
```

此举譬喻方便. 它的好处是,让我们可以把 神奇的外观前置处理工作 交给我们, 发现所有这些声明 埋藏在深度的瞄准镜中,

与第一章关于分两阶段处理的说法相比,单次通过显然更直截了当。

牵引作为重排代码的机制可能是一种有吸引力的简化,但并不准确. JS引擎实际上并没有重新安排代码. 它无法神奇地向前看,找到声明;准确找到声明的唯一方法,以及程序中的所有范围界限,就是充分解析代码.

猜猜什么是剖析? 两阶段处理的第一阶段! 没有一个神奇的精神体操 得到这个事实。

所以,如果举起的比喻(最好)是不准确的,那么我们该用这个词做什么呢? 我认为它仍然有用——事实上,甚至TC39的成员也经常使用它!——但我认为我们不应该声称这是对源代码的实际重新安排.

| 警报: |
| :--- |
| 不正确或不完整的心理模型往往看起来仍然足够,因为它们有时会导致偶然的正确答案. 但从长远来看,如果你的想法与JS引擎的运作方式不特别一致,那么准确分析和预测结果就更难了. |

我断言,*应* 提号是指** 编译时间作业** 生成运行时间指示,用于在范围开始时自动登记变量,每次输入范围。

这是一个微妙但重要的转变, 从举起作为运行时间的行为, 到它在编译时间任务中的适当位置。

## 重新声明?

当一个变量在同一范围被宣布不止一次时,你认为会发生什么? 考虑:

```js
var studentName = "Frank";
console.log(studentName);
// Frank

var studentName;
console.log(studentName);   // ???
```

你希望用什么来印刷第二封信? 很多人相信第二个`var studentName`重新宣布了变量(从而"重置"),所以他们期望`undefined`将予印刷。

但是,在同一个范围里,是否有像变量那样被"重新宣布"的东西? 没有

如果你从上升比喻的角度来考虑这个程序,代码会像这样重新排列,以便执行:

```js
var studentName;
var studentName;    // clearly a pointless no-op!

studentName = "Frank";
console.log(studentName);
// Frank

console.log(studentName);
// Frank
```

由于升起实际上是在范围开始时登记一个变量,所以在范围中间没有任何事情可做,原程序实际上有第二个`var studentName`语句。 这只是一个没有选择,一个毫无意义的声明。

| 提普: |
| :--- |
| 在第2章的谈话叙事风格中,`var`声明声明并询问 * 范围管理器 * 是否已经看到`studentName`(笑声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) |

亦须指出.`var studentName;`这并不意味着`var studentName = undefined;`就像大多数假设。 让我们通过考虑这个程序的变化来证明它们是不同的:

```js
var studentName = "Frank";
console.log(studentName);   // Frank

var studentName;
console.log(studentName);   // Frank <--- still!

// let's add the initialization explicitly
var studentName = undefined;
console.log(studentName);   // undefined <--- see!?
```

看如何明确`= undefined`初始化产生的结果不同于在省略时默认发生的结果? 在下一节中,我们将重温从他们申报的变量初始化这个话题.

重复`var`在某个范围内声明同一标识名称实际上是一种无为操作。 这里还有一个例子,这次跨越一个同名的函数:

```js
var greeting;

function greeting() {
    console.log("Hello!");
}

// basically, a no-op
var greeting;

typeof greeting;        // "function"

var greeting = "Hello!";

typeof greeting;        // "string"
```

第一个`greeting`声明将识别标志登记到范围,因为它是一个`var`自动初始化将`undefined`编辑`function`声明不需要重新登记标识符,但因为 * 函数升起 * 它超越了自动初始化来使用函数引用. 第二个`var greeting`自那以后 自己什么都不会做`greeting`已经是一个识别符,而且“功能提升”已经优先于自动启动。

实际分配`"Hello!"`改为:`greeting`从初始函数更改其值`greeting()`到字符串;`var`本身没有任何效果。

使用`let`或`const`?

```js
let studentName = "Frank";

console.log(studentName);

let studentName = "Suzy";
```

这个程序不会执行,而是立即丢出一个`SyntaxError`。根据你的联署材料环境,错误信息将显示诸如: 名字已经被宣布了". 换句话说,这是一桩明确不允许试图"重新声明"的案件!

不仅仅是两个声明涉及`let`将丢弃此错误。 如果其中任一声明使用`let`另一个也可以是`let`或`var`,而错误仍将发生,如这两个变化所示:

```js
var studentName = "Frank";

let studentName = "Suzy";
```

和:

```js
let studentName = "Frank";

var studentName = "Suzy";
```

在这两种情况下,a`SyntaxError`被扔在 * 秒 * 声明上。 换句话说,"重新宣告"变量的唯一方法是使用`var`为所有人(两个或两个以上)所作的声明。

但为什么拒绝? 错误的原因本身并不是技术性的,因为`var`"重新申报"是一直允许的;显然,本来可以给`let`.

这其实更是一个"社会工程"问题. 变数的"重新声明"被一些人,包括许多在TC39正文上的人,视为一种会导致程序错误的不良习惯. 因此,当ES6引入`let`,他们决定防止"重新声明"出现错误.

| 注: |
| :--- |
| 这当然是一种结构论观点,并不是真正的技术论证. 许多开发者都同意这个位置,这可能是TC39包含错误(以及`let`符合`const`) (中文(简体) ). 但是,可以提出合理的理由,说明遵守《公约》的规定。`var`先例是比较谨慎的 这种意见执行最好留待 选择进入的工具 像linters。 在附录A中,我们将探讨是否`var`(及其相关行为,如"重新声明")在现代JS中仍然有用. |

当 * 编译者 * 询问 * 范围管理者 * 有关声明时, 如果该标识符已经宣布, 如果其中之一/ 两项声明都与`let`,则出现错误。 对开发商的预定信号是"停止依赖草率的重新声明!

### 常数?

那个`const`关键字比`let`类似`let`, `const`无法在同一范围内使用相同的标识符重复。 但实际上有一个压倒一切的技术原因 为什么这种"重新声明"是不允许的,不像`let`它拒绝"重新声明" 大多是出于风格原因

那个`const`关键词要求一个变量初始化,因此在声明中省略一项转让导致`SyntaxError`:

```js
const empty;   // SyntaxError
```

`const`声明创建无法重新指定的变量:

```js
const studentName = "Frank";
console.log(studentName);
// Frank

studentName = "Suzy";   // TypeError
```

那个`studentName`变量不能重新指定,因为它被宣布为`const`.

| 警报: |
| :--- |
| 重新指派时抛出的错误`studentName`是一个`TypeError`,而不是一个`SyntaxError`这里微妙的区别其实相当重要,但不幸的是太容易错过了. 语法错误代表了程序中的断层,使其甚至无法开始执行. 类型错误代表程序执行过程中出现的断层. 在前面的片断中,`"Frank"`在重新分派之前打印出来`studentName`,然后丢出错误。 |

所以,如果`const`声明不能重新指定,以及`const`声明总是需要指派,那么我们有明确的技术理由,为什么`const`必须拒绝任何“重新声明”:`const`"重新声明"也必然是`const`重新分配,这是不允许的!

```js
const studentName = "Frank";

// obviously this must be an error
const studentName = "Suzy";
```

从`const`“重新声明”必须被拒绝(基于这些技术理由),TC39基本上认为,`let`"重新申报"也应予以否决,以保持一致性. 如果这是最好的选择,这是值得商榷的, 但至少我们有做决定的理由。

### 循环

因此从我们之前的讨论中可以清楚地看出,JS并不真正希望我们在同一范围内"重新宣告"我们的变量. 这似乎是一个直截了当的提醒,直到你考虑反复执行声明声明意味着什么。 考虑:

```js
var keepGoing = true;
while (keepGoing) {
    let value = Math.random();
    if (value > 0.5) {
        keepGoing = false;
    }
}
```

已经`value`被反复"重新宣布" 在这个节目中? 我们会不会出错? 没有

所有范围规则(包括`let`-created variables)被应用到每个范围实例*. 换句话说,在执行期间每次输入一个范围时,所有东西都会重现.

每个循环是它自己的新范围实例,在每个范围实例中,`value`只申报一次。 因此没有尝试"重新声明",因此没有错误. 在我们考虑其他循环形式之前 如果`value`前一个片断中的语句改为`var`?

```js
var keepGoing = true;
while (keepGoing) {
    var value = Math.random();
    if (value > 0.5) {
        keepGoing = false;
    }
}
```

已经`value`特别是因为我们知道`var`允许吗? 没有,因为`var`它不被视为一种限制范围的声明(见第6章),而是将自己纳入全球范围。 所以只有一个`value`变量,与`keepGoing`(全球范围,在此情况下)。 这里也没有"重新声明"!

保持一切正确的方法之一是记住`var`, `let`,以及`const`关键词在代码开始执行时有效被*删除*. 它们完全由编译器处理.

如果您在精神上删除了声明者的关键词,然后尝试处理代码,那么应该帮助您决定是否以及何时(重新)宣布。

那用其他循环形式"重新声明"呢?`for`-卢布?

```js
for (let i = 0; i < 3; i++) {
    let value = i * 10;
    console.log(`${ i }: ${ value }`);
}
// 0: 0
// 1: 10
// 2: 20
```

应该很清楚,只有一个`value`每个范围实例。 但是,怎么样`i`是被"重新宣布"吗?

为了回答这个问题,考虑一下什么范围`i`进来了 似乎在外(在此情况下,是全球性的)范围,但并非如此。 它的范围`for`- 荡荡的身体,就像`value`没错 事实上,你可以想一想这个循环 在这个更动词等同的形式:

```js
{
    // a fictional variable for illustration
    let $$i = 0;

    for ( /* nothing */; $$i < 3; $$i++) {
        // here's our actual loop `i`!
        let i = $$i;

        let value = i * 10;
        console.log(`${ i }: ${ value }`);
    }
    // 0: 0
    // 1: 10
    // 2: 20
}
```

现在应该清楚:`i`和`value`变量都完全被宣布一次**每个范围实例**。 这里没有"重新声明"

其他的怎么办?`for`- 滚床单?

```js
for (let index in students) {
    // this is fine
}

for (let student of students) {
    // so is this
}
```

也一样`for..in`和`for..of`循环:所宣布的变量作为循环体 * 的侧面处理,因此按迭代处理(aka,每个范围实例)。 无"重宣".

好吧,我知道你认为 我听起来像一个破纪录 在这一点上。 但让我们探索如何`const`影响这些循环构造。 考虑:

```js
var keepGoing = true;
while (keepGoing) {
    // ooo, a shiny constant!
    const value = Math.random();
    if (value > 0.5) {
        keepGoing = false;
    }
}
```

碞钩`let`我们之前看到的这个方案`const`在每圈循环中运行一次 所以它不会遇到"重新声明"的麻烦 但是当我们谈论的时候 事情会变得更加复杂`for`- 卢布

`for..in`和`for..of`与`const`:

```js
for (const index in students) {
    // this is fine
}

for (const student of students) {
    // this is also fine
}
```

但不是将军`for`- 滚开:

```js
for (const i = 0; i < 3; i++) {
    // oops, this is going to fail with
    // a Type Error after the first iteration
}
```

这里怎么了? 我们可以用`let`这个构思很好 我们断言它创造了一个新的`i`对于每个循环迭代范围,所以它甚至似乎不是一个"重新声明".

让我们在精神上"扩展"那个循环 就像我们之前做的:

```js
{
    // a fictional variable for illustration
    const $$i = 0;

    for ( ; $$i < 3; $$i++) {
        // here's our actual loop `i`!
        const i = $$i;
        // ..
    }
}
```

你发现问题了吗? 我们`i`确实是在循环中创造出来的 这不是问题所在。 问题是概念`$$i`每一次必须随`$$i++`表达式。 即**重派**(不是"重宣"),常数不允许.

记住,这种"扩展"形式只是帮助你适应问题根源的概念模型. 你可能会怀疑联署部队是否能够有效地`const $$i = 0`改为`let $ii = 0`,然后允许`const`与我们的经典合作`for`- 滚? 有可能,但之后它可能会引入 可能令人惊讶的例外`for`- 低语义学

例如,如果允许这种例外,就会是相当武断的(而且可能令人困惑的)细微的例外。`i++`输入`for`- 低头去遮住严谨的`const`,但不允许其他`i`在循环迭代中,有时也是有用的。

直接的答案是:`const`不能用经典`for`-因为需要重新分配,所以要离开

有趣的是,如果你不改派,那么它就是有效的:

```js
var keepGoing = true;

for (const i = 0; keepGoing; /* nothing here */ ) {
    keepGoing = (Math.random() > 0.5);
    // ..
}
```

这有效,但毫无意义。 没有理由宣布`i`担任该职者`const`,因为该位置上这样一个变量的整个点**用于计算迭代**。 只要用不同的循环形式,就像`while`循环,或使用`let`!

## 未初始化变量(aka, TDZ)

与`var`声明,变量被"热"到其范围的顶端. 但它也自动初始化到`undefined`值,这样整个范围都可以使用变量。

不过`let`和`const`在这方面,声明并不完全相同。

考虑:

```js
console.log(studentName);
// ReferenceError

let studentName = "Suzy";
```

这个计划的结果是:`ReferenceError`被扔在第一线。 根据你的JS环境,错误信息可能会说:"不能访问学生" 初始化前的名字".

| 注: |
| :--- |
| 这里看到的错误信息过去更模糊或误导。 幸运的是,我们社区中的几个人成功地游说JS引擎改进了这个错误信息,从而更准确地告诉你出了什么问题! |

这个错误信息相当地表明了什么是错的:`studentName`存在于1号线上,但还没有初始化,所以目前还不能使用. 让我们试试这个:

```js
studentName = "Suzy";   // let's try to initialize it!
// ReferenceError

console.log(studentName);

let studentName;
```

哎哟 我们仍然得到`ReferenceError`,但现在在第一线 我们试图分配(aka,初始化!) 这个所谓的"未初始化"变量!`studentName`怎么样?

真正的问题是,我们如何初始化一个未初始化的变量? 对于`let`/`const`,这样做的唯一方式**是在声明说明中附上一项转让。 任务本身是不够的! 考虑:

```js
let studentName = "Suzy";
console.log(studentName);   // Suzy
```

在这里,我们正在初始化`studentName`(在这种情况下,改为:`"Suzy"`改为`undefined`通过`let`声明声明表 与任务相结合

或者:

```js
// ..

let studentName;
// or:
// let studentName = undefined;

// ..

studentName = "Suzy";

console.log(studentName);
// Suzy
```

| 注: |
| :--- |
| 这很有趣! 刚才我们说过`var studentName;`*不* 与`var studentName = undefined;`但是这里`let`他们的行为是一样的。 差别在于`var studentName`自动初始化:`let studentName`没有。 |

记住,我们已经多次断言,到目前为止, *编译器* 最终去除任何`var`/`let`/`const`声明者,以每个范围的顶端的指示取代,以登记适当的识别资料。

因此,如果我们分析这里发生的情况, 我们可以看到一个额外的细微之处是, *编译器* 也在程序中间添加一个指令, 在变量的位置`studentName`宣布,处理该声明的自动初始化。 我们不能在初始化之前的任何时候使用变量。 也一样`const`就像它为`let`.

TC39发明的术语是指从进入范围到变量自动初始化发生的这个*时间段*是: Temperal Dead Zone(TDZ).

TDZ是存在变量但仍未初始化的时间窗口,因此无法以任何方式访问. 只有执行*编译者*在原声明点留下的指令才能完成该初始化. 在那一刻之后,TDZ就完成了,变数可以自由用于剩下的范围.

页:1`var`技术上也有TDZ,但长度为0,因此无法观测到我们的程序! 仅`let`和`const`拥有可观察到的TDZ.

顺便提一句,TDZ中的"时态"确实是指*时间*而不是*在代码中的位置*. 考虑:

```js
askQuestion();
// ReferenceError

let studentName = "Suzy";

function askQuestion() {
    console.log(`${ studentName }, do you know?`);
}
```

虽然在位置上`console.log(..)`参考资料`studentName`来了`let studentName`声明,时机明智`askQuestion()`函数在 * 之前被引用`let`语句被遇到,而`studentName`还在TDZ! 因此,错误。

有一种常见的误解是TDZ的意思是:`let`和`const`不要举起。 这种说法是不准确的,或至少略有误导性。 他们肯定举起。

实际区别在于`let`/`const`声明不会在范围、方式开始时自动初始化`var`当然 那么,“辩论”是指自动初始化是否是“上升”的一部分? 我认为,一个变量的自动注册位于范围顶端(即我所称的"热"),而自动初始化位于范围顶端(至`undefined`)是不同的操作,不应该在单词"热"下拼凑在一起.

我们已经看到了`let`和`const`不要自动初始化 在最高范围。 但让我们证明`let`和`const`* * 举起(在范围顶端自动注册),以我们的朋友跟踪为礼(见第三章中的"Shadowing"):

```js
var studentName = "Kyle";

{
    console.log(studentName);
    // ???

    // ..

    let studentName = "Suzy";

    console.log(studentName);
    // Suzy
}
```

第一个会怎么样?`console.log(..)`声明? 若为`let studentName`没有升到顶端的瞄准镜, 然后第一个`console.log(..)`*应* 打印`"Kyle"`是吗? 此时此刻,似乎只有外在`studentName`已存在,所以这是变量`console.log(..)`应访问和打印。

但是,第一个`console.log(..)`扔出一个 TDZ 错误, 因为事实上, 内在范围是`studentName`** 升起(自动登记在最高范围)。 内幕的自动初始化。`studentName`那时它还没有被初始化,所以TDZ违规!

简而言之,TDZ错误发生的原因是`let`/`const`*do* 把他们的声明提升到其范围的顶端,但与`var`,它们会将变量的自动初始化推迟到代码排序中出现原始声明的时刻. 这种时间之窗(int:tality),无论长度如何,都是TDZ.

你怎么能避免TDZ错误?

我的建议是: 永远把你的`let`和`const`在任何范围内的顶端声明。 将TDZ窗口缩窄为零(或近于零)长度,然后会停用.

但为什么TDZ甚至是一个东西? 为什么TC39没有规定`let`/`const`自动初始化方式`var`是吗? 耐心点 我们会回来探索附录A中的TDZ的原因

## 最后开始

与初看起来相比,用变量工作要细微得多。 * 召集*、*(re)声明*和*TDZ*是开发者,特别是那些在来联署材料之前曾用其他语言工作的开发者的共同混淆来源。 在继续前,确保你的心理模式充分基于JS范围和变量的这些方面.

霍斯廷一般被引用为JS引擎的明确机制,但实际上更是一个比喻来描述JS在汇编过程中处理可变声明的各种方式. 但即使作为一个比喻,升起也为思考变量的生命周期提供了有用的结构——当它被创造出来,当它可用,当它消失的时候.

变量的宣布和重新宣布在被认为是运行时间操作时往往会造成混乱. 但如果你转向这些操作的编译时间思维, 怪怪和*阴影* 就会减少。

TDZ(时空死亡区)的误差在遇到时是奇怪和令人沮丧的. 幸运的是,TDZ比较直截了当地避免 如果你总是小心地放置`let`/`const`在任何范围内的顶端声明。

在你成功浏览这些可变范围的曲折和转折时,下一章将列出指导我们决定将我们的宣言置于不同范围的因素,特别是嵌套区块。
