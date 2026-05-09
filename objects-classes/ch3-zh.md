# 你不了解JS然而:对象和类 - 第2版
# 第3章:类物体

| 注: |
| :--- |
| 进行中的工作 |

类设计图案一般需要定义一个*类型的事物*(类),包括数据(成员)和行为(方法),然后将这一类定义的一个或多个具体*ints*创建为能够相互作用和执行任务的实际对象. 此外,阶级导向还允许通过所谓的"继承"来宣告两个或两个以上阶级之间的关系,从而衍生出新的和增强的"子阶级",这些子阶级混合-n-匹配甚至重新定义行为.

在ES6(2015年)之前,JS开发者利用平面函数和对象,模仿了班级导向(aka "object-objective")设计的各个方面,同时使用了Plain函数和对象.`[[Prototype]]`机制(如前一章所解释)——故称为"原型班".

但对于许多开发者来说,ES6引入了专用语法,包括`class`和`extends`关键词,以更公开的方式表达面向阶级的设计.

在ES6的时候`class`这个新的专用语法 几乎完全只是合成糖 使类定义更方便易读 然而,在ES6之后的多年中,`class`已经成熟并发展成为它自己的一级特征机制,积累了大量的专用语法和复杂的行为,远远超过了ES6之前的"原始类"能力.

虽然`class`现在几乎与旧的"原型"代码风格没有相似之处,JS引擎仍然*只是*通过现有的将物体连接在一起.`[[Prototype]]`机制。 也就是说,`class`并不是它自己的语言独立支柱(如:`[[Prototype]]`),但更像是花哨,装饰性 *Capital * 顶端是柱子/柱子.

这么说吧,因为...`class`样式代码现已取代了几乎所有以前的“ prototypal 类” 编码, 这里的正文只注重`class`以及各种细节。 为了历史目的,我们将在附录中简要地叙述一下旧的"原始阶级"风格.

## 我什么时候该上课?

班级导向是一种设计模式,这意味着它是你如何组织程序的信息和行为的一种选择. 它有利弊。 这不是所有任务的通用解决方案.

你怎么知道什么时候该上课?

在理论意义上,类定向是一种将程序的商业领域划分为一个或多个片段的方法,每个片段都可以由"is-a"分类来定义:将一个事物分组到该事物与其他类似事物共有的特征的集合(或组合)中. 你会说"X是Y",意思是X拥有(至少)某种Y的特性.

例如,考虑计算机。 我们可以说一台计算机是电气的,因为它使用电流(电压,安培等)作为动力. 它进一步电子化,因为它操纵电流超越了简单的绕行电子(电/磁场),创造了一个有意义的电路来操纵电流来完成更复杂的任务. 相比之下,一个基本的办公桌灯是电气的,但并不是真正的电子的.

我们可以这样定义一个阶级`Electrical`来描述电气设备需要并且能够做什么。 然后我们可以再订个班`Electronic`,并定义除了电气,`Electronic`事情操纵电力 创造更专业的结果。

这里的课定向开始闪耀。 而不是重新定义所有`Electrical`特性`Electronic`班级,我们可以定义`Electronic`以这样的方式,它"分享"或"固有"这些特性来自`Electrical`,然后增强/重新定义设备电子化的独特行为。 两个阶级之间的这种关系——称为"继承"——是阶级导向的一个关键方面.

因此,分类导向是一种思考我们程序需要的实体的方式,并根据它们的特性(它们持有什么信息,可以在这些数据上进行什么操作)将它们分类,并定义不同分类特征之间的关系.

但是,从理论转向一个更实用的视角:如果你的程序需要同时持有和使用各种数据/行为的多个集合(内存),你*可能从班级定向中受益。

### 实例的时间

以下是一个简短的插图.

几十年前, 就在我读完大学几乎所有的计算机科学学位之后, 我发现自己坐在我第一个专业软件开发员的工作上。 我负责自己建造一个计时表和工资跟踪系统。 我在PHP中构建了后端(使用MySQL为DB),并在接口中使用JS(早在世纪之交的成熟期).

由于我的CS学位在整个课程中都大力强调班级定向,我迫切希望将所有理论付诸实践。 对于我的程序设计,我将"时表"实体的概念定义为2-3"周"实体的集合,每个"周"则定义为5-7"日"实体的集合,每个"日"则定义为"任务"实体的集合.

如果我想知道有多少小时被记录在一个计时表实例, 我可以打电话给一个`totalTime()`操作在那个例子。 计时表通过循环数周的收藏,调用`totalTime()`并总结每个值。 每星期都这样做,每一天都这样做。

这里所阐释的概念,如阶级导向等设计模式的基本原理之一,被称为*封装*. 每个实体级别封装(如控制,隐藏,抽象)内部细节(数据和行为),同时呈现一个有用的外部界面.

但是,单凭封装并不能充分证明有阶级倾向. 其他设计模式提供了足够的封装.

我的班级设计如何利用遗产? 我有一个基础课 定义了一套操作像`totalTime()`,而我的每个实体类 扩展/子分类这个基类。 这意味着,他们每个人都继承了这种总和的全时能力,但其中每个人都对如何完成这项工作的内部细节适用了自己的延伸和定义。

游戏中的设计模式还有另一个方面,即*组合*:每个实体被定义为其他实体的集合.

### 单倍数

我在上面提到,一个决定你是否需要班级导向的务实方式是,你的节目是否会出现多种单一类型/类型的行为(aka,"阶级"). 在计时表的例子中,我们有4个课:计时表、周、日和任务。 但对于每个班级,我们同时有多个实例。

难道我们只需要一个单一的类, 就像一个`Computer`事情是一个实例`Electronic`班级,是`Electrical`以班级为导向可能不会产生相当大的好处。 尤其是如果程序不需要创建实例`Electrical`分门别类没什么好处`Electrical`从`Electronic`因此,我们没有得到任何真正的帮助 从继承方面 面向阶级。

所以,如果你发现自己通过将一个商业问题域分为不同的“类”实体来设计一个程序, 但是在程序的实际代码中,你只需要一个具体的 * 东西 * 一种行为的定义(aka, “类” ), 你可能根本不需要“类”。 还有其他设计模式 可能更符合你的努力

但是如果你发现自己想要定义阶级, 和继承这些阶级的子阶级, 如果你要对其中一个或几个阶级进行反复的即兴表演, 那么以阶级为导向就是一个很好的人选。 为了在JS中进行课堂定向,你需要`class`关键词

