# 你不了解JS然而:对象和类 - 第2版
# 第4章:本作品

| 注: |
| :--- |
| 进行中的工作 |

我们见过`this`关键词到目前为止已经用了很多, 但还没有真正挖掘出来 来理解它是如何在JS工作。 是时候了

但为了正确理解`this`在联署材料中,你需要撇开你可能持有的任何成见,特别是从如何对待这些成见的假设。`this`以你可能具有经验的其他编程语言工作.

这是最重要的事情 了解`this`: 确定什么值(通常是对象)`this`点数不是在作者时间提出,而是在运行时间确定。 这说明你不能简单地看一个`this`- 认识函数(即使是在`class`(定义),并明确知道什么`this`将在此函数运行时保存。

相反,你必须找到每个函数被引用的地方, 并且看看它是如何被引用的(甚至没有在 * 事项中)。 这是完全回答问题的唯一方法`this`将指出。

事实上,一个`this`-认识到至少可以使用四种不同的方式,而其中任何一种方法最终会指定一种不同的`this`为此项特定职能援引。

所以在读代码时,我们可能会问一个典型的问题:`this`指向函数”——实际上不是一个有效的问题。 你真正要问的问题是,“当函数被以某种方式引用时,`this`将因这件事而受派遣。

如果你的大脑已经曲折 仅仅读这章的介绍... 好! 冷静点 重新思考你的想法`this`《联合提交的材料》。

## 这个知道

我用了这句话`this`- 刚刚才意识到 但我到底想说什么?

任何具有`this`关键词在里面。

如果一个函数没有`this`在任何地方,然后规则如何`this`行为不会以任何方式影响这种功能. 但是,如果它 * 做 * 甚至有一个`this`在其中,那么你绝对不能确定函数将如何运作,而不弄清楚,每次引用函数时,`this`将指出。

这有点像`this`关键词是一个模板中的占位符。 占位符的值置换在我们编写代码时没有得到确定;它在代码运行时得到确定.

你可能觉得我只是玩文字游戏 当然,当你写程序时,你写出每个功能的所有呼叫,所以你已经确定了什么是`this`当你写出代码时,对不对? 对吧?

别这么快!

首先,你并不总是写出所有引用你函数的代码. 你们`this`- 知觉函数可能作为召回符传递到其他代码,或者在您的代码基数中,或者在第三方框架/功能中,或者甚至在主机语言或环境的内置机制内.

但是,除了作为回调的传递功能外,JS中的几个机制允许有条件的运行时间行为来确定将设定哪些值(又一个,通常为客体)用于设定值.`this`援引。 因此,即使你可能已经写出了所有代码,你“最好”将不得不在精神上执行不同的条件/路径,最终影响函数的引用。

为什么这一切都很重要?

因为不仅仅是你,代码的作者, 需要找出这些东西。 这是"每一个读者" 你的代码,永远。 如果有人(甚至你未来的自我)想要读取一个定义一个`this`——意识到功能,这不可避免地意味着,为了充分理解和预测其行为,这个人必须找到,阅读,并理解该功能的每一个引用.

### 这让我困惑

公平地说,如果我们考虑一个函数的参数,那已经是部分正确。 为了了解一项职能将如何运作,我们需要知道它正在传递什么。 因此,任何至少有一个参数的函数,在类似意义上是*参数*-意识到——意思是,什么参数被传递/被分配到函数的参数中.

但是,用参数,我们经常从函数本身得到一些提示,这些参数会做什么和持有。

我们经常看到函数标题中宣布正确的参数名称,这非常有助于解释其性质/目的。 如果有默认参数,我们经常看到它们与`= whatever`条款。 此外,根据作者的代码风格,我们可以在函数的前几行看到一套适用于这些参数的逻辑;这可以是对数值(被丢弃的值等)的断言,甚至修改(类型转换,格式化等).

其实`this`与函数的参数非常相似,但它是一个隐含的参数,而不是一个明确的参数. 你没看到任何信号`this`将会在任何地方的函数标题中使用。 您必须读取整个函数体以查看是否`this`在任何地方出现。

"参数"的名字总是`this`,所以我们无法从这样的通用名称中得到多少关于它的性质/目的的提示. 事实上,在历史上,对于"这个"甚至应该是什么意思,也有很多混淆. 我们很少看到,如果做任何事情 验证/转换/etc`this`用于函数引用的值。 事实上,几乎所有`this`-我知道我看到的代码...`this`“参数”所持的正是预期值。 说到一个陷阱 出乎意料的虫子!

### 这是什么?

若为`this`是一个隐含的参数,它的目的是什么? 什么传入?

希望您已经读过本系列的“范围与关闭”一书。 如果不行,我强烈鼓励你回转 读完这个之后 在该书中,我详细解释了范围(和关闭!)是如何运作的,这是功能的一个特别重要的特点。

语法范围(包括关闭的所有变量)代表了要对照该函数的语法标识引用来评价的*静态*上下文. 这是固定的/静态的,因为在作者的时间,当你将函数和可变声明置于各种(已记)范围时,这些决定是固定的,不受任何运行时间条件的影响.

相反,不同的编程语言可能提供 * 动态* 范围,其中函数可变引用的上下文不是由作者-时间决定,而是由运行时间条件决定. 这种系统无疑比静态环境更灵活 -- -- 虽然灵活性往往带来复杂性。

简而言之:联署材料的范围是始终的,而且只有逻辑性和“静态性”(如果我们忽视非限制性模式的欺骗)`eval(..)`和`with`) (中文(简体) ). 然而,联署材料的一个真正强大的内容是,它提供了另一个具有类似灵活性和能力的机制,以 * 动态 * 范围。

那个`this`(a) 机制实际上是指 * 动态* 上下文(而不是范围);`this`-意识到功能可以被动态地针对不同的背景引用——用关闭和词典范围标识符是不可能实现的!

### 为什么这是"隐性"?

你可能会想,为什么像“动态”上下文这样重要的东西被处理成一个函数的隐含输入,而不是被传递出来的明确论据。

这是一个非常重要的问题, 但我们不能完全回答。 继续回答这个问题

### 我们可以继续吗?

为什么我对这个话题 有所不满? 你明白吗? 你准备好继续前进了

我的观点是,你这个代码的作者, 和所有其他的 代码读者,甚至几年或几十年 在未来,`this`- 发觉了 这就是选择,负担, 你放在阅读这种代码。 是的,这是选择使用`class`(见第3章)`this`- 没有必要

记住,这个`this`选择您写的代码。 故意这样做,这样做是为了产生比负担更多的结果利益。 确保`this`用在您的代码中 *携带它自己的重量 *。

让我这么说吧,别用`this`- 了解代码,除非你真的可以证明, 而且你仔细权衡成本。 就因为你见过很多代码的例子`this`在别人的代码,并不意味着`this`属于你正在写的代码。

