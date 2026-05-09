# 你不了解JS然而:范围与关闭 - 第二版
# 附录A:进一步探索

我们现在将围绕本书正文所涉及的许多专题探讨一些细微差别和边缘。 本附录为可选,辅助材料.

有些人发现潜入细微的角落, 各种观点只会制造噪音和分散注意力, 我的做法被批评为不切实际和适得其反。 我理解并欣赏这种观点,即使我不一定认同.

我认为,通过了解事物如何运作来增强能力,比用假设和缺乏好奇心来掩盖细节要好。 归根结底,你会遇到一些情况, 某些东西从你没有探索过的地方冒出来。 换句话说,你不会花所有的时间 骑在平滑的*快乐的路径*。 你难道不想为 出门的意外做好准备吗?

这些讨论也将受到我的意见比主要案文的影响更大,因此,在您消费和考虑所介绍的内容时牢记这一点。 这个附录有点像一些细小的博客文章, 长而深于草丛,故宜缓缓,勿急于此.

## 隐含范围

范围有时在非明显的地方产生. 在实践中,这些隐含的瞄准镜并不经常影响你的程序行为,但是知道它们正在发生还是有用的. 注意以下令人惊讶的范围:

* 参数范围
* 函数名称范围

### 参数范围

第二章中的对话比喻意味着函数参数与函数范围内的本地声明变量基本相同. 但并不总是这样

考虑:

```js
// outer/global scope: RED(1)

function getStudentName(studentID) {
    // function scope: BLUE(2)

    // ..
}
```

在这里,`studentID`是一个被认为是"简单"的参数,因此它确实作为BLUE(2)函数范围的成员行为. 但如果我们把它改成非简单参数, 技术上就不再是了。 被视为非简单化的参数表包括带有默认值的参数,休息参数(使用`...`),并解构参数.

考虑:

```js
// outer/global scope: RED(1)

function getStudentName(/*BLUE(2)*/ studentID = 0) {
    // function scope: GREEN(3)

    // ..
}
```

在这里,参数列表基本上成为了它自己的范围,然后函数的范围被嵌入到**范围.

为什么? 这有什么区别? 非简单参数表引入了各种角例,因此参数表成为了自身更有效地处理它们的范围.

考虑:

```js
function getStudentName(studentID = maxID, maxID) {
    // ..
}
```

假设从左到右操作, 默认值`= maxID`联 合 国`studentID`参数要求`maxID`已存在(和已初始化)。 本代码产生TDZ错误(第5章). 原因是`maxID`在参数范围中被宣告,但由于参数的顺序,它尚未初始化. 如果参数顺序被翻转,则不发生TDZ错误:

```js
function getStudentName(maxID,studentID = maxID) {
    // ..
}
```

如果我们将函数表达式引入默认参数位置,复杂度就会在杂草中变得更为复杂,这样就可以在这个隐含参数范围的参数上创建自己的关闭(第七章):

```js
function whatsTheDealHere(id,defaultID = () => id) {
    id = 5;
    console.log( defaultID() );
}

whatsTheDealHere(3);
// 5
```

片段也许有意义,因为`defaultID()`箭头函数关闭于`id`参数/变量,然后我们重新指定`5`但现在让我们介绍一个阴影的定义`id`在函数范围内:

```js
function whatsTheDealHere(id,defaultID = () => id) {
    var id = 5;
    console.log( defaultID() );
}

whatsTheDealHere(3);
// 3
```

瓳瓳 那个`var id = 5`正在跟踪`id`参数,但关闭`defaultID()`函数高于参数,而不是函数体中的阴影变量。 这证明了参数列表周围有一个瞄准镜泡沫.

但比这更疯狂!

```js
function whatsTheDealHere(id,defaultID = () => id) {
    var id;

    console.log(`local variable 'id': ${ id }`);
    console.log(
        `parameter 'id' (closure): ${ defaultID() }`
    );

    console.log("reassigning 'id' to 5");
    id = 5;

    console.log(`local variable 'id': ${ id }`);
    console.log(
        `parameter 'id' (closure): ${ defaultID() }`
    );
}

whatsTheDealHere(3);
// local variable 'id': 3   <--- Huh!? Weird!
// parameter 'id' (closure): 3
// reassigning 'id' to 5
// local variable 'id': 5
// parameter 'id' (closure): 3
```

这里奇怪的是第一个控制台的消息。 在那一刻, 阴影`id`本地变量刚刚`var id`第5章声称的通常自动初始化为`undefined`在它的顶端范围。 为什么它不打印`undefined`?

在这个特定的角落里(由于遗留的类似原因),JS不会自动启动`id`改为:`undefined`,但对于`id`参数 (`3`)!

虽然两者`id`看着这瞬间,就像它们是一个变量, 它们实际上仍然是分开的( 和在单独的瞄准镜中)。 那个`id = 5`转让使差异可以观察到,如果`id`参数保留`3`本地变量变为`5`.

我建议不要被这些怪异的微妙之处咬伤:

* 从不带本地变量的阴影参数

* 避免使用关闭于任何参数的默认参数函数

至少现在你已经意识到了,并且可以小心注意,如果任何参数不简单的话,参数列表就是它自己的范围.

### 函数名称范围

在第三章的"功能名称范围"一节中,我断言函数表达法的名称被添加到函数自身的范围中. 回顾:

```js
var askQuestion = function ofTheTeacher(){
    // ..
};
```

没错`ofTheTeacher`不添加到附件范围(如果`askQuestion`),但它也不只是增加了函数范围,也就是你可能假设的方式. 这是另一个隐含范围奇怪的角落案例.

