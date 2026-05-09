# 你不了解JS然而:范围与关闭 - 第二版
# 附录B:实践

本附录旨在给你一些具有挑战性和有趣的练习,以测试和巩固你对本书主要议题的理解. 在实际的代码编辑器中, 别作弊!

这些练习没有你必须准确得到的具体正确答案. 你的方法可能与所提出的解决方案有些(或很多)不同,这没关系。

没有判断你如何写代码。 我希望你们能从这本书中走出来, 相信你们能够解决这些建立在牢固的知识基础上的编码任务。 这是唯一的目标,在这里。 如果你对密码满意,我也是!

## 大理石块

记得第2章的图2吗?

<figure>
    <img src="images/fig2.png" width="300" alt="Colored Scope Bubbles" align="center">
    <figcaption><em>Fig. 2 (Ch. 2): Colored Scope Bubbles</em></figcaption>
    <br><br>
</figure>

此练习要求您写一个包含嵌套函数和块范围的程序, 满足这些限制:

* 如果将所有瞄准镜(包括全局瞄准镜!)涂上不同的颜色,则至少需要6种颜色. 确保添加一个代码注释,以颜色标注每个范围.

    BONUS: identify any implied scopes your code may have.

* 每个范围至少有一个标识符。

* 包含至少两个函数范围以及至少两个块范围。

* 至少有一个外部范围的变量必须被一个嵌套范围变量所阴影(见第3章)。

* 至少有一种可变的提法必须解决在范围链上至少高出两个层次的可变申报问题。

| 提普: |
| :--- |
| 你只要为这个练习写些破烂的foo/bar/baz类型的代码, 但我建议你试着想出一些非部落真实的代码, 至少做一些合理的事情。 |

自行试练,然后在本附录结尾处查看建议的解决办法.

## 关闭(PART 1)

让我们先用一些常见的计算机磁盘操作来实践关闭:确定一个值是否为质(除了1和本身之外没有偏差),并生成一个给定数的质因子(divisors)列表.

例如:

```js
isPrime(11);        // true
isPrime(12);        // false

factorize(11);      // [ 11 ]
factorize(12);      // [ 3, 2, 2 ] --> 3*2*2=12
```

下面是执行`isPrime(..)`,改编自 Math.js 库:[^MathJSisPrime]

```js
function isPrime(v) {
    if (v <= 3) {
        return v > 1;
    }
    if (v % 2 == 0 || v % 3 == 0) {
        return false;
    }
    var vSqrt = Math.sqrt(v);
    for (let i = 5; i <= vSqrt; i += 6) {
        if (v % i == 0 || v % (i + 2) == 0) {
            return false;
        }
    }
    return true;
}
```

