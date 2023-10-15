---
title: 【JavaScript】字符串方法
description: 
slug: 
date: 2023-08-24
image: 
categories:
    - Frontend
tags:
    - JavaScript
---
# JavaScript 字符串简介
JavaScript中的字符串是由**16位码元code unit**组成。

通常来说，一个字符=16位码元，该类字符也叫做**单码元字符**。

还有一种字符组成策略是**代理对**，它由两对16位码元组成，即一个字符对应两个16位码元，用于增补字符。

JavaScript使用两种Unicode编码混合的策略：**USC-2**和**UTF-16**。

> 深入了解字符编码
>
> [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)
>
> [JavaScript’s Internal Character Encoding:UCS-2 or UTF-16?](https://mathiasbynens.be/notes/javascript-encoding)

字符串原始值可以调用所有String对象的方法；有3个继承的方法：`valueOf()`、`toLocaleString()`、`toString()`，它们都返回字符串对象的原始字符串值；有1个length属性，其值为字符串所包含的字符数量。

下面介绍JavaScript字符串的相关方法。

# 获取指定位置上的字符、编码、码点

`charAt`、`charCodeAt`、`codePointAt`

`charAt`获取指定位置字符。

`charCodeAt`获取指定位置字符的编码（16进制变成10进制）。

```javascript
const str="Hello,世界!";
console.log(str.charAt(0)); //H
console.log(str.charAt(7)); //界
console.log(str.charCodeAt(0)); //72
console.log(str.charCodeAt(7)); //30028
console.log(parseInt(30028).toString(16)); //754c
```

`charCodeAt`只适合单码元字符，而代理对字符需要使用`codePointAt`。

为了正确解析既包含单码元字符又包含代理对字符的字符串，使用`codePointAt`接收16位码元的索引并返回该索引位置上的**码点**（`Unicode`中一个字符的完整标识）。

```javascript
const str = "ab😂ba";
console.log(str.charCodeAt(2)); // 55357
console.log(str.codePointAt(2)); // 128514
```

# fromCharCode、fromCodePoint

`fromCharCode`用于根据给定的UTF-16码元创建字符，可以接受多个数值，生成一个字符串。fromCodePoint用给定码点创建字符，同样可接受多个值生成一个字符串。

```javascript
// 使用 fromCharCode() 创建字符
const char1 = String.fromCharCode(65); // 65 是字符 'A' 的UTF-16码元
console.log(char1); // 输出：A

// 使用 fromCodePoint() 创建字符
const char2 = String.fromCodePoint(128514); // 128514 是笑脸符号的码点
console.log(char2); // 输出：😂
```

**两个一样的字符一定相等吗**?

如果这两个字符看着一样，但是对应的字符编码不一样，那么便不相等，例如:

```javascript
const char1 = 'A';   // 拉丁字母大写A
const char2 = 'Α';  // 希腊字母大写Alpha

// 比较两个字符是否相等
console.log(char1 === char2); // 输出：false，尽管外观相似，但字符编码不同

// 获取字符编码
console.log(char1.charCodeAt(0)); // 输出：65，字符A的Unicode编码
console.log(char2.charCodeAt(0)); // 输出：913，字符Α的Unicode编码
```
# concat

`concat`用于拼接字符串，可以一次性拼接多个字符串，当然我们更常使用 + 号来拼接字符串。

```javascript
let str="Hello "
console.log(str.concat("World","!")) //"Hello World!"
console.log(str) //"Hello"
```
# 截取子字符串

这三个方法有一定相似，但也有重大不同，且看下文。

它们都用于提取子字符串，都接受1或2个参数，且第一个参数都是起始位置的索引，如果省略第二个参数，表示截取到字符串末尾。

不同的地方是，`slice`和`substring`第二个参数是结束位置的索引（截取部分不包括该结束位置），而`substr`第二个参数是从起始位置截取字符串的字符个数。

值得注意的是，`substring`它会将较小的数字当成起始位置，可能会出现第一参数大于第二参数的情况。其他两个方法如果第一个参数小于第二个参数，就会返回空字符串。

对于负数参数，`slice`会将它们都加上字符串长度（即从右往左数），直到为正数；`substr`会将第一个负参加上字符串长度，将第二个负参变为`0`；`substring`将所有负参转为`0`。总而言之，只有`slice`的负参数才有作用，`substr`和`substring`包含负参数就一定会返回空字符串。

```javascript
let str = "Hello world!"

console.log(str.slice(0)) //Hello world!
console.log(str.slice(0, 5)) //Hello

console.log(str.substring(0)) //Hello world!
console.log(str.substring(0, 5)) //Hello

console.log(str.substr(0)) //Hello world!
console.log(str.substr(0, 5)) //Hello

console.log(str.slice(-5)) //orld!
console.log(str.substring(-5)) //Hello world!
console.log(str.substr(-5)) //orld!

console.log(str.slice(0, -5)) //Hello w
console.log(str.substring(0, -5)) //空字符串
console.log(str.substr(0, -5)) //空字符串

console.log(str.slice(-5, -2)) //orl
console.log(str.substring(-5, -2)) //空字符串
console.log(str.substr(-5, -2)) //空字符串

console.log(str.slice(-2, -5)) //空字符串
console.log(str.substring(-2, -5)) //空字符串
console.log(str.substr(-2, -5)) //空字符串

console.log(str.slice(5, 0)) //空字符串
console.log(str.substring(5, 0)) //Hello
console.log(str.substr(5, 0)) //空字符串
```
# 获取字符的位置

前文已经介绍了根据位置获取字符串的方法，我们也可以根据字符串获取它的位置——`indexOf`和`lastIndexOf`，它们区别在于`indexOf`是从前往后找，而`lastIndexOf`是从后往前找。如果没有找到，则返回`-1`。这两个方法可以接受第二参数，表示开始搜索位置。
# `startsWith`、`endsWith`、`includes`

`startsWith`、`endsWith`、`includes`都是判断是否包含某字符串的方法，返回布尔值。

`includes`检查整个字符串。

`startsWith`和`endsWith`分别是匹配开头和结尾是否有传入字符串。

`startsWith`和`includes`都接受第二个参数，表示起始搜索位置。
# 去除左边或右边空格

`trim`、`trimLeft`、`trimRight`

`trim`方法删除字符串前后所有空格。

`trimLeft`、`trimRight`分别删除字符串左边空格和字符串右边空格。不过这两个方法好像在某个版本 deprecated 啦。

```javascript
let s = " abc ";
console.log("s.trim()=" + s.trim() + "|");
console.log("s.trimLeft()="+s.trimLeft()+"|");
console.log("s.trimRight()=" + "|" + s.trimRight());
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/da595700ec744912920f120b8f51cd25.png)

# 重复字符串

`repeat`方法接受一个整数作为参数，表示字符串复制的次数，然后返回它们拼接起来的字符串。

```javascript
let s = "a";
console.log(s.repeat(3)); // "aaa"
```

不改变原字符串。
# 填充字符串

`padStart`和`padEnd`分别在字符串前面和字符串后面填充指定长度的指定字符串。

第一参数是填充后字符串要达到的长度，第二参数是要填充的字符串。

```javascript
let str = "Hello"
console.log(str.padStart(10, "World")) //WorldHello
console.log(str.padEnd(10, "World")) //HelloWorld
```
# 大小写转换

`toLowerCase`用于将字符串中的所有字符转换为小写，它不受地域设置的影响，只是简单地将字符转换为小写形式。`toLocaleLowerCase()`不同之处在于，它会受到地域设置的影响，可能会根据当前区域设置将某些特定字符转换为小写形式。

`toUpperCase`、`toLocaleUpperCase`同上。
# 模式匹配相关方法

第一个是`match`，它本质上和`RegExp`的`exec`方法相同，返回匹配结果的数组形式。如果使用全局模式`g`，它会返回所有匹配结果的数组，否则返回一个数组，其中第一个元素是匹配到的字符串，后续元素是正则表达式中的捕获组。

```javascript
let str = "To be what we are, and to become what we are capable of becoming, is the only end of life."
// 使用match匹配所有包含a的单词
let reg1 = /\b[a-zA-Z]*a[a-zA-Z]*\b/g
console.log(str.match(reg1)) // [ 'what', 'are', 'and', 'what', 'are', 'capable' ]

// 使用match匹配包含a的单词，不使用全局匹配
let reg2 = /\b[a-zA-Z]*a[a-zA-Z]*\b/
console.log(str.match(reg2))
// [
//   'what',
//   index: 6,
//   input: 'To be what we are, and to become what we are capable of becoming, is the only end of life.',
//   groups: undefined
// ]
```

第二个是`search`，返回匹配到的索引位置，没有匹配项就返回`-1`。

```javascript
let str = "To be what we are, and to become what we are capable of becoming, is the only end of life."

// 使用search匹配we
let reg = /we/g
console.log(str.search(reg)) // 11
```

第三个是`replace`，它使用新的字符串或函数替换正则表达式匹配的内容。

```javascript
const originalString = "Hello, world! Hello, universe!";

// 替换字符串中的单个匹配项
const replacedString1 = originalString.replace("world", "friends");
console.log(replacedString1); // 输出：Hello, friends! Hello, universe!

// 使用正则表达式进行替换（只替换第一个匹配项）
const replacedString2 = originalString.replace(/Hello/, "Hi");
console.log(replacedString2); // 输出：Hi, world! Hello, universe!

// 使用正则表达式进行替换（替换所有匹配项）
const replacedString3 = originalString.replace(/Hello/g, "Hi");
console.log(replacedString3); // 输出：Hi, world! Hi, universe!

// 使用$1、$2、$3、$4等来替换匹配项
const replacedString4 = originalString.replace(/(Hello), (world)! (Hello), (universe)!/g, "$3, $4! $1, $2!");
console.log(replacedString4); // 输出：Hello, universe! Hello, world!

// 使用函数来替换匹配项
const replacedString5 = originalString.replace(/(Hello), (world)! (Hello), (universe)!/g, 
function (matched, p1, p2, p3, p4, offset, original) {
    console.log(matched) // 匹配到的字符串
    console.log(offset) // 匹配到的字符串在原字符串中的偏移量
    console.log(original) // 原字符串
    return p3 + ", " + p4 + "! " + p1 + ", " + p2 + "!";
}
);
console.log(replacedString5); // 输出：Hello, universe! Hello, world!
```

最后一个是`split`，它根据传入的分隔符将字符串划分为一个数组，分隔符可以是字符串或`RegExp`对象。

```javascript
const str="apple,banana,orange"
//以逗号作为分隔符
console.log(str.split(",")) //["apple","banana","orange"]
//限定数组大小
console.log(str.split(",",2)) //["apple","banana"]
//使用正则表达式
const text="apple   orange\tbanana"
const parts=text.split(/\s+/) //匹配连续空格
console.log(parts) //["apple","orange","banana"]
```
# 迭代方法

字符串的原型上暴露了`@@iterator`方法

```javascript
const str = "abc"
const str2 = str[Symbol.iterator]()
console.log(str2.next()) //{ value: 'a', done: false }
console.log(str2.next()) //{ value: 'b', done: false }
console.log(str2.next()) //{ value: 'c', done: false }
console.log(str2.next()) //{ value: undefined, done: true }
```

通过该迭代器，我们使用`for-of`遍历字符串，以及使用解构操作符结构字符串。
# localeCompare

`localeCompare`比较字符串与字符串参数在字母表的顺序前后，如果字符串位于字符串参数前，则返回负值；如果相等，则返回0；如果字符串位于字符串参数后，则返回正值。