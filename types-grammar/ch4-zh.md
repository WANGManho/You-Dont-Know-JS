# 你不了解JS然而:类型和语法 - 第二版
# 第4章:胁迫价值

| 注: |
| :--- |
| 进行中的工作 |

我们已经彻底涵盖了JS中所有不同的*类型*价值观. 一路走来,不止几次,我们提到从一种价值到另一种价值的转换——实际上,强迫——的概念。

在本章中,我们将潜入胁迫的深处,并揭开它的所有奥秘.

## 胁迫:明示与默示

一些开发人员断言,当您在操作中明确显示类型变化时,这不符合*强制*的条件,而只是类型播报或类型转换. 换句话说,要求只是暗示胁迫。

我不同意这个说法。 我使用 * 强制 * 来标记任何类型转换的动态类型语言, 无论它是否在代码中显而易见. 这就是为什么:*解释性*和*暗示性*之间的界限并不明确和客观,相当主观. 如果你认为一种类型转换是隐含的(因此是*强制*),但我认为它是明示的(因此不是*强制*),这种区分变得无关紧要.

在我们探讨各种“明确”和“隐性”胁迫形式时,牢记这种主观性。 事实上,这里有一个破坏者:大多数胁迫行为都可以作为两者之一来争论,所以我们将以这样平衡的视角来看待它们.

### 隐含:坏还是...

联合来文开发者的一个极为普遍的看法是,“胁迫是坏的”具体地说,“隐性胁迫是坏的”;像TypeScript这样具有意识的类型的工具的流行程度上升,就有力地说明了这种情绪。

但这种感觉并不是新的。 14+多年前,道格拉斯·克罗克福德的著作"好部件"也以"暗中胁迫*"作为"坏部件*"之一而闻名. 甚至联署材料的创造者Brendan Eich也经常声称,“隐性胁迫”是一个错误。[^EichCoercion]在早期设计 他现在后悔的语言。

如果你在联署机构待了几个月 你几乎肯定听到这些观点 强烈和主要地表达出来 如果你和JS在一起已经好几年了 你可能已经下定决心了

事实上,我认为你很难说出任何其他知名的JS教学来源,这些教学来源都强烈赞同(几乎是各种形式的)胁迫;我确实如此——这本书绝对如此! - 但我感觉就像一个孤独的声音 在荒野中徒劳无益地喊叫。

然而,我多年来提出的一个看法是:大多数公开谴责“隐性胁迫”的人,实际上在他们自己的守则中使用了“隐性胁迫”。 垛...

道格拉斯·克罗克福德说为了避免"隐性胁迫"的错误[^CrockfordCoercion]但是他的代码是`if (..)`带有非布尔值的语句。[^CrockfordIfs]许多人否认我过去曾指出, 嗯,好吗?

Brendan Eich说他对 "隐性胁迫"表示遗憾 但他公开赞同[^BrendanToString]象样`x + ""`用来强迫他人在`x`这绝对是"隐性胁迫"

我们如何看待这种不和谐? 难道只是"照我说的做,而不是照做" 轻微的自我束缚吗? 还是还有别的呢?

我不打算在这里通过最后的判断,但我希望读者深刻思考这个问题,因为你在整个篇章和书中继续阐述这个问题。

## 摘要

现在,我已经挑战你 更深入地审查强迫 比你曾经有过的放纵, 让我们先看看 如何的基础 如何发生强迫,

规格详细列出了一些*抽象操作*.[^AbstractOperations]这决定了内部从一个值类型转换到另一个值类型。 必须意识到这些操作,作为语言组合中的强制力学,并以各种方式与之匹配.

这些操作 * 看起来像是真正可以称为的功能,例如`ToString(..)`或`ToNumber(..)`但是,通过“抽象”,我们的意思是,它们只在概念上通过这些名称存在;它们不是我们能够“直接”在程序上引用的功能。 相反,我们根据节目中的语句/表现,暗中/间接地激活它们。

### 到博莱安

决策(有条件的分支)总是需要布尔`true`或`false`数值。 但是,极常见的是,想要根据非布尔值条件做出这些决定,比如一个字符串是空的还是里面有什么.

当遇到需要布尔语的上下文时 -- -- 如条件条款`if`语句或`for`循环-`ToBoolean(..)`[^ToBoolean]抽象操作被激活以方便强制.

联合材料中的所有价值观都存在于两个桶中之一: " 真相 " 或 " 谎言 "。 真理价值观通过`ToBoolean()`操作到`true`,而麻痹值则迫使`false`:

```
// ToBoolean() is abstract

ToBoolean(undefined);               // false
ToBoolean(null);                    // false
ToBoolean("");                      // false
ToBoolean(0);                       // false
ToBoolean(-0);                      // false
ToBoolean(0n);                      // false
ToBoolean(NaN);                     // false
```

简单规则: *其他任何价值 * 不属于上述清单的,是真实的,通过`ToBoolean()`改为:`true`:

```
ToBoolean("hello");                 // true
ToBoolean(42);                      // true
ToBoolean([ 1, 2, 3 ]);             // true
ToBoolean({ a: 1 });                // true
```

甚至是价值`"   "`(弦与只有白空间),`[]`(空阵),和`{}`(空物体),它可能看起来直觉上像是它们比"真"更"假",但是强迫它`true`.

| 警报: |
| :--- |
| 有* 狭义,棘手的例外 这个真理规则。 例如,网络平台对长期存在的`document.all`收集/阵列特性,虽然不能完全删除 -- -- 这将打破太多的网站。 即使是在什么地方`document.all`仍然被定义, 它表现为“ 假对象 ”[^ExoticFalsyObjects] -- `undefined`然后强迫,`false`;这意味着遗留的有条件检查,例如`if (document.all) { .. }`不再通过。 |

那个`ToBoolean()`强制操作基本上是一个查询表,而不是一种在强制中使用非布尔到布尔的步骤的算法. 因此,一些开发者断言,这并非"真正的"胁迫,与其他抽象的强制操作一样. 我觉得那是假的`ToBoolean()`从非布尔值类型转换成布尔值类型,这是明显的剪切类型强制(即使它是一个非常简单的查找而不是一个算法).

铭记:这些布尔胁迫规则只有在下列情况下才适用:`ToBoolean()`已实际激活。 JS语言中有些构思/符号 可能看起来是布林式的强迫 但实际上并非如此 稍后会有更多关于这些。

### 初级

任何价值,如果不是已经是原始的,都可以用原始的`ToPrimitive()`(特别是,`OrdinaryToPrimitive()`[^OrdinaryToPrimitive])抽象操作. 一般来说,`ToPrimitive()`被给予一个 * 东西 * 告诉它是否`number`或`string`首选。

```
// ToPrimitive() is abstract

ToPrimitive({ a: 1 },"string");          // "[object Object]"

ToPrimitive({ a: 1 },"number");          // NaN
```

那个`ToPrimitive()`操作将查看所提供对象,用于`toString()`方法或一个`valueOf()`方法;它寻找的顺序由 *hint * 控制。`"string"`表示登录`toString()` / `valueOf()`顺序,而`"number"`(或无*int*)表示进入`valueOf()` / `toString()`秩序。 秩序。

如果该方法返回匹配 *hinted* 类型的值,则操作结束。 但如果该方法不返回 * shinted * 类型的值,`ToPrimitive()`然后查找并引用另一种方法(如果找到)。

如果试图援引方法未能产生 * inted * 类型的值,则通过相应的抽象操作强制最终返回值:`ToString()`或`ToNumber()`.

### 拖动

几乎任何已经不是字符串的值都可以被强制到字符串表示,通过`ToString()`. [^ToString]这通常相当直观,尤其是原始价值:

```
// ToString() is abstract

ToString(42.0);                 // "42"
ToString(-3);                   // "-3"
ToString(Infinity);             // "Infinity"
ToString(NaN);                  // "NaN"
ToString(42n);                  // "42"

ToString(true);                 // "true"
ToString(false);                // "false"

ToString(null);                 // "null"
ToString(undefined);            // "undefined"
```

有些结果可能与普通直觉不同 如第2章所述,使用科学标记表示的人数非常大或非常少:

```
ToString(Number.MAX_VALUE);     // "1.7976931348623157e+308"
ToString(Math.EPSILON);         // "2.220446049250313e-16"
```

另一个反直觉的结果来自`-0`:

```
ToString(-0);                   // "0" -- wtf?
```

这不是一个错误,这只是JS最早时代的故意行为,基于开发者一般不希望看到负零输出的假设.

一种原始价值类型是“不允许”被强迫(隐含,至少)弦。`symbol`:

```
ToString(Symbol("ok"));         // TypeError exception thrown
```

| 警报: |
| :--- |
| 呼叫电话`String()`[^StringFunction]具体功能( 无)`new`操作者)一般认为只是援引`ToString()`抽象操作。 虽然这大部分是真实的,但并非完全如此.`String(Symbol("ok"))`工作,而抽象`ToString(Symbol(..))`本身就是一个例外。 更多关于`String(..)`后於本章. |

#### 默认`toString()`

何时`ToString()`以对象值类型激活,它向`ToPrimitive()`操作(如前所述)`"string"`作为其 * bind * 类型:

```
ToString(new String("abc"));        // "abc"
ToString(new Number(42));           // "42"

ToString({ a: 1 });                 // "[object Object]"
ToString([ 1, 2, 3 ]);              // "1,2,3"
```

由于`ToPrimitive(..,"string")`代表团,这些对象都有默认值`toString()`方法(通过`[[Prototype]]`) 援引。

### 数量

非数字值 * 类似 * 数字,如数字字符串,一般可以被强迫进行数字表示,使用`ToNumber()`: [^ToNumber]

```
// ToNumber() is abstract

ToNumber("42");                     // 42
ToNumber("-3");                     // -3
ToNumber("1.2300");                 // 1.23
ToNumber("   8.0    ");             // 8
```

如果完整值不完全*(除白空)类似有效数字,结果将是`NaN`:

```
ToNumber("123px");                  // NaN
ToNumber("hello");                  // NaN
```

其他原始值有某些指定的数值当量:

```
ToNumber(true);                     // 1
ToNumber(false);                    // 0

ToNumber(null);                     // 0
ToNumber(undefined);                // NaN
```

有一些相当令人惊讶的指定`ToNumber()`:

```
ToNumber("");                       // 0
ToNumber("       ");                // 0
```

| 注: |
| :--- |
| 我把这些叫做"惊奇",因为我认为他们胁迫我`NaN`,路径`undefined`当然 |

一些原始价值被*不允许* 强迫数字,并导致例外而不是`NaN`:

```
ToNumber(42n);                      // TypeError exception thrown
ToNumber(Symbol("42"));             // TypeError exception thrown
```

| 警报: |
| :--- |
| 呼叫电话`Number()`[^NumberFunction]具体功能( 无)`new`操作者)一般认为只是援引`ToNumber()`抽象操作将一个值强制到一个数字。 虽然这大部分是真实的,但并非完全如此.`Number(42n)`工作,而抽象`ToNumber(42n)`本身就是一个例外。 |

#### 其他摘要数字转换

况乎.`ToNumber()`,该规格定义`ToNumeric()`,该激活`ToPrimitive()`,然后有条件地代表`ToNumber()`如果值是 * 不是 * 已经是一个`bigint`值类型。

