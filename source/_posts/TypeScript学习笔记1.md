---
title: TypeScript学习笔记 入门知识
date: 2019-11-25 15:40:42
tags:
- JavaScript
- TypeScript
- 学习笔记
categories:
- TypeScript
id: tnJSg4etzv
---
# 强类型与弱类型

- 强类型语言：不允许改变变量的数据类型，除非进行强制类型转换。
    - 严格的限制，可以避免很多低级错误。Java
- 弱类型语言：变量可以被赋予不同的数据类型。
    - JS就是弱类型语言，虽然灵活，但是会造成bug。
    
```js
let x = 1;
let y = true;
x = y;
//true
let z = 'a';
x = z;
//'a'
```

# 静态类型和动态类型

- 静态类型语言：在编译阶段确定所有变量的类型。
    - 对类型极度严格，立即发现错误，运行时性能好，自文档化

```c++
//C++
class C{
    public:
        int x;
        int y;
}

int add(C a,C b){
    return a.x + a.y + b.x + b.y
}
```

- 动态类型语言：在执行阶段确定所有变量的类型。
    - 对类型非常宽松，Bug可能隐藏数月甚至数年，运行时性能差，可读性差。

```js
//JS
class C {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }
}

function add(a, b) {
    return a.x + a.y + b.x + b.y
}
```

# 使用TypeScript

## 安装TypeScript

```js
//全局安装TypeScript
# npm i typescript -g
```

## 创建TypeScript配置文件

```js
# tsc --init
//生成tsconfig.json文件，ts的所有配置都可以在这里面设置
```

## 第一句'Hello TypeScript'

- 创建`src`目录，新建`index.ts`文件，写入代码；

```ts
let hello: string = 'Hello TypeScript';
```

- 编译ts文件，在控制台执行以下代码；

```js
# tsc ./src/index.ts
//生成index.js文件。
```

- 生成的`index.js`文件内容；

```js
var hello = 'Hello TypeScript';
```