那个`this`联合来文中的机制`[[Prototype]]`代表团是语言的一个极其强大的支柱。 但正如陈词滥调所言:"拥有强大的力量就带来巨大的责任". 尽管我真的很喜欢和欣赏JS的支柱, 而当有我,则以节制. 这不是我的默认, 去JS能力。

## 这是它!

好了,别再讲白话了 你准备潜入`this`代码,对不对?

让我们重新审视(和延伸)`Point2d`从第3章起,但仅作为一个具有数据属性和功能的对象,而不是使用`class`:

```js
var point = {
    x: null,
    y: null,

    init(x,y) {
        this.x = x;
        this.y = y;
    },
    rotate(angleRadians) {
        var rotatedX = this.x * Math.cos(angleRadians) -
            this.y * Math.sin(angleRadians);
        var rotatedY = this.x * Math.sin(angleRadians) +
            this.y * Math.cos(angleRadians);
        this.x = rotatedX;
        this.y = rotatedY;
    },
    toString() {
        return `(${this.x},${this.y})`;
    },
};
```

如你所见`init(..)`, `rotate(..)`,以及`toString()`函数为`this`- 发觉了 你可能习惯于假设`this`参考文献显然将永远保留`point`对象。 但绝对不能保证

继续提醒自己,你通过本章的其余部分:`this`函数的值由 * 如何引用该函数来确定。 也就是说,你不能查看函数的定义,也不能查看函数的定义(即使是附件)`class`{\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}你觉得呢? 实际上,它甚至并不重要 函数从哪里调用。

我们只需要研究一下这些功能是如何被称作的;这是唯一重要的因素。

### 隐性背景

考虑这一呼吁:

```js
point.init(3,4);
```

我们正在援引`init(..)`函数,但注意`point.`在它前面吗? 这是“隐性上下文”的约束。 它对联署材料说:`init(..)`函数为`this`参考资料`point`.

这就是"正常"的方式 我们期待`this`工作,这也是我们最常用的方法之一 我们引用函数。 所以典型的引用给我们带来了直观的结果. 这是好事!

### 默认语境引用

但是如果我们这样做会怎么样?

```js
const init = point.init;
init(3,4);
```

你可能认为我们得到的结果 与之前的片段相同。 但JS不是这样的`this`任务有效。

该职能的召见地点是`init(3,4)`,这与`point.init(3,4)`。当没有 " 隐性上下文 " (`point.`)或任何其他类型的`this`转让机制,发生“默认上下文”转让。

什么会`this`引用时`init(3,4)`被这样引用?

*这取决于。*

瓳 看情况? 这听起来很混乱。

别担心,它没有听起来那么糟糕。 *默认上下文* 的转让取决于代码是否是严格模式。 但令人欣慰的是,目前几乎所有的JS代码都在严格模式下运行;例如,无害环境管理(ES模块)总是严格模式运行,而内部的编码也是如此。`class`块。 而几乎所有转录的JS代码(通过Babel, TypeScript等)都写成声明严格模式.

因此,几乎所有的时间,现代JS代码都会在严格模式下运行,因此"默认指派*上下文不会"依赖"任何东西;这非常直截了当:`undefined`就是这样!

| 注: |
| :--- |
| 铭记:`undefined`不表示“ 未定义”; 意思是“ 用特殊的空来定义 ”`undefined`数值". 我知道,我知道... 名字和意义不匹配。 这是语言遗留的行李,给你。 (耸肩) |

就是说`init(3,4)`如果在严格模式下运行, 就会有一个例外。 为什么? 因为`this.x`参考文献`init(..)`是一个`.x`属性访问`undefined`(即:`undefined.x`)),不允许:

```js
"use strict";

var point = { /* .. */ };

const init = point.init;
init(3,4);
// TypeError: Cannot set properties of
// undefined (setting 'x')
```

暂停一下,考虑一下:为什么联署材料会选择默认上下文。`undefined`,以便任何 * 默认上下文 * 援引`this`-知道除此之外功能会失败吗?

因为一个`this`-意识到函数 **总是需要一个`this`**. 援引`init(3,4)`没有提供`this`,因此,* 是一个错误,* 应该* 提出一个例外,以便纠正错误。 教训:永远不要援引`this`- 不提供功能就意识到`this`!

为了完整起见:在不太常见的非限制性模式中, " 默认上下文 " 是全球目标 -- -- JS将其定义为`globalThis`,在浏览器 JS 中本质上是一个别名`window`,在节点是`global`所以,当`init(3,4)`在非限制性模式下运行,`this.x`表达式是`globalThis.x`- 也称为`window.x`在浏览器中,或`global.x`在节点。 因此,`globalThis.x`设定为`3`和`globalThis.y`设定为`4`.

```js
// no strict-mode here, beware!

var point = { /* .. */ };

const init = point.init;
init(3,4);

globalThis.x;   // 3
globalThis.y;   // 4
point.x;        // null
point.y;        // null
```

这很不幸,因为它几乎肯定不是 想要的结果。 它不仅是坏的,如果它是一个全球变量, 而且它不是* 改变我们的财产`point`对象,因此程序错误得到保证。

| 警报: |
| :--- |
| 哎哟! 没有人想要意外的全球变量 暗含在代码上 教训:始终确保您的代码在严格模式下运行! |

### A. 明确援引情况

函数可以和 * 解释性上下文* 交替引用,使用内置`call(..)`或`apply(..)`公用事业:

```js
var point = { /* .. */ };

const init = point.init;

init.call( point, 3, 4 );
// or: init.apply( point, [ 3, 4 ] )

point.x;        // 3
point.y;        // 4
```

`init.call(point,3,4)`实际上是与`point.init(3,4)`,在他们俩指定`point`作为`this`上下文`init(..)`引用

| 注: |
| :--- |
| 两者`call(..)`和`apply(..)`公用设施将第一论点a`this`上下文值;这几乎总是一个对象,但技术上可以是任何值(数字,字符串等). 那个`call(..)`函数将后续参数传递到引用函数,而`apply(..)`期望它的第二个参数是一系列作为参数的数值。 |

考虑援引与`明确上下文* 转让有关的职能,可能显得很尴尬(第1段)。`call(..)` / `apply(..)`) 节目中的风格. 但它比乍一看可能更有用

让我们回顾一下原片段:

```js
var point = {
    x: null,
    y: null,

    init(x,y) {
        this.x = x;
        this.y = y;
    },
    rotate(angleRadians) { /* .. */ },
    toString() {
        return `(${this.x},${this.y})`;
    },
};

point.init(3,4);

var anotherPoint = {};
point.init.call( anotherPoint, 5, 6 );

