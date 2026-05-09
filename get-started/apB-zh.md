# 你不认识JS然而:开始 - 第二版
# 附录B:练习,练习,练习!

在本附录中,我们将探讨一些练习及其建议的解决办法. 这些只是让你开始练习 从书中的概念。

## 练习比较

让我们实践一下价值类型和比较(第4章,支柱3),其中需要涉及胁迫。

`scheduleMeeting(..)`开始时间(24小时格式为字符串“hh:mm”)和会议时间(分钟数)。 应该会回来的`true`如果会议完全在工作日之内(根据工作日规定的时间)`dayStart`和`dayEnd`; 回转`false`如果会议违反工作日的规定。

```js
const dayStart = "07:30";
const dayEnd = "17:45";

function scheduleMeeting(startTime,durationMinutes) {
    // ..TODO..
}

scheduleMeeting("7:00",15);     // false
scheduleMeeting("07:15",30);    // false
scheduleMeeting("7:30",30);     // true
scheduleMeeting("11:30",60);    // true
scheduleMeeting("17:00",45);    // true
scheduleMeeting("17:30",30);    // false
scheduleMeeting("18:00",15);    // false
```

先自己想办法解决 考虑使用平等和关系比较操作人员,以及胁迫如何影响这一守则。 一旦您有有效的代码, * 将您的解决方案与本附录末尾的“ 推荐解决方案” 中的代码进行比较。

## 实践关闭

现在,让我们以结束来实践(第四章,支柱1)。

那个`range(..)`函数将数字作为其第一个参数,代表理想数字范围内的第一个数字。 第二个论点也是代表理想范围(包括)结束的数字。 如果省略了第二个参数,那么应当返回期望该参数的另一个函数。

```js
function range(start,end) {
    // ..TODO..
}

range(3,3);    // [3]
range(3,8);    // [3,4,5,6,7,8]
range(3,0);    // []

var start3 = range(3);
var start4 = range(4);

start3(3);     // [3]
start3(8);     // [3,4,5,6,7,8]
start3(0);     // []

start4(6);     // [4,5,6]
```

先自己想办法解决

一旦您有有效的代码, * 将您的解决方案与本附录末尾的“ 推荐解决方案” 中的代码进行比较。

## 练习原型

最后,让我们努力`this`和通过原型链接的对象(第四章,支柱2)。

定义一个有三卷可以单独操作的插槽机`spin()`,然后则`display()`所有折叠的当前内容。

单卷线的基本行为在`reel`下面的对象。 但插槽机需要个人的带状物体 授权给`reel`,且每个有`position`属性。

单曲只知道如何`display()`其当前槽符号,但一个槽机通常显示每卷三个符号: 当前槽(`position`),上方一个空档(`position - 1`),下面有一个插槽`position + 1`) (中文(简体) ). 因此显示槽机最终应显示一个3×3格的槽符号.

```js
function randMax(max) {
    return Math.trunc(1E9 * Math.random()) % max;
}

var reel = {
    symbols: [
        "♠", "♥", "♦", "♣", "☺", "★", "☾", "☀"
    ],
    spin() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        this.position = (
            this.position + 100 + randMax(100)
        ) % this.symbols.length;
    },
    display() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        return this.symbols[this.position];
    }
};

var slotMachine = {
    reels: [
        // this slot machine needs 3 separate reels
        // hint: Object.create(..)
    ],
    spin() {
        this.reels.forEach(function spinReel(reel){
            reel.spin();
        });
    },
    display() {
        // TODO
    }
};

slotMachine.spin();
slotMachine.display();
// ☾ | ☀ | ★
// ☀ | ♠ | ☾
// ♠ | ♥ | ☀

slotMachine.spin();
slotMachine.display();
// ♦ | ♠ | ♣
// ♣ | ♥ | ☺
// ☺ | ♦ | ★
```

先自己想办法解决

提示:

* 使用`%`用于包装的 Modulo 运算符`position`当你环绕着一卷卷环路 进入符号时

* 使用`Object.create(..)`创建对象并原型将其链接到另一个对象。 一旦链接, 授权允许对象共享`this`方法引用时的背景。

