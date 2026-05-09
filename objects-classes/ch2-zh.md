# 你不了解JS然而:对象和类 - 第2版
# 第2章:物体如何运作

| 注: |
| :--- |
| 进行中的工作 |

对象不仅仅是多个值的容器,尽管很明显,这是大多数与对象相互作用的背景.

为了充分理解JS中的对象机制,并且从我们程序中使用的对象中获取最多的机会,我们需要更仔细地研究对象(及其属性)的一些特征,这些特征在与对象互动时会影响它们的行为.

这些定义物体基本行为的特征在形式上被统称为"元对象协议"(MOP).[^mop]。MOP不仅有助于理解对象将如何行为,而且有助于压倒对象的默认行为来弯曲语言,更充分地适应我们程序的需求。

## 财产描述符

物体上的每个财产在内部都用所谓的"财产描述符"来描述. 这本身就是一个物体(aka,"metaobobject"),上面有多个属性(aka"属性"),说明目标属性的行为.

我们可以检索任何现存财产的地产描述符`Object.getOwnPropertyDescriptor(..)`(ES5): (英语).

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle"
};

Object.getOwnPropertyDescriptor(myObj,"favoriteNumber");
// {
//     value: 42,
//     enumerable: true,
//     writable: true,
//     configurable: true
// }
```

我们甚至可以使用这样的描述符来定义对象上的新属性,使用`Object.defineProperty(..)`(ES5): (英语).

```js
anotherObj = {};

Object.defineProperty(anotherObj,"fave",{
    value: 42,
    enumerable: true,     // default if omitted
    writable: true,       // default if omitted
    configurable: true    // default if omitted
});

anotherObj.fave;          // 42
```

如果已有财产尚未标记为不可配置( 与`configurable: false`在描述中,它总是可以使用`Object.defineProperty(..)`.

| 警报: |
| :--- |
| 本章前面的一些章节提到了"复制"或"复制"属性. 人们可能认为,这种复制/复制属于财产描述。 然而,这些行动实际上都没有这样运作;它们都很简单。`=`样式访问和指派,其效果是忽略了如何定义财产的基本描述符中的任何细微差别。 |

虽然这在野外似乎不太常见,但我们甚至可以同时定义多个属性,每个属性都有自己的描述:

```js
anotherObj = {};

Object.defineProperties(anotherObj,{
    "fave": {
        // a property descriptor
    },
    "superFave": {
        // another property descriptor
    }
});
```

看到这种用法并不常见,因为需要具体控制多个属性定义的情况比较少见. 但在某些情况下可能有用。

### 访问器属性

属性描述符通常定义`value`如上所示,财产。 然而,一种特殊类型的财产,称为"存取者财产"(aka, a getter/setter),可以定义. 对于这些财产,它的描述语没有定义固定的`value`财产,但看起来是这样的:

```js
{
    get() { .. },    // function to invoke when retrieving the value
    set(v) { .. },   // function to invoke when assigning the value
    // .. enumerable, etc
}
```

获取器看起来像一个财产访问( )`obj.prop`但是,在封面之下 它引用`get()`方法定义; 它有点像 如果你打电话`obj.prop()`设置者看起来像财产转让(`obj.prop = value`),但它引用`set(..)`方法定义; 它有点像 如果你打电话`obj.prop(value)`.

让我们举个例子,

```js
anotherObj = {};

Object.defineProperty(anotherObj,"fave",{
    get() { console.log("Getting 'fave' value!"); return 123; },
    set(v) { console.log(`Ignoring ${v} assignment.`); }
});

anotherObj.fave;
// Getting 'fave' value!
// 123

anotherObj.fave = 42;
// Ignoring 42 assignment.