point.x;                // 3
point.y;                // 4
anotherPoint.x;         // 5
anotherPoint.y;         // 6
```

你看到我做了什么了吗?

我想定义`anotherPoint`但我不想重复那些定义`init(..)` / `rotate(..)` / `toString()`从`point`因此,我"借"了一个函数参考,`point.init`,并明确设置空对象`anotherPoint`作为`this`上下文,通过`call(..)`.

何时`init(..)`此时此刻,`this`里面会提到`anotherPoint`,这就是为什么`x` / `y`属性( 值)`5` / `6`(分别)在那里设置。

任意`this`-意识到函数可以这样借用:`point.rotate.call(anotherPoint, ..)`, `point.toString.call(anotherPoint)`.

#### 重新审视隐性背景

另一种分享行为的方法`point`和`anotherPoint`本来是:

```js
var point = { /* .. */ };

var anotherPoint = {
    init: point.init,
    rotate: point.rotate,
    toString: point.toString,
};

anotherPoint.init(5,6);

anotherPoint.x;         // 5
anotherPoint.y;         // 6
```

这是通过在任何目标对象上添加共享的函数引用(例如,`anotherPoint`) (中文(简体) ). 呼叫地点的援引`anotherPoint.init(5,6)`是一种更自然/人类学的风格,它依赖于“隐性上下文”的指派。

这种方法似乎有点干净,比较一下`anotherPoint.init(5,6)`改为:`point.init.call(anotherPoint,5,6)`.

但主负面是必须修改任何带有这种共享函数引用的目标对象,这可以是动词,手动,容易出错. 有时,这种做法是可以接受的,但很多时候,“解释性上下文”转让`call(..)` / `apply(..)`比较好

### 新背景

迄今为止,我们已经在函数调用站点看到三种不同的上下文分配方式:*默认*,*隐性*和*解释*.

第四种调用函数的方法,并指定`this`对于这个引用, 是与`new`关键字:

```js
var point = {
    // ..

    init: function() { /* .. */ }

    // ..
};

var anotherPoint = new point.init(3,4);

anotherPoint.x;     // 3
anotherPoint.y;     // 4
```

| 提普: |
| :--- |
| 这个例子有一点细微的解释. 那个`init: function() { .. }`在此显示的窗体 -- -- 特别是指定给属性的函数表达式 -- -- 需要该函数与`new`关键词 简洁的方法形式`init() { .. }`定义调用 *不能调用的函数`new`. |

你一般都看见了`new`用于`class`用于创建实例。 但作为联署材料语言的基本机制,`new`不是内在的`class`操作。

从某种意义上说,`new`关键字劫持函数,并迫使其行为与正常引用不同模式. 以下是联合来文在援引某项职能时采取的4个特别步骤。`new`:

1. 创建全新的空物,脱离薄空气.

2. 链接`[[Prototype]]`该函数的`.prototype`对象(见第2章)。

3. 与`this`上下文设置为新空对象。

4. 如果函数未明确返回自己的对象值(带有`return ..`,则假设函数调用应返回新对象(从步骤1至3)。

| 警报: |
| :--- |
| 第四步意味着如果你`new`引用一个函数 * does * 返回自己的对象 -- -- 如`return { .. }`等——然后,步骤1-3的新对象是*不*返回。 这是一个很棘手的 需要知道, 因为它实际上抛弃了 新对象之前,程序 有机会接收和存储一个引用。 其实,`new`绝不应用来援引具有明确`return ..`语句。 |

为了了解这4个`new`更具体地说,我要用代码来说明它们,作为替代使用`new`关键字:

```js
// alternative to:
//   var anotherPoint = new point.init(3,4)

var anotherPoint;
// this is a bare block to hide local
// `let` declarations
{
    // (Step 1)
    let tmpObj = {};

    // (Step 2)
    Object.setPrototypeOf(
        tmpObj, point.init.prototype
    );
    // or: tmpObj.__proto__ = point.init.prototype

    // (Step 3)
    let res = point.init.call(tmpObj,3,4);

    // (Step 4)
    anotherPoint = (
        typeof res !== "object" ? tmpObj : res
    );
}
```

显然,`new`引用简化了那一套手动步骤!

| 提普: |
| :--- |
| 那个`Object.setPrototypeOf(..)`在步骤2中,也可以通过`__proto__`财产,例如`tmpObj.__proto__ = point.init.prototype`,甚至作为物体文字的一部分(步骤1),`tmpObj = { __proto__: point.init.prototype }`. |

跳过这些步骤的一些形式,让我们回顾一下早先的片段,看看如何`new`大致类似的结果:

```js
var point = { /* .. */ };

// this approach:
var anotherPoint = {};
point.init.call(anotherPoint,5,6);

// can instead be approximated as:
var yetAnotherPoint = new point.init(5,6);
```

好一点了! 但这里有个警告

使用其他函数`point`反对`anotherPoint` / `yetAnotherPoint`我们不想和`new`为什么? 因为`new`正在创建一个 * 新的* 对象, 但这不是我们希望的 如果我们打算 引用一个函数 针对一个已有的对象。

相反,我们可能会使用“解释性上下文”的任务:

```js
point.rotate.call( anotherPoint, /*angleRadians=*/Math.PI );

