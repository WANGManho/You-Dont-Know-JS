# 你不了解JS然而:范围与关闭 - 第二版
# 第3章:范围链

第1章和第2章对 " 范围 " (及其部分)作了具体定义,并举例说明了这一概念基础的有用隐喻。 在着手本章之前,请找别人用你自己的话来解释(书面或高声),什么是词汇范围,为什么需要理解.

这似乎是你可能跳过的一个步骤, 但我发现这确实帮助了 花时间重新拟订这些想法 来向其他人解释。 这帮助我们的大脑消化 我们学习的东西!

现在该是挖坚果和螺栓的时候了,所以期望从这里向前看事情会得到更多细节. 坚持下去,但是,因为这些讨论 真正的锤子回家 只是多少 我们都不知道* 关于范围,但。 确定您需要时间 与文本和所有代码片段提供。

为了刷新我们运行中的例子,让我们回顾一下从第2章图2开始的嵌入式瞄准镜泡的颜色编码插图:

<figure>
    <img src="images/fig2.png" width="500" alt="Colored Scope Bubbles" align="center">
    <figcaption><em>Fig. 2 (Ch. 2): Colored Scope Bubbles</em></figcaption>
    <br><br>
</figure>

在其他范围内嵌入的瞄准镜之间的连接称为瞄准镜链,它决定了可访问变量的路径. 链条是定向的,意思是只向上/向外移动.

## "寻找"是(最)概念

在图2中,注意颜色`students`变量引用`for`- 滚 我们如何确定它是一颗RED(1)大理石?

在第二章中,我们将变量的运行时间访问描述为"Lookup",其中*Engine*必须首先询问当前范围'的*Scope管理器*,如果它知道一个识别符/可变,然后通过巢形范围链(向全球范围)向上/向外走,直到找到,如果有的话。 一旦发现瞄准镜桶中的第一个匹配的命名声明,就会停止搜索.

因此,调查过程决定:`students`是一个RED(1)大理石, 因为我们还没有找到匹配的变量名称 当我们穿越范围链, 直到我们到达 最终RED(1) 全球范围。

同样,`studentID`输入`if`- 声明是BLUE(2)大理石

这个关于运行时间的搜索过程的建议对于概念理解很有用,但实际上事情通常在实际中是如何运作的.

大理石桶的颜色(aka, meta信息,一个变量起源于什么范围)在初始编译处理中被*通常确定*. 因为当时的词典范围几乎已经敲定,一个大理石的颜色不会根据运行期间以后可能发生的任何事情而改变.

由于大理石的颜色是从编译中得知的,并且是不可改变的,这种信息很可能与(或至少可以从)AST中每个变量的条目一起存储(或从中获取);然后信息被构成程序运行时间的可执行指令明确使用.

换句话说,*Engine*(来自第2章)不需要通过一堆瞄准镜来查看一个变量来自哪一个瞄准镜桶. 这个消息已经知道了! 避免对运行时间的搜索需求是词汇范围的关键优化好处。 运行时间运行得更好,不花时间看所有这些外观.

但我刚才说 "... 通常下定决心..." 关于在编译过程中 找出大理石的颜色 那么,在什么情况下,它永远不会在汇编过程中被知道?

考虑参考当前文件中任何可依逻辑获取的范畴中都没有声明的变量——见*Get Started *,第一章,其中断言,从JS汇编的角度来看,每个文件都是自己的独立程序. 如果没有发现声明,那就不是‘必然'错误. 运行时间中的另一个文件(程序)可能确实会宣布该变量属于共享的全球范围.

因此,最终确定该变量是否在某些无障碍桶中被适当宣布,可能需要推迟到运行时间.

在文件编译过程中,任何对初始的*未声明的*变量的引用都会作为未彩色的大理石留下;在其它相关文件编译完毕,应用程序运行时间开始之前,这种颜色无法确定. 这种推迟的查询最终会解决变量所在的哪个范围(可能是全球范围)的颜色。

然而,这种检查最多只需要每个变量一次,因为在运行时间里没有其他任何东西可以改变大理石的颜色。

第2章中的"寻找失败"一节涵盖了如果一个大理石在运行时间执行时最终仍然没有彩色,会发生什么.

## 阴影

"Shadowing"可能听起来很神秘,有点草率. 但别担心,这是完全合法的!

