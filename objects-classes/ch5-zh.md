# 你不了解JS然而:对象和类 - 第2版
# 第5章:代表团

| 注: |
| :--- |
| 进行中的工作 |

我们彻底探索了物体,原型,课程,现在`this`关键词 但我们现在要从不同的角度 重新审视我们迄今学到的东西。

如果你能利用所有 物体,原型,和动态的力量`this`机制,永远不用`class`还是它的后代?

事实上,我认为,联署材料本身就不那么注重阶级。`class`关键词可能会出现。 因为JS是一种动态的,原型的语言,它的强身之处其实是. *dlegation*.

## 序言

在我们开始审查代表团之前,我想提一句谨慎的话。 JS的目标`[[Prototype]]`和`this`函数上下文机制是 * 不* 主流。 框架作者和图书馆如何利用联署材料。 据我所知,你不会用这个模式 找到任何大的应用软件

那么,如果这种模式如此不受欢迎,我为什么要用一章来讨论它呢?

问得好 笑脸的回答是:因为这是我的书,我可以做我想做的事!

但更深层的答案是,因为我认为,发展“这个”对语言核心支柱之一的理解有助于你,即使你所做的只是使用`class`-就像JS一样

说清楚,代表团不是我的发明。 几十年来一直作为设计模式. 在很长一段时间里,开发者认为,原始的委托只是继承的动态形式。[^TreatyOfOrlando]但我认为把两者混为一谈是错误的。[^ClassVsPrototype]

为了本章的目的,我将介绍通过JS力学执行的代表团,作为一种替代设计模式,将定位在班级导向和对象封闭/模块模式之间。

第一步是拆除`class`机制的每个部分。 然后我们来挑樱桃 和混合的部分有点不同。

## 建筑师是什么?

在第三章中,我们看到`constructor(..)`作为建造一个`class`举个例子 不过`constructor(..)`不做任何 * 创造 * 工作, 它只是 * 初始化 * 工作。 换句话说,这个实例是在`constructor(..)`运行并初始化它 -- -- 例如,`this.whatever`任务类型。

创造的作品在哪里? 在那个`new`操作员。 正如第4章“援引新背景”一节所解释的,有四个步骤:`new`关键字执行;其中第一个是创建一个新的空对象(实例). 那个`constructor(..)`直到第3步才被援引`new`努力起来

不过`new`并不是唯一的,也许甚至最好的方法, 来创造一个物体,“内幕”。 考虑:

```js
// a non-class "constructor"
function Point2d(x,y) {
    // create an object (1)
    var instance = {};

    // initialize the instance (3)
    instance.x = x;
    instance.y = y;

    // return the instance (4)
    return instance;
}

var point = Point2d(3,4);

point.x;                    // 3
point.y;                    // 4
```

没有`class`,只是普通函数定义(`Point2d(..)`) (中文(简体) ). 没有`new`引用,只是常规函数调用( P)`Point2d(3,4)`) (中文(简体) ). 没有`this`参考文献,只是普通对象属性转让(`instance.x = ..`).

最常用来指代这种代码模式的术语是:`Point2d(..)`这里是一个“构件函数 ”。 引用它会导致一个物体的构造(创造和初始化),并返回到我们身上。 这是一种极为常见的模式,至少与面向阶级的代码一样常见.

我作说明`(1)`, `(3)`,以及`(4)`该片段大致相当于第1、第3和第4步`new`操作。 但第2步在哪里?

如果你记得,第二步`new`是指通过它的`[[Prototype]]`插槽(见第2章)。 所以,什么物体 我们想连接我们的`instance`反对? 我们可以把它连接到一个具有功能的物体上 我们想和我们的例子联系起来使用

让我们修正前一段:

