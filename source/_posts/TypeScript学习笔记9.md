---
title: TypeScript学习笔记 泛型函数与泛型接口
date: 2019-12-14 18:50:42
tags:
- JavaScript
- TypeScript
- 学习笔记
categories:
- TypeScript
id: rJmYZPcdMZ
---
# 泛型

- 希望一个函数和接口可以支持多种数据类型，有很大的灵活性。
- 不预先确定的数据类型，具体的类型在使用的时候才能确定。
- `重载`和`联合类型`也可实现支持多种类型。

```ts
// 重载
function log(value: string): string;
function log(value: string[]): string[];
function log(value: any) {
    console.log(value);
    return value
}
```

```ts
// 联合类型
function log(value: string | string[]): string | string[] {
    console.log(value);
    return value
}
```

- 使用泛型改造`log函数`。
- 定义函数类型为`<B>`，参数类型为`B`，函数返回值类型为`B`。
- 类型`B`不需要预先的制定，就相当于`any`类型，也保证了输入和返回值类型是一致的。

```ts
// 定义泛型函数
function log<B>(value: B): B {
    console.log(value);
    return value
}
// 使用泛型函数
// 调用时定义
log<string>('1')
// 类型推断省略类型参数
log(1)
```

- 类型别名，定义泛型函数类型。

```ts
function log<B>(value: B): B {
    console.log(value);
    return value
}
// 类型别名，定义泛型函数类型
type Log = <B>(value: B) => B
let myLog: Log = log;
myLog(124);
```

- 泛型接口，约束所有接口的所有成员
```ts
function log<B>(value: B): B {
    console.log(value);
    return value
}
// 约束接口中的一个函数
interface Log {
    // 约束其中一个函数
    <B>(value: B): B
}

let myLog: Log = log;
myLog(1234)

// 约束接口的所有成员
interface Log<B> {
    (value: B): B
}

let myLog: Log<number> = log;
myLog(1234)

// 约束接口指定默认类型
interface Log<B = number> {
    (value: B): B
}

let myLog: Log = log;
myLog(1234)
```
