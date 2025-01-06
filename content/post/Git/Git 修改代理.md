---
title: 配置 Git 代理
description: Git 代理的配置、验证与取消
date: 2025-01-06 10:01:00+0800
image: 
categories:
    - Git
tags:
    - Git
draft: 
hidden: 
math: 
weight:
slug:
license: true
---
# 设置代理
```bash
git config --global http.proxy http://your_proxy:port
git config --global https.proxy http://your_proxy:port
```
# 验证设置
```bash
git config --global --get http.proxy
git config --global --get https.proxy
```
# 取消代理
```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```