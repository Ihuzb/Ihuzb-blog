---
title: TypeScript学习笔记 函数
date: 2019-12-01 19:14:28
tags:
- JavaScript
- TypeScript
- 学习笔记
categories:
- TypeScript
id: QVI855IGBf
---
# 函数

## 定义函数

```ts
// 函数定义
function add1(x: number, y: number) {
    return x + y
}
```

## 定义函数类型

```ts
// 函数类型的定义，没有具体的实现
let add2: (x: number, y: number) => number

type add3 = (x: number, y: number) => number

interface add4 {
    (x: number, y: number): number
}
 
// add2使用方法
add2 = (x, y) => {
    return x + y
};
// 10
console.log(add2(2, 8));

// add3/add4使用方法
let add5: add3 = (x, y) => {
    return x + y
}
console.log(add5(2, 8));
```

## 函数参数的要求

### 参数个数一一对应

- 在`JS`中对函数参数的个数是没有要求的。但是在`TS`中形参和实参必须一一对应。

```ts
function add1(x: number, y: number) {
    return x + y
}

// 正确使用
console.log(add1(2, 8));
// 错误使用
console.log(add1(2, 8, 9));
```

### 可选参数

```ts
// y 是可选参数，必选参数不可位于可选参数之后
function add2(x: number, y?: number) {
    return y ? x + y : x
}
// 2
console.log(add2(2));
```

### 参数默认值

```ts
// y参数默认值是3
function add3(x: number, y = 3, z: number) {
    return x + y + z
}
// 必选参数前默认参数是不可以省略的，用undefined代替
// 14
console.log(add3(2, undefined, 9));
```

### 剩余参数

```ts
function add5(x: number, ...result: number[]) {
    return x + result.reduce((pre, cru) => pre + cru)
}
// 28
console.log(add5(1, 2, 3, 4, 5, 6, 7))
```

### 函数重载

```ts
// 这里是声明
function add4(...value: number[]): number
function add4(...value: string[]): string

// 这里是实现
function add4(...value: any[]) {
    let first = value[0];
    if (typeof first === 'string') {
        return value.join(',')
    }
    if (typeof first === 'number') {
        return value.reduce((pre, cur) => pre + cur)
    }
}
// 3
console.log(add4(1, 2));
// 1,2
console.log(add4('1', '2'));
```