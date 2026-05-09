# 你不了解JS然而:类型和语法 - 第二版
# 第3章:对象值

| 注: |
| :--- |
| 进行中的工作 |

现在,我们很适应 内置的原始类型, 我们把我们的注意力转向`object`《联合提交的材料》中的类型。

我可以写一整本书深入谈论物体;事实上,我已经写了! 本系列的"Objects & Classes"标题已经涵盖了对象的深度,所以在继续本章之前请务必读到.

而不是重复那本书的内容, 在这里,我们将集中关注如何`object`价值观类型在JS中表现并与其他价值观互动.

## 对象类型

那个`object`价值类型包括几个子类型,每个子类型都有专门的行为,包括:

* 普通对象
* 基本物体(箱形原始物)
* 内置对象
* 数组
* 正则表达式
* 函数(aka, “可调用对象” )

除了专门的行为之外,一个共同的特征是所有物体都可以作为(属性)持有值(包括函数/方法)的集合.

## 普通对象

一般对象值类型有时被称为*plain ol' javascript对象*(POJOs).

普通物体具有文字形式:

```js
address = {
    street: "12345 Market St",
    city: "San Francisco",
    state: "CA",
    zip: "94114"
};
```

此普通对象( POJO),定义为:`{ .. }`卷盖,是一组命名的属性(`street`, `city`, `state`,以及`zip`) (中文(简体) ). 属性可以持有任何值,原始物或其他对象(包括数组,函数等).

同一对象也可以是必须使用下列方法界定的:`new Object()`构造器:

```js
address = new Object();
address.street = "12345 Market St";
address.city = "San Francisco";
address.state = "CA";
address.zip = "94114";
```

默认为普通对象`[[Prototype]]`链接到`Object.prototype`,赋予他们使用几种一般物体方法的机会,例如:

* `toString()` / `toLocaleString()`
* `valueOf()`
* `isPrototypeOf(..)`
* `hasOwnProperty(..)`(最近贬值 -- -- 备选:静态)`Object.hasOwn(..)`函数)
* `propertyIsEnumerable(..)`
* `__proto__`(更好的函数)

```js
address.isPrototypeOf(Object.prototype);    // true
address.isPrototypeOf({});                  // false
```

## 基本目标

联合材料对若干“基本”物体类型作了定义,这些类型是各种内置建筑者的例子,包括:

* `new String()`
* `new Number()`
* `new Boolean()`

注意这些构造器必须与`new`构建基本对象实例的关键词。 否则,这些职能实际上进行类型的胁迫(见第4章)。

这些基本对象构建器创建对象值类型,而不是原始的:

```js
myName = "Kyle";
typeof myName;                      // "string"

myNickname = new String("getify");
typeof myNickname;                  // "object"
```

换句话说,一个基本对象构建器的例子实际上可以被看作是围绕相应的基础原始值的包裹.

| 警报: |
| :--- |
| 人们几乎普遍认为 直接即时攻击这些基本目标是一种坏做法 原始的对应物一般更可预测,更能表现,只要财产/方法的获取需要基本的对象-包装形式,就提供*自动箱*(见下文"自动对象"部分). |

那个`Symbol(..)`和`BigInt(..)`函数在规格中被称为“构造器”,尽管它们与`new`关键词和它们在联署材料计划中产生的价值确实是原始的。

如何,这两种类型都有内部*基本物体*,用于原型授权和*自动拳*.

相比之下,因为`null`和`undefined`原始价值,没有`Null()`或`Undefined()`"建筑师",或相应的基本对象或原型.

### 原型

基本物体构造器的例子有:`[[Prototype]]`和他们的建造者相连`prototype`对象:

* `String.prototype`: 定义`length`属性,以及字符串特定方法,例如`toUpperCase()`,等编.

* `Number.prototype`: 定义数字特定方法, 如`toPrecision(..)`, `toFixed(..)`,等编.

* `Boolean.prototype`: 定义默认`toString()`和`valueOf()`方法。。。

* `Symbol.prototype`: 定义`description`(改进)以及默认`toString()`和`valueOf()`方法。。。

* `BigInt.prototype`: 定义默认`toString()`, `toLocaleString()`,以及`valueOf()`方法。。。

任何直接的例子 内置建筑师有`[[Prototype]]`授权进入各自`prototype`属性/方法。 此外,相应的原始值也通过“自动箱式”方式获得这种授权。

### 自动对象

我曾多次提到*自动拳*(包括第1章和第2章,以及到目前为止在本章中几次). 我们终于该解释这个概念了。

在一个值上访问属性或方法要求该值成为对象. 正如我们已经在第一章中看到的,原始人 *不是* 对象,所以JS需要 临时转换/包装这样一个原始物 到它的基本对象对应物[^AutoBoxing]来完成访问

例如:

