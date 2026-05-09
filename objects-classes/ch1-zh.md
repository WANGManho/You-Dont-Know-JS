# 你不了解JS然而:对象和类 - 第2版
# 第1章:目标基础

| 注: |
| :--- |
| 进行中的工作 |

> JS里的一切都是目标

这是最普遍但最不正确的“事实”, 让神话开始吧

JS肯定有对象,但这并不意味着所有值都是对象. 因此掌握这些东西对于你的JS旅程至关重要。

对象机制当然是最灵活和最强大的容器类型——你将其他的值投入其中;你所写的每个JS程序都会以某种方式使用. 但这不是为什么 物品值得最高的帐单 对于这本书。 物体是JS的三根支柱中的第二根:原型.

为什么是原型?`this`关键词,在书的后面覆盖) 所以核心联署材料 成为它的三大支柱之一? 除其他外,原型是JS的物体系统如何表达类设计模式,这是在所有编程中最广泛依赖的设计模式之一.

因此,我们的旅程将从物体开始 建立对原型的强制理解,去神秘`this`关键词,并探索`class`系统。

## 关于此书

欢迎收看《你不知道JS Yet》系列第3集! 如果你已经完成了 * 开始 * (第一本书)和 * 范围与关闭 * (第二本书), 你的位置是正确的! 如果没有,在你继续之前,我鼓励你先把这两个作为基础,然后再潜入这本书中。

这本书的第一版题为"这个和对象原型". 在这本书中,我们的焦点是从`this`关键词,因为它可以说是所有联署材料中最混乱的话题之一. 这本书随后花费了大部分时间,专注于揭露原型系统,并倡导拥抱鲜为人知的"委托"模式而不是阶级设计. 在这本书写作时(2014年),ES6仍将是接近完成的2年,所以我感觉到早期的草图.`class`关键词仅应作简短的报导增编。

从那本书出版以来的近八年里,JS的地貌发生了很大的变化,这真是夸夸其谈. ES6现在是旧闻;在*本*本书撰写时,JS每年在ES6**(ES2016至ES2022)之后看到7次更新。

现在,我们仍然需要谈谈如何`this`以及这与针对各种目标所援引的方法有何关系。 还有`class`实际通过封面下深处的原型链运行(大多数!). 但JS在2022年的开发者几乎再也不会写代码来明确连接原始遗产. 尽管我个人的愿望不同,阶级设计模式——而不是"行为代表团"——是JS中大多数数据和行为组织(数据结构)如何表达的.

这本书反映了JS的当前现实:因此,新的副标题,新的组织和专题重点,并完整地重写了前一期的文本.

## 对象 作为集装箱

在单一容器中收集多个值的一种常见方法是与一个对象在一起。 对象为密钥/值对的集合. JS中也有具有特殊行为的天体的子类型,如数组(数字索引)甚至函数(可调用);更多关于这些子类型后来.

| 注: |
| :--- |
| 密钥常被称为"财产名称",其属性名称的对称和值常被称为"财产". 这本书将以这种方式明确使用这些术语。 |

普通的联署材料对象通常用文字语法宣布,例如:

```js
myObj = {
    // ..
};
```

** 说明:** 创建对象的替代方法( 使用`myObj = new Object()`)),但这并非常见或偏好,而且几乎从来不是进行这项工作的适当途径。 坚持对象字法.

很容易搞混什么对`{ .. }`也就是说,由于联署材料的括号过多,根据所使用的上下文,指以下任何一种:

* 定义值, 如对象字节
* 定义对象解结构图案( 稍后更多关于此图)
* 划定插线字符串表达式, 如`` `一些${ 获得数字( )} 事情` ``
* 定义块, 如在`if`和`for`循环
* 定义职能机构

虽然有时在读代码时可能会有挑战性,但看看是否`{ .. }`在一个值/表达值有效出现的程序中使用卷缩对;如果是的话,它是一个对象字形,否则它是其他超载用途之一.

## 定义属性

在对象的字形卷曲括号内,您定义属性(名称和值)`propertyName: propertyValue`双,像这样:

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle"
};
```

您指定属性的值可以是文字, 如所示, 或者可以通过表达式计算:

```js
function twenty() { return 20; }

