# 你不了解JS然而:范围与关闭 - 第二版
# 第一章:范围是什么?

当你写完你最初的几个程序时, 你可能会对创建变量和在其中存储值感到有些自在。 与变量合作是我们编程中最基础性的工作之一!

但是,你可能没有非常仔细地考虑过发动机用来组织和管理这些变量的基本机制. 我不是说电脑上的内存是如何分配的,而是:JS如何知道任何给定的语句可以访问哪些变量,它如何处理两个同名变量?

这些问题的答案采取定义明确的规则的形式,称为范围. 这本书将探索范围的各个方面——它是如何运作的,它有什么用处,可以避免,然后指出指导程序结构的共同范围模式。

我们的第一步是发现JS引擎如何在运行前处理我们的程序。

## 关于此书

欢迎收看《你不认识JS Yet》系列第二集! 如果你已经完成了 * 开始 * (第一本书), 你的位置是正确的! 如果没有,在你们开始之前,我鼓励你们从那里开始,以奠定最佳的基础。

我们的重点将是联署材料语言的三个支柱中的第一:范围系统及其功能关闭以及模块设计模式的力量。

JS一般被归类为一种被解释的脚本语言,因此大多数人认为JS程序是用单一的自上而下通过处理的. 但联署材料事实上是在另一个阶段进行解析/编成的,** 在处决开始前**。 在初步解析/汇编阶段,根据范围规则分析代码作者关于将变量、功能和块放在何处的决定。 由此产生的范围结构一般不受运行时间条件的影响。

JS功能本身就是头等值;它们可以像数字或字符串一样被分配和传递. 但由于这些函数持有和访问变量,无论在程序何处最终执行这些函数,它们都保持其原始范围. 这是所谓的关闭。

模块是一种以公共方法为特征的代码组织模式,这些公共方法可以(通过关闭)优先访问模块内部范围内的隐藏变量和功能.

## 编译对解译

你可能之前听说过*代码编译*,但也许它似乎是一个神秘的黑匣子,源代码在一端滑动,另一个端则弹出可执行程序.

虽然这不是神秘或神奇的。 代码汇编是一组步骤,用来处理您的代码文本,并将其变成计算机能够理解的指令列表. 通常情况下,整个源代码会同时变换,产生的指令会被保存为输出(通常是在文件中),以后可以执行.

你可能也听说过代码可以被*解释*,这与被*编译*有什么区别?

解释执行类似的编译任务,因为它将您的程序转变为机器可理解的指令. 但处理模式不同. 与一次全部编译的程序不同,有解释的源代码是逐行转换的;每行或语句在立即着手处理源代码的下一行之前都会执行.

<figure>
    <img src="images/fig1.png" width="650" alt="Code Compilation and Code Interpretation" align="center">
    <figcaption><em>Fig. 1: Compiled vs. Interpreted Code</em></figcaption>
    <br><br>
</figure>

图1显示汇编与方案解释。

这两种处理模式相互排斥吗? 一般来说,是的。 然而,这个问题比较细微,因为解释实际上可以采取其他形式,而不仅仅是在源代码文本上逐行操作. 现代联合来文引擎在处理联合来文方案时,实际上采用了多种编译和解释方式。

回顾我们在《开始》一书第一章中调查了这一专题。 我们的结论是,联署材料最准确地被描述为**编译语言**。 为了读者的利益,以下各节将重新审议和阐述这一说法。

## 编译代码

但是,首先,为什么联署材料的汇编与否甚至重要?

范围主要是在汇编过程中确定的,因此,了解汇编和执行之间的关系是掌握范围的关键。

在经典编译器理论中,一个程序由编译器在三个基本阶段处理:

1. ** 将字符串分割成有意义的(对语言)块,称为符号。 例如,考虑该方案:`var a = 2;`。这个方案可能会被细分为以下符号:`var`, `a`, `=`, `2`,以及`;`. Whitespace 视其是否有意义,可能或不会作为象征而坚持.

    (The difference between tokenizing and lexing is subtle and academic, but it centers on whether or not these tokens are identified in a *stateless* or *stateful* way. Put simply, if the tokenizer were to invoke stateful parsing rules to figure out whether `a` should be considered a distinct token or just part of another token, *that* would be **lexing**.)

