# 你不了解JS然而:类型和语法 - 第二版
# 第2章:首要行为

| 注: |
| :--- |
| 进行中的工作 |

迄今为止,我们在JS中探索了七种内置的原始价值类型:`null`, `undefined`, `boolean`, `string`, `number`, `bigint`,以及`symbol`.

第一章是相当多需要接受的, 比大多数读者预期的要多得多。 如果你读完这些之后还在呼吸 别担心在这继续之前先休息一会

一旦你清楚了方向,准备好继续前进, 让我们来研究一下 价值类型所隐含的某些行为。 我们将仔细仔细地审视所有这些各种行为。

## 初级不可用性

所有原始的价值观都是不可改变的,这意味着在JS程序中没有任何东西能够触及到价值观的内容,并且以任何方式修改它.

```js
myAge = 42;

// later:

myAge = 43;
```

那个`myAge = 43`语句不会改变值。 它重新指定了不同的值`43`改为:`myAge`,完全替换`42`.

新的值也是通过各种操作创建的,但这些也并不修改原始值:

```js
42 + 1;             // 43

"Hello" + "!";      // "Hello!"
```

价值`43`和`"Hello!"`是新的,与上一个不同的价值`42`和`"Hello"`数值。

甚至一个字符串值,它看起来只是一系列字符 -- -- 和数组内容通常是可变的 -- -- 是不可改变的:

```js
greeting = "Hello.";

greeting[5] = "!";

console.log(greeting);      // Hello.
```

| 警报: |
| :--- |
| 在非限制性模式下,指定只读属性(如`greeting[5] = ..`)默然失败. 在严格模式中,不允许的转让将产生例外。 |

原始值的性质是不可改变的,绝不因如何宣布持有该值的变量或对象属性而影响。 例如,是否`const`, `let`,或`var`用于宣布`greeting`变量,它所持有的字符串值是不可改变的。

`const`不创建不可变值, 它宣布不能重分配的变量( aka, 不可变任务) —— 请参看此系列的“ 范围与关闭” 标题以获取更多信息。

对象上的属性可标记为只读 -- -- 带有`writable: false`描述属性,如本系列"Objects & Classes"标题中所讨论的. 但这仍然不影响价值的性质,仅影响防止财产的重新分配。

### 有属性的原始人?

此外,属性 * 不能 * 添加到任何原始值中:

```js
greeting = "Hello.";

greeting.isRendered = true;

greeting.isRendered;        // undefined
```

这个片段看起来像是在增加一个属性`isRendered`数值`greeting`,但是这个任务却默默的失败(即使是严格模式).

绝对不允许以无效原始价值获取财产`null`和`undefined`。但是属性 * 可以在所有其他原始值上访问 - 是的,这听起来反直观。

例如,所有字符串值都有一个只读`length`属性:

```js
greeting = "Hello.";

greeting.length;            // 6
```

`length`不能设定,但可以是访问,它暴露了存储在值中的代码单位数量(参见第一章中的"JS字符编码"),这往往意味着字符串中的字符数量.

| 注: |
| :--- |
| 有点 对于大多数标准字符来说,这是真的;一个字符是一个代码点,是一个代码单位. 然而,如第一章所述,在代码点上方扩展的Unicode字符`65535`将存储为两个编码单元(代用半数)。 因此,对于每一个这样的特征,`length`将包括`2`尽管人物的视觉印记是一个符号。 |

非nullish原始值也有一些标准的内置方法可以访问:

```js
greeting = "Hello.";

greeting.toString();    // "Hello." <-- redundant
greeting.valueOf();     // "Hello."
```

此外,大多数原始价值类型用该类型固有的特定行为来定义自己的方法. 我们稍后在本章讨论这些。

| 注: |
| :--- |
| 正如第1章中已经简要提到的那样,从技术上讲,这种原始价值上的财产/方法的获取是通过一种被称为“自动拳*”的隐含胁迫行为得到便利的。 我们将在第三章的"自动物体"中详细论述这一点. |

## 初步转让

从一个变量/容器到另一个变量/容器的任何原始值分配都是*值复制件*:

```js
myAge = 42;

yourAge = myAge;        // assigned by value-copy

myAge;                  // 42
yourAge;                // 42
```

给`myAge`和`yourAge`每个变量都有自己的数字值副本`42`.

| 注: |
| :--- |
| 在JS引擎内部,可能只有一个`42`值存在于内存中,引擎同时点出`myAge`和`yourAge`变量。 由于原始价值是不可改变的,因此JS引擎这样做没有危险. 但是作为JS的开发者 对我们很重要的是 在我们的程序里`myAge`和`yourAge`假装他们有他们自己的复制品 而不是分享它。 |

如果以后我们改派`myAge`改为:`43`(当我有一个生日) 它不影响`42`仍然被分配给`yourAge`:

```js
myAge++;            // sort of like: myAge = myAge + 1

myAge;              // 43
yourAge;            // 42 <-- unchanged
```

## 字符串行为

字符串值具有许多特定的行为,每个JS开发者都应该意识到.

### 字符串访问

虽然字符串实际上不是数组,但JS允许`[ .. ]`数字字符的数组式访问( E)`0`- 基于)指数:

```js
greeting = "Hello!";

greeting[4];            // "o"
```

如果在`[ .. ]`如果可能的话,数值会被隐含地胁迫到它的全部/整数代表上。

```js
greeting["4"];          // "o"
```

如果值/表达式在整数范围外解决`0` - `length - 1`(或`NaN`),或者如果它不是一个`number`值类型,访问将被作为带有字符串等效属性名称的属性访问。 如果财产进入失败,结果是`undefined`.

| 注: |
| :--- |
|  以后在书中深入报道胁迫. |

### 字符迭代

字符串不是数组,但它们肯定在许多方面紧密模仿了数组. 其中一种行为是,像数组一样,弦是可移动的. 这意味着字符串的字符(code-units)可以被单个地重复:

```js
myName = "Kyle";

for (let char of myName) {
    console.log(char);
}
// K
// y
// l
// e

chars = [ ...myName ];
chars;
// [ "K", "y", "l", "e" ]
```

数值,如字符串和数组,是可移动的(通过`...`, `for..of`,以及`Array.from(..)`),如果在特殊符号属性位置暴露出一种传动器生成方法.`Symbol.iterator`(见第一章中的"众所周知的符号"):