一个函数表达式的名称标识符在它本身的隐含范围中,嵌入在外包范围与主要内部函数范围之间.

若为`ofTheTeacher`在函数范围内,我们预计这里会出现一个错误:

```js
var askQuestion = function ofTheTeacher(){
    // why is this not a duplicate declaration error?
    let ofTheTeacher = "Confused, yet?";
};
```

那个`let`声明形式不允许重新声明(见第5章)。 但这完全是法律上的影子 而不是重新声明 因为两者`ofTheTeacher`标识符属于单独的范畴。

你很少会遇到任何一个函数的名称标识范围有关系的情况. 但是,我们还是很高兴知道这些机制是如何实际运作的。 为避免被咬伤,永远不要阴影函数名称标识符.

## 匿名对命名函数

如第3章所讨论,功能可以用命名或匿名的形式表示. 使用匿名表格的常见程度要高得多,但这是个好主意吗?

当您考虑命名您的功能时,请考虑:

* 名称推断不完整
* 语法名称允许自引用
* 名称是有用的描述
* 箭头函数没有词典名称
* IIFEs也需要名字

### 明确或暗示的名称?

程序中的每一个功能都有目的. 如果它没有目的,把它拿出来, 因为你只是浪费空间。 如果它*有目的,有*有这个目的的名称。

到目前为止,许多读者都同意我的看法。 但是这是否意味着我们应该一直把这个名字写进代码里? 此处举眉间多. 我毫不含糊地说,是的!

首先,"匿名"在堆积痕迹中出现 并不是所有的帮助调试:

```js
btn.addEventListener("click",function(){
    setTimeout(function(){
        ["a",42].map(function(v){
            console.log(v.toUpperCase());
        });
    },100);
});
// Uncaught TypeError: v.toUpperCase is not a function
//     at myProgram.js:4
//     at Array.map (<anonymous>)
//     at myProgram.js:3
```

唉哟 与报告的内容相比,如果我给出函数名称:

```js
btn.addEventListener("click",function onClick(){
    setTimeout(function waitAMoment(){
        ["a",42].map(function allUpper(v){
            console.log(v.toUpperCase());
        });
    },100);
});
// Uncaught TypeError: v.toUpperCase is not a function
//     at allUpper (myProgram.js:4)
//     at Array.map (<anonymous>)
//     at waitAMoment (myProgram.js:3)
```

看怎么样`waitAMoment`和`allUpper`名称并给堆栈跟踪更有用的信息/文本用于调试? 如果我们所有功能都使用合理的名字,这个程序就更容易调试.

| 注: |
| :--- |
| 不幸者 "&lt;“匿名”一词仍然出现,指的是执行`Array.map(..)`我们的节目里没有,而是编成JS引擎的 这不是从任何混乱 我们的程序引入的可读性快捷键。 |

顺便说一下,让我们确定我们在同一页上 关于一个命名的功能是什么:

```js
function thisIsNamed() {
    // ..
}

ajax("some.url",function thisIsAlsoNamed(){
   // ..
});

var notNamed = function(){
    // ..
};

makeRequest({
    data: 42,
    cb /* also not a name */: function(){
        // ..
    }
});

var stillNotNamed = function butThisIs(){
    // ..
};
```

你说"等等" 其中一些 "被命名了",对不对?

```js
var notNamed = function(){
    // ..
};

var config = {
    cb: function(){
        // ..
    }
};

notNamed.name;
// notNamed

config.cb.name;
// cb
```

这些名称被称为 " 强制 " 名称。 推断出的名字是好的, 但是它们并没有真正解决 我讨论的全部关注。

### 失踪的名字?

是的,这些推断出的名字 可能出现在堆积的痕迹, 这绝对比"匿名"出现要好。 不过...

```js
function ajax(url,cb) {
    console.log(cb.name);
}

ajax("some.url",function(){
    // ..
});
// ""
```

哎哟 匿名组织`function`以回调方式传递的表达式无法接收一个推断名称,所以`cb.name`只持有空字符串`""`绝大多数`function`表达式,尤其是匿名表达式,被用作回调参数;这些表达式都没有获得名称. 因此依靠名字推论是不完整的,充其量也是.

不仅仅是回调 与推论不足:

```js
var config = {};

config.cb = function(){
    // ..
};

config.cb.name;
// ""

var [ noName ] = [ function(){} ];
noName.name
// ""
```

任何转让a`function`非“简单转让”的表达方式也将失败名称推断。 所以,换句话说,除非你谨慎和有意的, 基本上都是匿名的。`function`您程序中的表达方式实际上将没有任何名称。

名字推论只是... 还不够

即便一个`function`表达式 * does * 得到一个推断名称,这个名称仍然不算是一个完整的命名函数.

### 我是谁?

没有词汇名称标识符,该函数没有内部方法来称呼自己. 自我参考对于复发和事件处理等重要:

```js
// broken
runOperation(function(num){
    if (num <= 1) return 1;
    return num * oopsNoNameToCall(num - 1);
});

// also broken
btn.addEventListener("click",function(){
   console.log("should only respond to one click!");
   btn.removeEventListener("click",oopsNoNameHere);
});
```

从您的调用中去掉词典名称, 更难可靠地自我引用此函数。 你*可以* 在一个附加范围的变量中声明一个引用函数的变量,但是这个变量* 被附加范围的变量* 所控制——它可以被重新指定等等——所以它不像它自己拥有内部自我参考的函数那么可靠.

### 名称为描述符

最后,我认为最重要的是,从一个函数中去掉一个名称,使读者更难说出该函数的目的是什么,一目了然。 他们需要阅读更多的代码,包括函数内部的代码,以及函数外的周边代码,才能解析出来.

