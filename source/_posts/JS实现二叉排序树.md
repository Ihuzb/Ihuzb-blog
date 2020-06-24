---
title: 'JS实现二叉排序树'
date: 2019-06-11 12:10:52
id: XeALgbXgFq
categories:
- JavaScript
tags:
- JavaScript
- 二叉树
- 二叉排序树
- ES6
photos:
- /2019/06/11/XeALgbXgFq/1.jpg
---
![](JS实现二叉排序树/1.jpg)

## 二叉树的特点:

- 二叉树是一种数据结构。
- 由一系列节点组成，具有层级结构。每个节点的特性包含有节点值，关系指针。节点之间存在对应关系。
- 树中存在一个没有父节点的节点，叫做根节点。树的末尾存在一系列没有子节点的节点，成为叶子节点。其他可以叫做中间节点。
- 树的根节点位于第一层，层级数越大，节点位置越深，层基数也叫做树高。

## 二叉树排序是二叉树的一种类型，特点是:

- 节点分为左右子树。
- 在不为空的情况下，左子树子节点的值都小于父节点的值。
- 在不为空的情况下，右子树子节点的值都大于父节点得值。
[![NtoqTH.png](https://s1.ax1x.com/2020/06/23/NtoqTH.png)](https://imgchr.com/i/NtoqTH)

## 代码实现

### 节点用对象来描述，节点特性用对象属性来描述。

```js
//创建节点类
class Node {
    constructor(key) {
        this.key = key;//节点值
        this.left = null;//左指针
        this.right = null;//右指针
    }
}
```

### 二叉树结构用对象来描述。

```js
class BinaryTree {
    constructor() {
        this.root = null;
    }

    // 在这里写代码
}
```

### 插入值

```js
//插入
insert(key) {
    const newNode = new Node(key);
    if (this.root === null) {//设置根节点
        this.root = newNode;
    }
    this.insertNode(this.root, newNode);
}
insertNode(node, newNode) {
    if (newNode.key < node.key) {//插值小于节点值进入左子树
        if (node.left === null) {//直到左子树为空
            node.left = newNode
        } else {//左子树已存在
            this.insertNode(node.left, newNode)
        }
    } else if (newNode.key > node.key){//插入值大于节点值进入右子树
        if (node.right === null) {//直到右子树为空
            node.right = newNode
        } else {//右子树已存在
            this.insertNode(node.right, newNode)
        }
    }
}
```

### 使用方法

```js
// 实例化二叉树
const binaryTree = new BinaryTree();
// key值
const keys = [19, 8, 15, 24, 45, 12, 5];
// 生成二叉排序树
keys.forEach(key => binaryTree.insert(key));
```

### 二叉排序树的遍历

#### 中序遍历

- 总是先遍历左子树，然后访问根节点，接着遍历右子树

```js
//中序遍历
inorderTraversal() {
    let info = [];
    this.inorderTraversalNode(this.root, ({key}) => {
        info.push(key)
    });
    return info;
}
inorderTraversalNode(node, callback) {
    if (node) {//当前节点
        this.inorderTraversalNode(node.left, callback);//遍历左子树
        callback(node);//访问节点
        this.inorderTraversalNode(node.right, callback);//遍历右子树
    }
}
//遍历结果：[5, 8, 12, 15, 19, 24, 45]
```

#### 前序遍历

- 总是先访问根节点，然后遍历左子树，接着遍历右子树。

```js
//前序遍历
preOrderTraversal() {
    let info = [];
    this.preOrderTraversalNode(this.root, ({key}) => {
        info.push(key)
    });
    return info;
}
preOrderTraversalNode(node, callback) {
    if (node) {//当前节点
        callback(node);//访问节点
        this.preOrderTraversalNode(node.left, callback);//遍历左子树
        this.preOrderTraversalNode(node.right, callback);//遍历右子树
    }
}
//遍历结果：[19,  8,  5, 15, 12, 24, 45]
```

#### 后序遍历

- 总是先遍历左子树，接着遍历右子树，最后访问根节点。

```js
//后序遍历
postOrderTraversal() {
    let info = [];
    this.postOrderTraversalNode(this.root, ({key}) => {
        info.push(key)
    });
    return info;
}
postOrderTraversalNode(node, callback){
    if (node) {//当前节点
        this.postOrderTraversalNode(node.left, callback);//遍历左子树
        this.postOrderTraversalNode(node.right, callback);//遍历右子树
        callback(node);//访问节点
    }
}
//遍历结果：[5, 12, 15, 8, 45, 24, 19]
```
### 二叉排序树的查找

#### 查找最大值
- 根据二叉排序树的特点，右子树的值都大于父节点得值。只需要进入节点的右子树查找，当某个节点的右子树为空时，该节点就是最大值。

```js
//最大值
max() {
    return this.maxNode(this.root)
}
maxNode(node) {
    if (node) {
        while (node.right !== null) {//右子树不为空时
            node = node.right;
        }
        return node.key
    }
    return null
}
```

#### 查找最小值

- 根据二叉排序树的特点，左子树的值都小于父节点的值。只需要进入节点的左子树查找，当某个节点的左子树为空时，该节点就是最小值

```js
//最小值
min() {
    return this.minNode(this.root)
}
minNode(node) {
    if (node) {
        while (node.left !== null) {//左子树不为空时
            node = node.left;
        }
        return node.key
    }
    return null
}
```

#### 查找指定值。

- 在二叉排序树中查找有没有节点对应的值与给定值相同。
- 根据排序二叉树的特点，比较给定值与节点值，小于时进入节点左子树。大于时进入节点右子树。等于时返回真。层层对比，最后如果子树为空，则表示没找到。

```js
//查找
search(key) {
    return this.searchNode(this.root, key)
}
searchNode(node, key) {
    if (node === null) {
        return false
    }
    if (key < node.key) {//进入左子树
        return this.searchNode(node.left, key)
    } else if (key > node.key) {//进入右子树
        return this.searchNode(node.right, key)
    } else {//相等
        return true
    }
}
```

### 二叉排序树的删除

- 当删除的节点为叶子节点，关系指针为空，直接把叶子节点设置成空。
- 当删除的节点存在左子树，把父节点的关系指针直接指向左子树。存在右子树同理。
- 当删除的节点存在左右子树时，先去右子树找最小值，然后用最小值替换节点值，最后进入右子树删除最小值对应的节点。

```js
//删除
remove(key) {
    this.root = this.removeNode(this.root, key)
}
removeNode(node, key) {
    if (node === null) {//没有找到值对应节点
        return null
    }
    if (key < node.key) {//给定值小于当前节点值
        node.left = this.removeNode(node.left, key);
        return node
    } else if (key > node.key) {//给定值大于当前节点值
        node.right = this.removeNode(node.right, key);
        return node
    } else {//找到对应的节点
        if (node.left === null && node.right === null) {//节点为叶子节点
            node = null;
            return node
        }
        if (node.right === null) {//节点存在左子树
            node = node.left;
            return node
        } else if (node.left === null) {//节点存在右子树
            node = node.right;
            return node
        }
        //节点存在左右子树时，先去右子树找最小值
        const minKey = this.minNode(node.right);
        //用最小值替换节点值
        node.key = minKey;
        //进入右子树删除最小值对应的节点。
        this.removeNode(node.right, minKey);
        return node;
    }
}
```