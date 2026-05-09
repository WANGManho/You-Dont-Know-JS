# 你不了解JS然而:范围与关闭 - 第二版
# 第7章:使用关闭

至此为止,我们一直专注于词汇范畴的内在和外在,以及这如何影响我们方案中变量的组织和使用.

我们的注意力再次从抽象角度更广泛地转向历史上令人生畏的结束专题。 别担心! 你不需要高级的计算机科学学位来理解它. 我们在本书中的广泛目标不仅仅是理解范围,而是在方案结构中更有效地利用范围;关闭是这一努力的核心。

回顾第6章的主要结论: * 最小接触*原则(POLE)鼓励我们使用块(和功能)范围界定来限制变量的范围接触。 这有助于保持代码的可理解性和可维护性,并有助于避免许多范围界定陷阱(即名称碰撞等).

关闭以这种方法为基础:对于我们需要长期使用的变量,而不是将它们置于更大的外部范围,我们可以将其封装起来(范围更窄),但仍能从内部功能中保留访问,以便更广泛地使用。 * 记住* 这些通过关闭引用的范围变量。

我们已经在前一章中看到过这种结束的例子(第2章),`factorial(..)`在第六章中,你几乎肯定已经在自己的节目中使用了。 如果你曾经写过一个回调 访问它自己范围之外的变量... 你猜怎么着? 这是关闭。

封闭是编程中发明的最重要语言特征之一——它是主要编程范式的基础,包括功能编程(FP),模块,甚至一些面向班级的设计. 要掌握JS 并有效地利用你整个代码中的许多重要设计模式, 就必须适应关闭。

解决结束活动的所有方面问题,需要在整个本章中进行艰巨的讨论和制定守则。 保证你的时间 和确保你舒适 每一点之前,移动到下一个。

## 见结束语

闭塞最初是一个数学概念,来自羊肉达微积分. 但我不会列出数学公式 也不会用一连串的注解和术语来定义它。

相反,我要专注于一个实用的视角. 我们首先要从我们所观察到的 不同行为的角度来定义关闭, 而不是在JS中不存在关闭。 然而,在本章的后面,我们将翻转闭合,从一个“另类观点”来看。

关闭是函数的一种行为,仅是函数. 如果你不处理一个功能,关闭不适用。 对象不能有关闭,类也不能有关闭(尽管其功能/方法可能). 只有函数关闭。

要观察关闭,必须援引一种功能,具体地说,必须在范围链中与其最初定义的不同分支中援引。 在相同范围内执行它所定义的功能不会显示出任何明显不同的行为,无论是否可能关闭;从观察的角度和定义来看,这不是关闭。

让我们看看一些代码, 附加了相关的范围泡颜色(见第2章):

```js
// outer/global scope: RED(1)

function lookupStudent(studentID) {
    // function scope: BLUE(2)

    var students = [
        { id: 14, name: "Kyle" },
        { id: 73, name: "Suzy" },
        { id: 112, name: "Frank" },
        { id: 6, name: "Sarah" }
    ];

    return function greetStudent(greeting){
        // function scope: GREEN(3)

        var student = students.find(
            student => student.id == studentID
        );

        return `${ greeting }, ${ student.name }!`;
    };
}

var chosenStudents = [
    lookupStudent(6),
    lookupStudent(112)
];

// accessing the function's name:
chosenStudents[0].name;
// greetStudent

chosenStudents[0]("Hello");
// Hello, Sarah!

chosenStudents[1]("Howdy");
// Howdy, Frank!
```

注意这个代码的第一件事就是`lookupStudent(..)`外部函数创建并返回一个名为`greetStudent(..)`. `lookupStudent(..)`被调用两次,生成其内在的两个独立实例`greetStudent(..)`函数,两者均保存到`chosenStudents`数组。

我们通过检查...`.name`保存在`chosenStudents[0]`这确实是一个内在的例子`greetStudent(..)`.

每次通话后`lookupStudent(..)`最后,似乎它的所有内在变量都将被丢弃,GC'd(收集垃圾)。 内在的功能是唯一似乎被送回和保存的东西. 但这里的行为不同,我们开始观察。

虽然`greetStudent(..)`作为命名的参数,确实收到单个参数`greeting`,它也提到了两者`students`和`studentID`的识别符号,这些识别符号来自附件`lookupStudent(..)`。从内部函数到外部范围变量的每一个引用都称为“闭合物”。 在学术方面,每个实例`greetStudent(..)`*接近 * 外部变量`students`和`studentID`.

那么,从具体和可观察的意义上讲,这些关闭在这里做什么?