* 与其直接修改线条对象以显示三个位置中的每一个,不如使用另一个临时对象(`Object.create(..)`与它自己的`position`,代表来自。

一旦您有有效的代码, * 将您的解决方案与本附录末尾的“ 推荐解决方案” 中的代码进行比较。

## 建议的解决办法

记住,这些建议的解决办法只是:建议。 解决这些实践练习有很多不同的方法. 将你的方法与你在这里看到的相提并论,考虑每个方法的利弊.

“比较”(第3页)做法建议的解决办法:

```js
const dayStart = "07:30";
const dayEnd = "17:45";

function scheduleMeeting(startTime,durationMinutes) {
    var [ , meetingStartHour, meetingStartMinutes ] =
        startTime.match(/^(\d{1,2}):(\d{2})$/) || [];

    durationMinutes = Number(durationMinutes);

    if (
        typeof meetingStartHour == "string" &&
        typeof meetingStartMinutes == "string"
    ) {
        let durationHours =
            Math.floor(durationMinutes / 60);
        durationMinutes =
            durationMinutes - (durationHours * 60);
        let meetingEndHour =
            Number(meetingStartHour) + durationHours;
        let meetingEndMinutes =
            Number(meetingStartMinutes) +
            durationMinutes;

        if (meetingEndMinutes >= 60) {
            meetingEndHour = meetingEndHour + 1;
            meetingEndMinutes =
                meetingEndMinutes - 60;
        }

        // re-compose fully-qualified time strings
        // (to make comparison easier)
        let meetingStart = `${
            meetingStartHour.padStart(2,"0")
        }:${
            meetingStartMinutes.padStart(2,"0")
        }`;
        let meetingEnd = `${
            String(meetingEndHour).padStart(2,"0")
        }:${
            String(meetingEndMinutes).padStart(2,"0")
        }`;

        // NOTE: since expressions are all strings,
        // comparisons here are alphabetic, but it's
        // safe here since they're fully qualified
        // time strings (ie, "07:15" < "07:30")
        return (
            meetingStart >= dayStart &&
            meetingEnd <= dayEnd
        );
    }

    return false;
}

scheduleMeeting("7:00",15);     // false
scheduleMeeting("07:15",30);    // false
scheduleMeeting("7:30",30);     // true
scheduleMeeting("11:30",60);    // true
scheduleMeeting("17:00",45);    // true
scheduleMeeting("17:30",30);    // false
scheduleMeeting("18:00",15);    // false
```

----

建议的“关闭”(第1页)做法的解决办法:

```js
function range(start,end) {
    start = Number(start) || 0;

    if (end === undefined) {
        return function getEnd(end) {
            return getRange(start,end);
        };
    }
    else {
        end = Number(end) || 0;
        return getRange(start,end);
    }


    // **********************

    function getRange(start,end) {
        var ret = [];
        for (let i = start; i <= end; i++) {
            ret.push(i);
        }
        return ret;
    }
}

range(3,3);    // [3]
range(3,8);    // [3,4,5,6,7,8]
range(3,0);    // []

var start3 = range(3);
var start4 = range(4);

start3(3);     // [3]
start3(8);     // [3,4,5,6,7,8]
start3(0);     // []

start4(6);     // [4,5,6]
```

----

“原型”(Pillar 2)实践建议的解决办法:

```js
function randMax(max) {
    return Math.trunc(1E9 * Math.random()) % max;
}

var reel = {
    symbols: [
        "♠", "♥", "♦", "♣", "☺", "★", "☾", "☀"
    ],
    spin() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        this.position = (
            this.position + 100 + randMax(100)
        ) % this.symbols.length;
    },
    display() {
        if (this.position == null) {
            this.position = randMax(
                this.symbols.length - 1
            );
        }
        return this.symbols[this.position];
    }
};

var slotMachine = {
    reels: [
        Object.create(reel),
        Object.create(reel),
        Object.create(reel)
    ],
    spin() {
        this.reels.forEach(function spinReel(reel){
            reel.spin();
        });
    },
    display() {
        var lines = [];

        // display all 3 lines on the slot machine
        for (
            let linePos = -1; linePos <= 1; linePos++
        ) {
            let line = this.reels.map(
                function getSlot(reel){
                    var slot = Object.create(reel);
                    slot.position = (
                        reel.symbols.length +
                        reel.position +
                        linePos
                    ) % reel.symbols.length;
                    return slot.display();
                }
            );
            lines.push(line.join(" | "));
        }

        return lines.join("\n");
    }
};

slotMachine.spin();
slotMachine.display();
// ☾ | ☀ | ★
// ☀ | ♠ | ☾
// ♠ | ♥ | ☀

slotMachine.spin();
slotMachine.display();
// ♦ | ♠ | ♣
// ♣ | ♥ | ☺
// ☺ | ♦ | ★
```

即为此书. 但是现在是时候寻找真正的项目 来实践这些想法。 继续编码吧,因为这是最好的学习方法!
