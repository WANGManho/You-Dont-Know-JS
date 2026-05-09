# 你不认识JS然而:开始 - 第二版
# 第2章:调查联署材料

学习联署材料的最好办法是开始编写联署材料。

要做到这一点,你需要知道 语言是如何工作的, 而这就是我们将专注于这里。 即使你之前已经用其他语言编程过, 慢慢地适应JS, 确保练习每部作品。

本章并非详尽无遗地提到联署材料语言的每一个语法。 也无意成为完整的"介绍JS"首页.

相反,我们只是去调查语言的一些主要主题领域. 我们的目标是获得一个更好的* feel * 为它, 这样我们可以前进 写我们自己的程序 更加自信。 接下来我们再详细讨论其中的很多话题, 以及本书的其余部分。

请不要指望 本章是一个快速阅读。 久而多细可嚼. 慢慢来

| 提普: |
| :--- |
| 如果你仍然熟悉JS,我建议你 保留大量额外时间来完成这一章。 仔细思考一下 探讨一下话题 透过现有的JS程序, 以JS的"自然"为坚实基础, |

## 每个文件都是程序

几乎每个您使用的网站(web应用程序)都包含许多不同的JS文件(通常与.js文件扩展). 将整件事(应用)视为一个程序是诱人的. 但联署材料对此的看法不同。

在联署材料中,每个独立文件都是自己的单独程序。

其原因主要是处理错误。 由于JS将文件视为程序,一个文件可能会失败(在解析/编译或执行期间),这并不一定会妨碍下一个文件的处理. 显然,如果您的申请依赖于5个.js文件,而其中一个文件失败了,那么整个申请最多只能部分运行. 重要的是要确保每个文件正常工作,无论在什么程度上,他们尽可能优雅地处理其他文件的失败.

你可能会惊讶地考虑 分开的Js文件 作为单独的JS程序。 从你对一个应用程序的运用角度来说 这似乎是一个大程序 这是因为应用程序的执行使得这些个体*程序*能够合作并作为一个程序发挥作用.

| 注: |
| :--- |
| 许多项目使用构建过程工具,最终将项目中独立的文件合并为一个文件,交付网页. 当这种情况发生时,JS将这个单项综合文件视为整个程序. |

多个独立.js文件作为单一程序的唯一方式是通过"全球范围"来分享其状态(并访问其公共功能). 它们在这个全球范围命名空间中混合在一起,所以在运行时它们作为一个整体发挥作用.

Since ES6, JS has also supported a module format in addition to the typical standalone JS program format. Modules are also file-based. If a file is loaded via module-loading mechanism such as an `import` statement or a `<script type=module>` tag, all its code is treated as a single module.

尽管你通常不会把一个模块——一种在这种状态上操作的公开公布的状态方法集——作为一个独立的程序,但联署材料实际上仍然将每个模块分开处理。 与"全球范围"允许独立文件在运行时间混合的方式类似,将一个模块导入到另一个模块允许它们之间的运行时间交互操作.

无论文件(独立或模块)使用何种代码组织模式(和加载机制),你仍应当将每个文件视为自己的(mini)程序,然后可以与其他(mini)程序合作,履行你总体应用的功能.

## 数值

一个程序中最基本的信息单位是一个值. 数值是数据。 程序是怎样维持状态的 价值观在联署材料中分为两种形式:**原始**和**对象**。

数值被嵌入使用 *literals * 的程序:

```js
greeting("My name is Kyle.");
```

在此程序中, 值`"My name is Kyle."`是一种原始的字符串文字;字符串是命令字符集,通常用来代表文字和句子.

我用双引号`"`字符为 *delimit *(圆形、独立、定义)字符串值。 但我可以用单词`'`字符也一样。 哪个引用字符的选择是完全风格化的. 为了代码可读性和可维护性,重要的是选择一个并在整个节目中一致使用.

定义字符串文字的另一个选项是使用后键`` ` ``字符键。 然而,这种选择并不仅仅是形式主义的;还有行为上的区别. 考虑:

```js
console.log("My name is ${ firstName }.");
// My name is ${ firstName }.

console.log('My name is ${ firstName }.');
// My name is ${ firstName }.