也有各种各样的抽象操作,涉及将值转换为一般的非常具体的子集`number`类型:

* `ToIntegerOrInfinity()`
* `ToInt32()`
* `ToUint32()`
* `ToInt16()`
* `ToUint16()`
* `ToInt8()`
* `ToUint8()`
* `ToUint8Clamp()`

其他业务`bigint`:

* `ToBigInt()`
* `StringToBigInt()`
* `ToBigInt64()`
* `ToBigUint64()`

你可以从它们的名字中推断出这些操作的目的,和/或从在规格中咨询它们的算法中推断出来. 对于大多数联署部队的行动来说,更有可能是更高层次的行动,比如:`ToNumber()`被激活,而不是这些特定的。

#### 默认`valueOf()`

何时`ToNumber()`在对象值类型上激活,而是向`ToPrimitive()`操作(如前所述)`"number"`作为其 * bind * 类型:

```
ToNumber(new String("abc"));        // NaN
ToNumber(new Number(42));           // 42

ToNumber({ a: 1 });                 // NaN
ToNumber([ 1, 2, 3 ]);              // NaN
ToNumber([]);                       // 0
```

由于`ToPrimitive(..,"number")`代表团,这些对象都有默认值`valueOf()`方法(通过`[[Prototype]]`) 援引。

### 平等比较

当JS需要确定两个值是否为 * 相同值 * 时,它激活`SameValue()`[^SameValue]业务,它代表各种相关的次级业务。

这一行动非常狭窄和严格,没有任何胁迫或任何其他特殊情况的例外。 如果两个值是 * 完全相同 * 的结果是`true`否则`false`:

```
// SameValue() is abstract

SameValue("hello","\x68ello");          // true
SameValue("\u{1F4F1}","\uD83D\uDCF1");  // true
SameValue(42,42);                       // true
SameValue(NaN,NaN);                     // true

SameValue("\u00e9","\u0065\u0301");     // false
SameValue(0,-0);                        // false
SameValue([1,2,3],[1,2,3]);             // false
```

这些行动的一个变化是:`SameValueZero()`及其相关的次级行动。 主要的区别在于这些手术治疗`0`和`-0`如无分别.

```
// SameValueZero() is abstract

SameValueZero(0,-0);                    // true
```

如果数值是数字( Q)`number`或`bigint`), `SameValue()`和`SameValueZero()`两名代表都参加同一名称的分队行动,每个分队都有专长。`number`和`bigint`类型。

否则`SameValueNonNumeric()`如果所比较的数值都是非数字的,则为授权的子操作:

```
// SameValueNonNumeric() is abstract

SameValueNonNumeric("hello","hello");   // true

SameValueNonNumeric([1,2,3],[1,2,3]);   // false
```

#### 较高层次的平等

与`SameValue()`及其不同之处,该规格还界定了两个重要的较高抽象平等比较操作:

* `IsStrictlyEqual()`[^StrictEquality]
* `IsLooselyEqual()`[^LooseEquality]

那个`IsStrictlyEqual()`操作立即返回`false`如果比较的值类型不同。

如果值类型相同,`IsStrictlyEqual()`代表代表的分组行动`number`或`bigint`数值。[^NumericAbstractOps]你可能会期望这些授权的子操作是上述数字化的`SameValue()` / `SameValueZero()`操作。 不过`IsStrictlyEqual()`改为代表`Number:equal()`[^NumberEqual]或`BigInt:equal()`[^BigIntEqual].

两者的区别`Number:SameValue()`和`Number:equal()`后一种定义为`0`数字`-0`比较:

```
// all of these are abstract operations

Number:SameValue(0,-0);             // false
Number:SameValueZero(0,-0);         // true
Number:equal(0,-0);                 // true
```

这些业务也各不相同。`NaN`数字`NaN`比较:

```
Number:SameValue(NaN,NaN);          // true
Number:equal(NaN,NaN);              // false
```

| 警报: |
| :--- |
| 因此,换句话说,尽管它的名字,`IsStrictlyEqual()`并不完全像`SameValue()`,在比较`-0`或`NaN`被卷入其中。 |

那个`IsLooselyEqual()`操作还检查所比较的值类型;如果它们相同,它立即向`IsStrictlyEqual()`.

但如果比较的值类型不同`IsLooselyEqual()`实施各种“强制平等”步骤。 需要注意的是,这种算法总是试图将比较缩小到两种值类型都相同的地方(而且它往往倾向于`number` / `bigint`).

算法中*强制等值*部分的步法大致可归纳如下: 1.

1. 如果两个值是`null`而另一个是`undefined`, `IsLooselyEqual()`返回时`true`。换句话说,这个算法应用了 * nullish * 平等,即:`null`和`undefined`在胁迫下,它们彼此平等(而不是其他价值观)。

2. 如果两个值都是`number`而另一个是`string`,则`string`价值被强迫`number`通过`ToNumber()`.

3. 如果两个值都是`bigint`而另一个是`string`,则`string`价值被强迫`bigint`通过`StringToBigInt()`.

4. 如果两个值都是`boolean`它被胁迫到`number`.

5. 如果两个值都是非原始值(对象等),则被强迫给原始人使用`ToPrimitive()`;尽管未明确提供 *hint *,但默认行为将仿佛`"number"`是个暗示

在上述步骤中每次进行胁迫时,算法都*重复* 以新值重新激活。 这一过程一直持续到这些类型相同,然后将比较权下放给`IsStrictlyEqual()`操作。