myObj = {
    favoriteNumber: (twenty() + 1) * 2,
};
```

表达方式`(twenty() + 1) * 2`立即评估,并取得结果(`42`作为财产价值转让。

开发者有时会怀疑是否有方法定义一个属性值的表达式,其中表达式为"懒惰",意思是它不是在指派时计算出来的,而是后来定义的. JS没有懒惰的表达方式,所以唯一的方法就是将表达方式包裹在一个函数中:

```js
function twenty() { return 20; }
function myNumber() { return (twenty() + 1) * 2; }

myObj = {
    favoriteNumber: myNumber   // notice, NOT `myNumber()` as a function call
};
```

在这种情况下,`favoriteNumber`不是一个数值,而是一个函数引用。 为计算结果,必须明确执行该函数的引用。

### 看起来像JSON?

你可能会注意到,我们迄今看到的这个对象语法类似于相关的语法"JSON"(JavaScript Object Notation):

```json
{
    "favoriteNumber": 42,
    "isDeveloper": true,
    "firstName": "Kyle"
}
```

JSON对象文字和JSON最大的区别在于,对于定义为JSON的对象:

1. 必须引用属性名称`"`双引号字符

2. 属性值必须是文字(原始、对象或数组),而不是任意的JS表达式

在联署材料的方案中,对象字面上不需要引用的属性名称 -- -- 你*可以引用它们(`'`或`"`允许),但通常是可选的. 不过,有些字符在一个属性名称中是有效的,但是没有周围的引号就无法包含;例如,引号或白空格:

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle",
    "2 nicknames": [ "getify", "ydkjs" ]
};
```

另一个小的区别是,JSON语法 -- -- 也就是说,文本将* 被解析为JSON*,例如来自`.json`- 比一般JS更严格。 例如,联署材料允许发表评论(例如:`// ..`和`/* .. */`和末条,`,`对象和数组表达式中的逗号; JSON 不允许其中任何一种. 幸好JSON仍然允许任意的白空间.

### 属性名称

对象文字中的属性名称几乎总是作为字符串值处理/coected. 其中一个例外是整数(或"整数外观")属性"名称":

```js
anotherObj = {
    42:       "<-- this property name will be treated as an integer",
    "41":     "<-- ...and so will this one",

    true:     "<-- this property name will be treated as a string",
    [myObj]:  "<-- ...and so will this one"
};
```

那个`42`属性名称将作为整数属性名称(aka, 索引);`"41"`字符串值也会被这样对待,因为它的*看起来像*一个整数. 相比之下,`true`值将成为字符串属性名称`"true"`,则`myObj`标识符参考, *通过周围计算`[ .. ]`,将强制对象的值为字符串(一般是默认值)`"[object Object]"`).

| 警报: |
| :--- |
| 如果你需要实际使用一个对象作为密钥/属性名称,就永远不要依赖这个计算出的字符串强制;它的行为令人惊讶,几乎肯定不是预期的那样,所以程序错误很可能发生. 相反,使用更专业的数据结构,称为`Map`(在ES6中添加),其中用作属性"名称"的物体被留在原地,而不是被强制到字符串值. |

一样`[myObj]`上,您可以在对象字面定义时计算任何**属性名称**(与计算属性值相区别):

```js
anotherObj = {
    ["x" + (21 * 2)]: true
};
```

表达方式`"x" + (21 * 2)`,必须出现在`[ .. ]`括号,立即计算,结果`"x42"`)作为属性名称.

### 作为属性名称的符号

ES6增加了一个新的原始值类型`Symbol`,它常被用作存储和检索财产价值的特殊财产名称。 他们通过`Symbol(..)`函数调用(** 不包含)`new`关键词),它接受一个只用于friender调试目的的可选描述字符串;如果指定的话,描述是JS程序无法访问的,因此除了调试输出之外没有其他目的使用.

```js
myPropSymbol = Symbol("optional, developer-friendly description");
```

| 注: |
| :--- |
| 符号有点像数字或字符串,只不过其价值对JS程序来说是*opaque*,在全球范围内是独一无二的. 换句话说,你可以创造和使用符号,但JS不会让你知道任何基本价值,或用它做什么;这被JS引擎保存为隐藏的执行细节. |

如前所述,计算出来的属性名称是指在对象文字上如何定义符号属性名称:

