---
title: JS函数防抖和函数节流
date: 2019-06-10 11:02:44
id: P3kzrLtR3l
tags:
- JavaScript
- HTML
- 闭包
categories:  
- web前端
photos:
- /2019/06/10/P3kzrLtR3l/1.jpg
---
![](JS函数防抖和函数节流/1.jpg)

- 在做输入框查询的时候，如不加限制尤其是在输入中文，每输入一个拼音都会触发事件，造成计算机资源没必要的消耗。
- 使用防抖和节流可以很好的控制事件触发。
## 函数防抖
- 理解为每次输入都会开启一个定时器，但每次输入都会关闭上一个定时器，直到再也不输入，执行存在的定时器。
- 创建一个闭包，使用`timeout`存储已创建的定时器，若有输入就关闭已创建的定时器，再创建一个新的定时器，使用`bind`处理 `this`问题，使用`arguments`传递参数。
```js
//防抖
function debounce(fn, interval = 600) {
    let timeout = null;
    return () => {//创建一个闭包
        clearTimeout(timeout);//关闭上一个定时器
        timeout = setTimeout((() => {//创建新的定时器
            fn(arguments)
        }).bind(this), interval)
    }
}
```
### 使用方法
```js
let shuru = document.getElementById('shuru');
shuru.oninput = debounce((e) => {
    console.log(shuru.value)
})
```
## 函数节流
- 理解为第一次输入时创建一个指点时间的定时器，使用`canRun`管理状态，初始状态为`true`，将状态修改为`false`，不管后面再怎么输入都不会再创建定时器，直到达到定时器时间即执行，修改状态为`true`，可以创建新的定时器。
```js
//节流
function throttle(fn, interval = 600) {
    let canRun = true;
    return () => {
        if (!canRun) return;
        canRun = false;
        setTimeout((() => {
            canRun = true;
            fn(arguments)
        }).bind(this), interval)
    }
}
```
### 使用方法
```js
let shuru = document.getElementById('shuru');
shuru.oninput = throttle((e) => {
    console.log(shuru.value)
})
```

## 总结
- 函数防抖，直到不再输入才执行。
- 函数节流，达到指定的时间就执行。
- 其实函数节流和函数防抖的实现很简单，主要是通过`setTimeout`和闭包来实现，应用的时候选择合适的方式即可。