考虑:

```js
[ 1, 2, 3, 4, 5 ].filter(function(v){
    return v % 2 == 1;
});
// [ 1, 3, 5 ]

[ 1, 2, 3, 4, 5 ].filter(function keepOnlyOdds(v){
    return v % 2 == 1;
});
// [ 1, 3, 5 ]
```

只是没有合理的论点 说** 离开** 名字`keepOnlyOdds`从第一个回调可以更有效地向读者传达这个回调的目的. 你拯救了13个角色,但是失去了重要的可读性信息. 名称`keepOnlyOdds`非常清楚地告诉读者, 在一瞬间,发生了什么。

JS引擎并不关心这个名字. 但人类对你的代码的读者绝对如此。

读者可以看看`v % 2 == 1`想想它在做什么吗? 当然 但是他们必须通过精神上执行代码来推断目的(和名称). 即使是短暂的暂停,这样做也会减缓代码的阅读速度. 良好的描述性名称使得这一过程几乎毫无努力和瞬间.

这样想:这个代码的作者需要多少次才能在代码中添加这个名字之前弄清楚一个函数的目的? 大约一次。 如果需要调整名称,可能要两三次. 但这个代码的读者需要多少次才能知道名字/目的? 每念此行时. 数百次? 成千上万? 还要吗?

无论这个功能的长度和复杂性如何,我的断言是,作者应该想出一个很好的描述性名称,并添加到代码中. 连单行道在`map(..)`和`then(..)`语句应命名为:

```js
lookupTheRecords(someData)
.then(function extractSalesRecords(resp){
   return resp.allSales;
})
.then(storeRecords);
```

名称`extractSalesRecords`告诉读者这个目的`then(..)`*比从精神处决中推断出目的更好*`return resp.allSales`.

在一个函数上不包含一个名字的唯一借口要么是懒惰(不想键入几个额外的字符),要么是缺乏创造力(无法得出一个好的名字). 如果你不知道一个好名字, 你可能还不理解功能和目的。 这个功能也许设计得很差,或者做太多事情,应该重新工作. 一旦你有一个设计完善的,单一目的的功能,它的适当名称就应该变得明显.

我用一个把戏: 首先写一个函数, 如果我不完全理解它的目的, 并且不能想到一个好的名字使用, 我只是用`TODO`作为名字。 这样一来,当我审查我的代码时,我很可能会找到那些名字的占位符,我更倾向于(和更多的准备!)回去想出一个更好的名字,而不只是把它当作是.`TODO`.

所有函数都需要名称。 每服一钱. 无例外。 任何您省略的名字都会使程序更难读取,更难调试,更难扩展和维护.

### 箭头函数

箭头的功能总是**** 匿名的,即使(很少)使用它们的方式给他们一个推断的名字. 我花了几页时间来解释为什么匿名功能是一个坏主意, 所以你也许可以猜我对箭头功能的看法。

别用它们作为常规功能的一般替代. 它们更简洁,是的,但是简洁的 代价是省略了关键的视觉分界器 帮助我们的大脑快速解析我们读到的东西。 至于这次讨论,他们都是匿名的, 这使得他们从这个角度读取能力更差。

箭头函数有一个目的,但这个目的并不是保存按键键. 箭头功能有 * lextic this * 行为, 这有点超出我们在本书中讨论的范围.

简略:箭头函数不定义`this`身份识别关键字。 如果你用一个`this`在箭头函数内,它的表现与任何其他可变的引用完全相同,即参照范围链来寻找其*is*定义的函数范围(非窄函数),并使用该函数。

换句话说,箭头函数处理`this`就像其他词汇变量一样

如果你习惯于黑客`var self = this`,或者如果你喜欢打电话`.bind(this)`内部`function`表达式,只是强迫他们继承`this`从一个外函数 就像它是一个词典变量,然后`=>`箭头函数绝对是更好的选项。 它们是专门用来解决这个问题的

因此,在罕见的情况中,你需要的 *lexic this *,使用箭头函数. 这是工作最好的工具 但是要知道,在这样做的时候, 你接受匿名功能的缺点。 您应该付出更多努力来降低可读性*成本*,比如更多的描述性可变名称和代码注释.

### IIFE 变化

所有功能应具名. 我说了几次,对不对? 包括IIFEs.

```js
(function(){
    // don't do this!
})();

(function doThisInstead(){
    // ..
})();
```

我们怎么给一个IIFE定一个名字? 确定IIFE是为了什么. 为什么你需要一个瞄准镜 在那个位置? 你在隐藏学生记录的缓存变量吗?

```js
var getStudents = (function StoreStudentRecords(){
    var studentRecords = [];

    return function getStudents() {
        // ..
    }
})();
```

我给IFE取了名字`StoreStudentRecords`因为那是它所做的: 存储学生记录。 每个IIFE应该有一个名字。 无例外。

IIFE通常通过放置来定义.`( .. )`环绕`function`表达式,如前几个片段所示。 但这不是定义IIFE的唯一方法. 从技术上讲,我们使用第一套`( .. )`就是这样`function`关键词不能成为`function`向联合来文调查官提出的声明。 但还有其他避免被解析为宣言的方法:

```js
!function thisIsAnIIFE(){
    // ..
}();

+function soIsThisOne(){
    // ..
}();

~function andThisOneToo(){
    // ..
}();
```

那个`!`, `+`, `~`,其他几个无操作器(操作器有一个操作符)都可以放在前面`function`把它变成一种表达 然后是决赛`()`电话是有效的,因此它是一个IIFE。

