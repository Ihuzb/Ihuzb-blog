---
title: W4J音乐热评一Puppeteer爬虫并发和连接池(1)
date: 2020-01-05 11:38:24
tags:
- JavaScript
- Node.js
- W4J
- 爬虫
- async
- Puppeteer
categories:
- JavaScript
id: QFUu7Q8EkJ
---
- 上一节我们完成了Puppeteer的初体验，爬取音乐《囍》的评论。
- 但是一首一首音乐的爬取效率未免过低，准备并发和连接池的功能。
- 设置并发次数，同时爬取多首音乐评论信息。

## Puppeteer并发

### 多首音乐获取评论信息

- 我们要获取《麻雀》《囍》《呓语》这三首歌的评论信息。我们首先获取到他们的音乐id。
- 比如《麻雀》的页面地址为`https://music.163.com/#/song?id=1407551413`其中`id=`后面的值就是音乐的id。
- 我们分别拿到三首歌的id组成数组分别是`[1407551413,1303289043,1417862046]`，改造之前的代码。

#### Nodejs库async

- 首先要安装一个新的库`async`，这个可不`ES6`里面的`async`但有类似的功能。

```JS
# 安装async
npm i async --save

# 安装完成
+ async@3.2.0
```

- 将要用的一个新的方法`mapLimit`，控制并发，详细信息可点击这里[mapLimit](https://caolan.github.io/async/v3/docs.html#mapLimit)。
    - `mapLimit(coll, limit, iteratee, callback)`
        - coll中一般是一个数组，会根据这个数组进行循环。
        - limit为并发限制次数，mapLimit方法将coll中的每一项依次拿给iterator去执行。
        - iteratee可获取coll中的每一项
        - callback获取最后的执行结果。
- 尝试一波！！

```js
const mapLimit = require('async/mapLimit');
let musicId = [1, 2, 3];
mapLimit(musicId, 2, (item, callback) => {
    setTimeout(()=>{
        console.log(item);
        callback(null, item);
    },1000)
}, (err, results) => {
    console.log(results);
});
```

- 执行结果
    - 因为设置的并发次数是2，但是数组长度为3，所以只能并发2次。
    - 也就是说第1次会并发2个，第二次并发1个。
    - 第1个1秒先打印1 2
    - 第2个1秒再打印3
    - 最终返回的结果是[1, 2, 3]
    - 返回的结果是按照之前数组的顺序
- 改造一下爬虫代码
- 大致流程，创建指定并发个数的浏览器，获取完毕后关闭浏览器。

```js
//index.js
const puppeteer = require('puppeteer');
const mapLimit = require('async/mapLimit');
//准备的音乐id数组
let musicId = [1407551413, 1303289043, 1417862046];
//并发次数为3
mapLimit(musicId, 3, (item, callback) => {
    (async () => {
        //并发执行爬取代码，返回结果
        let info = await selectInfo(item);
        callback(null, info);
    })();
}, (err, results) => {
    //三首音乐爬取完成再返回结果
    console.log(results);
});

//定义函数，内部返回Promise
async function selectInfo(id) {
    return new Promise(async (resolve, reject) => {
        const browser = await puppeteer.launch({
            timeout: 15000,
            ignoreHTTPSErrors: true,
            devtools: true,
            headless: true,
            args: [
                '-–disable-dev-shm-usage',
                '-–disable-setuid-sandbox',
                '-–no-first-run',
                '--no-sandbox',
                '-–no-zygote',
                '-–single-process'
            ]
        });
        const page = await browser.newPage();
        //同样的域名不同id
        await page.goto(`https://music.163.com/#/song?id=${id}`);
        const iframe = await page.frames().find(f => f.name() === 'contentFrame');
        const musicComment = await iframe.$('.cmmts.j-flag');
        let commentList = await iframe.evaluate((e) => {
            let comment = Array.from(e.getElementsByClassName('cnt f-brk'));
            //只获取前五条
            return comment.map((item) => item.innerText).filter((item, index) => index <= 5);
        }, musicComment);
        await browser.close();
        //返回当前音乐爬取的数据
        resolve(commentList);
    })
}
```

```js
# 爬取评论内容
[
  [
    '李荣浩：网易云音乐的朋友们 欢迎收听我的新歌《麻雀》',
    '李小年1223P：我在电动车论坛等得好辛苦。',......
  ],
  [
    'Liam_BZ：唢呐倒是个神奇的东西\n中国人的红白喜事，从生到死\n一根铜管全能吹出来',......
  ],
  [
    '写蘑菇的小菇凉：他说——\n如果不想改变的话，请不要改变。',
    'angliaqin：起床？不光起床吧，还听他歌入睡的'......
  ]
]
```

- 一个简单的并发爬虫就完成了，但是这样就会造成另外一个问题。
- 每次爬取都是创建新的浏览器和关闭浏览器，会造成很大的资源开销。不利于接下来大量音乐的爬取。
- 我们接下来用连接池的方式解决这个问题。