## 留着吧`class`区域

`class`定义一个类的声明或表达式。 作为声明,类定义出现在语句位置上,类似这样:

```js
class Point2d {
    // ..
}
```

作为表达式,类定义出现在值位置上,可以有名字,也可以匿名:

```js
// named class expression
const pointClass = class Point2d {
    // ..
};

// anonymous class expression
const anotherClass = class {
    // ..
};
```

a 的内容`class`机构通常包括一个或多个方法定义:

```js
class Point2d {
    setX(x) {
        // ..
    }
    setY(y) {
        // ..
    }
}
```

内置 a`class`机构、方法的定义没有`function`关键词,没有`,`或`;`方法定义之间的分隔符。

| 注: |
| :--- |
| 内置 a`class`块中,所有代码都以严格模式运行,即使没有`"use strict"`文件或其函数中的 pragma。 这尤其影响到`this`如第四章所述,功能调用的行为。 |

### 构造器

所有班级都有一种特殊的方法叫做"建筑师". 如果省略,定义中会假设有一个默认的空构造器.

构造器随时被引用 a`new`创建类别实例:

```js
class Point2d {
    constructor() {
        console.log("Here's your new instance!");
    }
}

var point = new Point2d();
// Here's your new instance!
```

尽管语法意味着一个实际命名的函数`constructor`, JS 定义了指定的函数,但该类的名称(`Point2d`上文所列:

```js
typeof Point2d;       // "function"
```

这不仅仅是一个正常的功能,虽然;这种特殊功能的表现有些不同:

```js
Point2d.toString();
// class Point2d {
//   ..
// }

Point2d();
// TypeError: Class constructor Point2d cannot
// be invoked without 'new'

Point2d.call({});
// TypeError: Class constructor Point2d cannot
// be invoked without 'new'
```

您可以按需要构建多个不同的类:

```js
var one = new Point2d();
var two = new Point2d();
var three = new Point2d();
```

每一个`one`, `two`,以及`three`这里的对象是独立实例`Point2d`课。

| 注: |
| :--- |
| 每一个`one`, `two`,以及`three`对象有`[[Prototype]]`与《公约》的联系`Point2d.prototype`对象(见第2章)。 在这个代码中,`Point2d`两者都是`class`定义和同名的构造函数。 |

如果向对象添加属性`one`:

```js
one.value = 42;
```

地产现在只存在于`one`,而且不存在任何独立`two`或`three`对象可以访问:

```js
two.value;      // undefined
three.value;    // undefined
```

### 分类方法

如上所示,类定义可以包括一个或多个方法定义:

```js
class Point2d {
    constructor() {
        console.log("Here's your new instance!");
    }
    setX(x) {
        console.log(`Setting x to: ${x}`);
        // ..
    }
}

var point = new Point2d();

point.setX(3);
// Setting x to: 3
```

那个`setX`属性( 方法) * 似乎 * 它存在于( 属 )`point`这里的对象。 但那是个幻觉 每种类方法都添加到`prototype`对象,是构造器函数的属性。

这么说`setX(..)`仅作为`Point2d.prototype.setX`。自`point`这是`[[Prototype]]`链接到`Point2d.prototype`(见第2章)`new`关键词即时`point.setX(..)`引用通过`[[Prototype]]`链条并找到执行方法。

分类方法只能通过一个案例加以援引;`Point2d.setX(..)`因为没有这样的财产 所以没用 你*可以引用`Point2d.prototype.setX(..)`,但是这在标准的面向阶级的编码中一般并不恰当/建议. 总是通过实例访问类方法.

## 类实例`this`

我们会掩护`this`关键词在接下来的一章中要详细得多。 但是,关于面向阶级的代码,`this`关键词一般是指任何方法引用的当前实例。

在构造器以及任何方法中,都可以使用`this.`以添加或访问当前实例中的属性:

```js
class Point2d {
    constructor(x,y) {
        // add properties to the current instance
        this.x = x;
        this.y = y;
    }
    toString() {
        // access the properties from the current instance
        console.log(`(${this.x},${this.y})`);
    }
}

var point = new Point2d(3,4);

point.x;                // 3
point.y;                // 4

point.toString();       // (3,4)
```

任何不持有函数值的属性,被添加到类实例中(通常是通过构造器),都被称为*member *,而不是用于可执行函数的术语*方法*.

虽然`point.toString()`方法正在运行,其`this`参考指的物体是:`point`参考文献。 这就是为什么两者`point.x`和`this.x`显示同样的`3`构造器与其`this.x = x`操作。

### 公共领域

而不是刻不容缓地通过`this.`在建构器或方法中,类可以声明定义`class`机构,直接对应每个场合将设立的成员:

```js
class Point2d {
    // these are public fields
    x = 0
    y = 0

    constructor(x,y) {
        // set properties (fields) on the current instance
        this.x = x;
        this.y = y;
    }
    toString() {
        // access the properties from the current instance
        console.log(`(${this.x},${this.y})`);
    }
}
```

公共领域可以有价值初始化,如上所示,但这并不需要. 如果不在类定义中初始化一个字段,你几乎总是应该在构建器中初始化它.

田间也可以相互参照,通过自然`this.`访问语法:

```js
class Point3d {
    // these are public fields
    x
    y = 4
    z = this.y * 5

    // ..
}
```

| 提普: |
| :--- |
| 您大多可以想到 公开的场声明 仿佛它们出现在顶端`constructor(..)`,每个前缀都包含`this.`在声明书中省略`class`身体形态。 但是,有一个赶上! 请参看"That's Super!" 稍后的更多信息. |

与计算的财产名称一样(见第1章),可以计算字段名称:

```js
var coordName = "x";

class Point2d {
    // computed public field
    [coordName.toUpperCase()] = 42

    // ..
}

var point = new Point2d(3,4);

point.x;        // 3
point.y;        // 4

point.X;        // 42
```

#### 避免这个

一种已经出现并变得相当受欢迎的模式, 但我坚信它是一个反模式`class`,看起来如下:

```js
class Point2d {
    x = null
    y = null
    getDoubleX = () => this.x * 2

    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
    toString() { /* .. */ }
}

var point = new Point2d(3,4);

point.getDoubleX();    // 6
```

见球场持有`=>`箭头函数? 我说这是不 为什么? 解风解风.

首先,为什么这样做? 因为JS的开发者 似乎永远受挫于动态`this`(见第4章)`this`通过`=>`箭头函数。 那样,不管怎样`getDoubleX()`被引用,它总是`this`- 具体地说 这是对欲望的一种可以理解的便利, 但是... 它背叛了本质`this` / `[[Prototype]]`语言的支柱。 怎么会?

让我们考虑一下与之前的片段等同的代码:

```js
class Point2d {
    constructor(x,y) {
        this.x = null;
        this.y = null;
        this.getDoubleX = () => this.x * 2;

        this.x = x;
        this.y = y;
    }
    toString() { /* .. */ }
}

var point = new Point2d(3,4);

point.getDoubleX();    // 6
```

你能发现问题吗? 仔细看 我会等的

...

我们一再表明,`class`定义将其方法放在类构造器上`prototype`物体--这就是它们属于的地方! (笑声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) (掌声) 事情就是这样`toString()`在上述片断中。

但是,怎么样`getDoubleX()`这基本上是一种阶级方法, 但不会由JS处理 相当一样`toString()`将. 考虑:

```js
Object.hasOwn(point,"x");               // true -- good
Object.hasOwn(point,"toString");        // false -- good
Object.hasOwn(point,"getDoubleX");      // true -- oops :(
```

你看见了吗? 通过定义函数值并将其附加为字段/成员属性,我们失去了函数的共享原型方法性,它就像任何偶入属性一样. 这意味着我们创建了一个新的函数属性 ** 对于每个实例**, 而不是仅仅创建一次 在类构造者`prototype`.

这在表现和记忆中是浪费的,即使一点点。 仅这一点就足以避免这种情况。

但我会认为,更重要的是, 你用这种模式所做的 使得使用`class`和`this`- 认识的方法甚至是远程有用/强大的!

如果你去所有困难 定义类方法`this.`在整个过程中,但是你把大多数或所有的方法锁定/固定到一个特定的物体实例上, 你基本上到处走,只是去隔壁。

如果你想要的只是静态固定到特定“文本”的函数,而不需要任何动态或共享,你想要的是... ** 关闭**。 你走运了:我在这个系列("范围与关闭")中写了一本关于如何使用关闭的全书,这样函数就能记住/访问他们静态定义的范围(aka "context"). 这种方法更合适, 更简单的代码, 接近得到你的目标。

不要虐待/滥用`class`并变成超赞,颂扬的闭关文集.

说得很清楚,我不是说,永远不要用`=>`箭头在类内函数。

我说: 永远不要附着`=>`箭头作为实例属性发挥作用,以取代动态原型类方法,或者出于无脑习惯,或者在输入较少字符时懒惰,或者被误导`this`- 装订方便。

在接下来的一章中,我们将深入到如何理解和正确利用动力的全能`this`机制。

## 类扩展

解锁阶级遗产权力的方法是`extends`关键词,它定义了两个类之间的关系:

```js
class Point2d {
    x = 3
    y = 4

    getX() {
        return this.x;
    }
}

class Point3d extends Point2d {
    x = 21
    y = 10
    z = 5

    printDoubleX() {
        console.log(`double x: ${this.getX() * 2}`);
    }
}

var point = new Point2d();

point.getX();                   // 3

var anotherPoint = new Point3d();

anotherPoint.getX();            // 21
anotherPoint.printDoubleX();    // double x: 42
```

花点时间重新阅读代码片段,确保你完全了解正在发生的事情.

基础类`Point2d`定义名为`x`和`y`,并给出初始值`3`和`4`分别为: 它还界定了`getX()`访问的方法`x`实例成员并返回。 我们看到行为表现在`point.getX()`方法调用。

不过`Point3d`类扩展`Point2d`,制作`Point3d`a 衍生级、儿童级或(最常见的)子类。 内`Point3d`,同样的`x`继承自`Point2d`以不同方式重新初始化`21`数值,如同`y`覆盖到数值`4`改为:`10`.

还增加了一个新的`z`字段/成员方法以及`printDoubleX()`方法,它本身称为`this.getX()`.

何时`anotherPoint.printDoubleX()`被引用,继承`this.getX()`因此,该方法提及:`this.x`。自`this`正在指向阶级案例。`anotherPoint`),它现在找到的价值`21`(代替:`3`从`point`对象的`x`成员)。