我其实有点喜欢用`void`定义独立的 IIFE 时的无操作符:

```js
void function yepItsAnIIFE() {
    // ..
}();
```

获得的惠益`void`也就是说,它显然在函数的开头就表示这个IIFE不会返回任何值.

然而,你定义你的IIFE, 给他们一些爱 通过给他们的名字。

## 牵引:函数和变量

第5章既阐述了 " 功能上升 ",也阐明了 " 可变上升 "。 由于在联署材料的设计中,举起常常被引用为错误,我想简要地探讨一下为什么这两种形式的举起*能够有益,并且仍然应该加以考虑。

通过考虑以下各项的优点,给予更深入的审议:

* 可执行代码先, 函数声明最后
* 可变声明的语义定位

### 函数同步

节目之所以能成功,

```js
getStudents();

// ..

function getStudents() {
    // ..
}
```

那个`function`汇编时悬挂声明,这意味着`getStudents`是宣布整个范围的标识符。 此外,`getStudents`标识符是随函数引用自动初始化的,也是在范围开始时。

为什么这样有用? 我更愿意利用“职能提振”的理由是,它将“可执行的”代码置于任何范围的最高位置,并将任何进一步的声明(职能)置于下面。 这意味着更容易找到将在任何特定区域运行的代码,而不是需要滚动和滚动,希望找到一个线索`}`标记某个区域范围/功能的结束。

我利用这一反向定位在各级范围:

```js
getStudents();

// *************

function getStudents() {
    var whatever = doSomething();

    // other stuff

    return whatever;

    // *************

    function doSomething() {
        // ..
    }
}
```

当我第一次打开这样的文件时,第一行就是可以执行的代码,可以踢开它的行为. 这很容易发现! 那么,如果我需要去寻找和检查`getStudents()`我喜欢它的第一行也是可执行代码. 除非我需要看看细节`doSomething()`我到下面找它的定义

换句话说,我认为“功能提升” 使代码通过一个流畅的、渐进的读取顺序,从上到下,更容易读取。

### 可变同步

那"可变的吊"呢?

虽然`let`和`const`升起,你不能在它们的TDZ中使用这些变量(见第5章)。 因此,以下讨论仅适用于`var`声明。 在我继续之前,我承认:在几乎所有情况下,我完全同意,“可变的升起”是一个坏主意:

```js
pleaseDontDoThis = "bad idea";

// much later
var pleaseDontDoThis;
```

虽然这种颠倒命令对"功能提升"很有帮助,但我觉得这通常会使代码更难解释.

但我在自己的编码中发现了一个例外,有些少见. 这与我放在哪里有关`var`在通用JS模块定义中声明。

以下是我如何在节点构建我的模块定义:

```js
// dependencies
var aModuleINeed = require("very-helpful");
var anotherModule = require("kinda-helpful");

// public API
var publicAPI = Object.assign(module.exports,{
    getStudents,
    addStudents,
    // ..
});

// ********************************
// private implementation

var cache = { };
var otherData = [ ];

function getStudents() {
    // ..
}

function addStudents() {
    // ..
}
```

注意到如何`cache`和`otherData`变量位于模块布局的“私有”部分吗? 因为我不打算公开揭露他们 所以,我组织这个模块 这样它们就和模块其他隐藏的 执行细节放在一起。

但我有几件罕见的事例,我需要这些值的任务来完成。 在我宣布模块输出的 API之前。 例如:

```js
// public API
var publicAPI = Object.assign(module.exports,{
    getStudents,
    addStudents,
    refreshData: refreshData.bind(null,cache)
});
```

我需要那个`cache`变量已指定一个值,因为该值用于公共 API 初始化(`.bind(..)`部分适用)。

我应该只是移动`var cache = { .. }`最高,高于这个公共API初始化? 也许吧 但现在不那么明显`var cache`是一个*私人* 执行细节。 这是我(很少)采用的折衷方案:

```js
cache = {};   // used here, but declared below

// public API
var publicAPI = Object.assign(module.exports,{
    getStudents,
    addStudents,
    refreshData: refreshData.bind(null,cache)
});

// ********************************
// private implementation

var cache /* = {}*/;
```

看见了吗? 我已经宣布`cache`从逻辑上讲,在它属于哪个地方, 但在这个罕见的案例中,我用过它 上面,在需要它初始化的地区。 我甚至给分配的值留了点提示`cache`在代码注释中。

这是我唯一找到的 利用“可变悬浮” 来分配一个比其声明更早范围的变量的案例。 但我认为谨慎行事是合理的例外。

## 理由`var`

说到"易变的升起" 让我们好好谈谈`var`一个最爱的恶棍, 在第五章中,我们探索`let`/`const`并答应我们再看看哪里`var`掉进整个组合。

我提出这个案子时,不要错过:

* `var`从来没有断过
* `let`是你的朋友吗?
* `const`用途有限
* 两个世界中最好的:`var`* 和 * (中文(简体) ).`let`

### 别扔了`var`

`var`不错,而且工作也很好。 它已经绕了25年了 它会绕着它 有用和功能 超过25年 声称`var`破碎、腐烂、过时、危险或设计不当都是假的

这是否意味着`var`您的节目中的每一份声明都是正确的声明者吗? 当然不会 但是它在你的节目中仍然占有一席之地 拒绝使用,因为团队中有人 选择了攻击性的观点 窒息`var`就是割掉你的鼻子 来掩饰你的脸

好了,现在我把你惹火了 让我解释一下我的立场

