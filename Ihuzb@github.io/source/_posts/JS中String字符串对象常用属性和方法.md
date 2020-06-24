---
title: JS中String字符串对象常用属性和方法
date: 2019-07-01 10:52:45
id: BqI2F0KXTQ
categories:
- JavaScript
tags:
- JavaScript
---
# 属性

## String.prototype

- 所有 `String` 的实例都继承自 `String.prototype.` 任何 `String.prototype` 上的改变都会影响到所有的 `String` 实例。

```javascript
String.prototype.myFun = function () {
    console.log(`字符串:${this}`)
};
let text = 'text';
text.myFun();//字符串:text
```

## String.length

- `length`属性表示一个字符串的长度。

```javascript
let text = '1text2', text1 = '';
console.log(text.length);//6
console.log(text1.length);//0
console.log(String.length);//1
```

# 方法
## String.prototype.charAt()
- `charAt()`方法从一个字符串中返回指定索引的字符。默认从索引0开始获取
### 语法
- `str.charAt(index)`,`index`一个介于0 和字符串长度减1之间的整数。 (0~length-1)

```javascript
let text = 'text';
console.log(text.charAt(1));//e
console.log(text.charAt());//t
//类似于
console.log(text[1]);//e
```

## String.prototype.concat()

- `concat`方法将一个或多个字符串与原字符串连接合并，形成一个新的字符串并返回。`concat`方法并不影响原字符串。

### 语法

- `str.concat(string2, string3[, ..., stringN])`,`string2...stringN`和原字符串连接的多个字符串

```javascript
let text = 'text';
console.log(text.concat('gogo'));//textgogo
```

## String.prototype.endsWith() / String.prototype.startsWith()

- `endsWith()`方法用来判断当前字符串是否是以另外一个给定的子字符串“结尾”的，根据判断结果返回`true`或`false`。

- `startsWith()`方法用来判断当前字符串是否是以另外一个给定的子字符串“开头”的，根据判断结果返回`true`或`false`。

### 语法

-  `str.endsWith(searchString[, length])`。`searchString`要搜索的子字符串。`length`可选。作为`str`的长度。默认值为`str.length`。
-  `str.startsWith(searchString[, position])`。`searchString`要搜索的子字符串。`position`可选。在`str`中搜索`searchString`的开始位置，默认值为0，也就是真正的字符串开头处。

```javascript
let text = 'Hello，世界';
//endsWith
console.log(text.endsWith('世界'));//true
console.log(text.endsWith('世'));//false
 
//startsWith
console.log(text.startsWith('He'));//true
console.log(text.startsWith('e'));//false
 
// 7表示文字长度，意思是取字符串前7位，验证最后一个字符是否匹配，7是长度不是索引。
console.log(text.endsWith('世', 7));//true
 
// 1表示从索引1开始，验证第一个字符是否匹配。
console.log(text.startsWith('e', 1));//true
```

## String.prototype.includes()
- `includes()`方法用于判断一个字符串是否包含在另一个字符串中，根据判断结果返回`true`或`false`。
### 语法
- `str.includes(searchString[, position])`。`searchString`要搜索的子字符串。`position`可选。从当前字符串的哪个索引位置开始搜寻子字符串，默认值为0。
####示例
```javascript
let text = 'Hello，世界';
console.log(text.includes('世界'));//true
console.log(text.includes('你好'));//false
 
//6表示开始搜索字符串的起始下标
console.log(text.includes('世界', 6));//true
```

## String.prototype.indexOf()
- `indexOf()`方法返回调用它的`String`对象中第一次出现的指定值的索引，从`fromIndex`处进行搜索。如果未找到该值，则返回 -1。
### 语法
- `str.indexOf(searchValue [, fromIndex])`。`searchValue`要被查找的字符串值。`fromIndex `可选。数字表示开始查找的位置。可以是任意整数，默认值为 0。

```javascript
let text = 'Hello，世界，世界';//length=11
//第一次出现的指定值的索引
console.log(text.indexOf('世界'));//6
console.log(text.indexOf('世', 3));//6
console.log(text.indexOf('世界', 10));//-1
 
//如果fromIndex的值小于 0，会从位置0处开始查找，
console.log(text.indexOf('世界', -1));//6
 
//如果fromIndex的值大于字符串长度，将会返回 -1，因为开始查找的位置12处，已经是这个字符串的结尾了。
console.log(text.indexOf('世界', 12));//-1
 
//区分大小写
console.log(text.indexOf('hello'));//-1
 
//完整匹配
console.log(text.indexOf('世世'));//-1
```

## String.prototype.lastIndexOf()

- `lastIndexOf()`方法返回调用`String`对象的指定值最后一次出现的索引，在一个字符串中的指定位置`fromIndex`处从后向前搜索。如果没找到这个特定值则返回-1。

### 语法

- `str.lastIndexOf(searchValue [, fromIndex])`。`searchValue`要被查找的字符串值。`fromIndex `可选。待匹配字符串`searchValue`的开头一位字符从`str`的第`fromIndex`位开始从右往左查找。
- 字符串中的字符被从左向右索引。首字符的索引`index`是0，最后一个字符的索引是`stringName.length - 1`。

