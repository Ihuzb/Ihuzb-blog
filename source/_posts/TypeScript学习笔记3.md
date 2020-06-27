---
title: TypeScript学习笔记 枚举类型
date: 2019-11-27 14:57:33
tags:
- JavaScript
- TypeScript
- 学习笔记
categories:
- TypeScript
id: GK8jzgcyF0
---
# 枚举

- 一组有名字的常量集合。
- 枚举的值无法通过外部修改。
-  数字枚举
    - 下标从0开始，也可指定下标。

```ts
enum Role {
    Reporter,
    Developer,
    Maintainer,
    Owner,
    Guest
}
// 0
console.log(Role.Reporter);
// Reporter
console.log(Role[0]);
```

- 字符串枚举

```ts
enum Messade {
    Success = '恭喜你，成功了',
    Fail = "抱歉，失败了"
}
// '恭喜你，成功了'
console.log(Messade.Success);
```

- 枚举成员

```ts
enum Char {
    //const
    a,
    b = Char.a,
    c = 1 + 3,
    //computend
    d = Math.random(),
    e = '123'.length
}
```

- 常量枚举
    - 不需要对象，只需要对象得值。

```ts
const enum Month {
    Jan,
    Fed,
    Mar
}
let month = [Month.Jan, Month.Fed, Month.Mar]
//  [0, 1, 2]
console.log(month);
```

