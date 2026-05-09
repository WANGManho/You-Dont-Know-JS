# 你不认识JS然而:开始 - 第二版
# 附录A:进一步探索

在本附录中,我们将更详细地从主要章节文本中探讨一些话题. 把这个内容看作是一些更细微的细节的可选预览,涵盖整个书系列的其他部分.

## 数值与参考文献

在第二章中,我们介绍了两种主要的价值观:原始的和物体。 但我们还没有讨论两者之间的一个关键区别: 这些价值是如何分配和传递的。

在许多语言中,开发者可以在指定/通过一个值作为值本身之间,或者作为该值的参考物来选择. 然而,在联署材料中,这一决定完全取决于其价值。 这令许多来自其他语言的开发者在开始使用JS时感到惊讶.

如果指定/通过一个值本身,则该值会被复制。 例如:

```js
var myName = "Kyle";

var yourName = myName;
```

给`yourName`变量有一个单独的副本`"Kyle"`从存储在其中的值的字符串`myName`,这是因为该值是原始的,原始值总是被指定/通过为**值副本**.

这里可以证明其中包含两个不同的价值观:

```js
var myName = "Kyle";

var yourName = myName;

myName = "Frank";

console.log(myName);
// Frank

console.log(yourName);
// Kyle
```

看怎么样`yourName`没有受到重新分配的影响`myName`改为:`"Frank"`这是因为每个变量 持有自己的复制值。

与此相反,引用是两个或两个以上变量指向相同价值的想法,因此修改这一共同价值将通过通过其中任何一种引用访问而得到反映。 在联署材料中,只有对象值(阵列、对象、函数等)作为参考。

考虑:

```js
var myAddress = {
    street: "123 JS Blvd",
    city: "Austin",
    state: "TX"
};

var yourAddress = myAddress;

// I've got to move to a new house!
myAddress.street = "456 TS Ave";

console.log(yourAddress.street);
// 456 TS Ave
```

因为指定给`myAddress`是一个物体,它被持有/指定作为参考,因此,转让给`yourAddress`变量是引用的复制件,而不是对象值本身。 因此,更新后的价值被分配给`myAddress.street`当我们进入时会反映`yourAddress.street`. `myAddress`和`yourAddress`拥有对单个共享对象的引用副本,所以对其中之一的更新是对两者的更新.

联合来文再次根据价值类型选择价值副本与参考副本行为。 原始物按价值持有,对象按参考物持有. 在JS中,无论是哪一个方向,都无法推翻这一点.

## 所以许多函数表单

回顾第2章中"功能"一节的这一段:

```js
var awesomeFunction = function(coolThings) {
    // ..
    return amazingStuff;
};
```

这里的函数表达式被称为“ 匿名函数表达式*”, 因为它在`function`关键词和`(..)`参数列表。 因为截至ES6, JS对匿名功能进行"姓名推论":

```js
awesomeFunction.name;
// "awesomeFunction"
```

那个`name`函数的属性将披露其直接给定的名称(在声明的情况下),或匿名函数表达情况下的推定名称。 该值一般被开发者工具在检查函数值或报告错误堆栈跟踪时使用.

所以即使是匿名函数表达式 * might * 也会得到一个名字. 然而,名称推论只在有限的情况下发生,例如函数表达式被指定时(与`=`) (中文(简体) ). 如果将函数表达式作为参数传递给函数调用,例如,不发生名称推论;`name`属性将是空字符串,开发者控制台通常会报告"(匿名函数)".

即使一个名字被推断出来,**它仍然是一个匿名功能。** 为什么? 因为推断的名称是一个元数据字符串值,而不是可用的标识符来引用函数. 匿名功能没有标识符可以用来从内部指代自己——用于重复,事件无约束力等.

将匿名函数表达式表单与:

```js
// let awesomeFunction = ..
// const awesomeFunction = ..
var awesomeFunction = function someName(coolThings) {
    // ..
    return amazingStuff;
};

awesomeFunction.name;
// "someName"
```

此函数表达式是 * name 函数表达式 *, 因为标识符`someName`与汇编时函数表达式直接相关; 与标识符关联`awesomeFunction`直到发言时的运行时间才发生 这两个识别符不必相匹配;有时有区别是合情合理的,其他时候最好有相同的.

并通知明确函数名称、标识符`someName`,在指定 * 名称时优先。`name`属性。

函数表达式应该命名还是匿名? 关于这一点,意见大不相同。 大多数开发者往往不关心匿名功能的使用. 它们比较短,在JS代码的广大领域无疑更常见.

我认为,如果你的程序中存在一个功能,它就有目的;否则,把它拿出来! 如果它有一个目的,它有一个自然的名字来描述这个目的.

如果一个函数有一个名称,那么代码作者应该将该名称包含在代码中,这样读者就不必从读取和精神执行该函数的源代码中推断出这个名称. 甚至一个微不足道的功能体`x * 2`必须读取一个像"double"或"multBy2"这样的名字;这种短暂的额外精神工作是不必要的,当你只需花一秒钟就可以给功能命名"double"或"multBy2"*once*,挽救了以后每读一次就重复精神工作的读者.

遗憾的是,截至2020年初,联署材料中还有许多其他职能定义形式(今后可能更多!)。

以下是一些宣言表格:

```js
// generator function declaration
function *two() { .. }

// async function declaration
async function three() { .. }

// async generator function declaration
async function *four() { .. }

// named function export declaration (ES6 modules)
export function five() { .. }
```