```js
var prototypeObj = {
    toString() {
        return `(${this.x},${this.y})`;
    },
}

// a non-class "constructor"
function Point2d(x,y) {
    // create an object (1)
    var instance = {
        // link the instance's [[Prototype]] (2)
        __proto__: prototypeObj,
    };

    // initialize the instance (3)
    instance.x = x;
    instance.y = y;

    // return the instance (4)
    return instance;
}

var point = Point2d(3,4);

point.toString();           // (3,4)
```

现在你看到了`__proto__`内设任务`[[Prototype]]`,这是缺少步骤2。 我用过`__proto__`此处仅供参考;`setPrototypeOf(..)`如第4章所示,本可以完成同样的任务。

### *新*工厂实例

你觉得如果我们使用`new`援引`Point2d(..)`在此显示的函数?

```js
var anotherPoint = new Point2d(5,6);

anotherPoint.toString(5,6);         // (5,6)
```

单单 这是怎么回事? 一个普通的,非`class`引用的工厂函数`new`关键词,好像它是一个`class`这是否改变了代码的结果?

不... 还有是的`anotherPoint`这里的物体和如果我没有使用它完全一样`new`不过... 对象`new`创建、链接和指定为`this`语境? * 该物品被完全忽略和丢弃,最终成为联署材料收集的垃圾。 不幸的是,JS引擎无法预测你不会使用 你要求的对象`new`所以即使它没有被使用,它也总是会变质

没错! 使用一个`new`与工厂功能相反的关键词 可能感觉得更多或更熟悉, 但它相当浪费,因为它创造了**2**物体, 并浪费地扔掉其中之一。

### 工厂初始化

在当前代码示例中,`Point2d(..)`函数看起来还是很正常的`constructor(..)`页:1`class`定义。 但是,如果我们移动初始化代码 到一个单独的函数,说命名`init(..)`:

```js
var prototypeObj = {
    init(x,y) {
        // initialize the instance (3)
        this.x = x;
        this.y = y;
    },
    toString() {
        return `(${this.x},${this.y})`;
    },
}

// a non-class "constructor"
function Point2d(x,y) {
    // create an object (1)
    var instance = {
        // link the instance's [[Prototype]] (2)
        __proto__: prototypeObj,
    };

    // initialize the instance (3)
    instance.init(x,y);

    // return the instance (4)
    return instance;
}

var point = Point2d(3,4);

point.toString();           // (3,4)
```

那个`instance.init(..)`调用`[[Prototype]]`通过`__proto__`任务。 因此,它将原型链的上方`prototypeObj.init(..)`,并用`this`一、导 言`instance`转让(见第4章)。

继续拆解 准备开关!

```js
var Point2d = {
    init(x,y) {
        // initialize the instance (3)
        this.x = x;
        this.y = y;
    },
    toString() {
        return `(${this.x},${this.y})`;
    },
};
```

或 我放弃了`Point2d(..)`函数,并改名为`prototypeObj`作为`Point2d`有点奇怪

现在让我们看看其余的密码:

```js
// steps 1, 2, and 4
var point = { __proto__: Point2d, };

// step 3
point.init(3,4);

point.toString();           // (3,4)
```

最后一点改进:让我们利用JS提供的内在效用,叫做`Object.create(..)`:

```js
// steps 1, 2, and 4
var point = Object.create(Point2d);

// step 3
point.init(3,4);

point.toString();           // (3,4)
```

什么行动`Object.create(..)`表演吗?

1. 创建全新的空物,脱离薄空气.

2. 链接`[[Prototype]]`该函数的`.prototype`对象。

如果这些看起来很眼熟,那是因为那些 完全一样的前两步`new`关键词(见第4章)。

让我们现在重新整理一下:

```js
var Point2d = {
    init(x,y) {
        this.x = x;
        this.y = y;
    },
    toString() {
        return `(${this.x},${this.y})`;
    },
};

var point = Object.create(Point2d);

point.init(3,4);

point.toString();           // (3,4)
```

对 花点时间思考一下这里的起源 如何比较`class`方法吗?