```js
myName = "Kyle";
it = myName[Symbol.iterator]();

it.next();      // { value: "K", done: false }
it.next();      // { value: "y", done: false }
it.next();      // { value: "l", done: false }
it.next();      // { value: "e", done: false }
it.next();      // { value: undefined, done: true }
```

| 注: |
| :--- |
| 执行协议的具体内容,包括:`{ value: "e" .. }`结果仍然显示`done: false`,在本系列的"Sync & Async"标题中详细述及. |

### 长度计算

如第一章所述,字符串值具有`length`属性会自动显示字符串的长度; 此属性只能访问; 设置的尝试会被默默忽略。

报告`length`值在某种程度上与字符串中的字符数(实际上,是代码单位)相对应,但正如我们在第一章中看到的那样,当Unicode字符被卷入时,它更为复杂.

多数人视线区分符号为单独的字符;这种独立视觉符号的概念被称为*grapheme*,或*grapheme cluster*. 所以,在计算一个字符串的“长度”时, 我们通常是指我们在计算石墨的数量。

但电脑不是这么处理人物的

在联署材料中,每个*特征*是一个代码单元(16位),代码点值在或低于`65535`编辑`length`字符串属性总是计算字符串值中的代码单位数,而不是代码点。 代码单元可能单独代表单个字符,也可能是代对的一部分,也可能与相邻的*组合*符号结合,或石墨集合的一部分. 因此,`length`与计算视觉人物/图形的典型概念不符。

要更接近字符串的预期/直观 *grapheme 长度 *,首先需要将字符串值与`normalize("NFC")`(见第1章中的"Normalizing Unicode"),用于生产任何*编组的*编组的代码单位(如有可能),以防任何字符原来存储*编组的*编组为单独的代码单位.

例如:

```js
favoriteItem = "teléfono";
favoriteItem.length;            // 9 -- uh oh!

favoriteItem = favoriteItem.normalize("NFC");
favoriteItem.length;            // 8 -- phew!
```

不幸的是,正如我们在第一章中所看到的那样, 我们还有更大的代码字符的可能性。`65535`,因此需要一对代孕夫妇作为代表。 这类字符在`length`:

```js
// "☎" === "\u260E"
oldTelephone = "☎";
oldTelephone.length;            // 1

// "📱" === "\u{1F4F1}" === "\uD83D\uDCF1"
cellphone = "📱";
cellphone.length;               // 2 -- oops!
```

那怎么办?

一个固定是使用字符迭代(通过`...`操作员),如我们在上一节所见,因为它从一个代对中自动返回每个合并字符:

```js
cellphone = "📱";
cellphone.length;               // 2 -- oops!
[ ...cellphone ].length;        // 1 -- phew!
```

但不幸的是,石墨星团(如第1章所解释的)将另一个扳手投入字符串的长度计算. 例如,如果我们把拇指 下来的emoji(`"\u{1F44E}"`并增加中黑皮肤的皮肤调理剂。`"\u{1F3FE}"`),我们得到:

```js
// "👎🏾" = "\u{1F44E}\u{1F3FE}"
thumbsDown = "👎🏾";

thumbsDown.length;              // 4 -- oops!
[ ...thumbsDown ].length;       // 2 -- oops!
```

如你所见,这是两个截然不同的代码点(不是代词对),由于它们的顺序和附着性,导致计算机的Unicode渲染画出拇指下行符号,但皮肤色调比默认更暗. 因此计算字符串长度`2`.

需要复制一个平台的大部分复杂的Unicode渲染逻辑,才能识别这种集群的代码点,作为用于长度计数的单一"特征". 有一些图书馆声称这样做,但是它们不一定是完美的,从额外代码的角度来说,它们成本很高.

| 注: |
| :--- |
| 作为一个推特用户,你可能期望能够将280个拇指下调的emojis放入一个单一的推文,因为它看起来像一个单一的角色. 推特数`"👎"`(默认拇指下移),`"👎🏾"`(中深皮肤大拇指向下)`"👩‍👩‍👦‍👦"`(Family emoji phrameme cluster) 全部为每个字符2个字符,尽管各自的字符串长度(从JS的角度)是:`2`, `4`,以及`7`因此,你只能在推特上将爱莫吉人(140人而不是280人)的一半合为一体。 事实上,Twitter在2018年实施了这一修改,专门将所有Unicode字符的计数定级,每个符号为2个字符.[^TwitterUnicode]对于Twitter用户来说, 特别是那些想使用最能代表性别、皮质等的emoji字符的人, 推特选择将所有Unicode/emoji符号分别计为2个字符, |

计算弦的*长度* 来匹配人类的直觉,是一项非常艰巨的任务,也许更多的是艺术而不是科学。 在许多情况下,我们可以得到可以接受的近似值, 但有很多其他情况 可能会混淆我们的方案。

### 国际化(i18n)和本地化(l10n)

为了满足JS方案在任何国际语言/文化背景下不断增长的预期运作需求,ECMAScript委员会还出版了ECMAScript Internationalization API。[^INTLAPI]

JS程序根据运行程序的环境(web浏览器页面,节点实例等)默认为地方/语言. 无效的方位会影响排序(和值比较),格式化,以及其它几个假设的行为. 这样的改变行为也许用弦比较明显,

但字符串也可以在其中嵌入语言/地方信息,这优先于环境默认. 如果字符串在语言/地方(例如:`"a"`),使用默认环境设置。

根据字符串的内容,它可能被解释为从左到右(LTR)或从右到左(RTL)命令. 因此,我们以后会覆盖的许多字符串方法在其名字中使用逻辑描述符,如"开始","结束","开始","结束",以及"最后",而不是"左","右"等方向性术语.

例如,希伯来语和阿拉伯语都是常见的RTL语言:

```js
hebrewHello = "\u{5e9}\u{5dc}\u{5d5}\u{5dd}";

console.log(hebrewHello);                       // שלום
```

