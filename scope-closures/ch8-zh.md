# 你不了解JS然而:范围与关闭 - 第二版
# 第8章:模块模式

在本章中,我们通过探索所有编程中最重要的代码组织模式之一:模块来总结本书的正文. 正如我们所看到的,模块是内在的 从我们已经覆盖的: 回报你努力学习 词汇范围和关闭。

我们从全球范围的宽度 一直到筑巢区块的宽度 到可变寿命周期的复杂程度 然后,我们利用词汇范围来理解关闭的全部力量。

花点时间思考一下你在这趟旅程中走的有多远;你迈出了大步来更深入地了解JS!

本书的中心主题是理解和掌握范围与关闭是正确构建和组织我们的代码的关键,尤其是关于将信息储存在变量中的决定.

我们最后一章的目标是理解模块如何体现这些主题的重要性,并将它们从抽象的概念提升到建设方案的具体、实际的改进。

## 封装和最小接触(POLE)

封装常被引用为面向对象(OO)编程的原则,但比此更具根本性和广泛适用性. 封装的目标是将信息(数据)和行为(功能)捆绑在一起或合用同一地点,共同服务于共同目的.

独立于任何语法或代码机制,封装的精神可以像使用单独的文件那样简单地实现,以共同的目的持有总体程序的位点. 如果我们把授权搜索结果列表的所有内容都捆绑到一个名为"search-list.js"的单一文件中,我们就会将这部分程序封装起来.

围绕组件架构组织应用的现代前端编程的最新趋势更进一步推进封装. 对许多人来说,将所有构成搜索结果清单的东西——甚至超越代码,包括演示标记和造型——整合成一个单一的程序逻辑单元是自然的,这是我们能够与之互动的。 然后我们给收集"搜索列表"的组件贴上标签。

另一个关键目标是控制封装数据和功能的某些方面的能见度. 从第6章中回顾 " 最大接触 " 原则(POLE),该原则试图防御范围过度接触的各种 " 距离 ";这些影响变量和功能。 在联署材料中,我们最常通过词汇范围机制实施能见度控制。

想法是将程序比特组合在一起,并有选择地限制方案进入我们认为的*私有*细节的部分. 不被认为是*私有*的,然后标记为*公*,整个程序都可以访问.

这一努力的自然效果是更好的代码组织. 当我们知道事物在哪里时,更容易建立和维护软件,有清晰明显的界限和连接点. 如果我们避免过度曝光的数据和功能的陷阱,也更容易保持质量.

这些是将联署方案组织成单元的一些主要好处。

## 什么是模块?

一个模块是相关数据和函数的集合(通常在此背景下被称为方法),其特征是将隐藏的*私有*细节与*公有*可获取的细节区分开来,通常被称为"公有API".

一个模块也具有状态:它长期保存一些信息,以及获取和更新这些信息的功能。

| 注: |
| :--- |
| 对模块模式的一个更广泛的关切是通过松散的组合和其他程序架构技术,完全接受系统一级的模块化。 这是一个复杂的话题 远远超出了我们的讨论范围, 但值得进一步研究 除了这本书。 |

为了更好地了解一个模块是什么, 让我们比较一下一些模块的特性 与一些不完全模块的实用代码模式。

### 命名空间( 无状态分组)

如果将一组相关的函数组合在一起,没有数据,那么你就不会真的有模块所暗示的预期封装. *无状态 * 函数组合的更好术语是命名空间:

```js
// namespace, not module
var Utils = {
    cancelEvt(evt) {
        evt.preventDefault();
        evt.stopPropagation();
        evt.stopImmediatePropagation();
    },
    wait(ms) {
        return new Promise(function c(res){
            setTimeout(res,ms);
        });
    },
    isValidEmail(email) {
        return /[^@]+@[^@.]+\.[^@.]+/.test(email);
    }
};
```

`Utils`这里有一套有用的公用设施 但是它们都是国家独立的功能 集聚功能一般都是好的做法,但这并不能使这成为模块. 相反,我们定义了`Utils`命名空间并组织其下的职能。

### 数据结构( 状态分组)

即使你将数据和状态功能捆绑在一起,如果你没有限制其中任何一个的能见度,那么你就停止了封装的POLE方面;给这个模块贴标签并不特别有帮助.

