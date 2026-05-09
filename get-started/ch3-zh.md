# 你不认识JS然而:开始 - 第二版
# 第3章:挖掘JS的根部

如果你读了第1章和第2章 花了点时间消化和渗透 希望你们能开始多点JS 如果你跳过/跳过它们(特别是第2章),我建议回去再花点时间处理这些材料。

在第二章中,我们调查了高层次的语法、模式和行为。 在本章中,我们的注意力转向了联署材料的一些较低层次的根基特征,这些特征几乎是我们所写的每一行守则的基础。

注意:这一章的挖掘深度比你可能用来思考编程语言要深得多. 我的目标是帮助你理解 JS工作的核心, 是什么让它滴答。 这一章应该开始回答一些“为什么?” 当你探索联署机构时,可能会出现的问题。 然而,这份材料仍然不是对语言的详尽阐述;这就是其他书系列的用途! 我们的目标仍然只是 开始 * 并变得更自在, JS 的 * feel *, 如何推移和流动。

别跑得这么快 以至于你迷路了 我已经说了十几次了 慢慢来 即便如此,你也可能用剩下的问题来完成这一章. 没关系,因为你还有一系列书要继续探索!

## 重复

由于程序基本上是为处理数据而构建的(并就数据作出决定),因此用于跨过数据的模式对程序的可读性有重大影响.

借机模式已经持续了几十年, 并提出了一种“标准化”的方法 来消耗来自源 1*cunk*一次的数据。 这个想法是比较常见的,并且有助于数据源的迁移——通过处理第一部分,然后是下一部分,等等逐步处理数据收集,而不是同时处理整个数据集.

想象一个代表关系数据库的数据结构`SELECT`查询,通常将结果组织为行。 如果此查询只有一行或一行, 您可以同时处理全部结果, 并指定每个行为本地变量, 并进行该数据上任何适当的操作。

但如果查询有100行或1000行(或更多行!),则需要迭代处理处理此数据(典型的循环).

迭代图案定义了一个名为"迭代"的数据结构,该结构参考了一个基础数据源(如查询结果行),它暴露了一种方法,如:`next()`调用`next()`返回下一个数据块(即数据库查询中的“记录”或“row”)。

你并不总是知道需要多少数据才能通过,所以模式通常表示,一旦你通过整个集完成并* 越过末端*,那么完成值或例外。

借重者模式的重要性在于坚持*标准*数据迭代处理方式,这创造了更清洁和更容易理解代码的方式,而不是让每个数据结构/来源都定义自己的自定义处理数据的方式.

在联署机构围绕共同商定的迭代技术开展多年努力之后,ES6将直接使用语言的迭代模式的具体协议标准化。 协议定义了`next()`方法,其返回是一个称为 * 梯子结果* 的物体;该物体有`value`和`done`属性,在`done`是一个布尔是`false`直到基础数据源的迭代完成.

### 消耗式传动器

随着ES6迭代协议的到位,可以一次消耗一个数据源值,然后检查每个值`next()`呼吁`done`将会是`true`来阻止重复。 但这种方法相当手工,因此ES6还包含了几种机制(语法和API),用于这些延时器的标准化消耗.

其中一个机制是:`for..of`循环:

```js
// given an iterator of some data source:
var it = /* .. */;

// loop over its results one at a time
for (let val of it) {
    console.log(`Iterator value: ${ val }`);
}
// Iterator value: ..
// Iterator value: ..
// ..
```

| 注: |
| :--- |
| 我们这里省略了手动循环 但它绝对不如手动循环`for..of`循环! |

另一种常用于消耗传动器的机制是`...`操作员。 此操作符实际上有两种对称的形式: *spread * 和 *rest * (或 *gather *,我更喜欢). *扩散* 形式是一种流动消费。

为了传播 旅行者, 你必须有 东西,把它传播到。 JS中有两个可能性:一个数组或函数调用参数列表.

数组分布:

```js
// spread an iterator into an array,
// with each iterated value occupying
// an array element position.
var vals = [ ...it ];
```

函数调用扩展:

```js
// spread an iterator into a function,
// call with each iterated value
// occupying an argument position.
doSomethingUseful( ...it );
```

在这两种情况中,都存在着一种形式:`...`遵循延时消费协议(与`for..of`循环)从一个转发器获取所有可用的值并将其放置(aka, 分布)到接收上下文(array, 参数列表)。