注意字符串文字中第一个列出的字符 (`"\u{5e9}"`当字符串被渲染时, 它实际上是最右侧的字符?

尽管希伯来语是一种RTL语言,但实际上你并没有用倒转(RTL)顺序键入字符串文字中的字符,以显示其表达方式. 您按逻辑顺序输入字符, 位置`0`是第一个字符,位置`1`是第二个字符等。 渲染层是RTL字符被反转以正确顺序显示的地方.

这也意味着如果你进入`hebrewHello[0]`(或`hebrewHello.charAt(0)`) - 将字符作为位置`0`- 你得到一个`"ש"`因为从逻辑上讲,这是弦的第一个字符,而不是`"ם"`(逻辑上是字符串的最后一个字符). 索引位置访问遵循的是逻辑位置,而不是已实现位置。

下面是另一种RTL语言的同样例子,阿拉伯语:

```js
arabicHello = "\u{631}\u{62d}\u{628}\u{627}";

console.log(arabicHello);                       // رحبا

console.log(arabicHello[0]);                    // ر
```

JS程序可以强制使用特定语言/语言,使用多种语言。`Intl`API 类`Intl.Collator`: [^INTLCollator]

```js
germanStringSorter = new Intl.Collator("de");

listOfGermanWords = [ /* .. */ ];

germanStringSorter.compare("Hallo","Welt");
// -1 (or negative number)

// examples adapted from MDN:
//
germanStringSorter.compare("Z","z");
// 1 (or positive number)

caseFirstSorter = new Intl.Collator("de",{ caseFirst: "upper", });
caseFirstSorter.compare("Z","z");
// -1 (or negative number)
```

多字串可以使用`Intl.Segmenter`: [^INTLSegmenter]

```js
arabicHelloWorld = "\u{645}\u{631}\u{62d}\u{628}\u{627} \
\u{628}\u{627}\u{644}\u{639}\u{627}\u{644}\u{645}";

console.log(arabicHelloWorld);      // مرحبا بالعالم

arabicSegmenter = new Intl.Segmenter("ar",{ granularity: "word" });

for (
    let { segment: word, isWordLike } of
    arabicSegmenter.segment(arabicHelloWorld)
) {
    if (isWordLike) {
        console.log(word);
    }
}
// مرحبا
//لعالم
```

| 注: |
| :--- |
| 那个`segment(..)`方法(来自`Intl.Segmenter`返回标准JS执行器,`for..of`循环在这里消耗。 更多关于本系列标题"Sync & Async"中的迭代协议. |

### 字符串比较

字符串值可以与其他字符串值(无论是平等还是关系排序)进行比较,使用各种内置运算符. 必须记住,这种比较对实际的字符串内容很敏感,尤其包括来自非BPM Unicode字符的基本代码点.

对大写和小写有明确定义的任何字符,平等与关系比较都是对大小写敏感的. 为了进行对大小写不敏感的比较,首先使两个值的外壳正常化(与`toUpperCase()`或`toLowerCase()`).

#### 字符串平等

那个`===`和`==`操作者(与其否定的对等者)`!==`和`!=`)是最常用的对原始值进行平等比较的方法,包括字符串值:

```js
"my name" === "my n\x61me";               // true

"my name" !== String.raw`my n\x61me`;     // true
```

那个`===`运算符[^StrictEquality]- 通常被称为"严格平等" - 首先检查类型是否匹配,如果不匹配,返回`false`马上 如果类型匹配,则它会检查这些值是否相同;对于字符串,这是从开始到结束的每个代码单位的比较.

尽管有"严格"的命名, 仍然有细微的`===`(例如:`-0`和`NaN`处理),但我们稍后会覆盖这些。

##### 强制平等

相比之下,`==`运算符[^LooseEquality]——常被称为"loose equality"——履行 *强制平等*:如果两个操作的值类型不匹配,`==`首先强迫一个或两个操作,直到值类型 * do * 匹配为止,然后在内部将比较交给`===`.

胁迫是一个极其重要的议题——这是JS类型系统的一个固有部分,是语言的3个支柱之一——但我们只在此简单介绍一下本章,稍后再详细回顾一遍.

| 注: |
| :--- |
| 你可能听到 引用的,但是不准确的解释`==`和`===`这是`==`比较值时`==`比较数值和类型。 不是真的,你可以自己读到这个谱子来验证 -- 两个`isStrictlyEqual(..)`和`isLooselyEqual(..)`规格算法作为脚注在前几段中链接。 但总结如下:`==`和`===`了解并敏感地了解操作的类型。 如果操作类型相同,两个操作符都会做完全相同的事情;如果类型不同,`==`强制强迫直到类型匹配,而`===`返回时`false`马上 |

开发商非常普遍地断言`==`操作员很困惑,而且很难不感到意外地使用(因此,几乎普遍倾向于`===`) (中文(简体) ). 我认为这完全是假的, 事实上,JS开发者应该违约`==`(和避免)`===`(如果可能的话) 但是,我们需要进行更多的讨论,支持这种有争议的发言;坚持你的反对意见,直到我们稍后再讨论。

目前,要获得一些直觉 关于强制性质`==`最有启发性的观察是 如果类型不匹配`==`*偏好 *数字比较。 这意味着它将尝试将两个操作都转换为数字,然后进行平等检查(与`===`).

因此,就我们目前的讨论而言,如果两个操作都已经是字符串,实际的字符串平等就可以被检查:

```js
// actual string equality check (via === internally):
"42" == "42";           // true
```

`==`不真正执行字符串平等检查。 如果操作值类型 既是弦,`==`把手拿开比较`===`如果它们不是两边的 强制步骤`==`将减少比对数字而不是字符串:

```js
// numeric (not string!) equality check:
42 == "42";             // true
```

后此章论数平等.

##### *真正的* 严格平等

况乎.`==`和`===`,《联合提交的材料》提供了:`Object.is(..)`工具,该工具返回`true`如果两个论点*完全相同*,`false`其他(无例外或细微差别):

```js
Object.is("42",42);             // false

Object.is("42","\x34\x32");     // true
```

从`===`添加a`=`转到`==`为了让行为更加严格, 我有点半笑话说`Object.is(..)`工具就像一个`====`(单位:千美元)`=`接线员,真正的严格 - 不例外 那种平等检查!

这么说`===`(和`==`其内部代表团`===`当比较两个实际已经存在的字符串值时,这些数值是“完全可以预测的”,没有奇怪的例外。 我强烈建议使用`==`(或`===`)和储备金`Object.is(..)`角大小写(数字)。

#### 字符串关系比较

In addition to equality checks between strings, JS supports relational comparisons between primitive values, like strings: `<`, `<=`, `>`, and `>=`.

The `<` (less-than) and `>` (greater-than) operations compare two string values lexicographically -- like you would sort words in a dictionary -- and should thus be fairly self explanatory:

```js
"hello" < "world";          // true
```

| 注: |
| :--- |
| 如前所述,运行中的JS程序有一个默认的区划,这些运营商根据该区划进行比较. |

Like `==`, the `<` and `>` operators are numerically coercive. Any non-number values are coerced to numbers. So the only way to do a relational comparison with strings is to ensure both operands are already string values.

Perhaps somewhat surprisingly, the `<` and `>` have no strict-comparison equivalent, the way `===` avoids the coercion of `==`. These operators are always coercive (when the types don't match), and there's no way in JS to avoid that.

当两个值都是“数字外观”字符串时会发生什么?

```js
"100" < "11";               // true
```

当然,在数字上`100`应不小于`11`.

但两个字符串之间的关系比较使用词典顺序. 所以第二个`"0"`字符( 在`"100"`)小于第二个`"1"`(单位:千美元)`"11"`因此,`"100"`将按“字典”排序`"11"`。关系运算符只有在操作类型已经不是字符串时才强制数字。

The `<=` (less-than-or-equal) and `>=` (greater-than-or-equal) operators are effectively a shorthand for a compound check.

```js
"hello" <= "hello";                             // true
("hello" < "hello") || ("hello" == "hello");    // true

"hello" >= "hello";                             // true
("hello" > "hello") || ("hello" == "hello");    // true
```

| 注: |
| :--- |
| Here's an interesting bit of specification nuance: JS doesn't actually define the underlying greater-than (for `>`) or greater-than-or-equal (for `>=`) operations. Instead, it defines them by reversing the arguments to their *less-than* complement counterparts. So `x > y` is treated by JS essentially as `y <= x`, and `x >= y` is treated by JS essentially as `y < x`. So JS only needs to specify how `<` and `==` work, and thus gets `>` and `>=` for free! |

##### 语言学软件关系比较

正如我刚才提到的,关系操作员 承担和使用当前无效果的地方。 然而,有时为了比较而强制一个特定的地块(比如在排序字符串列表时)可能是有用的.

联合来文提供的方法`localCompare(..)`为此,联合工作组:

```js
"hello".localeCompare("world");
// -1 (or negative number)

"world".localeCompare("hello","en");
// 1 (or positive number)

"hello".localeCompare("hello","en",{ ignorePunctuation: true });
// 0

// examples from MDN:
//
// in German, ä sorts before z
"ä".localeCompare("z","de");
// -1 (or negative number) // a negative value

// in Swedish, ä sorts after z
"ä".localeCompare("z","sv");
// 1 (or positive number)
```

可选的第二和第三个论点`localeCompare(..)`通过`Intl.Collator`API 密码[^INTLCollatorApi]如前所述。

你可能会用`localeCompare(..)`排序字符串阵列时:

```js
studentNames = [
    "Lisa",
    "Kyle",
    "Jason"
];

// Array::sort() mutates the array in place
studentNames.sort(function alphabetizeNames(name1,name2){
    return name1.localeCompare(name2);
});

studentNames;
// [ "Jason", "Kyle", "Lisa" ]
```

但是,如前所述,一个更直接的方法(在排序许多字符串时稍多表现者)正在使用`Intl.Collator`直接:

```js
studentNames = [
    "Lisa",
    "Kyle",
    "Jason"
];

nameSorter = new Intl.Collator("en");

// Array::sort() mutates the array in place
studentNames.sort(nameSorter.compare);

studentNames;
// [ "Jason", "Kyle", "Lisa" ]
```

### 字符串连接

两个或两个以上的字符串值可以缩合(合并)成一个新的字符串值,使用`+`运算符:

```js
greeting = "Hello, " + "Kyle!";

greeting;               // Hello, Kyle!
```

那个`+`如果两个操作符( 操作符左侧或右侧的值) 中任何一个已经是一个字符串( 甚至是空字符串), 操作符将充当字符串连接符`""`).

如果一个操作是字符串,而另一个则不是,则一个不是字符串的操作会被胁迫到它的字符串代表,以达到调和的目的:

```js
userCount = 7;

status = "There are " + userCount + " users online";

status;         // There are 7 users online
```

此类的字符串集合本质上是将数据插入字符串,这是模板字词的主要目的(见第1章). 因此,以下代码将具有相同的结果,但一般认为是比较可取的方法:

```js
userCount = 7;

status = `There are ${userCount} users online`;

status;         // There are 7 users online
```

字符串连接的其他选项包括:`"one".concat("two","three")`和`[ "one", "two", "three" ].join("")`,但这类方法只有在连接的字符串数量取决于运行时间条件/计算时才更可取. 如果字符串有固定/已知的一组内容,如上,模板字元是更好的选项.

### 字符串值方法

字符串值提供了一组额外的字符串特定方法(作为属性):

* `charAt(..)`: 在数值索引中生成一个新的字符串值,类似于`[ .. ]`; 不同之处`[ .. ]`,结果总是字符串,无论是位置上的字符`0`(如果索引范围以外的有效数字),或空字符串`""`(如果缺少/无效指数)

* `at(..)`类似`charAt(..)`,但负指数从字符串的末尾倒数

* `charCodeAt(..)`: 在指定的索引中返回数字编码单位(见第1章中的“JS字符编码”)

* `codePointAt(..)`: 返回从指定索引开始的整个代码点; 如果在那里找到一个替代对, 则返回整个字符( 代码点) s

* `substr(..)` / `substring(..)` / `slice(..)`: 生成一个新字符串值, 代表来自原字符串的字符范围; 这些值在如何指定或确定区域的起始/结束索引方面有所不同

* `toUpperCase()`: 生成所有大写字符的新字符串值

* `toLowerCase()`: 生成所有小写字符的新字符串值

* `toLocaleUpperCase()` / `toLocaleLowerCase()`: 大写或小写操作时使用区域映射

* `concat(..)`: 生成一个新的字符串值, 即原始字符串和所有通过字符串值参数的调和

* `indexOf(..)`: 搜索原始字符串中的字符串值参数,可选择从第二个参数中指定的位置开始;返回`0`- 如果找到基于索引的位置,或`-1`如果找不到的话

* `lastIndexOf(..)`: 喜欢`indexOf(..)`但是,从字符串的末尾(在 LTR 本地语区中右, 在 RTL 本地语区中左)

* `includes(..)`:类似于`indexOf(..)`但返回布尔结果

* `search(..)`:类似于`indexOf(..)`但有指定的正则表达式匹配

* `trimStart()` / `trimEnd()` / `trim()`: 从字符串的起始( 左在 LTR 本地语区, 右在 RTL 本地语区), 或 字符串的结束( 右在 LTR 本地语区, 左在 RTL 本地语区), 或两者兼有, 生成一个新的字符串值

* `repeat(..)`: 生成一个带有原字符串值的新字符串,重复指定次数

* `split(..)`: 在指定的字符串或正则表达式边界下生成一系列字符串值

* `padStart(..)` / `padEnd(..)`: 生成一个带有粘贴符的新字符串值( 默认 " 白空间, 但可以被覆盖), 用于启动( 左在 LTR 本地语区, 右在 RTL 本地语区) 或 末端( 右在 LTR 本地语区), 从而最终字符串结果至少是指定长度。

* `startsWith(..)` / `endsWith(..)`: 为字符串值参数检查原始字符串的起始( LTR locates 左, RTL locates 右) 或结束( LTR locates 右); 返回布尔结果

* `match(..)` / `matchAll(..)`: 返回类似数组的正则表达式匹配结果与原始字符串

* `replace(..)`: 返回新字符串, 替换原字符串中指定的正则表达式匹配的一个或多个匹配事件

* `normalize(..)`: 在内容上执行过Unicode正常化的新字符串(见第1章中的“Unicode正常化”)

* `localCompare(..)`: 函数,根据当前区域比较两个字符串(用于排序);返回负数(通常是)`-1`但是不能保证),如果原始字符串值在参数字符串值的词典之前出现,则正数(通常是`1`如果原始字符串值在逻辑学上出现在参数字符串值之后,则不能保证)`0`如果两个字符串相同

* `anchor()`, `big()`, `blink()`, `bold()`, `fixed()`, `fontcolor()`, `fontsize()`, `italics()`, `link()`, `small()`, `strike()`, `sub()`,以及`sup()`: 历史上, 这些在生成 HTML 字符串片段中是有用的; 现在它们已贬值, 应该避免

| 警报: |
| :--- |
| 上述许多方法都依赖立场指数。 如"Length计算"部分前所述,这些位置取决于字符串值的内部内容,这意味着如果一个扩展的Unicode字符存在并占用两个代码单元槽,那么将算作两个索引位置而不是一个. 未能说明*解密*代码单位,代对,和石墨隐蔽器是JS字符串处理中常见的bug源. |

这些字符串方法都可以直接在字面值上调用,或者在持有字符串值的变量/财产上调用. 如果适用,它们会产生一个新的字符串值,而不是修改现有的字符串值(因为字符串是不可改变的):

```js
"all these letters".toUpperCase();      // ALL THESE LETTERS

greeting = "Hello!";
greeting.repeat(2);                     // Hello!Hello!
greeting;                               // Hello!
```

### 静态`String`帮助者

以下字符串工具函数直接在以下`String`对象,而不是单个字符串值的方法:

* `String.fromCharCode(..)` / `String.fromCodePoint(..)`: 从一个或多个参数中生成一个字符串,代表代码单元(`fromCharCode(..)`)或整个代码点(`fromCodePoint(..)`)

* `String.raw(..)`: 一个默认的模板标签功能,允许在模板字面上插插,但防止字符逃逸序列被解析,所以它们留在其 *raw * 单个输入字符中

此外,大多数价值(特别是原始价值)可以通过将价值传递给`String(..)`函数( 无)`new`关键词). 例如:

```js
String(true);           // "true"
String(42);             // "42"
String(Infinity);       // "Infinity"
String(undefined);      // "undefined"
```

我们将在后面的一章中更详细地叙述这类胁迫行为。

## 数量行为

数字用于我们程序中的各种任务,但大多用于数学计算. 密切关注联署材料数量的表现,确保结果如预期。

### 浮点不精确

我们需要从第一章中重新讨论IEE-754。

任何IEEE-754数字系统在任何编程语言中的经典特征之一 -- -- Not UNIQUELY JS! -- 并不是所有的操作和值都能够完美地融入IEEE-754表示。

最常见的例子是:

```js
point3a = 0.1 + 0.2;
point3b = 0.3;

point3a;                        // 0.30000000000000004
point3b;                        // 0.3

point3a === point3b;            // false <-- oops!
```

行动`0.1 + 0.2`最终产生浮点错误( drift), 存储的值实际上是`0.30000000000000004`.

相应的比特表示是:

```
// 0.30000000000000004
00111111110100110011001100110011
00110011001100110011001100110100

// 0.3
00111111110100110011001100110011
00110011001100110011001100110011
```

如果你仔细观察这些位图案, 只有最后两个位不同,从`00`改为:`11`但这两个数字就足够不平等了!

同样,为了强化这一点:这种行为无论如何都是JS独有的。 任何符合IEEE-754的编程语言都正是这样在同一个情景下运作的. 正如我在上文所断言的,大多数编程语言都使用IEEE-754,因此它们都将遭受同样的命运.

取笑JS的诱惑`0.1 + 0.2 !== 0.3`我知道很强壮 但这里完全是假的

| 注: |
| :--- |
| 几乎所有的程序员都需要意识到IEEE-754,并确保他们小心这些类型的gotchas. 有点令人惊奇,令人失望的是,很少有人知道IEEE-754是如何工作的. 如果你把时间 阅读和理解这些概念到目前为止, 你现在在 罕见的微小百分比 谁真正投入 努力 理解他们程序的数字! |

#### 埃普西隆阈值

围绕这种浮点不精度工作的一个共同建议 使用这个 "非常小"`number`联合来文界定的价值:

```js
Number.EPSILON;                 // 2.220446049250313e-16
```

* Epsilon* 是JS能够代表的最小差别`1`下一个值大于`1`。虽然这一价值在技术上是执行/平台所依赖的,但一般是有关`2.2E-16`,或`2^-52`.

对那些对这里的细节关注不够的人 -- -- 包括我的过去! - 通常认为任何浮点精度的Skew 永远不能大于`Number.EPSILON`因此,理论上,我们可以使用`Number.EPSILON`作为一种“非常小的”容忍值,以确保数量上的平等比较是“安全的”:

```js
function safeNumberEquals(a,b) {
    return Math.abs(a - b) < Number.EPSILON;
}

point3a = 0.1 + 0.2;
point3b = 0.3;

// are these safely "equal"?
safeNumberEquals(point3a,point3b);      // true
```

| 警报: |
| :--- |
| 在第一版的"Types & Grammar"一书中,我确实推荐了确切的这个方法. 我错了 我应该更仔细地研究这个话题 |

但事实证明,这个方法根本不安全:

```js
point3a = 10.1 + 0.2;
point3b = 10.3;

safeNumberEquals(point3a,point3b);      // false :(
```

嗯... 真是个烂人!

很遗憾`Number.EPSILON`只作为某些小数/操作的"安全平等的"差错阈值,在其他情况下,它太小,产生假负数.

你可以缩放`Number.EPSILON`由某种因素生成一个更大的阈值,避免虚假的负数,但仍会过滤出程序中所有浮点skew. 但是,使用什么因素 完全是一个人工判断呼叫 根据价值的大小, 和操作它们,你的程序将产生。 无法自动计算出可靠,通用的阈值.

除非你真的知道你在做什么, 你只是不应该用这个`Number.EPSILON`门槛接近。

| 提普: |
| :--- |
| 我强烈建议阅读此文。[^EpsilonBad]但是如果我们不能使用`Number.EPSILON`为了避免浮点刺的危险 我们该怎么办? 如果你可以完全避免浮点 通过缩放你的所有数字 所以它们都是整数整数(或者比奇特) 在进行数学时,做它。 只有在所有数学完成后,您必须输出/呈现最终值时,才会处理小数值。 如果不可能/实用,请使用任意精度小数模拟库并避免`number`完全的价值观。 或者在另一个不基于IEEE-754的外部编程环境中进行数学. |

### 数字比较

和字符串一样,数字值可以使用相同的运算符来比较(对于平等和关系顺序).

记住,无论数字值在被指定为字数(基数-10,八进制,十六进制,指数等)时采取什么形式,所储存的基本值就是将比较的值. 也铭记上一节所讨论的浮点不精确问题,因为比较将对准确的二进制内容敏感。

#### 数字平等

和字符串一样,数字的平等比较使用任意一种`==` / `===`运算符或`Object.is(..)`.还忆及如果两者类型相同,`==`执行与`===`.

```js
42 == 42;                   // true
42 === 42;                  // true

42 == 43;                   // false
42 === 43;                  // false

Object.is(42,42);           // true
Object.is(42,43);           // false
```

对于`==`强制平等(当操作类型不匹配时),如果两个操作都不是字符串值,`==`倾向于数字平等检查(意指两种操作都受数字胁迫).

```js
// numeric (not string!) comparison
42 == "42";                 // true
```

在这个片断中,强制平等迫使`"42"`改为:`42`,反之亦然(`42`改为:`"42"`) (中文(简体) ). 一旦这两类都是`number`,然后将它们的价值观作精确的平等比较,与`===`会的。

回顾联署材料没有区分价值观,例如:`42`, `42.0`,以及`42.000000`;盖下,皆同. 令人惊讶的是,`==`和`===`平等检查证实:

```js
42 == 42.0;                 // true
42.0 == 42.00000;           // true
42.00 === 42.000;           // true
```

你可能拥有的直觉是, 如果两个数字是完全相同的, 他们是平等的。 JS就是这样解释的. 不过`0.3`并非与`0.1 + 0.2`,因为(如我们先前所见),后者产生一个基础值,即“非常接近”`0.3`,但并不完全一样。

有趣的是,这两个值是如此的接近 他们的区别小于`Number.EPSILON`因此,JS实际上不能代表这种差异。

因为他们之间的差别太小, 但请注意:联署材料*可以说明,有*个区别,这就是为什么你看到`4`在十进制结束时,JS评价`0.1 + 0.2`。你可以 * 键出数字字元`0.00000000000000004`(aa,`4e-17`)),是两者之间的区别。`0.3`和`0.1 + 0.2`.