anotherObj.fave;
// Getting 'fave' value!
// 123
```

### 可假设、可写、可配置

再说`value`或`get()` / `set(..)`,财产描述符的其他3个属性为(如上所示):

* `enumerable`
* `writable`
* `configurable`

那个`enumerable`属性控件是否出现在对象属性的各种列表中,例如`Object.keys(..)`, `Object.entries(..)`, `for..in`循环,以及随`...`对象扩展和`Object.assign(..)`。大多数属性应该被保留,但是如果不应该被粘贴/复制,可以在物体上标注某些特殊属性为不可保存。

那个`writable`属性控件是否为 a`value`任务(通过)`=`)允许. 要让属性“ 仅读”, 请用`writable: false`然而,只要财产仍然可以配置,`Object.defineProperty(..)`仍然可以通过设置更改值`value`不一样

那个`configurable`属性控制一个属性的**描述符**是否可以重新定义/覆盖。 财产是`configurable: false`以及任何进一步试图更改的定义`Object.defineProperty(..)`将失败。 不可配置的财产仍然可以指定新的值(通过`=`),只要`writable: true`仍被设定在地产的描述符上。

## 对象子类型

JS中有多种专门的子类物体. 但迄今为止,你会与两个最常见的 相互作用的是阵列和`function`编号

| 注: |
| :--- |
| 通过"子类型",我们指一种衍生类型的概念,它从一个父类型中继承了行为,但后来又专门或扩展了这些行为. 换句话说,这些亚型的值是完全对象,但也是*大于*对象. |

### 矩阵

阵列是专门打算**数字索引**的对象,而不是使用字符串命名的属性位置. 它们是物体,所以一个命名的财产像`favoriteNumber`这是合法的。 但将命名的属性混合成数字索引的数组,是相当可畏的。

矩阵最好用字面语法(类似于对象)来定义,但使用`[ .. ]`方括号而不是`{ .. }`圆括号:

```js
myList = [ 23, 42, 109 ];
```

JS允许在数组中任意混合值类型,包括对象,其他数组,函数等. 您可能已经知道, 阵列是“ 零指数化的 ”, 这意味着阵列中的第一个元素是在索引中。`0`没有`1`:

```js
myList = [ 23, 42, 109 ];

myList[0];      // 23
myList[1];      // 42
```

回顾,一个对象上的任何字符串属性名称,如果"看起来像"一个整数——能够被有效强制到一个数值整数——实际上将被当作一个整数属性(aka,整数指数)处理. 数组也一样。 你应该总是用`42`作为整数索引(aka,属性名称),但如果使用字符串`"42"`JS会假设你是一个整数 为你做这个

```js
// "2" works as an integer index here, but it's not advised
myList["2"];    // 109
```

“ 数组上无命名属性” *rule * 的一个例外是, 所有数组自动曝光一个`length`属性,将自动与数组的“长度”更新。

```js
myList = [ 23, 42, 109 ];

myList.length;   // 3

// "push" another value onto the end of the list
myList.push("Hello");

myList.length;   // 4
```

| 警报: |
| :--- |
| 许多JS开发者错误地认为阵列`length`基本上是一个 *geter *(参见本章前面的"获得者属性"),但它不是. 问题是,这些开发者觉得访问这个地产是"费钱的"——仿佛JS不得不在飞行中重新计算长度——从而在做一个非模拟循环之前会做一些诸如捕获/存储一个阵列长度的事情. 从表现的角度来看,这曾经是"最佳做法". 但至少10年来,这实际上是一个反模式的, 因为JS引擎在管理`length`而不是我们的JS代码 试图"超智能"的引擎 以避免引出一些我们认为是“更好”的东西。 让JS引擎做工作效率更高,只要有需要,无论需要多少,就直接进入房产. |

#### 空槽

JS阵列在设计上也有非常不幸的"flaw",称为"空槽". 如果在数组的当前端之外指定一个多个数组位置的索引,JS将会在"空"字段之间留下一个索引,而不是自动指定它们给`undefined`正如你可能期望的那样:

```js
myList = [ 23, 42, 109 ];
myList.length;              // 3

myList[14] = "Hello";
myList.length;              // 15

myList;                     // [ 23, 42, 109, empty x 11, "Hello" ]