此图案会丢弃`class`和`new`关键词,但结果完全相同。 *成本*吗? 单身`new`操作被拆分为两个语句:`Object.create(Point2d)`和`point.init(3,4)`.

#### 帮我重建!

如果把这两个手术分开会令你烦恼——是不是太破解了? - 他们总是可以重新组合在一个小工厂的助手:

```js
function make(objType,...args) {
    var instance = Object.create(objType);
    instance.init(...args);
    return instance;
}

var point = make(Point2d,3,4);

point.toString();           // (3,4)
```

| 提普: |
| :--- |
| 这样`make(..)`工厂函数辅助器一般为任何对象类型工作,只要您遵循隐含的惯例,即每个对象`objType`您链接到一个名为`init(..)`交给我 |

当然,你还是可以创造出你想创造的很多例子:

```js
var point = make(Point2d,3,4);

var anotherPoint = make(Point2d,5,6);
```

## 跳槽类思考

坦率地说,我们刚刚经历的“解构” 只会以稍有不同的方式结束, 可能更好或更差一点, 比起`class`样式。 如果代表团就是这个意思的话, 它可能连一个脚注都不够用, 更不用说整章了.

但这里我们真正要开始推进 面向阶级的思维本身, 而不仅仅是语法, 仅此而已。

面向阶级的设计在本质上创造了一个分级的*分类*,意味着我们如何划分和分组特征,然后把它们垂直地堆放在继承链中. 此外,界定子类是通用基类的专业化. 证明性是普适类的一个专业.

传统阶级等级体系中的行为是通过继承链层的垂直构成. 几十年来,人们曾试图平息继承的深层等级制度,通过*混合* 和相关的想法,支持更加横向的构成。

我不是在断言这些接近密码的方法有什么问题。 但我是说,他们不是 "自然的" JS如何工作的, 所以在 JS 中采用他们 是一个漫长,风化,复杂的道路, 并且已经不同程度地 吸收了很多细微的语法 在 JS 的核心之上改装`[[Prototype]]`和`this`图标。

对于本章的其余部分,我打算放弃两种语法:`class`*和* 阶级的思想*

## 代表团

那么,代表团是什么? 其核心是大约两个或两个以上*东西* 分享完成一个任务的努力.

而不是定义`Point2d`一般父母 * 代表一个或一个以上孩子共同行为的东西`point` / `anotherPoint`* 继承的事物 * 代表团让我们用彼此合作的离散同伴 * 来建立我们的方案。

我会用一些代码来描述

```js
var Coordinates = {
    setX(x) {
        this.x = x;
    },
    setY(y) {
        this.y = y;
    },
    setXY(x,y) {
        this.setX(x);
        this.setY(y);
    },
};

var Inspect = {
    toString() {
        return `(${this.x},${this.y})`;
    },
};

var point = {};

Coordinates.setXY.call(point,3,4);
Inspect.toString.call(point);         // (3,4)

var anotherPoint = Object.create(Coordinates);

anotherPoint.setXY(5,6);
Inspect.toString.call(anotherPoint);  // (5,6)
```

分解此经.

我定义了`Coordinates`作为持有我与设定点坐标相关的某些行为的具体对象(`x`和`y`) (中文(简体) ). 我也已经定义了`Inspect`作为具有一些调试检查逻辑的具体对象,例如`toString()`.

然后再创造两个混凝土物体`point`和`anotherPoint`.