联合来文无法做到的是以IEEE-754浮动点数为单位,以足够准确的方式表示一个小的数字,使其业务产生预期结果。 它太小了,不能在`number`《联合提交的材料》提供的类型。

这么说`0.1 + 0.2 == 0.3`决定`false`尽管JS不能准确代表或做任何 价值小于这个差别的东西

就像我们看到的弦,`!=`(强制不均)和`!==`(限制-不平等)操作员与数字合作。`x != y`基本上是这样的`!(x == y)`,以及`x !== y`基本上是这样的`!(x === y)`.

数字平等有两个令人沮丧的例外(无论您使用`==`或`===`):

```js
NaN === NaN;                // false -- ugh!
-0 === 0;                   // true -- ugh!
```

`NaN`绝不等于自己(即使`===`),以及`-0`永远等于`0`(甚至连`===`) (中文(简体) ). 有时让人惊讶的是,甚至`===`有两个例外

然而,`Object.is(..)`平等检查没有例外,因此,平等检查与平等检查的比较没有例外。`NaN`和`-0`,避免`==` / `===`运算符和使用`Object.is(..)`- 或用于`NaN`特别是,`Number.isNaN(..)`.

#### 数字关系比较

Just like with string values, the JS relational operators (`<`, `<=`, `>`, and `>=`) operate with numbers. The `<` (less-than) and `>` (greater-than) operations should be fairly self explanatory:

```js
41 < 42;                    // true

0.1 + 0.2 > 0.3;            // true (ugh, IEEE-754)
```

Remember: just like `==`, the `<` and `>` operators are also coercive, meaning that any non-number values are coerced to numbers -- unless both operands are already strings, as we saw earlier. There are no strict relational comparison operators.

如果在数字间进行关系比较,唯一能避免强迫的方法就是确保比较总是有两个数字. 否则,这些操作员将进行类似于“如何”的“强制关系”比较。`==`进行 *强制平等* 比较。

### 数学运算符

正如我早些时候所断言的那样,使用编程语言数字的主要原因,是用它们进行数学操作. 因此,让我们谈谈我们如何做到这一点。

基本的算术操作员是:`+`(增加),`-`(减),`*`(乘法),和`/`(编). 还有操作员`**`(收费)和`%`(modulo, aka * division release *). 还有`+=`, `-=`, `*=`, `/=`, `**=`,以及`%=`运算符的形式,该运算符又将结果重新分配给左操作符,必须像变量或财产一样成为有效的分配目标。

| 注: |
| :--- |
| 正如我们已经看到,`+`运算符已经超载,可以同时使用数字和字符串。 当一个或两个操作器都是字符串时,结果就是字符串协和(包括必要时将两个操作器都强迫为字符串). 但是,如果两个操作都不是字符串,结果就是数字加成,如预期的那样. |