// looks like a real slot with a
// real `undefined` value in it,
// but beware, it's a trick!
myList[9];                  // undefined
```

你可能想知道为什么空位这么差? 原因之一:JS里有API,比如数组的`map(..)`空位突然跳过的地方! 永远不要,永远不要故意在您的数组中创建空位. 这在无可争辩的JS的"坏部分"之一.

### 职能

除了指出它们也是子对象类型外,我在这里对函数没有太多具体的说法. 这意味着他们除了可以执行外,还可以命名他们添加或访问的属性.

函数有两个预定义的属性,你可能会发现自己与这些属性相互作用,特别是为了元编程目的:

```js
function help(opt1,opt2,...remainingOpts) {
    // ..
}

help.name;          // "help"
help.length;        // 2
```

那个`length`函数是指其明确定义的参数的计数,最多但不包括一个有默认值的参数(例如,`param = 42`)或"后备参数"(例如,`...remainingOpts`).

#### 避免设置函数- 对象属性

您应当避免在函数对象上指定属性。 如果您想要存储与函数相关的额外信息, 请单独使用`Map(..)`(或`WeakMap(..)`)以函数对象为键,以额外信息为值。

```js
extraInfo = new Map();

extraInfo.set(help,"this is some important information");

// later:
extraInfo.get(help);   // "this is some important information"
```

## 对象特征

除了定义特定属性的行为外,某些行为在整个对象上也可以被描绘:

* 可扩展
* 密封
* 冻结

### 可扩展

扩展性是指一个对象是否可以有新的属性定义/添加到它上. 默认情况下,所有对象都是可扩展的,但可以更改对象的关闭可扩展性:

```js
myObj = {
    favoriteNumber: 42
};

myObj.firstName = "Kyle";                  // works fine

Object.preventExtensions(myObj);

myObj.nicknames = [ "getify", "ydkjs" ];   // fails
myObj.favoriteNumber = 123;                // works fine
```

在非限制性模式下,创造新财产的转让将默默失败,而在严格模式下,将出现例外。

### 密封

//待办事项

### 冻

//待办事项

## 《议定书》延期

如本章开头所述,联署材料中的物体行为符合一套称为《Metaobject议定书》的规则。[^mop]。既然我们更充分地了解了对象在默认情况下是如何工作的,我们想把我们的注意力转向如何连接到其中一些默认行为上,并推翻/定制它们。

//待办事项

## `[[Prototype]]`链

一个物体(MOP的一部分)最重要的但最不明显的特征之一,被称为它的"原型链条";官方JS规格说明是:`[[Prototype]]`确保不要混淆`[[Prototype]]`公共财产`prototype`. 尽管有命名,这些是不同的概念。

那个`[[Prototype]]`是一个对象创建时默认获得的内部链接,指向另一个对象。 这种联系是物体的隐藏的,往往是微妙的特征,但对与物体的相互作用将如何发挥产生深远影响. 它被称为"链条",因为一个物体与另一个物体相连,而另一个物体又与另一个物体相连,......等等. 在这个链条上有一个*末*或*顶*,其中连接停止了,没有进一步. 更多关于这个不久。

我们已经看到一些影响`[[Prototype]]`第1章中的链接。 例如,默认情况下,所有对象都是`[[Prototype]]`- 与命名的内置对象链接`Object.prototype`.

| 警报: |
| :--- |
| 那个`Object.prototype`名称本身可能令人困惑,因为它使用一个名为`prototype`。怎么样`[[Prototype]]`和`prototype`关系? 暂停一下,我们再解释一下`[[Prototype]]`和`prototype`后於本章. 就目前而言,只要假设这个重要但奇怪的内置物体的存在,`Object.prototype`. |

让我们来考虑一些代码:

```js
myObj = {
    favoriteNumber: 42
};
```

从第一章来看,这应该是熟悉的。 但在这个代码中你看不到的是 那里的物体是自动连接的(通过其内部的)`[[Prototype]]`对于自动内置,但奇怪的命名,`Object.prototype`对象。

当我们做诸如:

```js
myObj.toString();                             // "[object Object]"