2. **Parsing:** 取一串令牌的溪流(阵列),将其变成一棵嵌入式元素的树,这共同代表了程序的语法结构. 这被称为抽象语法树(AST).

    For example, the tree for `var a = 2;` might start with a top-level node called `VariableDeclaration`, with a child node called `Identifier` (whose value is `a`), and another child called `AssignmentExpression` which itself has a child called `NumericLiteral` (whose value is `2`).

3. ** 代码生成器:** 使用 AST 并将其变成可执行代码。 这一部分因语言,其目标平台以及其他因素而有很大差异.

    The JS engine takes the just described AST for `var a = 2;` and turns it into a set of machine instructions to actually *create* a variable called `a` (including reserving memory, etc.), and then store a value into `a`.

| 注: |
| :--- |
| JS引擎的实施细节(利用系统内存资源等)比我们在此挖掘要深得多. 我们将关注我们程序可观察到的行为 让JS引擎管理这些更深层次的系统抽象 |

JS引擎比这三阶段复杂得多。 在解析和代码生成过程中,有步骤优化执行的性能(即崩溃冗余元素). 事实上,代码甚至可以在执行过程中被重新编译和重新优化.

所以,我在这里画的只是粗略的划线。 但你很快会明白 为什么这些细节 我们所覆盖的,即使是在高层次,都是相关的。

JS引擎没有充裕的时间来完成他们的工作和优化,因为JS编译不像其他语言那样提前一步地进行建设. 它通常必须在代码执行前仅以微秒(或更少的!)发生. 为了保证这些限制下最快的性能,JS引擎使用各种诡计(如JIT,懒惰的编译甚至热的重编);这些都远远超出了我们这里讨论的"范围".

### 所需:两个阶段

简而言之,我们可以对JS程序的处理提出的最重要的意见是它(至少)分为两个阶段:首先分析/汇编,然后执行。

分解/汇编阶段与随后的执行阶段是可观察到的事实,而不是理论或意见。 虽然JS的规格没有明确要求"汇编",但它要求的行为基本上只有采用汇编-当时执行的方法才是实用的.

可以观察到三种程序特性来向自己证明这一点:语法错误,早期错误,以及升起.

#### 起始时的语法错误

考虑这一方案:

```js
var greeting = "Hello";

console.log(greeting);

greeting = ."Hi";
// SyntaxError: unexpected token .
```

此程序不会输出( T)`"Hello"`未打印),而是丢出一个`SyntaxError`关于意外`.`符号位于`"Hi"`字符串。 由于语法错误发生在形状好之后`console.log(..)`如果JS逐条执行自上而下,`"Hello"`消息在丢弃语法错误之前被打印。 这不会发生。

事实上,JS引擎在执行第一行和第二行之前,唯一能够知道第三行语法错误的方法是JS引擎在任何程序被执行之前首先分析整个程序.

#### 早期错误

接下来,考虑:

```js
console.log("Howdy");

saySomething("Hello","Hi");
// Uncaught SyntaxError: Duplicate parameter name not
// allowed in this context

function saySomething(greeting,greeting) {
    "use strict";
    console.log(greeting);
}
```

那个`"Howdy"`信件不是打印的,尽管它是一个很好的语句。

相反,就像前一节的片段,`SyntaxError`程序执行前就扔到这里了 在这种情况下,这是因为严格模式(仅选用`saySomething(..)`函数)除其他外,禁止函数具有重复的参数名称;在非限制性模式中总是允许这样做。

抛出的错误不是语法错误, 意思是是一个错误的符号串( 如`."Hi"`)),但在严格模式中,仍然需要按照规格在任何执行开始前作为"早期错误"抛出.

但JS引擎怎么会知道`greeting`参数已复制吗? 它怎么会知道`saySomething(..)`函数在处理参数列表时甚至处于严格模式(即`"use strict"`pragma只出现在之后,在函数体中)?

同样,唯一合理的解释是,在任何执行发生之前,代码必须首先被*充分*解析.

#### 欢呼

最后,考虑:

```js
function saySomething() {
    var greeting = "Hello";
    {
        greeting = "Howdy";  // error comes from here
        let greeting = "Hi";
        console.log(greeting);
    }
}

saySomething();
// ReferenceError: Cannot access 'greeting' before
// initialization
```

说明`ReferenceError`从语句的对齐中发生`greeting = "Howdy"`发生的事情是`greeting`该语句的变量属于下一行的声明,`let greeting = "Hi"`,而不是前一个`var greeting = "Hello"`语句。