所有这些数学运算符都是 *binary *,意思是它们期望两个值操作符,一个在操作符的两侧;它们都期望操作符是数字值. 如果其中一种或两种操作均为非编号,则非编号操作即被强迫或被强迫进行操作。 我们将在后面的一章中详细论述胁迫问题。

考虑:

```js
40 + 2;                 // 42
44 - 2;                 // 42
21 * 2;                 // 42
84 / 2;                 // 42
7 ** 2;                 // 49
49 % 2;                 // 1

40 + "2";               // "402" (string concatenation)
44 - "2";               // 42 (because "2" is coerced to 2)
21 * "2";               // 42 (..ditto..)
84 / "2";               // 42 (..ditto..)
"7" ** "2";             // 49 (both operands are coerced to numbers)
"49" % "2";             // 1 (..ditto..)
```

那个`+`和`-`运算符还以 *unary * 形式出现,意思是它们只有一个操作符;同样,操作符预期会是一个数字,如果不是,则被强迫一个数字:

```js
+42;                    // 42
-42;                    // -42

+"42";                  // 42
-"42";                  // -42
```

你可能已经注意到`-42`看起来只是"负四十二"数字字形. 这不太对劲 JS语法的一个细微差别是它不承认负数字. 相反,联署材料视此为正数。`42`这之前,和否定, 由无常`-`操作员在它的前面。

