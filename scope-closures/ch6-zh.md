# 你不了解JS然而:范围与关闭 - 第二版
# 第6章:限制范围

迄今为止,我们的重点是解释范围和变量如何发挥作用的力学。 随着这一基础的牢固建立,我们更加注意更高的思维水平:决定和模式适用于整个方案。

首先,我们将研究我们应如何和为什么使用不同程度的范围(功能和块)来组织我们程序的变量,特别是减少范围过度暴露.

## 最小接触量

功能可以定义它们自己的范围,这是有道理的. 但是,为什么我们也需要块块来建立瞄准镜?

软件工程阐明一个基本学科,一般适用于软件安全,称为"最少特权原则"(POLP).[^POLP]而适用于我们目前讨论的这一原则的一个变体,一般被标注为"东接触"(POLE).

POLP表达对软件架构的防御姿态:系统组件的设计应当以最少的特权,最少的接入,最少的曝光来运行. 如果每个部件都与最低必要能力相连接,从安全角度看,整个系统更强大,因为一个部件的折中或失败对系统其他部分的影响最小。

如果POLP专注于系统层面的组件设计,POLE *Exposure*变体则侧重于较低层面;我们将将其应用于范围如何相互作用.

在跟踪POLE时,我们想怎样才能尽量减少曝光? 简而言之:每个范围登记的变量。

这么想:为什么你不应该把程序的所有变量都放到全球范围呢? 这可能马上觉得是个坏主意 但值得考虑一下 当一部分程序使用的变量通过范围接触到另一部分程序时,经常会出现三个主要危险:

* **Naming Collisions**:如果在程序的两个不同部分使用一个共同和有用的变量/函数名称,但标识符来自一个共享范围(类似于全球范围),那么名称碰撞就会发生,由于一部分使用变量/函数的方式是另一部分不期望的,因此很可能发生错误.

    For example, imagine if all your loops used a single global `i` index variable, and then it happens that one loop in a function is running during an iteration of a loop from another function, and now the shared `i` variable gets an unexpected value.

* ** 意外行为**:如果将原本使用*私用*的变量/功能暴露于程序的一块,它允许其他开发者以你无意的方式使用它们,这可能会违反预期行为并引起错误.

    For example, if your part of the program assumes an array contains all numbers, but someone else's code accesses and modifies the array to include booleans and strings, your code may then misbehave in unexpected ways.

    Worse, exposure of *private* details invites those with mal-intent to try to work around limitations you have imposed, to do things with your part of the software that shouldn't be allowed.

* ** 无意依赖**:如果你不必要地暴露变量/功能,它邀请其他开发者使用并依赖那些否则的 * 私有* 部件。 虽然这不会破坏你今天的程序,但它在未来会造成一个重构的危害,因为现在你无法轻易地重构这个变量或功能,而不会潜在地破坏你无法控制的软件的其他部分.

    For example, if your code relies on an array of numbers, and you later decide it's better to use some other data structure instead of an array, you now must take on the liability of adjusting other affected parts of the software.

POLE, 适用于变量/功能范围界定, 基本上说, 默认暴露 最低限度的必要, 尽可能保持其他的隐私。 将变量尽可能小和深陷在范围内,而不是将一切置于全球(甚至外部功能)范围。

如果你按此设计自己的软件,你更有机会避免(或至少尽量减少)这三种危险。

考虑:

```js
function diff(x,y) {
    if (x > y) {
        let tmp = x;
        x = y;
        y = tmp;
    }

    return y - x;
}

diff(3,7);      // 4
diff(7,5);      // 2
```

在这`diff(..)`函数,我们希望确保`y`大于或等于`x`,这样,当我们减少时,`y - x`),结果为`0`或更大一点。 若为`x`),我们互换。`x`和`y`使用一个`tmp`变量,以保持结果为正数。

在这个简单的例子中,似乎并不重要`tmp`内在`if`块或它是否属于函数级别——它当然不应该是一个全局变量! 然而,根据POLE原则,`tmp`应尽量隐藏在范围中。 所以,我们封锁了范围`tmp`(使用时间)`let`页:1`if`块。