JS引擎在抛出错误的线条上唯一能够知道*next语句*会宣布一个同名的区块范围变量(S.`greeting`即JS引擎是否已经在较早的通行证中处理过这个代码,并且已经设置了所有范围及其可变关联. 这种范围与申报的处理只能通过在执行前解析程序来准确完成.

那个`ReferenceError`技术来源于此`greeting = "Howdy"`访问`greeting`冲突被称为临时死亡区(TDZ)。 第5章将更详细地阐述这一点。

| 警报: |
| :--- |
| 常有说法说.`let`和`const`正如刚才所说明的TDZ行为那样,声明没有举起。 但这并不准确。 我们回来解释一下 升起和TDZ`let`/`const`第5章第1节 |

希望现在你确信JS程序 在任何处决开始前都会被解析 但是这能证明它们是编成的吗?

这是一个值得思考的有趣问题。 JS能否分析一个程序,但随后通过*解释在AST**中代表的*操作来执行这个程序,而不**首先编译程序? 是的,这是"可能"。 但是这极不可能,主要因为这样会非常低效的性能明智.

很难想象生产质量的JS引擎会遇到将程序解析成AST的所有麻烦,但不会再将AST转换(aka,"complying")为引擎最高效(二进制)的表示,然后执行.

许多人努力用这个术语来分头, 因为有很多细微的细微的, "其实..." 的插头漂浮着。 但在精神和实践中,引擎在处理JS程序时所做的是**相当相似的汇编**。

将JS分类为汇编语言与其二进制(或字节码)可执行表述的分发模式无关,而是在我们的头脑中明确区分JS代码的处理和分析阶段;这一阶段在* 代码开始执行之前,明显和无可争议地发生。

我们需要适当的心理模型 说明JS引擎如何对待我们的代码 如果我们想有效地理解JS和范围。

## 编译器说话

将注意力转向JS引擎如何识别变量并确定编译程序的范围。

首先,让我们审查一个简单的联合来文方案,用于分析下几章的内容:

```js
var students = [
    { id: 14, name: "Kyle" },
    { id: 73, name: "Suzy" },
    { id: 112, name: "Frank" },
    { id: 6, name: "Sarah" }
];

function getStudentName(studentID) {
    for (let student of students) {
        if (student.id == studentID) {
            return student.name;
        }
    }
}

var nextStudent = getStudentName(73);

console.log(nextStudent);
// Suzy
```

除声明外,一个程序中所有变量/识别符的出现都服务于两个"作用"之一:要么是任务*目标*,要么是值*源*.

(当我第一次在获得计算机科学学位的同时学习编译器理论时,我们分别被教授了这些角色的"LHS"(aka, *目标*)和"RHS"(aka, *source*). 你可能从"L"和"R"中猜到,缩略语意为"左手边"和"右手边",如一个字母的左侧和右侧`=`任务操作员。 然而,任务目标和来源并不总是从字面上出现在某个人的左边或右边.`=`,因此从*目标*/*来源*而不是*左*/*右*的角度思考可能更明确.

你怎么知道一个变量是否是一个*目标*? 检查是否有一个值被指定给它;如果是的话,它是一个*目标*. 如果不是,则变量是一个*源*.

JS引擎要正确处理一个程序的变量,必须首先将每个变量的发生标记为*目标*或*源*. 我们现在开始研究每个角色的确定方式

### 目标

是什么使变量成为"目标"? 考虑:

```js
students = [ // ..
```

这个声明显然是一项任务行动。`var students`部分完全作为汇编时的申报处理,因此在执行期间无关紧要;我们没有说明和重点。 同样的`nextStudent = getStudentName(73)`语句。

但代码中还有另外三种*目标*任务操作也许不那么明显. 其中一个:

```js
for (let student of students) {
```

该语句给`student`每一次循环。 另一种*目标*参考:

```js
getStudentName(73)
```

但是,这是如何分配 一个目标? 仔细观察:论据`73`分配给参数`studentID`.

我们的程序还有最后一个(隐约)*目标*参考。 你能发现吗?

..

..

..

你认出这个了吗?

```js
function getStudentName(studentID) {
```

页:1`function`声明是*目标*参考的特殊情况。 你可以觉得它有点像`var getStudentName = function(studentID)`但这并不准确 标识符`getStudentName`已宣布(在编译时),但`= function(studentID)`部分也通过汇编处理;`getStudentName`函数在范围开始时自动设置,而不是等待`=`待执行的任务语句。

| 注: |
| :--- |
| 这种函数和变量的自动关联被称为"函数提升",详见第五章. |

### 资料来源

因此,我们已经确定了所有五个*目标* 参考在程序。 其他可变引用则必须是*source*引用(因为这是唯一的其他选项!).

内`for (let student of students)`,我们说`student`是一个*目标*,但是`students`是一个 * 来源 * 参考。 声明中`if (student.id == studentID)`,两者`student`和`studentID`* 参考资料。`student`*来源 * 参考文献`return student.name`.

内`getStudentName(73)`, `getStudentName`是一个 * source * 引用( 我们希望能够解决函数引用值)。 内`console.log(nextStudent)`, `console`是一个 * source * 引用, 如`nextStudent`.

| 注: |
| :--- |
| 如果你想知道的话`id`, `name`,以及`log`是所有属性,而不是变量引用。 |

了解*目标*对*来源*的实际重要性是什么? 在第二章中,我们将重新审视这个话题,并论述一个变量的角色如何影响它的仰望(具体地说,如果仰望失败).

## 欺骗:运行时间范围修改

现在应该清楚的是,范围是在编译程序时确定的,一般不应该受到运行时间条件的影响. 然而,在非限制性模式中,技术上仍然有两种欺骗这一规则的方法,在运行时间修改一个程序的范围.

这两种技术都不应该被使用——它们既危险又令人困惑,你还是应该使用严格的模式(如果它们不被允许的话)。 但重要的是要了解他们 以防你在一些节目中碰到他们

那个`eval(..)`函数收到一串代码,以便在程序运行期间在苍蝇上编译和执行。 如果这串代码有`var`或`function`这些声明将修改目前的范围,即:`eval(..)`目前正在执行中:

```js
function badIdea() {
    eval("var oops = 'Ugh!';");
    console.log(oops);
}
badIdea();   // Ugh!
```

如果`eval(..)`当时没有在场,`oops`变量在`console.log(oops)`将不存在,并将扔出一个`ReferenceError`不过`eval(..)`修改本条款的范围`badIdea()`函数在运行时。 这有多种原因,包括每次修改已编译和优化范围的性能打击`badIdea()`运行。

第二个骗局是`with`关键词基本上是动态地将一个对象转化为局部范围——其属性在新范围块中作为标识符处理:

```js
var badIdea = { oops: "Ugh!" };

with (badIdea) {
    console.log(oops);   // Ugh!
}
```

全球范围在此没有修改,但`badIdea`在运行时变成一个范围,而不是编译时间及其财产`oops`成为这个范围的变量。 同样,由于表现和可读性原因,这是一个可怕的想法。

不惜一切代价避免`eval(..)`(至少,`eval(..)`建立声明)和`with`再说一遍,这些骗局都没有严格规则,所以如果你只是使用严格规则,那么诱惑就消失了!

## 语法范围

我们已经证明,JS的范围是在编译时确定的;这种范围的术语是"弹性范围". "Lexical"与编纂的"lexing"阶段有关,如本章前面所讨论的.

将本章缩小为有用的结论,"弹性范围"的关键思想是,它完全通过将函数,块和可变声明放在彼此的关系上来控制.

如果将变量声明放在函数内,编译器会处理此声明作为解析函数,并将该声明与函数的范围联系起来. 如果一个变量被声明为块镜(`let` / `const`),然后它与最近的附件有关`{ .. }`块,而不是其附加功能(如`var`).

此外,一个变量的参考(*目标*或*来源*角色)必须解决,因为它来自其中之一的可被其使用的范围*;否则该变量被说成是"未宣布的"(通常会导致错误!). 如果变量未在当前范围中宣布,则将参考下一个外部/关闭范围。 这种跨出一级范围筑巢的过程一直持续,直到找到匹配的可变声明,或者达到全球范围,无处可去.

需要注意的是,汇编在为范围与变量保留内存方面实际上没有做 * 做任何事情. 目前还没有一个程序被执行.

相反,编译会创建一个所有词典范畴的地图,该地图列出了程序在执行时需要什么. 您可以将这个计划视为插入代码,用于运行时,它定义了所有范围(aka,"lexical environment"),并登记每个范围的所有标识符(变量).

换句话说,虽然范围在编译过程中被识别出来,但实际上直到运行时间才创建,每次需要运行一个范围. 在下一章中,我们将勾画出词汇范围的概念基础.