考虑:

```js
// data structure, not module
var Student = {
    records: [
        { id: 14, name: "Kyle", grade: 86 },
        { id: 73, name: "Suzy", grade: 87 },
        { id: 112, name: "Frank", grade: 75 },
        { id: 6, name: "Sarah", grade: 91 }
    ],
    getName(studentID) {
        var student = this.records.find(
            student => student.id == studentID
        );
        return student.name;
    }
};

Student.getName(73);
// Suzy
```

从`records`是公开的数据,而不是隐藏在公共API后面,`Student`这里不是真正的模块。

`Student`有封装的数据和功能方面,但没有可见度控制方面。 最好给这个数据结构贴上标签。

### 模块(状态访问控制)

为了体现模块模式的全部精神,我们不仅需要分组和状态,还需要通过能见度(私人对公众)进行访问控制.

我们转身`Student`从上一节进入模块。 我们从一种我称之为"经典模块"的形式开始,这个模块最初在2000年代初首次出现时被称为"启示模块". 考虑:

```js
var Student = (function defineStudent(){
    var records = [
        { id: 14, name: "Kyle", grade: 86 },
        { id: 73, name: "Suzy", grade: 87 },
        { id: 112, name: "Frank", grade: 75 },
        { id: 6, name: "Sarah", grade: 91 }
    ];

    var publicAPI = {
        getName
    };

    return publicAPI;

    // ************************

    function getName(studentID) {
        var student = records.find(
            student => student.id == studentID
        );
        return student.name;
    }
})();

Student.getName(73);   // Suzy
```

`Student`现在是一个模块的例子。 它的特点是公开的API,采用单一的方法:`getName(..)`。这种方法可以访问隐藏的私人`records`数据。

| 警报: |
| :--- |
| 我要指出的是,本单元定义中的明确学生数据是硬编码的,只是为了说明问题。 您程序中的一个典型模块将会从外部来源接收此数据, 通常从数据库, JSON 数据文件, Ajax 调用等中加载. 然后数据一般通过模块公共API上的方法注入模块实例. |

经典模块格式如何运作?

