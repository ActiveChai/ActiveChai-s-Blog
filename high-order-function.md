---
title: 学习几个函数的实现
date: 2019-10-17 23:08:06
tags: [函数, 原理]
categories:
- JavaScript
---

学习如何实现简单的防抖、节流、分时、New、bind、Ajax函数。<!-- more -->

# 防抖函数

连续触发事件，将被执行的函数延迟 n 秒执行。如果在 n 秒内又触发了事件，则会重新计算函数执行时间，即重置计时器。

```JavaScript
const debounce = function(fn, n) {
    let timer;

    return function() {
        const __this = this;
        const args = arguments;
        if(timer) {
            clearTimeout(timer);
            timer = null;
        }
        timer = setTimeout(function() {
            fn.apply(__this, args);
        }, n)
    }
}
```

# 节流函数

连续触发事件，将被执行的函数延迟 n 秒执行，如果在 n 秒内又触发了事件，则忽略。

```JavaScript
const throttle = function(fn, n) {
    let timer;

    return function() {
        const __this = this;
        const args = arguments;
        if(timer) {
            return;
        }
        timer = setTimeout(function() {
            fn.apply(__this, args);
            clearTimeout(timer);
            timer = null;
        }, n)
    }
}
```

# 分时函数

对于数据量大的情况，分批次操作数据。

```JavaScript
// data 为数据，fn 为逻辑执行函数， n 为每次操作的数据量, time 为每次操作之间的间隔
const timeChunk = function(data, fn, n, time) {
    const operator = function() {
        for(let i = 0; i < n; i++) {
            fn(data.shift());
        }
    }

    return function() {
        let timer = setInterval(function() {
            if(data.length === 0) {
                clearTimeout(timer);
                timer = null;
                return;
            }
            operator();
        }, time)
    }
}
```

# new 功能

```JavaScript
const New = function() {
    let obj = {};
    const Constructor = [].shift.apply(arguments); // 获取构造函数
    obj.__proto__ = Constructor.prototype;
    const res = Constructor.apply(obj, arguments); // 借用构造函数继承属性

    return typeof res === 'object' ? res : obj;
}

// 使用
// 类似 let a = new A(1);
let a = New(A, 1);
```

# bind 函数

```JavaScript
Function.prototype.bind = function() {
    const __this = this; // bind
    const __that = [].shift.apply(arguments); // 需要绑定的 this
    let bindArgs = [].slice.apply(argusments); // 使用 bind 时的参数

    return function() {
        let args = [].slice.apply(arguments); // 原函数参数
        // bind 返回的还是函数
        return __this.apply(__that, [].concat.apply(bindArgs, args));
    }
}
```

# Ajax

```JavaScript
const getJSON = function(url) {
  return new Promise(function(resolve, reject) {
    let xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onreadystatechange = function() {
        if (this.readyState !== 4) {
            return;
        }
        if (this.status === 200) {
            resolve(this.res);
        } else {
            reject(new Error(this.message));
        }
    };
    xhr.send();
  });
};
```