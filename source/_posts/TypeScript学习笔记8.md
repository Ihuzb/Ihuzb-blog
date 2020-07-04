---
title: TypeScript学习笔记 类与接口的关系
date: 2019-12-11 17:33:59
tags:
- JavaScript
- TypeScript
- 学习笔记
categories:
- TypeScript
id: 98gxEa87zw
---

# 类类型接口

- implements关键字，类实现接口。
- 类实现接口必须实现接口中所有属性。如果类定义自己的方法或属性是可以的。
- 接口只能约束类的公有成员。接口也不能约束类的构造函数。

```ts
// 定义Human接口
interface Human {
    name: string;
    eat(): void;
}
// Asian是按Human接口
class Asian implements Human {
    constructor(name: string) {
        this.name = name
    }
    name: string;
    // private name: string; 将name定义为私有成员，这样是不可以的
    eat() {}
    // 自定义方法
    sleep() {}
}
```

# 接口继承接口

- 接口可以像类一样相互继承，一个接口可以继承多个接口。
- 接口的继承可抽离出可重用的接口，也可以将多个接口合并成一个接口。
```ts
interface Human {
    name: string;
    eat(): void;
}

// Man接口继承Human接口
interface Man extends Human {
    run(): void
}

interface Child {
    cry(): void
}
// 继承多个接口，用逗号分开
interface Boy extends Man, Child {}
// 定义对象，符合Boy的定义。
// 这里需要包含4个属性，分别来自Human，Man，Child
let boy:Boy={
    // Human
    name:'',
    eat(){},
    // Man
    run(){},
    // Child
    cry(){}
}
```

# 接口继承类

- 接口把类的成员都抽象出来。只有类的成员结构没有具体实现。

```ts
// 接口继承类
class Auto {
    state = 1
}

// 接口中就隐含了state属性
interface AutoInter extends Auto {}

let fun = (auto: AutoInter) => {
    // {state: 3}
    console.log(auto)
}
let auto = {
    state: 3
}
fun(auto);
```

- 类实现接口，定义类C实现AutoInter接口
- 定义Bus是Auto的子类，实现AutoInter接口
- 接口在抽离类成员的时候，受保护成员，私有成员和公共成员都会抽离。

```ts
// 类实现接口
class Auto {
    state = 1
    // private state2 = 0
}

// 接口中就隐含了state属性
interface AutoInter extends Auto {}

// 定义类C实现AutoInter接口
class C implements AutoInter {
    state = 1
}
let c=new C();
// 1
console.log(c.state);

// 定义Bus是Auto的子类，实现AutoInter接口
class Bus extends Auto implements AutoInter {
//因为Bus是Auto的子类，可以直接调用state
}
let bus=new Bus();
// 1
console.log(bus.state)
```

# 总结

- 接口之间可以相互继承，能实现接口的复用。
- 类之间也可以相互继承，能实现方法和属性的服用。
- 接口可以通过类来实现，接口只能约束类的公有成员。
- 接口可以抽离出类的成员，包括公有成员，私有成员和受保护成员。