这里有一个基本的执行`factorize(..)`(不与`factorial(..)`从第6章起:

```js
function factorize(v) {
    if (!isPrime(v)) {
        let i = Math.floor(Math.sqrt(v));
        while (v % i != 0) {
            i--;
        }
        return [
            ...factorize(i),
            ...factorize(v / i)
        ];
    }
    return [v];
}
```

| 注: |
| :--- |
| 我称之为基本,因为它不是为性能而优化的. 它具有二进制-折叠性(并非尾调可选择性),并产生大量中间数组拷贝. 它也不以任何方式命令发现的因素. 有许多其他的算法来完成这项任务, 但我想用一些简短的, 大致可以理解的东西来进行我们的练习。 |

如果你打电话`isPrime(4327)`在一个程序里,你可以看到它每次会经历所有数十个比较/计算步骤. 如果你考虑`factorize(..)`它在召唤`isPrime(..)`许多次,因为它计算出各种因素的清单。 很有可能大多数电话都是重复的 浪费了好多工作!

这项工作的第一部分是使用关闭来实施缓存,以记住:`isPrime(..)`,使原始(`true`或`false`(a) 特定数字中只有一次计算。 提示:我们已经在第六章中显示过这种缓冲`factorial(..)`.

如果你看着`factorize(..)`,它被重复执行, 意思是它反复自称。 这再次意味着,我们可能看到许多浪费的电话,用来计算同样数字的主要因素。 因此,该练习的第二部分是使用相同的关闭缓存技术用于`factorize(..)`.

对缓存使用单独的关闭`isPrime(..)`和`factorize(..)`而不是把它们放在一个单一的范围内。

自行试练,然后在本附录结尾处查看建议的解决办法.

### 关于记忆的文字

我想和大家分享一下 这个关闭缓存技术 以及它对你应用程序性能的影响

我们可以看到,在省下重复的电话时,我们提高了计算速度(在某些情况下,增加了惊人的数量)。 但是,这种关闭的用法是一种明确的权衡,你应该非常清楚。

取舍是记. 我们基本上正在无限制地增加我们的缓存。 如果这些功能被称作数百万次, 并且大多是独特的输入, 我们将会被嚼掉很多记忆。 这绝对是值得花费的,但只有我们觉得我们有可能看到共同投入的重复,这样我们才能利用缓存.

如果大多数电话都会有一个独特的输入,而缓存本质上从不为任何好处使用*,那么这个技术是不合适的.

使用更精密的缓存方法,例如LRU(最近使用最少的缓存)来限制其大小,也可能是一个好主意;当它跑到极限时,LRU会驱逐那些被...

这里的缺点是LRU本身相当非部落性. 你会想使用高度优化的 LRU 的执行, 并敏锐地意识到 所有在游戏中的权衡。

## 关闭(PART 2)

在这个练习中,我们将再次通过定义一个`toggle(..)`用于给我们一个值切换器。

您将把一个或多个值(作为参数)传递到`toggle(..)`,然后恢复功能。 返回的函数将按顺序在所有通过值之间交替/旋转,一次一个,就像它被反复称为.

```js
function toggle(/* .. */) {
    // ..
}

var hello = toggle("hello");
var onOff = toggle("on","off");
var speed = toggle("slow","medium","fast");

hello();      // "hello"
hello();      // "hello"

onOff();      // "on"
onOff();      // "off"
onOff();      // "on"

speed();      // "slow"
speed();      // "medium"
speed();      // "fast"
speed();      // "slow"
```

转瞬即逝`toggle(..)`不太重要;这样的切换实例总是会回来的`undefined`.

自行试练,然后在本附录结尾处查看建议的解决办法.

## 关闭(PART 3)

在关于关闭的第三次也是最后一次演习中,我们要实施一个基本的计算器. 那个`calculator()`函数将生成一个以函数形式保持自身状态的计算器实例(`calc(..)`页:1

```js
function calculator() {
    // ..
}

var calc = calculator();
```

每次都这样`calc(..)`你会在一个代表计算器按钮按键的字符中通过。 为了让事情更加直截了当,我们将限制我们的计算器只支持输入数字(0-9),算术操作(+,-,,/),以及"="来计算操作. 操作严格按照输入的顺序进行处理;没有"()"分组或操作者优先.

我们不支持进入小数点,但分裂操作会导致它们. 我们不支持输入负数,但"-"操作会导致负数. 因此,你应该能够先输入一个操作来计算,从而产生任何负数或小数。 然后可以继续用这个值进行计算.

回归`calc(..)`电话应该模仿真实的计算器上显示的东西,比如反映刚刚按下的东西,或者在按"="时计算总数.

例如:

```js
calc("4");     // 4
calc("+");     // +
calc("7");     // 7
calc("3");     // 3
calc("-");     // -
calc("2");     // 2
calc("=");     // 75
calc("*");     // *
calc("4");     // 4
calc("=");     // 300
calc("5");     // 5
calc("-");     // -
calc("5");     // 5
calc("=");     // 0
```

由于这种用法有点笨拙,这里有一个`useCalc(..)`帮助器,从字符串中以一个字符一次运行计算器,并每次计算显示:

```js
function useCalc(calc,keys) {
    return [...keys].reduce(
        function showDisplay(display,key){
            var ret = String( calc(key) );
            return (
                display +
                (
                  (ret != "" && key == "=") ?
                      "=" :
                      ""
                ) +
                ret
            );
        },
        ""
    );
}

useCalc(calc,"4+3=");           // 4+3=7
useCalc(calc,"+9=");            // +9=16
useCalc(calc,"*8=");            // *5=128
useCalc(calc,"7*2*3=");         // 7*2*3=42
useCalc(calc,"1/0=");           // 1/0=ERR
useCalc(calc,"+3=");            // +3=ERR
useCalc(calc,"51=");            // 51
```

这最明智的用法`useCalc(..)`帮助者是总是有"="作为最后一个输入的字符.

计算器显示的总数的一些格式化需要特殊处理. 我提供这个`formatTotal(..)`函数,计算器在返回当前计算的总和时(在`"="`输入:

```js
function formatTotal(display) {
    if (Number.isFinite(display)) {
        // constrain display to max 11 chars
        let maxDigits = 11;
        // reserve space for "e+" notation?
        if (Math.abs(display) > 99999999999) {
            maxDigits -= 6;
        }
        // reserve space for "-"?
        if (display < 0) {
            maxDigits--;
        }

        // whole number?
        if (Number.isInteger(display)) {
            display = display
                .toPrecision(maxDigits)
                .replace(/\.0+$/,"");
        }
        // decimal
        else {
            // reserve space for "."
            maxDigits--;
            // reserve space for leading "0"?
            if (
                Math.abs(display) >= 0 &&
                Math.abs(display) < 1
            ) {
                maxDigits--;
            }
            display = display
                .toPrecision(maxDigits)
                .replace(/0+$/,"");
        }
    }
    else {
        display = "ERR";
    }
    return display;
}
```

不要太担心`formatTotal(..)`工作时 其逻辑大多是一组处理,将计算器显示限制在11个字符最大,即使负数,重复十进制,甚至"e+"指数表示需要.

再说一遍,不要在计算器特定行为周围的泥浆中沉浸。 集中关注关闭的*记忆*.

自行试练,然后在本附录结尾处查看建议的解决办法.

## 模块

这项工作是将计算器从关闭(PART 3)转换为模块.

我们不会在计算器中添加任何额外的功能,只是改变它的界面. 而不是调用一个函数`calc(..)`我们会在公共API上为计算器的每个"键压"调用特定的方法. 产出保持不变。

这个模块应该表示为经典的模块厂函数,称为`calculator()`,而不是一个单顿IIFE,这样,如果需要,可以创建多个计算器.

公共API应包括以下方法:

* `number(..)`(输入:字符/数字"压").
* `plus()`
* `minus()`
* `mult()`
* `div()`
* `eq()`

用法看起来像:

```js
var calc = calculator();

calc.number("4");     // 4
calc.plus();          // +
calc.number("7");     // 7
calc.number("3");     // 3
calc.minus();         // -
calc.number("2");     // 2
calc.eq();            // 75
```

`formatTotal(..)`与以前的工作相同。 不过`useCalc(..)`需要调整帮助以配合模块 API:

```js
function useCalc(calc,keys) {
    var keyMappings = {
        "+": "plus",
        "-": "minus",
        "*": "mult",
        "/": "div",
        "=": "eq"
    };

    return [...keys].reduce(
        function showDisplay(display,key){
            var fn = keyMappings[key] || "number";
            var ret = String( calc[fn](key) );
            return (
                display +
                (
                  (ret != "" && key == "=") ?
                      "=" :
                      ""
                ) +
                ret
            );
        },
        ""
    );
}

useCalc(calc,"4+3=");           // 4+3=7
useCalc(calc,"+9=");            // +9=16
useCalc(calc,"*8=");            // *5=128
useCalc(calc,"7*2*3=");         // 7*2*3=42
useCalc(calc,"1/0=");           // 1/0=ERR
useCalc(calc,"+3=");            // +3=ERR
useCalc(calc,"51=");            // 51
```

自行试练,然后在本附录结尾处查看建议的解决办法.

在你进行这项工作时,也花费一些时间来考虑将计算器作为模块来表示的利弊,而不是前一次工作的关闭功能方法.

写几句解释你的想法

BONUS #2:尝试将您的模块转换为其他模块格式,包括:UMD, CommunityJS,和无害环境管理(ES模块).

## 建议的解决办法

希望你在读到这为止之前 已经试过练习了 别作弊!

记住,每一个建议的解决办法只是处理问题的不同方法之一。 它们并不是"正确的答案",但它们确实说明了一种合理的方法来对待每一个练习.

从阅读这些推荐的解决方案可以得到的最重要的好处是将它们与您的代码进行比较,并分析我们为什么各自做出相似或不同的选择. 不要陷入太多的摩托化;尽量保持专注于主体而不是小细节.

### 建议:大理石块

*大理石运动* 盒可以这样解决:

```js
// RED(1)
const howMany = 100;

// Sieve of Eratosthenes
function findPrimes(howMany) {
    // BLUE(2)
    var sieve = Array(howMany).fill(true);
    var max = Math.sqrt(howMany);

    for (let i = 2; i < max; i++) {
        // GREEN(3)
        if (sieve[i]) {
            // ORANGE(4)
            let j = Math.pow(i,2);
            for (let k = j; k < howMany; k += i) {
                // PURPLE(5)
                sieve[k] = false;
            }
        }
    }

    return sieve
        .map(function getPrime(flag,prime){
            // PINK(6)
            if (flag) return prime;
            return flag;
        })
        .filter(function onlyPrimes(v){
            // YELLOW(7)
            return !!v;
        })
        .slice(1);
}

findPrimes(howMany);
// [
//    2, 3, 5, 7, 11, 13, 17,
//    19, 23, 29, 31, 37, 41,
//    43, 47, 53, 59, 61, 67,
//    71, 73, 79, 83, 89, 97
// ]
```

### 建议:结束(PART 1)

*关闭练习(PART 1)*`isPrime(..)`和`factorize(..)`,可以这样解决:

```js
var isPrime = (function isPrime(v){
    var primes = {};

    return function isPrime(v) {
        if (v in primes) {
            return primes[v];
        }
        if (v <= 3) {
            return (primes[v] = v > 1);
        }
        if (v % 2 == 0 || v % 3 == 0) {
            return (primes[v] = false);
        }
        let vSqrt = Math.sqrt(v);
        for (let i = 5; i <= vSqrt; i += 6) {
            if (v % i == 0 || v % (i + 2) == 0) {
                return (primes[v] = false);
            }
        }
        return (primes[v] = true);
    };
})();

var factorize = (function factorize(v){
    var factors = {};

    return function findFactors(v) {
        if (v in factors) {
            return factors[v];
        }
        if (!isPrime(v)) {
            let i = Math.floor(Math.sqrt(v));
            while (v % i != 0) {
                i--;
            }
            return (factors[v] = [
                ...findFactors(i),
                ...findFactors(v / i)
            ]);
        }
        return (factors[v] = [v]);
    };
})();
```

我用于每个工具的一般步骤:

1. 环绕一个IIFE,以定义缓存变量的内存范围。

2. 在基础调用中,首先检查缓存,如果结果已经知道,请返回.

3. 在每一个地方`return`原本是,分配到缓存 并只是返回 分配操作的结果 - 这是一个空间节省技巧 主要是在书中简洁。

我还把内部功能从`factorize(..)`改为:`findFactors(..)`。这在技术上是没有必要的,但它有助于更清楚地说明哪些功能是重复式的。

### 建议:结束(PART2)

*关闭演习(PART 2)*`toggle(..)`可以这样解决:

```js
function toggle(...vals) {
    var unset = {};
    var cur = unset;

    return function next(){
        // save previous value back at
        // the end of the list
        if (cur != unset) {
            vals.push(cur);
        }
        cur = vals.shift();
        return cur;
    };
}

var hello = toggle("hello");
var onOff = toggle("on","off");
var speed = toggle("slow","medium","fast");

hello();      // "hello"
hello();      // "hello"

onOff();      // "on"
onOff();      // "off"
onOff();      // "on"

speed();      // "slow"
speed();      // "medium"
speed();      // "fast"
speed();      // "slow"
```

### 建议:结束(PART 3)

*关闭练习(PART 3)*`calculator()`可以这样解决:

```js
// from earlier:
//
// function useCalc(..) { .. }
// function formatTotal(..) { .. }

function calculator() {
    var currentTotal = 0;
    var currentVal = "";
    var currentOper = "=";

    return pressKey;

    // ********************

    function pressKey(key){
        // number key?
        if (/\d/.test(key)) {
            currentVal += key;
            return key;
        }
        // operator key?
        else if (/[+*/-]/.test(key)) {
            // multiple operations in a series?
            if (
                currentOper != "=" &&
                currentVal != ""
            ) {
                // implied '=' keypress
                pressKey("=");
            }
            else if (currentVal != "") {
                currentTotal = Number(currentVal);
            }
            currentOper = key;
            currentVal = "";
            return key;
        }
        // = key?
        else if (
            key == "=" &&
            currentOper != "="
        ) {
            currentTotal = op(
                currentTotal,
                currentOper,
                Number(currentVal)
            );
            currentOper = "=";
            currentVal = "";
            return formatTotal(currentTotal);
        }
        return "";
    };

    function op(val1,oper,val2) {
        var ops = {
            // NOTE: using arrow functions
            // only for brevity in the book
            "+": (v1,v2) => v1 + v2,
            "-": (v1,v2) => v1 - v2,
            "*": (v1,v2) => v1 * v2,
            "/": (v1,v2) => v1 / v2
        };
        return ops[oper](val1,val2);
    }
}

var calc = calculator();

useCalc(calc,"4+3=");           // 4+3=7
useCalc(calc,"+9=");            // +9=16
useCalc(calc,"*8=");            // *5=128
useCalc(calc,"7*2*3=");         // 7*2*3=42
useCalc(calc,"1/0=");           // 1/0=ERR
useCalc(calc,"+3=");            // +3=ERR
useCalc(calc,"51=");            // 51
```

| 注: |
| :--- |
| 记住:这个练习是关于关闭的. 不要太注重计算器的实际力学,而是要注意你是否正确*记住*计算器状态跨函数调用. |

### 建议:单元

*机动演习*`calculator()`可以这样解决:

```js
// from earlier:
//
// function useCalc(..) { .. }
// function formatTotal(..) { .. }

function calculator() {
    var currentTotal = 0;
    var currentVal = "";
    var currentOper = "=";

    var publicAPI = {
        number,
        eq,
        plus() { return operator("+"); },
        minus() { return operator("-"); },
        mult() { return operator("*"); },
        div() { return operator("/"); }
    };

    return publicAPI;

    // ********************

    function number(key) {
        // number key?
        if (/\d/.test(key)) {
            currentVal += key;
            return key;
        }
    }

    function eq() {
        // = key?
        if (currentOper != "=") {
            currentTotal = op(
                currentTotal,
                currentOper,
                Number(currentVal)
            );
            currentOper = "=";
            currentVal = "";
            return formatTotal(currentTotal);
        }
        return "";
    }

    function operator(key) {
        // multiple operations in a series?
        if (
            currentOper != "=" &&
            currentVal != ""
        ) {
            // implied '=' keypress
            eq();
        }
        else if (currentVal != "") {
            currentTotal = Number(currentVal);
        }
        currentOper = key;
        currentVal = "";
        return key;
    }

    function op(val1,oper,val2) {
        var ops = {
            // NOTE: using arrow functions
            // only for brevity in the book
            "+": (v1,v2) => v1 + v2,
            "-": (v1,v2) => v1 - v2,
            "*": (v1,v2) => v1 * v2,
            "/": (v1,v2) => v1 / v2
        };
        return ops[oper](val1,val2);
    }
}

var calc = calculator();

useCalc(calc,"4+3=");           // 4+3=7
useCalc(calc,"+9=");            // +9=16
useCalc(calc,"*8=");            // *5=128
useCalc(calc,"7*2*3=");         // 7*2*3=42
useCalc(calc,"1/0=");           // 1/0=ERR
useCalc(calc,"+3=");            // +3=ERR
useCalc(calc,"51=");            // 51
```

这就是这本书,恭喜你的成就! 准备好后,继续看第三册, *对象和阶级 *.

[^MathJSisPrime]: *Math.js:为Prime(.) *,https://github.com/josdejong/mathjs/blob/develop/src/function/utils/isPrime.js,3 March 2020 (英语).