有点奇怪,然后:

```js
-42;                    // -42
- 42;                   // -42
-
    42;                 // -42
```

如您所见,允许在`-`无操作符及其操作符;实际上,所有操作符和操作符都是如此。

#### 增减

还有另外两个非数字运算符:`++`(递减)和`--`减减. 他们既进行各自的操作,又将结果重新分配给操作 -- -- 必须像变量或财产一样是一个有效的分配目标。

你可能会想到`++`等值于`+= 1`,以及`--`等值于`-= 1`:

```js
myAge = 42;

myAge++;
myAge;                  // 43

numberOfHeadHairs--;
```

然而,这些操作符是特殊的操作符,因为它们可以像上面那样出现在后缀(在操作符之后)位置上,或者在前缀(在操作符之前)位置上:

```js
myAge = 42;

++myAge;
myAge;                  // 43

--numberofHeadHairs;
```

在此类例子中,前缀和后缀位置似乎产生同样的结果(递增或减少),这似乎很奇怪。 区别是微妙的,与最终重新分配的结果无关. 我们将在稍后的一章中重新审视这些特定的操作员,以挖掘位置差异.

### 位元运算符

JS提供多个位元运算符,以进行数字值的位元级操作.

然而,这些比特操作并不是针对IEEE-754数字的打包比特式(见第1章)进行的. 相反,操作数字首先转换为签名的32位*整数*,执行位操作,然后将结果转换为IEEE-754数字.

记住,就像其他原始操作员一样,这些只是计算新的值,而不是实际修改一个数值。

* `&`(bitwise and):用两个操作中每个对应的位进行AND操作;`42 & 36 === 32`(即:`0b00...101010 & 0b00...100100 === 0b00..100000`)

* `|` (bitwise OR): Performs an OR operation with each corresponding bit from the two operands; `42 | 36 === 46` (i.e., `0b00... 101010 (英语). | 0b00... 100100......... 0b00...101110')

* `^`(bitwise XOR):执行一个XOR(eXclusive-OR)操作,从两个操作中每个对应的位点;`42 ^ 36 === 14`(即:`0b00...101010 ^ 0b00...100100 === 0b00...001110`)

* `~`(bitwise NOT):针对单个操作的位点执行一个不动操作;`~42 === -43`(即:`~0b00...101010 === 0b11...010101`); 使用 2 的补数,签名整数有第一个比特设置到`1`表示负数,其余的位数(根据2's supplate,即1's supplate bit felping,然后添加)`1`这将是`43` (`0b10...101011`;相当于`~`数字算术`~x === -(x + 1)`,这样`~42 === -43`

* `<<`(左转):用右转指定的位数来进行左转的位数;`42 << 3 == 336`(即:`0b00...101010 << 3 === 0b00...101010000`)

* `>>`(右移):用右移指定的位数来进行左移位数的符号宣传右移,丢弃从右侧掉下来的位数;无论最左移位是(`0`,或`1`阴性)作为位数复制到左边(从而保留结果中原始值的符号);`42 >> 3 === 5`(即:`0b00..101010 >> 3 === 0b00...000101`)

* `>>>`(零填充右移,aka未署名右移): 执行与`>>`不过`0`在从左侧移来的位数上填充,而不是复制最左边的位数(从而忽略了结果中原始值的符号);`42 >>> 3 === 5`不过`-43 >>> 3 === 536870906`(即:`0b11...010101 >>> 3 === 0b0001...111010`)

* `&=`, `|=`, `<<=`, `>>=`, and `>>>=` (bitwise operators with assignment): Performs the corresponding bitwise operation, but then assigns the result to the left operand (which must be a valid assignment target, like a variable or property, not just a literal value); note that `~=` is missing from the list, because there is no such "binary negate with assignment" operator

老实说,在联署材料中,比特操作并不常见。 但有时你可能会看到这样的陈述:

```js
myGPA = 3.54;

myGPA | 0;              // 3
```

由于位元运算符只作用于32位整数,所以 `| 0` operation truncates (i.e., `Math.trunc(.)' 任意小数值,只留下整数.

| 警报: |
| :--- |
| 一个常见的误解是:`| 0` is like *floor* (i.e., `数学地板(.`). The result of `| 0` agrees with `数学地板(.` on positive numbers, but differs on negative numbers, because by standard definition, *floor* is an operation that rounds-down towards `-无穷无尽`. `| 0`只是丢弃十进制位数,事实上是截断。 |

### 数值方法

数字值为数字特定操作提供以下方法(作为属性): 1.

* `toExponential(..)`:使用科学符号生成数字的字符串表示(例如,`"4.2e+1"`)

* `toFixed(..)`: 生成一个带有指定小数位数的数字的非科学注释字符串( 必要时绕圆或零铺设)

* `toPrecision(..)`: 喜欢`toFixed(..)`,但它应用数字参数作为重要数字(即精确度)的数目,包括整数和小数位(如果有的话)

* `toLocaleString(..)`: 根据当前区域生成数字的字符串表示

```js
myAge = 42;

myAge.toExponential(3);         // "4.200e+1"
```

JS语法的一个特别细微之处是:`.`在处理数字文字和财产/方法获取时,可能含糊不清。

如果一个`.`数字字数后立即出现( 没有空白), 而还没有`.`数字值的小数,`.`假定是数字小数的起始部分。 但是如果`.`毫不含糊地说,不是数字字面的一部分,那么它总是被当作一种财产存取。

```js
42 .toExponential(3);           // "4.200e+1"
```

这里,白色的空间 使得`.`,将其指定为财产/方法访问。 也许更常见/更喜欢使用`(..)`而不是这种混淆的空白:

```js
(42).toExponential(3);          // "4.200e+1"
```

联合来文解释语法规则的一个不同寻常的效果:

```js
42..toExponential(3);           // "4.200e+1"
```

叫做"双点",第一个`.`在这个表达式中是小数,因此是第二个`.`无疑不是十进制,而是财产/方法。

还有,注意第一个之后没有数字`.`留下线索是完全合法的语法`.`数字字表中:

```js
myAge = 41. + 1.;

myAge;                          // 42
```

数值`bigint`类型不能有小数,所以解析是明确的:`.`在一个字之后,`n`) 总是财产存取:

```js
42n.toString();                 // 42
```

### 静态`Number`属性

* `Number.EPSILON`: 在`1`和次高数字

* `Number.NaN`: 与全球相同`NaN`符号,特殊无效编号

* `Number.MIN_SAFE_INTEGER` / `Number.MAX_SAFE_INTEGER`: 绝对值最大的正负整数(从`0`)

* `Number.MIN_VALUE` / `Number.MAX_VALUE`: 最小值(最接近正值)`0`)和最大值(正值距离`0`) 可由`number`类型

* `Number.NEGATIVE_INFINITY` / `Number.POSITIVE_INFINITY`: 与全局相同`-Infinity`和`Infinity`,代表最大(非无限)值的数值距离`0`

### 静态`Number`帮助者

* `Number.isFinite(..)`: 返回布尔表示值是否为有限 -- -- a`number`这不是`NaN`和两个无限之一

* `Number.isInteger(..)` / `Number.isSafeInteger(..)`: 返回布尔表示值为整数`number`没有小数位,如果它位于整数的*安全*范围内(`-2^53 + 1` - `2^53 - 1`)

* `Number.isNaN(..)`: 全局的bug 固定版本`isNaN(..)`工具,用于识别所提供的参数是否是特殊`NaN`价值

* `Number.parseFloat(..)` / `Number.parseInt(..)`: 用于解析数字数字的字符串值的功能, 由左到右, 直到字符串结束或遇到第一个非浮动( 或非整数) 字符

### 静态`Math`命名空间

自主要使用以来`number`数值用于进行数学操作,JS包括许多标准的数学常数和操作工具。`Math`名称空间。

有很多这些,所以我省略列出每一个。 但这里有几条用于说明:

```js
Math.PI;                        // 3.141592653589793

// absolute value
Math.abs(-32.6);                // 32.6

// rounding
Math.round(-32.6);              // -33

// min/max selection
Math.min(100,Math.max(0,42));   // 42
```

与`Number`,这也是`Number(..)`函数(指数字强迫),`Math`只是持有这些属性和静态函数工具的物体;不能将其称为函数。

| 警报: |
| :--- |
| 一个奇怪的成员`Math`名称空间是`Math.random()`,用于在`0`和`1.0`将随机数生成视为数学操作是不寻常的, 这是一项内在的状态/副作用。 JS使用的伪随机数生成器(PRNG)从密码学的角度来看, 几年前,网络平台 与更安全`crypto.getRandomValues(..)`API(基于更好的PRNG),它以随机位数填充一个打字阵列,这些位数可以被解释为一个或多个整数(类型指定的最大量). 使用`Math.random()`现在普遍感到沮丧。 |

### 大写和数字不要混合

正如第一章所述,`number`类型和类型`bigint`类型不能在同一操作中混合。 即使你正在做一个简单的数值递增(就像循环中):

```js
myAge = 42n;

myAge + 1;                  // TypeError thrown!
myAge += 1;                 // TypeError thrown!

myAge + 1n;                 // 43n
myAge += 1n;                // 43n

myAge++;
myAge;                      // 44n
```

所以,如果你使用两者`number`和`bigint`在您的程序中,您需要手动将一个值类型强制到另一个值类型。 那个`BigInt(..)`函数( 无)`new`关键词)可以强制 a`number`值为`bigint`。反之亦然,从`bigint`改为:`number`时,使用`Number(..)`函数( 又无)`new`关键词:

```js
BigInt(42);                 // 42n

Number(42n);                // 42
```

但记住,在这些类型之间进行胁迫有一定的风险:

```js
BigInt(4.2);                // RangeError thrown!
BigInt(NaN);                // RangeError thrown!
BigInt(Infinity);           // RangeError thrown!

Number(2n ** 1024n);        // Infinity
```

## 基础是基础

在过去的两章里,我们深入挖掘了JS中原始价值观的行为方式. 我敢打赌,不止几个读者,像我一样, 准备跳过这些话题。 但现在,希望你能看到理解这些概念的重要性。

故事并没有在这里结束。 远远没有! 在下一章中,我们将将注意力转向理解JS的对象类型(对象,阵列等).

[^TwitterUnicode]:"推特-图文库新更新:埃莫吉人物计数";安迪·皮珀;2018年10月;https://twittercommunity.com/t/new-update-to-the-twitter-text-library-emoji-character-count/114607; 2022年7月访问

[^INTLAPI]: ECMAScript 2022国际化API规格;https://402.ecma-international.org/9.0/; 2022年8月访问

[^INTLCollator]: "Intl.Collator",MDN; 互联网档案馆的存檔,存档日期2013-03-02.https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Collator; 2022年8月访问

[^INTLSegmenter]: "Intl.Secmenter",MDN; (中文(简体) ).https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter; 2022年8月访问

[^StrictEquality]:"7.2.16 IsstrettlyEqual(x,y)",ECMAScript 2022语言规格; 2.https://262.ecma-international.org/13.0/#sec-isstrictlyequal; 2022年8月访问

[^LooseEquality]:"7.2.15 IsLoosely Equal(x,y)",ECMAScript 2022语言规格; 1.https://262.ecma-international.org/13.0/#sec-islooselyequal; 2022年8月访问

[^EpsilonBad]:"PLEASE在被接受的答案中不遵循代码配方", Stack Overflow; Daniel Scott; 2019年7月;.https://stackoverflow.com/a/56967003/228852; 2022年8月访问
