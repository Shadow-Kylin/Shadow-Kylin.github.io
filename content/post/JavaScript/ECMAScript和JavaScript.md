---
title: ECMAScript 和 JavaScript
description: 
date: 2025-01-10 15:06:00+0800
image: 
categories:
    - Frontend
tags:
    - JavaScript
draft: 
hidden: 
math: 
weight:
slug:
license: true
---

# 脚本语言

脚本语言是一种解释型编程语言，通常用于控制已有的系统、应用程序或环境（如浏览器、操作系统、游戏引擎）的行为，而不是独立构建一个完整的程序。

常见脚本语言及其用途：

1. Web 开发脚本：
    - JavaScript：用于浏览器中的动态网页。
    - PHP：用于服务器端动态网页生成。
2. 通用脚本：
    - Python：广泛应用于数据科学、自动化和服务器端编程。
    - Ruby：用于开发 Web 应用和脚本任务。
3. 系统脚本：
    - Bash 和 Shell：在 Unix/Linux 系统中，用于任务自动化和脚本控制。
    - PowerShell：Windows 系统管理工具。
4. 嵌入式脚本：
    - Lua：嵌入到游戏引擎（如 Unity）或应用中。
    - Tcl：嵌入式脚本工具。
5. 其他：
    - Perl：擅长文本处理和脚本任务。
    - R：数据分析和统计建模。


# ECMAScript 和 JavaScript 的关系

JavaScript 由 Netscape 公司开发，最早在 1995 年发布，它诞生时是一个具体的实现，没有标准化的语言规范。

1997 年，一种标准化的脚本语言规范 ECMAScript 第一版发布，提供了一套语言的核心特性和语法规则，供脚本语言实现和扩展。

ECMAScript 由 ECMA 国际（ECMA International） 通过其 ECMA-262 标准定义。

可以说，ECMAScript 的出现是为了规范化 JavaScript，使其成为一个可以跨平台和跨浏览器统一的脚本语言。

后来，JavaScript 经过不断发展，不仅实现了 ECMAScript 的规范，还包含了许多 Web 环境相关的特性，例如 DOM 操作和与浏览器的交互 API。JavaScript 已经可以用于开发 Web 应用、服务端程序（Node.js）、桌面应用和移动应用。

其它 ECMAScript 实现还包括 JScript（微软）和 ActionScript（Adobe）。

总结：ECMAScript 是规范，是理论层面的标准，JavaScript 是 ECMAScript 的具体实现和应用。

# ECMAScript 的内容

ECMAScript 仅定义了脚本语言的核心功能和语法规则，包括：

1. 数据类型
    - 基本类型：undefined, null, boolean, number, bigint, string, symbol。
    - 复杂类型：Object（包括数组、函数等）。
2. 语法规则
    - 变量声明：var, let, const。
    - 控制结构：if, for, while 等。
    - 运算符：算术、比较、逻辑、位运算等。
3. 内置对象
    - 常见对象：Object, Array, Function, Date, RegExp, Map, Set 等。
    - 工具对象：Math, JSON, Promise 等。
4. 函数
    - 函数声明与表达式、箭头函数、默认参数、剩余参数。
5. 类与对象
    - 对象字面量、原型链。
    - 类和继承（从 ES6 引入）。
6. 模块化
    - 导入和导出：import/export（从 ES6 引入）。
7. 异步编程
    - Promise 和 async/await。
8. 迭代器与生成器
    - 可迭代协议、for...of。
    - 生成器函数（function*）。
9.  内存管理
    - 垃圾回收（自动回收未引用的对象）。