```js
myPropSymbol = Symbol("optional, developer-friendly description");

anotherObj = {
    [myPropSymbol]: "Hello, symbol!"
};
```

用于定义属性的计算属性名称`anotherObj`将是实际原始符号值(无论是什么),而不是可选描述字符串(`"optional, developer-friendly description"`).

因为符号在你的程序中是全球独有的,所以有**不**偶然碰撞的机会,程序的一部分可能会意外地定义一个属性名称,与程序的另一部分尝试过定义/指定相同.

符号对连接对象的特殊默认行为也有帮助,我们将在下一章的"Extinding the MOP"中对此进行更详细的描述.

### 简洁属性

在定义对象字面时,通常使用一个属性名称,这个属性名称与现有的镜内标识符相同,它持有您要指定的值.

```js
coolFact = "the first person convicted of speeding was going 8 mph";

anotherObj = {
    coolFact: coolFact
};
```

| 注: |
| :--- |
| 这和引用的财产名称定义是一样的`"coolFact": coolFact`,但JS开发者很少引用财产名称,除非绝对有必要. 事实上,除非需要,避免引文是典型的,所以不鼓励不必要地列入引文. |

在这种情况下,当属性名称和值表达式标识符相同时,可以省略属性定义中的属性名称部分,作为所谓的"简明属性"定义: 1.

```js
coolFact = "the first person convicted of speeding was going 8 mph";

anotherObj = {
    coolFact   // <-- concise property short-hand
};
```

财产名称是`"coolFact"`(字符串),财产的价值是`coolFact`变量:`"the first person convicted of speeding was going 8 mph"`.

一开始,这种简便的方便可能看起来很混乱. 但随着你更加熟悉这个非常常见和流行的特征被使用,你可能会倾向于打字(和阅读!)更少.

### 简明方法

另一个类似的简称是使用更简洁的形式定义对象文字中的函数/方法:

```js
anotherObj = {
    // standard function property
    greet: function() { console.log("Hello!"); },

    // concise function/method property
    greet2() { console.log("Hello, friend!"); }
};
```

虽然我们讨论的是简洁的方法属性,但我们也可以定义生成函数(另一个ES6特性):

```js
anotherObj = {
    // instead of:
    //   greet3: function*() { yield "Hello, everyone!"; }

    // concise generator method
    *greet3() { yield "Hello, everyone!"; }
};
```

简洁的方法/生成者甚至可以引用或计算名字:

```js
anotherObj = {
    "greet-4"() { console.log("Hello, audience!"); },

    // concise computed name
    [ "gr" + "eet 5" ]() { console.log("Hello, audience!"); },

    // concise computed generator name
    *[ "ok, greet 6".toUpperCase() ]() { yield "Hello, audience!"; }
};
```

### 对象扩展

在对象字面创建时间定义属性的另一种方式是:`...`语法——从技术上讲它不是一个操作器,但是它看起来肯定是——经常被称为"对象扩散".

那个`...`当在一个对象字面内使用时,会将另一个对象值的内容(财产,aka密钥/值对)"扩展"到被定义的对象中:

```js
anotherObj = {
    favoriteNumber: 12,

    ...myObj,   // object spread, shallow copies `myObj`

    greeting: "Hello!"
}
```

传播`myObj`属性比较浅, 仅复制顶级属性`myObj`;这些属性持有的任何值都会被简单地指定。 如果其中任何一个值是引用其它对象,则引用本身被指定(通过复制),但基础对象值是*不*重复的——所以你最后会给同一个对象带来多个共享引用.

你可以想象物体的传播 像一个`for`循环,一次贯穿一个属性并做一个`=`来自源对象的样式指派( E)`myObj`目标对象(`anotherObj`).

另外,考虑这些属性定义操作会发生"顺序",从对象文字的上到下. 在上述片断中,`myObj`有一个`favoriteNumber`属性, 对象扩展将最终覆盖`favoriteNumber: 12`上行的属性分配。 此外,如果`myObj`载有`greeting`被复制的属性,下行(`greeting: "Hello!"`)将推翻该财产定义。

| 注: |
| :--- |
| 对象散开的还只复制*自有 * 属性(那些直接在对象上),这些属性是*可假定 * (被允许列出/列出)。 它不重复财产——实际上模仿财产的确切特征——而是做一个简单的指派样式复制。 在下一章的"财产说明书"一节中将更多这样的细节叙述. |

