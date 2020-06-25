---
title: W4J音乐热评一Puppeteer设置代理IP
date: 2020-01-15 17:23:54
tags:
- JavaScript
- Node.js
- W4J
- 爬虫
- Puppeteer
- 代理IP
- generic-pool
categories:
- JavaScript
id: 09vmKIOyQJ
---
- 当频繁的爬取难免会被网站发现被封锁IP,这个时候代理IP就显得很重要.
- 毕竟,魔高一尺,道高一丈.知己知彼,百战不殆.🤓

## 阿布云HTTP隧道

- 经过百般筛选,我最终选择[阿布云](https://www.abuyun.com/http-proxy/dyn-manual.html)服务,详细信息可点击查看.
- 选择其中的`HTTP隧道（动态版）`,动态版HTTP隧道会为每个请求从IP池中挑选一个随机IP。
- 也就是说我们的每次爬取都将从一个随机ip发出请求。
- HTTP隧道基于HTTP协议，支持HTTP/HTTPS协议的数据接入。
- 平台在云端维护一个全局IP池供HTTP隧道使用，池中的IP会不间断更新，以保证IP池中有足够多的IP供用户使用。

### 购买隧道服务

- 进入到管理页面,选择`HTTP隧道=>动态版`,点击右上角`选购隧道`
[![tcMeYV.md.png](https://upload-images.jianshu.io/upload_images/7059932-30833a01585d627f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://imgchr.com/i/tcMeYV)
- 首先选择时间单位,可小时,天,月,周,季,年.我一般选择时,2个小时足以.
- 隧道数为1即可,但下面的请求数建议按照并发数来设置,比如并发的浏览器示例为5,设置请求数为5,因为他会默认2个请求数,这样就7个.
- 保证每个浏览器示例使用的ip不冲突.
- 随用随买,贼方便,购买完成后会需要`通行证书`和`通行密钥`两个参数,保留好我们接下来会使用.

### 隧道服务使用方法

- 需要登陆到HTTP隧道服务器,一般是`http://http-dyn.abuyun.com:9020`这个地址.
- 这就需要用到我们上面提到的`通行证书`和`通行密钥`
- 改造一下我们的代码.

```js
const puppeteer = require('puppeteer');
const mapLimit = require('async/mapLimit');
const initPuppeteerPool = require('./genericPool');
let musicId = [1407551413, 1303289043, 1417862046];
//设置通行证书和同行密钥,格式一定是要username和password
let agencyIp = {
    username: 'HG*32T**34*8Q*VD',
    password: 'CC4*79*1*15*AE*C'
}
const pool = initPuppeteerPool({
    puppeteerArgs: {
        timeout: 15000,
        ignoreHTTPSErrors: true,
        devtools: true,
        headless: false,
        args: [
            '-–disable-dev-shm-usage',
            '-–disable-setuid-sandbox',
            '-–no-first-run',
            '--no-sandbox',
            '-–no-zygote',
            '-–single-process',
            //设置HTTP隧道服务地址
            '--proxy-server=http://http-dyn.abuyun.com:9020'
        ]
    }
})
mapLimit(musicId, 3, (item, callback) => {
    (async () => {
        let info = await selectInfo(item);
        callback(null, info);
    })();
}, (err, results) => {
    console.log(results);
});
async function selectInfo(id) {
    return new Promise(async (resolve, reject) => {
        const page = await pool.use(async browser => {
            const page = await browser.newPage();
            //使用通行证书和通行密钥登录服务器
            await page.authenticate(agencyIp);
            await page.goto(`https://music.163.com/#/song?id=${id}`);
            return page
        })
        const iframe = await page.frames().find(f => f.name() === 'contentFrame');
        const musicComment = await iframe.$('.cmmts.j-flag');
        let commentList = await iframe.evaluate((e) => {
            let comment = Array.from(e.getElementsByClassName('cnt f-brk'));
            return comment.map((item) => item.innerText).filter((item, index) => index <= 5);
        }, musicComment);
        await page.close();
        resolve(commentList);
    })
}
```

- 简单三步,设置完成,要想验证一下是否成功.
    - 先不设置代理,`headless`设置`false`显示浏览器,在打开的浏览器中,百度搜过ip查看一下默认ip.
        - 本机IP: 123.1\*9.1*7.*6 北京市北京市 联通
    - 再设置上代理,保持`headless`设置不变,查看ip地址.
        - 本机IP: 120.2*.5.*0 河北省保定市 联通
    - 多试几次会发现,设置的ip都是随机的.
- 至此我们的设置代理ip就完成了.