我们对这些章节的运行实例使用不同可变名称跨越范围边界。 由于它们都有独特的名字,因此从某种意义上说,如果它们都只储存在一个桶里(像RED(1)),那就无所谓了.

当不同的词典范围桶开始更重要的时候,就是当你有两个或更多的变量时,每个变量都具有不同的词典名称. 单一范围不能有两个或两个以上的同名变量;这种多重引用将假设为一个变量。

因此,如果需要保持两个或两个以上的同名变量,则必须使用单独的(通常为巢)瞄准镜. 那样的话,不同的瞄准镜桶是如何布置的,这非常相关.

考虑:

```js
var studentName = "Suzy";

function printStudent(studentName) {
    studentName = studentName.toUpperCase();
    console.log(studentName);
}

printStudent("Frank");
// FRANK

printStudent(studentName);
// SUZY

console.log(studentName);
// Suzy
```

| 提普: |
| :--- |
| 在你继续前, 需要一些时间来分析这个代码, 使用我们书中包含的各种技术/元数据。 特别是,确保在这个片段中识别大理石/泡状颜色. 这是好做法! |

那个`studentName`第1行中的变量(`var studentName = ..`) 创建RED(1)大理石. 同名变量被宣布为3号线上的BLUE(2)大理石,该参数位于该线上.`printStudent(..)`函数定义。

什么颜色的大理石会`studentName`出现在`studentName = studentName.toUpperCase()`任务声明和`console.log(studentName)`声明? 全部三个`studentName`参考文献为BLUE(2)。

我们用“看”的概念概念断言,它从目前的范围开始,然后向外/向上工作,一旦找到匹配的变量就停止。 《BLUE》(2)`studentName`马上找到 降排(1)`studentName`甚至从来没有考虑过。

这是词典范围行为的一个关键方面,称为*阴影*. 《BLUE》(2)`studentName`变量(参数)阴影`studentName`。所以,参数是阴影化(阴影化)全球变量。 重复一遍这句话 以确保你有直的术语!

这就是为什么重新分配`studentName`仅影响内(参数)变量: BLUE(2)`studentName`,而不是全球RED(1)`studentName`.

当你选择从外部范围对一个变量进行阴影时,一个直接的撞击是,从该范围向内/向下(通过任何嵌入的瞄准镜),现在任何大理石都不可能像阴影的变量一样配色——(RED(1),在这种情况下). 换言之,任何`studentName`标识符引用将对应该参数变量,绝不是全局`studentName`变量。 从逻辑学上讲,不可能提到全球`studentName`{\fn黑体\fs22\bord1\shad0\3aHBE\4aH00\fscx67\fscy66\2cHFFFFFF\3cH808080}无论在哪个地方`printStudent(..)`函数(或来自任何嵌入范围)。

### 全球无影诡计

请注意: 利用我将要描述的技巧并不是很好的做法,因为它在实用性上是有限的,令你的代码的读者感到困惑,并有可能邀请bugs参加你的程序. 我覆盖它只是因为你可能会在现有的程序里 经历这个行为, 了解正在发生的事情对不被绊倒至关重要。

* 有可能从该变量被遮蔽的范围访问一个全球变量,但不能通过典型的词典标识参考。

全球范围(RED(1)),`var`声明和声明`function`声明还将自己暴露为“全球对象”上的属性(名称与标识符相同),这基本上是代表全球范围的物体。 如果你为浏览器环境写了JS, 你可能承认这个全球对象是`window`这完全不准确,但这对我们的讨论是足够的。 在下一章中,我们将更深入地探索全球范围/对象主题.

考虑此程序, 具体执行为浏览器环境中的独立. js 文件:

```js
var studentName = "Suzy";

function printStudent(studentName) {
    console.log(studentName);
    console.log(window.studentName);
}

printStudent("Frank");
// "Frank"
// "Suzy"
```

注意到`window.studentName`参考文献? 此表达式正在访问全局变量`studentName`作为财产在`window`(我们现在假装是全球物体的同义词) 这是从阴影变量存在的范围内访问阴影变量的唯一方法.

那个`window.studentName`是全球之镜`studentName`变量,而不是单独的快照副本。 在两个方面,一个方面的变化仍然从另一个方面看出来。 你可以考虑`window.studentName`作为访问实际的获取者/获取者`studentName`变量。 事实上,您甚至可以通过在全球对象上创建/设定一个属性来给全球范围添加一个变量。

