---
title: JavaScript-Promise对象
date: 2019-06-30 16:14:04
tags:
- JavaScript
- Promise
categories:
- JavaScript
id: ZRn6kBsoy4
photos:
- /2019/06/30/ZRn6kBsoy4/1.jpg
---
# Promise介绍

- `Promise`是一个对象，它代表了一个异步操作的最终完成或者失败，及其结果值。
- 本质上Promise是一个函数返回的对象，可以在它上面绑定回调函数，这样就不需要再一开始把回调函数作为参数传入这个函数。

# Promise语法

- `executor`是带有`resolve`和`reject`两个参数的函数。
- `Promise`构造函数执行时立即调用`executor`函数，`resolve`和`reject`两个函数作为参数传递给`executor`。
- `resolve`和`reject`函数被调用时，分别将`Promise`的状态改为`fulfilled`(完成)或`rejected`(失败)。
- `executor`内部通常会执行一些异步操作，一旦异步操作执行完毕，成功时调用`resolve`函数将`Promise`状态改成`fulfilled`，失败时调用`reject`函数将`Promise`状态改成`rejected`。
- 如果在`executor`函数中抛出一个错误，那么该`Promise`状态为`r`ejected`。`executor`函数的返回值会被忽略

```js
new Promise( function(resolve, reject) {...} /* executor */  );
```

# Promise描述

- 一个`Promise`有以下几种状态:
    - `pending`: 初始状态，既不是成功，也不是失败状态。
    - `fulfilled`: 意味着操作成功完成。
    - `rejected`: 意味着操作失败。
- `pending`状态的`Promise`对象可能会变为`fulfilled`状态并传递一个值给相应的状态处理方法，也可能变为失败状态`rejected`并传递失败信息。
- 当其中任一种情况出现时，`Promise`对象的`then`方法绑定的处理方法`handlers`就会被调用。
    - `then`方法包含两个参数：`onfulfilled`和`onrejected`，它们都是`Function`类型。
- 当`Promise`状态为`fulfilled`时，调用`then`的`onfulfilled`方法。

```js
let a = new Promise((resolve, reject) => {
    resolve('操作成功！')
});

a.then(value => {
    // 操作成功！ onfulfilled
    console.log(value, 'onfulfilled')
}, err => {
    console.log(err, 'onRejected')
});

```

- 当`Promise`状态为`rejected`时，调用`then`的`onrejected`方法。

```js
let a = new Promise((resolve, reject) => {
    reject('操作失败！')
});

a.then(value => {
    console.log(value, 'onfulfilled')
}, err => {
    //操作失败！ onRejected
    console.log(err, 'onRejected')
});
```

# Promise方法

### Promise.all(iterable)

- `Promise.all(iterable)`方法返回一个`Promise`实例，此实例在`iterable`参数内所有的`promise`都完成`resolved`。
- 如果参数中`promise`有一个失败`rejected`，此实例回调失败`reject`，失败的原因是第一个失败`promise`的结果。
    - 如果你传入的`promise`中，有四个`promise`在一定的时间之后调用成功函数，有一个立即调用失败函数，那么`Promise.all`将立即变为失败。
- 此方法在集合多个`promise`的返回结果时很有用，它通常在启动多个异步任务并发运行并为其结果创建承诺之后使用，以便人们可以等待所有任务完成。

```js
// 全部执行结果为成功时
let a = new Promise((resolve, reject) => {
    resolve(1)
});
let b = Promise.resolve(2);
// 如果非promise值，这些值将被忽略，但仍然会被放在返回数组中
let c = 3;
Promise.all([a, b, c]).then(value => {
    // [1,2,3]
    console.log(value)
});
```

```js
// b执行失败，返回第一个失败的结果
let a = new Promise((resolve, reject) => {
    resolve(1)
});
let b = Promise.reject(2);
let c = Promise.reject(3);
Promise.all([a, b, c]).then(value => {
    console.log(value)
}, err => {
    // 2
    console.log(err)
});

```

### Promise.race(iterable)

- `Promise.race(iterable)`方法返回一个`promise`，一旦迭代器中的某个`promise`解决或拒绝，返回的`promise`就会解决或拒绝。
- 可以理解为竞速，谁先执行完返回谁的结果。

```js
let a = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('a')
    }, 100)
});

let b = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('b')
    }, 500)
});

Promise.race([a, b]).then(res => {
    // a更快所以返回a的结果
    console.log(res)
})
```

### Promise.reject()

- `Promise.reject()`方法返回一个带有失败原因的`Promise`对象。

```js
let a = Promise.reject('失败了！！');

a.then(res => {
    console.log(res)
}, err => {
    //失败了！！
    console.log(err)
});
```

### Promise.resolve()

- `Promise.resolve()`方法返回一个以给定值解析后的`Promise`对象。

```js
let a = Promise.resolve('成功了！！');

a.then(res => {
    //成功了！！
    console.log(res)
}, err => {
    console.log(err)
});
```

# Promise原型方法

### Promise.prototype.then(onFulfilled, onRejected)

- `then()`方法返回一个`Promise`。它最多需要有两个参数：`Promise`的成功和失败情况的回调函数。
- `onFulfilled`当`Promise`变成接受状态`fulfilled`时调用的函数。
- `onRejected`当`Promise`变成拒绝状态`rejected`时调用的函数。

```js
let a = Promise.resolve('成功了');
a.then(res => {
    // 成功了
    console.log(res)//成功时
}, err => {
    console.log(err)//失败时
})
```
- 链式调用
    - `then`方法返回一个`Promise`对象，其允许方法链。

```js
// 链式调用 
let a = Promise.resolve('成功了');
a.then(res => {
    return Promise.reject('失败了');
}, err => {
    console.log(err)
}).then(res => {
    console.log(res)
}, err => {
    // 失败了
    console.log(err)
})
```

### Promise.prototype.catch(onRejected)

- `catch()`方法返回一个`Promise`，并且处理拒绝的情况。
```js
// catch链式操作 1
let a = Promise.reject('失败了1');
a.then(res => {
    console.log(res)
}).catch(err => {
    // 失败了1
    console.log(err);
    return Promise.reject('失败了2');
}).then(res => {
    console.log(res)
}).catch(err => {
    // 失败了2'
    console.log(err)
})
```

```js
// catch链式操作 2
new Promise((resolve, reject) => {
    console.log('初始化');
    resolve();
}).then(() => {
    throw new Error('有哪里不对了');
    console.log('执行「这个」”');
}).catch(() => {
    console.log('执行「那个」');
}).then(() => {
    console.log('执行「这个」，无论前面发生了什么');
});
// 最终输出结果
// 初始化
// 执行“那个”
// 执行“这个”，无论前面发生了什么
```

- 如果`then`函数中有`onRejected`回调函数，`catach`不会执行

```js
let a = Promise.reject('失败了');
a.then(res => {
    console.log(res);
}, err => {
    // 失败了 1
    console.log(err, 1);
}).catch(err => {
    console.log(err, 2);
});
```

### Promise.prototype.finally(onFinally)

- `finally()`方法返回一个`Promise`。在`promise`结束时，无论结果是`fulfilled`或者是`rejected`，都会执行指定的回调函数。
- 由于无法知道`promise`的最终状态，所以`finally`的回调函数中不接收任何参数，它仅用于无论最终结果如何都要执行的情况。

```js
let a = Promise.reject('失败了');
a.then(res => {
    console.log(res);
}).catch(err => {
    // 失败了
    console.log(err);
}).finally(() => {
    // 123
    console.log(123)
})
```