point.toString.call( yetAnotherPoint );
// (5,6)
```

### 审查此

我们见过四条规矩`this`函数调用时的上下文任务。 让我们按顺序排列:

1. 是否引用函数为`new`,创建和设置`this`?

2. 是否引用函数为`call(..)`或`apply(..)`, * 明确 * 设置`this`?

3. 在呼叫站点引用的函数是否带有对象参考(例如,`point.init(..)`), * 隐含* 设定`this`?

4. 如果上面没有... 我们处于非限制性状态吗? 如果有,则“默认”`this`改为:`globalThis`但是,如果在严格模式中,“默认”`this`改为:`undefined`.

这些规则,* 依此顺序,是JS如何确定`this`用于函数引用。 如果多个规则匹配一个呼叫网站(例如,`new point.init.call(..)`),从榜单上第一个比分规则获胜.

就是这样,你现在是主人了`this`关键词 不尽然 还有一堆更细微的遮盖. 但你已经很顺利了!

## 箭头点某处

我至今为止所说的一切`this`在函数中,以及它是如何根据呼叫站点来确定的, 得出了一个巨大的假设: 你处理的是一个 * 常规 * 函数(或方法).

那么,什么是“非正常”的功能? 看起来是这样的:

```js
const x = x => x <= x;
```

| 注: |
| :--- |
| 是的,我是一个刻薄的讽刺和不公平的人,把箭函数称为"非正常的",并用这样的巧妙的例子. 这是一个笑话,好吗? |

这里有个真正的例子`=>`箭头函数:

```js
const clickHandler = evt =>
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();
```

为了比较起见,让我也表明非狭义的等效:

```js
const clickHandler = function(evt) {
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();
};
```

或者,如果我们去 有点旧的学校 关于它 - 这是我的果酱! 我们可以尝试独立的函数声明表:

```js
function clickHandler(evt) {
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();
}
```

或者,如果函数作为方法出现在`class`定义,或作为对象文字中的简洁方法,它看起来是这样:

```js
// ..
clickHandler(evt) {
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();
}
```

我真正想强调的是,这些功能的每一种形式 如何表现在它们身上`this`参考,以及第一种`=>`形式不同于其他形式(其实是的!) 但先来个小测验 看看你们有没有注意过

对于刚才显示的每个函数,我们如何知道每个函数`this`会参考吗?

### 呼叫站点在哪?

但愿你的反应是 “首先,我们需要看看这些功能是如何命名的。”

不错

假设我们的节目是这样的:

```js
var infoForm = {
    theFormElem: null,
    theSubmitBtn: null,

    init() {
        this.theFormElem =
            document.getElementById("the-info-form");
        this.theSubmitBtn =
            theFormElem.querySelector("button[type=submit]");

        // is *this* the call-site?
        this.theSubmitBtn.addEventListener(
            "click",
            this.clickHandler,
            false
        );
    },

    // ..
}
```

啊,有趣的是,你觉得呢? 你们一半的读者从未见过像DOM API这样的`getElementById(..)`, `querySelector(..)`,以及`addEventListener(..)`之前,我刚说 刚才我听到混乱的铃声!

| 注: |
| :--- |
| 对不起,我约会自己,在这里。 我做这些事的时间足够长了,我记得 当我们做那种代码 很久以前,我们有 公用事业,如 jQuery 乱编代码`$`到处都是 经过多年的前端进化,我们似乎已经降落了 更“现代”的地方—— 至少,这是普遍的假设。 |

我想这些天你们很多人 都习惯于看到类似这样的组件框架代码:

```jsx
// ..

infoForm(props) {
    return (
        <form ref={this.theFormElem}>
            <button type=submit onClick=this.clickHandler>
                Click Me
            </button>
        </form>
    );
}

// ..
```

当然,还有许多其他方法可以塑造代码,这取决于你是否使用一个框架或另一个框架等等.

或者你甚至没有使用`class` / `this`风格组件,因为你已经移动了所有的东西 钩和关闭。 无论如何,为了我们的讨论目的, 这篇章就是`this`,所以我们需要坚持像上面那样的编码风格,有与讨论相关的代码.

而前两个代码片段都没有显示`clickHandler`函数正在定义。 但我已经反复说过了,这不重要, 重要的是... 什么? 跟我说说... 最重要的是 功能是如何被引用的

怎么样`clickHandler`被援引? 呼叫站点是什么,哪个上下文分配规则匹配?.

### 从视觉中隐藏

如果你被困住了 别担心 我故意让这很难, 指出一些非常重要的事情。

当`"click"`或`onClick=`处理器绑定的情况, 在两种情况下, 我们指定`this.clickHandler`,这意味着存在`this`带有属性的上下文对象`clickHandler`,它持有我们的函数定义。

这么说`this.clickHandler`电话站? 如果是的话,适用什么转让规则? * 隐性上下文* 规则(# 3)?

不幸的是 没有

问题是,**我们实际上无法在这个节目中看到呼叫站**. 瓳

如果我们看不到呼叫网站,我们怎么知道 函数会怎么被呼叫?

*这就是我要说的*

我们过去没关系`this.clickHandler`。这只是对函数对象值的参考。 这不是一个呼叫网站。

在封面下,某个框架的某个地方,图书馆,甚至JS环境本身,当一个用户点击按钮时,一个引用`clickHandler(..)`函数将被引用。 正如我们所暗示的, 呼叫网站甚至会通过 在DOM事件的对象 作为`evt`参数。

因为我们看不到呼叫站, 我们必须想象它。 看起来像...?

```js
// ..
eventCallback( domEventObj );
// ..
```

如果有的话,哪个`this`规则是否适用? * 默认上下文* 规则 (# 4)?

或者,如果呼叫站 看起来像这个?

```js
// ..
eventCallback.call( domElement, domEventObj );
```

现在`this`规则是否适用? *解释性上下文*规则(#2)?

除非您打开并查看框架/图书馆的源代码,或者阅读文档/规格,否则您不会知道*该期待哪个呼叫站点. 这意味着,最终预测,什么`this`标注在`clickHandler`你写的功能,是... 轻轻地说...

### *这个* 错误

为了让你多受点痛苦 我就直说吧

几乎所有执行的 点击手机制 会做类似的东西`.call(..)`,它们会设置事件听众必须参加的 DOM 元素(如按钮),作为引用的 * 说明上下文 *。

嗯... 是没关系,还是会是一个问题?

Recall that our `clickHandler(..)` function is `this`-aware, and that its `this.theFormElem` reference implies referencing an object with a `theFormElem` property, which in turn is pointing at the parent `<form>` element. DOM buttons do not, by default, have a `theFormElem` property on them.

换句话说,`this`我们的活动处理者会为之设定的引用几乎肯定是错误的。 哎哟

除非我们想重写`clickHandler`功能,我们需要修复它。

### 修复`this`

让我们考虑一些选择 来解决错误的任务。 为了保持重点,我会坚持这种活动风格 约束讨论:

```js
this.submitBtnaddEventListener(
    "click",
    this.clickHandler,
    false
);
```

这里有一个解决的方法:

```js
// store a fixed reference to the current
// `this` context
var context = this;