## 隐藏在平原(功能)范围

现在应该清楚为什么必须把我们的变数和功能声明隐藏在尽可能最低(最深的巢穴)范围. 但我们如何这样做?

我们已经看过了`let`和`const`关键词,是块状的显示器;我们很快会再详细讨论。 但首先,藏起来怎么样?`var`或`function`范围的声明? 包起来很容易做到`function`范围围绕宣言。

让我们想个例子`function`范围界定可能有用。

数学运算“ 要素” (注为“ 6! ”) 是将一个给定整数与所有相继较低的整数乘以下至`1`-事实上 你可以停下来`2`自乘数起`1`什么也没做。 换句话说,"6!"与"6*5!"相同,与"6*5*4!"相同,等等. 由于所涉及的数学性质,一旦任何给定的整数的因子(如"4!")被计算出来,我们不应该再做这个工作,因为它永远是同一个答案.

如果你天真地计算出因素`6`,然后想计算`7`,您可能会不必要地重新计算所有整数的因子,从2到6。 如果你愿意用内存来交换速度,你可以通过将每个整数的因子分解为:

```js
var cache = {};

function factorial(x) {
    if (x < 2) return 1;
    if (!(x in cache)) {
        cache[x] = x * factorial(x - 1);
    }
    return cache[x];
}

factorial(6);
// 720

cache;
// {
//     "2": 2,
//     "3": 6,
//     "4": 24,
//     "5": 120,
//     "6": 720
// }

factorial(7);
// 5040
```

我们正在储存所有计算出的元素`cache`因此,跨越多个呼叫`factorial(..)`,则保留以前的计算。 不过`cache`变量显然是一个 私人 细节如何`factorial(..)`工作是有效的,不应该在外部范围——特别是全球范围——暴露的东西。

| 注: |
| :--- |
| `factorial(..)`这里有递归性——从内部发出呼吁——但这只是为了简洁的代码;非递归性执行将产生同样的范围分析。`cache`. |

然而,解决这一过度暴露问题并不像隐藏`cache`内置变量`factorial(..)`可能看起来是这样 因为我们需要`cache`为了在多次通话中幸存下来,它必须位于该功能以外的范围。 我们能怎么办?

