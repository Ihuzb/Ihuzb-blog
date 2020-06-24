---
title: JS常用正则表达式
date: 2019-06-13 09:22:18
id: yIxumQvhiY
tags:
- JavaScript
- 正则表达式
categories:
- JavaScript
photos:
- /2019/06/13/yIxumQvhiY/1.jpg
---
![](JS常用正则表达式/1.jpg)

### 添加千分位符

```javascript
let text = 456890345.678;
let textNew = (text.toString()).replace(/\B(?=(\d{3})+(?!\d))/g, ',');//456,890,345.678
```

### 验证邮箱格式

```javascript
let email = '1516608****@163.com';
let reg = /^([a-zA-Z]|[0-9])(\w|\-)+@[a-zA-Z0-9]+\.([a-zA-Z]{2,4})$/;
let result = reg.test(email);//true
```

### 验证手机号码格式

```javascript
let tel = "1516608****";
var reg = /^[1][3,4,5,7,8][0-9]{9}$/;//重点在于[3,4,5,7,8]，现有号段13，15等，根据新的号段随时添加
let result = reg.test(tel);//true
```