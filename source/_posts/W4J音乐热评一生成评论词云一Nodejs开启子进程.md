---
title: W4J音乐热评一生成评论词云一Nodejs开启子进程
date: 2020-02-01 09:15:46
tags:
- JavaScript
- Node.js
- W4J
- segment
- child_process
- 词云
categories:
- JavaScript
id: lilio0vu78
---
## 功能场景

1. 获取888条评论内容，使用`segment`进行分词。
2. 统计词出现次数，生成`{'喜欢':67,'爱情':56}`数据结构。
3. 前端使用`echarts-wordcloud`生成词云。

## 功能难点

- 使用 [segment](https://www.npmjs.com/package/segment) 中文分词模块，但是会有一个小问题：`请勿用此模块来对较长且无任何标点符号的文本进行分词，否则会导致分词时间成倍增加`。
- 实际情况也是如此，个别时候分词会导致长时间无反应，相当长的时间，但有时候也会很流畅。

## 解决方案

1. 开启`Nodejs`子进程，单独运行分词功能。
2. 若超出设定的运行时间，关闭子进程。提示用户重新生成。

## 代码实现

### 子进程代码`ciYun.js`

- 将其封装成自执行模块

```js
((() => {
    //所有代码将写在这里
}))();
```

- 获取评论信息，处理成长字符串

```js
let selectInfo = await connectMysql.selectInfo(selectCiYunList);
let info = selectInfo.map(item => {
    return item.comment_text
}).join(',');
//info==>前任这种东西呢,过了好久你再回头,都不知道当初看上他什么了......
```

- 设置`segment`参数，进行分词

```js
//使用前引入
const Segment = require('segment');
const segment = new Segment();
// 使用默认的识别模块及字典，载入字典文件需要1秒，仅初始化时执行一次即可
segment.useDefault();
//去除停止符,载入词典
segment.loadStopwordDict('stopword.txt');
 
//使用分词
let overInfo = segment.doSegment(info, {
    simple: true,//不返回词性
    stripPunctuation: true,//去除标点符号
    stripStopword: true,//配合loadStopwordDict使用去除停止符
});
//overInfo==>['戴着','耳机','听到','耳朵','躺着',......
```

- 分词数组进行统计词个数

```js
let numObject = {};
let hanzi = /^[\u4e00-\u9fa5]*$/;
overInfo.forEach((value, index) => {
    //获取是汉字的词并且字数大于1
    if (value.length > 1 && hanzi.test(value)) {
        numObject[value] == void 0 ? numObject[value] = 1 : numObject[value] = ++numObject[value]
    }
});
//numObject==>{'葛炮版': 1,'入门': 1,'下载': 10,'镜音': 1,'双子': 1,......
```

- 将准备好的数据通过`IPC`通道发送到父进程，之后关闭`IPC`通道

```js
//process 对象是一个全局变量，它提供有关当前 Node.js 进程的信息并对其进行控制。
process.send({ciyunInfo});//发送数据
process.disconnect();//关闭通道
```

### 主进程代码`index.js`

- 创建异步子进程的方法，更多信息可参考[child_process](http://nodejs.cn/api/child_process.html)

```js
//使用子进程执行命令，缓存子进程的输出，并将子进程的输出以回调函数参数的形式返回。
child_process.exec(command[, options][, callback])
//使用指定的命令行参数创建新进程。
child_process.spawn(command[, args][, options])
//是 spawn()的特殊形式，用于在子进程中运行的模块，如 fork('/haha.js') 相当于 spawn('node', ['/haha.js']) 。与spawn方法不同的是，fork会在父进程与子进程之间，建立一个通信管道，用于进程之间的通信。
child_process.fork(modulePath[, args][, options])
```

- 创建子进程运行`ciYun.js`

```js
const {fork} = require('child_process');
const forked = fork('./pageService/ciYun.js');
```

- 监听子进程通过`IPC`通道发送的数据

```js
forked.on('message', (async msg => {
    //msg==>{'葛炮版': 1,'入门': 1,'下载': 10,'镜音': 1,'双子': 1,......
}));
```

- 设置超时时间，若超时关闭子进程

```js
setTimeout(function () {
    forked.kill();
}, 5000);
```

## 最终效果

![微信截图_20200520110933.png](https://upload-images.jianshu.io/upload_images/7059932-5f0b636e04a271b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
