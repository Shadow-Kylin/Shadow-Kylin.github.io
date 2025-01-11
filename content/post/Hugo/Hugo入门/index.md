---
title: Hugo 入门
description: Hugo 客户端环境的搭建、主题的使用
slug: 
date: 2025-01-04 01:33:00+0800
image: 
categories:
    - Hugo
tags:
    - Stack
# weight: 1       # You can add weight to some posts to override the default sorting (date descending)
draft: false
hidden: false
math: false
---
# Hugo 环境准备
## 安装 Git（必需）
### Windows
下载 [Git for Windows](https://git-scm.com/) 安装包。
按提示安装，默认设置即可。
安装完成后，打开命令行，验证安装：
```bash
git --version
```
### macOS
### Linux
## 安装 Go（可选）
Hugo 使用 Go 语言开发，但使用 Hugo 并不要求安装 Go，因为大多数用户直接下载 Hugo 的预编译二进制文件即可。
如果需要自行编译 Hugo 或使用 extended 版本，则需要安装 Go。

Go is required to:
- Build Hugo from source
- Use the Hugo Modules feature
### Windows
下载 [Go 官方安装包](https://go.dev/doc/install)。
按提示安装。
验证安装：
```bash
go version
```
### macOS
### Linux
## 安装 Hugo
Hugo is available in three editions: standard, extended, and extended/deploy.
### 安装 Hugo 预编译二进制版本
1. 下载 Hugo 二进制文件：
   - 访问 [Hugo Releases](https://github.com/gohugoio/hugo/releases) 页面。
   - 下载适合的 .zip 文件（通常选择不带 extended 的版本，除非需要 SCSS 支持）。
2. 解压文件，将 hugo.exe 添加到系统环境变量的 PATH 中。
   - 测试是否安装成功：打开命令行，运行 `hugo version`。
### 通过包管理器
### 从源代码构建
# 创建 Hugo 站点

# 使用 Hugo 主题
一般使用 Hugo 主题的步骤为：
```bash
# 使用 Git 克隆主题
git submodule add https://github.com/主题作者/主题仓库路径.git themes/主题名称
# 修改配置文件 config.toml，指定主题
theme = "主题名称"
# 根据主题来进行个性化配置
```
有些主题也提供了快捷模板，如下。
## Stack 主题
Documentation: https://stack.jimmycai.com/

QuickStart：Use this template: [CaiJimmy/hugo-theme-stack-starter](https://github.com/CaiJimmy/hugo-theme-stack-starter)
### 开启评论
Hugo ships with support for Disqus, a third-party service that provides comment and community capabilities to websites via JavaScript.

Alternative:
1. [waline](https://waline.js.org/guide/get-started/)
2. 