### 扩展表达式

//TODO:覆盖`class Foo extends ..`地点`..`是表达式,不是类名

### 压倒性方法

除了在子类中压倒一个字段/成员外,您还可以压倒(重新定义)一个方法:

```js
class Point2d {
    x = 3
    y = 4

    getX() {
        return this.x;
    }
}

class Point3d extends Point2d {
    x = 21
    y = 10
    z = 5

    getX() {
        return this.x * 2;
    }
    printX() {
        console.log(`double x: ${this.getX()}`);
    }
}

var point = new Point3d();

point.printX();       // double x: 42
```

那个`Point3d`子类覆盖继承`getX()`给出不同行为的方法。 不过,你还是可以即时攻击基地的`Point2d`类,它随后会给出一个使用原始对象(`return this.x;`定义`getX()`.

如果想要从子类中获取一个继承的方法,即使它已经被覆盖,也可以使用`super`改为`this`:

```js
class Point2d {
    x = 3
    y = 4

    getX() {
        return this.x;
    }
}

class Point3d extends Point2d {
    x = 21
    y = 10
    z = 5

    getX() {
        return this.x * 2;
    }
    printX() {
        console.log(`x: ${super.getX()}`);
    }
}

var point = new Point3d();

point.printX();       // x: 21
```

在继承等级的不同级别上,同名方法在直接接触或相对接触时表现出不同行为的能力。`super`,称为*方法多态性*. 它是一个非常强大的 面向阶级,当使用适当。

### 这是超级!

除了子类方法之外,还访问一个继承的方法定义(即使子类上已覆盖)通过`super.`引用时,子类构建器必须手动通过`super(..)`函数引用:

```js
class Point2d {
    x
    y
    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
}

class Point3d extends Point2d {
    z
    constructor(x,y,z) {
        super(x,y);
        this.z = z;
    }
    toString() {
        console.log(`(${this.x},${this.y},${this.z})`);
    }
}

var point = new Point3d(3,4,5);

point.toString();       // (3,4,5)
```

| 警报: |
| :--- |
| 明确定义的子类构建器 * must * call`super(..)`来运行继承的类的初始化,这必须在子类构建者提及`this`或完成/返回。 否则,当使用该子类构造器(通过`new`) (中文(简体) ). 如果您省略了子类构造器, 默认构造器会自动-- 感谢! -- 援引`super()`给你的 |

要注意的一个细微之处是:如果你在子类内定义一个字段(公共或私人),并明确定义一个字段。`constructor(..)`对于这个子类,字段初始化不会在构造器的顶端处理,而是在 * 之间处理。`super(..)`调用和构造器中任何后续代码。

在此密切关注控制台消息的顺序:

```js
class Point2d {
    x
    y
    constructor(x,y) {
        console.log("Running Point2d(..) constructor");
        this.x = x;
        this.y = y;
    }
}

class Point3d extends Point2d {
    z = console.log("Initializing field 'z'")

    constructor(x,y,z) {
        console.log("Running Point3d(..) constructor");
        super(x,y);

        console.log(`Setting instance property 'z' to ${z}`);
        this.z = z;
    }
    toString() {
        console.log(`(${this.x},${this.y},${this.z})`);
    }
}

var point = new Point3d(3,4,5);
// Running Point3d(..) constructor
// Running Point2d(..) constructor
// Initializing field 'z'
// Setting instance property 'z' to 5
```

如控制台信息所示,`z = ..`字段初始化发生`super(x,y)`* 电话之前``console.log(`设置实例...`)``执行。 也许把它当做 尾部的实地初始化`super(..)`电话,所以他们跑 之前,其他的建筑器做。

#### 哪门课?

您可能需要在建构器中确定该类是直接被即时化,还是从带有`super()`打电话 我们可以使用特别的"Pseudo财产"`new.target`:

```js
class Point2d {
    // ..

    constructor(x,y) {
        if (new.target === Point2d) {
            console.log("Constructing 'Point2d' instance");
        }
    }

    // ..
}

class Point3d extends Point2d {
    // ..

    constructor(x,y,z) {
        super(x,y);

        if (new.target === Point3d) {
            console.log("Constructing 'Point3d' instance");
        }
    }

    // ..
}

var point = new Point2d(3,4);
// Constructing 'Point2d' instance

var anotherPoint = new Point3d(3,4,5);
// Constructing 'Point3d' instance
```

### 但哪种情况?

你也许想回顾一下某个对象实例,看看它是否是一个特定类别的实例. 我们用这个`instanceof`运算符:

```js
class Point2d { /* .. */ }
class Point3d extends Point2d { /* .. */ }

var point = new Point2d(3,4);

point instanceof Point2d;           // true
point instanceof Point3d;           // false

var anotherPoint = new Point3d(3,4,5);

anotherPoint instanceof Point2d;    // true
anotherPoint instanceof Point3d;    // true
```

可能看起来很奇怪`anotherPoint instanceof Point2d`结果是`true`为了了解为什么更好,也许可以将两者都想象出来。`[[Prototype]]`链条:

```
Point2d.prototype
        /       \
       /         \
      /           \
  point   Point3d.prototype
                    \
                     \
                      \
                    anotherPoint
```

那个`instanceof`操作者不只看当前对象,而是跨越整个阶级继承等级(即:`[[Prototype]]`链),直到找到匹配. 因此,`anotherPoint`是两个例子之一`Point3d`和`Point2d`.

为了更明显地说明这个事实, 另一种( 较少的基因组学) 方法 进行同样的检查 与`instanceof`与(从)`Object.prototype`实用性,`isPrototypeOf(..)`:

```js
Point2d.prototype.isPrototypeOf(point);             // true
Point3d.prototype.isPrototypeOf(point);             // false

Point2d.prototype.isPrototypeOf(anotherPoint);      // true
Point3d.prototype.isPrototypeOf(anotherPoint);      // true
```

这个功能更能说明为什么两者`Point2d.prototype.isPrototypeOf(anotherPoint)`和`anotherPoint instanceof Point2d`结果是`true`: 对象`Point2d.prototype`*是* 位于`[[Prototype]]`链条`anotherPoint`.

如果您想要检查对象实例是否仅由特定类直接创建, 请检查实例的`constructor`属性。

```js
point.constructor === Point2d;          // true
point.constructor === Point3d;          // false

anotherPoint.constructor === Point2d;   // false
anotherPoint.constructor === Point3d;   // true
```

| 注: |
| :--- |
| 那个`constructor`这里显示的属性是 * 不 * 实际存在于 (所有)`point`或`anotherPoint`实例对象。 那从哪来的? 每个物体上都有`[[Prototype]]`链接原型对象:`Point2d.prototype.constructor === Point2d`和`Point3d.prototype.constructor === Point3d`. |

### "继承"是分享,不是复制

可能看起来`Point3d`当它`extends`联合国`Point2d`类,实质上是获得一份*复制 * 所有行为定义`Point2d`此外,似乎似具体对象`anotherPoint`接收、复制到所有方法`Point3d`(并进而从`Point2d`).

然而,这并非是JS实施阶级导向的正确心理模式. 回顾这一基础类和次类定义以及即时`anotherPoint`:

```js
class Point2d {
    x
    y
    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
}

class Point3d extends Point2d {
    z
    constructor(x,y,z) {
        super(x,y);
        this.z = z;
    }
    toString() {
        console.log(`(${this.x},${this.y},${this.z})`);
    }
}

var anotherPoint = new Point3d(3,4,5);
```

如果你检查`anotherPoint`对象,你会看到它只有`x`, `y`,以及`z`上面的属性,但不包括`toString()`方法:

```js
Object.hasOwn(anotherPoint,"x");                       // true
Object.hasOwn(anotherPoint,"y");                       // true
Object.hasOwn(anotherPoint,"z");                       // true

Object.hasOwn(anotherPoint,"toString");                // false
```

哪里是那个`toString()`查找方法吗? 在原型物体上:

```js
Object.hasOwn(Point3d.prototype,"toString");    // true
```

还有`anotherPoint`可通过下列途径获得该方法:`[[Prototype]]`(见第2章)。 换句话说,原型物体** 分享其方法** 与子类和例。 方法保留在原位,不复制到继承链下.

不错的`class`语法是,不要忘记在语法下到底发生了什么: JS只是* 沿着一个线将物体连接在一起。`[[Prototype]]`链条。

## 静态行为类

迄今为止,我们强调数据或行为(方法)存在的两个不同地点: 在构造者的原型上,或者在实例上。 但还有第三种选择:在构造器(函数对象)本身上.

在一个传统的面向阶级的体系中,一个阶级上定义的方法并不是你能引用或与之互动的具体事物. 你必须要即兴表演一个阶级 才能有具体的目标来使用这些方法 JS等原始语言模糊了这一行:所有阶级定义的方法都是"真实的"功能,都停留在构造者的原型上,因此你可以引用它们. 但是,正如我先前所说,你真的不应该这样做,因为这不是JS认为你会写你`class`是的,有些怪异的角箱行为 你可能会遇到。 最好继续走这条窄路`class`为你准备

并不是所有我们定义和想要与类关联/组织的行为 *需要 * 了解一个实例。 此外,有时一个类需要公开定义使用该类的开发者需要访问的数据(如常数),独立于他们可能或可能已经创建的任何实例.

因此,一个类系统如何能够定义这种数据和行为,而这种数据和行为应该有一个类,但独立于(不注意)即时物体? ** 稳定属性和功能**。

| 注: |
| :--- |
| 我将使用"静态属性"/"静态函数",而不是"成员"/"方法",只是这样可以更清楚地看出,在有实例约束的成员/实例意识的方法,与无实例属性和无实例功能之间有区别. |

我们用`static`我们的关键`class`区分这些定义的机构:

```js
class Point2d {
    // class statics
    static origin = new Point2d(0,0)
    static distance(point1,point2) {
        return Math.sqrt(
            ((point2.x - point1.x) ** 2) +
            ((point2.y - point1.y) ** 2)
        );
    }

    // instance members and methods
    x
    y
    constructor(x,y) {
        this.x = x;
        this.y = y;
    }
    toString() {
        return `(${this.x},${this.y})`;
    }
}

console.log(`Starting point: ${Point2d.origin}`);
// Starting point: (0,0)

var next = new Point2d(3,4);
console.log(`Next point: ${next}`);
// Next point: (3,4)

console.log(`Distance: ${
    Point2d.distance( Point2d.origin, next )
}`);
// Distance: 5
```

那个`Point2d.origin`是一个静态的地产, 恰好有我们这个阶级的建筑实例。 还有`Point2d.distance(..)`是计算两点之间二维笛卡尔距离的静态函数。

當然,我們可以把這兩個人放在別的地方,而不是...`static`关于类的定义。 但是,由于它们与`Point2d`同学们,把他们安排到那里最有意义

| 注: |
| :--- |
| 不要忘记,当你使用`class`语法, 名称`Point2d`实际上是JS定义的构造器函数的名称. 这么说`Point2d.origin`仅仅是该函数对象的正常属性访问。 这就是我在这一部分的顶部的意思 当我提到第三个地点 存储 * 东西 * 与类; 在JS,`static`s作为属性存储在构造器函数上。 注意不要混淆那些 与存储在构造器上的属性`prototype`(方法)和存储在实例上的属性(成员). |

### 静态属性初始化

静态初始化中的值 (`static whatever = ..`)可包括`this`引用,指类本身(实际上是指构建者)而不是实例:

```js
class Point2d {
    // class statics
    static originX = 0
    static originY = 0
    static origin = new this(this.originX,this.originY)

    // ..
}
```

| 警报: |
| :--- |
| 我并不建议你这么做`new this(..)`骗局我在这里说明。 这只是为了说明问题 密码读得更干净`new Point2d(this.originX,this.originY)`,所以更喜欢这种方式。 |

与公共领域初始化不同的是,它只发生一次即时化(与`new`)发生时,类静态初始化总是运行 * 立即 * 之后`class`已定义。 此外,静态初始化的顺序很重要;你可以把这些语句想成是一次被评价.

与类成员一样,静态属性也无需初始化(默认:`undefined`)),但这样做更为常见. 申报一个没有初始价值的静态属性没有什么用处(`static whatever`; 获取`Point2d.whatever`或`Point2d.nonExistent`两者都会导致`undefined`.

最近(ES2022号),`static`关键字被扩展,所以现在可以定义其中的块`class`更复杂的初始化`static`编号:

```js
class Point2d {
    // class statics
    static origin = new Point2d(0,0)
    static distance(point1,point2) {
        return Math.sqrt(
            ((point2.x - point1.x) ** 2) +
            ((point2.y - point1.y) ** 2)
        );
    }

    // static initialization block (as of ES2022)
    static {
        let outerPoint = new Point2d(6,8);
        this.maxDistance = this.distance(
            this.origin,
            outerPoint
        );
    }

    // ..
}

Point2d.maxDistance;        // 10
```

那个`let outerPoint = ..`这里不是特别的`class`特性; 它就像一个正常的`let`在任何正常范围块中声明(参见本系列的"范围与关闭"书). 我们只是宣布一个本地化的例子`Point2d`分配给`outerPoint`,然后使用该值得出转让给`maxDistance`静态属性。

静态初始化区块对诸如`try..catch`语句围绕表达式计算。

### 静态继承

类静态由子类继承(显然,作为静态!),可以被覆盖,以及`super`用于基准类参考(和静态函数多态性),所有方法都与继承方法与实例成员/方法相同:

```js
class Point2d {
    static origin = /* .. */
    static distance(x,y) { /* .. */ }

    static {
        // ..
        this.maxDistance = /* .. */;
    }

    // ..
}

class Point3d extends Point2d {
    // class statics
    static origin = new Point3d(
        // here, `this.origin` references wouldn't
        // work (self-referential), so we use
        // `super.origin` references instead
        super.origin.x, super.origin.y, 0
    )
    static distance(point1,point2) {
        // here, super.distance(..) is Point2d.distance(..),
        // if we needed to invoke it

        return Math.sqrt(
            ((point2.x - point1.x) ** 2) +
            ((point2.y - point1.y) ** 2) +
            ((point2.z - point1.z) ** 2)
        );
    }

    // instance members/methods
    z
    constructor(x,y,z) {
        super(x,y);     // <-- don't forget this line!
        this.z = z;
    }
    toString() {
        return `(${this.x},${this.y},${this.z})`;
    }
}

Point2d.maxDistance;        // 10
Point3d.maxDistance;        // 10
```

你可以看到,静态的属性`maxDistance`我们定义在`Point2d`作为静态财产继承`Point3d`.

| 提普: |
| :--- |
| 记住: 每当您定义一个子类构造器时, 您需要呼叫`super(..)`在其中,通常作为第一个语句. 我觉得这很容易忘记 |

别忽略JS的行为 与之前讨论的方法继承一样,静态的"继承"是*不是*将这些静态属性/功能从基类复制到次类;它通过基类共享.`[[Prototype]]`链条。 具体来说,构建器函数`Point3d()`拥有`[[Prototype]]`由联署材料更改的链接(自默认`Function.prototype`改为`Point2d`,这是允许`Point3d.maxDistance`代表`Point2d.maxDistance`.

同样有趣的是,也许现在只有历史, 才注意到这种静态的继承—— 这是原始ES6的一部分`class`机制特性集!——是超越"只是语法糖"的一个特定特性. 正如我们在这里所看到的,在ES6之前不可能在JS中以原始的典型的法典风格实现/融入静态遗产。 这是一种特殊的新行为,仅在ES6上引入.

## 私人行为

我们讨论过的一切都是...`class`定义是公开可见/可访问的,既可以是类上的静态属性/功能,也可以是构造器上的方法。`prototype`或成员财产。

但如何储存无法从班外看到的信息? 这是最需要的特征之一,也是对联署材料的最大投诉。`class`直到最后在ES2022中解决为止

`class`现在支持新语法用于宣布私有域(内部成员)和私有方法. 此外,私人静态特性/功能也是可能的。

### 动机?

在我们说明怎么做之前`class`私人,它值得思考 为什么这是一个有用的特征?

有了面向关闭的设计模式(再次参见本系列的"范围与关闭"一书),我们自动得到"隐私"内置. 当您在范围内声明一个变量时,不能在范围外看到它。 期间。 减少宣言的能见度有助于防止命名空间碰撞(相同的变量名称)。

但更重要的是确保软件的"防御性"设计,即所谓的"最少特权原则".[^POLP]. POLP指出,我们只应该将软件中的一条信息或能力暴露在必要的最小表面。

过度曝光开启了我们的软件 几个问题 使软件的安全/维护复杂化, 包括另一块代码 恶意行为 做一些 我们的代码没有预期或意图。 此外,我们软件的其他部分 依赖(使用)我们代码的部分 也存在不那么关键,但仍然有问题的关切, 我们应该保留作为隐藏的执行细节。 一旦其他代码依赖于我们代码的执行细节,我们不再能够重构我们的代码而不可能会破坏程序的其他部分.

因此,简言之,我们*应该隐藏执行细节,如果它们没有必要被曝光的话。 从这个意义上讲,联署材料`class`系统感觉有点太放纵了 因为它的一切 默认是公开的。 班级的私人功能是对更适当的软件设计的一种受欢迎的补充。

#### 太私人吗?

这一切说,我不得不扔 一点点大坝在课堂上的私人派对。

我强烈建议你只用`class`如果你真的要利用 大部分或所有 面向阶级给你。 否则,你会更适合使用JS的其他核心支柱特征来组织代码,比如使用关闭模式.

阶级取向最重要的方面之一是亚阶级继承,我们已经在本章中多次看到这一点。 猜猜一个基础课的私人成员/方法,当它被子类扩展时会发生什么?

私人成员/方法只对其在**中定义的阶级进行私分**,不**由子阶级以任何方式继承. 瓳

这似乎不是一个太大的担心, 直到你开始合作`class`和真实软件中的私人成员/方法。 你可能很快会遇到一个情况,即你需要使用一种私人方法,甚至更经常地,只是一个来自子类的私人成员,这样子类就可以按照期望扩展/提升基础类的行为. 一旦你意识到这是不可能的 你可能会很快在沮丧中尖叫

接下来的必然是一个尴尬的决定:你只是回去公开,这样子阶级才能访问它? 瓳 或者,更糟糕的是,你是否尝试重新设计基础课,来调节其会员/方法的设计,这样,缺乏访问的途径就部分地解决了. 这往往涉及方法的过度参数化(以私密参数为默认参数值),以及其他此类诡计。 双关

老实说 这里没有特别好的答案 如果你在Java或C++等较传统的班级语言上有课前定向经验, 你可能怀疑为什么我们没有在*公共*和*私人*之间有*保护*的能见度。 这就是“保护”的目的:对一个阶级及其任何子阶级保守秘密。 这些语言也有"朋友"的特征,但这超出了我们这里的讨论范围.

不幸的是,不仅联署材料没有 " 保护 " 的能见度,而且似乎(即使如此有用!)不可能成为联署材料的特点。 十多年来(在ES6甚至成为一件事情之前),人们都非常详细地讨论了这个问题,对此也提出了多项建议.

我不应该说它永远不会发生, 因为这不是任何软件的可靠基础。 但这不太可能 因为它实际上背叛了支柱`class`继续发展。 如果你好奇,或者(更可能)确定有办法, 我会在附录中描述“受保护的”能见度在JS机制内的不相容性。

重点是,从现在起,JS没有 保护的能见度, 不会很快。 而*受保护的*能见度实际上比*私人的能见度更有用.

因此,我们回到一个问题:** 为什么你要考虑做任何`class`私人内容? **

如果我是诚实的,也许你不应该。 或许你应该去 这取决于你。 直接去了解这些绊脚石

### 私人成员/方法

你很高兴终于看到 魔法的语法 * 私人* 的知名度,对不对? 如果对即将到来的事物感到愤怒或悲伤,请不要射杀使者.

```js
class Point2d {
    // statics
    static samePoint(point1,point2) {
        return point1.#ID === point2.#ID;
    }

    // privates
    #ID = null
    #assignID() {
        this.#ID = Math.round(Math.random() * 1e9);
    }

    // publics
    x
    y
    constructor(x,y) {
        this.#assignID();
        this.x = x;
        this.y = y;
    }
}

var one = new Point2d(3,4);
var two = new Point2d(3,4);

Point2d.samePoint(one,two);         // false
Point2d.samePoint(one,one);         // true
```

不,JS没有做明智的事情 介绍一个`private`关键词就像他们用`static`相反,他们介绍了`#`. (插入关于社交媒体的恶作剧 迷恋标签,或什么的)

| 提普: |
| :--- |
| 是的,有一百万和一次讨论 为什么不。 我可以花章节 讲述整个历史, 但说实话,我只是不关心。 我认为这个语法很丑陋,很多其他人也一样. 有人喜欢它! 如果你在后一个营地, 虽然我很少这样做, 我只是要说:** 接受它**。 为时已晚,不得再辩论或恳求. |

那个`#whatever`语法(包括`this.#whatever`窗体)只在内部有效`class`尸体,还有尸体。 如果在外使用, 它会丢弃语法错误`class`.

与公共领域/成员不同,私人领域/成员 *必须* 宣布为`class`躯体。 在构建者方法中,您不能在类声明中动态添加私人成员;`this.#whatever = ..`类型任务仅在`#whatever`私人领域在类体中宣布。 此外,虽然可以重新分配私人田地,但不能`delete`d 从实例来看,公共领域/阶级成员可以采用的方式。

#### 子类+ 私人

我先前警告说,对有私人成员/方法的班级进行分类,可能是一个限制性陷阱。 但这并不意味着它们不能一起使用.

因为JS中的"继承"是共享(通过`[[Prototype]]`如果在子类中引用一个继承方法,而该继承方法又在主机(基地)类中访问/输入私人,则这个方法可以:

```js
class Point2d { /* .. */ }

class Point3d extends Point2d {
    z
    constructor(x,y,z) {
        super(x,y);
        this.z = z;
    }
}

var one = new Point3d(3,4,5);
```

那个`super(x,y)`在此构造器中调用所继承的基类构造器(`Point2d(..)`),它本身访问`Point2d`私人方法`#assignID()`(见早先的片段). 没有例外,尽管`Point3d`无法直接看到或访问`#ID` / `#assignID()`实际存储在实例上的私人(名称:`one`这里).

事实上,即使是继承`static samePoint(..)`函数将工作于其中任一`Point3d`或`Point2d`:

```js
Point2d.samePoint(one,one);         // true
Point3d.samePoint(one,one);         // true
```

事实上,这应该不奇怪,因为:

```js
Point2d.samePoint === Point3d.samePoint;
```

继承的函数引用是*与基准函数引用完全相同的函数*;它不是函数的某个复制品. 因为这个功能没有`this`在其中提到的,无论从何处援引,它都应该产生同样的结果。

可惜还是这样`Point3d`无法进入/影响,甚至无法了解`#ID` / `#assignID()`私人从`Point2d`:

```js
class Point2d { /* .. */ }

class Point3d extends Point2d {
    z
    constructor(x,y,z) {
        super(x,y);
        this.z = z;

        console.log(this.#ID);      // will throw!
    }
}
```

| 警报: |
| :--- |
| 注意此片段在定义时会抛出一个早期的静态语法错误`Point3d`班级,甚至没有机会创造一个班级的例子。 如果提及`super.#ID`改为`this.#ID`. |

#### 存在检查

记住,只有`class`自己知道,因此可以检查这样一个私人领域/方法。

您可能想要检查对象实例上是否存在私有字段/ 方法。 例如(如下所示),在一个类中,您可能具有静态函数或方法,该类接收一个外部对象的引用通过. 要检查通过对象引用是否属于这一类(因此在其中也有相同的私人成员/方法),基本上需要针对对象进行"品牌检查".

这种检查可能相当曲折,因为如果你进入一个在物体上并不存在的私人领域,你得到一个JS例外,需要丑陋的`try..catch`逻辑

但有一个更清洁的方法,所以被称为"基因组品牌检查",使用`in`关键字:

```js
class Point2d {
    // statics
    static samePoint(point1,point2) {
        // "ergonomic brand checks"
        if (#ID in point1 && #ID in point2) {
            return point1.#ID === point2.#ID;
        }
        return false;
    }

    // privates
    #ID = null
    #assignID() {
        this.#ID = Math.round(Math.random() * 1e9);
    }

    // publics
    x
    y
    constructor(x,y) {
        this.#assignID();
        this.x = x;
        this.y = y;
    }
}

var one = new Point2d(3,4);
var two = new Point2d(3,4);

Point2d.samePoint(one,two);         // false
Point2d.samePoint(one,one);         // true
```

那个`#privateField in someObject`如果找不到此字段, 检查将不会丢弃一个例外, 所以不用它就可以安全使用`try..catch`并使用它简单的布尔结果。

#### 过滤

即使成员/方法可以以 * 私人* 的能见度被宣布,它仍然可以从一个类例中被过滤(摘录):

```js
var id, func;

class Point2d {
    // privates
    #ID = null
    #assignID() {
        this.#ID = Math.round(Math.random() * 1e9);
    }

    // publics
    x
    y
    constructor(x,y) {
        this.#assignID();
        this.x = x;
        this.y = y;

        // exfiltration
        id = this.#ID;
        func = this.#assignID;
    }
}

var point = new Point2d(3,4);

id;                     // 7392851012 (...for example)

func;                   // function #assignID() { .. }
func.call(point,42);

func.call({},100);
// TypeError: Cannot write private member #ID to an
// object whose class did not declare it
```

这里主要关注的是,在通过私人方法作为回调(或以任何方式将私人暴露于程序的其他部分)时要小心. 没有什么可以阻止你这样做的, 这可以造成 一点点意外的隐私披露。

### 私人静态

静态属性和函数也可以使用`#`标记为私有:

```js
class Point2d {
    static #errorMsg = "Out of bounds."
    static #printError() {
        console.log(`Error: ${this.#errorMsg}`);
    }

    // publics
    x
    y
    constructor(x,y) {
        if (x > 100 || y > 100) {
            Point2d.#printError();
        }
        this.x = x;
        this.y = y;
    }
}

var one = new Point2d(30,400);
// Error: Out of bounds.
```

那个`#printError()`这里的静态私有函数有`this`但这是引用`Point2d`班级,不是例。 因此,`#errorMsg`和`#printError()`不受实例影响,因此最好作为静态。 此外,他们也没有理由在课外可以进入,所以他们被标记为私人.

记住:私人静态同样不能像私人成员/方法一样被子类继承.

#### 与静态二等兵和`this`

回顾从一个子类中引用的继承方法没有使用困难(通过`this.#whatever`样式参考),任何来自本类的私人:

```js
class Point2d {
    // ..

    getID() {
        return this.#ID;
    }

    // ..
}

class Point3d extends Point2d {
    // ..

    printID() {
        console.log(`ID: ${this.getID()}`);
    }
}

var point = new Point3d(3,4,5);
point.printID();
// ID: ..
```

不错

不幸的是,而且(对我来说)出乎意料地/不连贯地说,从继承的公共静态功能上获取的私人静态并不是这样:

```js
class Point2d {
    static #errorMsg = "Out of bounds."
    static printError() {
        console.log(`Error: ${this.#errorMsg}`);
    }

    // ..
}

class Point3d extends Point2d {
    // ..
}

Point2d.printError();
// Error: Out of bounds.

Point3d.printError === Point2d.printError;
// true

Point3d.printError();
// TypeError: Cannot read private member #errorMsg
// from an object whose class did not declare it
```

那个`printError()`静态是继承的(通过`[[Prototype]]`从。`Point2d`改为:`Point3d`只是精细, 这就是为什么函数引用相同。 和上面的非静态片段一样,你可能已经预料到了`Point3d.printError()`静态援引以通过`[[Prototype]]`链条至原基类( )`Point2d`)位置,从而允许它进入基班`#errorMsg`静态私密。

但它失败了,正如该片断中最后的发言所示。 它在这里失败的原因,但不是与之前的片段,是一个曲折的大脑扭矩. 我不会去挖掘 为什么在这里解释, 坦率地说, 因为它煮我的血液这样做。

虽有 *修*. 在静态函数中,而不是`this.#errorMsg`,换成`Point2d.#errorMsg`,现在它的工作:

```js
class Point2d {
    static #errorMsg = "Out of bounds."
    static printError() {
        // the fixed reference vvvvvv
        console.log(`Error: ${Point2d.#errorMsg}`);
    }

    // ..
}