console.log(`My name is ${ firstName }.`);
// My name is Kyle.
```

假设此程序已经定义了一个变量`firstName`带有字符串值`"Kyle"`,则`` ` ``-delimited string then resolves the variable expression (indicated with `${.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       这叫做**内插**.

背脚`` ` ``-有限字符串可以在不包含插值表达式的情况下使用,但这会挫败替代字符串语法的整个目的:

```js
console.log(
    `Am I confusing you by omitting interpolation?`
);
// Am I confusing you by omitting interpolation?
```

更好的办法是使用`"`或`'`{\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}"除非你需要"内插 预备"`` ` ``仅用于包含内插表达式的字符串。

JS程序除了字符串以外,还常常包含其他原始的字面价值,如布尔和数字:

```js
while (false) {
    console.log(3.141592);
}
```

`while`代表一个循环类型,一种重复操作 * 的方式,而* 其状态是真实的.

在这种情况下,循环永远不会运行(而且不会被打印),因为我们使用了`false`布尔值作为循环条件。`true`这会形成一个循环 一直持续, 所以小心点!

号码`3.141592`也就是数学PI到前六个数字的近似值。 而不是嵌入这样的值,但您通常会使用预定义`Math.PI`价值。 关于数字的另一个变化是:`bigint`(big-integer)原始类型,用于任意大量存储.

数字在程序上最常用于计数步骤,如循环迭代,以及以数字位置获取信息(即数组索引). 我们稍稍覆盖阵列/物体,但作为一个例子,如果有一个阵列叫做`names`,我们可以访问元素 在它的第二个位置像这样:

```js
console.log(`My name is ${ names[1] }.`);
// My name is Kyle.
```

我们用过`1`,代替`2`,因为与大多数编程语言一样,JS数组指数是0基(英语:`0`这是第一个位置)。

除了字符串、数字和布林斯之外,JS计划中还有两个“原始”值`null`和`undefined`虽然它们之间存在差异(有些是历史性的,有些是当代的),但大多数情况下,这两个值都是为了表明一个值的*空 * (或缺失)。

许多开发者倾向于以这种方式一致地对待两者,也就是说,这些值被假定是不可区分的. 如果谨慎行事,这往往是可能的。 不过,它最安全,最好只使用`undefined`作为单一的空值,尽管`null`看起来很吸引人,因为打字时间短了!

```js
while (value != undefined) {
    console.log("Still got something!");
}
```

最终要意识到的原始价值是一个符号,它是一种特殊目的价值,表现为一种隐藏的不可知价值. 符号几乎全部用作对象的特殊密钥:

```js
hitchhikersGuide[ Symbol("meaning of life") ];
// 42
```

在典型的JS节目中你不会经常遇到直接使用符号的情况. 它们大多用于图书馆和框架等低级代码.

### 矩阵和对象

除了原始人外,JS中的另一个价值类型是对象值.

如前所述,数组是一类特殊对象,由顺序和数字索引数据列表组成:

```js
var names = [ "Frank", "Kyle", "Peter", "Susan" ];

names.length;
// 4

names[0];
// Frank

names[1];
// Kyle
```

JS阵列可以持有任意的值类型,无论是原始的还是对象(包括其他阵列). 正如我们可以看到的,在第三章的结尾, 即使是函数,都是可以在数组或物体中持有的值。

| 注: |
| :--- |
| 函数与数组一样,是对象的一种特殊类型(aka,子类型). 稍稍详细论述功能. |

对象比较一般:一个没有顺序的,按键的任意各种值的集合. 换句话说,您通过字符串位置名称(aka "key" 或"property")而不是通过它的数值位置(如数组)来访问元素. 例如:

```js
var me = {
    first: "Kyle",
    last: "Simpson",
    age: 39,
    specialties: [ "JS", "Table Tennis" ]
};

console.log(`My name is ${ me.first }.`);
```

在这里,`me`表示对象,并且`first`表示该对象中信息位置的名称(值收集)。 另一个按对象属性/键访问对象中信息的语法选项使用方块括号`[ ]`,例如,`me["first"]`.

### 数值类型确定

对于区分价值,`typeof`操作员告诉你它的内置类型,如果原始,或者`"object"`否则:

```js
typeof 42;                  // "number"
typeof "abc";               // "string"
typeof true;                // "boolean"
typeof undefined;           // "undefined"
typeof null;                // "object" -- oops, bug!
typeof { "a": 1 };          // "object"
typeof [1,2,3];             // "object"
typeof function hello(){};  // "function"
```

| 警报: |
| :--- |
| `typeof null`不幸归来`"object"`而不是预期`"null"`还有,`typeof`返回特定`"function"`,但不包括预期`"array"`用于数组。 |

从一个值类型转换到另一个值类型,例如从字符串转换到数字,在JS中被称为"强迫". 我们将在本章稍后更详细地阐述这一点。

原始值和对象值在分配或传递时表现不同. 详见附录A"Values vs Reference".

## 宣告和使用变量

要明确说明上一节可能并不明显的东西:在联署材料程序中,数值可以作为文字值出现(如前述许多例子所说明的),也可以用变量来表示;把变量看作是价值的容器。

要使用变量必须声明(创建)。 有多种语法形式可以声明变量(aka,"识别符"),每种形式都有不同的暗示行为.

例如,考虑`var`语句:

```js
var myName = "Kyle";
var age;
```

那个`var`关键词宣布该部分程序将使用一个变量,并且可以选择允许一个值的初始指定.

另一个类似的关键词是`let`:

```js
let myName = "Kyle";
let age;
```

那个`let`关键词与`var`最明显的是,`let`允许比`var`。这被称为“块范围界定”,而不是常规或函数范围界定。

考虑:

```js
var adult = true;

if (adult) {
    var myName = "Kyle";
    let age = 39;
    console.log("Shhh, this is a secret!");
}

console.log(myName);
// Kyle

console.log(age);
// Error!
```

试图进入`age`范围`if`语句导致错误,因为`age`被挡住`if`,则`myName`没有。

封锁范围对于限制我们方案中的可变声明的广度非常有用,这有助于防止它们名称的意外重叠。

不过`var`仍然有用,因为它沟通的"这个变量将会被更广泛的范围(整个函数)所看到". 根据具体情况,两种声明表都可以适用于某一程序的任何特定部分。

| 注: |
| :--- |
| 这很常见`var`应避免赞成`let`(或`const`一般来说,由于人们感到困惑,无法理解如何界定`var`自联署材料开始以来就一直在工作。 我认为,这种建议限制性过强,最终无济于事。 假设你无法与其他特性结合学习和使用一个特性。 我相信,你能够 * 和 * 应该学习任何可用的特性, 并酌情使用它们! |

第三个声明表是:`const`这就像`let`但有一个额外的限制,它必须在宣布时被赋予一个值,并且不能在以后重新分配一个不同的值.

考虑:

```js
const myBirthday = true;
let age = 39;

if (myBirthday) {
    age = age + 1;    // OK!
    myBirthday = false;  // Error!
}
```

那个`myBirthday`常数不允许重新分配。

`const`已声明的变量不是“不可更改的”,只是不能重新指定。 没用的`const`带有对象值,因为这些值仍然可以更改,即使变量不能重新指定. 这导致潜在的混乱 下行, 所以我认为明智的 避免的情况,例如:

```js
const actors = [
    "Morgan Freeman", "Jennifer Aniston"
];

actors[2] = "Tom Cruise";   // OK :(
actors = [];                // Error!
```

一个词的最好用词`const`当您有一个简单的原始值, 您想要给它一个有用的名称, 例如使用`myBirthday`改为`true`。这使得程序更容易阅读。

| 提普: |
| :--- |
| 如果你坚持使用`const`只有原始值,你才能避免任何重新分配(不允许)对突变(允许)的混淆! 这是最安全最好的方法`const`. |

再说`var` / `let` / `const`,还有其它的合成形式,在各种范围中声明标识符(可变). 例如:

```js
function hello(myName) {
    console.log(`Hello, ${ myName }.`);
}

hello("Kyle");
// Hello, Kyle.
```

标识符`hello`在外部范围内创建,它也自动关联,以便它引用函数。 但命名的参数`myName`只创建于函数内部,因此只能在该函数范围内访问。`hello`和`myName`一般表现为`var`- 已经宣布了

宣告变量的另一种语法是`catch`条款:

```js
try {
    someError();
}
catch (err) {
    console.log(err);
}
```

那个`err`是一个仅存在于`catch`条款,仿佛以`let`.

## 职能

"函数"一词在编程中有着多种含义. 例如,在功能编程世界中,"功能"有一个精确的数学定义,并暗示要遵守一套严格的规则.

在联署材料中,我们应该考虑"功能",以取另一个相关术语的广义含义:"程序". 程序是指可以一次或多次引用,可能提供一些投入,并可能归还一项或多项产出的语句集合.

从JS成立初期起,功能定义看起来是:

```js
function awesomeFunction(coolThings) {
    // ..
    return amazingStuff;
}
```

这被称为函数声明,因为它是作为声明本身出现,而不是作为另一种声明的表达. 标识符之间的关联`awesomeFunction`和函数值发生于代码编译阶段,然后执行该代码。

相对于函数声明语句,函数表达式可以这样定义和指定:

```js
// let awesomeFunction = ..
// const awesomeFunction = ..
var awesomeFunction = function(coolThings) {
    // ..
    return amazingStuff;
};
```

此函数是指定给变量的表达式`awesomeFunction`。与函数声明格式不同的是,函数表达式与其标识符不相关,直到运行期间该语句。

极为重要的是,在JS中,函数是可以指定(如此片段所示)并传承的值. 事实上,JS函数是对象值类型的特殊类型. 并非所有语言都把函数视为值,但如JS一样,一种语言支持函数编程模式至关重要.

JS功能可以接收参数输入:

```js
function greeting(myName) {
    console.log(`Hello, ${ myName }!`);
}

greeting("Kyle");   // Hello, Kyle!
```

在这个片段,`myName`是一个参数,它作为函数内的局部变量。 函数可以被定义为接收任意数量的参数,从不向上,如你所见. 每个参数都被指定了您在此位置传递的参数值( X)`"Kyle"`(这里)电话。

函数还可以使用`return`关键字:

```js
function greeting(myName) {
    return `Hello, ${ myName }!`;
}

var msg = greeting("Kyle");

console.log(msg);   // Hello, Kyle!
```

你只能这样`return`单值,但如果返回的值更多,则可以将它们组合成单个对象/阵列。

由于函数是值,它们可以在对象上被指定为属性:

```js
var whatToSay = {
    greeting() {
        console.log("Hello!");
    },
    question() {
        console.log("What's your name?");
    },
    answer() {
        console.log("My name is Kyle.");
    }
};

whatToSay.greeting();
// Hello!
```

在此片断中,提及三个函数(`greeting()`, `question()`,以及`answer()`) 包含在由`whatToSay`。每个函数可以通过访问属性来调取函数引用值。 将定义对象上的函数的这种直截了当的样式比作更复杂的`class`本章后面讨论的语法.

有很多不同的形式`function`接受联署材料。 我们在附录A“许多函数形式”中 研究了这些变化。

## 比较

在节目中做决策需要比较价值以确定其身份和彼此的关系. 联合来文有几种机制可以进行价值比较,让我们仔细研究一下。

### 一样...

JS节目中最常见的比较质问:"这个X值是否与Y值相同? 但"同样"对JS到底意味着什么?

由于人机学和历史的原因,其意义比明显的*精确身份*某种匹配更为复杂. 有时,平等比较意在 * 准确 * 匹配,但有时,理想的比较要略为宽泛,允许 * 近似 * 或 * 可互换 * 匹配. 换句话说,我们必须认识到**平等**比较和**等同**比较之间的细微差别。

如果你花任何时间与JS合作并阅读,你肯定看到了所谓的"三平等".`===`运算符,又称"严格平等"运算符. 这似乎相当直截了当,对不对? 当然,"严格"的意思是严格,如狭义和"准确的".

不,没有。

是,大多数参加`===`平等比较将符合这种*准确的相同*直觉。 举几个例子:

```js
3 === 3.0;              // true
"yes" === "yes";        // true
null === null;          // true
false === false;        // true

42 === "42";            // false
"hello" === "Hello";    // false
true === 1;             // false
0 === null;             // false
"" === null;            // false
null === undefined;     // false
```

| 注: |
| :--- |
| 换个方式`===`'平等比较经常被描述为"检查值和类型". 在几个例子中,我们已经研究了到目前为止,像`42 === "42"`,两个值(数字、字符串等)的*类型*似乎是区分因素。 虽复有余. ** 所有** 联合来文中的价值比较都考虑到所比较的数值的类型,而不只是`===`操作员。 具体来说,`===`禁止任何类型的转换(aka,"强制")在其比较中,其他JS比较*do*允许强制. |

不过`===`操作员确实对它有一些细微的差别,事实上,许多联署材料的开发者都抹黑了,对他们不利。 那个`===`运算符旨在在两种特殊值的情况下 * 否认 *:`NaN`和`-0`考虑:

```js
NaN === NaN;            // false
0 === -0;               // true
```

在涉及`NaN`,则`===`操作员 * 显示 * 并说`NaN`不等于另一个`NaN`。如果`-0`(是的,这是一个真实的,独特的价值 你可以在您的程序中故意使用!),该`===`操作员 * 显示 * 并说它等于普通`0`数值。

由于关于这种比较的*LOY*是麻烦的,所以最好避免使用`===`为了他们 对于`NaN`比较,使用`Number.isNaN(..)`工具,不包含 *。 对于`-0`比较,使用`Object.is(..)`工具,该工具也并非 *lie *。`Object.is(..)`也可用于非*地面*`NaN`支票,如果你喜欢。 谦虚的,你可以想到`Object.is(..)`作为"等价"`====`真正的限制比较!

但这并不改变一个事实:`===`在“最严格”意义上,实际上不是“严格完全相等”的比较。

当我们考虑对象值(非原始值)的比较时,故事变得更加复杂. 考虑:

```js
[ 1, 2, 3 ] === [ 1, 2, 3 ];    // false
{ a: 42 } === { a: 42 }         // false
(x => x * 2) === (x => x * 2)   // false
```

这是怎么回事?

也许可以合理地假设,平等检查会考虑价值的 * 性质 * 或 * 内容 *;毕竟,`42 === 42`考虑实际`42`价值和比较。 但当涉及物体时,一个内容意识的比较一般被称为"结构平等".

JS没有定义`===`作为对象值的*结构平等*. 相反,`===`对对象值使用“身份平等”。

在联署材料中,所有对象值都是以参考方式持有的(见附录A中的"Values vs Reference"),通过参考副本来指定和传递,**和**与我们目前的讨论通过参考(身份)平等来比较. 考虑:

```js
var x = [ 1, 2, 3 ];

// assignment is by reference-copy, so
// y references the *same* array as x,
// not another copy of it.
var y = x;

y === x;              // true
y === [ 1, 2, 3 ];    // false
x === [ 1, 2, 3 ];    // false
```

在这个片段,`y === x`是真的,因为这两个变量都有一个相同的初始数组的引用。 不过`=== [1,2,3]`比较都失败,因为`y`和`x`正在分别与新“ 不同” 数组进行比较`[1,2,3]`。在比较中,数组结构和内容不重要,只有**参考身份**。

联合来文没有为物体值的结构平等比较提供机制,只是参考身份比较。 为了进行结构平等比较,你需要自己执行检查.

但当心,事情比你想的复杂 例如,如果两个函数引用是"结构等同的",你如何确定? 连用来比较源代码文本的字符串都不会考虑关闭等内容. JS没有提供结构平等比较,因为处理所有角落案件几乎都难以解决!

### 强制比较

强迫是指一种类型的价值被转换成在另一种类型的各自的代表(尽可能)。 正如我们将在第四章中讨论的那样,胁迫是联署材料语言的核心支柱,而不是一些可以合理避免的可选特征.

但遗憾的是,在胁迫与比较操作者相遇(如平等)的情况下,往往会出现混乱和挫折。

联署材料在更广泛的联署材料社群中所占的比重比`==`经营者,一般简称为" Loose equality"经营者. 关于联署材料的所有著作和公开论述中,大多数都谴责该操作员在联署材料方案中使用时设计不良、危险/充斥。 甚至语言的创造者布兰登·艾希本人也曾对它是如何被设计成一个大错误而感到悲叹.

据我所知,这种挫折大多来自一个很短的、令人困惑的角落案例清单,但更深层的问题是,在不考虑其比较价值的类型的情况下,它进行比较是一种极为普遍的误解。

那个`==`操作员进行平等比较的方式类似于`===`执行它。 事实上,两个操作符都考虑比较的值类型. 如果比较是同一数值类型 两者`==`和`===`做同样的事情,没有任何区别。

如果比较的数值类型不同,则`==`与`===`因为它允许在比较之前进行胁迫。 换句话说,他们都想比较类似类型的数值,但是.`==`允许类型转换 * 首先是 *, 一旦类型转换为两侧相同, 那么`==`做同样的事情与`===`而不是"罗丝平等"`==`操作者应当被描述为"强制平等".

考虑:

```js
42 == "42";             // true
1 == true;              // true
```

在这两种比较中,数值类型不同,因此`==`导致非数字值(`"42"`和`true`改为数字(`42`和`1`在进行比较之前。

只是意识到这种性质`==`——它更喜欢原始的数字比较——帮助你避免了大部分麻烦的角落案例,例如远离像Getchas这样的.`"" == 0`或`0 == false`.

你可能会在想,"哦,好吧,我只是会永远避免任何强制的平等比较(使用`===`来避免那些角落的案子!" 呃,对不起,那不太可能 像你希望的那样

There's a pretty good chance that you'll use relational comparison operators like `<`, `>` (and even `<=` and `>=`).

碞钩`==`,这些运算符会表现为"限制",如果将类型相对比起来已经匹配,但是如果类型不同,它们会允许先强制(一般对数字).

考虑:

```js
var arr = [ "1", "10", "100", "1000" ];
for (let i = 0; i < arr.length && arr[i] < 500; i++) {
    // will run 3 times
}
```

那个`i < arr.length`比较是“安全”不受胁迫,因为`i`和`arr.length`总是数字。 那个`arr[i] < 500`但是,由于`arr[i]`值都是字符串。 因此,这些比较成为:`1 < 500`, `10 < 500`, `100 < 500`,以及`1000 < 500`。由于第四套是虚假的,循环在第三套之后停止。

这些关系运算符通常使用数字比较,除非****两个正在比较的值已经是字符串;在这种情况下,它们使用字符串的字母(类似字典)比较:

```js
var x = "10";
var y = "9";

x < y;      // true, watch out!
```

没有办法让这些关系操作员避免强迫,除了在比较中永远不要使用不匹配的类型. 这或许是一个令人钦佩的目标, 但是它仍然很有可能 你会遇到一个案件 类型可能不同。

较明智的方法不是避免强制比较,而是拥抱和学习他们的进出.

强制比较在联署材料的其他地方出现,例如有条件的(`if`),我们会在附录A"强制条件比较"中重温.

## 我们在联署材料中如何组织

组织编码的两种主要模式(数据和行为)在JS生态系统中广泛使用:类和模块。 这些模式并不是相互排斥的;许多程序可以并且确实使用两者. 其它程序会坚持一个模式,甚至两个模式都不行!

在某些方面,这些模式非常不同. 但有趣的是,在其他方面,它们只是同一个硬币的不同面. 精通联署材料需要既了解模式,又了解其适当之处(而非!)

### 类

"面向对象","面向阶级","阶级"等术语,都充满了丰富的细节和细微;在定义上并不普遍.

我们将在这里使用一个共同的和有些传统的定义,这个定义最有可能为C++和Java等具有"面向对象"语言背景的人所熟悉.

一个程序中的一个类是自定义数据结构的"类型"的定义,它既包括数据,也包括在该数据上操作的行为. 类定义了这样的数据结构是如何运作的,但类本身并不是具体值. 为了获得一个可以在程序中使用的具体值,一个课程必须“证明” (与`new`关键词)一次或多次.

考虑:

```js
class Page {
    constructor(text) {
        this.text = text;
    }

    print() {
        console.log(this.text);
    }
}

class Notebook {
    constructor() {
        this.pages = [];
    }

    addPage(text) {
        var page = new Page(text);
        this.pages.push(page);
    }

    print() {
        for (let page of this.pages) {
            page.print();
        }
    }
}

var mathNotes = new Notebook();
mathNotes.addPage("Arithmetic: + - * / ...");
mathNotes.addPage("Trigonometry: sin cos tan ...");

mathNotes.print();
// ..
```

在那个`Page`分类中,该数据是存储在`this.text`成员财产。 行为是`print()`,一种将文本倾倒到控制台的方法。

对于`Notebook`分类,数据是`Page`实例。 行为是`addPage(..)`,即时化新的方法`Page`页面并添加到清单上,以及`print()`(打印出笔记本中的所有页面).

声明`mathNotes = new Notebook()`创建实例`Notebook`类,和`page = new Page(text)`是指`Page`类被创建。

行为(方法)只能用于实例(而不是类本身),例如:`mathNotes.addPage(..)`和`page.print()`.

那个`class`机制允许包装数据(`text`和`pages`)与行为(例如,`addPage(..)`和`print()`) (中文(简体) ). 同样的程序本来可以建造而无需任何`class`定义,但是它很可能组织得要少得多,阅读和解释起来要困难得多,而且更容易受到bugs和subpar维护的影响.

#### 类继承

传统"面向阶级"设计所固有的另一个方面,虽然在JS中用得少一些,但"继承"(和"多变主义"). 考虑:

```js
class Publication {
    constructor(title,author,pubDate) {
        this.title = title;
        this.author = author;
        this.pubDate = pubDate;
    }

    print() {
        console.log(`
            Title: ${ this.title }
            By: ${ this.author }
            ${ this.pubDate }
        `);
    }
}
```

这个`Publication`类定义了任何出版物可能需要的一套共同行为。

现在让我们考虑更具体的出版类型 比如`Book`和`BlogPost`:

```js
class Book extends Publication {
    constructor(bookDetails) {
        super(
            bookDetails.title,
            bookDetails.author,
            bookDetails.pubDate
        );
        this.publisher = bookDetails.publisher;
        this.ISBN = bookDetails.ISBN;
    }

    print() {
        super.print();
        console.log(`
            Publisher: ${ this.publisher }
            ISBN: ${ this.ISBN }
        `);
    }
}

class BlogPost extends Publication {
    constructor(title,author,pubDate,URL) {
        super(title,author,pubDate);
        this.URL = URL;
    }

    print() {
        super.print();
        console.log(this.URL);
    }
}
```

两者`Book`和`BlogPost`使用`extends`* 扩大 *`Publication`包括额外的行为。 那个`super(..)`给每个建筑师的代表打电话`Publication`class's buildinger for its初始化工作,然后他们根据各自的出版类型(aka,"子类"或"子类")做更具体的事情.

现在考虑使用这些儿童班:

```js
var YDKJS = new Book({
    title: "You Don't Know JS",
    author: "Kyle Simpson",
    pubDate: "June 2014",
    publisher: "O'Reilly",
    ISBN: "123456-789"
});

YDKJS.print();
// Title: You Don't Know JS
// By: Kyle Simpson
// June 2014
// Publisher: O'Reilly
// ISBN: 123456-789

var forAgainstLet = new BlogPost(
    "For and against let",
    "Kyle Simpson",
    "October 27, 2014",
    "https://davidwalsh.name/for-and-against-let"
);

forAgainstLet.print();
// Title: For and against let
// By: Kyle Simpson
// October 27, 2014
// https://davidwalsh.name/for-and-against-let
```

通知两个儿童班的情况`print()`方法,是“继承”的重置`print()`从父方法`Publication`课。 每个被打倒的孩子班`print()`方法调用`super.print()`以引用继承的版本`print()`方法。

继承的方法和被推翻的方法都可以有同一种名称和共存的事实被称为*polymorphism*.

继承是将数据/行为组织在单独的逻辑单元(类)中的强大工具,但允许儿童阶层通过访问/使用其行为和数据与父母合作.

### 模块

模块模式与类模式基本相同,即将数据和行为组合成逻辑单元. 同样和类一样,模块可以"包含"或"访问"其他模块的数据和行为,以配合.

但模块与班级有一些重要的区别. 最显著的是,语法完全不同.

#### 经典模块

ES6在本土JS语法中增加了模块语法表,我们稍后再研究. 但是,从联署材料的早期开始,模块就是一种重要和共同的模式,被无数联署材料方案所利用,即使没有专门的语法。

*经典模块*的关键标志是外函数(至少运行一次),它返回模块的"内置",其一个或多个函数暴露,可以在模块实例的内部(隐藏)数据上运行.

因为这种形式的模块是*只是函数*,并称它产生模块的"内涵",所以对这些功能的另一种描述是"模块工厂".

考虑早期的经典模块形式`Publication`, `Book`,以及`BlogPost`类:

```js
function Publication(title,author,pubDate) {
    var publicAPI = {
        print() {
            console.log(`
                Title: ${ title }
                By: ${ author }
                ${ pubDate }
            `);
        }
    };

    return publicAPI;
}

function Book(bookDetails) {
    var pub = Publication(
        bookDetails.title,
        bookDetails.author,
        bookDetails.publishedOn
    );

    var publicAPI = {
        print() {
            pub.print();
            console.log(`
                Publisher: ${ bookDetails.publisher }
                ISBN: ${ bookDetails.ISBN }
            `);
        }
    };

    return publicAPI;
}

function BlogPost(title,author,pubDate,URL) {
    var pub = Publication(title,author,pubDate);

    var publicAPI = {
        print() {
            pub.print();
            console.log(URL);
        }
    };

    return publicAPI;
}
```

将这些表格与`class`形式,其相似性大于差异。

那个`class`窗体存储对象实例的方法和数据,必须使用`this.`前缀。 有了模块,方法和数据就可以作为范围上的标识变量访问,而没有任何`this.`前缀。

与`class`,一个实例的"API"隐含在类定义中——此外,所有的数据和方法都是公开的. 在模块工厂功能下,您明确创建并返回一个具有任何公开曝光方法的物体,任何数据或其他无参考方法在工厂功能内仍然保持私有.

即使在2020年,在联合来文中,这种工厂功能形式还有其他很常见的变异;在不同的联合来文程序中,你可能跨越这些形式:AMD(同步模块定义)、UMD(通用模块定义)和CommonJS(经典节点.js风格模块)。 变化不大(不完全兼容)。 然而,所有这些形式都依赖同样的基本原则。

还考虑这些模块工厂功能的用法(aka, “证明” ):

```js
var YDKJS = Book({
    title: "You Don't Know JS",
    author: "Kyle Simpson",
    publishedOn: "June 2014",
    publisher: "O'Reilly",
    ISBN: "123456-789"
});

YDKJS.print();
// Title: You Don't Know JS
// By: Kyle Simpson
// June 2014
// Publisher: O'Reilly
// ISBN: 123456-789

var forAgainstLet = BlogPost(
    "For and against let",
    "Kyle Simpson",
    "October 27, 2014",
    "https://davidwalsh.name/for-and-against-let"
);

forAgainstLet.print();
// Title: For and against let
// By: Kyle Simpson
// October 27, 2014
// https://davidwalsh.name/for-and-against-let
```

这里唯一可见的区别是没有使用`new`,称模块工厂为正常功能.

#### ES 模块

在ES6中引入联署材料语言的ES模块(ESM)旨在服务于上述现有*经典模块* 的精神和目的,特别是考虑到AMD、UMD和CommonJS的重要变化和使用案例。

然而,执行办法确实大不相同。

首先,对*define * 模块没有包接功能. 包装上下文是一个文件。 无害环境管理总是基于文件;一个文件,一个模块。

第二,你并不与模块的"API"明确互动,而是使用`export`关键词用于在其公开的API定义中添加变量或方法. 如果一个模块中定义了某种内容, 但定义不是`export`ed,然后它保持隐藏(和*经典模块*一样).

第三,也许与之前讨论的规律最显著的区别, 你并不"证明"一个ES模块,你只是`import`它使用它的单一实例。 无害环境管理实际上就是 "singletons", 因为只有一个例子 曾经创建,起初`import`在你的节目,和所有其他`import`仅仅得到一个引用 同一单一的例子。 如果您的模块需要支持多个即时功能,您必须在您的无害环境管理定义上提供一个*经典模块式*工厂功能。

在我们运行中的例子中,我们确实假定了多重证据,因此,这些后面的片段将同时混合无害环境管理和*经典模块*。

考虑文件`publication.js`:

```js
function printDetails(title,author,pubDate) {
    console.log(`
        Title: ${ title }
        By: ${ author }
        ${ pubDate }
    `);
}

export function create(title,author,pubDate) {
    var publicAPI = {
        print() {
            printDetails(title,author,pubDate);
        }
    };

    return publicAPI;
}
```

要导入和使用此模块, 请从另一个 ES 模块中导入并使用。`blogpost.js`:

```js
import { create as createPub } from "publication.js";

function printDetails(pub,URL) {
    pub.print();
    console.log(URL);
}

export function create(title,author,pubDate,URL) {
    var pub = createPub(title,author,pubDate);

    var publicAPI = {
        print() {
            printDetails(pub,URL);
        }
    };

    return publicAPI;
}
```

最后,为了使用这个模块,我们导入另一个ES模块,比如:`main.js`:

```js
import { create as newBlogPost } from "blogpost.js";

var forAgainstLet = newBlogPost(
    "For and against let",
    "Kyle Simpson",
    "October 27, 2014",
    "https://davidwalsh.name/for-and-against-let"
);

forAgainstLet.print();
// Title: For and against let
// By: Kyle Simpson
// October 27, 2014
// https://davidwalsh.name/for-and-against-let
```

| 注: |
| :--- |
| 那个`as newBlogPost`条款`import`语句是可选的; 如果省略, 刚刚命名的顶级函数`create(..)`将输入。 在这种情况下,我为了可读性而改名它; 它更通用的工厂名称是:`create(..)`更确切地描述其宗旨:`newBlogPost(..)`. |

如上所示,ES模块如果需要支持多证性,可以内部使用*经典模块*. 或者,我们本可以暴露`class`从我们的模块而不是一个`create(..)`工厂功能,结果一般相同. 然而,既然你当时已经在使用无害环境管理,我建议继续使用*经典模块*而不是`class`.

如果您的模块只需要一个实例, 您可以跳过额外的复杂层:`export`它的公开方法直接。

## 兔子洞深处

正如在本章的顶部所承诺的那样,我们只是看一下了JS语言主要部分的广阔表面。 你的头可能还在转, 但这是完全自然的 经过这样一个火爆的信息!

即使只是JS的"简略"调查, 我们覆盖或暗示了一吨细节 你应该仔细考虑 并确保你舒适。 我是认真的,当我建议: 重读这一章,也许几次。

在接下来的一章中,我们将深入挖掘JS在核心工作方式的一些重要方面. 但在你更深入地跟踪兔子洞之前 确保你有足够的时间 充分消化我们刚刚覆盖的东西