myObj.hasOwnProperty("favoriteNumber");   // true
```

我们正在利用这个内部`[[Prototype]]`联系,没有真正意识到它。 从`myObj`没有`toString`或`hasOwnProperty`上面定义的属性,这些属性访问实际上最终**DELEGALING** 继续其沿`[[Prototype]]`链条。

从`myObj`这是`[[Prototype]]`- 与命名对象链接`Object.prototype`,寻找`toString`和`hasOwnProperty`属性在对象上继续;事实上,这些方法就在那里找到!

能力`myObj.toString`访问`toString`财产虽然实际上没有,但通常被称为"继承",或更具体地说是"财产继承". 那个`toString`和`hasOwnProperty`据说,财产与许多其他财产一样,是“继承的财产”。`myObj`.

| 注: |
| :--- |
| 我对这里使用"继承"一词感到很多挫折——应该称之为"委托! 但大多数人都这么认为, 所以我们现在只能勉强遵守并使用同样的术语(尽管在抗议下, 我会把反对意见留作本书的附录. |

`Object.prototype`具有若干内置属性和方法,所有这些属性和方法都由任何物体所“继承”`[[Prototype]]`- 直接或间接通过另一个对象的链接与`Object.prototype`.

一些常见的"继承"属性来自`Object.prototype`包括:

* `constructor`
* `__proto__`
* `toString()`
* `valueOf()`
* `hasOwnProperty(..)`
* `isPrototypeOf(..)`

回顾`hasOwnProperty(..)`,我们早些时候看到它给了我们一个布尔检查,以确定某个属性(通过字符串名称)是否为对象所拥有:

```js
myObj = {
    favoriteNumber: 42
};

myObj.hasOwnProperty("favoriteNumber");   // true
```

人们一直认为,这样重要的用途是:`hasOwnProperty(..)`包含在对象上`[[Prototype]]`链是一种实例方法,而不是被定义为静态工具。

截至ES2022,JS终于添加了这个功能的静态版本:`Object.hasOwn(..)`.

```js
myObj = {
    favoriteNumber: 42
};

Object.hasOwn(myObj,"favoriteNumber");   // true
```

现在,这种形式被认为是更可取和稳健的选择,也是实例方法(例如:`hasOwnProperty(..)`现在一般应避免形式。

有些不幸和不一致的地方,没有相应的静态公用事业,比如:`Object.isPrototype(..)`(代替实例法)`isPrototypeOf(..)`) (中文(简体) ). 不过至少`Object.hasOwn(..)`存在,所以这是进步。

### 创建一个不同对象`[[Prototype]]`

默认情况下,您在程序中创建的任何对象都将是`[[Prototype]]`- 和那个有关`Object.prototype`对象。 然而,您可以创建不同链接的对象:

```js
myObj = Object.create(differentObj);
```

那个`Object.create(..)`方法将其第一个参数作为新创建对象的值`[[Prototype]]`.

这种方法的一个缺点是,你没有使用`{ .. }`字面语法,所以您最初不定义任何内容`myObj`。然后通常需要逐一定义属性,使用`=`.

| 注: |
| :--- |
| 第二个备选论点:`Object.create(..)`是 -- -- 如第二个论点:`Object.defineProperties(..)`如前所述 -- -- 具有特性的对象,其属性将标记符以初始定义新对象。 在野外的实践中,这种形式很少使用,可能是因为指定完整的描述符而不只是名称/值对更尴尬. 但在某些有限的情况下,这样做可能是有益的。 |

换句话说,但最好不要用`{ .. }`文字语法与特殊(和奇怪的外观!)属性:

```js
myObj = {
    __proto__: differentObj,

    // .. the rest of the object definition
};
```

| 警报: |
| :--- |
| 奇怪的样子`__proto__`财产在一些联署机构的引擎中已经存在了20多年,但截至2015年,联署机构的标准化程度仅为ES6。 即便如此,在规格的附录B中也添加了该词。[^specApB]上面列出了 TC39 令人憎恶地包括的功能,因为这些功能在各种基于浏览器的 JS 引擎中普遍存在,因此即使它们并非源于 TC39,也是一种事实上的现实. 因此,这个特性被所有基于符合要求的浏览器JS引擎中存在的光谱"保证",但不一定保证在其他独立的JS引擎中工作. Node.js使用来自Chrome浏览器的JS引擎(v8),因此Node.js获得.`__proto__`默认/事故。 使用时小心`__proto__`要知道所有的JS引擎环境 您的代码会运行在。 |

是否使用`Object.create(..)`或`__proto__`,该创建对象通常是`[[Prototype]]`- 链接到与默认不同的对象`Object.prototype`.

#### 空`[[Prototype]]`链接

我们提到,`[[Prototype]]`连锁店必须停在某个地方,这样才能让检查不会永远继续下去。`Object.prototype`通常为每张的顶部/末端`[[Prototype]]`链条,作为自己的`[[Prototype]]`这是`null`所以没有别的地方可以继续寻找

然而,您也可以用自己的定义对象`null`数值`[[Prototype]]`,例如:

```js
emptyObj = Object.create(null);
// or: emptyObj = { __proto__: null }