通知模块实例是由`defineStudent()`IIFE被执行. 此 IIFE 返回对象( 名称`publicAPI`上面有一块地产 上面有内在的`getName(..)`函数。

命名对象`publicAPI`就我而言,是典型的偏好 对象可以任意命名(JS并不在意),也可以直接返回对象而不将其分配给任何内部命名变量. 关于附录A中的这一选择的更多信息。

从外面,`Student.getName(..)`引用此已曝光的内在函数,该函数保持对内`records`变量通过关闭。

你没有* 返回一个具有函数的物体作为其属性之一。 您可以直接返回一个函数, 代替对象。 这仍然满足了经典模块的所有核心位.

由于语法范围如何工作,定义您外模块定义函数中的变量和函数使得所有*默认* 都变得私有. 只有从函数返回的公共 API 对象中添加的属性才会导出供外部公共使用.

使用IIFE意味着我们的程序只需要模块的单个中心实例,通常被称为"singleton". 事实上,这个具体的例子就足够简单了,我们除了需要一个实例之外,没有任何明显的理由需要任何东西。`Student`模块。

#### 模块工厂(多实例)

但如果我们真的想要定义一个支持我们程序中多个实例的模块,我们可以略微修改代码:

```js
// factory function, not singleton IIFE
function defineStudent() {
    var records = [
        { id: 14, name: "Kyle", grade: 86 },
        { id: 73, name: "Suzy", grade: 87 },
        { id: 112, name: "Frank", grade: 75 },
        { id: 6, name: "Sarah", grade: 91 }
    ];

    var publicAPI = {
        getName
    };

    return publicAPI;

    // ************************

    function getName(studentID) {
        var student = records.find(
            student => student.id == studentID
        );
        return student.name;
    }
}

var fullTime = defineStudent();
fullTime.getName(73);            // Suzy
```

而不是具体说明`defineStudent()`作为一个IIFE,我们只是把它定义为一个正常的独立函数,在这个语境中通常被称为"模块工厂"函数.

然后我们叫模组工厂,生产一个我们标注的模组实例`fullTime`。本模块实例意味着内在范围的新实例,从而意味着新的结束语:`getName(..)`暂停`records`. `fullTime.getName(..)`现在援引了这个具体案例的方法。

#### 经典模块定义

因此,为了澄清是什么 使得一个经典模块:

* 必须有一个外部范围,一般来自至少运行一次的模块工厂功能.

* 模块的内在范围必须至少有一块隐藏信息代表模块状态.

* 模块必须返回到其公共 API 上至少一个在隐藏模块状态上关闭的函数的引用(这样这个状态才能被实际保存).

你可能会在这种经典的模块方法上 经历其他的变异, 我们会在附录A中详细研究。

## 节点普通JS模块

在第四章中,我们引入了Node使用的CommonJS模块格式. 与前面描述的经典模块格式不同,您可以将模块厂房或IIFE与包括其他模块在内的其他代码一起捆绑在一起,CommonJS模块基于文件;每个文件一个模块.

让我们调整我们的模块实例,以遵守这个格式:

```js
module.exports.getName = getName;

// ************************

var records = [
    { id: 14, name: "Kyle", grade: 86 },
    { id: 73, name: "Suzy", grade: 87 },
    { id: 112, name: "Frank", grade: 75 },
    { id: 6, name: "Sarah", grade: 91 }
];

function getName(studentID) {
    var student = records.find(
        student => student.id == studentID
    );
    return student.name;
}
```

那个`records`和`getName`标识符属于本模块的顶级范围,但这不是全球范围(如第四章所解释的). 因此,这里的一切都是*默认 * 私人到模块。

要在 CommonJS 模块的 API 上曝光一些内容, 您会在空对象中添加一个属性。`module.exports`。在一些旧的遗产代码中,你可能 横跨引用只是赤裸裸的`exports`,但为了明确代码,你应当始终完全有资格使用`module.`前缀。

为了风格目的,我喜欢把我的"出口"放在顶部,我的模块执行放在底部. 但这些出口可以放在任何地方。 我强烈建议你们在档案的顶部或底部 把它们收集起来

一些开发者习惯于替换默认导出对象,例如:

```js
// defining a new object for the API
module.exports = {
    // ..exports..
};
```

这种方法有一些怪异之处,包括如果多个这样的模块循环依赖对方,会出现意想不到的行为. 因此,我建议不替换该物体。 如果您想要同时指定多个导出, 使用对象字元样式定义, 您可以这样做:

```js
Object.assign(module.exports,{
   // .. exports ..
});
```

这里正在发生的是 定义`{ .. }`带有您模块的 公共 API 指定的对象文字, 然后`Object.assign(..)`正在将所有这些属性的浅表复制到现有的`module.exports`这是方便和更安全的模块行为之间的良好平衡。

要将另一个模块实例纳入您的模块/程序,请使用节点`require(..)`方法。 假设这个模块位于"/path/to/stuent.js",这就是我们如何访问它:

```js
var Student = require("/path/to/student.js");

Student.getName(73);
// Suzy
```

`Student`现在引用我们示范模块的 API。

CommunityJS模块表现为单顿实例,类似于之前呈现的IIFE模块定义样式. 无论你多少次`require(..)`在同一模块中,您只需获得对单个共享模块实例的额外引用。

`require(..)`是一个全或全无的机制;它包括了模块中整个被曝光的公共API的参考. 为了有效获取部分API,典型的方法是这样:

```js
var getName = require("/path/to/student.js").getName;

// or alternately:

var { getName } = require("/path/to/student.js");
```

与经典模块格式类似,CommonJS模块的API的公开输出方法在内部模块细节上持有关闭. 这就是模块单子状态 在整个程序整个期间的维持。

| 注: |
| :--- |
| 在节点`require("student")`语句、非绝对路径( E)`"student"`) 假设“.js”文件扩展名并搜索“node modules”。 |

## 现代ES模块(ESM)

无害环境管理格式与共同联合统计系统格式有几种相似之处。 无害环境管理以文件为基础,模块实例为单数,万事皆私*默认*。 一个显著的区别是,无害环境管理文件被假定为严格模式,而不需要`"use strict"`在顶端的普拉格玛。 无法将无害环境管理定义为非限制性模式。

