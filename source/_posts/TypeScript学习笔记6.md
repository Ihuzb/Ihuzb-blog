---
title: TypeScript学习笔记 类继承和成员修饰符
date: 2019-12-04 11:13:19
tags:
- JavaScript
- TypeScript
- 学习笔记
categories:
- TypeScript
id: MdQbv7MmeD
---

# ES6 class类

```js
class Dog {
    constructor(name) {
        this.name = name
    }
}
let dog=new Dog('wangwang');
console.log(dog.name);
```

# TypeScript calss类

## 定义类

- 使用ts重构上面的`class类`，给构造函数的参数和成员属性添加类型注解。
- 类成员的属性，都是实例属性，而不是原型属性。
- 类成员的方法，都是实例方法。
- 实例的属性必须有初始值，或者在构造函数中被初始化。

```ts
class Dog {
    // 构造函数的参数添加类型注解
    constructor(jiao: string) {
        // 构造函数中被初始化
        this.name = jiao
    }
    // 成员属性的类型注解
    name: string
    
}

let dog = new Dog('wangwang');
console.log(dog.name);
```

## 类的继承

```ts
class Husky extends Dog {
    constructor(jiao: string, color: string) {
        super(jiao);
        this.color = color
    }

    color: string
}

let husky = new Husky('miaomiao', 'red');
// miaomiao red
console.log(husky.name, husky.color)
```

## 类的修饰符

### public/private

- `public`公有成员，类的所有属性默认都是公有成员。
- `private`私有成员，只能在类的本身调用，不能被实例和子类调用。
- `private`如果加在构造函数，表示这个类既不能实例化，也不能继承。

```ts
class Dog {
    // private 这个类既不能实例化，也不能继承
    private constructor(jiao: string) {
        this.name = jiao
        this.nuwName = jiao
    }
    // public 公有成员
    public name: string
    // private 私有成员
    private nuwName: string
}
// 如果构造函数设为私有，则这里会报错。
let dog = new Dog('wangwang');
// 无法使用
console.log(dog.nuwName);
```

### protected

- `protected`受保护成员，只能在类或子类访问，不能再类实例中访问。
- `protected`如果加在构造函数，表示这个类不能被实例化,能被继承。

```ts
class Dog {
    //protected 这个类不能被实例化 能被继承
    protected constructor() {
    }
    // protected 受保护成员，只能在类或子类访问，不能再类实例中访问
    protected height: number = 123
}
// 如果构造函数设为受保护，则这里会报错。
let dog = new Dog();
// 无法使用
console.log(dog.height);
```

### readonly

- `readonly`只读属性，不可以被更改，一定要初始化。

```ts
class Dog {
    constructor() {
    }
    // readonly 只读属性，不可以被更改，一定要初始化。
    readonly height: number = 123
}
let dog = new Dog();
// 123
console.log(dog.height);
```

### static

- `static`静态成员，只能用过类名来调用，不能通过实例来调用，但是可以被继承。

```ts
class Dog {
    constructor() {
    }
    // readonly 只读属性，不可以被更改，一定要初始化。
    static height: number = 123
}
let dog = new Dog();
// 无法使用
console.log(dog.height);
// 123
console.log(Dog.height);
```

### 构造函数参数添加修饰符

- 将参数变成实例的属性，即可省略类中的定义。

```ts
class Dog {
    // 将参数变成实例的属性，即可省略类中的定义。
    constructor(public name: string) {
        this.name = name
    }
}
let dog = new Dog('wangwang');
console.log(dog.name);
```

# 总结

## 实例后访问

- `public`公有成员，`readonly`只读属性

## 类直接访问

- `static`静态成员

## 可以被继承

- `public`公有成员，`protected`，`readonly`只读属性，`static`静态成员

## 不可以被继承

- `private`私有成员

## 类内部访问

- `private`私有成员，`protected`受保护成员