### 可移动

从技术上讲,累进-消费协议是用于消耗*可渗入物*的;可累进值是一种可累进值。

协议会自动从可执行性中创建一个可执行性实例,并消耗 * 只是可执行性实例 * 直至完成。 这意味着一个单一的可执行性可以被消耗不止一次;每次将创建和使用一个新的可执行性实例。

那么,我们在哪里找到可移动物?

ES6将JS中的基本数据结构/收集类型定义为可移动性. 这包括字符串、阵列、地图、套件等。

考虑:

```js
// an array is an iterable
var arr = [ 10, 20, 30 ];

for (let val of arr) {
    console.log(`Array value: ${ val }`);
}
// Array value: 10
// Array value: 20
// Array value: 30
```

由于阵列是可移动的,因此我们可以浅显复制一个阵列,通过`...`散开运算符:

```js
var arrCopy = [ ...arr ];
```

我们还可以一次在字符串中标注字符:

```js
var greeting = "Hello world!";
var chars = [ ...greeting ];

chars;
// [ "H", "e", "l", "l", "o", " ",
//   "w", "o", "r", "l", "d", "!" ]
```

页:1`Map`数据结构使用对象作为按键,将(任何类型的)值与对象联系起来。 地图的默认重迭与这里的重迭不同,因为重迭不仅超过地图的值,而是其 * 参差*. *enter * 是一个Tuple(2-元素阵列),既包括键,也包括值.

考虑:

```js
// given two DOM elements, `btn1` and `btn2`

var buttonNames = new Map();
buttonNames.set(btn1,"Button 1");
buttonNames.set(btn2,"Button 2");

for (let [btn,btnName] of buttonNames) {
    btn.addEventListener("click",function onClick(){
        console.log(`Clicked ${ btnName }`);
    });
}
```

在那个`for..of`绕过默认的地图迭代,我们使用`[btn,btnName]`语法(称为"阵列解构")将每个消耗的Tuple细分为相应的密钥/值对(`btn1` / `"Button 1"`和`btn2` / `"Button 2"`).

JS的每一个内在可移动性 都暴露出一个默认的迭代, 一个可能符合你的直觉。 但必要时也可以选择更具体的迭代. 例如,如果我们只想消耗上面的值`buttonNames`地图,我们可以调用`values()`以获取仅限值的浏览器:

```js
for (let btnName of buttonNames.values()) {
    console.log(btnName);
}
// Button 1
// Button 2
```

或者,如果我们想要指数 * 和 * 值在一个阵列迭代, 我们可以制作一个条目迭代器`entries()`方法:

```js
var arr = [ 10, 20, 30 ];

for (let [idx,val] of arr.entries()) {
    console.log(`[${ idx }]: ${ val }`);
}
// [0]: 10
// [1]: 20
// [2]: 30
```

大部分情况下,联署材料中的所有内置可执行文件都有三种可使用的可执行文件:仅按键(`keys()`),仅值(`values()`)和条目(`entries()`).

除了使用内置的可移动性外, 您还可以确保您自己的数据结构遵守迭代协议; 这样做意味着您选择使用`for..of`循环和`...`操作员。 本协议的"标准化"是指总体上更容易识别和可读的代码.

| 注: |
| :--- |
| 你可能注意到这次讨论的细微变化。 我们首先谈论的是消费**提琴手**,但后来转而谈论过**提琴手**。 迭代-消费协议期望一个"可插入的",但我们可以提供直接的"电阻器"的原因,就是电阻器只是自身可移动的! 在从已有的传动器创建传动器实例时,传动器本身会被返回. |

## 结束

也许没有意识到,几乎每个联署材料开发商都利用了关闭。 事实上,关闭是大多数语言中最普遍的编程功能之一。 理解变量或循环可能同样重要;这就是它的根本性。

但感觉有点隐蔽 几乎是神奇的 它经常被用非常抽象或非常非正式的术语来谈论, 这对帮助我们解决它究竟是什么没有什么帮助。

我们需要能够识别程序中使用关闭的地方,因为存在或缺乏关闭有时是错误的原因(甚至表现问题的原因).

因此,让我们以务实和具体的方式界定结束:

> 关闭是指一个函数记忆并继续从其范围之外访问变量时,即使该函数是在不同的范围内执行的.

这里有两个定义特征。 第一,关闭是功能性质的一部分。 物体不会被关闭,功能也会被关闭. 第二,要观察关闭,您必须在与该函数最初定义的不同范围内执行该函数。

考虑:

```js
function greeting(msg) {
    return function who(name) {
        console.log(`${ msg }, ${ name }!`);
    };
}

var hello = greeting("Hello");
var howdy = greeting("Howdy");

hello("Kyle");
// Hello, Kyle!

hello("Sarah");
// Hello, Sarah!

howdy("Grant");
// Howdy, Grant!
```

首先是`greeting(..)`执行外部函数,创建内在函数实例`who(..)`; 关闭变量上的函数`msg`,这是来自外部范围的参数`greeting(..)`当返回该内在函数时,其参考文献被指定为`hello`变量。 然后我们打电话`greeting(..)`第二次,创建一个新的内在函数实例,在新的函数之上重新关闭`msg`,并返回指定给`howdy`.

当`greeting(..)`函数完成运行,通常我们期望其所有变量都是收集的垃圾(从内存中删除)。 我们期待每个人`msg`离开,但他们没有。 原因是关闭。 由于内部函数实例仍然存在(分配给`hello`和`howdy`他们的封锁仍然在维持`msg`变量。

这些封锁并非是对`msg`变量的值;它们是变量本身的直接联系和维护。 这意味着关闭可以随着时间的推移实际观察(或做!)这些变量的更新.

```js
function counter(step = 1) {
    var count = 0;
    return function increaseCount(){
        count = count + step;
        return count;
    };
}

var incBy1 = counter(1);
var incBy3 = counter(3);

incBy1();       // 1
incBy1();       // 2

incBy3();       // 3
incBy3();       // 6
incBy3();       // 9
```

每一个内在的例子`increaseCount()`函数关闭于`count`和`step`外部变量`counter(..)`函数的范围。`step`时间不变,但`count`在每次援引这种内在功能时都会更新。 由于关闭在变量之上,而不只是数值的快照,这些更新被保存下来.

关闭在使用同步代码时最为常见,例如使用调用回调. 考虑:

```js
function getSomeData(url) {
    ajax(url,function onResponse(resp){
        console.log(
            `Response (from ${ url }): ${ resp }`
        );
    });
}

getSomeData("https://some.url/wherever");
// Response (from https://some.url/wherever): ...
```

内在功能`onResponse(..)`关闭了`url`,从而保存和记住它,直到Ajax呼叫返回和执行`onResponse(..)`尽管`getSomeData(..)`即刻结束`url`参数变量在关闭中保持存活,只要需要。

外部范围不一定要是一个功能——它通常,但并不总是——只是外部范围至少有一个变量从内部功能进入:

```js
for (let [idx,btn] of buttons.entries()) {
    btn.addEventListener("click",function onClick(){
       console.log(`Clicked on button (${ idx })!`);
    });
}
```

因为这个循环正在使用`let`声明,每个迭代得到新的块范围(aka, 局部)`idx`和`btn`变量; 循环还创建了新的内部`onClick(..)`函数每次。 内部功能关闭`idx`,只要点击处理器设置在`btn`。因此,在单击每个按钮时,其处理器可以打印其相关的索引值,因为处理器记得各自的索引值`idx`变量。

记住: 关闭时没有超过值( 如`1`或`3`),但在变量之上`idx`它本身。

关闭是任何语言中最普遍和最重要的编程模式之一. 但JS尤其如此;很难想象在不以某种方式利用关闭的情况下做任何有用的事情.

如果你仍然对关闭感到不明确或动摇,第2册(英语:Book 2 *Scope & Closings *)的大部分内容都集中在这个话题上.

## `this`关键词

联署材料最强大的机制之一也是其最误解的机制之一:`this`关键词 一个常见的误解是,一个函数的`this`指函数本身。 因为如何`this`另一种误解是:`this`指某一方法所属实例。 两者都是错误的。

如前所述,当一个函数被定义时,它被* 附加在通过关闭而附加的范围中。 范围是控制如何解决变量引用问题的一套规则.

但功能除了其范围之外,还有另一个特征,影响它们能够获取的东西。 这个特征最好被描述为"执行上下文*",并通过它的功能暴露在其中.`this`关键词

范围是静态的,包含一组固定的变量,在您定义函数时和位置,但函数的执行*context*是动态的,完全依赖于**它是如何被称作**(无论它被定义或甚至从哪里调用).

`this`不是基于函数定义的函数的固定特征,而是每次调用函数时都会确定的动态特征.

一种思考"执行上下文*"的方法是,它是一个有形物体,其属性在它执行时被赋予一个函数. 与此相比,范围也可以被认为是一个*对象*;除了,"范围对象*"隐藏在JS引擎内,对于这个功能总是是一样的,其"属性*"采取功能内可用的识别变量的形式.

```js
function classroom(teacher) {
    return function study() {
        console.log(
            `${ teacher } says to study ${ this.topic }`
        );
    };
}
var assignment = classroom("Kyle");
```

外边`classroom(..)`函数未提及 a`this`关键词,所以它只是像任何其他的功能 我们已经看到到目前为止。 但内心`study()`函数会引用`this`,这使得它是一个`this`- 意识到功能。 换句话说,它是一个依赖于其*执行上下文*的函数.

| 注: |
| :--- |
| `study()`也关闭在`teacher`变量。 |

内心`study()`函数返回`classroom("Kyle")`被分配到一个名为`assignment`那么,怎样才能`assignment()`(卡卡语)`study()`被叫来?

```js
assignment();
// Kyle says to study undefined  -- Oops :(
```

在这个片段,我们叫`assignment()`作为平坦的,正常的功能,而不提供任何 *执行上下文 *.

由于此程序不采用严格模式(参见第一章,"Strictly Speaking"),因此称为**的上下文函数在没有任何指定上下文的情况下**默认了全局对象的上下文(`window`在浏览器中). 由于没有全球变量命名`topic`(因此全球目标上没有这种财产),`this.topic`决定`undefined`.

现在考虑:

```js
var homework = {
    topic: "JS",
    assignment: assignment
};

homework.assignment();
// Kyle says to study JS
```

副本`assignment`函数引用设置为`homework`对象,然后称为`homework.assignment()`这意味着`this`对于此函数调用`homework`对象。 因此,`this.topic`决定`"JS"`.

最后:

```js
var otherHomework = {
    topic: "Math"
};

assignment.call(otherHomework);
// Kyle says to study Math
```

第三种援引函数的方法是:`call(..)`方法,它取一个对象(`otherHomework`用于设置`this`函数调用时的引用。 财产参考`this.topic`决定`"Math"`.

相同的上下文函数引用了三种不同的方式, 每次给出不同的答案。`this`将参考。

获得的惠益`this`-认识到函数——及其动态上下文——是能够更灵活地使用来自不同物体的数据的单一函数。 一个关闭在一个范围上的函数永远不能引用一个不同的范围或一组变量. 但有动态功能`this`认识背景对某些任务很有帮助。

## 原型

何处`this`是功能执行的特征,原型是对象的特征,具体来说是财产访问的解析.

将原型视为两个物体之间的联系;联系隐藏在幕后,虽然有办法揭露和观察. 这种原型链接发生在一个对象被创建时;它与已经存在的另一个对象相连.

一系列通过原型连接在一起的物体被称为"原型链".

这种原型链接(即从一个对象B到另一个对象A)的目的,是使针对B的对属性/方法的访问被*授权*A处理. 授权财产/方法访问允许两个(或更多!)对象相互合作执行某项任务.

考虑将对象定义为普通文字:

```js
var homework = {
    topic: "JS"
};
```

那个`homework`对象上只有一个属性:`topic`然而,它的默认原型链接连接到`Object.prototype`对象,它有常见的内置方法`toString()`和`valueOf()`,还有其他。

我们可以看到这个原型连接 * 授权 * from`homework`改为:`Object.prototype`:

```js
homework.toString();    // [object Object]
```

`homework.toString()`虽然有用`homework`没有`toString()`定义方法; 代表团援引`Object.prototype.toString()`相反。

### 对象链接

要定义对象原型链接, 您可以使用`Object.create(..)`工具栏:

```js
var homework = {
    topic: "JS"
};

var otherHomework = Object.create(homework);

otherHomework.topic;   // "JS"
```

第一个论点`Object.create(..)`指定一个对象,将新创建的对象链接到,然后返回新创建(和链接!)对象。

图4显示三个物体是如何(`otherHomework`, `homework`,以及`Object.prototype`)与原型链相连:

<figure>
    <img src="images/fig4.png" width="200" alt="Prototype chain with 3 objects" align="center">
    <figcaption><em>Fig. 4: Objects in a prototype chain</em></figcaption>
    <br><br>
</figure>

通过原型链进行授权仅适用于访问者查询一个属性中的值. 如果指定一个对象的属性,则该属性将直接适用于该对象,而不论该对象的原型与何处相连。

| 提普: |
| :--- |
| `Object.create(null)`创建一个没有原型链接到任何地方的物体,所以它纯粹是一个独立的物体;在某些情况下,这可能是更可取的. |

考虑:

```js
homework.topic;
// "JS"

otherHomework.topic;
// "JS"

otherHomework.topic = "Math";
otherHomework.topic;
// "Math"

homework.topic;
// "JS" -- not "Math"
```

任务`topic`直接在`otherHomework`;没有影响`topic`属性在`homework`。然后访问下一个语句`otherHomework.topic`,我们从新财产中看到未经授权的答案:`"Math"`.

图5显示转让后产生`otherHomework.topic`属性:

<figure>
    <img src="images/fig5.png" width="200" alt="3 objects linked, with shadowed property" align="center">
    <figcaption><em>Fig. 5: Shadowed property 'topic'</em></figcaption>
    <br><br>
</figure>

那个`topic`打开`otherHomework`将同一名称的财产“阴影”放在`homework`中的对象。

| 注: |
| :--- |
| 另一种坦率地说,更复杂但也许更常见的 创建原型连接对象的方法 是使用“原型类”模式,从以前开始`class`(见第2章"分类")在ES6中加入. 我们将在附录A"prototypal 'Classes"中更详细地论述这个话题. |

### `this`重审

我们掩护`this`之前的关键词,但它的真正重要性在考虑它如何赋予原型授权函数调用时闪耀. 确实,一个主要原因是`this`支持基于函数的调用方式的动态上下文,这样方法就可以调用通过原型链授权的物体,这些对象仍然维持预期值`this`.

考虑:

```js
var homework = {
    study() {
        console.log(`Please study ${ this.topic }`);
    }
};

var jsHomework = Object.create(homework);
jsHomework.topic = "JS";
jsHomework.study();
// Please study JS

var mathHomework = Object.create(homework);
mathHomework.topic = "Math";
mathHomework.study();
// Please study Math
```

两个物体`jsHomework`和`mathHomework`每个原型链接到单个`homework`对象,该对象具有`study()`函数。`jsHomework`和`mathHomework`每个人得到自己的`topic`财产(见图6)。

<figure>
    <img src="images/fig6.png" width="495" alt="4 objects prototype linked" align="center">
    <figcaption><em>Fig. 6: Two objects linked to a common parent</em></figcaption>
    <br><br>
</figure>

`jsHomework.study()`常驻裁军谈判会议代表`homework.study()`,但其`this` (`this.topic`执行决定`jsHomework`因为这个功能怎么称呼,所以`this.topic`这是`"JS"`类似`mathHomework.study()`授权给`homework.study()`但仍然解决`this`改为:`mathHomework`,因此,`this.topic`作为`"Math"`.

如果`this`决心`homework`然而,在许多其他语言中,似乎`this`这将是`homework`因为`study()`方法确实在`homework`.

与许多其他语言不同,JS的`this`动态是允许原型授权的一个关键组成部分,事实上也是`class`照预期工作!

## 问"为什么?"

本章的意向是,JS在头罩下比表面的闪烁明显要多得多.

在你开始学习和了解JS时, 你能够练习和增强的最重要技能之一是好奇心,

尽管这一章在一些议题上已经深入了起来,但许多细节仍然被完全省略。 这里还有很多东西要学习, 通往这的路径始于你问 你的代码的*正确*问题。 提出正确的问题是成为更好开发者的关键技能.

在这本书的最后一章中,我们要简要地研究一下JS是如何分裂的,正如“你不知道JS Yet*书集”的其余部分一样。 另外,不要跳过本书附录B,该附录有一些练习代码来审查本书涵盖的一些主要话题.