this.submitBtn.addEventListener(
    "click",
    function handler(evt){
        return context.clickHandler(evt);
    },
    false
);
```

| 提普: |
| :--- |
| 大多数使用这种方法的JS旧代码都会说`var self = this`代替`context`我给它的名字。 "自我"是一个较短的词,听起来更酷. 但这也是完全错误的语义意义. 那个`this`关键词不是对函数的"自身"的引用,而是当前函数引用的上下文. 这些看起来可能和一眼看一样,但它们是完全不同的概念,和苹果和披头士歌曲一样不同. 所以... 来解释一下,“嘿,开发商,不要弄坏它。 来个悲哀吧`self`并让它更好`context`." |

这是怎么回事? 我认出了所附代码,其中`addEventListener`呼叫要运行, 有电流`this`环境是正确的,我们需要确保`this`上下文在`clickHandler(..)`被援引。

我定义了一个周围的功能( 即:`handler(..)`),然后迫使呼叫站看起来像:

```js
context.clickHandler(evt);
```

| 提普: |
| :--- |
| 哪个`this`此处是否适用上下文指派规则? 没错 隐性上下文*规则(#3) |

现在,图书馆/框架/环境的内部呼叫站点看起来怎么样并不重要. 为什么?

因为我们现在已经控制了呼叫站台 这不重要`handler(..)`被援引,或是什么`this`指定。 只有时间`clickHandler(..)`被援引,`this`上下文被设定为我们想要的。

我不仅通过定义周围的功能 完成了这个诡计`handler(..)`所以我可以控制电话站,但是... 这很重要,所以不要错过... 我定义`handler(..)`作为非政府组织`this`-意识到了功能! 没有`this`关键词`handler(..)`所以随你怎么说`this`完全无关。

那个`var context = this`线条对诡计至关重要。 它定义了一个词典变量`context`,这不是一些特殊的关键词,在外侧持有一个值的快照`this`然后进去`clickHandler`,我们仅提及一个词典变量(`context`无相对/魔法`this`关键词

### 语法 这个

顺便说一句,这个图案的名称是"lexic this",意思是`this`行为就像一个词汇范围变量 而不是一个动态上下文绑定。

但事实证明,JS有比较容易的表演方式,即"用这种语法"魔术. 你准备好要揭秘了吗?

...

那个`=>`箭头功能! 塔达!

没错`=>`函数与所有其他函数形式不同,是特殊的,因为它根本不是特殊的。 或者说,它没有定义任何特殊的东西`this`做点什么

在一个`=>`函数,`this`关键词... ** 不是关键词 **. 它绝对没有区别 与其他变量一样,`context`或`happyFace`或`foobarbaz`.

让我更直接地说明这一点:

```js
function outer() {
    console.log(this.value);

    // define a return an "inner"
    // function
    var inner = () => {
        console.log(this.value);
    };

    return inner;
}

var one = {
    value: 42,
};
var two = {
    value: "sad face",
};

var innerFn = outer.call(one);
// 42

innerFn.call(two);
// 42   <-- not "sad face"
```

那个`innerFn.call(two)`就任何 * 常规 * 函数定义而言,`"sad face"`在这里。 但自从`inner`函数,我们定义并返回(并分配给)`innerFn`)是一个"非正常的"`=>`箭头函数, 它没有特殊功能`this`行为,但反之有"语义上这种"行为.

当`innerFn(..)`(卡卡语)`inner(..)`)功能被援引,即使通过下列方式转让`.call(..)`,该任务被忽略。

| 注: |
| :--- |
| 我不知道为什么`=>`箭头函数甚至有一个`call(..)` / `apply(..)`因为他们是无声的, 我想是为了符合正常功能 但是,正如我们以后看到的, 有一些其他不一致 在*正常的*功能和*不正常的*`=>`箭头函数。 |

当 a`this`遇到( A)`this.value`在一个`=>`箭头函数,`this`被作为普通词典变量,而不是特殊的关键词。 既然没有`this`JS在功能本身的变数中,总是用词汇变量来做它总是做的事:它向上延伸了一种层次的词汇范围 -- -- 在这种情况下,到周围。`outer(..)`函数,它检查是否有注册`this`在范围。

幸运的是,`outer(..)`是一个 * 常规 * 函数, 这意味着它有正常的`this`关键词 还有`outer.call(one)`转让的援引`one`联 合 国`this`.

这么说`innerFn.call(two)`正在引用`inner()`但何时`inner()`查找一个值`this`它会...`one`没有`two`.

#### 回到按钮

你以为我会开玩笑说"未来",是吗?

以更直接和适当的方式解决我们先前的问题,我们已经这样做了`var context = this`要得到一种假的“ 弹性” 行为,就是使用`=>`箭头功能,因为它的主要设计特征是. "lexic this".

```js
this.submitBtn.addEventListener(
    "click",
    evt => this.clickHandler(evt),
    false
);
```

轰! 问题解决了! 弹弓!

听我讲这个`=>`箭头函数是 * 不是 * -- -- 我重复, * 不是 * -- -- 关于打字更少的字符。 关键`=>`在联署材料中添加的职能是给我们“从语法上”的行为,而不必诉诸`var context = this`(或者更糟的是,`var self = this`风格黑客。

| 提普: |
| :--- |
| 如果你需要"语法" 总是喜欢`=>`箭头函数。 如果你不需要"说这个" 那么...`=>`箭头函数可能不是工作的最佳工具。 |

#### 认罪时间

我在本章中一直说,你如何写一个函数, 在你写这个函数的地方, 与它是如何`this`将分配。

对于正常的功能,这是真的。 但是当我们觉得不正常的时候`=>`箭头函数,它不再完全准确了.

记得原作`=>`形式`clickHandler`从前面的章节?

```js
const clickHandler = evt =>
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();
```

如果我们使用这个形式,在与我们的活动绑定相同的上下文中,它可能是这样的:

```js
const clickHandler = evt =>
    evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation();

this.submitBtn.addEventListener("click",clickHandler,false);
```

很多开发者更喜欢进一步缩小到内含`=>`箭头函数:

```js
this.submitBtn.addEventListener(
    "click",
    evt => evt.target.matches("button") ?
        this.theFormElem.submit() :
        evt.stopPropagation(),
    false
);
```

当我们写一个`=>`箭头功能,我们肯定知道`this`绑定会是当前`this`绑定任何周围函数,无论该函数的呼叫站点是什么`=>`箭头函数看起来像。 换句话说 我们怎么写的`=>`箭头功能,和 * 在哪里,我们写它,有关系。

这不完全回答`this`问题,虽然。 它只是把问题转移到* 如何引用附加函数 *。 实际上,关注呼叫网站仍然是唯一重要的.

但我承认的细微之处是, 直到这个时刻, 这才是最重要的。 我们所考虑的呼叫站点, 不仅仅是当前呼叫堆中的任何呼叫站点。 重要的呼叫站点是当前调用堆栈中最近的函数调用 *** 该调用堆栈实际上指定了一个`this`上下文***。

由于`=>`箭头函数从未有过`this`- 指定呼叫站点(无论什么), 那个呼叫站点与问题无关。 我们必须不断加大呼叫量 直到找到一个函数引用`this`- 转让 -- -- 即使援引的功能本身不是`this`-意识到了

** THAT**是唯一重要的呼叫站点。

#### 找到正确的呼叫位置

让我举个例子,一团乱糟糟的一堆嵌套功能/呼叫:

```js
globalThis.value = { result: "Sad face" };

function one() {
    function two() {
        var three = {
            value: { result: "Hmmm" },

            fn: () => {
                const four = () => this.value;
                return four.call({
                    value: { result: "OK", },
                });
            },
        };
        return three.fn();
    };
    return two();
}

new one();          // ???
```

你能穿过你脑海里的噩梦吗? 确定会从那里得到什么?`new one()`引用吗?

这可能是其中任何一种:

```js
// from `four.call(..)`:
{ result: "OK" }

// or, from `three` object:
{ result: "Hmmm" }