`point`没有具体的`[[Prototype]]`(默认:`Object.prototype`我援引第4章,使用`解释性上下文*转让 '。`Coordinates.setXY(..)`和`Inspect.toString()`公用事业`point`我称之为 " 明确代表团 "。

`anotherPoint`这是`[[Prototype]]`链接到`Coordinates`大多是为了方便 让我使用 " 隐性上下文 " 的任务`anotherPoint.setXY(..)`但我还是可以...`anotherPoint`上下文`Inspect.toString()`打电话 这就是我所谓的 "隐性代表团"。

*别错过* *这个* 我们仍然完成了组成: 我们创造了行为从`Coordinates`和`Inspect`,在运行时函数中引用`this`上下文共享。 我们不必把那些行为 编成一个`class`(或基准子类)`class`等级)用于`point` / `anotherPoint`用于继承。 我想称之为这种运行时间构成,**虚拟构成**。

这里的*点*是:这四个对象中没有一个是父母或孩子. 他们都是彼此的同伴,都有不同的目的. 我们可以用逻辑块(在每个对象上)来组织我们的行为,并通过`this`(可选)`[[Prototype]]`和书中我们研究过的其他模式一样

* 正如联署材料所体现的那样,这是**权力下放**模式的核心。

| 提普: |
| :--- |
| 在这本书系列的第一版中,这本书("This & Object Prototypes")发明了一个术语"OLOO",它代表着"Objects Linked to Other Object"——与"OO"("Object Oriented")相对立. 在前面的例子中,你可以看到OLOO的本质:我们拥有的只是物体,与其它物体有联系和合作. 我觉得这太简单了 |

## 配置对等对象

让我们把* 这个代表团* 更进一步。

在前面的片断中,`point`和`anotherPoint`仅仅掌握了数据,他们授权的行为发生在其他物体上(`Coordinates`和`Inspect`) (中文(简体) ). 但是我们可以直接将行为添加到一个授权链中的任何物体上,而那些行为甚至可以互相影响,全部通过"虚拟成分的魔法"(Virtual communication *)来进行(Virtual communications).`this`上下文共享)。

To illustrate, we'll evolve our current *point* example a fair bit. And as a bonus we'll actually draw our points on a `<canvas>` element in the DOM. Let's take a look:

```js
var Canvas = {
    setOrigin(x,y) {
        this.ctx.translate(x,y);

        // flip the canvas context vertically,
        // so coordinates work like on a normal
        // 2d (x,y) graph
        this.ctx.scale(1,-1);
    },
    pixel(x,y) {
        this.ctx.fillRect(x,y,1,1);
    },
    renderScene() {
        // clear the canvas
        var matrix = this.ctx.getTransform();
        this.ctx.resetTransform();
        this.ctx.clearRect(
            0, 0,
            this.ctx.canvas.width,
            this.ctx.canvas.height
        );
        this.ctx.setTransform(matrix);

        this.draw();  // <-- where is draw()?
    },
};

var Coordinates = {
    setX(x) {
        this.x = Math.round(x);
    },
    setY(y) {
        this.y = Math.round(y);
    },
    setXY(x,y) {
        this.setX(x);
        this.setY(y);
        this.render();   // <-- where is render()?
    },
};

var ControlPoint = {
    // delegate to Coordinates
    __proto__: Coordinates,

    // NOTE: must have a <canvas id="my-canvas">
    // element in the DOM
    ctx: document.getElementById("my-canvas")
        .getContext("2d"),

    rotate(angleRadians) {
        var rotatedX = this.x * Math.cos(angleRadians) -
            this.y * Math.sin(angleRadians);
        var rotatedY = this.x * Math.sin(angleRadians) +
            this.y * Math.cos(angleRadians);
        this.setXY(rotatedX,rotatedY);
    },
    draw() {
        // plot the point
        Canvas.pixel.call(this,this.x,this.y);
    },
    render() {
        // clear the canvas, and re-render
        // our control-point
        Canvas.renderScene.call(this);
    },
};

// set the logical (0,0) origin at this
// physical location on the canvas
Canvas.setOrigin.call(ControlPoint,100,100);

ControlPoint.setXY(30,40);
// [renders point (30,40) on the canvas]

// ..
// later:

// rotate the point about the (0,0) origin
// 90 degrees counter-clockwise
ControlPoint.rotate(Math.PI / 2);
// [renders point (-40,30) on the canvas]
```

好吧,这是很多代码 消化。 慢慢来,再读几段 我添加了几件新的混凝土物体`Canvas`和`ControlPoint`) 与前一个`Coordinates`对象。

确保你看到并了解这三个混凝土物体之间的相互作用。

`ControlPoint`链接(通过`__proto__`* 间接委托 * (中文(简体) ).`[[Prototype]]`链)至`Coordinates`.

这是"明确代表团":`Canvas.setOrigin.call(ControlPoint,100,100);`-我正援引`Canvas.setOrigin(..)`调用`ControlPoint`。这将产生共享的效果`ctx`与`setOrigin(..)`,通过`this`.

`ControlPoint.setXY(..)`*隐含地*代表`Coordinates.setXY(..)`,但仍在`ControlPoint`。这里有一个关键的细节,很容易错过:`this.render()`内部`Coordinates.setXY(..)`从哪里来的? 自从`this`上下文为`ControlPoint`(无)`Coordinates`),它被引用为`ControlPoint.render()`.

`ControlPoint.render()`* 代表*`Canvas.renderScene()`,再次还在`ControlPoint`上下文。`renderScene()`电话`this.draw()`但这是从哪里来的? 是的,还是从`ControlPoint`(通过)`this`页:1

还有`ControlPoint.draw()`* 明确代表*`Canvas.pixel(..)`,再次在`ControlPoint`上下文。

这三个物体都有最终互相触发的方法. 但是这些电话并不特别硬化。`Canvas.renderScene()`不打电话`ControlPoint.draw()`,它召唤`this.draw()`这很重要,因为这意味着`Canvas.renderScene()`更灵活地用于不同的`this`上下文 -- -- 例如,针对其他类型 * 点 * 对象之外`ControlPoint`.

它穿过了`this`一、导 言`[[Prototype]]`链,这三个物体基本上几乎是混合的(组合的),按照每个步骤的需要,这样它们就可以一起工作**,就像它们是一个物体而不是三个分离物体**.

这就是代表团在联署材料中实现的 虚拟构成的美丽

### 灵活的背景

我在上面提到,我们可以很容易地把其他具体物体添加到混凝土中。 以下是一个例子:

```js
var Coordinates = { /* .. */ };

var Canvas = {
    /* .. */
    line(start,end) {
        this.ctx.beginPath();
        this.ctx.moveTo(start.x,start.y);
        this.ctx.lineTo(end.x,end.y);
        this.ctx.stroke();
    },
};

function lineAnchor(x,y) {
    var anchor = {
        __proto__: Coordinates,
        render() {},
    };
    anchor.setXY(x,y);
    return anchor;
}

var GuideLine = {
    // NOTE: must have a <canvas id="my-canvas">
    // element in the DOM
    ctx: document.getElementById("my-canvas")
        .getContext("2d"),

    setAnchors(sx,sy,ex,ey) {
        this.start = lineAnchor(sx,sy);
        this.end = lineAnchor(ex,ey);
        this.render();
    },
    draw() {
        // plot the point
        Canvas.line.call(this,this.start,this.end);
    },
    render() {
        // clear the canvas, and re-render
        // our line
        Canvas.renderScene.call(this);
    },
};

// set the logical (0,0) origin at this
// physical location on the canvas
Canvas.setOrigin.call(GuideLine,100,100);

GuideLine.setAnchors(-30,65,45,-17);
// [renders line from (-30,65) to (45,-17)
//   on the canvas]
```

不错,我想!

但我认为另一个不那么明显的好处是 物体通过动态连接`this`上下文往往使测试程序的不同部分独立,有些容易。

举例来说,`Object.setPrototypeOf(..)`可用于动态更改`[[Prototype]]`链接对象,将其授权给模拟对象等不同的对象。 或者你可以动态地重新定义`GuideLine.draw()`和`GuideLine.render()`* 明确委派* 至`MockCanvas`改为`Canvas`.

那个`this`关键词和`[[Prototype]]`链接,是一个非常灵活的机制, 当你理解和充分利用它们。

## 为什么?

好,所以希望可以清楚地看到,代表团的格局主要依靠隐含的投入,通过下列方式分享背景:`this`而不是通过一个明确的参数。

你也许应该问一下,为什么不总是明确地绕过这个背景呢? 我们当然可以这样做,但是... 为了手动传递 必要的上下文, 我们将不得不改变 几乎每一个函数签名, 和任何相应的呼叫网站。

让我们重新审视一下之前`ControlPoint`以代表团为例,在不以代表团为主的情况下予以执行`this`上下文共享。 认真注意这些差异:

```js
var Canvas = {
    setOrigin(ctx,x,y) {
        ctx.translate(x,y);
        ctx.scale(1,-1);
    },
    pixel(ctx,x,y) {
        ctx.fillRect(x,y,1,1);
    },
    renderScene(ctx,entity) {
        // clear the canvas
        var matrix = ctx.getTransform();
        ctx.resetTransform();
        ctx.clearRect(
            0, 0,
            ctx.canvas.width,
            ctx.canvas.height
        );
        ctx.setTransform(matrix);

        entity.draw();
    },
};

var Coordinates = {
    setX(entity,x) {
        entity.x = Math.round(x);
    },
    setY(entity,y) {
        entity.y = Math.round(y);
    },
    setXY(entity,x,y) {
        this.setX(entity,x);
        this.setY(entity,y);
        entity.render();
    },
};

var ControlPoint = {
    // NOTE: must have a <canvas id="my-canvas">
    // element in the DOM
    ctx: document.getElementById("my-canvas")
        .getContext("2d"),

    setXY(x,y) {
        Coordinates.setXY(this,x,y);
    },
    rotate(angleRadians) {
        var rotatedX = this.x * Math.cos(angleRadians) -
            this.y * Math.sin(angleRadians);
        var rotatedY = this.x * Math.sin(angleRadians) +
            this.y * Math.cos(angleRadians);
        this.setXY(rotatedX,rotatedY);
    },
    draw() {
        // plot the point
        Canvas.pixel(this.ctx,this.x,this.y);
    },
    render() {
        // clear the canvas, and re-render
        // our control-point
        Canvas.renderScene(this.ctx,this);
    },
};

// set the logical (0,0) origin at this
// physical location on the canvas
Canvas.setOrigin(ControlPoint.ctx,100,100);

// ..
```

老实说,你们有些人可能更喜欢这种代码风格. 如果你在营地,那也没关系 这个片段可以避免`[[Prototype]]`完全,而只依赖 更少的基本`this.`- 风格引用属性和方法。

与此相对照,我在本章中提倡的代表团风格是不熟悉的,也是有用的.`[[Prototype]]`和`this`分享的方式 你不太可能熟悉。 要有效利用这种风格,就必须投入时间和实践,建立更深入的熟悉感.

但在我看来,通过授权避免虚拟构成的“成本”在所有功能签名和呼叫站点中都可以感觉到;我发现它们更复杂。 这一明确背景的通过是相当值得的。

事实上,我绝对不会提倡这种守则风格。 如果你想避开代表团,最好还是坚持`class`样式代码,见第三章。 作为留给读者的一项练习,尝试转换早期的`ControlPoint` / `GuideLine`要使用的代码片段`class`.

[^TreatyOfOrlando]:"奥兰多条约";亨利·利伯曼,林恩·安德烈亚·斯坦因,大卫·恩加尔;1987年10月6日;https://web.media.mit.edu/~lieber/Publications/Treaty-of-Orlando-Treaty-Text.pdf; PDF; 2022年7月访问

[^ClassVsPrototype]:"Classes vs. prototypes,一些哲学和历史观察"; Antero Taivalsaari; Apr 22, 1996;.https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.56.4713&rep=rep1&type=pdf; PDF; 2022年7月访问