一个常见的方法`...`对象分布用于执行 *shallow*对象重复:

```js
myObjShallowCopy = { ...myObj };
```

记住,你不能`...`扩展为已存在的对象值;`...`对象扩展语法只能出现在`{ .. }`对象字元,它正在创建一个新的对象值。 要执行类似的浅对象副本,但使用API而不是语法,请参见本章后面的"Object Entries"部分(涵盖`Object.entries(..)`和`Object.fromEntries(..)`).

但如果你想将对象属性(shallowly)复制到 * 存在的 * 对象中,请参见本章后面的"签名属性"部分(涵盖`Object.assign(..)`).

### 深对象复制

而且,从`...`不做完整,深层对象重复,对象扩散一般只适合复制只持有简单,原始值的物体,而不是引用其他对象.

深层的物体重复是一个令人难以置信的复杂和细微的操作. 复制像`42`但复制一个函数(它是一种特殊的对象,也是参考对象)或复制外部对象(不完全在联署材料中)参考对象(如DOM元素)意味着什么? 如果一个物体有圆形的参考物(如巢形的后代物体在外祖先物体上背着一个参考物)会发生什么? 野外对于这些角落案件应该如何处理有各种各样的观点,因此对于深层对象重复不存在单一的标准.

为了与目标重叠,标准方法是:

1. 使用一个库工具来宣布关于如何处理重复行为/细微差别的具体意见。

2. 使用`JSON.parse(JSON.stringify(..))`圆圈诡计——如果没有循环引用,如果对象中没有不能与JSON(如函数)正确序列的值,则只有"工作"正确.

最近,第三种选择已经落地。 这不是联署材料的特色,而是网络平台等环境提供给联署材料的配套API。 对象现在可以使用深复制`structuredClone(..)`[^structuredClone].

```js
myObjCopy = structuredClone(myObj);
```

此内置工具背后的基础算法支持重复循环引用,以及** 更多** 类型的值比`JSON`圆行伎俩. 然而,这个算法仍有其极限,包括没有支持克隆功能或DOM元素.

## 访问属性

现有对象的财产访问最好与`.`运算符:

```js
myObj.favoriteNumber;    // 42
myObj.isDeveloper;       // true
```

如果可以这样进入一个房产,强烈建议这样做.

如果属性名称包含无法在标识符中出现的字符,例如主数字或白空格,`[ .. ]`可使用括号代替`.`:

```js
myObj["2 nicknames"];    // [ "getify", "ydkjs" ]
```

```js
anotherObj[42];          // "<-- this property name will..."
anotherObj["41"];        // "<-- this property name will..."
```

尽管数字属性“姓名”仍为数字,但通过`[ .. ]`括号会将字符串表示符强制为数字(例如,`"42"`作为`42`),然后相应访问相关的数字属性。

类似于对象字面,访问的属性名称可以通过`[ .. ]`括号。 表达式可以是简单的标识符:

```js
propName = "41";
anotherObj[propName];
```

事实上,你把中间的东西`[ .. ]`括号可以是任何任意的JS表达式,而不只是标识或字面价值,例如:`42`或`"isDeveloper"`JS将首先评价表达式,然后将由此得出的值作为查找对象的属性名称:

```js
function howMany(x) {
    return x + 1;
}

myObj[`${ howMany(1) } nicknames`];   // [ "getify", "ydkjs" ]
```

在此片断中, 表达式是一个反键定义`` `模板字符串文字` ``带有函数调用内插表达式`howMany(1)`。该表达式的总结果是字符串值。`"2 nicknames"`,然后用作访问的财产名称。

### 对象条目

您可以获取对象中属性的列表, 作为持有属性名称和值的拖曳( tuple) 数组:

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle"
};

Object.entries(myObj);
// [ ["favoriteNumber",42], ["isDeveloper",true], ["firstName","Kyle"] ]
```

列入ES6,`Object.entries(..)`从源对象获取此条目列表——仅包含拥有可假设属性;参见下一章中的"财产描述符"部分.

这样的列表可以被环绕/显示,有可能将属性分配给另一个现有对象. 然而,也可以从条目列表中创建一个新对象,使用`Object.fromEntries(..)`(列入ES2019):

```js
myObjShallowCopy = Object.fromEntries( Object.entries(myObj) );