关闭允许`greetStudent(..)`即使在外部范围完成后(每次呼叫`lookupStudent(..)`页:1 而不是以`students`和`studentID`作为GC'd, 他们留在周围的记忆。 在以后,当其中任一实例`greetStudent(..)`函数被引用,这些变量仍然存在,并持有其当前值。

如果联署材料的职能没有结束,则完成每项职能。`lookupStudent(..)`将立即拆除其范围,`students`和`studentID`变量。 我们后来打电话给一个`greetStudent(..)`函数,然后会发生什么?

若为`greetStudent(..)`试图获取它认为是BLUE(2)大理石, 但大理石实际上并不存在(再也没有), 合理的假设是我们应该得到一个`ReferenceError`是吗?

但我们没有犯错 死刑的执行`chosenStudents[0]("Hello")`写着"你好,莎拉!" 意思是它仍然可以进入`students`和`studentID`变量。 这是对关闭的直接观察!

### 终止

事实上,我们在之前的讨论中略微夸大了一点细节,我想许多读者都错过了!

由于该语法的字节`=>`箭头函数是,很容易忘记它们仍然创造了一个范围(正如第3章"箭头函数"中断言的那样). 那个`student => student.id == studentID`箭头函数正在创建另一个瞄准泡`greetStudent(..)`函数范围。

以第2章的彩色桶和气泡的比喻为基础,如果我们为这个代码创建彩色图,在这个最内层的筑巢层有第四个范围,所以我们需要第四个颜色;也许我们可以为这个范围选择ORANGE(4):

```js
var student = students.find(
    student =>
        // function scope: ORANGE(4)
        student.id == studentID
);
```

《BLUE》(2)`studentID`参考范围实际上在ORANGE(4)范围内,而不是GREEN(3)范围内。`greetStudent(..)`;还有,`student`箭头函数的参数为ORANGE(4),阴影GREEN(3)`student`.

这里的后果是,这个箭头函数作为回调传递到数组的`find(..)`方法必须维持关闭`studentID`,而不是`greetStudent(..)`保持关闭。 这没什么大不了的,因为一切仍然如预期一样有效. 切勿跳过即使微小的箭头功能也能进入关闭派对的事实,这是非常重要的.

### 添加关闭

让我们来研究一下一个经常被引用用来结束的犬类例子:

```js
function adder(num1) {
    return function addTo(num2){
        return num1 + num2;
    };
}

var add10To = adder(10);
var add42To = adder(42);

add10To(15);    // 25
add42To(9);     // 51
```

每一个内在的例子`addTo(..)`函数正在关闭。`num1`变量( 有值)`10`和`42`,因此,`num1`不要因为`adder(..)`结束。 当我们后来引用其中之一`addTo(..)`实例,例如:`add10To(15)`电话,它的关闭`num1`变量仍然存在,仍然持有原始`10`数值。 因此,行动能够`10 + 15`还原答案`25`.

一个重要的细节在上一段可能过于容易被掩盖,因此,让我们加强它:关闭与函数的一个实例有关,而不是其单一的词典定义。 在前面的片段里,只有一个内在的`addTo(..)`在内部定义的函数`adder(..)`因此,这似乎意味着一个结束。

但实际上,每次外在`adder(..)`函数运行, a * 新 * 内`addTo(..)`函数实例被创建,每个新实例都有一个新的关闭。 因此,每个内函数实例(标记为`add10To(..)`和`add42To(..)`在我们的方案中,它有自己的关闭 其自身的例子 范围环境 从执行`adder(..)`.

尽管关闭是基于在编译时处理的词典范围,但关闭被视为函数实例的运行时间特征。

### Live Link, 不是抓图

在前几节的这两个例子中,我们** 从一个变数** 读取了关闭时的值。 这使人感到,在某一时刻,关闭可能是一种价值的快照。 的确,这是一个常见的误解。

关闭实际上是一个直播链接,保留了对全变量本身的访问. 我们不限于只读一个值;关闭的变量也可以更新(重新分配)! 通过关闭函数中的变量,只要程序中存在该函数参考,我们就可以继续使用该变量(读写),并且从任何我们想要引用该函数的地方. 这就是为什么关闭是一个如此强大的技术 广泛使用在如此多的编程领域!

图4描述了函数实例和范围链接:

<figure>
    <img src="images/fig4.png" width="400" alt="Function instances linked to scopes via closure" align="center">
    <figcaption><em>Fig. 4: Visualizing Closures</em></figcaption>
    <br><br>
</figure>

如图4所示,每次呼吁`adder(..)`创建一个新的 BLUE(2) 范围,包含`num1`变量,以及一个新的实例`addTo(..)`函数为GREEN(3)范围。 通知函数实例( E)`addTo10(..)`和`addTo42(..)`在RED(1)范围内存在和援引。

现在让我们看看一个实例 关闭变量更新:

```js
function makeCounter() {
    var count = 0;

    return function getCurrent() {
        count = count + 1;
        return count;
    };
}

var hits = makeCounter();

// later

hits();     // 1

// later

hits();     // 2
hits();     // 3
```

那个`count`变量由内部关闭`getCurrent()`函数,使其不至于受到GC的制约。 那个`hits()`函数调用 access * 和 * 更新此变量,每次返回递增数。

虽然封闭的附加范围通常来自一个函数,但实际上并不需要;只需要在外部范围内存在一个内在函数:

```js
var hits;
{   // an outer scope (but not a function)
    let count = 0;
    hits = function getCurrent(){
        count = count + 1;
        return count;
    };
}
hits();     // 1
hits();     // 2
hits();     // 3
```

| 注: |
| :--- |
| 我故意定义的`getCurrent()`作为`function`表达式代替`function`声明。 这不是关于关闭,而是与危险的怪异的FiB(第六章). |

因为将关闭误认为面向价值而不是面向变量,所以开发者有时会被绊倒,试图利用关闭来从某个时刻开始保存一个值. 考虑:

```js
var studentName = "Frank";

var greeting = function hello() {
    // we are closing over `studentName`,
    // not "Frank"
    console.log(
        `Hello, ${ studentName }!`
    );
}

// later

studentName = "Suzy";

// later

greeting();
// Hello, Suzy!
```

通过定义`greeting()`(aa,`hello()`何时`studentName`持有值`"Frank"`(改派至`"Suzy"`),错误的假设往往是关闭会捕捉到`"Frank"`不过`greeting()`在变量上关闭`studentName`而不是它的价值。 无论何时`greeting()`引用变量的当前值(`"Suzy"`,此处反映).

这个错误的经典插图是定义循环中的函数:

```js
var keeps = [];

for (var i = 0; i < 3; i++) {
    keeps[i] = function keepI(){
        // closure over `i`
        return i;
    };
}

keeps[0]();   // 3 -- WHY!?
keeps[1]();   // 3
keeps[2]();   // 3
```

| 注: |
| :--- |
| 这种关闭插图通常使用`setTimeout(..)`或者像事件处理器那样在循环中召回 我简化了这个例子,把函数引用存储在一个数组中,这样我们不需要在分析中考虑同步的时点. 无论如何,关闭原则是一样的。 |

你可能会想到`keeps[0]()`援引返回`0`,因为该函数是在循环第一次迭代时创建的,`i`当时是`0`但是,这一假设再次源于将关闭视为注重价值而不是注重可变。

关于一个结构的东西`for`-Loop可以欺骗我们 以为每个迭代都有自己的新`i`变量;事实上,这个程序只有一个`i`由于它被宣布为`var`.

每个已保存的函数返回`3`,因为到循环结束,单曲`i`程序中的变量已指定`3`中的三个功能`keeps`阵列确实有单个关闭, 但是它们都关闭在同一共享上`i`变量。

当然,单个变量在任何特定时刻都只能持有一个值. 所以,如果想要保存多个值,就需要为每个变量设定不同的变量.

我们怎么能在循环片段里这样做? 让我们为每个迭代创建一个新的变量:

```js
var keeps = [];

for (var i = 0; i < 3; i++) {
    // new `j` created each iteration, which gets
    // a copy of the value of `i` at this moment
    let j = i;

    // the `i` here isn't being closed over, so
    // it's fine to immediately use its current
    // value in each loop iteration
    keeps[i] = function keepEachJ(){
        // close over `j`, not `i`!
        return j;
    };
}
keeps[0]();   // 0
keeps[1]();   // 1
keeps[2]();   // 2
```

每个函数现在通过每个迭代中一个单独的(新)变量关闭,尽管它们都被命名了`j`每一个`j`获得一份`i`在循环迭代时;`j`从来没有被重新分配。 因此,所有三种函数现在返回其预期值:`0`, `1`,以及`2`!

记住,即使我们在程序中使用一个同步器,比如通过每个内部`keepEachJ()`函数进入`setTimeout(..)`或者某些事件处理器订阅, 同样的关闭行为仍然会被观察到。

回顾第5章的“Loops”一节,该节说明`let`声明a`for`循环实际上不仅为循环创建了一个变量,而且实际上为循环的*每个迭代*创造了一个新的变量. 那个诡计和怪兽正是我们关闭环路所需要的:

```js
var keeps = [];

for (let i = 0; i < 3; i++) {
    // the `let i` gives us a new `i` for
    // each iteration, automatically!
    keeps[i] = function keepEachI(){
        return i;
    };
}
keeps[0]();   // 0
keeps[1]();   // 1
keeps[2]();   // 2
```

自从我们用`let`3个`i`''是创建的,每个循环一个,所以三个关闭的每一个都按预期工作.

### 共同关闭:Ajax和事件

关闭最常见的是召回:

```js
function lookupStudentRecord(studentID) {
    ajax(
        `https://some.api/student/${ studentID }`,
        function onRecord(record) {
            console.log(
                `${ record.name } (${ studentID })`
            );
        }
    );
}

lookupStudentRecord(114);
// Frank (114)
```

那个`onRecord(..)`在Ajax呼叫的回复回来后,未来某个时候将援引回调。 这种援引将发生在`ajax(..)`效用,无论它来自何处。 此外,一旦发生这种情况,`lookupStudentRecord(..)`电话早就要打完了

那为什么`studentID`仍然在周围 并且可以被召回? 关闭。

事件处理器是关闭的另一种常用方法:

```js
function listenForClicks(btn,label) {
    btn.addEventListener("click",function onClick(){
        console.log(
            `The ${ label } button was clicked!`
        );
    });
}

var submitBtn = document.getElementById("submit-btn");

listenForClicks(submitBtn,"Checkout");
```

那个`label`参数由`onClick(..)`事件处理器调用。 当按钮被点击时,`label`尚存待使用。 这是关闭。

### 如果我看不见呢?

你可能听过这个俗语:

> 如果一棵树掉在森林里 但周围没人听到 它发出声音吗?

这是一个愚蠢的 哲学体操。 当然,从科学的角度来看,声音波是创造出来的. 但真正的要点是: 如果声音发生,它是否重要?

记住,我们在关闭的定义中强调可观察性。 如果存在(在技术、执行或学术意义上)关闭,但在我们的方案中却无法观察到,那么它是否重要? 没有

为加强这一点,让我们看看一些“不是”明显基于结束的例子。

例如,引用一个使用词典范围检索功能:

```js
function say(myName) {
    var greeting = "Hello";
    output();

    function output() {
        console.log(
            `${ greeting }, ${ myName }!`
        );
    }
}

say("Kyle");
// Hello, Kyle!
```

内在功能`output()`访问变量`greeting`和`myName`从它的包罗范围。 但援引`output()`发生在同一范围,当然`greeting`和`myName`仍然可以使用;这只是词汇范围,而不是关闭。

任何功能不支持关闭的语法范围化语言,仍然会这样的行为.

事实上,全球范围变量基本上无法(可观察)关闭,因为它们总是可以从任何地方获取。 在范围链的任何部分中,任何功能都不得被援引,而不能作为全球范围的后裔。

考虑:

```js
var students = [
    { id: 14, name: "Kyle" },
    { id: 73, name: "Suzy" },
    { id: 112, name: "Frank" },
    { id: 6, name: "Sarah" }
];

function getFirstStudent() {
    return function firstStudent(){
        return students[0].name;
    };
}

var student = getFirstStudent();

student();
// Kyle
```

内心`firstStudent()`函数会引用`students`,这是一个超出自身范围的变量。 但自从`students`恰好来自全球范围,无论程序中在哪里引用该功能,其访问能力`students`没有什么比正常的词汇范围更特别的了。

所有函数引用都可以访问全局变量,无论关闭是否由语言支持. 全球变量不需要关闭。

仅存在但从未访问的变量不会导致关闭:

```js
function lookupStudent(studentID) {
    return function nobody(){
        var msg = "Nobody's here yet.";
        console.log(msg);
    };
}

var student = lookupStudent(112);

student();
// Nobody's here yet.
```

内在功能`nobody()`不关闭任何外部变量, 它只使用自己的变量`msg`尽管`studentID`位于附件的范围,`studentID`未提及`nobody()`JS引擎不需要保留`studentID`紧接着`lookupStudent(..)`已经运行完毕,所以GC想要清理记忆!

不论联署机构的职能是否支持关闭,这个方案都会有同样的行为。 因此,这里没有观察到关闭。

如果没有函数引用,则无法观察到关闭:

```js
function greetStudent(studentName) {
    return function greeting(){
        console.log(
            `Hello, ${ studentName }!`
        );
    };
}

greetStudent("Kyle");

// nothing else happens
```

这个很狡猾 因为外在功能肯定被引用了 但内在的功能是*可能*已经关闭的功能,然而它从未被引用;这里返回的功能只是被丢弃. 因此,即使技术上JS引擎创造了短暂的关闭时间,但在这个计划中没有任何有意义的观察.

一棵树可能掉下来了... 但我们没听到 所以我们不在乎

### 可观察的定义

我们现在准备好定义关闭:

> 当一个函数使用外部范围的变量时,即使运行在无法访问这些变量的范围时,也会观察到关闭。

这一定义的关键部分是:

* 一定涉及一个功能

* 必须在外部范围至少引用一个变量

* 必须在范围链中与变量不同的分支中引用

这个面向观察的定义意味着我们不应该把关闭视为一些间接的,学术的三维论. 相反,我们应当寻找并计划 关闭对我们程序行为的直接,具体的影响。

## 关闭寿命周期和垃圾收集(GC)

由于关闭与函数实例有着内在的联系,只要仍然提及该函数,关闭在一个变量上就会持续。

如果在同一个变量上全部关闭的10个函数,并且随着时间的推移,这些函数的引用中有9个被丢弃,那么仅剩的函数引用仍然保留该变量. 一旦该最终函数引用被丢弃,该变量上最后一个关闭点就消失,变量本身就是GC'd.

这对建立高效和有效果的方案有重要影响。 关闭可以意外地防止你用其他方法处理的变量的GC,这会导致一段时间内运行的内存使用. 因此,在不再需要函数引用时,必须丢弃函数引用(从而放弃其关闭).

考虑:

```js
function manageBtnClickEvents(btn) {
    var clickHandlers = [];

    return function listener(cb){
        if (cb) {
            let clickHandler =
                function onClick(evt){
                    console.log("clicked!");
                    cb(evt);
                };
            clickHandlers.push(clickHandler);
            btn.addEventListener(
                "click",
                clickHandler
            );
        }
        else {
            // passing no callback unsubscribes
            // all click handlers
            for (let handler of clickHandlers) {
                btn.removeEventListener(
                    "click",
                    handler
                );
            }

            clickHandlers = [];
        }
    };
}

// var mySubmitBtn = ..
var onSubmit = manageBtnClickEvents(mySubmitBtn);

onSubmit(function checkout(evt){
    // handle checkout
});

onSubmit(function trackAction(evt){
    // log action to analytics
});

// later, unsubscribe all handlers:
onSubmit();
```

在这个节目里 内心`onClick(..)`函数对所经过的`cb`(提供的事件召回). 这意味着`checkout()`和`trackAction()`函数表达式引用通过关闭(不能是GC'd)进行,只要这些事件处理器被订阅。

当我们打电话`onSubmit()`在最后一行没有输入时,所有事件处理器都未订阅,并且`clickHandlers`阵列已空出。 一旦所有点击处理器函数引用被丢弃,则关闭`cb`参考文献`checkout()`和`trackAction()`被丢弃。

在考虑程序的整体健康和效率时,在不再需要时不订阅事件处理器,可能比初始订阅更重要!

### 每个变量还是每个范围?

我们需要处理的另一个问题:我们是否应考虑仅对引用的外部变量适用关闭,还是关闭保留了整个范围链及其所有变量?

换句话说,在前一个事件 订阅片段,是内在的`onClick(..)`仅关闭函数`cb`或者它也关闭了`clickHandler`, `clickHandlers`,以及`btn`?

在概念上,关闭是**每个变量**而不是*每个范围*。 Ajax召回器、事件处理器和所有其他形式的功能关闭通常被认为只关闭它们明确提到的功能。

但实际情况比这更加复杂。

考虑的另一个方案:

```js
function manageStudentGrades(studentRecords) {
    var grades = studentRecords.map(getGrade);

    return addGrade;

    // ************************

    function getGrade(record){
        return record.grade;
    }

    function sortAndTrimGradesList() {
        // sort by grades, descending
        grades.sort(function desc(g1,g2){
            return g2 - g1;
        });

        // only keep the top 10 grades
        grades = grades.slice(0,10);
    }

    function addGrade(newGrade) {
        grades.push(newGrade);
        sortAndTrimGradesList();
        return grades;
    }
}

var addNextGrade = manageStudentGrades([
    { id: 14, name: "Kyle", grade: 86 },
    { id: 73, name: "Suzy", grade: 87 },
    { id: 112, name: "Frank", grade: 75 },
    // ..many more records..
    { id: 6, name: "Sarah", grade: 91 }
]);

// later

addNextGrade(81);
addNextGrade(68);
// [ .., .., ... ]
```

外部功能`manageStudentGrades(..)`并返回`addGrade(..)`函数引用, 外部标签`addNextGrade(..)`每次我们打电话`addNextGrade(..)`有了新的分数,我们就能重新获得前十名的目前名单,按数字递减排序(见`sortAndTrimGradesList()`).

从原来的结尾`manageStudentGrades(..)`调用,并在多个调用之间`addNextGrade(..)`电话,`grades`变量在内部保存`addGrade(..)`通过关闭;这就是如何维持最高年级的运行列表. 记住,是变数的关闭`grades`而不是它持有的数组。

但这不是唯一的结账 你能发现其他变量被关闭了吗?

你看见了吗?`addGrade(..)`参考文献`sortAndTrimGradesList`这意味着它也关闭 在识别器上, 它碰巧持有一个参考`sortAndTrimGradesList()`函数。 第二个内在功能必须留在身边,这样`addGrade(..)`也可以继续叫它, 这也意味着任何变量 *它* 关闭在周围—— 虽然,在这种情况下, 没有什么额外的关闭在那里。

还有什么关闭?

考虑`getGrade`变量( 及其函数)。 它是否关闭? 外在范围中参考`manageStudentGrades(..)`输入`.map(getGrade)`打电话 但它没有在`addGrade(..)`或`sortAndTrimGradesList()`.

那么我们通过的学生记录(潜在)大名单呢?`studentRecords`变数关闭了吗? 如果是这样的话, 学生的记录就永远得不到GC'd, 这导致了这个程序 持有比我们想象的更多的内存。 但是如果我们再仔细看一遍 任何内在功能都没有提到`studentRecords`.

根据 " 每个变量 " 的关闭定义,`getGrade`和`studentRecords`*不是* 被内在功能所引用, 它们没有关闭。 应在《公约》生效之后立即免费提供给希族塞人。`manageStudentGrades(..)`呼叫完毕。

事实上,尝试在JS最近的引擎中调试这个代码, 就像在Chrome的v8, 将一个断点放进`addGrade(..)`函数。 您可以注意到,督察** 不**列出`studentRecords`变量。 这证明,从调试角度来说,引擎没有维护`studentRecords`通过关闭。 嘘!

但这种观察作为证据有多可靠? 考虑一下这个( 而不是设计出来的 ) 程序:

```js
function storeStudentInfo(id,name,grade) {
    return function getInfo(whichValue){
        // warning:
        //   using `eval(..)` is a bad idea!
        var val = eval(whichValue);
        return val;
    };
}

var info = storeStudentInfo(73,"Suzy",87);

info("name");
// Suzy

info("grade");
// 87
```

注意内在功能`getInfo(..)`没有明确关闭任何`id`, `name`,或`grade`变量。 然而,呼吁`info(..)`似乎仍然能够访问变量,尽管通过使用`eval(..)`逻辑范围欺诈(见第1章)。

因此,所有变量都通过关闭得到了绝对的保存,尽管内部功能没有明确的引用. 因此,这是否否定了“每个可变”主张支持“每个范围”的说法? 要看情况

许多现代JS引擎的确应用了"优化"(Operationalization),将任何变量从没有明确引用的关闭范围中移除. 然而,正如我们看到的`eval(..)`,存在无法应用这种优化的情况,关闭范围继续包含其所有原始变量. 换句话说,关闭必须是*每个范围*,执行时明智的,然后可选地优化,缩小范围,只缩小到所关闭的范围(类似的结果是*每个可变*关闭)。

即使在几年前,许多JS引擎都没有应用这种优化;你的网站也可能仍然在这样的浏览器中运行,特别是在旧的或低端的设备上. 这意味着,像事件处理员这样的长期关闭 可能比我们想象的时间要长得多。

而事实上,它首先是一种可选的优化,而不是对规格的要求,这意味着我们不应该只是随意地过度地假定它的适用性.

在变量持有大值(如对象或阵列),且该变量存在于关闭范围中的情况下,如果不再需要该值而不希望该存储器被持有,那么手动丢弃该值而不是依赖关闭优化/GC是更安全的(memory used).

让我们用一个*fix * 之前`manageStudentGrades(..)`实例,以确保可能存在的大阵列`studentRecords`并非不必要地陷入关闭范围:

```js
function manageStudentGrades(studentRecords) {
    var grades = studentRecords.map(getGrade);

    // unset `studentRecords` to prevent unwanted
    // memory retention in the closure
    studentRecords = null;

    return addGrade;
    // ..
}
```

我们不会搬走的`studentRecords`我们无法控制。 我们保证,即使`studentRecords`该变量不再引用可能存在的大量数据;该数组可以是GC'd。

同样,在许多情况下,联署材料可能自动使方案达到同样的效果。 但是,要小心谨慎,明确确保我们不会将任何数量可观的设备内存绑在任何必要的时间里,这仍然是一个很好的习惯.

事实上,我们也在技术上不需要这个功能`getGrade()`之后的`.map(getGrade)`呼叫完毕。 如果我们的应用程序显示这是一个 过度使用内存的关键领域, 我们也许可以释放出一点点 更多的内存 通过释放该参考,所以它的价值也不捆绑。 这在玩具的例子中可能是不必要的,但是如果你在优化应用程序的记忆足迹,这是一个可以记住的一般技术.

外卖:重要的是要知道关闭 出现在我们的程序, 以及包含哪些变量。 我们应该小心地管理这些关闭, 所以我们只是坚持 最低限度的需要,而不是浪费记忆。

## 备选视角

审查我们关闭的工作定义时,断言函数是"一流的值",可以环绕程序传递,就像任何其他值一样. 关闭是连接功能与自身之外的范围/可变因素的链接-关联,无论该功能去向何方.

让我们回顾一下本章前面的一个代码例子,

```js
// outer/global scope: RED(1)

function adder(num1) {
    // function scope: BLUE(2)

    return function addTo(num2){
        // function scope: GREEN(3)

        return num1 + num2;
    };
}

var add10To = adder(10);
var add42To = adder(42);

add10To(15);    // 25
add42To(9);     // 51
```

我们目前的观点表明,只要一个功能被通过和引用,关闭就会保留一个隐藏的链接回溯到原始范围,以便于访问关闭变量。 为方便起见,在此重复的图4说明了这一概念:

<figure>
    <img src="images/fig4.png" width="400" alt="Function instances linked to scopes via closure" align="center">
    <figcaption><em>Fig. 4 (repeat): Visualizing Closures</em></figcaption>
    <br><br>
</figure>

但是还有另一种方法来思考关闭, 更确切地说, 功能的性质被*绕过*, 这可能有助于深化精神模型。

这种替代模式去强调"功能为一等值",而是包含功能(像所有非原始值一样)如何在JS中通过引用来持有,并被指定/通过参考副本——详见*Get Started* book中的附录A以获取更多信息.

而不是考虑内部函数实例`addTo(..)`移动到外部RED(1)范围,通过`return`我们可以设想,功能实例实际上只是停留在自己的范围环境中,当然其范围链是完好无损的。

*同意* RED(1) 范围的内容只是** 提及** 在位函数实例,而不是函数实例本身。 图5显示RED(1)指出的内在函数实例。`addTo10`和`addTo42`分别提及:

<figure>
    <img src="images/fig5.png" width="400" alt="Function instances inside scopes via closure, linked to by references" align="center">
    <figcaption><em>Fig. 5: Visualizing Closures (Alternative)</em></figcaption>
    <br><br>
</figure>

如图5所示,每次呼吁`adder(..)`仍然创建一个新的 BLUE(2) 范围,其中包含`num1`以及GREEN的一个实例(3)`addTo(..)`范围。 但与图4不同的是,现在这些GREEN(3)实例仍然存在,自然地在它们的BLUE(2)范围实例中嵌入. 那个`addTo10`和`addTo42`引用移至RED(1)外部范围,而不是函数本身。

何时`addTo10(15)`被称为,`addTo(..)`函数实例(仍在其BLUE(2)的原始范围环境中使用)。 由于函数本身从未移动过,它当然仍然可以自然进入其范围链. 同样的`addTo42(9)`这里没有什么特别的 超越词汇范围。

那么,什么是* 关闭,如果不是 * magic * 让一个函数保持一个链接 与它原来的范围链,即使这个函数在其它范围移动。 在这个替代模式中,功能保持原位,并一如既往地不断访问其原有的瞄准链.

关闭则描述了** 保持函数实例** 的 *magic * 及其整个范围环境和链条,只要至少有一个关于函数实例的提法漂浮在程序的任何其他部分.

与传统的学术观点相比,关闭的定义不太具有观察性,也不太熟悉。 但它仍然有用, 因为好处是我们把结束的解释简化到 直接结合参考和就地函数实例。

前一种模式(图4)在联署材料中描述关闭并非“错误”。 它只是概念上的灵感, 一个关于关闭的学术视角。 相比之下,备选模式(图5)可以说是更注重执行,即JS的实际运作方式。

两种观点/模式都有助于理解结束语,但读者可能会发现其中一种比另一种更容易掌握. 无论你选择什么,我们计划中的可见结果都是一样的。

| 注: |
| :--- |
| 这个关闭的替代模式确实影响了我们是否将同步回调归类为关闭的例子. 关于附录A中这一细微差别的更多信息。 |

## 为什么关闭?

现在我们对什么是封闭和它如何运作有周全的感觉, 让我们探索一些方法,它可以改进一个实例程序的代码结构和组织.

想象一下您在页面上有一个按钮,在点击时,应该通过Ajax请求检索并发送一些数据. 不使用关闭:

```js
var APIendpoints = {
    studentIDs:
        "https://some.api/register-students",
    // ..
};

var data = {
    studentIDs: [ 14, 73, 112, 6 ],
    // ..
};

function makeRequest(evt) {
    var btn = evt.target;
    var recordKind = btn.dataset.kind;
    ajax(
        APIendpoints[recordKind],
        data[recordKind]
    );
}

// <button data-kind="studentIDs">
//    Register Students
// </button>
btn.addEventListener("click",makeRequest);
```

那个`makeRequest(..)`只接收工具`evt`来自单击事件的对象。 从那里,它必须取回`data-kind`属性,并使用该值查找 API 端点的 URL 以及 Ajax 请求中应当包含哪些数据。

这行得通,但很不幸(效率低下,更令人困惑),事件处理者每次发射都要读取DOM属性. 为什么一个事件管理员不能记住这个值? 让我们尝试用关闭来改进代码:

```js
var APIendpoints = {
    studentIDs:
        "https://some.api/register-students",
    // ..
};

var data = {
    studentIDs: [ 14, 73, 112, 6 ],
    // ..
};

function setupButtonHandler(btn) {
    var recordKind = btn.dataset.kind;

    btn.addEventListener(
        "click",
        function makeRequest(evt){
            ajax(
                APIendpoints[recordKind],
                data[recordKind]
            );
        }
    );
}

// <button data-kind="studentIDs">
//    Register Students
// </button>

setupButtonHandler(btn);
```

带着`setupButtonHandler(..)`方法、方法、`data-kind`属性一次检索并指定给`recordKind`初始设置时的变量。`recordKind`然后被内部关闭`makeRequest(..)`单击处理器,其值用于每次事件点火时,以查看应发送的URL和数据。

| 注: |
| :--- |
| `evt`仍然传递给`makeRequest(..)`虽然在这种情况下 我们不再使用它了 仍列于上,以与前篇片段一致. |

通过放置`recordKind`内部`setupButtonHandler(..)`,我们将该变量的范围暴露限制在更合适的程序子集;全球存储该变量对于代码组织和可读性来说更糟糕. 关闭让内心`makeRequest()`函数实例 * 记住 * 此变量和需要时的访问。

基于这种模式,我们可以在设置时查阅一次URL和数据:

```js
function setupButtonHandler(btn) {
    var recordKind = btn.dataset.kind;
    var requestURL = APIendpoints[recordKind];
    var requestData = data[recordKind];

    btn.addEventListener(
        "click",
        function makeRequest(evt){
            ajax(requestURL,requestData);
        }
    );
}
```

现在`makeRequest(..)`关闭了`requestURL`和`requestData`(笑声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声)(掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声(掌声) (掌声) (掌声(掌声) (掌声) (掌声) (掌声(掌声)

功能性方案拟订模式中依赖关闭的两个类似技术是部分应用和咖喱。 简而言之,通过这些技术,我们改变需要多种投入的功能的*形状*,以便先提供一些投入,然后提供其他投入;通过关闭来记住最初的投入。 一旦提供了所有投入,即采取基本行动。

通过创建一个将一些信息封装在内部的函数实例(通过关闭),与存储的函数信息日后可以直接使用,而不需要重新提供该输入. 这使得这部分代码更清洁,也为部分应用的功能贴上更好的语义名称提供了机会.

在调整部分应用时,我们可以进一步改进上述守则:

```js
function defineHandler(requestURL,requestData) {
    return function makeRequest(evt){
        ajax(requestURL,requestData);
    };
}

function setupButtonHandler(btn) {
    var recordKind = btn.dataset.kind;
    var handler = defineHandler(
        APIendpoints[recordKind],
        data[recordKind]
    );
    btn.addEventListener("click",handler);
}
```

那个`requestURL`和`requestData`提前提供投入,导致`makeRequest(..)`部分应用的函数, 本地标签`handler`当事件最终起火时,最后投入(`evt`尽管它被忽略)被传递给`handler()`,完成输入并触发基本的Ajax请求.

行为上,这个程序与前一个程序相当相似,关闭类型相同. 但通过隔离 创建`makeRequest(..)`在一个单独的公用设施中(`defineHandler(..)`),我们让这个定义在整个程序上更容易重复使用. 我们还明确将关闭范围限于所需要的两个变量。

## 接近关闭

当我们关闭一个密集的篇章时 深呼吸一下 让它沉入水中 说真的,这是很多信息 谁可以消费!

我们探索了两种解决精神问题的模式:

* 观察:关闭是一个函数实例,可以记住其外部变量,即使该函数被传递到**其他范围并被**引用。

* 实施:关闭是一个函数实例,其范围环境保留在原地,而任何提及它之处则相互传递,** 从** 其他范围引出。

总结方案收益:

* 关闭可以通过允许函数实例记住以前确定的信息而不是每次需要计算来提高效率.

* 关闭可以提高代码的可读性,通过封装函数实例内的变量来限制范围-曝光,同时仍然确保这些变量中的信息可供今后使用。 由此产生的更窄,更专业化的函数实例更清洁,可以与之互动,因为保存的信息不需要在每次引用时传递.

在你继续前, 需要一些时间来重复这个摘要 * 用你自己的话来说 *,解释什么是结束,为什么它会帮助你的节目。 主书文本最后是最后一章,该章以模块模式的关闭之上为基础。