| 警报: |
| :--- |
| 记住: 只是因为你*可以* 不意味着你*应该*。 不要阴影一个你需要访问的全球变量, 反之,避免利用这个诡计来访问你所跟踪的全球变量。 绝对不要通过创建全球变量来混淆您的代码读者`window`而不是正式声明! |

这个小的"trick"只用于访问一个全球范围变量(而不是一个来自巢状范围的阴影变量),即使如此,也只有一个被宣布为带有`var`或`function`.

其他形式的全球范围宣言并不产生反射的全球对象属性:

```js
var one = 1;
let notOne = 2;
const notTwo = 3;
class notThree {}

console.log(window.one);       // 1
console.log(window.notOne);    // undefined
console.log(window.notTwo);    // undefined
console.log(window.notThree);  // undefined
```

在全球范围之外存在的任何变量(无论它们是如何被宣布的!)都完全无法从被阴影到的范围中看到:

```js
var special = 42;

function lookingFor(special) {
    // The identifier `special` (parameter) in this
    // scope is shadowed inside keepLooking(), and
    // is thus inaccessible from that scope.

    function keepLooking() {
        var special = 3.141592;
        console.log(special);
        console.log(window.special);
    }

    keepLooking();
}

lookingFor(112358132134);
// 3.141592
// 42
```

全球RED(1)`special`由 BLUE (2) 阴影`special`(参数)和BLUE(2)`special`本身被绿色所笼罩(3)`special`内部`keepLooking()`。我们仍然可以访问RED(1)`special`使用间接引用`window.special`但是没有办法`keepLooking()`访问 BLUE(2)`special`持有数字`112358132134`.

### 复制不是访问

我被问到"但是..." 问了几十次 考虑:

```js
var special = 42;

function lookingFor(special) {
    var another = {
        special: special
    };

    function keepLooking() {
        var special = 3.141592;
        console.log(special);
        console.log(another.special);  // Ooo, tricky!
        console.log(window.special);
    }

    keepLooking();
}

lookingFor(112358132134);
// 3.141592
// 112358132134
// 42
```

瓳 这个也是`another`物体技术反驳了我的主张`special`参数是从内部“ 完全无法访问 ”`keepLooking()`不,这个说法仍然正确。