// alternate approach to the earlier discussed:
// myObjShallowCopy = { ...myObj };
```

### 调整结构

获取属性的另一种方法是通过对象分解(在ES6中添加). 认为解结构是定义一个"派特尔",描述一个对象值应该是什么"看起来像"(结构上),然后要求JS跟随这个"派特尔"系统访问一个对象值的内容.

对象分解的最终结果不是另一个对象,而是从源对象向其他目标(变量等)进行一项或多项任务.

想象一下这种前ES6代码:

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle"
};

const favoriteNumber = (
    myObj.favoriteNumber !== undefined ? myObj.favoriteNumber : 42
);
const isDev = myObj.isDeveloper;
const firstName = myObj.firstName;
const lname = (
    myObj.lastName !== undefined ? myObj.lastName : "--missing--"
);
```

财产价值的这些存取,以及转让给其他识别器,一般称为"人工解构". 要使用声明对象解构语法,可能看起来是这样:

```js
myObj = {
    favoriteNumber: 42,
    isDeveloper: true,
    firstName: "Kyle"
};

const { favoriteNumber = 12 } = myObj;
const {
    isDeveloper: isDev,
    firstName: firstName,
    lastName: lname = "--missing--"
} = myObj;

favoriteNumber;   // 42
isDev;            // true
firstName;        // "Kyle"
lname;            // "--missing--"
```

如所示,`{ .. }`对象切变类似于对象的字面值定义,但它出现在左侧`=`运算符而不是显示对象值表达式的右侧。 这么说`{ .. }`左侧是一个解结构图案,而不是另一个对象定义。

那个`{ favoriteNumber } = myObj`分解结构让JS找到一个名为`favoriteNumber`,并指定其值为同一名称的标识符。 单一实例`favoriteNumber`模式中的标识符与本章前面讨论的"简明属性"相似:如果源(财产名称)和目标(识别符)相同,可以省略其中之一,只列出一次.

那个`= 12`部分请联合来文提供`12`作为转让到`favoriteNumber`如果源对象没有`favoriteNumber`财产,或财产持有`undefined`数值。

在第二个解构模式中,`isDeveloper: isDev`正在指示JS找到一个名为`isDeveloper`在源对象上,将其值指定为命名为`isDev`. 某种意义上的"重命名"来源到目标. 相比之下,`firstName: firstName`正在提供任务的来源和目标,但因相同而多余;`firstName`这里已经足够了,而且通常更可取。

那个`lastName: lname = "--missing--"`合并源目标重命名和一个默认值(如果`lastName`源属性缺失或`undefined`).

以上片段将对象解结构与可变声明结合起来 -- -- 例如,`const`已经使用,但`var`和`let`(笑声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声)(掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声(掌声) (掌声) (掌声) 调整结构涉及访问和分配(来源到目标),因此它可以针对现有目标运作,而不是宣布新的目标:

```js
let fave;

// surrounding ( ) are required syntax here,
// when a declarator is not used
({ favoriteNumber: fave } = myObj);

fave;  // 42
```

对象解构语法一般因其宣示性较可读性较强的风格而优于急需的ES6前等效物. 但不要因为分解而过份. 有时只是做`x = someObj.x`完全没问题!

### 有条件的财产访问

最近(在ES2020),JS增加了一个被称为"可选连锁"的功能,它增强了物业访问能力(尤其是嵌入物业访问). 主要形式是双特征复合操作器`?.`喜欢`A?.B`.

此运算符将检查左侧引用( I)`A`看它是否无效(`null`或`undefined`) (中文(简体) ). 如果是的话,其余的财产访问表达式是短路的(滑动),以及`undefined`以结果返回(即使是)`null`这实际上是遇到的!). 否则`?.`将像正常一样访问财产`.`接线员会的

例如:

```js
myObj?.favoriteNumber
```

这里,无效的检查是针对`myObj`这意味着`favoriteNumber`属性访问仅在下列情况下进行:`myObj`是非无谓的。 注意它不能证实`myObj`其实是持有一个真正的物体, 只是它不是nullish。 然而,所有非无核价值都可以通过“无核”标准“安全”(没有JS例外)标准“获取”。`.`操作员,即使没有匹配的属性可以检索.