老实说,我很喜欢`let`,用于块状声明. 我真的很讨厌TDZ 我觉得那是个错误 不过`let`自己是伟大的。 我经常用 事实上,我可能用得比我多或多`var`.

### `const`-很困惑

`const`另一方面,我不经常使用。 我不会去探究所有原因的 但最后`const`而不是自己携带的重量 也就是说,虽然有一点好处`const`在某些情况下,这种好处被长期存在的麻烦所抵消。`const`早在JS出现之前,

`const`假装创造出无法变异的价值观,

```js
const studentIDs = [ 14, 73, 112 ];

// later

studentIDs.push(6);   // whoa, wait... what!?
```

使用一个`const`具有可变值(如数组或对象)的,是要求未来的开发者(或读取您的代码)落入你设定的陷阱,即他们要么不知道,要么忘记了,* 值不可变性* 与* 指定不可变性* 完全不一样.

我只是不认为我们应该设置这些陷阱。 我唯一一次用过`const`当我给一个已经不可改变的值时(比如`42`或`"Hello, friends!"`)),当它明显是一个"恒定"时,意思是作为一个被命名的占位符,用于文字价值,语义目的. 就这样`const`用于最佳。 这在我的密码里是很罕见的

如果可变的改派是件大事 那么`const`这将是更有用的。 但是,可变的重新分配 只是不是那么大的问题 在引起bug。 程序里有很多导致错误的东西, 但是"偶然的重新分配"是的方式, 远低于这个清单。

把这件事和事实结合起来`const`(和`let`)应该用在块中,块应该是短的,你有一个非常小的代码区域,一个`const`声明甚至适用。 A级`const`你10号线的1号线 只能告诉你接下来的9条线路 它告诉你的事情已经很明显了, 滑翔下来的这九行: 变量从来没有在左手边一个`=`;没有重新分配.

就这样,就这样`const`确实 除此之外,也没什么用处. 与价值与任务不可改变性的重大混淆相提并论,`const`失去很多它的色素。

页:1`let`(或`var`尽管它没有编译器的保证,但从未被重新分配过的行为已经是"恒定"了. 这在多数情况下都足够了。

### `var`* 和 * (中文(简体) ).`let`

在我脑海里`const`所以这只是两匹马之间的比赛`let`和`var`但这也不是一场真正的比赛, 因为不必只有一个赢家。 他们可以赢得... 不同的种族。

事实是,你应该同时使用`var`和`let`在你的节目。 它们不能互换:你不应该使用`var`区域`let`需要,但你也不应该使用`let`区域`var`这是最合适的。

那么,我们应该在哪里仍然使用`var`在什么情况下,这是一个更好的选择比`let`?

一个人,我总是用`var`在任何函数的顶级范围中,无论该函数的起始,中间还是结尾. 我也会用`var`尽管我试图尽量减少全球范围的使用。

为什么用`var`用于函数范围界定? 因为这正是`var`当然 实际上,没有任何比声明者更好的工具可以用来界定一项宣言的范围。

你*可以 *使用`let`但这不是做这个工作的最佳工具 我也发现,如果你使用`let`随处可见,那么比较不明显的是,哪些声明被设计为局部化的,哪些声明被打算用于整个功能.

相比之下,我很少使用`var`在一个街区。 就这样`let`这是为。 用最好的工具来工作。 如果你看到一个`let`它告诉你,你正在处理 一个本地化的宣言。 如果你看见`var`,它告诉你,你正在处理 一个全函数宣言。 就这么简单

```js
function getStudents(data) {
    var studentRecords = [];

    for (let record of data.records) {
        let id = `student-${ record.id }`;
        studentRecords.push({
            id,
            record.name
        });
    }

    return studentRecords;
}
```

那个`studentRecords`变量用于整个函数。`var`这是最好的声明者 告诉读者。 相比之下,`record`和`id`仅用于循环迭代的较窄范围,因此`let`是完成这项工作的最佳工具。

除了这个 最好的工具 * 语义论证,`var`有一些其他的特征,在某些有限的情况下,这些特征使其更强大。

例如,当循环完全使用变量时,但其条件性条款不能在迭代中看到块范围声明:

```js
function commitAction() {
    do {
        let result = commit();
        var done = result && result.code == 1;
    } while (!done);
}
```

在这里,`result`显然只在街区内使用 所以我们用`let`不过`done`有点不一样 它只对循环有用,但`while`条款无法看见`let`循环中出现的声明。 所以我们妥协并使用`var`,这样`done`被抬到可以看见的外部范围。

备选声明`done`在循环之外——将它与它最初使用的地方分开,或者必须选择一个默认值来指定,或者更糟糕的是,让它没有指定,因此对读者来说模棱两可。 我觉得`var`圈内最好在这里。

另一个有用的特点是:`var`在意想不到的区块内用申报来看待。 无意区块是因语法需要块而创建的区块,但其中开发者的意图并不是真正要创建本地化范围. 意外范围的最佳实例是:`try..catch`语句:

```js
function getStudents() {
    try {
        // not really a block scope
        var records = fromCache("students");
    }
    catch (err) {
        // oops, fall back to a default
        var records = [];
    }
    // ..
}
```

还有其它方法来构建这个代码,是的。 但考虑到各种权衡,我认为这是最佳办法。

我不想说`records`(与`var`或`let`) 范围以外`try`块,然后按一个或两个块分配。 我更希望初始声明总是尽可能接近(理想的,同一行)变量的第一用法. 在这个简单的例子中,那将只是几条线的距离,但是在真正的代码中,它可以发展到更多的线. 差距越大,就越难分辨出 从你分配到什么范围的变量。`var`实际指派时使用的方法使其不那么模糊。

还有,我用过`var`两者`try`和`catch`块。 那是因为我想向读者表明 无论走哪条路`records`总是被宣布。 从技术上讲,这是可行的,因为`var`被提升到函数范围一次。 但还是一个很好的语义信号来提醒读者`var`保证。 若为`var`只有一个区块使用, 而你只读另一个区块, 你不会那么容易发现哪里`records`这是从。

我认为,这是一个小超级大国`var`不仅能逃脱无意中`try..catch`块,但允许在函数的范畴中多次出现. 你不能这样`let`不错,其实是有点有用的 考虑一下`var`更像是一个宣示性注解, 它提醒你,每个用法,变量的来源。 "啊哈,没错,它属于整个功能。"

这种重复式的超能力在其他情况下是有用的:

```js
function getStudents() {
    var data = [];

    // do something with data
    // .. 50 more lines of code ..

    // purely an annotation to remind us
    var data;

    // use data again
    // ..
}
```

第二个`var data`没有重新声明`data`只是为了读者的利益而作注释`data`是一个全函数声明。 这样,读者就不需要卷起50+行的代码来寻找初始的申报.

我完全可以重新利用变量 在整个函数范围实现多重目的 我完全可以用几行代码来分隔变量的两种用法。 在这两种情况下,安全地“重新宣布”的能力(通知)与`var`帮我确定我在哪里`data`不管我身处何方

可悲的是,`let`不能这样做。

还有其他细微差别和情况`var`本来是来帮忙的 但我不想再提这件事了 外卖是`var`对我们的节目有帮助`let`(以及偶尔发生的)`const`) (中文(简体) ). 你愿意创造性地使用联署材料语言提供的工具,向读者讲述更丰富的故事吗?