代替`module.exports`在共同JS中,无害环境管理使用一种`export`关键词在模块的公开 API 上曝光. 那个`import`关键词替换`require(..)`语句。 让我们调整“stures.js”以使用无害环境管理格式:

```js
export { getName };

// ************************

var records = [
    { id: 14, name: "Kyle", grade: 86 },
    { id: 73, name: "Suzy", grade: 87 },
    { id: 112, name: "Frank", grade: 75 },
    { id: 6, name: "Sarah", grade: 91 }
];

function getName(studentID) {
    var student = records.find(
        student => student.id == studentID
    );
    return student.name;
}
```

这里唯一的变化是`export { getName }`语句。 和以前一样`export`声明可以出现在文件的任何地方,尽管`export`必须是顶级范围; 它不能在任何其他块或函数内。

无害环境管理在如何实现无害环境管理方面提供了相当的差别。`export`可以指定语句。 例如:

```js
export function getName(studentID) {
    // ..
}
```

虽然`export`出现在`function`关键字在这里,这个表还是`function`也恰好被导出。 也就是说,`getName`标识符是*函数升起的*(见第5章),因此整个模块范围都可以使用.

另一个允许的变异:

```js
export default function getName(studentID) {
    // ..
}
```

这是所谓的"默认输出",它与其他出口有不同的语义. 实质上,“默认导出”是模块消费者在使用时的简称`import`,当他们只需要这个单一的默认 API 成员时,给他们一个terser 语法.

无`default`出口称为"名出口".

那个`import`关键词 - 如`export`,它只能用于任何区块或功能以外的无害环境管理的顶层,在语法上也有一些变化。 第一种称为"名导入":

```js
import { getName } from "/path/to/students.js";

getName(73);   // Suzy
```

如您所见,这个表单只从一个模块中导入特定命名的公用API成员(抽取未明确命名的任何东西),它将这些标识符添加到当前模块的顶级范围. 这种类型的导入是那些用来用Java等语言包装导入的习惯风格.

多个 API 成员可以列在`{ .. }`设置,用逗号分隔。 命名的导入也可以与`as`关键字:

```js
import { getName as getStudentName }
   from "/path/to/students.js";

getStudentName(73);
// Suzy
```

若为`getName`是模块的“ 默认导出 ”, 我们可以这样导入:

```js
import getName from "/path/to/students.js";

getName(73);   // Suzy
```

这里唯一的区别是放弃`{ }`环绕导入绑定。 如果您想要将默认导入与其他命名的导入混合:

```js
import { default as getName, /* .. others .. */ }
   from "/path/to/students.js";

getName(73);   // Suzy
```

相比之下,另一个主要变化是:`import`名为“ 命名空间导入 ”:

```js
import * as Student from "/path/to/students.js";

Student.getName(73);   // Suzy
```

很明显,`*`导入全部导出到 API,默认并命名,并将其全部存储在指定的单一名称空间标识符中。 这种方法与大多数JS历史的经典模块形式最为吻合.

| 注: |
| :--- |
| 截至编写本报告之时,现代浏览器已经支持无害环境管理数年了,但节点对无害环境管理的稳定支持是相当近期的,并且已经发展了相当一段时间。 这一演变可能持续一年或更长的时间;在ES6中向JS引入无害环境管理,为Node与CommonJS模块的兼容性带来了一些具有挑战性的关切。 咨询节点无害环境管理文件的所有最新细节:https://nodejs.org/api/esm.html |

## 退出范围

无论是使用经典模块格式(浏览器或节点),通用JS格式(在节点),还是无害环境管理格式(浏览器或节点),模块都是构建和组织程序功能和数据的最有效方式之一.

模块模式是我们在这本学习书中旅程的结束,我们如何利用词汇范围规则将变量和功能放置在适当的地点. POLE是防守性 * 默认的* 姿态,我们总是采取,确保我们避免过度暴露,并且只与必需的最小公共API表面积相互作用.

在模块下方,我们模块状态如何维持的 *magic* 是利用词汇范围系统的关闭。

即为正文. 恭喜你一路走来 正如我多次说过的, 这是一个非常好的主意,暂停,反省, 实践我们刚才讨论的。

当你感到舒适和准备好的时候,看看附录,这些附录更深入地挖掘出这些话题的一些角落,同时也挑战你进行一些练习来巩固你学到的知识.
