---
title: W4J音乐热评一Puppeteer爬虫并发和连接池(2)
date: 2020-01-05 12:50:48
tags:
- JavaScript
- Node.js
- W4J
- 爬虫
- async
- Puppeteer
- generic-pool
categories:
- JavaScript
id: BWYsMSQVZr
---

- 上一节我们完成了并发数的控制，这一节完成连接池。
- 因为单纯只用并发数来控制的话，每次爬取新的页面都会开启新的浏览器，使用完毕后关闭，会造成很大的资源开销。
- 使用连接池，一次性创建指定个数的浏览器实例，随用随取，减少资源开销。

## Puppeteer连接池

- 使用`generic-pool`,这是一个基于`Promise`的通用链接池库。
- 有了他之后我们就可以 将`Puppeteer`实例放在我们的链接池中,首先启动指定数量的浏览器实例，如果有请求进来，那么就去池子里面去取一个实例。
- 更多详细信息可点击这里[generic-pool](https://github.com/coopernurse/node-pool)

```js
# 安装generic-pool
npm i generic-pool --save

# 安装完成
+ generic-pool@3.7.1
```

- 创建一个文件专门处理连接池`genericPool.js`
- 代码参考大佬[使用 generic-pool 优化 puppeteer 并发问题](https://blog.guowenfh.com/2019/06/16/2019/puppeteer-pool/)这篇文章,稍加修改.

```js
//genericPool.js
const puppeteer = require('puppeteer')
const genericPool = require('generic-pool')
/**
 * 初始化一个 Puppeteer 池
 * @param {Object} [options={}] 创建池的配置配置
 * @param {Number} [options.max=10] 最多产生多少个 puppeteer 实例 。如果你设置它，请确保 在引用关闭时调用清理池。 pool.drain().then(()=>pool.clear())
 * @param {Number} [options.min=1] 保证池中最少有多少个实例存活
 * @param {Number} [options.maxUses=2048] 每一个 实例 最大可重用次数，超过后将重启实例。0表示不检验
 * @param {Number} [options.testOnBorrow=2048] 在将 实例 提供给用户之前，池应该验证这些实例。
 * @param {Boolean} [options.autostart=false] 是不是需要在 池 初始化时 初始化 实例
 * @param {Number} [options.idleTimeoutMillis=3600000] 如果一个实例 60分钟 都没访问就关掉他
 * @param {Number} [options.evictionRunIntervalMillis=180000] 每 3分钟 检查一次 实例的访问状态
 * @param {Object} [options.puppeteerArgs={}] puppeteer.launch 启动的参数
 * @param {Function} [options.validator=(instance)=>Promise.resolve(true))] 用户自定义校验 参数是 取到的一个实例
 * @param {Object} [options.otherConfig={}] 剩余的其他参数 // For all opts, see opts at https://github.com/coopernurse/node-pool#createpool
 * @return {Object} pool
 */
const initPuppeteerPool = (options = {}) => {
    const {
        max = 10,
        min = 3,//建议连接池最小(min)实例数,应与并发数一致
        maxUses = 2048,
        testOnBorrow = true,
        autostart = false,
        idleTimeoutMillis = 3600000,
        evictionRunIntervalMillis = 180000,
        puppeteerArgs = {},
        validator = () => Promise.resolve(true),
        ...otherConfig
    } = options

    const factory = {
        create: () =>
            puppeteer.launch(puppeteerArgs).then(instance => {
                // 创建一个 puppeteer 实例 ，并且初始化使用次数为 0
                instance.useCount = 0
                return instance
            }),
        destroy: instance => {
            instance.close()
        },
        validate: instance => {
            // 执行一次自定义校验，并且校验校验 实例已使用次数。 当 返回 reject 时 表示实例不可用
            return validator(instance).then(valid => Promise.resolve(valid && (maxUses <= 0 || instance.useCount < maxUses)))
        }
    }
    const config = {
        max,
        min,
        testOnBorrow,
        autostart,
        idleTimeoutMillis,
        evictionRunIntervalMillis,
        ...otherConfig
    }
    const pool = genericPool.createPool(factory, config)
    const genericAcquire = pool.acquire.bind(pool)
    // 重写了原有池的消费实例的方法。添加一个实例使用次数的增加
    pool.acquire = () =>
        genericAcquire().then(instance => {
            instance.useCount += 1
            return instance
        })
    pool.use = fn => {
        let resource
        return pool
            .acquire()
            .then(r => {
                resource = r
                return resource
            })
            .then(fn)
            .then(
                result => {
                    // 不管业务方使用实例成功与后都表示一下实例消费完成
                    pool.release(resource)
                    return result
                },
                err => {
                    pool.release(resource)
                    throw err
                }
            )
    }
    return pool
}
module.exports = initPuppeteerPool
```

- 整体流程如下：
    - 在服务启动时启动池。
    - 请求到达 => 从池中取得一个`Puppeteer`实例 => 打开tab页 => 运行代码 => 关闭tab页 => 返回数据（其他的管理都交给池了）
- 那么我们之前的代码也要稍加修改.

```js
//index.js
const puppeteer = require('puppeteer');
const mapLimit = require('async/mapLimit');
const initPuppeteerPool = require('./genericPool');
//准备的音乐id数组
let musicId = [1407551413, 1303289043, 1417862046];
// 全局只应该被初始化一次
const pool = initPuppeteerPool({ 
    puppeteerArgs: {
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
    }
})
//并发次数为3,建议连接池最小(min)实例数,应与并发数一致
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
        // 在业务中取出实例使用
        const page = await pool.use(async browser => {
            const page = await browser.newPage();
            //同样的域名不同id
            await page.goto(`https://music.163.com/#/song?id=${id}`);
            return page
        })
        const iframe = await page.frames().find(f => f.name() === 'contentFrame');
        const musicComment = await iframe.$('.cmmts.j-flag');
        let commentList = await iframe.evaluate((e) => {
            let comment = Array.from(e.getElementsByClassName('cnt f-brk'));
            //只获取前五条
            return comment.map((item) => item.innerText).filter((item, index) => index <= 5);
        }, musicComment);
        //关闭标签页!!!!!
        await page.close();
        //返回当前音乐爬取的数据
        resolve(commentList);
    })
}
```

- 这次时关闭标签页而不是关闭浏览器.
- 最终的效果,谁用谁知道,运行速度有很大的提升.
- 至此,一个比较完整的并发和连接池的爬虫就完成了,下一步该解决IP的问题.