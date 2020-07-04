---
title: TypeScript学习笔记 抽象类与多态
date: 2019-12-09 15:12:45
tags:
- JavaScript
- TypeScript
- 学习笔记
categories:
- TypeScript
id: 6WJMcN5tvl
---
# TypeScript抽象类

- 抽象类，只能被继承，不能被抽象化的类。
- `abstract`定义抽象类，抽象类中定义方法，子类中可以直接调用。
- 在抽象类内可以抽象方法，不指定具体的实现。好处就是抽离出方法的共性，有利于代码的服用，知道此方法有其他的实现方式。
```ts
abstract class Animal {
    // 抽象类中定义方法
    eat() {
        console.log('eat')
    }
    // 在抽象类中定义抽象方法，在子类中具体实现
    abstract sleep(): void
}

class Dog extends Animal {
    constructor() {
        super();
    }
    // 在子类中实现这个抽象方法
    sleep() {
        console.log('dog sleep')
    }
}

let dog = new Dog();
// eat
dog.eat();
// 使用抽象方法
// dog sleep
dog.sleep();
```

# TypeScript抽象类的多态

- 同一个抽象方法，在多个子类中对这个方法有多个实现，在程序运行的时候会根据不同的对象，执行不同的操作。

```ts
// 定义抽象类
abstract class Animal {
    // 定义抽象方法
    abstract sleep(): void
}
// 创建Dog类继承Animal
class Dog extends Animal {
    // 实现sleep方法
    sleep() {
        console.log('dog sleep')
    }
}
// 创建dog实例
let dog = new Dog();

// 创建Cat类继承Animal
class Cat extends Animal {
    // 实现sleep方法
    sleep() {
        console.log('cat sleep')
    }
}
// 创建cat实例
let cat = new Cat();
// 定义animals数组，类型是Animal[]
let animals: Animal[] = [dog, cat];
animals.forEach(i => {
    // 循环依次执行sleep方法，判断具体的实例，依次打印
    // dog sleep
    // cat sleep
    i.sleep();
});

```

# TypeScript类型this类型

- 类的成员方法返回this，可以实现链式调用。

```ts
class WorkFlow {
    step1() {
        console.log(1);
        return this;
    }

    step2() {
        console.log(2);
        return this;
    }
}
// 依次打印
// 1
// 2
new WorkFlow().step1().step2();
```

- 继承的时候，this类型也可以表现出多态，this既可以是夫类型也可以是子类型。

```ts
class WorkFlow {
    step1() {
        console.log(1);
        return this;
    }

    step2() {
        console.log(2);
        return this;
    }
}

new WorkFlow().step1().step2();
// d
class Myflow extends WorkFlow {
    next() {
        console.log(3);
        return this;
    }
}
// 依次打印
// 3
// 1
// 2
new Myflow().next().step1().step2();
```