很容易弄糊涂地认为 无效支票是针对`favoriteNumber`属性。 但保持直线的方法之一是记住`?`在进行安全检查的一侧,而`.`只在非无核支票通过时才能有条件地评估。

通常情况下`?.`操作员用于可能深达3级或3级以上的嵌入式财产访问,例如:

```js
myObj?.address?.city
```

与《公约》同等的行动`?.`操作员会像这样:

```js
(myObj != null && myObj.address != null) ? myObj.address.city : undefined
```

请记住,没有针对最合适的财产进行过任何检查(`city`这里。

还有`?.`不应普遍用于取代每一个`.`程序中的操作员。 你应该努力知道,如果`.`只要有可能,在进入之前,财产进入将成功与否。 使用`?.`仅当访问的值的性质受制于无法预测/控制的条件时。

例如,在上一个片断中,`myObj?.`使用可能是被错误地引导的,因为你真的不应该针对一个甚至不能持有顶级对象的变量(除了其内容可能在某些条件下丢失某些属性)启动一个属性访问链.

相反,我建议采用这样的做法:

```js
myObj.address?.city
```

而这个表达方式只应用于你程序的一部分,你确信`myObj`至少持有一个有效的对象(无论它是否有`address`属性,带有子对象。

“可选链式”运算符的另一种形式是`?.[`,当您想要设定条件/安全性需要时使用该选项。`[ .. ]`括号。

```js
myObj["2 nicknames"]?.[0];   // "getify"
```

所有关于如何`?.`行为也一样`?.[`.

| 警报: |
| :--- |
| There's a third form of this feature, named "optional call", which uses `?.(` as the operator. It's used for performing a non-null'ish check on a property before executing the function value in the property. For example, instead of `myObj.someFunc(42)`, you can do `myObj.someFunc?.(42)`. The `?.(` checks to make sure `myObj.someFunc` is non-null'ish before invoking it (with the `(42)` part). While that may sound like a useful feature, I think this is dangerous enough to warrant complete avoidance of this form/construct.<br><br>My concern is that `?.(` makes it seem as if we're ensuring that the function is "callable" before calling it, when in fact we're only checking if it's non-null'ish. Unlike `?.` which can allow a "safe" `.` access against a non-null'ish value that's also not an object, the `?.(` non-null'ish check isn't similarly "safe". If the property in question has any non-null'ish, non-function value in it, like `true` or `"Hello"`, the `(42)` call part will be invoked and yet throw a JS exception. So in other words, this form is unfortunately masquerading as more "safe" than it actually is, and should thus be avoided in essentially all circumstances. If a property value can ever *not be* a function, do a more fullsome check for its function'ness before trying to invoke it. Don't pretend that `?.(` is doing that for you, or future readers/maintainers of your code (including your future self!) will likely regret it. |

### 在非对象上访问属性

这可能听起来反直觉,但你一般可以从本身不是对象的值中获取属性/方法:

```js
fave = 42;

fave;              // 42
fave.toString();   // "42"
```

在这里,`fave`拿着原始的`42`数字值。 那怎么办呢?`.toString`进入它的财产,然后`()`以行使该财产中的职能?

这是一个非常深入的话题, 而不是我们在本书中将进入的话题; 参看本系列第4册, “语言和语法”, 更多。 然而,作为短暂的一瞥:如果你执行一种财产访问(例如:`.`或`[ .. ]`针对非标的、非标的值,JS将默认(暂时!)将标的值强迫成一个被包的标的值,允许该标的物对隐含的即时标的物进行财产访问。

这个过程一般称为"boxing",如在"box"(对象容器)内放置一个值.

因此,在以上片段,只是暂时`.toString`正在进入`42`JS 将这个值输入`Number`对象,然后执行属性访问。

请注意:`null`和`undefined`可以通过调用`Object(null)` / `Object(undefined)`然而,联署材料并没有自动收录这些无效的值,因此针对他们的财产访问会失败(如前面"有条件财产访问"部分所讨论的).

| 注: |
| :--- |
| 拳击有对应: 解拳. 例如,JS引擎将采用一个对象包装器 -- -- 如`Number`围绕对象`42`-- 创建于`Number(42)`或`Object(42)`拆开它 取回基础原始`42`,每当数学操作时(例如`*`或`-`)遇到这样的物体。 解除拳击行为已经超出我们讨论的范围, |

## 指定属性

无论是在对象字面定义时对财产进行定义,还是后来添加,财产价值的转让都与`=`运营商,如同任何其他正常任务一样:

```js
myObj.favoriteNumber = 123;
```

如果`favoriteNumber`属性不存在,该语句将创建该名称的新属性并指定其值。 但如果已经存在,该声明将重新分配其价值。

| 警报: |
| :--- |
| 一个`=`对财产的转让可能失败(沉默或放弃例外),或者它可能不会直接转让该价值,而是引用执行某种操作的*ster*函数。 在下一章中更详细地介绍这些行为. |

也可以同时指定一个或多个属性 -- -- 假设源属性(名称和值对)位于另一个对象 -- -- 使用`Object.assign(..)`(加入ES6)方法:

```js
// shallow copy all (owned and enumerable) properties
// from `myObj` into `anotherObj`
Object.assign(anotherObj,myObj);

Object.assign(
    /*target=*/anotherObj,
    /*source1=*/{
        someProp: "some value",
        anotherProp: 1001,
    },
    /*source2=*/{
        yetAnotherProp: false
    }
);
```

`Object.assign(..)`将第一个对象作为目标,将第二个(以及可选择的随后)对象作为源。 复制方式与前面"Object Spanding"部分所述方式相同.

## 删除属性

一旦在物体上定义了财产,唯一可以将其移除的方法就是与`delete`运算符:

```js
anotherObj = {
    counter: 123
};

anotherObj.counter;   // 123

delete anotherObj.counter;

anotherObj.counter;   // undefined
```

与常见的误解相反,联合来文`delete`操作员通过垃圾收集(GC)直接进行任何交易定位/解除内存. 它唯一要做的就是从物体中移除一个财产. 如果财产中的价值是参考物(指另一个物体/etc),而且一旦财产被移走,没有其他未亡价值的参考物,那么该价值就有可能在今后对GC的扫荡中被移走。

通话`delete`除物体财产外的任何东西都是滥用`delete`运算符,或静默失败(非限制性模式),或丢弃例外(严格模式)。

从对象中删除一个财产不同于给它分配一个值,例如`undefined`或`null`转让的财产`undefined`最初或以后,物体上仍然存在,在列举内容时仍可能显示

## 确定集装箱内容

您可以以多种方式确定对象的内容. 要询问对象是否有特定属性:

```js
myObj = {
    favoriteNumber: 42,
    coolFact: "the first person convicted of speeding was going 8 mph",
    beardLength: undefined,
    nicknames: [ "getify", "ydkjs" ]
};

"favoriteNumber" in myObj;            // true

myObj.hasOwnProperty("coolFact");     // true
myObj.hasOwnProperty("beardLength");  // true

myObj.nicknames = undefined;
myObj.hasOwnProperty("nicknames");    // true

delete myObj.nicknames;
myObj.hasOwnProperty("nicknames");    // false
```

* 如何改变*`in`运算符和`hasOwnProperty(..)`方法行为。 那个`in`操作员将不仅检查指定的目标对象,如果找不到,还将查看对象的`[[Prototype]]`链条(涵盖在下一章)。 相比之下,`hasOwnProperty(..)`只查阅目标对象。

如果你仔细观察, 你可能已经注意到`myObj`似乎有一个方法属性叫做`hasOwnProperty(..)`尽管我们没有给它下定义 那是因为`hasOwnProperty(..)`定义为内置`Object.prototype`,默认情况下,所有普通对象“由”继承。 然而,使用这种“固有”方法存在固有的风险。 在下一章中,更多关于原型。

### 更好的存在检查

ES2022(在撰写本报告时几乎是官方的)已经确定了一个新的特征,`Object.hasOwn(..)`。它基本上与`hasOwnProperty(..)`,但它被引用为对象值以外的静态帮助器,而不是通过对象的`[[Prototype]]`,使其在使用上更加安全和一致:

```js
// instead of:
myObj.hasOwnProperty("favoriteNumber")

// we should now prefer:
Object.hasOwn(myObj,"favoriteNumber")
```

尽管(在写作之时)这个功能刚刚在JS中出现, 但有些多填充器使得这个API在您的节目中可以使用, 例如,快速立体多填图:

```js
// simple polyfill sketch for `Object.hasOwn(..)`
if (!Object.hasOwn) {
    Object.hasOwn = function hasOwn(obj,propName) {
        return Object.prototype.hasOwnProperty.call(obj,propName);
    };
}
```

在您的程序中包含这样的多填补丁意味着您可以安全开始使用`Object.hasOwn(..)`不论JS环境是否存在,`Object.hasOwn(..)`建在还没有或没有。

### 列出所有集装箱内容

我们已经讨论过了`Object.entries(..)`API早期,它告诉我们一个物体的属性是什么(只要它们可以被假定——在下一章中更多).

还有其他各种机制可供使用。`Object.keys(..)`提供对象中可假设的财产名称(aka, key)列表 -- -- 名称只,没有值;`Object.values(..)`相反,我们给出了所有持有的可假设属性值的清单。

但是,如果我们想得到 * 所有 * 在一个物体(是否可以假设)中的钥匙呢?`Object.getOwnPropertyNames(..)`似乎做我们想要的, 因为它就像`Object.keys(..)`但也返回不可假定的财产名称。 然而,该清单** 不包括任何符号属性名称,因为这些名称被视为物体上的特殊位置。`Object.getOwnPropertySymbols(..)`返回对象的所有符号属性。 所以,如果你把这些列表合并在一起, 你会拥有一个物体的所有直接(* 拥有*)内容。

然而,正如我们已经暗示过多次, 并将在下一章中详细叙述, 一个物体也可以从它的"继承"内容`[[Prototype]]`链条。 这些不被认为是*拥有*的内容,所以不会出现在任何这些列表中.

回顾`in`运营商可能会穿越整个链条寻找财产的存在. 同样,a`for..in`循环将穿越链条,列出任何可被假定(拥有或继承)的财产。 但并没有内置的API可以穿越整个链条,并返回一个组合的列表,其中既包含*所有*内容,也包含*继承*内容.

## 临时集装箱

使用一个容器来持有多个值有时只是一个临时的传输机制,例如当您想要通过单个参数将多个值传递给某个函数时,或者当您想要一个函数返回多个值时:

```js
function formatValues({ one, two, three }) {
    // the actual object passed in as an
    // argument is not accessible, since
    // we destructured it into three
    // separate variables

    one = one.toUpperCase();
    two = `--${two}--`;
    three = three.substring(0,5);

    // this object is only to transport
    // all three values in a single
    // return statement
    return { one, two, three };
}

// destructuring the return value from
// the function, because that returned
// object is just a temporary container
// to transport us multiple values
const { one, two, three } =

    // this object argument is a temporary
    // transport for multiple input values
    formatValues({
       one: "Kyle",
       two: "Simpson",
       three: "getify"
    });

one;     // "KYLE"
two;     // "--Simpson--"
three;   // "getif"
```

对象字形传入`formatValues(..)`立即解构参数,所以在函数内我们只处理三个独立的变量(`one`, `two`,以及`three`) (中文(简体) ). 对象文字`return`ed从函数也立即被解析,所以我们再次只处理三个不同的变量(`one`, `two`, `three`).

这种片段说明物体有时只是临时运输容器,而不是本身有意义的价值。

## 容器收藏属性

物体最常见的用途是作为多个值的容器. 我们通过以下方式创建和管理财产容器对象:

* 定义属性( 命名位置), 无论是在对象创建时间还是以后
* 指定值, 无论是在对象创建时间还是以后
* 稍后访问值,使用位置名称(财产名称)
* 删除属性通过`delete`
* 确定集装箱内装物`in`, `hasOwnProperty(..)` / `hasOwn(..)`, `Object.entries(..)` / `Object.keys(..)`页:1

但是,对于物体来说,不只是静止地收集财产名称和价值。 在接下来的一章中,我们将潜入引擎盖下,看看它们是如何实际工作的.

[^structuredClone]: "硬化克隆体算法",HTML规格;https://html.spec.whatwg.org/multipage/structured-data.html#structured-cloning; 2022年7月访问