我们从这个算法中能拿什么? 首先,我们看到有偏见`number`(或`bigint`比较;它从不强迫值`string`或`boolean`值类型。

重要的是,我们看到,`IsLooselyEqual()`和`IsStrictlyEqual()`具有类型敏感性。`IsStrictlyEqual()`如果类型不匹配,立即保释,而`IsLooselyEqual()`执行额外的工作,以强制不匹配的值类型成为相同的值类型(同样,理想的是,`number`或`bigint`).

此外,如果/一旦这些类型相同,两种操作都是相同的 --`IsLooselyEqual()`常驻裁军谈判会议代表`IsStrictlyEqual()`.

### 关系比较

当对数值进行相对比较时——也就是说,一个值是"小于"另一个值吗? ——有一个特定的抽象操作被激活:`IsLessThan()`. [^LessThan]

```
// IsLessThan() is abstract

IsLessThan(1,2, /*LeftFirst=*/ true );            // true
```

没有`IsGreaterThan()`操作;转而使用前两个参数`IsLessThan()`可以倒置实现"比"的比较. 保留左对右评价语义(对于细微的副作用),`isLessThan()`也采用第三个参数(`LeftFirst`(a) 如果`false`,这表明比较被逆转,第二个参数应当在第一个参数之前进行评估。

```
IsLessThan(1,2, /*LeftFirst=*/ true );            // true

// equivalent of a fictional "IsGreaterThan()"
IsLessThan(2,1, /*LeftFirst=*/ false );          // false
```

类似`IsLooselyEqual()`,则`IsLessThan()`操作是 * 强制 *, 意思是它首先保证其两个值的值类型匹配, 并倾向于数字比较。 没有`IsStrictLessThan()`非强制关系比较。

作为强制关系比较的例子,如果一个值的类型是:`string`另一类是`bigint`,则`string`被强迫`bigint`与上述`StringToBigInt()`操作。 一旦类型相同`IsLessThan()`以下各节所述收益。

#### 字符串比较

当两个值都是类型时`string`, `IsLessThan()`检查左手值是否为前缀( 第一个 *n * 字符)[^StringPrefix]右边的,如果是,`true`已返回。

如果两个字符串都不是另一个字符的前缀,那么两个字符串之间不同的第一个字符位置(从头到尾方向,而不是从左到右),会被比较各自的代码单位(数字)值;然后返回结果.

一般来说,编码单位遵循直观的词典(aka,字典)顺序:

```
IsLessThan("a","b", /*LeftFirst=*/ true );        // true
```

甚至数字也被当作字符(不是数字):

```
IsLessThan("101","12", /*LeftFirst=*/ true );     // true
```

unicode编码单元命令中甚至有一点嵌入式*humor*:

```
IsLessThan("🐔","🥚", /*LeftFirst=*/ true );      // true
```

至少我们现在已经回答了一个老问题 哪个先来?

#### 数字比较

对于数字比较,`IsLessThan()`服从于`Number:lessThan()`或`BigInt:lessThan()`操作[^NumericAbstractOps]分别:

```
IsLessThan(41,42, /*LeftFirst=*/ true );         // true

IsLessThan(-0,0, /*LeftFirst=*/ true );          // false

IsLessThan(NaN,1 /*LeftFirst=*/ true );          // false

IsLessThan(41n,42n, /*LeftFirst=*/ true );       // true
```

## 具体胁迫

现在我们已经涵盖了JS定义的 处理各种胁迫行为的所有抽象行动, 是时候把我们的注意力转向 具体的声明和表达了 我们可以用在我们的程序 启动这些行动。

### 到布尔

强制非类型值`boolean`进入这种类型,我们需要抽象`ToBoolean()`如本章前面所述。

在我们探索如何激活它之前 让我们来讨论 为什么你会想要强迫一个`ToBoolean()`强迫。

从代码可读性角度看,对类型胁迫加以解释是可取的(尽管不是普遍的)。 但从功能上来说, 迫使`boolean`当你将数据传递给外部来源时 -- 例如,将数据作为 JSON 提交到 API 端点 -- 而该位置预期`true` / `false`不需要强迫。

有几个方法`ToBoolean()`可以启动。 也许最明白的就是`Boolean(..)`函数:

```js
Boolean("hello");               // true
Boolean(42);                    // true

Boolean("");                    // false
Boolean(0);                     // false
```

如第三章所述,铭记`Boolean(..)`正在被调用没有`new`关键字,用于激活`ToBoolean()`抽象操作。

看到JS开发商使用`Boolean(..)`(b) 对此种明示胁迫的功能。 更多时候,开发者会使用双-`!`缩写:

```js
!!"hello";                      // true
!!42;                           // true

!!"";                           // false
!!0;                            // false
```

那个`!!`尽管它似乎如此,但它并不是自己的经营者。 其实是两个用法`!`操作员。 此操作员首先强制任何非`boolean`,然后否定它。 为了消除否定,第二个`!`翻转回向.

那么... 两者中哪一个`Boolean(..)`或`!!`,你是否认为这更是一种明确的胁迫?

鉴于这个翻转`!`做,然后必须用另一个`!`我会说`Boolean(..)`* 更明确* -- -- 在胁迫非 -- --`boolean`改为:`boolean`--比`!!`没错 但调查开源JS代码`!!`更经常使用。

如果我们将“解释性”定义为“最直接和明显地进行一项行动”,`Boolean(..)`边缘向外`!!`但是,如果我们定义 * 解释 * 是, "最明显地执行一个行动,"`!!`也许有边缘。 这里有确切的答案吗?

在你思考这个问题的时候 让我们看看另一个JS机制 激活`ToBoolean()`在封面之下:

```js
specialNumber = 42;

if (specialNumber) {
    // ..
}
```

那个`if`语句需要`boolean`以作出控制流程决定为条件。 如果你通过了一个非...`boolean`页:1`ToBoolean()`* 已执行。

与以往不同`ToBoolean()`胁迫言论,如`Boolean(..)`或`!!`这个`if`胁迫是短暂的,因为我们的联署方案从未看到胁迫的结果;它只是内部使用的。`if`. 有些人可能觉得如果程序不保存/使用该值,那就不是 * 真正的 * 强制. 但我强烈不同意 因为强制最肯定地影响了程序的行为

许多其他语句类型也激活`ToBoolean()`强迫,包括强迫和`? :`毒性条件,和`for` / `while`循环。 我们也有`&&`(逻辑-AND)和`||`(逻辑-OR)运算符。 例如:

```js
isLoggedIn = user.sessionID || req.cookie["Session-ID"];

isAdmin = isLoggedIn && ("admin" in user.permissions);
```

对两个运算符来说,左手表达式首先被评价;如果它已经是一个`boolean`页:1`ToBoolean()`强制被激活,为有条件的决定产生一个值.

| 注: |
| :--- |
| 简要解释这些操作符:||`, if the lefthand expression value (post-coercion, if necessary) is `真实`, the pre-coercion value is returned; otherwise the righthand expression is evaluated and returned (no coercion). For `&&`, if the lefthand expression value (post-coercion, if necessary) is `虚假`, the pre-coercion value is returned; otherwise, the righthand expression is evaluated and returned (no coercion). In other words, both `&&` and `||` force a `给Boolean ()` coercion of the lefthand operand for making the decision, but neither operator's final result is actually coerced to a `布尔。 |

在之前的片断中,尽管有命名意义,但不太可能`isLoggedIn`将是一个`boolean`如果这是事实`isAdmin`也不会是`boolean`那种代码是相当常见的, 但绝对危险的是,假设的结果`boolean`类型其实并不存在。 我们会在下一章重温这个例子, 以及这些操作员。

这类声明/表述(例如:`if (..)`, `||`, `说明在有条件的决策中 * 明示 * 胁迫或 * 默示 * 胁迫?

再说一遍,我想这取决于你的观点 具体要求非常明确地规定,他们只能通过`boolean`附加条件的价值观,如果非`boolean`已收到。 另一方面,还可以提出有力的论点,认为任何内部胁迫都是对主要职能的次要(隐含)影响。`if` / `&&`/ 等 (中文(简体) ).

此外,如上文所述,`ToBoolean()`讨论,有些人不考虑 任何激活`ToBoolean()`成为一种强迫。

我觉得这太过分了 我的话:`Boolean(..)`是最可取的 * 明确 * 胁迫形式。 我觉得`!!`, `if`, `for`, `while`, `&&`,并`||` are all *implicitly* coercing non-`布尔的,但我不介意。

由于包括道格·克罗克福德等著名名称在内的大多数开发者在实务中也使用隐含(英语:`boolean`其守则中的胁迫[^CrockfordIfs]我认为我们可以说,至少“某些形式”的“隐性”胁迫是普遍接受的,而不管那些无处不在的反面言论。

### 到字符串

一样`ToBoolean()`,有许多方法可以激活`ToString()`胁迫(如本章前面所述)。 哪种做法的决定同样具有主观性。

喜欢`Boolean(..)`函数,`String(..)`函数( 无)`new`关键词)是激活 * 说明 * 的主要方式`ToString()`胁迫:

```js
String(true);                   // "true"
String(42);                     // "42"
String(-0);                     // "0"
String(Infinity);               // "Infinity"

String(null);                   // "null"
String(undefined);              // "undefined"
```

不过`String(..)`不仅仅是一个激活`ToString()`。例如:

```js
String(Symbol("ok"));           // "Symbol(ok)"
```

这样做是有效的,因为 * 明显 * 胁迫`symbol`允许值。 但是,如果一个符号是 * 隐含 * 被胁迫的字符串(例如,`Symbol("ok") + ""`),基础`ToString()`操作丢出一个例外。 这证明`String(..)`不仅仅是激活`ToString()`。更多关于* 隐含* 字符串对符号的强制。

如果你打电话`String(..)`具有对象值(例如数组等),它激活`ToPrimitive()`操作(通过`ToString()`操作),然后查找该值的引用`toString()`方法:

```js
String([1,2,3]);                // "1,2,3"

String(x => x + 1);             // "x => x + 1"
```

除此之外`String(..)`,任何原始的,非nullish值(既不是`null`也没有`undefined`)可以在相应的对象包装中自动装箱(见第3章),提供调用`toString()`方法。

```js
true.toString();                // "true"
42..toString();                 // "42"
-0..toString();                 // "0"
Infinity.toString();            // "Infinity"
Symbol("ok").toString();        // "Symbol(ok)"
```

| 注: |
| :--- |
| 记住,这些`toString()`* 不一定 * 必然激活`ToString()`操作时,它们只是定义了自己如何将值表示为字符串的规则。 |

如所示`String(..)`就在刚才,各种对象子类型 -- 例如数组、函数、正则表达式,`Date`和`Error`实例等 -- -- 都定义自己的特定`toString()`可直接引用的方法:

```js
[1,2,3].toString();             // "1,2,3"

(x => x + 1).toString();        // "x => x + 1"
```

此外,任何普通物体的(默认)`[[Prototype]]`链接到`Object.prototype`有默认`toString()`可用方法:

```js
({ a : 1 }).toString();         // "[object Object]"
```

这是`toString()`* 明示 * 或 * 默示 *? 再说一遍,这要看情况 它当然是一种自我描述的机制,它倾斜了*解释*. 但它往往依赖于自动拳击,而自动拳击本身就是一种相当"隐性"的强迫.

让我们看看另一个常见的-- 和著名的认可! -- 用于强制字符串的值。 回顾第2章中的"String Concatenation",`+`如果操作器中的任何一个已经是一个字符串,则操作器会被超载来选择字符串协和,从而在必要时将非字符串协和为字符串。

考虑:

```js
true + "";                      // "true"
42 + "";                        // "42"
null + "";                      // "null"
undefined + "";                 // "undefined"
```

那个`+ ""`绳子强迫的规范利用了`+`超载, 不更改最后的强制字符串值。 顺便说一句,所有这些工作都与操作倒置(即,`"" + ..`).

| 警报: |
| :--- |
| 一个极为常见的误解是:`String(x)`和`x + ""`基本上相当于胁迫,分别只是形式上的“解释性”与“隐性”。 但事实并非如此! 我们将在本章稍后的"致先验"一节中重新讨论这个问题. |

一些人觉得这是一次“解释性”的胁迫,但我认为这显然更“含蓄性”,因为它利用了这种威胁。`+`超载;`""`被间接用于激活胁迫而不修改。 此外,考虑一下如果使用这个符号值时会发生什么:

```js
Symbol("ok") + "";              // TypeError exception thrown
```

| 警报: |
| :--- |
| 允许 * 明示 * 对符号的胁迫`String(Symbol("ok"))`,但不允许 * 隐性 * 胁迫(`Symbol("ok") + ""`TC39相当有意的。[^SymbolString]人们认为,符号作为原始的常用于原本使用字符串的地方,很容易被误认为字符串. 因此,他们希望确保开发者表示有意将一个符号强制到一个字符串上,希望避免许多这些预期的混乱. 这是一种极为罕见的* 情况,即语言设计坚持对* explect * vs. * implective * 强制行为发表意见,实际上加以歧视。 |

为什么例外? JS 治疗`+ ""`作为一种“隐性”的胁迫,这就是为什么在使用符号时,就会出现例外。 我认为这是一个非常铁的证明。

然而,正如我在本章开头提到的那样,Brendan Eich赞同`+ ""`[^BrendanToString]作为将值强制用于字符串的最佳* 方法。 我认为这具有很大的份量,因为他支持至少一个子集的“隐性”胁迫做法。 他对“隐性”胁迫的看法必须比“这都糟糕”更细微一些。

### 改为数字

数字逼迫比弦逼还复杂 因为我们也可以说`number`或`bigint`作为目标类型。 还有一套更小的数值,可以有效表示数字(其他的都变成`NaN`).

让我们从`Number(..)`和`BigInt(..)`函数(无)`new`关键词:

```js
Number("42");                   // 42
Number("-3.141596");            // -3.141596
Number("-0");                   // -0

BigInt("42");                   // 42n
BigInt("-0");                   // 0n
```

`Number`未能获得承认的胁迫`NaN`(参见第一章中的"无效数字"),而`BigInt`丢弃例外:

```js
Number("123px");                // NaN

BigInt("123px");
// SyntaxError: Cannot convert 123px to a BigInt
```

而且,虽然`42n`作为字词的有效语法`bigint`字符串`"42n"`绝不是公认的字符串代表`bigint`,按强制函数的任一形式:

```js
Number("42n");                  // NaN

BigInt("42n");
// SyntaxError: Cannot convert 42n to a BigInt
```

然而,我们*能够* 用其他表示数字的字符串来强迫数字,而不是典型的基数-10小数(更多信息见第1章):

```js
Number("0b101010");             // 42

BigInt("0b101010");             // 42n
```

通常情况下,`Number(..)`和`BigInt(..)`接收字符串值,但实际上不需要。 比如说,`true`和`false`强迫其典型数值等同:

```js
Number(true);                   // 1
Number(false);                  // 0

BigInt(true);                   // 1n
BigInt(false);                  // 0n
```

通常也可以在`number`和`bigint`类型:

```js
Number(42n);                    // 42
Number(42n ** 1000n);           // Infinity

BigInt(42);                     // 42n
```

我们也可以使用`+`普通运算符,通常假定它与`Number(..)`函数:

```js
+"42";                          // 42
+"0b101010";                    // 42
```

小心点 如果胁迫行为在某些方面不安全/无效,则将例外:

```js
BigInt(3.141596);
// RangeError: The number 3.141596 cannot be converted to a BigInt

+42n;
// TypeError: Cannot convert a BigInt value to a number
```

显然`3.141596`不安全地强迫整数,更不要说`bigint`.

不过`+42n`抛出例外是一个有趣的案例。 相比之下,`Number(42n)`工作很好,所以有点惊讶`+42n`失败。

| 警报: |
| :--- |
| 这个惊喜特别明显 因为...`+`通常认为数字前面只是指"正数",同样的方式`-`数字被假定为“负数”。 然而,正如第1章所解释的那样,JS的语法(`number`和`bigint`) 承认“负值”的语法。 默认情况下,所有数字字数被解为"阳性". 如果一个`+`或`-`被预留的,这些被作为与分析(正数)数字相对应的无操作符处理。 |

好吧,这样`+42n`解析为`+(42n)`但是... 为什么`+`丢一个例外在这里?

你可能记得早些时候,当我们显示JS允许对符号值的弦强制,但不允许对符号值的弦强制? 亦复如是. JS语言设计解释无谓`+`面对一个`bigint`价值作为 * 隐性 *`ToNumber()`强迫是不可抗拒的,`Number(..)`被解释为“解释性”`ToNumber()`强制(允许!)

换句话说,与流行的假设/言论相反,`Number(..)`和`+`无法互换。 我觉得`Number(..)`是更安全/更可靠的形式。

#### 数学操作

数学运算符(例如,`+`, `-`, `*`, `/`, `%`,以及`**`)期望它们的操作是数字. 如果你使用一个非...`number`与他们一起,这个价值将被强迫`number`用于数学计算。

类似如何`x + ""`是一种胁迫的规范`x`到字符串,表达式如`x - 0`安全胁迫`x`到一个数字。

| 警报: |
| :--- |
| `x + 0`不太安全,因为...`+`如果操作中任何一个操作都已经是一个字符串, 操作员都会超载以进行字符串连接。 那个`-`接线员不是那样超载的 所以唯一的胁迫就是...`number`当然`x * 1`, `x / 1`,甚至`x ** 1`通常在数学上也是相当的, 但那些是少得多的常见, 并且也许应该避免 可能使你的代码的读者感到困惑。 连`x % 1`似乎应该是安全的,但它可以引入浮点skew(见第2章中的"浮点隐形"). |

不论使用何种数学操作符,如果胁迫失败,a`NaN`这些操作员将宣传`NaN`以他们的结果。

#### 按位操作

Bitwise operators (e.g., `|`, `&`, `^`, `>>`, `<<`, and `<<<`) all expect number operands, but specifically they clamp these values to 32-bit integers.

如果你确定你处理的数字 在32位整数范围内,`x | 0` is another common expression idiom that has the effect of coercing `页:1` to a `如有必要,编号为`。

此外,由于JS引擎知道这些值会是整数,因此如果它们看到‘x',它们就有可能优化纯整数数学. | 0`. 这是从ASM.js中推荐的若干“类型说明”之一。[^ASMjs]几年前的努力。

#### 财产访问

物体的属性访问(和数组的索引访问)是可能发生隐性胁迫的另一个地方.

考虑:

```js
myObj = {};

myObj[3] = "hello";
myObj["3"] = "world";

console.log( myObj );
```

你对这个物体的内容有什么期望? 您期望两个不同的属性, 数字`3`(持有)`"hello"`)和字符串`"3"`(持有)`"world"`{\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}你觉得呢? 或者你认为两个属性在同一地点?

如果你尝试这个代码,你会看到,我们真的得到一个物体 与单一财产, 它持有`"world"`数值。 也就是说,联署材料在内部强迫`3`改为:`"3"`,或者反之,当这些属性进入。

有趣的是,开发者控制台可能非常能代表这样的对象:

```js
console.log( myObj );
// {3: 'world'}
```

难道`3`显示属性为数字`3`没有。 尝试添加其它属性到`myObj`:

```js
myObj.something = 42;

console.log( myObj )
// {3: 'world', something: 42}
```

我们可以看到,这个开发者控制台没有引用字符串属性密钥,因此我们无法推断出来自`3`相对于控制台是否使用`"3"`键名。

不如试试查看对象值的规格[^ObjectValue],我们发现:

> 属性密钥值要么是ECMAScript字符串值,要么是符号值. 所有字符串和符号值,包括空字符串,都作为属性键有效. 属性名称是一个属性键,属于字符串值.

罢 因此,在JS中,对象只持有字符串(或符号)属性. 这一定意味着数字`3`被胁迫为字符串`"3"`是吗?

在规格的同一节,我们进一步读作:

> An integer index is a String-valued property key that is a canonical numeric String (see 7.1.21) and whose numeric value is either +0𝔽 or a positive integral Number ≤ 𝔽(253 - 1). An array index is an integer index whose numeric value i is in the range +0𝔽 ≤ i < 𝔽(232 - 1).

如果属性密钥( 如`"3"`) **看起来*就像一个数字,它被当作一个整数指数. 嗯... 这似乎与我们刚才假设的相反 对吧?

尽管如此,我们从前面的引文中知道,属性键是*只*字符串(或符号). 因此,这里的"整数指数"不是描述实际位置,而是有意使用`3`在JS代码中,作为开发者表达的"整数指数";JS随后仍然必须实际将其存储在"canonical numberic String"的位置.

考虑尝试使用其他值类型,例如`true`, `null`, `undefined`,甚至非原物(其他对象):

```js
myObj[true] = 100;
myObj[null] = 200;
myObj[undefined] = 300;
myObj[ {a:1} ] = 400;

console.log(myObj);
// {3: 'world', something: 42, true: 100, null: 200,
// undefined: 300, [object Object]: 400}
```

如你所见,所有这些其他的值类型都是为了物体属性名称的目的被强制弦.

但是在我们说服我们自己 相信一切(甚至数字) 都被胁迫于弦之前 让我们看看一个阵列的例子:

```js
myArr = [];

myArr[3] = "hello";
myArr["3"] = "world";

console.log( myArr );
// [empty × 3, 'world']
```

开发者控制台可能代表一个与普通对象不同的数组. 尽管如此,我们仍然看到,这个阵列只有单数`"world"`值,在数值指数位置对应`3`.

这种输出方式意味着与我们之前的解释相反:一个数组的值只存储在数字位置上。 如果我们将字符串属性名称添加到`myArr`:

```js
myArr.something = 42;
console.log( myArr );
// [empty × 3, 'world', something: 42]
```

现在我们看到,这个开发者控制台代表了数组中的数字索引位置 * 没有 * 属性名称(位置),但是`something`属性在输出中命名。

同样真实的情况是,像v8这样的JS引擎出于性能优化的原因,往往会具有特殊的情况对象属性,这些特例对象像实际存储在数字位置上,就像它们是数组一样. 因此,即使JS程序的行为 似乎财产名称是`"3"`事实上,在封面下,v8可能把它当作是被迫的`3`!

这一切能有什么后果?

规格明确告诉我们,对象属性的行为是把它们当作字符串(或符号)处理. 这意味着我们可以假设,使用`3`访问对象上的位置将产生内部效果,强迫该属性名称用于`"3"`.

但是,用数组,我们看到一种相反的语义:`"3"`作为属性名称的效果是访问数字索引`3`位置,如同字符串被强制到数字。 但这只是一个结论, 即数组总是表现得像数字索引, 和/或也许反映了JS引擎的基本执行/优化细节。

重要部分是,我们需要认识到,物体不能简单地将任何价值用作财产名称。 如果它除了一个字符串或一个数字之外还有别的东西,我们可以预期会有*会有这种价值的强制.

我们需要期待和计划 而不是让它给我们惊喜 在路上的虫子!

### 到原始状态

联署材料中的大多数操作人员,包括那些我们见过的强迫`string`和`number`,旨在对抗原始价值。 当这些操作符中的任何一个被对照对象值使用时,抽象`ToPrimitive`算法(如前所述)被激活,以强迫对象为原始。

让我们设置一个可以用来检查不同操作行为的物品:

```js
spyObject = {
    toString() {
        console.log("toString() invoked!");
        return "10";
    },
    valueOf() {
        console.log("valueOf() invoked!");
        return 42;
    },
};
```

此对象定义两个`toString()`和`valueOf()`方法,每个方法返回不同的值类型(`string`数字`number`).

让我们试试我们已经看到的一些强制行动:

```js
String(spyObject);
// toString() invoked!
// "10"

spyObject + "";
// valueOf() invoked!
// "42"
```

呜! 我敢打赌,这令你们中的几个读者感到惊讶;这当然是我做的。 人们常说`String(..)`和`+ ""`等同的激活形式`ToString()`操作。 但他们显然不是!

区别在于每一次行动所提供的`ToPrimitive()`. `String(..)`明确规定`"string"`作为 *int *,而`+ ""`idiom 不提供 *hint * (类似于 *hinting *)`"number"`) (中文(简体) ). 但不要错过这个细节:`+ ""`引用`valueOf()`,当它返回 a`number`原始值为`42`,然后将该值强制用于字符串(通过`ToString()`因此我们得到`"42"`改为`42`.

让我们继续:

```js
Number(spyObject);
// valueOf() invoked!
// 42

+spyObject;
// valueOf() invoked!
// 42
```

这个例子意味着:`Number(..)`和闲人,`+`运算符都相同`ToPrimitive()`胁迫(带有`"number"`)),就我们的情况而言,返回`42`因为那已经是一个`number`如所要求,该值将不再出现。

但是,如果一个`valueOf()`返回 a`bigint`?

```js
spyObject2 = {
    valueOf() {
        console.log("valueOf() invoked!");
        return 42n;  // bigint!
    }
};

Number(spyObject2);
// valueOf() invoked!
// 42     <--- look, not a bigint!

+spyObject2;
// valueOf() invoked!
// TypeError: Cannot convert a BigInt value to a number
```

我们在早先的"致数字"一节中看到了这种区别. 联合来文允许对`42n`比值`42`数字值,但它否定了它所认为的“隐性”胁迫形式。

怎么样`BigInt(..)`(无)`new`关键词)强制功能?

```js
BigInt(spyObject);
// valueOf() invoked!
// 42n    <--- look, a bigint!

BigInt(spyObject2);
// valueOf() invoked!
// 42n

// *******************************

spyObject3 = {
    valueOf() {
        console.log("valueOf() invoked!");
        return 42.3;
    }
};

BigInt(spyObject3);
// valueOf() invoked!
// RangeError: The number 42.3 cannot be converted to a BigInt
```

再说一遍,正如我们在"数字"栏目中看到的,`42`可以安全地强迫`42n`另一方面,`42.3`无法安全地强迫`bigint`.

我们已经看到了`toString()`和`valueOf()`以各种方式援引`string`和`number` / `bigint`进行胁迫。

#### 没有找到原始的?

若为`ToPrimitive()`无法生成原始值, 将丢弃例外:

```js
spyObject4 = {
    toString() {
        console.log("toString() invoked!");
        return [];
    },
    valueOf() {
        console.log("valueOf() invoked!");
        return {};
    }
};

String(spyObject4);
// toString() invoked!
// valueOf() invoked!
// TypeError: Cannot convert object to primitive value

Number(spyObject4);
// valueOf() invoked!
// toString() invoked!
// TypeError: Cannot convert object to primitive value
```

如果你打算定义习惯 初级胁迫通过`toString()` / `valueOf()`一定要从其中至少一个回来!

#### 对象到布尔

怎么样`boolean`强迫物体?

```js
Boolean(spyObject);
// true

!spyObject;
// false

if (spyObject) {
    console.log("if!");
}
// if!

result = spyObject ? "ternary!" : "nope";
// "ternary!"

while (spyObject) {
    console.log("while!");
    break;
}
// while!
```

每一个都激活了`ToBoolean()`但是如果你记得以前, 算法从来不代表`ToPrimitive()`; 因此, 我们没有看到“ valueOf( ) ” 被删除。

#### 取消框: 切换到原始

一种特殊的物体形式`ToPrimitive()`被胁迫者:拳击/包装原始人(见第三章)。 这种特定的对象对主的胁迫往往被称为*unboxing*.

考虑:

```js
hello = new String("hello");
String(hello);                  // "hello"
hello + "";                     // "hello"

fortyOne = new Number(41);
Number(fortyOne);               // 41
fortyOne + 1;                   // 42
```

对象包装器`hello`和`fortyOne`上面还有`toString()`和`valueOf()`方法配置到它们上,以类似`spyObject`/ etc 对象来自我们以前的例子。

一个需要小心的特例 与包裹对象原始的 是与`Boolean()`:

```js
nope = new Boolean(false);
Boolean(nope);                  // true   <--- oops!
!!nope;                         // true   <--- oops!
```

记住,这是因为`ToBoolean()`*not* 将对象缩小为原始形式,其形式为`ToPrimitive`它只检查其内部表格中的价值,而且自正常(非兴奋剂)以来,[^ExoticFalsyObjects]物体总是真实的,`true`出来

| 注: |
| :--- |
| 这是一个讨厌的小 得到了。 一定可以证明`new Boolean(false)`内部应配置为异国情调的“假物”。[^ExoticFalsyObjects]不幸的是,这种变化,在JS的历史上已经过去了25年,很容易在节目中造成中断。 因此,联署材料未触及此事。 |

#### 覆盖默认`toString()`

正如我们所看到的,你可以永远定义`toString()`* * 由有关缔约国援引。`ToPrimitive()`强迫。 但另一种选择是推翻`Symbol.toStringTag`:

```js
spyObject5a = {};
String(spyObject5a);
// "[object Object]"
spyObject5a.toString();
// "[object Object]"

spyObject5b = {
    [Symbol.toStringTag]: "my-spy-object"
};
String(spyObject5b);
// "[object my-spy-object]"
spyObject5b.toString();
// "[object my-spy-object]"

spyObject5c = {
    get [Symbol.toStringTag]() {
        return `myValue:${this.myValue}`;
    },
    myValue: 42
};
String(spyObject5c);
// "[object myValue:42]"
spyObject5c.toString();
// "[object myValue:42]"
```

`Symbol.toStringTag`旨在定义一个自定义字符串值,以描述对象在默认情况下的情况`toString()`行动是直接援引的,或通过胁迫间接援引的;在没有胁迫的情况下,所使用的价值是:`"Object"`常规`"[object Object]"`输出。

那个`get ..`语法在`spyObject5c`正在定义一个“改进者 ”。 这意味着当JS试图访问这个`Symbol.toStringTag`作为属性( 正常), 这个 getter 代码会让我们指定的函数被引用来计算结果。 我们可以在此获取器中运行任意的逻辑, 以动态地确定一个字符串 *tag *, 供默认使用`toString()`方法。

#### 覆盖`ToPrimitive`

您可以替换整个默认值`ToPrimitive()`操作任何对象,设置特殊符号属性`Symbol.toPrimitive`具有函数:

```js
spyObject6 = {
    [Symbol.toPrimitive](hint) {
        console.log(`toPrimitive(${hint}) invoked!`);
        return 25;
    },
    toString() {
        console.log("toString() invoked!");
        return "10";
    },
    valueOf() {
        console.log("valueOf() invoked!");
        return 42;
    },
};

String(spyObject6);
// toPrimitive(string) invoked!
// "25"   <--- not "10"

spyObject6 + "";
// toPrimitive(default) invoked!
// "25"   <--- not "42"

Number(spyObject6);
// toPrimitive(number) invoked!
// 25     <--- not 42 or "25"

+spyObject6;
// toPrimitive(number) invoked!
// 25
```

如您所见, 如果您在对象上定义此函数, 它完全用于替换默认`ToPrimitive()`抽象操作。 从`hint`仍然用于此引用的函数( E)`[Symbol.toPrimitive](..)`),在理论上可以执行你自己的版本的算法,引用一个`toString()`, `valueOf()`,或物体上的任何其他方法(`this`页:1

或者您只需手动定义一个返回值,如上所示. 无论如何,JS不会自动援引`toString()`或`valueOf()`方法。。。

| 警报: |
| :--- |
| 如前所述,如果定义是“没有找到原始数据?”`Symbol.toPrimitive`函数实际上不会返回一个原始的值,一个例外将会被扔到无法“... 将对象转换为原始值”上。 确保总是从这样的函数返回一个实际的原始值! |

### 平等

迄今为止,我们所看到的胁迫一直集中在单一的价值观上. 我们现在开始关注平等比较,这种比较必然涉及两种价值观,其中一种或两种价值观都可能受到胁迫。

在本章的早些时候,我们谈到了若干关于价值平等比较的抽象行动。

例如,`SameValue()`操作[^SameValue]这是最严格的平等比较,绝对没有强迫。 最明显的联署材料行动依赖于`SameValue()`为:

```js
Object.is(42,42);                   // true
Object.is(-0,-0);                   // true
Object.is(NaN,NaN);                 // true

Object.is(0,-0);                    // false
```

那个`SameValueZero()`操作 - record, 它只和`SameValue()`通过治疗`-0`和`0`作为不可区分的 -- -- 被用在更多地方,包括:

```js
[ 1, 2, NaN ].includes(NaN);        // true
```

我们可以看到`0` / `-0`错误方向`SameValueZero()`这里:

```js
[ 1, 2, -0 ].includes(0);           // true  <--- oops!

(new Set([ 1, 2, 0 ])).has(-0);     // true  <--- ugh

(new Map([[ 0, "ok" ]])).has(-0);   // true  <--- :(
```

在这些情况下,有一种“强制”对待。`-0`和`0`如无分别. 不,这在技术上并不是一种"胁迫",因为这种类型没有被改变,但我有点模糊了定义,将这个案件纳入我们关于胁迫的更广泛的讨论中。

对比`includes()` / `has()`这里的方法, 激活`SameValueZero()`带着好孩子`indexOf(..)`数组工具, 它会激活`IsStrictlyEqual()`相反。 这个算法比`SameValueZero()`,因为它阻止`NaN`被视作平等的价值观:

```js
[ 1, 2, NaN ].indexOf(NaN);         // -1  <--- not found
```

如果这些细微的奇特`includes(..)`和`indexOf(..)`当搜索- 寻找内部的平等匹配- 在一个数组中的值时, 您可以避免任何“ 强制” 速率和 * 强制 * 最严格的`SameValue()`平等匹配,通过`Object.is(..)`:

```js
vals = [ 0, 1, 2, -0, NaN ];

vals.find(v => Object.is(v,-0));            // -0
vals.find(v => Object.is(v,NaN));           // NaN

vals.findIndex(v => Object.is(v,-0));       // 3
vals.findIndex(v => Object.is(v,NaN));      // 4
```

#### 平等运算符:`==`数字`===`

*强迫 * 参与平等检查的最明显地点是:`==`操作员。 尽管你有任何预想的概念`==`,它的行为极具预测性,确保两种操作在进行平等检查之前都符合类型。

说一些可能或可能不是非常明显的:`==`(和`===`运算符总是返回 a`boolean` (`true`或`false`不论可能发生何种胁迫行为,他们从不归还任何东西。

现在,回顾和回顾在前面一章中讨论的有关步骤。`IsLooselyEqual()`操作。[^LooseEquality]它的行为,因此如何`==`在现实中,只要考虑到这两个事实,就可以进行实际的渗透:

1. 如果两者的操作类型相同`==`行为与`===` -- `IsLooselyEqual()`立即派代表前往`IsStrictlyEqual()`. [^StrictEquality]

    For example, when both operands are object references:

    ```js
    myObj = { a: 1 };
    anotherObj = myObj;

    myObj == anotherObj;                // true
    myObj === anotherObj;               // true
    ```

    Here, `==` and `===` determine that both of their respective operands are of the `object` reference type, so both equality checks behave identically; they compare the object references for equality.

2. 但是,如果操作类型不同,`==`允许胁迫直至它们吻合,并倾向于数字比较;如果可能,它试图胁迫两种操作方式和数字:

    ```js
    42 == "42";                         // true
    ```

    Here, the `"42"` string is coerced to a `42` number (not vice versa), and thus the comparison is then `42 == 42`, and must clearly return `true`.


有了这个知识,我们现在就能消除 共同的神话,只有`===`检查类型和值,同时`==`只检查值。 不对!

事实上,`==`和`===`都具有型态敏感度, 检查操作的种类。 那个`==`操作员允许对不匹配类型进行强制,而`===`禁止任何胁迫。

几乎全世界都认为`==`应避免赞成`===`. 我可能是唯一一个公开主张直截了当的开发者之一。 我想人们喜欢的主要理由`===`除了仅仅符合现状之外,还缺乏时间来实际理解`==`.

我会重新讨论这个话题 来说明为什么`==`结束`===`,稍后在本章"Type 意识到平等"中. 我所问的是,无论你目前对我有多强烈的异议,都尽量保持开放的心态.

#### 努利什胁迫

我们已经看到一些JS的行动是无效的 -- -- 治疗`null`和`undefined`包括经济、社会、文化权利委员会`?.`可选链路运算符和`??`无效的coalescing操作员(参见第1章中的"Null'ish").

不过`==`JS最明显地揭露了无效的强制平等:

```js
null == undefined;              // true
```

也没有`null`也没有`undefined`除了彼此之外,语言上的任何其它价值都将具有强制性。 就是说`==`将这两个值视为不可区分值,这让人类基因组学变得不可分.

你可以利用这种能力:

```js
if (someData == null) {
    // `someData` is "unset" (either null or undefined),
    // so set it to some default value
}

// OR:

if (someData != null) {
    // `someData` is set (neither null nor undefined),
    // so use it somehow
}
```

记住`!=`是否定`==`,则`!==`是否定`===`。不匹配计数`=`除非你想把自己弄糊涂

比较这两种办法:

```js
if (someData == null) {
    // ..
}

// vs:

if (someData === null || someData === undefined) {
    // ..
}
```

两者`if`声明将完全相同。 你宁愿写哪篇,你愿意以后读哪篇?

公平地说,你们有些人更喜欢动词`===`相当。 没关系 我不同意,我觉得`==`此检查的版本是“ much ”。 我也认为`==`版本在风格精神上与其它无产者喜欢的更一致.`?.`和`??`行动。

但另一个小事实你可能会考虑: 在业绩基准中,我运行过多次,JS引擎可以执行单曲`== null`请检查access-date=中的日期值 (帮助)`===`检查。 换句话说,让JS的`==`进行“隐性”的强制,而不是试图“明确”列出两个检查。

我会看到,即使是许多死硬`===`球迷往往承认`== null`至少有一种情况:`==`最好是这样

#### `==`找到布尔了

除了一些强迫性转角案件 我们将在下一节讨论, 可能是最大的 了解`==`和布林有关

注意这里,因为这是人们被咬的最大原因之一, 然后开始轻视,`==`如果你接受我的简单建议(在本节结尾),你永远不会成为受害者!

想想下面的片段 我们假设一下`isLoggedIn`*不* 持有`boolean`价值(`true`或`false`):

```js
if (isLoggedIn) {
    // ..
}

// vs:

if (isLoggedIn == true) {
    // ..
}
```

и竒`if`语句表单。 我们知道`if`期望a`boolean`因此在这种情况下`isLoggedIn`将被迫接受`boolean`使用`ToBoolean()`抽象操作。 相当直接的预测,对不对?

不过看看`isLoggedIn == true`表达式。 你觉得它会同样的行为吗?

如果你的本能是"是的" 你刚刚陷入了一个棘手的小陷阱 在本章的早期,我曾警告说,`ToBoolean()`强制只有在JS行动实际激活该算法时才适用. 在这里,JS似乎必须这样做,因为`== true`很明显是"与布尔有关"的比较

但是没有。 去再读一遍`IsLooselyEqual()`算法(用于`==`)在章节的前面. 继续,我等着 如果你不喜欢我的总结 读一下规格算法[^LooseEquality]它本身。

好吧,你有没有看到 任何提到引用`ToBoolean()`在任何情况下?

没有!

记住: 当两者的类型`==`操作方式不尽相同,它倾向于强迫它们和数字。

里面有什么东西?`isLoggedIn`如果这不是一个`boolean`那么,它可能是一个字符串值 像`"yes"`比如说。 这样子`if ("yes") { .. }`将明确通过有条件的检查并执行块。

但接下来会发生什么?`==`类型`if`有条件? 事情是这样的:

```js
// (1)
"yes" == true

// (2)
"yes" == 1

// (3)
NaN == 1

// (4)
NaN === 1           // false
```

所以换句话说,如果`isLoggedIn`拥有类似`"yes"`,则`if (isLoggedIn) { .. }`块会通过条件检查,但`if (isLoggedIn == true)`检查不会。 唉哟!

狦`isLoggedIn`握住绳子`"true"`?

```js
// (1)
"true" == true

// (2)
"true" == 1

// (3)
NaN == 1

// (4)
NaN === 1           // false
```

脸帕。

以下是一个流行题: 值多少?`isLoggedIn`需要持有两种形式的`if`声明通过的条件?

...

...

...

...

狦`isLoggedIn`正在持有的号码`1`? `1`这是事实,所以`if (isLoggedIn)`表格通过。 而另一个`==`涉及胁迫的形式:

```js
// (1)
1 == true

// (2)
1 == 1

// (3)
1 === 1             // true
```

不过如果`isLoggedIn`而是握着弦`"1"`再说一遍`"1"`是真的,但怎么样`==`强迫?

```js
// (1)
"1" == true

// (2)
"1" == 1

// (3)
1 == 1

// (4)
1 === 1             // true
```

好吧,这样`1`和`"1"`两种价值:`isLoggedIn`能够安全地控制着`true`在一个`==`平等检查。 但基本上没有其他价值是安全的`isLoggedIn`以坚守.

如果支票是,我们也有类似的`== false`在这种比较中,哪些价值观是安全的?`""`和`0`工作时 不过:

```js
if ([] == false) {
    // this will run!
}
```

`[]`是一个真理的价值,但它也强制地等同于`false`♪! 哎哟

我们该拿这些东西做什么?`== true`和`== false`支票吗? 我有一个简单而简单的答案。

永远,永远,在任何情况下, 执行`==`检查比较的两边是否为`true`或`false`数值。 它看起来会表现得像个好人`ToBoolean()`威逼,但狡猾不会,反而会陷入各种威逼的角落(下一节论述). 并避开`===`表格也一样。

当你面对布尔人时, 坚持隐含的强制形式 真正激活`ToBoolean()`,例如,`if (isLoggedIn)`,并远离`==` / `===`表单。

## 胁迫角病例

到目前为止,我一直明确表达我赞成胁迫的意见。 而它*只是一种意见,虽然它是基于解释从研究语言规格和可观察到的JS行为中收集的事实.

这并不是说强迫是完美的。 有几个令人沮丧的角落案件 我们需要知道, 所以我们避免绊入这些坑洞。 以防万一,我对这些转角案件的以下描述,只是我更多的意见. 你的里程可能会不同

### 字符串

我们已经看到一个阵列的弦强制 看起来像:

```js
String([ 1, 2, 3 ]);                // "1,2,3"
```

我个人觉得那超级烦人, 它不包括周围`[ ]`特别是,这导致了这种荒谬:

```js
String([]);                         // ""
```

所以,我们不能说它甚至是一个阵列,因为我们得到的只是空弦? 不错,JS。 这太蠢了 对不起,但它是。 事情变得更糟:

```js
String([ null, undefined ]);        // ","
```

观赏! 我们知道`null`强迫字符串`"null"`,以及`undefined`强迫字符串`"undefined"`。但是如果这些值处于数组中,它们神奇地消失在数组对字符串的强制中。 只有`","`哪怕是暗示我们 阵列里有什么东西 这镇子太傻了

物体怎么办? 几乎是加重,虽然方向相反:

```js
String({});                         // "[object Object]"

String({ a: 1 });                   // "[object Object]"
```

垛 当然,感谢JS在理解物体价值方面毫无帮助。

### 数字

我要揭开我所认为的 "最糟糕的根源" 所有胁迫角案件邪恶。 你准备好了吗?

```js
Number("");                         // 0
Number("       ");                  // 0
```

我仍然在这个地方摇头,我知道这件事已经近20年了. 我还是不明白Brendan的想法

空字符串缺少任何内容;其中没有用于确定数字表示值的任何内容。`0`是绝对 ***NOT *** 缺失/无效数值的数值等值。 你知道我们有什么价值吗? 这很适合沟通?`NaN`甚至不要让我开始 白空间如何剥离 字符串 当强迫到一个数字,`"       "`字符串的处理仍然与`""`为数字强迫目的。

更糟糕的是,记住`[]`强迫字符串`""`通过扩展:

```js
Number([]);                         // 0
```

杜尔! 若为`""`没有强迫`0`记住,这是所有胁迫邪恶的根源! - 那么,这样`[]`不会强迫`0`也一样。

这是荒谬的, 颠倒的宇宙领地。

更温柔,但还是有点烦人:

```js
Number("NaN");                      // NaN  <--- accidental!

Number("Infinity");                 // Infinity
Number("infinity");                 // NaN  <--- oops, watch case!
```

字符串`"NaN"`不被解释为一个可识别的数字值,所以胁迫失败了,产生了(意外的).`NaN`数值。`"Infinity"`明确适用于胁迫,但任何其他外壳,包括:`"infinity"`,将失败,再次产生`NaN`.

这个下个例子,你可能认为根本不是一个角落的案子:

```js
Number(false);                      // 0
Number(true);                       // 1
```

只是程序员会议 语言的遗产 原本没有布尔`true`和`false`价值,我们对待`0`作为`false`,以及`1`作为`true`但走另一个方向是否真的有意义?

这样想:

```js
false + true + false + false + true;        // 2
```

真的吗? 我不认为有 任何病例的治疗`boolean`联合国`number`等同在程序上是任何合理的。 我可以理解的是,出于历史原因:`Boolean(0)`和`Boolean(1)`.

但我觉得`Number(false)`和`Number(true)`(以及任何隐含的胁迫形式)`NaN`没有`0` / `1`.

### 胁迫

为了证明我的观点,让我们把荒谬 升至11级:

```js
[] == ![];                          // true
```

怎么会? 这似乎不可信,一个价值可以被强制等同于它的否定,对不对?

但跟随强制兔洞:

1. `[] == ![]`
2. `[] == false`
3. `"" == false`
4. `0 == false`
5. `0 == 0`
6. `0 === 0`  ->  `true`

我们有三种不同的荒谬 阴谋反对我们:`String([])`, `Number("")`,以及`Number(false)`如果这些都不是真的 就不会发生这种无稽之谈

但让我说清楚一点:`==`'是错. 当然,它在这里得到了责备。 但真正的罪魁祸首是幕后黑手`string`和`number`转角箱。

## 类型意识

我们现在从每个可以想象的角度来切片和骰子审查胁迫,从规格的抽象内部开始,然后转向实际触发胁迫的具体表达和声明.

但这一切有什么意义? 本章中的细节,实际上这整本书一直到现在为止,大多只是琐碎的? 呃,我不这么认为。

让我们回到我在这漫长的篇章开头提出的观察/问题。

关于胁迫的意见(尤其是负面意见)并不缺乏. 几乎普遍持有的立场是,胁迫大多/完全属于联署材料语言设计的坏部分。 但是,尽管存在这种现实,大多数开发者,在大多数联署材料中,都面临着无法避免胁迫的现实。

换句话说,无论你做什么,你都不可能摆脱了解,理解,管理JS的价值类型和转换的需要. 与通常的假设相反,接受一种动态型(或甚至一种弱型)语言,并不意味着** 粗心或不了解类型。

类型意识编程总是,总是比类型无知/不可知编程更好.

### 脚本?

此刻你肯定在想: 为什么我不能用TypeScript 静态地宣布我的所有类型,避免所有动态打字和强制的混淆?

| 注: |
| :--- |
| 我对TypeScript和它在我们的生态系统中所扮演的更大角色有更详细的想法;我会把这些观点保存在附录("TypeScript上的思考")中. |

我们首先谈谈 TypeScript 如何做和不做 帮助类型意识的编程, 正如我所主张的。

TypeScript既是**Statistical typed**(意为类型在作者时间宣布,在编译时间检查),也是**强型**(意为变量/容器被键入,这些关联被强制执行;强型系统也不允许*隐含*强制). TypeScript的最大优点是,它通常会迫使代码的作者,以及代码的读者,面对一个程序的大部分(理想的,所有!)构成的类型. 这绝对是件好事

相比之下,JS是**动态型**(意思是纯粹在运行时发现和管理类型)和**弱型**(意思是变量/容器不打字,因此没有关联执行,因此变量可以持有任何价值型;弱型系统允许任何形式的胁迫).

| 注: |
| :--- |
| 我手织的高度相当高, 而且故意不潜入很多细微的 静态/动力和强/弱的打字谱。 如果你觉得有"实际上..."的冲动 请稍等一下,让我来陈述我的论点 |

### 类型意识 * 没有 * 类型脚本

一个动态型系统是否自动意味着你在编程时对类型认识较少? 很多人会这样说 但我不同意

我完全不认为,宣布静态类型(如TypeScript中的说明)是实现有效类型意识的唯一途径。 显然,支持静态打字的人认为,这是最佳方式。

让我来说明一下类型意识 而不是TypeScript的静态打字。 考虑这一可变声明:

```js
let API_BASE_URL = "https://some.tld/api/2";
```

这话是怎样的,“类型意识”吗? 当然,没有`: string`注释后`API_BASE_URL`但我肯定觉得它"仍然有意识" 我们清楚地看到价值类型(`string`所分配的值的`API_BASE_URL`.

| 警报: |
| :--- |
| 别被那玩意分心了`let`可重新转让的声明(相对于`const`) 联合来文`const`*不*是其类型系统的一个头等特征。 我们并没有真正获得更多的类型意识 仅仅因为我们知道调用一个`const`变量被JS引擎否决. 如果代码结构良好 -- -- 以类型意识为优先的结构 -- -- 我们可以阅读代码并清楚地看到`API_BASE_URL`*不* 重新分配,因此仍然是它以前分配的价值类型。 从类型意识的角度看,这其实和它不能被重新分配一样。 |

如果以后我想做一些类似的事情:

```js
// are we using the secure API URL?
isSecureAPI = /^https/.test(API_BASE_URL);
```

и笵硂贺 regular`test(..)`方法期望一个字符串,而且我知道`API_BASE_URL`我知道操作是安全的

同样,因为我知道简单的规则`ToBoolean()`与字符串值相关的强制,我知道这种语句也是安全的:

```js
// do we have an API URL determined yet?
if (API_BASE_URL) {
    // ..
}
```

但如果以后我开始打这样的字:

```js
APIVersion = Number(API_BASE_URL);
```

警笛在我脑中触发了警报 由于我知道有一些非常具体的规则 关于弦值如何强迫数字, 我承认这个操作是** 不**类型安全。 因此,我采取不同的做法:

```js
// pull out the version number from API URL
versionDigit = API_BASE_URL.match(/\/api\/(\d+)$/)[1];

// make sure the version is actually a number
APIVersion = Number(versionDigit);
```

и笵`API_BASE_URL`是一个字符串,我进一步知道其内容的格式包括:`".../api/{digits}"`在结尾。 让我知道正则表达式比赛会成功 所以`[1]`数组访问是安全的。

и笵`versionDigit`将保持一个字符串, 因为正则表达匹配返回。 现在,我知道这是安全的 强迫数字字符串成数字`Number(..)`.

根据我的定义,这种思考, 和那种编码的风格, 是类型意识。 编码中的类型意识意味着要仔细思考这些东西是否会对编码的读者来说是*清楚*和*明显*.

### 类型意识 * 与 * 类型脚本

TypeScript粉丝会指出,TypeScript可以通过类型推论,进行静态打字(执行),而不需要在程序中进行单一类型注解. 因此,我在上一节中分享的所有代码示例,TypeScript也可以处理,并提供其编译时间静态类型执行的风味.

换句话说,TypeScript在类型检查中会给我们同样的好处,无论我们写哪一种:

```ts
let API_BASE_URL: string = "https://some.tld/api/2";

// vs:

let API_BASE_URL = "https://some.tld/api/2";
```

但无闲行. 我们有一些问题需要面对。 首先, TypeScript 会在这里触发错误:

```js
API_BASE_URL = "https://some.tld/api/2";

APIVersion = Number(API_BASE_URL);
// NaN
```

直觉上,*我*想要一个具有类型意识的系统来理解为什么这不安全. 但也许这太过分了 或者如果我们真的定义了 更狭义/特定的类型`API_BASE_URL`变量,而非简单`string`可能有用吗? 我们可以使用 TypeScript 技巧,称为“ Template 文学类型 ”:[^TSLiteralTypes]

```ts
type VersionedURL = `https://some.tld/api/${number}`;

API_BASE_URL: VersionedURL = "https://some.tld/api/2";

APIVersion = Number(API_BASE_URL);
// NaN
```

不,TypeScript 仍然看不出任何问题。 是的,我知道有 解释为什么(如何)`Number(..)`本身已输入)。

| 注: |
| :--- |
| 我想象真正聪明的人 谁知道 TypeScript对于我们如何将自己编织成一个错误有创意的想法。 可能甚至有十几种不同的方式迫使TypeScript触发该代码. 但这不是重点 |

我的观点是,我们不能完全依靠TypeScript类型来解决我们所有的问题,让我们检查一下,并且仍然愉快地不知道各种类型的细微差别,在这种情况下,是胁迫行为。

不过! 你当然反对这一条论据,急于断言,即使TypeScript无法理解某些具体情况,使用TypeScript肯定不会使它变得"软弱*! 对吧?

让我们看看TypeScript要说什么[^TSExample1]关于此行:

```ts
type VersionedURL = `https://some.tld/api/${number}`;

let API_BASE_URL: VersionedURL = "https://some.tld/api/2";

let versionDigit = API_BASE_URL.match(/\/api\/(\d+)$/)[1];
// Object is possibly 'null'.
```

错误表明`[1]`访问不安全,因为如果正则表达式无法在字符串上找到任何匹配,`match(..)`返回时`null`.

你瞧,尽管*I*可以比较正则表达式的写法来解释字符串的内容,即使*I*去麻烦地让TypeScript完全清楚这些具体的字符串内容是什么,但将这两个字排成一行并不够聪明,看得出假设匹配发生实际上完全的类型安全.

| 提普: |
| :--- |
| 它是否真正是一个能识别类型工具的工作和最佳用途,以表达每一种可能的安全类型的细微差别? 我们不需要完善和通用的工具 从他们能做的事物中获得巨大的利益。 |

此外,将上一节中的代码样式与本节中的代码(有或没有说明)进行比较,是否TypeScript实际上使我们的编码更加有类型意识?

喜欢,这样做`type VersionedURL = ..`和`API_BASE_URL: VersionedURL`*实际上* 使我们的代码更清晰的 类型意识? 我未必这么认为。

### 类型脚本智能

是的,我听到你尖叫 通过计算机屏幕对我。 是的,我知道TypeScript向您的代码编辑器提供了它发现(或推断)的什么类型的信息,它以智能自动完成,帮助内线警告标记等形式通过.

但我认为即使那些 * 本身并不让你更加体会开发者的类型

为什么? 因为类型意识不只是关于写作经历。 这也涉及到阅读经验,也许更是这样. 并不是所有读到代码的地方/机制,都能从所有的额外智能中受益.

听着 语言服务器的魔法 将情报输入你的代码编辑器 无疑是惊人的 很酷 超有帮助的

我不抱怨TypeScript 作为一种工具 来推断我的**JS代码** 并通过令人愉快的代码编辑器集成给我提示和建议。 我只是不一定想用某种非常具体的方式 来说明类型信息 只是为了平息工具的抱怨

### 类型上方栏脚本

但即使我做了这些, 仍然不够 *** *** 我完全的字型意识, 既作为一个代码作者 也作为一个代码阅读器。

这些工具不会捕捉到每一个可能发生的类型错误,不管我们多么想告诉自己它们可以做到,也不管我们忍受了多少个圈和轮廓来希望它能够做到. 通过无休止地增加语法技巧的复杂程度 来捕捉这些细微错误的工具 所有的努力 充其量是错的

此外,没有这样的工具能够免受虚假的阳性反应的影响, 抱怨那些实际上没有错误的东西; 这些工具永远不会像我们人类那样聪明。 你真的在浪费时间 追寻一些怪异的语法诡计 来减少工具的抱怨

完全没有替代品,如果想真正成为能识别类型的代码作者和代码阅读者,从学习语言的内置类型系统是如何工作的. 是的,这意味着你团队中的每一个开发者 都需要花费精力去学习它。 你不能为了让经验较少的开发者 在项目/团队中 更能实现这些东西。

即使我们允许你避免百分之百的 * 隐性 * 胁迫 -- -- 你不能 -- -- 你绝对会面对 * 显性 * 胁迫 -- -- 所有程序都这样做!

如果你对这个事实的反应是 表明你会把理解它们的精神负担 卸载到TypeScript这样的工具上... 那么我很抱歉告诉你,但你明显和痛苦地 落到了我挑战所有开发者努力争取的*type-aware* bar的下限.

我不是主张你放弃TypeScript 如果你喜欢,很好。 但我非常明确和热情地挑战你们:停止用TypeScript作为拐杖. 停止叩头以安抚TypeScript引擎超机主机. 停止愚蠢地追逐 每一个类型的兔子 每一个综合洞。

从我的观察来看,在使用有类型意识的工具(如TypeScript)和作为代码作者和代码阅读者追求实际类型意识的愿望/努力之间,存在着一种悲剧性的反向关系. 越是依赖TypeScript,越是诱惑和鼓励你将注意力从JS的类型系统(尤其是强制)转移到另类的TypeScript类型系统.

不幸的是,TypeScript永远无法完全逃脱JS的型号系统,因为TypeScript的型号被编译器*废除*,剩下的只是JS,JS引擎必须与之对抗.

| 提普: |
| :--- |
| 想象一下如果有人给你 一杯过滤水喝。 就在你喝一口之前,他们说, “我们从垃圾堆附近的地面上提取了水。 但别担心,我们用了一个非常棒的过滤器,水是完全安全的。” 你有多相信那个过滤器? 如果你能理解水源、过滤过程 以及你手中的玻璃水里的一切, 你会不会觉得更舒服地喝那水? 还是相信这个过滤器足够好? |

### 类型意识平等

我将用一个最后的插图结束这个漫长的章节, 模拟开发者应该如何-- 使用比波段宽度一致主义更具有批判性的思维-- 接近有意识的类型编码, 不管你是否使用类似TypeScript的工具。

我们再次重温平等比较`==`数字`===`),从类型意识的角度. 在本章的早些时候,我曾承诺,我将证明`==`结束`===`开始吧

让我们重述和总结我们所知道的`==`和`===`迄今为止:

1. 如果操作类型`==`匹配,它的行为与`===`.

2. 如果操作类型`===`不匹配,它永远会回来`false`.

3. 如果操作类型`==`不匹配,它将允许强制任一操作(一般倾向于数字类型值),直到类型最终匹配;一旦匹配,见(1).

好吧,我们来分析一下这些事实 分析一下它们如何在我们的节目中互动

如果你正在对`x`和`y`像这样:

```js
if ( /* are x and y equal */ ) {
    // ..
}
```

我们可能处于什么样的条件下?`x`和`y`?

1. 我们可能知道确切的类型`x`和`y`可能是,因为我们知道这些变量是如何分配的。

2. 或者我们可能无法知道这些类型是什么。 也许吧`x`或`y`可以是任何类型,或至少是几种不同类型中的任何一种,因此比较中可能的各种类型组合过于复杂,无法理解/预测。

我们能否同意(1)比(2)好得多? 我们能否进一步同意,(1) 代表以了解类型的方式写出我们的代码,而(2) 则代表绝对不知情的代码?

如果你在使用 TypeScript, 你很可能知道类型`x`和`y`是吗? 即使你没有使用TypeScript,我们已经表明,你可以采取有意的步骤,以这样的方式写出代码,使类型`x`和`y`已知且明显。

#### (2)未知类型.

如果你在假设(2), 我会断言 你的代码处于一个问题状态。 你的密码不理想 您的代码需要重构。 如果你在这个状态下找到密码 最好的办法就是... 修好它!

更改代码,使其具有类型意识. 如果这意味着使用TypeScript,甚至插入一些类型说明,请这样做. 或者,如果你觉得你可以 得到的类型意识状态 与JS*,做到这一点。 无论哪种方式,尽你所能 得到的情况(1)。

如果您不能确保代码进行这种平等比较`x`和`y`具有类型意识,而且你没有其他选择,那么你绝对必须使用`===`严格平等的操作员。 不这样做将是极其不负责任的。

```js
if (x === y) {
    // ..
}
```

如果你不知道这些类型,你(或你代码的其他未来读者) 怎么会知道这些强制步骤是如何形成的?`==`你要表现得好吗? 你不能这么做。

唯一的责任就是避免强迫和使用`===`.

但不要忽视这个事实,你只是在挑剔`===`当您的代码如此无用时, 作为最后的手段。。。 - 没有其他选择。

#### (1)已知类型.

好吧,让我们假设你是在情景(1)。 你知道什么类型`x`和`y`. 代码中非常清楚 参加平等检查的 狭义类型是什么

不错!

但还有两个可能的附加条件:

* (1a): (中文(简体) ).`x`和`y`可能已经是同一种类型,无论两者是`string`s, (帮助)`number`e) 妇女:

* (1b): (中文(简体) ).`x`和`y`可能是不同类型的。

让我们逐个考虑这些案子

##### (1a) 已知的匹配类型

如果平等比较中的类型相符(无论是什么),我们已经知道这一点。`==`和`===`做同样的事情。 绝对没有区别。

不过`==`* 短于一个字符。 大多数开发者本能地认为,最尖端但等同的版本的东西往往最可取. 当然,这不是普遍的,但它至少是一个普遍的偏好.

```js
// this is best
if (x == y) {
    // ..
}
```

在这个特殊情况下,额外`=`对我们来说没有任何帮助 来让代码更清晰。 事实上,这会使比较更糟糕!

```js
// this is strictly worse here!
if (x === y) {
    // ..
}
```

为什么更糟糕?

因为假设(2)我们已经确定`===`用于最后的手段 当我们对能预测结果的种类一无所知时。 我们用`===`当我们想要确保 我们避免胁迫 当我们知道胁迫可能发生。

但这里不适用! 我们已经知道不会发生任何胁迫。 没有任何理由混淆读者与一个`===`在这里。 如果使用的话`===`在一个你已经知道类型的地方-- 而且,它们也是匹配的! - 这实际上可能会给读者发出混合信号。 他们可能以为他们知道 平等检查中会发生什么`===`然后他们再猜自己!

再说一遍 如果你知道什么是平等比较 你知道它们很吻合 只有一个正确的选择`==`.

```js
// stick to this option
if (x == y) {
    // ..
}
```

##### (b) 已知的错配类型

好吧,我们是在我们的最终情景。 我们需要比较一下`x`和`y`,我们知道它们的种类,但我们也知道它们的种类是**NOT**相同的.

我们这里应该用哪个接线员?

如果你选的话`===`你犯了个大错 为什么? 因为`===`与已知的匹配类型使用 永远不会,永远,永远返回`true`永远失败

```js
// `x` and `y` have different types?
if (x === y) {
    // congratulations, this code in here will NEVER run
}
```

摆 这么说`===`当类型已知且不匹配时,则退出。 我们唯一的选择是什么?

事实上,我们还有两种选择。 我们可以决定:

* (1b-1):让我们改变代码,这样我们就不会试图用已知的不匹配类型来进行平等检查;这可能会涉及明确强迫一个或两个值,所以现在它们输入匹配,在这种情况下弹出回情景(1a).

* (1b-2):如果我们要比较已知的不匹配类型以争取平等,我们希望这一检查的任何希望永远过去,我们*必须*使用`==`因为它是平等操作者中 唯一可以强迫一种或两种操作 直到类型吻合

```js
// `x` and `y` have different types,
// so let's allow JS to coerce them
// for equality comparison
if (x == y) {
    // .. (so, you're saying there's a chance?)
}
```

就这样 我们结束了。 我们研究了每一个可能的对类型敏感的平等比较条件(在`x`和`y`).

#### 类型敏感平等比较总结

总是更喜欢`==`结束`===`内容如下:

1. 无论您是否使用 TypeScript - 但特别是如果您*do* 使用 TypeScript - 目标应该是让代码的每个部分,包括所有的平等比较,成为 * type-aware *.

2. 如果你知道类型,你应该总是喜欢`==`.

    - In the case where the types match, `==` is both shorter and more proper for the check.

    - In the case where the types are not matched, `==` is the only operator that can coerce operand(s) until the types match, so it's the only way such a check could ever hope to pass

3. 最后,如果你不知道/预测 类型,出于某种令人沮丧的原因, 而你没有其他选择,`===`作为最后的手段。 并可能增加一个代码注释 在那里承认原因`===`正在使用中,也许可以促使一些未来的开发者以后修改代码,以弥补缺陷并去除拐杖`===`.

#### 类型脚本的不一致问题

让我说清楚:如果你正确使用TypeScript, 你知道平等比较的类型,`===`比较只是简单的错误! 期间。

问题是 TypeScript 奇怪和令人沮丧地仍然需要您使用`===`除非它已经知道这些类型是匹配的。

这是因为TypeScript要么不能完全理解类型意识和强迫,要么——这更令人生气! ——它完全理解,但仍然轻视JS的类型系统,以至于甚至忽略了最基本的类型意识推理.

不相信我吗? 你觉得我太苛刻了吗? 在类型脚本中尝试:[^TSExample2]

```js
let result = (42 == "42");
// This condition will always return 'false' since
// the types 'number' and 'string' have no overlap.
```

我无法形容这对我有多严重。 如果你已经关注了这个漫长,沉重的篇章, 你知道TypeScript 基本上是在说谎。 当然`42 == "42"`将产生`true`《联合提交的材料》。

这不是谎言,但它暴露了一个根本的真理, 许多人仍然不完全欣赏: TypeScript完全抛出JS类型系统的正常规则, 因为TypeScript的立场是JS的类型系统——特别是隐含的强制——是不好的,需要替换.

在TypeScript的世界中,`42`和`"42"`永远不能彼此平等 因此,错误信息。 但是在JS土地,`42`和`"42"`绝对是相互强制的。 我相信我在这里做了一个有力的理由, 他们应该被假设 安全强制等同。

更让我困扰的是,TypeScript在这方面有各种各样的不一致之处. TypeScript 完全可以使用此代码中的 * 隐性 * 强制:

```js
irony = `The value '42' and ${42} are coercively equal.`;
```

那个`42`插入句子时被隐含地胁迫到弦上. 为什么TypeScript可以接受这种隐含的强迫,但不能`42 == "42"`隐含的强迫?

TypeScript 也没有关于此代码的抱怨:

```js
API_BASE_URL = "https://some.tld/api/2";
if (API_BASE_URL) {
    // ..
}
```

为什么`ToBoolean()`暗中胁迫,但`ToNumber()`输入`==`算法不是吗?

我留你来思考一下:你是否真的认为写出最终会用JS引擎运行的代码是一个好主意,但使用一种故意弹出JS语言大部分支柱的代码工具和风格? 此外,它是否也带有各种不一致的例外,仅仅为了适应JS开发者的旧习惯而翻转,这是否很好?

## 左边是什么?

我希望你现在能更了解JS的类型系统如何运作, 从原始价值类型到对象类型, 以及发动机如何进行类型强制。

更重要的是,你现在还更完整地了解了我们使用JS类型系统所作选择的利弊,例如在不同地点选择“隐性”或“明确”胁迫。

但我们还没有完全涵盖类型系统运行的背景. 在本书的其余部分,我们将转而关注联署机构的语法/语法规则,这些规则规范操作者和言论的行为.

[^EichCoercion]:"JavaScript的状态——Brendan Eich",评论线条,Hacker News;2012年10月9日;https://news.ycombinator.com/item?id=4632704; 2022年8月访问

[^CrockfordCoercion]:"JavaScript: The World's Most Misunderstood Programming Language";2001年;;.https://www.crockford.com/javascript/javascript.html; 2022年8月访问

[^CrockfordIfs]:"json2.js", Github; Apr 21 2018; (中文(简体) ).https://github.com/douglascrockford/JSON-js/blob/8e8b0407e475e35942f7e9461dab81929fcc7321/json2.js#L336; 2022年8月访问

[^BrendanToString]:ES讨论邮件列表;2014年8月26日;https://esdiscuss.org/topic/string-symbol#content-15; 2022年8月访问

[^AbstractOperations]: " 7.1类型转换 ",ECMAScript 2022语言规格;https://262.ecma-international.org/13.0/#sec-type-conversion; 2022年8月访问

[^ToBoolean]: "7.1.2 To Boolean(参数)",ECMAScript 2022语言规格; 2.https://262.ecma-international.org/13.0/#sec-toboolean; 2022年8月访问

[^ExoticFalsyObjects]: " B.3.6 The [IsHTMLDDA]] 内部槽 ",ECMAScript 2022语言规格;https://262.ecma-international.org/13.0/#sec-IsHTMLDDA-internal-slot; 2022年8月访问

[^OrdinaryToPrimitive]: 7.1.1.1 普通初级语言(O,hint),ECMAScript 2022语言规格; 2.https://262.ecma-international.org/13.0/#sec-ordinarytoprimitive; 2022年8月访问

[^ToString]: " 7.1.17 托施特灵(参数)",ECMAScript 2022语言规格; 3.https://262.ecma-international.org/13.0/#sec-tostring; 2022年8月访问

[^StringConstructor]: “22.1.1 弦构造器”,ECMAScript 2022语言规格;https://262.ecma-international.org/13.0/#sec-string-constructor; 2022年8月访问

[^StringFunction]:"22.1.1.1 String(值)",ECMAScript 2022语言规格; 2.https://262.ecma-international.org/13.0/#sec-string-constructor-string-value; 2022年8月访问

[^ToNumber]: "7.1.4 to Number(参数)",ECMAScript 2022语言规格; 3.https://262.ecma-international.org/13.0/#sec-tonumber; 2022年8月访问

[^ToNumeric]: 7.1.3 Toumic(参数),ECMAScript 2022语言规格; 2.https://262.ecma-international.org/13.0/#sec-tonumeric; 2022年8月访问

[^NumberConstructor]: " 21.1.1 数字构造器 ",ECMAScript 2022语言规格;https://262.ecma-international.org/13.0/#sec-number-constructor; 2022年8月访问

[^NumberFunction]:"21.1.1.1数字(值)",ECMAScript 2022语言规格; 2.https://262.ecma-international.org/13.0/#sec-number-constructor-number-value; 2022年8月访问

[^SameValue]:"7.2.11 SameValue(x,y)",ECMAScript 2022语言规格; 3.https://262.ecma-international.org/13.0/#sec-samevalue; 2022年8月访问

[^StrictEquality]:"7.2.16 IsstrettlyEqual(x,y)",ECMAScript 2022语言规格; 2.https://262.ecma-international.org/13.0/#sec-isstrictlyequal; 2022年8月访问

[^LooseEquality]:"7.2.15 IsLoosely Equal(x,y)",ECMAScript 2022语言规格; 1.https://262.ecma-international.org/13.0/#sec-islooselyequal; 2022年8月访问

[^NumericAbstractOps]: “6.1.6 数字类型”,ECMAScript 2022语言规格;https://262.ecma-international.org/13.0/#sec-numeric-types; 2022年8月访问

[^NumberEqual]: 6.1.6.1.13 数字:相等(x,y),ECMAScript 2022语言规格; 2.https://262.ecma-international.org/13.0/#sec-numeric-types-number-equal; 2022年8月访问

[^BigIntEqual]:"6.1.6.2.13 BigInt:equal(x,y)",ECMAScript 2022语言规格; 2.https://262.ecma-international.org/13.0/#sec-numeric-types-bigint-equal; 2022年8月访问

[^LessThan]:"7.2.14 IsLessThan(x,y,Left First)",ECMAScript 2022语言规格; 1.https://262.ecma-international.org/13.0/#sec-islessthan; 2022年8月访问

[^StringPrefix]:"7.2.9 IsStringPrefix(p,q)",ECMAScript 2022语言规格; 2.https://262.ecma-international.org/13.0/#sec-isstringprefix; 2022年8月访问

[^SymbolString]:"String(符号)",ES Discuss邮件列表;2014年8月12日;https://esdiscuss.org/topic/string-symbol; 2022年8月访问

[^ASMjs]:"ASM.js——工作草案";2014年8月18日;http://asmjs.org/spec/latest/; 2022年8月访问

[^TSExample1]:"TypeScript游戏场";https://tinyurl.com/ydkjs-ts-example-1; 2022年8月访问

[^TSExample2]:"TypeScript游戏场";https://tinyurl.com/ydkjs-ts-example-2; 2022年8月访问

[^TSLiteralTypes]:"TypeScript 4.1,模板文体类型"; 3.https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-1.html#template-literal-types; 2022年8月访问
