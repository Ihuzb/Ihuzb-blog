---
title: 发布一个npm包
date: 2021-11-15 19:40:42
tags:
- JavaScript
- Node.js
categories:
- JavaScript
id: dI6bC3dA6b
---
## 准备工作

### 切换npm源
为了能能快的下载npm包会切换到淘宝源，但是我们要把生成的包发布到npm上去，所以要切换到npm源。
```javascript
$ npx nrm use npm
// Registry has been set to: https://registry.npmjs.org/
```
发布完成后可切换回淘宝源
```javascript
$ npx nrm use taobao
// Registry has been set to: https://registry.npmmirror.com/
```
### 注册/登录npm账号
打开 [npm](https://www.npmjs.com/) 注册用户，打开命令行在本地进行npm登录，提示输入用户名、密码和邮箱。
```javascript
$ npm adduser
```
登录完成后确认登录身份
```javascript
$ npm whoami
// ihuzb
```

## 创建包文件

### 创建项目

执行项目初始化，创建项目
```javascript
$ npm init -y
```
创建 `index.js`文件，使用ES6中export default向外暴露成员
```javascript
...
export default SunCalc;
```
为防止多余文件被发布，修改`package.json`文件，新增文件白名单配置，只会发布白名单中的文件。
```javascript
{
    "files": [
        "index.js",
        "package.json",
        "README.md"
    ]
}
```

## 发布包文件

### 发布前准备
发布前修改`package.json`文件中项目名和每次发布时都要修改版本号
```javascript
{
    "name": "项目名", 
    "version": "1.0.12",
}
```

### 发布npm包
在控制台执行发布包文件
```javascript
$ npm publish
```
登录npm查看发布的包信息。

## 文档优化

### gitHub的md文档中显示npm版本号
使用[shields.io](https://shields.io/category/version) 创建npm包的版本图片地址，在md文档中按显示图片方式