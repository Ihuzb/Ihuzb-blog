---
title: Vue学习笔记1
date: 2019-07-19 09:25:13
tags:
- JavaScript
- Vue.js
categories:
- JavaScript
id: HMGDd5XGEN
---
# MVVM理解

## 介绍

MVVM是Model-View-ViewModel的缩写。

**Model**代表数据模型，可以在Model中定义数据修改和操作的业务逻辑。

**View**代表UI组件，负责将数据模型转化成UI展示出来。通常就是DOM层。

**ViewModel**代表视图模型层，是View和Model沟通的桥梁。一方面实现了DataBinding，也就是数据绑定，将Model的改变实时的反应到View中，View数据的变化也会同步到Model中。另一方面它实现了DOMListener，也就是DOM监听，当DOM发生一些时间（点击，滚动，touch等）时，可以监听到，并在需要的情况下改变对应的Data。

## 优点

- 分离视图（View）和模型（Model），降低代码耦合，提高试图或者逻辑的重用性。
- 自动更新DOM：利用双向绑定，数据更新后视图自动更新，让开发者从繁琐的手动DOM中解放。

##　缺点

- Bug很难调试。
- 对于大型的图形应用程序，视图比较多，ViewModel的构建和维护的成本都会比较高。

# Vue生命周期

**beforeCreate**（创建前）在数据观测和初始化事件还未开始。
**created**（创建后）完成数据观测，属性和方法的运算，初始化事件，$el属性还没显示出来。
**beforeMount**（载入前）在挂载开始之前被调用，相关的render函数首次被调用。实例已完成以下的配置：编译模板，把data里面的数据和模板生成html。注意此时还没有挂载到html到页面上。
**mounted**（载入后）在el被新创建的vm.$el替换，并挂载到实例上去之后调用。实例已完成以下配置：用上面编译好的html内容替换el属性指向的DOM对象。完成模板中的html渲染到html页面中。此过程中进行ajax交互。
**beforeUpdate**（更新前）在数据更新之前调用，发生在虚拟DOM重新渲染和打补丁之前。可以在该钩子中进一步的更改状态，不会触发附加的重渲染过程。
**updated**（更新后）在由于数据更改导致的虚拟DOM重新渲染和打补丁之后调用。调用时，组建DOM已经更新，所以可以执行依赖于DOM的操作。然而在大多数情况下，应该避免在此期间更新状态，因为这可能导致更新无限循环。该钩子在服务器渲染期间不被调用。
**beforeDestroy**（销毁前）在实例销毁之前调用。实例仍然完全可用。
**destroyed**（销毁后）在实例销毁之后调用。调用后，所有事件监听器会被一处，所有的子实例也会被销毁。该钩子在服务器端渲染期间不被调用。

# computed 计算属性
- 计算属性会进行缓存，如果多次使用，计算属性只会调用一次。
```js
computed: {
      NBA: function () {
        return `${this.firstName}-${this.lastName}`
      },
      CBA: function () {
        return this.movies.join(',');
      }
    }
```
- 为什么计算属性是个函数，使用的时候当成普通属性来使用?
```js
// 计算属性的本质就是set方法
computed: {
      fullName: {
        set: function(){

        },
        get: function () {
          return 'ABC'
        }
      }
    },
```
- 计算属性的缓存，为什么使用计算属性而不使用方法？
    - 使用方法调用几次就会执行几次。
    - 使用计算属性，只调用一次。会对内部的属性进行检查值有没有变化。

# 块级作用域，let和const；对象的增强写法。
- for循环var的问题使用闭包解决，因为函数是一个作用域。
- const使用
    - 一旦给const修饰的标识符被赋值之后，不能修改。
    - 在使用const定义标识符，必须进行赋值。
    - 常量的含义是指向的对象不能修改，但是可以改变对象内部的属性。
- 优先使用const，如果需要更改的变量使用let。

# v-on事件传递event
- 使用$event传递按钮event对象
```html
    <button @click="eventShow(NBA,$event)">安妮1</button>
```

# v-on修饰符
- stop阻止事件冒泡，父标签和子标签都有点击事件的时候；
    - @click.stop
- prevent阻止默认行为，阻止from标签的提交事件。
```html
<form action="baidu" @submit.prevent>
    <button type="submit" @click.prevent="eventShow">安妮1</button>
</form>
```

# DOM渲染问题
- input有输入内容情况下，切换别的input显示，文字依然显示之前的输入内容。
- 因为Vue在进行DOM渲染是，出于性能考虑会使用已存在的元素。
- 给对应的input添加key，并且保证key的不同。

# v-show和v-if的区别
- `v-show`的本质是`css`样式`display:none`，只是隐藏元素还存在。
- `v-if`的本质会直接删除DOM，需要的时候再创建。
- 当需要在显示与隐藏之间频繁切换使用v-show，只一次切换使用v-if。

# 组件的key属性
- Vue的虚拟DOM的Diff算法有关系。
- 当某一层有很多相同的节点时，也就是列表节点时，希望插入一个新的节点。
- 需要使用key给每个节点做一个唯一的表示，Diff算法就可以正确的识别此节点，找到正确的位置区插入新的节点。
- key的作用主要是为了高效的更新虚拟DOM。

# 检测数组更新
- 因为数据是响应式的所以修改数据页面也会更新。
- 响应式的方法
    - push
    - pop
    - shift
    - unshift
    - splice 删除/插入/替换
    - sort
    - reverse
    - Vue.set(修改的对象，索引值，修改后的值)
- 非响应式的方法
    - 根据下标修改

# 我为什么data必须是一个函数
- 组件多次使用data都会返回一个新的独立的对象，新的栈空间创建新的变量并返回。

# slot插槽，使组件具有扩展性