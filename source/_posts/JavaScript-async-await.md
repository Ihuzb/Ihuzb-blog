---
title: JavaScript-async/await
date: 2019-07-02 21:03:14
tags:
- JavaScript
- async/await
categories:
- JavaScript
id: hzroUWRdFa
---

# async function

- `async function`用来定义一个异步函数。如果在代码中使用了异步函数，就会发现它的语法和结构会更像是标准的同步函数。
- 一个`async`异步函数可以包含`await`指令，该指令会暂停异步函数的执行，并等待`Promise`执行，然后继续执行异步函数，并返回结果。
- `await`关键字只在异步函数内有效。如果你在异步函数外使用它，会抛出语法错误。

```js
let a = Promise.resolve(1);
let b = Promise.resolve(2);
let run = async () => {
    let info = await Promise.all([a, b]);
    //[ 1, 2 ]
    console.log(info)
}
run();
```

- 使用`async`函数重写`promise`链

```js
//promise链式写法
function getProcessedData(url) {
  return downloadData(url) // 返回一个 promise 对象
    .catch(e => {
      return downloadFallbackData(url)  // 返回一个 promise 对象
    })
    .then(v => {
      return processDataInWorker(v); // 返回一个 promise 对象
    });
}

// async 重写后
async function getProcessedData(url) {
  let v;
  try {
    v = await downloadData(url); 
  } catch (e) {
    v = await downloadFallbackData(url);
  }
  return processDataInWorker(v);
}
```
- 最后的`return`语句中没有`await`操作符，因为`async function`的返回值将被隐式地传递给`Promise.resolve`。

# await

- `await`操作符用于等待一个`Promise`对象。它只能在异步函数`async function`中使用。
- `await`表达式会暂停当前`async function`的执行，等待`Promise`处理完成。
- 若`Promise`正常处理`fulfilled`，其回调的`resolve`函数参数作为`await`表达式的值，继续执行`async function`。
- 若`Promise`处理异常`rejected`，`await`表达式会把`Promise`的异常原因抛出。
- 另外，如果`await`操作符后的表达式的值不是一个`Promise`，则返回该值本身。

```js
let a = Promise.resolve(1);
let b = Promise.reject(2);

let run = async () => {
    //处理正常
    let infoA = await a;
    // 1
    console.log(infoA);

    //处理异常
    try {
        let infoB = await b;
    } catch (e) {
        // 2
        console.log(e)
    }

}
run();
```