emptyObj.toString;   // undefined
```

创建一个没有`[[Prototype]]`链接到`Object.prototype`例如,如第一章所述,`in`和`for..in`构造将参考`[[Prototype]]`用于继承财产的链条。 但这也许是不可取的,因为你可能不想 喜欢的东西`"toString" in myObj`成功解决。

此外,一个物体如果是空的`[[Prototype]]`不受任何意外的"继承" 碰撞,它自己的财产名称 和它"继承" 从其他地方。 这些类型的(有用!)对象有时在流行的言语中被称为"词典对象".

### `[[Prototype]]`数字`prototype`

关于公共财产名称的通知`prototype`在此特殊对象的名称/位置中,`Object.prototype`这是怎么回事?

`Object`这是`Object(..)`函数;默认情况下,所有函数(它们本身是对象!)都有这样的函数!`prototype`指着物体。

这里的任何名称冲突之处`[[Prototype]]`和`prototype`真的咬我们。 那个`prototype`属性在函数上并不定义函数本身经历的任何链接。 事实上,功能(作为物体)本身具有内部`[[Prototype]]`在别的地方建立联系 -- -- 稍后再谈这个问题。

相反,`prototype`属性是指在调用该函数时,由其它任何对象创建的、应当由 * 链接到 * 的对象。`new`关键字:

```js
myObj = {};

// is basically the same as:
myObj = new Object();
```

自从`{ .. }`对象字面语法基本上与`new Object()`调用, 以`Object.prototype`用作内部`[[Prototype]]`用于我们创建和命名的新对象的值`myObj`.

嘘! 谈论一个主题 更令人困惑 仅仅因为名称重叠`[[Prototype]]`和`prototype`!

----

但何者能连结自己,`[[Prototype]]`聪明? 他们联系`Function.prototype`,又是一个内置对象,位于`prototype`属性`Function(..)`函数。

换句话说,你可以认为功能本身是被一个`new Function(..)`打电话,然后`[[Prototype]]`- 连接到`Function.prototype`对象。 此对象包含默认情况下所有“ 继承” 功能的属性/ 方法, 例如`toString()`(将函数的源代码序列化)和`call(..)` / `apply(..)` / `bind(..)`(我们稍后在本书中解释).

## 对象行为

对象上的属性是由一个"描述器"元对象内部定义和控制的,它包括诸如:`value`(财产的现值)和`enumerable`(bullean控制财产是否被列入财产/财产名称的可假定清单)。

JS中的对象及其属性工作方式被称为"对象协议"(MOP).[^mop]我们可以通过`Object.defineProperty(..)`,以及对象行为`Object.freeze(..)`。但更强大的是,我们可以使用特殊预定义的符号,在对象上勾上并推翻某些默认行为。

原型(英語:Prototypes)是对象之间的内部联系,它允许属性或方法访问对一个对象——如果请求的属性/方法不存在——通过"授权"访问另一个对象处理. 当授权涉及一种方法时,该方法的上下文通过该方法从初始对象共享到目标对象。`this`关键词

[^mop]: "Metaobobject", 维基百科; 互联网档案馆的存檔,存档日期2013-03-02.https://en.wikipedia.org/wiki/Metaobject; 2022年7月查阅.

[^specApB]:"附录B:网络浏览器附加ECMAScript功能",ECMAScript 2022语言规格; 3.https://262.ecma-international.org/13.0/#sec-additional-ecmascript-features-for-web-browsers; 2022年7月访问