别就这样丢掉有用的工具`var`因为有人羞辱你 觉得这不再酷了 别回避`var`因为你几年前就糊涂了 学习这些工具,并使用它们各自最擅长的东西.

## TDZ有什么关系?

第5章解释了TDZ(时空死亡区). 我们说明了事情是如何发生的,但是我们略微解释了为什么首先必须介绍。 让我们简单看看TDZ的动机.

TDZ起源故事中的一些面包屑:

* `const`s 永远不应改变
* 是时候了
* 应否`let`表现得更像`const`或`var`?

### 所有开始的地方

TDZ来自`const`事实上。

在ES6早期开发工作期间,TC39必须决定是否`const`(和`let`本来要抬到他们的街区顶端 他们决定这些声明会举起,类似于如何`var`当然 如果没有,我认为有些恐惧与中镜阴影混淆,例如:

```js
let greeting = "Hi!";

{
    // what should print here?
    console.log(greeting);

    // .. a bunch of lines of code ..

    // now shadowing the `greeting` variable
    let greeting = "Hello, friends!";

    // ..
}
```

我们该怎么办呢?`console.log(..)`声明? JS Devs是否觉得写"你好"有道理? 看来那可能是个骗局 只有下半场才会有阴影 但不是上半场 这不太直观,像JS一样的行为。 这么说`let`和`const`整个街区都能看到

不过如果`let`和`const`抬到街区的顶端(如:`var`举起手来,为什么不`let`和`const`自动初始化( 到`undefined`途径`var`是吗? 主要问题是:

```js
{
    // what should print here?
    console.log(studentName);

    // later

    const studentName = "Frank";

    // ..
}
```

让我们想象一下`studentName`不仅被抬到这个街区的顶端,而且还被自动初始化到`undefined`上半个街区`studentName`可以观察到,`undefined`价值,例如我们`console.log(..)`语句。 一旦`const studentName = ..`已到达语句, 现在`studentName`已指定`"Frank"`从这一点出发,`studentName`永远不能重新分配。

但是,一个恒定的有两种不同的价值是奇怪还是令人惊讶的,首先`undefined`,则`"Frank"`?。。。 这似乎违背了我们的想法`const`蚂蚁的意思;它只能用一个值来观察。

所以... 现在我们有麻烦了 我们不能自动启动`studentName`改为:`undefined`(或该事项的任何其他价值)。 但变量必须存在于整个范围。 我们从最初存在(从范围开始)到分配其价值的时间段,我们该怎么办?

我们把这个时期称为"死区",如"时空死区"(TDZ). 为了防止混淆,确定一个变量在其TDZ中的任何类型的访问都是非法的,必须导致TDZ错误.

好吧,我必须承认,这种推理是有道理的。

### 谁`let`TDZ出局了吗?

但这只是`const`。关于`let`?

TC39做出了决定,因为我们需要一个TDZ`const`,我们最好有一个TDZ用于`let`临Τ *事实上,如果我们让一个TDZ, 那么我们劝阻所有那些丑陋的可变的吊人。 * 所以有一个一致性的视角,也许, 一点点的社会工程来改变开发者的行为。

我的反证是: 如果你赞成一致性,就和`var`改为`const`; `let`绝对更像`var`超过`const`。特别是因为他们已经选择了与`var`整个升到顶级的东西。 让`const`成为它自己与TDZ的独特交易, 让TDZ的答案纯粹是:只要通过总是在范围顶端宣布你的常数来避免TDZ. 我觉得这样更合理

可惜它不是这么降落的`let`拥有TDZ是因为`const`需要一个TDZ,因为`let`和`const`模拟`var`在他们升起的高度上, 就这样 太循环了? 次诵一遍.

## 同步召回还是关闭吗?

第7章提出了两种不同的结案模式:

* 关闭是一个函数实例,可以记住其外部变量,即使该函数被绕过并**在其他范围中被引用。

* 关闭是一个函数实例,其范围环境被保留在原地,而任何提及它的内容则从** 其他范围中转来转去。

这些模型并非千差万别,但它们确实从不同的角度着手。 这种不同的视角改变了我们所认为的结束。

不要迷路 通过关闭和召回的兔子小径:

* 唤回什么(或在哪里)?
* 也许"同步回调"不是最好的标签
* 为什么需要关闭?
* 长期推迟是结束的关键

### 什么是回电?

在重新讨论结束语之前,让我花一点时间来谈谈“召回”一词。 一种普遍接受的规范是,说"召回"与*同步召回*和*同步召回*都是同义词. 我不认为我同意这是个好主意,所以我想解释为什么,并提议我们从那个词转到另一个词.

让我们首先考虑一个*同步调用*,这个函数引用将在某个未来*后*点被引用. "召回"是什么意思,在这种情况下?

表示当前代码已经完成或暂停,暂停本身,当相关函数在稍后被引用时,执行会重新进入中止程序,恢复. 具体地说,再入点是函数引用中包裹的代码:

```js
setTimeout(function waitForASecond(){
    // this is where JS should call back into
    // the program when the timer has elapsed
},1000);

// this is where the current program finishes
// or suspends
```

在这种情况下,"召回"很有道理. JS引擎正在恢复我们暂停的程序,在具体地点召回。 好吧,所以回调是同步的。

### 同步回电?

但"同步回调"怎么办? 考虑:

```js
function getLabels(studentIDs) {
    return studentIDs.map(
        function formatIDLabel(id){
            return `Student ID: ${
               String(id).padStart(6)
            }`;
        }
    );
}

getLabels([ 14, 73, 112, 6 ]);
// [
//    "Student ID: 000014",
//    "Student ID: 000073",
//    "Student ID: 000112",
//    "Student ID: 000006"
// ]
```

我们是否应该提及`formatIDLabel(..)`作为回电? 这是`map(..)`实用性真的通过引用我们提供的功能来召回* 我们的程序?

没有什么可以*召回*本身,因为程序没有暂停或退出. 我们正在将一个功能(参考)从程序的一个部分传递到程序的另一个部分,然后立即被引用.

还有其他固定的术语 可能与我们正在做的事情相匹配 在一个函数中通过(参考) 这样程序的另一部分 可以代表我们引用它。 你可能会把这看作是"依赖注射*(DI)或"控制*(IoC)的转换".

DI可以概括为将功能的必要部分传递给程序的另一部分,以便它能够引用它们来完成它的工作. 这是一个很好的描述`map(..)`上面打电话,不是吗? 那个`map(..)`工具对列表的值知道, 但是它不知道用这些值做什么。 这就是为什么我们通过它`formatIDLabel(..)`函数。 我们通过依赖。

IoC是一个相当相似的相关概念. 控制权的倒置意味着,相对于你程序当前控制正在发生的事情的区域,你把控制权交给程序的另一部分. 我们在函数中将计算标签字符串的逻辑包裹起来`formatIDLabel(..)`,然后将引用控制权交给`map(..)`函数。

值得注意的是,马丁·福勒(Martin Fowler)引用IoC作为框架和图书馆的区别:用一个图书馆,你称之为它的功能;用一个框架,它称为你的功能.[^fowlerIOC]

在我们的讨论范围内,DI或IoC可以作为“同步回调”的替代标签。

但我有一个不同的建议。 让我们把(前称的函数) *同步调用*,称为*互用函数*(IIFs). 是的,没错,我在玩IIFEs。 这些类型的功能是*互用 *,意思是:另一个实体援引它们,而不是立即援引自己的IIFEs.

一个*同步回调*和一个IIF有什么关系? *同步召回*是一种被同步而不是同步引用的IIF.

### 同步关闭?

现在我们把“同步召回”重新标注为IIF, 我们可以回到我们的主要问题上:IIF是关闭的例子吗? 显然,综合投资框架必须参考外部范围的变量,这样它就有可能成为结束。 那个`formatIDLabel(..)`早先的IIF没有提及任何超出自身范畴的变量,因此绝对不是关闭.

那一个有外部参考的IIF呢? 是关闭的吗?

```js
function printLabels(labels) {
    var list = document.getElementById("labelsList");

    labels.forEach(
        function renderLabel(label){
            var li = document.createElement("li");
            li.innerText = label;
            list.appendChild(li);
        }
    );
}
```

内心`renderLabel(..)`综合投资框架参考文献`list`从附件范围, 所以这是一个综合投资框架 可能* 关闭。 但这里是定义/模式 我们选择关闭的重要性:

* 若为`renderLabel(..)`是一个在别的地方传递的**函数**,然后该函数被引用,然后是的,`renderLabel(..)`因为关闭是保持其进入原有范围链的途径。

* 但是,如果,像第7章的备选概念模型那样,`renderLabel(..)`留在原地,只提及`forEach(..)`,是否有必要关闭,以保持其范围链。`renderLabel(..)`当它同时在自己的范围内同步执行时?

没有 那只是普通的词汇范围

为了了解原因,考虑这一替代形式:`printLabels(..)`:

```js
function printLabels(labels) {
    var list = document.getElementById("labelsList");

    for (let label of labels) {
        // just a normal function call in its own
        // scope, right? That's not really closure!
        renderLabel(label);
    }

    // **************

    function renderLabel(label) {
        var li = document.createElement("li");
        li.innerText = label;
        list.appendChild(li);
    }
}
```

这两个版本`printLabels(..)`基本上是一样的。

后一种情况绝对不是结束的例子,至少在任何有用或可观察到的意义上都是如此。 这仅仅是词汇范围。 前一个版本`forEach(..)`调用我们的函数参考, 本质上是一样的。 也并非封闭,而只是一个普通的ol'lexical范围函数调用.

### 推迟关闭

7章简要提到部分应用和咖喱(*do* 依赖关闭!). 这是一个有趣的情景,可以使用人工咖喱:

```js
function printLabels(labels) {
    var list = document.getElementById("labelsList");
    var renderLabel = renderTo(list);

    // definitely closure this time!
    labels.forEach( renderLabel );

    // **************

    function renderTo(list) {
        return function createLabel(label){
            var li = document.createElement("li");
            li.innerText = label;
            list.appendChild(li);
        };
    }
}
```

内在功能`createLabel(..)`,我们分配给`renderLabel`,关闭`list`,因此,关闭肯定是在使用。

关闭让我们想起`list`稍后,我们推迟执行实际标签创建逻辑`renderTo(..)`给随后的`forEach(..)`援引`createLabel(..)`综合投资框架。 这也许只是这里短暂的时刻,但任何时间都可能过去,因为关闭从呼叫到呼叫的桥梁。

## 经典模块变化

第八章解释了经典的模块模式,这种模式可以这样看:

```js
var StudentList = (function defineModule(Student){
    var elems = [];

    var publicAPI = {
        renderList() {
            // ..
        }
    };

    return publicAPI;

})(Student);
```

发现我们正经过`Student`(另一个模块实例)作为一个依赖。 但是这个模块中有很多有用的变化 你可能会遇到。 识别这些差异的一些提示:

* 模块知道自己的API吗?
* 即使我们使用一个精致的模块加载器,它只是一个经典的模块
* 一些模块需要普遍发挥作用

### 我的API呢? 我... API在哪儿?

首先,大多数经典模块不定义和使用`publicAPI`我用这个代码显示的方式 相反,它们一般看起来像:

```js
var StudentList = (function defineModule(Student){
    var elems = [];

    return {
        renderList() {
            // ..
        }
    };

})(Student);
```

这里唯一的区别是直接将作为该模块公共 API 的物体还原,而不是首先将其保存到内部`publicAPI`变量。 这就是目前大多数经典模块是如何定义的.

但我更喜欢,而且总是用我自己,前者`publicAPI`表单。 有两个原因:

* `publicAPI`是一种语义描述符,通过使可读性更明显地显示物体的目的,来帮助可读性.

* 内存`publicAPI`引用返回的相同外部公共 API 对象的变量,如果在模块存在期间需要访问或修改 API,则该变量可以有用。

    For example, you may want to call one of the publicly exposed functions, from inside the module. Or, you may want to add or remove methods depending on certain conditions, or update the value of an exposed property.

    Whatever the case may be, it just seems rather silly to me that we *wouldn't* maintain a reference to access our own API. Right?

### 同步模块定义(AMD)

经典模块形式上的另一个变体是AMD风格的模块(几年前流行),例如由RequestJS工具支持的模块:

```js
define([ "./Student" ],function StudentList(Student){
    var elems = [];

    return {
        renderList() {
            // ..
        }
    };
});
```

如果你仔细看`StudentList(..)`,这是一个经典的模块工厂功能. 机器内部`define(..)`(由要求JS提供)`StudentList(..)`函数被执行,然后传递给被宣布为依赖性的任何其他模块实例。 返回值是一个代表模块的公共 API 的对象.

这完全基于我们用经典模块探索的完全相同的原则(包括关闭是如何运作的!).

### 通用模块(UMD)

最终的变体是UMD,它不太具体,准确的格式,更是非常相似格式的集合. 它旨在为可能在浏览器,AMD风格加载器,或节点中加载的模块创建更好的接口(没有任何构建工具转换). 我个人仍使用UMD形式出版许多公共图书馆。

以下是一个UMD的典型结构:

```js
(function UMD(name,context,definition){
    // loaded by an AMD-style loader?
    if (
        typeof define === "function" &&
        define.amd
    ) {
        define(definition);
    }
    // in Node?
    else if (
        typeof module !== "undefined" &&
        module.exports
    ) {
        module.exports = definition(name,context);
    }
    // assume standalone browser script
    else {
        context[name] = definition(name,context);
    }
})("StudentList",this,function DEF(name,context){

    var elems = [];

    return {
        renderList() {
            // ..
        }
    };

});
```

虽然它看起来有点不寻常, UMD其实只是一个IIFE。

不同之处在于主的`function`IIFE的表达式部分(在顶部)包含一系列`if..else if`语句,以检测模块正在装入的三种支持环境中的哪一种。

决赛`()`通常援引国际家庭教育倡议的论点有三个:`"StudentsList"`, `this`,另一个`function`表达式。 如果你把这些参数和参数相匹配,你会看到:`name`, `context`,以及`definition`分别为:`"StudentList"` (`name`)是模块的名称标签,主要是在定义为全局变量时.`this` (`context`通常为:`window`(aka, global object;见第4章)用于定义模块的名称.

`definition(..)`被引用来获取模块的定义, 你会注意到,当然,这只是一个经典模块形式!

毫无疑问,截至本文编写之时,无害环境管理(ES Modules)正在迅速流行和普及. 但是在过去20年里,有上百万个模块被写下来, 都使用一些经典模块的ESM前变体, 当你看到它们时,它们仍然非常重要,能够读懂和理解它们。

[^fowlerIOC]马丁·福勒https://martinfowler.com/bliki/InversionOfControl.html,2005年6月26日,纽约