界定另一个中间范围(外部/全球范围与`factorial(..)`用于`cache`将定位:

```js
// outer/global scope

function hideTheCache() {
    // "middle scope", where we hide `cache`
    var cache = {};

    return factorial;

    // **********************

    function factorial(x) {
        // inner scope
        if (x < 2) return 1;
        if (!(x in cache)) {
            cache[x] = x * factorial(x - 1);
        }
        return cache[x];
    }
}

var factorial = hideTheCache();

factorial(6);
// 720

factorial(7);
// 5040
```

那个`hideTheCache()`函数除了为`cache`不断发出多次呼吁`factorial(..)`但为了`factorial(..)`有机会接触`cache`我们必须定义`factorial(..)`在同一范围。 然后返回函数引用, 作为值来自`hideTheCache()`,并存储在一个外延变量中,该变量也命名为`factorial`现在,我们叫它`factorial(..)`(数倍!)`cache`隐藏但仅可访问`factorial(..)`!

好吧,但是... 定义(和名称)会很乏味!`hideTheCache(..)`函数范围每次出现这种需要变量/函数隐藏时,特别是因为我们可能希望通过给每个事件一个独特的名称来避免名称与此函数的碰撞. 瓳

| 注: |
| :--- |
| 插图技术——在预期会重复使用相同投入时,将函数的计算输出用于优化性能——在功能方案编制(FP)世界中相当常见,这种插图被称为“memoization”,它依赖于关闭(见第7章)。 另外,还有记忆使用问题(附录B"关于记忆的一句话"中述及). FP图书馆通常将提供一个经过优化和审查的功能记忆工具,取代`hideTheCache(..)`在这里。 记忆超越了我们讨论的*scope *(pun intendent!),但可参见我的"功能-光JavaScript*书"以了解更多信息. |

也许更好的解决办法是使用函数表达式:

```js
var factorial = (function hideTheCache() {
    var cache = {};

    function factorial(x) {
        if (x < 2) return 1;
        if (!(x in cache)) {
            cache[x] = x * factorial(x - 1);
        }
        return cache[x];
    }

    return factorial;
})();

factorial(6);
// 720

factorial(7);
// 5040
```

单单 这仍然在使用一个函数来创建隐藏空间`cache`,在这种情况下,函数仍然命名为`hideTheCache`那么这怎么解决呢?

从“功能名称范围”(第3章)中回顾,名称标识符从一个`function`表达式。 从`hideTheCache(..)`定义为:`function`表达式代替`function`声明,其名称本身的范围——基本上与`cache`- 而不是外部/全球范围。

也就是说,我们可以将这种函数的每一个发生点都命名为完全相同的名称,并且永远没有任何碰撞. 更恰当的是,我们可以根据我们试图隐藏的事物 来命名每个事件, 不用担心我们选择的任何名称 都会与任何其他事物相冲突`function`程序中的表达范围。

事实上,我们只要不说这个名字 就能定义"匿名"`function`表达式。 但附录A将讨论名称的重要性,即使是对于这种仅限范围的职能来说也是如此.

### 立即引用函数表达式

前面的因子递归程序还有一个重要的位点,容易错过: 结尾的行`function`包含的表达式`})();`.

发现我们包围了整个`function`一组中的表达式`( .. )`,然后在结尾,我们加第二`()`括号集;这实际上叫`function`我们刚刚定义的表达式。 此外,在这种情况下,第一套环绕`( .. )`绕函数表达式并非绝对必要(在一瞬间就更多),但我们还是为了可读性而使用它们。

所以,换句话说,我们正在定义一个`function`然后立即被引用的表达式。 这个常见的图案有一个( 非常有创意!)的名称: 立即 Invoked 函数表达式(IIFE).

当我们想要创建隐藏变量/功能的范围时,IIFE是有用的. 由于它是一个表达式,因此可以在允许表达式的JS程序**任何**位置使用. 一个IIFE可以命名,就像`hideTheCache()`,或(更常见的是!)无名/匿名。 它可以是独立的,或者像以前一样,是另一个声明的一部分——`hideTheCache()`返回`factorial()`函数引用( W)`=`分配给变量`factorial`.

比较一下,这里有一个独立IIFE的例子:

```js
// outer scope

(function(){
    // inner hidden scope
})();

// more outer scope
```

和以前不一样`hideTheCache()`,在外侧`(..)`被指为一种可选的型式选择,对于独立的IIFE来说,它们是**必须**的;它们区别于`function`作为表达,而不是声明。 不过,为了保持一致性,必须始终围绕一个IIFE`function`与`( .. )`.

| 注: |
| :--- |
| 从技术上讲,周围`( .. )`并不是唯一能保证`function`IIFE被JS parser视为一种功能表达. 我们将在附录A中考察其他一些选项. |

#### 函数边界

注意使用IIFE来定义一个范围会产生一些意想不到的后果,这取决于它周围的代码. 由于IIFE是一个完整的函数,函数边界会改变某些语句/构造的行为.

例如,a`return`如果将一个IIFE包裹在其中,那么某些代码中的语句会改变它的含义,因为现在`return`将参考国际家庭教育研究所的职能。 非窄函数 IIFEs 也改变 a 的约束`this`关键词 — 更多在 * 对象和类 * 书中。 和声明`break`和`continue`无法跨越IIFE函数边界来控制一个外环或块.

所以,如果代码你需要 包围一个瞄准镜周围有`return`, `this`, `break`,或`continue`在其中,IIFE可能不是最佳方法. 这样的话,你可能会想用一个块而不是一个函数来创建范围.

## 以区块覆盖

到此为止,你应该对创建范围以限制识别资料曝光的优点感到相当满意。

目前为止 我们是通过`function`(即IIFE)范围. 但是,我们现在考虑使用`let`带有嵌入区块的声明。 一般情况下,任何`{ .. }`卷轴对子是一种语句,将起到块的作用,但**不一定**是一个范围。

区块仅在必要时成为包含其区块范围声明(即:`let`或`const`。考虑:

```js
{
    // not necessarily a scope (yet)

    // ..

    // now we know the block needs to be a scope
    let thisIsNowAScope = true;

    for (let i = 0; i < 5; i++) {
        // this is also a scope, activated each
        // iteration
        if (i % 2 == 0) {
            // this is just a block, not a scope
            console.log(i);
        }
    }
}
// 0 2 4
```

不尽然`{ .. }`曲轴对创建块(因此有资格成为范围):

* 使用的对象文字`{ .. }`curly-brace 配对以定义其密钥值列表,但这样的对象值是**不**范围。

* `class`用途`{ .. }`环绕着它的身体定义, 但这不是一个块或范围。

* 页:1`function`用途`{ .. } `但从技术上讲,这不是一个障碍,而是函数的单一说明。 但它是一个(职能)范围。

* 那个`{ .. }`卷发一对`switch`语句`case`条款)没有定义块/范围。

除此类非块式实例外,a`{ .. }`curly-brace 配对可以定义附在语句上的块( 如`if`或`for`或独自一人,或视最外者,`{ .. }`上一个片段中的卷曲结对。 这种明确的块——如果它没有声明,它实际上不是一个范围——就没有任何操作目的,尽管它仍可作为语义信号有用。

明确独立`{ .. }`块一直是有效的JS语法, 但是由于它们不可能是 范围之前的ES6's`let`/`const`它们很罕见 然而,ES6后,他们开始有点赶上.

在大多数支持区块范围界定的语文中,一个明确的区块范围是为一个或几个变量创建一个狭小范围的一个极为常见的模式. 因此,我们应当遵循 " POLE " 原则,在联署材料中也更普遍地采用这种模式;使用(明确)块范围,将识别资料的曝光范围缩小到最低限度。

明确的块范围即使在另一个块内部(不管外块是否是范围)也是有用的.

例如:

```js
if (somethingHappened) {
    // this is a block, but not a scope

    {
        // this is both a block and an
        // explicit scope
        let msg = somethingHappened.message();
        notifyOthers(msg);
    }

    // ..

    recoverFromSomething();
}
```

给`{ .. }`卷轴对** 侧**`if`语句是一个更小的内在明确块范围`msg`,因为整个过程不需要这个变量`if`块。 大部分开发者只是屏蔽镜`msg`页:1`if`挡住并前进。 并且公平,当只有几行可以考虑时,就是一个抛掷式的裁判召唤. 但随着代码的增长,这些过度曝光的问题变得更加明显.

所以,这足够重要 添加额外`{ .. }`成对和缩进水平? 我认为你应该遵循 POLE, 并且总是(在合理范围内!)定义每个变量最小的块. 因此,我建议使用所显示的额外清晰块范围。

回顾"未初始化变量(TDZ)"(第5章)对TDZ错误的讨论. 我的建议是:尽量降低TDZ错误的风险`let`/`const`声明,总是将这些声明置于其范围之首。

如果你发现自己放置一个`let`在范围中间,首先想,"哦,不! TDZ警报!" 如果这样`let`上半个区块不需要申报,你应该用一个内在的清晰区块范围来进一步缩小它的暴露范围!

另一个有明确块范围的例子:

```js
function getNextMonthStart(dateStr) {
    var nextMonth, year;

    {
        let curMonth;
        [ , year, curMonth ] = dateStr.match(
                /(\d{4})-(\d{2})-\d{2}/
            ) || [];
        nextMonth = (Number(curMonth) % 12) + 1;
    }

    if (nextMonth == 1) {
        year++;
    }

    return `${ year }-${
            String(nextMonth).padStart(2,"0")
        }-01`;
}
getNextMonthStart("2019-12-25");   // 2020-01-01
```

首先,我们确定范围及其识别特征:

1. 外部/全球范围有一个标识符,即函数`getNextMonthStart(..)`.

2. 函数范围`getNextMonthStart(..)`有三个:`dateStr`(参数),`nextMonth`,以及`year`.

3. 那个`{ .. }`curly-brace配对定义了一个包含一个变量的内块范围:`curMonth`.

所以为什么说`curMonth`在明确的块范围中,而不是仅仅与`nextMonth`和`year`在顶级函数范围中? 因为`curMonth`仅是前两个语句需要;在功能范围层面,它被过度曝光.

这个例子很小,所以过度暴露的危害`curMonth`相当有限。 但POLE原则的好处最好在您采用默认最小范围暴露的心态时实现,作为习惯. 如果你坚持原则,即使是在小案件里, 它会更有利于你 随着你的计划的增长。

让我们来看看一个更实质性的例子:

```js
function sortNamesByLength(names) {
    var buckets = [];

    for (let firstName of names) {
        if (buckets[firstName.length] == null) {
            buckets[firstName.length] = [];
        }
        buckets[firstName.length].push(firstName);
    }

    // a block to narrow the scope
    {
        let sortedNames = [];

        for (let bucket of buckets) {
            if (bucket) {
                // sort each bucket alphanumerically
                bucket.sort();

                // append the sorted names to our
                // running list
                sortedNames = [
                    ...sortedNames,
                    ...bucket
                ];
            }
        }

        return sortedNames;
    }
}

sortNamesByLength([
    "Sally",
    "Suzy",
    "Frank",
    "John",
    "Jennifer",
    "Scott"
]);
// [ "John", "Suzy", "Frank", "Sally",
//   "Scott", "Jennifer" ]
```

共有6个识别资料被公布在5个不同范围。 所有这些变量是否都存在于单一的外部/全球范围? 从技术上讲,是的,因为它们都是独特的命名,因此没有名称碰撞. 但是,这将是非常糟糕的代码组织, 并可能导致混淆和未来的错误。

我们酌情把它们分成每个巢穴范围。 每个变量都被定义在最内在的范围内,以便程序能够按需要运行.

`sortedNames`可以在顶级函数范围中定义,但仅此功能的后半部分需要. 为了避免在更高水平范围内过度暴露这个变量,我们再次跟随POLE,并在内显块范围内进行区块范围.

### `var`* 和 * (中文(简体) ).`let`

接下来,我们谈谈宣言`var buckets`。该变量用于整个函数(除最后`return`页:1 所有(甚至大多数)功能都需要的任何变量都应宣布,这样这种使用是显而易见的。

| 注: |
| :--- |
| 参数`names`并不是整个函数都使用, 但不能限制一个参数的范围, 所以它表现为一个全函数的宣告,不管怎样。 |

那为什么我们用`var`改为`let`宣布:`buckets`变量? 有语义和技术上的理由来选择`var`在这里。

结构上,`var`一直以来,从JS最早的时代起,就表示"属于整个功能的可变性". 正如我们在“范围”(第1章)中所断言的那样,`var`附加到最近的附件函数范围,无论它在哪里出现。 虽然如此`var`显示在块内:

```js
function diff(x,y) {
    if (x > y) {
        var tmp = x;    // `tmp` is function-scoped
        x = y;
        y = tmp;
    }

    return y - x;
}
```

虽然`var`在区块内,其声明为函数范围(至`diff(..)`),没有挡板镜.

当你可以宣布`var`我建议反对这种做法,除非有少数具体案例(附录A讨论)。 否则`var`应保留用于某个函数的顶级范围。

为何不直接使用`let`在同一地点? 因为`var`视像区别于`let`并因此发出明确的信号,"这个变量是函数范围。" 使用`let`在顶层范围,特别是如果不是在某一函数的前几行,以及当所有其他的区块声明使用时`let`,不以视觉方式提请注意与功能范围声明的区别。

换句话说 我觉得`var`较之`let`有,而且`let`两边都互相交流,`var`不够充分。 只要您的程序既需要函数范围变量,也需要块范围变量,最合理和可读的方法就是同时使用`var`* 和 * (中文(简体) ).`let`和他们一起,各自为己任。

还有其他语义和操作原因可以选择`var`或`let`在不同的情景下。 我们会探索这个案子`var`* 和 * (中文(简体) ).`let`详见附录A。

| 警报: |
| :--- |
| 我建议两样都用`var`* 和 * (中文(简体) ).`let`这显然引起争议,与多数意见相矛盾。 听到诸如"var已经坏了,让我们修复它"和"从来没有使用var,让我们代替它"这样的断言,就更常见了. 这些观点是有效的,但它们只是观点,就像我一样.`var`自早期联署材料以来,它一直在发挥作用,只要联署材料在周围,它就将继续发挥作用。 |

### 向何处去`let`?

我对预备队的建议`var`对于(大多数)只有顶级函数范围意味着大多数其他声明应当使用`let`. 但你可能还在想如何决定 你计划中的每个声明属于哪里?

POLE已经指导你做那些决定了 但我们要明确声明 决定方法并非基于您想要使用的哪一个关键词. 决定的方法是问,“什么是最最小的 范围接触足以满足这个变量?”

一旦得到答案,你会知道一个变量是否属于块范围或函数范围. 如果您最初决定一个变量应该被块-瞄准镜,后来意识到它需要被提升才能被函数-瞄准镜,那么这就要求不仅改变该变量声明的位置,而且改变所使用的声明器关键词. 决策进程确实应该这样进行。

如果声明属于块范围,则使用`let`如果属于函数范围,则使用`var`(再次,只是我的意见).

但另一种将这种决策进行目视化的方法是考虑一个程序的ES6前版本. 例如,让我们回顾`diff(..)`从早先:

```js
function diff(x,y) {
    var tmp;

    if (x > y) {
        tmp = x;
        x = y;
        y = tmp;
    }

    return y - x;
}
```

在这个版本中`diff(..)`, `tmp`在函数范围内明确宣布。 这是否适合`tmp`我会争论,没有。`tmp`这些发言只是需要的。 不需要这个`return`语句。 因此,它应当被划为块状。

在ES6之前,我们没有`let`所以我们不能 "实际上" 挡住它。 但我们可以做下一个最好的事情 来表明我们的意图:

```js
function diff(x,y) {
    if (x > y) {
        // `tmp` is still function-scoped, but
        // the placement here semantically
        // signals block-scoping
        var tmp = x;
        x = y;
        y = tmp;
    }

    return y - x;
}
```

设置`var`声明`tmp`内部`if`语句向密码的读者发出信号:`tmp`属于那个街区。 尽管JS不强制进行范围界定 但语义信号仍然有利于你的密码读者

从这个角度看,你可以找到`var`在一个街区里,然后切换到`let`以执行已发送的语义信号。 这是正确使用`let`在我看来

另一个历史上基于`var`但是现在应该经常使用`let`这是`for`循环:

```js
for (var i = 0; i < 5; i++) {
    // do something
}
```

无论在何地定义了这种循环,`i`基本上应始终只在循环内使用,在这种情况下,POLE规定它应当以下列方式宣布:`let`改为`var`:

```js
for (let i = 0; i < 5; i++) {
    // do something
}
```

几乎是唯一一个切换`var`改为:`let`这样你就能"破解"你的代码了 如果你依赖于访问循环的传动器的话`i`环外/环后,例如:

```js
for (var i = 0; i < 5; i++) {
    if (checkValue(i)) {
        break;
    }
}

if (i < 5) {
    console.log("The loop stopped early!");
}
```

这种使用模式并非非常罕见,但大多数人觉得它闻起来像糟糕的代码结构. 一种可取的做法是为此目的使用另一个外部范围变量:

```js
var lastI;

for (let i = 0; i < 5; i++) {
    lastI = i;
    if (checkValue(i)) {
        break;
    }
}

if (lastI < 5) {
    console.log("The loop stopped early!");
}
```

`lastI`这整个范围都需要, 所以它宣布`var`. `i`只在(每个)循环迭代中需要,所以它与`let`.

### "捕捉记"是什么?

目前为止,我们坚持`var`参数为函数范围,以及`let`/`const`信号屏蔽 有个小例外:`catch`条款。

介绍以来`try..catch`在ES3中(1999年),`catch`条款使用了额外的(鲜为人知的)块范围声明能力:

```js
try {
    doesntExist();
}
catch (err) {
    console.log(err);
    // ReferenceError: 'doesntExist' is not defined
    // ^^^^ message printed from the caught exception

    let onlyHere = true;
    var outerVariable = true;
}

console.log(outerVariable);     // true

console.log(err);
// ReferenceError: 'err' is not defined
// ^^^^ this is another thrown (uncaught) exception
```

那个`err`变量已声明`catch`条款的区块范围是该区块。 这个`catch`条款块可通过`let`不过`var`此块内的声明仍然与外部函数/全球范围有关。

ES2019(最近,在编写本报告时)有所改变`catch`条款,因此其声明是任择声明;如果删除声明,则`catch`block 不再是(默认的)范围了; 但它仍然是一个块!

因此,如果你需要对发生例外的条件 * 作出反应(这样你就可以优雅地恢复),但你并不关心错误值本身,你可以省略`catch`声明:

```js
try {
    doOptionOne();
}
catch {   // catch-declaration omitted
    doOptionTwoInstead();
}
```

这对一个相当常见的用法案例来说是一个小的但令人愉快的简化语法,在去除不必要的范围方面也可能稍有表现!

## 区块中的函数声明 (FiB)

我们已经看到,现在的申报 使用`let`或`const`设有屏蔽镜,`var`声明是功能范围。 那么,怎么样`function`直接出现在块内的声明? 作为特色,这被称为"FiB".

我们通常会想到`function`声明就像他们相当于`var`声明。 他们的功能范围就像`var`是吗?

亦无及是. 我知道... 这很困惑 让我们来挖:

```js
if (false) {
    function ask() {
        console.log("Does this run?");
    }
}
ask();
```

你期望这个计划做什么? 三种合理结果:

1. 那个`ask()`呼叫可能会失败`ReferenceError`例外,因为`ask`标识符为块范围`if`块范围,因此在外部/全球范围内不可用。

2. 那个`ask()`呼叫可能会失败`TypeError`例外,因为`ask`标识符存在,但它是`undefined`(自`if`语句不运行),因此不是一个可调用的函数.

3. 那个`ask()`电话可能会正确运行,打印出“它运行吗?” 消息。

这是令人困惑的部分: 根据JS环境,你尝试代码片段,你可能会得到不同的结果! 这是少数疯狂的领域之一 现有的遗产行为 背叛了可预测的结果。

联合来文指出:`function`区块内部的声明是块状的,所以答案应该是(1). 然而,大多数基于浏览器的JS引擎(包括来自Chrome但也在Node中使用的v8)将表现为(2),即标识符被标定在范围以外.`if`块,但函数值不会自动初始化,所以它仍然`undefined`.

为什么允许浏览器JS引擎的行为违反规格? 因为这些引擎在ES6引入块范围之前已经存在围绕FiB的某些行为,而且有人担心改变遵守规格可能会打破一些现有的网站JS代码. 因此,JS规格附录B中有一个例外,允许浏览器JS引擎的某些偏差(仅限!).

| 注: |
| :--- |
| 你一般不会将节点归类为浏览器JS环境,因为它通常运行在服务器上. 但Node的v8引擎与Chrome(和Edge)浏览器共享. 由于v8首先是一个浏览器JS引擎,它采用了这个附录B的例外,这也就是说浏览器的例外被扩展至节点. |

用于放置`function`在块中声明是有条件地以某种方式定义一个函数(如:`if..else`语句),取决于某些环境状态. 例如:

```js
if (typeof Array.isArray != "undefined") {
    function isArray(a) {
        return Array.isArray(a);
    }
}
else {
    function isArray(a) {
        return Object.prototype.toString.call(a)
            == "[object Array]";
    }
}
```

由于性能原因,我们想用这种方式来构建代码`typeof Array.isArray`检查只执行一次, 而不是只定义一次`isArray(..)`并设置`if`里面的语句——然后检查会不必要地运行在每一个呼叫上。

| 警报: |
| :--- |
| 除了FiB偏差的风险外,有条件定义函数的另一个问题是更难调试这样的程序. 如果你最后会遇到虫子`isArray(..)`函数,您必须先弄清楚 * 哪一个`isArray(..)`正在运行中! 有时,错误是应用错误的,因为有条件的检查是不正确的! 如果定义一个函数的多个版本,该程序总是更难解释和维护. |

除了之前的片段外,其他几个FiB角案例也在潜伏中;在各种浏览器和非浏览器JS环境中的类似行为(JS引擎不基于浏览器)可能会有所不同. 例如:

```js
if (true) {
    function ask() {
        console.log("Am I called?");
    }
}

if (true) {
    function ask() {
        console.log("Or what about me?");
    }
}

for (let i = 0; i < 5; i++) {
    function ask() {
        console.log("Or is it one of these?");
    }
}

ask();

function ask() {
    console.log("Wait, maybe, it's this one?");
}
```

回顾在“何时可以使用变量”中(第5章)所描述的这一功能,可以建议最后`ask()`在片段中,以"等待,也许..."作为其信息, 将高举在呼吁之上`ask()`。因为它是该名称的最后一个函数声明,它应该"胜利",对不对? 不幸的是 没有

我并不打算记录所有这些怪异的角落案件, 也不打算试图解释为什么每个案件都有某种行为方式. 我认为,这一信息是残酷的遗产。

我对FiB真正关心的是 我能给什么建议来确保你的代码在一切情况下都能预测到?

就我而言,唯一能避免FiB变幻莫测的实用答案就是简单地完全避免FiB. 换句话说,永远不要放置`function`直接在任意块内声明。 永远`function`在一个函数的顶级范围(或全球范围)的任何地方作出声明。

所以,对于早期`if..else`例如,我建议尽可能避免有条件地界定职能。 是的,其绩效可能略低,但这是更好的总体办法:

```js
function isArray(a) {
    if (typeof Array.isArray != "undefined") {
        return Array.isArray(a);
    }
    else {
        return Object.prototype.toString.call(a)
            == "[object Array]";
    }
}
```

如果业绩打击成为你申请的关键路径问题,我建议你考虑这一办法:

```js
var isArray = function isArray(a) {
    return Array.isArray(a);
};

// override the definition, if you must
if (typeof Array.isArray == "undefined") {
    isArray = function isArray(a) {
        return Object.prototype.toString.call(a)
            == "[object Array]";
    };
}
```

需要注意的是,我在这里放一个`function`** 表达**,而非声明,在`if`语句。 这是完全好的,有效的,因为`function`显示在块内的表达式。 我们有关FiB的讨论是避免`function`** 以区块表示。

即使你测试了你的程序 并且它工作正确, 你从使用 FiB 风格 从你的代码中可能得到的微小好处 远大于 未来的潜在风险 其他开发者混淆, 或 你代码运行在其他 JS 环境中的差异。

FiB不值得,应该避免。

## 封锁结束

一种编程语言的词典范围界定规则的要点是,为了操作和语义代码通信的目的,我们可以适当地组织我们的程序的变量.

而最重要的组织技术之一是确保任何变量都不会过度暴露于不必要的范围(POLE). 希望你们现在能比以前更深刻地理解区块范围。

希望现在你觉得 你站在更坚实的地上 具有理解的词汇范围 从那个基座上,下一章跳入了"关闭"这个重量级话题.

[^POLP]: *最低特权原则*,https://en.wikipedia.org/wiki/Principle_of_least_privilege,3 March 2020 (英语).