```js
myName = "Kyle";

myName.length;              // 4

myName.toUpperCase();       // "KYLE"
```

访问`length`属性或`toUpperCase()`方法,只允许在原始字符串值上,因为JS *auto-boxs * 原始`string`以包装为基本对象,`new String(..)`。否则,所有此类访问都将失败,因为原始人没有任何属性。

更重要的是,当原始值是*auto-boxed * 到其基本对象对应物时,那些内部创建的对象可以使用预先定义的属性/方法(如:`length`和`toUpperCase()`通过a`[[Prototype]]`链接到他们各自的基本对象的原型。

所以"自动箱"`string`是一个实例:`new String()`,因此与`String.prototype`此外,下列情况也是如此:`number`(编为实例)`new Number()`和(或)`boolean`(编为实例)`new Boolean()`).

虽然`Symbol(..)`和`BigInt(..)`“制造者”(使用时无`new`产生原始值,这些原始值也可以被*auto-box* 作为其内部基本对象包装形式,以授权获取属性/方法.

| 注: |
| :--- |
| 详见本系列的"Objects & Classes"一书`[[Prototype]]`与基本物体建造者原型物体的联系和授权/继承的进入权。 |

从`null`和`undefined`没有相应的基本目标,没有这些值的*自动框*。

要考虑的一个主观问题:“自动拳击”是否是一种胁迫形式? 我说是的,虽然有些人不同意。 内部,一个原始体被转换成对象,意思是价值类型发生了改变. 是的,这是暂时的,但很多胁迫都是暂时的. 此外,转换是相当“隐含的”(由财产/方法获取所掩盖,但只发生在内部)。 我们将重新审视第四章中的胁迫性质.

## 其他内建对象

除了基本对象构造器之外,JS还定义了其他一些内置构造器,这些构造器会创造出进一步的专用对象子类型:

* `new Date(..)`
* `new Error(..)`
* `new Map(..)`, `new Set(..)`, `new WeakMap(..)`, `new WeakSet(..)`-- 密钥收藏
* `new Int8Array(..)`, `new Uint32Array(..)`等 -- -- 已索引、 输入阵列收藏
* `new ArrayBuffer(..)`, `new SharedArrayBuffer(..)`等 -- -- 结构化数据收集

## 矩阵

阵列是专门用来作为数值的数值索引集合来表现的对象,而不是像普通对象那样在命名属性上持有值.

阵列具有文字形式:

```js
favoriteNumbers = [ 3, 12, 42 ];

favoriteNumbers[2];                 // 42
```

同样的阵列也可以是必须使用以下方法定义的:`new Array()`构造器:

```js
favoriteNumbers = new Array();
favoriteNumbers[0] = 3;
favoriteNumbers[1] = 12;
favoriteNumbers[2] = 42;
```

阵列是`[[Prototype]]`链接到`Array.prototype`,让他们获得各种面向阵列的方法,例如`map(..)`, `includes(..)`等:

```js
favoriteNumbers.map(v => v * 2);
// [ 6, 24, 84 ]

favoriteNumbers.includes(42);       // true
```

定义的一些方法`Array.prototype`- 例如,`push(..)`, `pop(..)`, `sort(..)`等 -- 通过修改已设置的数组值来进行行为。 其他方法 -- -- 例如,`concat(..)`, `map(..)`, `slice(..)`- 行为方式是创建返回的新数组,使原数组保持完整。 第三类数组函数 -- -- 例如,`indexOf(..)`, `includes(..)`等——只计算并返回一个(非阵列)结果。

## 正则表达式

//待办事项

## 职能

//待办事项

## 拟议:记录/记录

在编写本报告时,有一项(第2阶段)建议[^RecordsTuplesProposal]与普通物体和阵列密切相关,但存在一些显著差异。

记录与平面物体类似,但不可改变(密封,只读),(不相似的物体)作为原始值处理,以价值分配和平等比较为目的. 语法差异是`#`开始前`{ }`释者. 记录只能包含原始值(包括记录和拖累).

Tuples有着完全相同的关系,但是与数组,包括`#`开始前`[ ]`结界者.

需要注意的是,虽然这些看起来和看起来像物体/阵列,但它们确实是原始的(非物体)值.

[^FundamentalObjects]:"20个基本对象",EcamScript 2022语言规格; 4.https://262.ecma-international.org/13.0/#sec-fundamental-objects; 2022年8月访问

[^AutoBoxing]: "6.2.4.6 PutValue(V,W)",第5.a步,ECMAScript 2022语言规格; 2.https://262.ecma-international.org/13.0/#sec-putvalue; 2022年8月访问

[^RecordsTuplesProposal]:"JavaScript Records & Tuples profile"; Robin Ricard, Rick Button, Nicolò Ribaudo; 互联网档案馆的存檔,存档日期2013-12-22.
https://github.com/tc39/proposal-record-tuple; 2022年8月访问
