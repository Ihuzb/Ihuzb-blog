---
title: TypeScript学习笔记 对象类型接口&函数类型接口
date: 2019-11-29 16:00:25
tags:
- JavaScript
- TypeScript
- 学习笔记
categories:
- TypeScript
id: ZfiPod5T48
---
# 对象类型接口
- 接口可以用来约束对象，函数以及类的结构和类型。

## 定义接口
- 即使传入多余的字段也可以通过类型检查。

```ts
interface List {
    id: number;
    name: string;
}

interface Result {
    data: List[];
}

let render = (result: Result) => {
    result.data.forEach(value => {
        console.log(value.id, value.name)
    })
};

let result = {
    data: [
        {id: 1, name: 'A', sex: '男'},
        {id: 2, name: 'B'}
    ]
}
render(result);
```

## 绕过类型检查
- 类型断言

```ts
//第一种 类型断言
render({
    data: [
        {id: 1, name: 'A', sex: '男'},
        {id: 2, name: 'B'}
    ]
} as Result);
```

```ts
//第二种 类型断言
render(<Result>{
    data: [
        {id: 1, name: 'A', sex: '男'},
        {id: 2, name: 'B'}
    ]
});
```

- 字符串索引签名
    - 用任意的字符串索引`list`可以得到任意的结果。
    - `list`就可以支持多个属性。

```ts
interface List {
    id: number;
    name: string;
    // 字符串索引签名
    [x: string]: any;
}
```

## 可选属性
- 属性后加上`?`，表示可选属性。

```ts
interface List {
    id: number;
    name: string;
    // 加上？ 表示可选属性
    age?: number;
}
```

## 只读属性
- 属性前加上`readonly`，表示只读属性。

```ts
interface List {
    // readonly表示只读属性，只
    readonly id: number;
    name: string;
    age?: number;
    // [x: string]: any;
}
```

## 可索引接口

- 数字索引接口，相当于字符串类型的数组。

```ts
interface StringArray {
    [index: number]: string
}

let chars: StringArray = ['a', 'b'];
```

- 字符传索引接口。

```ts
interface Names {
    [x: string]: string;
}

let names: Names = {'a': 'a', 'b': 'b'}
```

- 混合索引接口。注意返回的类型。

```ts
interface Names {
    [x: string]: string;
    [z: number]: string
}

let names: Names = {'a': 'a', 'b': 'b', 5: 'c'}
```

# 函数类型接口

```ts
// 第一种
type Cdd = (x: number, y: number) => number
// 第二种
interface Add {
    (x: number, y: number): number
}

let cccc: Cdd = (x, y) => x + y
// 6
console.log(cccc(2, 4))
```