// or, from the `globalThis.value`:
{ result: "Sad face" }

// or, empty object from the `new` call:
{}
```

调用卡`new one()`援引是:

```
four         |
three.fn     |
two          | (this = globalThis)
one          | (this = {})
[ global ]   | (this = globalThis)
```

从`four()`和`fn()`两者都是`=>`箭头函数,该`three.fn()`和`four.call(..)`电话站不是`this`- 分配; 因此,它们与我们的查询无关。 接下来要考虑什么?`two()`。这是一个常规函数(它可以接受)`this`- 转让),而且呼叫站点与“默认上下文”转让规则(第4条)相符。 因为我们没有严格模式,`this`已指定`globalThis`.

何时`four()`正在运行,`this`只是一个普通变量。 然后看它的内含功能`three.fn()`),但它再次找到一个没有`this`所以它又升到另一个高度,找到一个`two()`具有`this`定义。 还有`this`这是`globalThis`因此,`this.value`表达式决定`globalThis.value`,这让我们...`{ result: "Sad face" }`.

...

深呼吸点 我知道这对精神过程很重要 公平地说,这是一个超级巧妙的例子。 你几乎永远不会看到所有这些复杂 混合在一个调用。

但你绝对会在真实的节目中找到混合调用器. 你需要对我刚才所描述的分析感到自在 才能解开调压 直到找到最新的`this`- 指定呼叫站点

记住我之前引用的加词:"拥有巨大的力量就带来巨大的责任". 选择`this`- 定向代码(偶数)`class`e)意味着既要选择它给我们提供的灵活性,也要选择舒适的导航,以了解它将如何行为。

这是有效写作的唯一方法(后来读完!).`this`-知道密码

### 这是必经之路

后退一点,还有另一种选择 如果你不想使用`=>`箭头函数的"lexic this"行为可以解决按钮事件处理器的功能.

况乎.`call(..)` / `apply(..)`- 这些功能,记住! - 联合来文的职能还有第三种功能,称为:`bind(..)`- 它不* 引用函数,只是要明确。

那个`bind(..)`工具定义一个函数的 * 新的* 包裹/绑定版本,其中`this`是预定的,是固定的,不能用一个`call(..)`或`apply(..)`,甚至“隐性上下文”在呼叫站点的物体:

```js
this.submitBtn.addEventListener(
    "click",
    this.clickHandler.bind(this),
    false
);
```

自从我经过一个`this`- 作为事件处理器的捆绑功能,同样也不管该工具如何试图设置`this`因为我已经强迫`this`成为我想要的:`this`从周围函数引用上下文。

#### 几乎没有新

这种模式常被称为"硬约束",因为我们正在创建一种功能参考,它与特定的`this`许多联署材料声称,`=>`箭头函数基本上只是`bind(this)`硬带 这不是。 让我们挖进去。

如果你打算创建一个`bind(..)`工具,它可能看起来有点像 这样的:

```js
function bind(fn,context) {
    return function bound(...args){
        return fn.apply(context,args);
    };
}
```

| 注: |
| :--- |
| 事实并非如此`bind(..)`已执行。 行为更为精密细腻. 我只是在说明它的一部分行为 在这个片段。 |

看起来很眼熟吗? 它使用好的 ol'假的"弹性这个"黑客。 而在封面之下,这是一种"解释性上下文"的指派,在这种情况下通过`apply(..)`.

所以,等等... 这不意味着 我们可以只是用一个`=>`箭头函数?

```js
function bind(fn,context) {
    return (...args) => fn.apply(context,args);
}
```

呃... 不尽然 与JS中的大部分内容一样,也有一些细微的细微差别. 让我举例说明:

```js
// candidate implementation, for comparison
function fakeBind(fn,context) {
    return (...args) => fn.apply(context,args);
}

// test subject
function thisAwareFn() {
    console.log(`Value: ${this.value}`);
}

// control data
var obj = {
    value: 42,
};

// experiment
var f = thisAwareFn.bind(obj);
var g = fakeBind(thisAwareFn,obj);

f();            // Value: 42
g();            // Value: 42

new f();        // Value: undefined
new g();        // <--- ???
```

首先,看看那个`new f()`打电话 这的确是一种奇怪的习惯,打电话`new`在硬性函数上。 可能很少你会这么做 但它显示了一些有趣的东西。 虽然`f()`很难接受`this`一、导 言`obj`,则`new`操作员能够劫持硬约束功能的`this`,然后将其重置为新建和空对象。 那个物体没有`value`财产,这就是为什么我们看到`"Value: undefined"`打印出来。

如果觉得奇怪,我同意。 怪角微细. 这不是你可能利用的东西。 但我指出它不只是为三角。 参考本章前面提出的四项规则。 记得我如何断言他们的序号,`new`在顶级(#1),领先于*说明*`call(..)` / `apply(..)`指派规则(# 2)?

既然我们能想到`bind(..)`作为该规则的变体,我们现在看到这种优先秩序得到证明。`new`比起甚至一个硬性功能,它更具有先例性。 有点让你觉得 硬约束的功能 也许不是那么"硬"约束,是吧?

但是... 接下来会发生什么`new g()`电话,正在引用`new`返回时`=>`箭头函数? 你预测结果是否与`new f()`?

抱歉让你失望了

这条线实际上会有一个例外,因为`=>`函数不能与`new`关键词

为什么? 我对TC39自己没有权威的最好答案是,`=>`箭头函数不是硬绑定的函数`this`,这是一个没有`this`一点也不 因此,`new`因此JS只是反对。

| 注: |
| :--- |
| 刚才我曾指出`=>`箭头函数有`call(..)`, `apply(..)`,甚至连`bind(..)`但我们已经看到,这些功能基本上忽略了这些公用设施。 我觉得有点奇怪`=>`箭头函数具有所有这些公用设备作为不通过操作,但用于`new`关键词,这不仅仅是一个,再次, 禁止通过, 而是不允许例外。 |

但要点是:`=>`箭头函数是 * not * 的合成形式`bind(this)`.

### 失败 这场战役

再次返回到按钮事件处理器示例:

```js
this.submitBtnaddEventListener(
    "click",
    this.clickHandler,
    false
);
```

我们还没有解决更深层的问题

我们见过几种不同的方法 来构建不同的召回功能参考物`this.clickHandler`.

但是无论我们选择哪种方式,它们正在产生一种完全不同的功能,而不只是在原地修改我们现有的`clickHandler`函数。

这有什么关系?

首先,我们创造(和再创造)的功能越多,处理时间越长(非常微小),记忆也越多(小,通常),我们正在咀嚼. 当我们重新创建一个函数参考物, 丢掉一个旧的, 也留下了没有恢复的记忆, 它给垃圾收集器(GC)带来压力, 更经常地, 暂停我们程序的宇宙, 同时它清理并恢复记忆。

如果连接这个事件 听是一个一次性的操作,没什么大不了的. 但是,如果一次又一次发生,系统层面的性能效果*可以*开始加起来. 曾经有过平滑的动画颤抖吗? 可能是GC踢了进去 清理了一堆可收回的记忆

但另一个问题是,对于事件处理者来说, 如果我们要在以后某个时间去掉一个事件听众, 我们需要继续提及我们原先附带的完全相同的功能。 如果我们使用一个图书馆/框架, 他们经常(但并不总是)照顾你那肮脏的小细节。 但是,否则,我们有责任确保 无论我们计划附加什么功能, 我们保留一个参考,以防我们稍后需要它。

所以我想说的是:`this`任务,无论你怎么做, 所以它是可预测的,有代价的。 系统级别成本和程序维护/复杂成本。 它从来没有自由。

对这个事实的一种反应是决定, 好吧,我们只是去制造所有那些`this`- 一次、一次、一次、一次和一次 这样,我们就能把系统压力和密码压力降低到最低限度。

听起来很合理,对吧? 别这么快

#### 启动前函数上下文

如果您有一个一次性函数引用需要`this`- 带,你用`=>`箭头或箭头`bind(this)`我看不出有什么问题。

但是,如果大多数或所有`this`- 在您代码的某一段中意识到函数, 以引用方式`this`并不是你所期望的可预测的环境, 所以你决定你需要硬化它们的全部... 我认为这是一个很大的警告信号 你走错了路

请记住第3章"Avoid This"部分的讨论,从这个代码片段开始:

```js
class Point2d {
    x = null
    getDoubleX = () => this.x * 2

    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
    toString() { /* .. */ }
}