class Point3d extends Point2d {
    // ..
}

Point2d.printError();
// Error: Out of bounds.

Point3d.printError();
// Error: Out of bounds.  <-- phew, it works now!
```

如果正在继承公共静态函数,请使用类名访问任何私有静态,而不是使用`this.`参考文献。 小心那个家伙!

## 类例

好了,我们已经提出了一系列不同的阶级特征。 我想总结一下这一章, 试图用一个简单的例子来说明这些能力的样板, 这个例子比较简单。

```js
class CalendarItem {
    static #UNSET = Symbol("unset")
    static #isUnset(v) {
        return v === this.#UNSET;
    }
    static #error(num) {
        return this[`ERROR_${num}`];
    }
    static {
        for (let [idx,msg] of [
            "ID is already set.",
            "ID is unset.",
            "Don't instantiate 'CalendarItem' directly.",
        ].entries()) {
            this[`ERROR_${(idx+1)*100}`] = msg;
        }
    }
    static isSameItem(item1,item2) {
        if (#ID in item1 && #ID in item2) {
            return item1.#ID === item2.#ID;
        }
        else {
            return false;
        }
    }

    #ID = CalendarItem.#UNSET
    #setID(id) {
        if (CalendarItem.#isUnset(this.#ID)) {
            this.#ID = id;
        }
        else {
            throw new Error(CalendarItem.#error(100));
        }
    }

    description = null
    startDateTime = null

    constructor() {
        if (new.target !== CalendarItem) {
            let id = Math.round(Math.random() * 1e9);
            this.#setID(id);
        }
        else {
            throw new Error(CalendarItem.#error(300));
        }
    }
    getID() {
        if (!CalendarItem.#isUnset(this.#ID)) {
            return this.#ID;
        }
        else {
            throw new Error(CalendarItem.#error(200));
        }
    }
    getDateTimeStr() {
        if (this.startDateTime instanceof Date) {
            return this.startDateTime.toUTCString();
        }
    }
    summary() {
        console.log(`(${
            this.getID()
        }) ${
            this.description
        } at ${
            this.getDateTimeStr()
        }`);
    }
}

class Reminder extends CalendarItem {
    #complete = false;  // <-- no ASI, semicolon needed

    [Symbol.toStringTag] = "Reminder"
    constructor(description,startDateTime) {
        super();

        this.description = description;
        this.startDateTime = startDateTime;
    }
    isComplete() {
        return !!this.#complete;
    }
    markComplete() {
        this.#complete = true;
    }
    summary() {
        if (this.isComplete()) {
            console.log(`(${this.getID()}) Complete.`);
        }
        else {
            super.summary();
        }
    }
}

class Meeting extends CalendarItem {
    #getEndDateTimeStr() {
        if (this.endDateTime instanceof Date) {
            return this.endDateTime.toUTCString();
        }
    }

    endDateTime = null;  // <-- no ASI, semicolon needed

    [Symbol.toStringTag] = "Meeting"
    constructor(description,startDateTime,endDateTime) {
        super();

        this.description = description;
        this.startDateTime = startDateTime;
        this.endDateTime = endDateTime;
    }
    getDateTimeStr() {
        return `${
            super.getDateTimeStr()
        } - ${
            this.#getEndDateTimeStr()
        }`;
    }
}
```

需要一些时间来读和消化这些`class`定义。 你发现大部分`class`我们在本章谈到的特征?

| 注: |
| :--- |
| 你可能有一个问题:我为什么不移动重复的逻辑:`description`和`startDateTime`从两个子类构造器设置到单个基建器? 这是一个细微的点, 但这不是我的意图`CalendarItem`这就是我们所谓的“抽象阶级”。 所以我才会用`new.target`如果错误`CalendarItem`班级是直接即时的! 所以我不想用签名暗示`CalendarItem(..)`应当直接使用构造器。 |

现在看看这三门课在用:

```js
var callMyParents = new Reminder(
    "Call my parents to say hi",
    new Date("July 7, 2022 11:00:00 UTC")
);
callMyParents.toString();
// [object Reminder]
callMyParents.summary();
// (586380912) Call my parents to say hi at
// Thu, 07 Jul 2022 11:00:00 GMT
callMyParents.markComplete();
callMyParents.summary();
// (586380912) Complete.
callMyParents instanceof Reminder;
// true
callMyParents instanceof CalendarItem;
// true
callMyParents instanceof Meeting;
// false


var interview = new Meeting(
    "Job Interview: ABC Tech",
    new Date("June 23, 2022 08:30:00 UTC"),
    new Date("June 23, 2022 09:15:00 UTC")
);
interview.toString();
// [object Meeting]
interview.summary();
// (994337604) Job Interview: ABC Tech at Thu,
// 23 Jun 2022 08:30:00 GMT - Thu, 23 Jun 2022
// 09:15:00 GMT
interview instanceof Meeting;
// true
interview instanceof CalendarItem;
// true
interview instanceof Reminder;
// false


Reminder.isSameItem(callMyParents,callMyParents);
// true
Meeting.isSameItem(callMyParents,interview);
// false
```

诚然,这个例子中有些地方是有点虚构的. 但老实说,我认为这一切 几乎都是合理合理的使用 各种`class`特性。

顺便说一句,可能还有一百万种不同的方式来构建上述代码逻辑. 我绝不是说这是正确或最好的方法 作为读者的练习,试试你的手,自己写出来,注意你所做的事情与我的方法不同.

[^POLP]:"最低特权原则",维基百科; 互联网档案馆的存檔,存档日期2013-03-04.https://en.wikipedia.org/wiki/Principle_of_least_privilege; 2022年7月访问