```javascript
let text = 'HelloWord';//length=9
//      索引0 1 2 3 4 5 6 7 8
//   返回结果>===============>从左往右索引
 
//      查询<0==============8<从右往左查询
console.log(text.lastIndexOf('o'));//6
//如果 fromIndex >= str.length ，则会搜索整个字符串。
console.log(text.lastIndexOf('o', 90));//6
 
//      查询<0========5<从索引5向索引0方向查询
console.log(text.lastIndexOf('o', 5));//4
 
//      查询<0=0<从索引0向索引0方向查询，未查询到结果返回-1。fromIndex<=0等同于0。
console.log(text.lastIndexOf('o', 0));//-1
console.log(text.lastIndexOf('o', -5));//-1
```

## String.prototype.localeCompare()
- `localeCompare()`方法返回一个数字来指示一个参考字符串是否在排序顺序前面或之后或与给定字符串相同。
### 语法
- `referenceStr.localeCompare(compareString[, locales[, options]])`。`compareString`用来比较的字符串。
- 当`引用字符串`在`比较字符串`前面时返回-1
- 当`引用字符串`在`比较字符串`后面时返回 1
- 相同位置时返回 0。
- 更多详细信息点[这里](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare)查询文档

```javascript
// 'a' 引用字符串；'b' 比较字符串
//'a'在'b'之前，返回-1
console.log('a'.localeCompare('b'));//-1
 
// 'b' 引用字符串；'a' 比较字符串
//按照排序规则，'b'在'a'之后，返回1
console.log('b'.localeCompare('a'));//1
 
//按照排序规则，'a'和'a'相同位置，返回0
console.log('a'.localeCompare('a'));//0
 
// '34' 引用字符串；'180' 比较字符串
//按照字符串排序规则，'34'在'180'之后，返回 1
console.log('34'.localeCompare('180'));//1
//设置开启数值排序，'34'在'180'之前，返回 -1
console.log('34'.localeCompare('180', 'kn', {numeric: true}));//-1
```

## String.prototype.match()
- `match()`方法检索返回一个字符串匹配正则表达式的的结果。
### 语法
- `str.match(regexp)`。`regexp`一个正则表达式对象。

```javascript
let str = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz';
let regexp = /[A-E]/gi;//字母A到E，全局匹配忽略大小写
console.log(str.match(regexp));//['A', 'B', 'C', 'D','E', 'a', 'b', 'c','d', 'e']
```

## String.prototype.padEnd() / String.prototype.padStart()

- `padEnd()`方法会用一个字符串填充当前字符串（如果需要的话则重复填充），返回填充后达到指定长度的字符串。从当前字符串的末尾（右侧）开始填充。
- `padStart()`方法用另一个字符串填充当前字符串(重复，如果需要的话)，以便产生的字符串达到给定的长度。填充从当前字符串的开始(左侧)应用的。
### 语法

- `str.padEnd(targetLength [, padString])`。`targetLength`当前字符串需要填充到的目标长度。`padString`填充字符串。
- `str.padStart(targetLength [, padString])`。`targetLength`当前字符串需要填充到的目标长度。`padString`填充字符串。

```javascript
let str = 'ABC';
//String.prototype.padEnd()
console.log(str.padEnd(10, "foo"));//ABCfoofoof
console.log(str.padEnd(6, "12345678"));//ABC123
console.log(str.padEnd(1));//ABC
 
//String.prototype.padStart()
console.log(str.padStart(10, "foo"));//foofoofABC
console.log(str.padStart(6, "12345678"));//123ABC
console.log(str.padStart(1));//ABC
```

## String.prototype.repeat()
- `repeat()`构造并返回一个新字符串，该字符串包含被连接在一起的指定数量的字符串的副本。
### 语法
- `str.repeat(count)`。介于0和正无穷大之间的整数 : [0, +∞) 。表示在新构造的字符串中重复了多少遍原字符串。

```javascript
let str = 'ABC';
console.log(str.repeat(2));//ABCABC
console.log(str.repeat(0));//""
console.log(str.repeat(1.8));//ABC  向下取整
```

## String.prototype.replace()
- `replace()`方法返回一个由替换值替换一些或所有匹配的模式后的新字符串。
### 语法
- `str.replace(regexp|substr, newSubStr|function)`。`regexp`正则表达式。`substr`字符串。`newSubStr`用于替换的字符串。

```javascript
let str = 'ABC';
console.log(str.replace('A', 'a'));//aBC
console.log(str.replace(/./g, 'a'));//aaa
console.log(str.replace(/abc/ig, 'def'));//def
```

## String.prototype.search()
- `search()`方法执行正则表达式和`String`对象之间的一个搜索匹配。
### 语法
- `str.search(regexp)`。`regexp`正则表达式。
- 如果匹配成功，则`search()`返回正则表达式在字符串中首次匹配项的索引;否则，返回-1。

```javascript
let str = "hey JudE";
let re = /[A-Z]/g;
let re2 = /[.]/g;
console.log(str.search(re)); //4
console.log(str.search(re2)); //-1
```