var point = new Point2d(3,4);
```

现在想象一下我们用那个代码做了这个:

```js
const getX = point.getDoubleX;

// later, elsewhere

getX();         // 6
```

如你所见,我们试图解决的问题 和我们在本章中处理的问题一样。 我们想引用一个功能参考`getX()`,并有这个 * 意思 * 和 "行为像`point.getDoubleX()`不过`this`关于*正常*函数的规则不能这样运作.

所以我们用了一个`=>`箭头函数。 没什么大不了的 对吧?

错.

真正的根本问题是 我们想要两个相互矛盾的东西 出我们的代码, 我们试图使用相同的 * hammer * 为两个 * 奈尔斯*。

我们想要一个`this`- 认识方法存储在`class`原型,这样这个功能只有一个定义, 而我们所有的子类和例 都很好地分享同样的功能。 他们共享的方式是通过动力的力量`this`装订。

但与此同时,我们也希望 那些功能参考 神奇地留下`this`-当我们通过这些功能参考文献时 -其他代码负责呼叫站

换句话说,有时候我们想要一些像`point.getDoubleX`意思是"给我一个参考`this`- 分配到`point`",有时我们想要同样的表达`point.getDoubleX`意思是,给我一个动态`this`-可分配的函数参考,这样它就可以正确得到我此刻需要的背景.

也许联署材料可以提供不同的操作员。`.`喜欢`::`或`->`或类似的东西, 让你分辨 什么样的功能 你追求。 事实上,有一个长期的建议是:`this`- 装订操作员(`::`这时不时引起人们的注意,然后似乎拖延了下来。 谁知道,也许有一天,这样的操作员 会最终着陆, 我们将有更好的选择。

但我强烈怀疑,即使它真的着陆 总有一天,它会 复仇一个全新的功能参考, 正是作为`=>`或`bind(this)`我们已经谈过了 它不会成为一个自由和完美的解决方案。 总是有矛盾的 想要相同的功能 有时是`this`- 灵活,有时是`this`-可以预测的

联合来文作者:`class`- 定向的代码常常会遇到,迟早, 这正是紧张。 你知道他们在做什么吗?

他们不考虑成本 简单的预先约束所有阶级`this`- 认识的方法作为代替`=>`成员属性中的箭头函数。 他们没有意识到,它完全 击败了整个目的`[[Prototype]]`链条。 他们并不意识到,如果固定文字是他们真正需要的,那么JS中有一个完全不同的机制更适合这个目的.

#### 更严谨的看

所以当你这样做的时候:

```js
class Point2d {
    x = null
    y = null
    getDoubleX = () => this.x * 2
    toString = () => `(${this.x},${this.y})`

    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
}

var point = new Point2d(3,4);
var anotherPoint = new Point2d(5,6);

var f = point.getDoubleX;
var g = anotherPoint.toString;

f();            // 6
g();            // (5,6)
```

我说,"啊!",对硬的`this`- 认识的方法`getDoubleX()`和`toString()`这里 对我来说,这是一个代码的味道。 但过去许多开发商都喜欢这样,

```js
class Point2d {
    x = null
    y = null

    constructor(x,y) {
        this.x = x;
        this.y = y;
        this.getDoubleX = this.getDoubleX.bind(this);
        this.toString = this.toString.bind(this);
    }
    getDoubleX() { return this.x * 2; }
    toString() { return `(${this.x},${this.y})`; }
}

var point = new Point2d(3,4);
var anotherPoint = new Point2d(5,6);

var f = point.getDoubleX;
var g = anotherPoint.toString;

f();            // 6
g();            // (5,6)
```

双关

在这两种情况下,你使用`this`却完全背叛/否定了它, 夺走了所有强大的动力`this`.

你真的应该至少考虑 这个替代方法, 它跳过整个`this`整个机制:

```js
function Point2d(px,py) {
    var x = px;
    var y = py;

    return {
        getDoubleX() { return x * 2; },
        toString() { return `(${x},${y})`; }
    };
}

var point = Point2d(3,4);
var anotherPoint = Point2d(5,6);

var f = point.getDoubleX;
var g = anotherPoint.toString;

f();            // 6
g();            // (5,6)
```

看到没? 没有丑陋或复杂`this`来拼凑代码 或担心角落的案子。 词汇范围非常直截了当和直观。

当我们想要的只是我们大多数/所有功能行为都有固定和可预测的上下文时,最合适的解决方案,最直接甚至最能表现的解决方案,就是词汇变量和范围关闭.

当你去所有的麻烦 刺探`this`全部引用在代码上, 然后你切断整个机制 在膝盖`=>`"这个"或`bind(this)`,你选择了使代码 更动词, 更复杂, 更过度。 除了跟随`this`(和`class`乐队乐队。

...

深处呼吸 收拾东西

我在自言自语,不是你 但是,如果我刚才说的话打扰你,我也在跟你说话!

好吧,听着。 这就是我的看法。 如果你不同意, 那没关系。 但当你们决定你们要得出什么结论时,请和我一样,在思考这些机制如何运作时,采用同样的严格度。

## 变化

在我们结束漫长的讨论之前`this`,有一些不规则的变化 函数调用,我们应该讨论。

### 间接函数调用

回顾本章前面的这个例子?

```js
var point = {
    x: null,
    y: null,

    init(x,y) {
        this.x = x;
        this.y = y;
    },
    rotate(angleRadians) { /* .. */ },
    toString() { /* .. */ },
};