这里还有一些函数表达形式:

```js
// IIFE
(function(){ .. })();
(function namedIIFE(){ .. })();

// asynchronous IIFE
(async function(){ .. })();
(async function namedAIIFE(){ .. })();

// arrow function expressions
var f;
f = () => 42;
f = x => x * 2;
f = (x) => x * 2;
f = (x,y) => x * y;
f = x => ({ x: x * 2 });
f = x => { return x * 2; };
f = async x => {
    var y = await doSomethingAsync(x);
    return y * 2;
};
someOperation( x => x * 2 );
// ..
```

记住箭头函数表达式是**在战术上是匿名的**,这意味着语法没有为函数提供直接名称标识符的方法. 函数表达式可能获得一个推断名称,但只有它是指派表单之一,而不是作为函数调用参数(如片断的最后一行)传递的(更常见的!)形式.

我觉得匿名功能不适合在你的节目中频繁使用,所以我不喜欢使用`=>`箭头函数形式。 这种功能实际上有一个特定的目的(即处理`this`关键词在逻辑上是),但这并不意味着我们应该用它来完成我们写的每一个功能. 为每项工作使用最适当的工具。

函数也可以在类定义和对象字面定义中指定. 虽然JS这个术语与"功能"没有太大的明显区别:

```js
class SomethingKindaGreat {
    // class methods
    coolMethod() { .. }   // no commas!
    boringMethod() { .. }
}

var EntirelyDifferent = {
    // object methods
    coolMethod() { .. },   // commas!
    boringMethod() { .. },

    // (anonymous) function expression property
    oldSchool: function() { .. }
};
```

嘘! 定义函数的方法有很多不同.

这里没有简单的快捷路径;你只需要建立对所有函数表的熟悉度,这样你就可以在现有的代码中识别它们,并在你写的代码中适当使用它们. 仔细研究,练习!

## 强制条件比较

是的,那个区的名字很臭 但我们在谈论什么? 我们谈论的是有条件的表达方式,需要进行面向强制的比较来做出决策.

`if`和`? :`- 长期声明,以及`while`和`for`循环,全部执行隐含值比较。 什么样的? 是"严格"还是"强制"? 其实都是

考虑:

```js
var x = 1;

if (x) {
    // will run!
}

while (x) {
    // will run, once!
    x = false;
}
```

你可能会想到这些`(x)`像这样的有条件表达式:

```js
var x = 1;

if (x == true) {
    // will run!
}

while (x == true) {
    // will run, once!
    x = false;
}
```

在这个具体情况下 -- -- 价值`x`状态`1`- 那个心理模型有效, 但它并不准确 更广义。 考虑:

```js
var x = "hello";

if (x) {
    // will run!
}

if (x == true) {
    // won't run :(
}
```

哎哟 那么,这是什么`if`声明真的做了吗? 这是更准确的心理模型:

```js
var x = "hello";

if (Boolean(x) == true) {
    // will run
}

// which is the same as:

if (Boolean(x) === true) {
    // will run
}
```

自从`Boolean(..)`函数总是返回类型布尔的值,`==`数字`===`在这个片段里是无关紧要的,他们都会做同样的事情. 但重要的是在比较之前 无论是哪种类型的胁迫`x`现在, 要布尔。

在JS比较中 你无法逃避胁迫 系好安全带,学他们

## 原生的"细胞"

在第三章中,我们引入了原型,并展示了如何通过原型链连接物体.

这种原型连接的另一种方式是(诚实、丑陋)ES6优雅的前身`class`系统(参见第2章"Classes"),又被称为原生类.

| 提普: |
| :--- |
| 虽然这种守则风格在联署部队中相当罕见, |

让我们首先回顾`Object.create(..)`编码样式:

```js
var Classroom = {
    welcome() {
        console.log("Welcome, students!");
    }
};

var mathClass = Object.create(Classroom);

mathClass.welcome();
// Welcome, students!
```

这里,一个`mathClass`对象通过其原型链接到 a`Classroom`对象。 通过这个链接,函数调用`mathClass.welcome()`授权给定义的方法`Classroom`.

原型的阶级模式会给这个代表团的行为贴上"继承"的标签,或者将其(具有相同行为)定义为:

```js
function Classroom() {
    // ..
}

Classroom.prototype.welcome = function hello() {
    console.log("Welcome, students!");
};

var mathClass = new Classroom();

mathClass.welcome();
// Welcome, students!
```

默认情况下所有函数都引用一个位于命名属性上的空对象`prototype`尽管命名方式混乱,但这不是****函数的*原型*(函数原型与该原型相连),而是在通过调用函数生成其他对象时,将*链接到*的原型对象。`new`.

我们加一个`welcome`空对象的属性(称为`Classroom.prototype`),指着`hello()`函数。

礛`new Classroom()`创建新对象( 指定给`mathClass`,并原型将其与现有的`Classroom.prototype`对象。

虽然`mathClass`没有`welcome()`财产/职能,它成功地代表该职能`Classroom.prototype.welcome()`.

这种"原产阶级"模式现在被强烈劝阻,赞成使用ES6's.`class`机制:

```js
class Classroom {
    constructor() {
        // ..
    }

    welcome() {
        console.log("Welcome, students!");
    }
}

var mathClass = new Classroom();

mathClass.welcome();
// Welcome, students!
```

在封面下,同一个原型连接被连接, 但这个`class`语法符合面向阶级的设计模式,比"原型班"要干净得多.
