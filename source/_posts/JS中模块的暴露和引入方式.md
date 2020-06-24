---
title: JS中模块的暴露和引入方式
date: 2019-06-23 11:26:11
id: x1rQ0FB2ub
tags:
- JavaScript
- ES6
categories:
- JavaScript
photos:
- /2019/06/23/x1rQ0FB2ub/1.jpg
---

![](JS中模块的暴露和引入方式/1.jpg)

# export和export default：

- 在创建JavaScript模块时，export 语句用于从模块中导出函数、对象或原始值，以便其他程序可以通过import语句使用它们。  
- 能够在每一个模块中定义多个export 命名导出，但是只允许有一个export default 默认导出。

## export命名导出（每个模块包含任意数量）：

```javascript
//my-module.js

//导出列表
let a = 1, b = 2;
export {a, b}

// 导出单个特性
export let name1, name2;
export let name1 = 1, name2 = 2;
export function FunctionName(){...}
export class ClassName {...}

//重命名导出
export {a as aa,b as bb};

// 解构导出并重命名
export const { name1, name2: bar } = o;
```

## import语句使用命名导出：

```javascript
import { a, b,FunctionName } from 'my-module.js';
```

## export default默认导出（每个模块包含一个）：

```javascript
//my-module.js

// 默认导出
export default expression;
export default function (…) { … } 
export default function nameInfo(…) { … } 
export { name1 as default, … };
```

- 更多详细信息可查询[这里](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/export)官方文档


# exports和module.exports：

- `exports`允许使用快捷方式，因此`module.exports.f = ...`可以更简洁地写成`exports.f = ...`。但是，就像任何变量一样，如果为`exports`赋予了新值，则它将不再绑定到`module.exports`。  
- 能够在每一个模块中定义多个`exports`导出，但是只允许有一个`module.exports`导出。

## exports导出（每个模块包含任意数量）：

```javascript
//my-module.js

//导出多个方法和变量
let a = 1, b = 2;
exports.a = a;
exports.b = b;
exports.FunctionName = () => {
    console.log(123)
};
console.log(exports);//{ a: 1, b: 2, FunctionName: [Function] }
```

## require语句使用导出：

```javascript
const {a, b, FunctionName} = require('./my-module');//可直接解构
console.log(a, b);//1 2
FunctionName();//123
```

## module.exports导出（每个模块包含一个）：

```javascript
//my-module.js

//导出多个方法和变量
let FunctionName = () => {
    console.log(123)
};
let a = 1, b = 2;
module.exports = {a, b, FunctionName};
console.log(module.exports);//{ a: 1, b: 2, FunctionName: [Function: FunctionName] }
```

## require语句使用导出：

```javascript
const {a, b, FunctionName} = require('./my-module');// 可直接解构
console.log(a, b);//1 2
FunctionName();//123
```

- 更多详细信息可查询[这里](http://nodejs.cn/api/modules.html#modules_module_exports)官方文档

# 总结

- `export`和`export default`使用`import`导入。
- `exports`和`module.exports`使用`require`导入。常用的`Nodejs`项目中。
- `export`和`exports`在模块中可使用多个。 
- `export default`和`module.exports`在模块中只可使用一个。 