var init = point.init;
init(3,4);                  // broken!
```

这是破碎的,因为`init(3,4)`电话站不能提供必需的`this`- 任务信号 但也有其他方法观察到类似的断裂. 例如:

```js
(1,point.init)(3,4);        // broken!
```

这个奇怪的语法是首先评价一个表达式`(1,point.init)`,这是一个逗号序列表达式。 这种表达式的结果是最终评价值,在这种情况下是函数参考值(由`point.init`).

结果将函数引用到表达式堆栈上,然后以`(3,4)`。这是对函数的间接引用。 结果如何? 它实际上符合我们在本章前面提到的“默认上下文*转让规则”(第4条)。

因此,在非限制性模式下,`this`联 合 国`point.init(..)`电话会`globalThis`. 如果我们在严格模式中,它应该是`undefined`,则`this.x = x`然后操作会丢弃一个无效访问`x`属性`undefined`数值。

获得间接函数引用有几种不同的方式. 例如:

```js
(()=>point.init)()(3,4);    // broken!
```

间接函数引用的另一个例子是即时启动函数表达式(IIFE)模式:

```js
(function(){
    // `this` assigned via "default" rule
})();
```

可见,函数表达值被放入表达式堆栈,然后与`()`在结尾。

但是这个密码呢?

```js
(point.init)(3,4);
```

守则的结果如何?

根据我们在前几个例子中看到的同样推理,有理由认为`point.init`表达式将函数值设置在表达式堆栈上,然后以`(3,4)`.

还没完呢! JS语法有特殊规则来处理引用表`(someIdentifier)(..)`好像已经`someIdentifier(..)`(无`(..)`环绕标识名称)。

想知道你为什么要强制使用“ 默认上下文 ”`this`是否通过间接函数引用进行转让?

### 访问`globalThis`

在回答这个问题之前,让我们引入另一种间接功能`this`任务。 迄今为止,所显示的间接函数引用模式对严格模式很敏感。 但是如果我们想要一个间接的功能`this`不尊重严格模式的任务。

那个`Function(..)`构造器取一串代码并动态定义等效函数. 然而,它总是这样做,仿佛在全球范围宣布了这一职能。 此外,它确保这种功能 * 不* 在严格模式下运行,无论程序严格模式地位如何。 和间接运行的结果一样

这种严格模式不可知间接功能的一种特殊用途`this`任务是指在联署材料规格实际界定全球目标之前获得可靠的参考。`globalThis`标识符( 例如, 在多填充中):

```js
"use strict";

var gt = new Function("return this")();
gt === globalThis;                      // true
```

事实上,类似的结果是使用逗号操作员的伎俩(见上一节)和`eval(..)`:

```js
"use strict";

function getGlobalThis() {
    return (1,eval)("this");
}

getGlobalThis() === globalThis;      // true
```

| 注: |
| :--- |
| `eval("this")`对严格模式很敏感 但是`(1,eval)("this")`没有,因此可靠地给了我们`globalThis`在任何计划中。 |

很遗憾,`new Function(..)`和`(1,eval)(..)`这两种方法都有一个重要的限制:如果应用软件得到某些内容-安全政策(CSP)限制,(出于安全原因)不允许动态代码评价,那么该代码将被屏蔽在基于浏览器的JS代码中。

我们能绕过去吗? 是的,大多数。[^globalThisPolyfill]

联合来文的规格说,在全球对象上或从中继承的任何对象上定义的更强函数(如:`Object.prototype`,以`this`指定的背景`globalThis`,不管节目的严格模式。

```js
// Adapted from: https://mathiasbynens.be/notes/globalthis#robust-polyfill
function getGlobalThis() {
    Object.defineProperty(Object.prototype,"__get_globalthis__",{
        get() { return this; },
        configurable: true
    });
    var gt = __get_globalthis__;
    delete Object.prototype.__get_globalthis__;
    return gt;
}

getGlobalThis() === globalThis;      // true
```

是啊,这是超级腺。 但这是JS`this`给你的!

### 模板标记函数

还有一种不同寻常的功能变化 我们应该涵盖: 标记的模板功能。

模板字符串 -- -- 我更喜欢称为插图字面 -- -- 可以用前缀函数“标记”它与模板字面的解析内容一起引用:

```js
function tagFn(/* .. */) {
    // ..
}

tagFn`actually a function invocation!`;
```

如你所见,没有`(..)`引用语法,只是标记函数(`tagFn`) 出现在`` `模板文字` ``;它们之间允许空白,但非常罕见.

尽管外表怪异 功能`tagFn(..)`将援引。 它传递了从模板文字中解析的一个或多个字符串字元的列表,以及遇到的任何插值表达式值.

我们不会覆盖所有有标记的模板功能的内外-- 它们真的是JS中最强大和有趣的功能之一-- 但是既然我们是在谈论`this`为了完整起见,我们需要谈谈如何`this`将分配。

您可能遇到的标记函数的另一种形式是:

```js
var someObj = {
    tagFn() { /* .. */ }
};

someObj.tagFn`also a function invocation!`;
```

简单解释如下:`` tagFn`..` ``和`` someObj.tagFn`..` ``将每个人都拥有`this`- 指定与呼叫站点相对应的行为`tagFn(..)`和`someObj.tagFn(..)`分别为: 也就是说,`` tagFn`..` ``按“默认上下文”转让规则(第4条)行事;`` someObj.tagFn`..` ``* 行为方式为 " 隐性上下文 " 转让规则(#3)。

幸运的是,我们不需要担心`new`或`call(..)` / `apply(..)`分配规则,因为这些表格不可能有标记功能。

应当指出,贴有标签的模板文字功能被定义为:`this`-意识到了,所以你不太可能需要 应用这些规则。 但以防万一 你现在在"知道"了

## 保持警觉

所以,这是`this`我愿意为你们中的很多人打赌, 这比你们想象的要多一点... 可以说,参与...

好消息或许是,实际上你并不经常 经历所有这些不同的复杂情况。 但用得越多 越多`this`越需要你和你的密码的读者 了解它的实际作用

这里的教训是,你应该有意并意识到`this`之前,你先去调查你的密码 确保你最有效地使用它,并充分利用JS的这一重要支柱.

[^globalThisPolyfill]:"在通用JavaScript中的恐怖全球This polyfill";Mathias Bynens;2019年4月18日;https://mathiasbynens.be/notes/globalthis#robust-polyfill; 2022年7月访问