## String.prototype.slice()
- `slice()`方法提取某个字符串的一部分，并返回一个新的字符串，且不会改动原字符串。
### 语法
- `str.slice(beginIndex[, endIndex])`。`beginIndex`从该索引（以 0 为基数）处开始提取原字符串中的字符。`endIndex`在该索引（以 0 为基数）处结束提取字符串。
- `beginIndex`如果值为负数，会被当做`strLength`+`beginIndex`看待，这里的`strLength`是字符串的长度。

```javascript
let str = "abcdefg"; //length=7
console.log(str.slice(4)); //efg [4,6]
console.log(str.slice(4, 5)); //e [4,5)
console.log(str.slice(-4)); //efg   7-4=3 [3,6]
console.log(str.slice(-4, -1)); //def [7-4=3,7-1=6] [3,6)
```

## String.prototype.split()
- `split()`方法使用指定的分隔符字符串将一个`String`对象分割成子字符串数组，以一个指定的分割字串来决定每个拆分的位置。 
### 语法
- `str.split([separator[, limit]])`。`separator`指定表示每个拆分应发生的点的字符串。`separator`可以是一个字符串或正则表达式。`limit`一个整数，限定返回的分割片段数量。
- `beginIndex`如果值为负数，会被当做`strLength`+`beginIndex`看待，这里的`strLength`是字符串的长度。

```javascript
let str = "aBcBdBeBfBq";
console.log(str.split('B')); //[ 'a', 'c', 'd', 'e', 'f', 'q' ]
console.log(str.split(/[B]/ig)); //[ 'a', 'c', 'd', 'e', 'f', 'q' ]
console.log(str.split('B', 2)); //[ 'a', 'c']
 
//移除字符串之间的空格
let names = "Harry Trump ;Fred Barney";
let re = /\s*(?:;|$)\s*/;
console.log(names.split(re));//[ 'Harry Trump', 'Fred Barney' ]
```

## String.prototype.substring()
- 官方建议避免使用`String.prototype.substr()`，未来可能会被移除。
- `substring()`方法返回一个字符串在开始索引到结束索引之间的一个子集, 或从开始索引直到字符串的末尾的一个子集。
### 语法
- `str.substring(indexStart[, indexEnd])`。`indexStart`需要截取的第一个字符的索引，该索引位置的字符作为返回的字符串的首字母。`indexEnd`可选。一个 0 到字符串长度之间的整数，以该数字为索引的字符不包含在截取的字符串内。
- 返回包含给定字符串的指定部分的新字符串。

```javascript
let text = '123456789';//length=9
console.log(text.substring(4));//56789  [4,8]
console.log(text.substring(1, 4));//234  [1,4)
console.log(text.substring(-6, 4));//1234  负值按0处理 [0,4)
console.log(text.substring(-4));//123456789  负值按0处理 [0,8]
console.log(text.substring(3, -6));//123  负值按0处理 [0,3)
```

## String.prototype.toLocaleLowerCase() / String.prototype.toLocaleUpperCase() / String.prototype.toLowerCase() / String.prototype.toUpperCase()
- `toLocaleLowerCase()`方法根据任何指定区域语言环境设置的大小写映射，返回调用字符串被转换为小写的格式。
- `toLocaleUpperCase()`方法根据任何指定区域语言环境设置的大小写映射，返回调用字符串被转换为大写的格式。
- `toLowerCase()`会将调用该方法的字符串值转为小写形式，并返回。
- `toUpperCase()`会将调用该方法的字符串值转为大写形式，并返回。
### 语法
- `str.toLocaleLowerCase()`
- `str.toLocaleUpperCase()`
- `str.toLowerCase()`
- `str.toUpperCase()`

```javascript
//转小写
let text = 'ALPHaBET';
console.log(text.toLocaleLowerCase());//alphabet
console.log(text.toLowerCase());//alphabet
 
//转大写
let text1 = 'alphAbet';
console.log(text1.toLocaleUpperCase());//ALPHABET
console.log(text1.toUpperCase());//ALPHABET
```

## String.prototype.toString()
- `toString()`方法返回指定对象的字符串形式。
### 语法
- `str.toString()`

```javascript
let num = 123;
console.log(num.toString());//'123'
console.log(typeof num);//number
console.log(typeof num.toString());//string
```

## String.prototype.trim() / String.prototype.trimRight() / String.prototype.trimStart()
- `trim()`方法会从一个字符串的两端删除空白字符。
- `trimEnd()`方法从一个字符串的末端移除空白字符。trimRight() 是这个方法的别名。
- `trimStart()`方法从一个字符串的开头移除空白字符。trimLeft() 是这个方法的别名。
### 语法
- `str.trim()`
- `str.trimEnd()` / `str.trimRight()`
- `str.trimStart()` / `str.trimLeft()`

```javascript
let orig = '   foo  ';
console.log(orig.trim()); // 'foo'
console.log(orig.trimStart()); // 'foo  '
 
let orig1 = 'foo    ';
console.log(orig1.trim()); // 'foo'
console.log(orig1.trimEnd()); // 'foo'
```