`special: special`正在复制`special`参数变量为另一个容器(同名属性)。 当然,如果你在另一个容器中放置一个值,阴影就不再适用(除非`another`被跟踪了! 但这并不意味着我们正在访问参数`special`这意味着我们正在通过*另一个*容器(物体属性)获取它当时的价值。 我们不能重新指定BLUE(2)`special`参数改为与内部不同的值`keepLooking()`.

另一个"但是...",你可能要提: 如果我用物体或阵列作为数值而不是数字,怎么办?`112358132134`(等等)? (中文(简体) ). 我们能否引用对象,而不是原始价值的复制 "修正" 无法获取?

没有 通过参考副本更改对象值的内容,**不**与从逻辑上访问变量本身相同。 BLUE(2)仍然无法再分配`special`参数。

### 非法阴影

并不是所有声明的组合都允许影子化.`let`可以阴影`var`不过`var`无法阴影`let`:

```js
function something() {
    var special = "JavaScript";

    {
        let special = 42;   // totally fine shadowing

        // ..
    }
}

function another() {
    // ..

    {
        let special = "JavaScript";

        {
            var special = "JavaScript";
            // ^^^ Syntax Error

            // ..
        }
    }
}
```

通知`another()`函数, 内`var special`正在试图声明全函数`special`,其本身是好的(如:`something()`函数)。

此处的语法错误描述表明`special`已经定义了, 但是错误消息有点误导性。`something()`通常, 影子是允许的 只是罚款。

真正的原因 它作为一个`SyntaxError`因为`var`基本上试图"跨越" (或跳过)`let`声明同名,这是不允许的。

跨越边界的禁令实际上止于每个函数边界,因此,这一变体没有例外:

```js
function another() {
    // ..

    {
        let special = "JavaScript";

        ajax("https://some.url",function callback(){
            // totally fine shadowing
            var special = "JavaScript";

            // ..
        });
    }
}
```

总结:`let`(内在范围)总是可以阴影外在范围`var`. `var`(内在范围)只能阴影外在范围`let`如果两者之间有函数边界。

## 函数名称范围

如你所见,现在`function`声明是这样的:

```js
function askQuestion() {
    // ..
}
```

正如第1章和第2章所讨论的那样,`function`声明将在所命名的所附范围(此处为全球范围)中创建标识符。`askQuestion`.

这个节目呢?

```js
var askQuestion = function(){
    // ..
};
```

变量也是如此`askQuestion`正在创建。 不过既然是...`function`表达式——作为值而不是单独声明使用的函数定义——函数本身不会“危险”(见第5章)。

最大的区别之一`function`声明和声明`function`表达式是函数名称标识符发生的情况。 考虑一个名字`function`表达式:

```js
var askQuestion = function ofTheTeacher(){
    // ..
};
```

我们知道`askQuestion`最终在外范围。 但怎么样`ofTheTeacher`标识符? 为正式`function`声明,名称识别符最终出现在外/封闭范围,因此可以合理地假设这里就是这种情况. 不过`ofTheTeacher`被宣布为标识符 ** 函数本身**:

```js
var askQuestion = function ofTheTeacher() {
    console.log(ofTheTeacher);
};

askQuestion();
// function ofTheTeacher()...

console.log(ofTheTeacher);
// ReferenceError: ofTheTeacher is not defined
```

| 注: |
| :--- |
| 其实`ofTheTeacher`* 并不完全在函数范围内。 附录A"扩展范围"将作进一步解释. |

不仅如此`ofTheTeacher`声明在函数内部而不是外部,但是它也被定义为只读:

```js
var askQuestion = function ofTheTeacher() {
    "use strict";
    ofTheTeacher = 42;   // TypeError

    //..
};

askQuestion();
// TypeError
```

因为我们使用严格的模式, 分配失败被报告为`TypeError`;在非限制性模式中,这种转让毫无例外地无声无息。

那当一个`function`表达式没有名称标识符吗?

```js
var askQuestion = function(){
   // ..
};
```

页:1`function`有名称标识符的表达式称为"名函数表达式",但无名称标识符的表达式称为"无名函数表达式". 匿名函数表达式显然没有影响两个范围的名称标识符。

| 注: |
| :--- |
| 我们来讨论个匿名的`function`更为详细的表述,包括附录A中哪些因素影响决定使用其中一种或另一种。 |

## 箭头函数

ES6 增加了一个`function`对语言的表达式,称为“arrow函数”:

```js
var askQuestion = () => {
    // ..
};
```

那个`=>`箭头函数不需要单词`function`以定义它。 还有`( .. )`环绕参数列表,在某些简单情况下是可选的。 同样,`{ .. }`环绕函数机构在某些情况下是可选的。 当`{ .. }`不使用`return`关键词

| 注: |
| :--- |
| 其吸引力`=>`箭头函数经常作为"较短的语法"出售,这被声称等同于客观上更可读的代码. 这个说法充其量是可疑的,我认为是完全错误的。 我们将挖掘附录A中各种功能形式的"可读性". |

箭头函数在逻辑上是匿名的,这意味着它们没有直接相关的标识符来引用函数. 任务`askQuestion`创建“问询”的推断名称,但这与非匿名不同**:

```js
var askQuestion = () => {
    // ..
};

askQuestion.name;   // askQuestion
```

箭头功能实现它们的合成简洁,而牺牲了在精神上为不同形式/条件拼凑一系列的变异. 例如:

```js
() => 42;

id => id.toUpperCase();

(id,name) => ({ id, name });

(...args) => {
    return args[args.length - 1];
};
```

我提出箭头函数的真正原因,是因为人们通常但错误地声称箭头函数的行为与标准不同。`function`函数。

这是不正确的。

除了匿名(没有申报形式),`=>`箭头函数具有与`function`函数有。 箭头函数,有或无`{ .. }`仍会形成一个单独的 内嵌式瞄准镜 这个嵌入式瞄准镜桶内的可变声明 表现与在`function`范围。

## 备份退出

当函数(声明或表达式)被定义时,会创建一个新的范围. 瞄准镜的定位互相嵌入,在整个计划中形成了自然的瞄准镜等级,称为瞄准镜链. 范围链控制可变接入,方向向上和向外.

每个新范围都提供了一个干净的板块,一个空间来持有自己的一组变量. 当一个可变名称在范围链的不同级别重复出现时,会出现阴影,从而阻止从该点向内进入外变量.

随着我们走出这些更细微的细节,下一章将重点转向所有联署方案的主要范围:全球范围。
