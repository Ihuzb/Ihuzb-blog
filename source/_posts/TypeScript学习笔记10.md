---
title: TypeScript学习笔记 泛型类与泛型约束
date: 2019-12-16 20:30:20
tags:
- JavaScript
- TypeScript
- 学习笔记
categories:
- TypeScript
id: v3H9oaesFL
---

# 泛型类

- 泛型不能约束类的静态成员，`static`修饰符修饰的成员。

```ts
class Log<T> {
    run(value: T) {
        console.log(value)
        return value
    }
}
// 指定参数类型
let log = new Log<number>()
log.run(124)
// 当不定义类型时，传入的参数可以是任意的类型
let log1 = new Log()
log1.run(444)
```

# 泛型约束

```ts
// 1 预定义接口
interface Length {
    length: number
}
// 2 T继承Length接口
function log<T extends Length>(value: T): T {
    // T类型上默认没有length属性，需要预定义接口
    console.log(value, value.length);
    return value;
}
// 函数执行，不管传入什么参数必须要有length属性，比如数组，字符串和对象
log({length: 1})
log('1')
log([1, 2, 3])
```

# 总结

- 函数和类可以轻松支持多种类型，增强程序的扩展性。
- 不必写多条函数重载，冗长的联合类型声明，增强代码可读性。
- 灵活控制类型之间的约束。