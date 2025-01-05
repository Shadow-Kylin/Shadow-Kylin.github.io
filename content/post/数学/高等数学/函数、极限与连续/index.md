---
title: 函数、极限与连续
description: 高等数学的函数、极限与连续的笔记
date: 2024-02-03 00:00:00+0000
image: 
categories:
    - Math
tags:
    - 高等数学
    - 函数
    - 极限
    - 连续
draft: false
hidden: false
math: true
weight:
slug:
license: true
---
# 函数、极限与连续

## 数列极限

**数列极限的定义** 如果$\forall\varepsilon{>}0$,$\exists N\in\mathbf{Z}^+$,当 $n>N$ 时,恒有$\mid x_n-a\mid{<}\varepsilon$,则$\lim\limits_{n\to{\infty}}x_n{=}a.$

数列的极限就是数列**收敛**的位置。

**任意的$\varepsilon$** 刻画了$x_n$与$a$的接近程度，对应存在的$N$不是唯一的。从第$N+1$项开始到$\infty$，后续项都位于$(a-\varepsilon,a+\varepsilon)$之间。这也就是说，数列的极限与前N项的变化无关，只与其发展趋势有关。

**定理(数列极限的运算法则)** 若$\lim\limits_{n\to\infty}x_n=a,\lim\limits_{n\to\infty}y_n=b$，则

1. $\lim\limits_{n\to\infty}(x_n\pm y_n)=\lim\limits_{n\to\infty}x_n\pm\lim\limits_{n\to\infty}y_n=a\pm b$(加减法则)
2. $\lim\limits_{n\to\infty}(x_n\cdot y_n)=\lim\limits_{n\to\infty}x_n\cdot\lim\limits_{n\to\infty}y_n=a\cdot b$(乘法法则)
3. $\lim\limits_{n\to\infty}\sqrt{x_{n}}=\sqrt{\lim\limits_{n\to\infty}x_{n}}=\sqrt{a}\left(x_{n}\geqslant0,a\geqslant0\right)$(交换法则)
4. $\mathbf{\lim\limits_{n\to\infty}\frac{x_n}{y_n}=\frac{\lim\limits_{n\to\infty}x_n}{\lim\limits_{n\to\infty}y_n}=\frac{a}{b}(\lim\limits_{n\to\infty}y_n=b\neq0)}$(除法法则)

## 函数极限

**自变量趋于无穷大的函数极限的定义** $\text{如果 }\forall\varepsilon>0\text{, }\exists X>0\text{,当}|x|>X\text{ 时, 恒有}|f(x)-A|<\varepsilon\text{,则}\lim\limits_{x\to\infty}f(x)=A.$

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/0c72d73d3f624057bfdf316832975a69.png)

**定理1** $\lim\limits_{x\to\infty}{f(x)} = A \Leftrightarrow \lim\limits_{x\to+\infty}{f(x)} = A$且$\lim\limits_{x\to-\infty}{f(x)} = A$

一般地，如果$\lim\limits_{x\to+\infty}f(x)=A$或$\lim\limits_{x\to-\infty}f(x)=A$，则称直线$y=A$为函数$y=f(x)$图形的水平渐近线。

**趋于一点时的函数极限的定义** $\forall\varepsilon>0,\text{ }\exists\delta>0,\text{ 当 }0<|x-x_0|<\delta\text{ 时, 恒有 }|f(x)-A|<\varepsilon,\text{那么}\lim\limits_{x\to x_0}f(x)=A.$

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ac751476ac9e4e90928105fff207ba03.png)

位于某个去心领域的自变量映射的因变量也位于某个领域内。

显然有$\lim\limits_{x\to x_{0}}f(x)=A{\Leftrightarrow}f(x_{0}^{+})=f(x_{0}^{-})=A$。

**定理 2 (函数极限的四则运算法则)** 设$\lim\limits_{x\to x_0}{f(x)}=A,\lim\limits_{x\to x_0}{g(x)}=B$

1. $\lim\limits_{x\to x_0}\bigl[f(x)\pm g(x)\bigr]=A\pm B=\lim\limits_{x\to x_0}f(x)\pm\lim\limits_{x\to x_0}g(x);$
2. $\lim\limits_{x\to x_0}[f(x)\cdot g(x)]=A\cdot B=\lim\limits_{x\to x_0}f(x)\cdot\lim\limits_{x\to x_0}g(x);$
3. $\lim\limits_{x\to x_{0}}\frac{f(x)}{g(x)}=\frac{A}{B}=\frac{\lim\limits_{x\to x_{0}}f(x)}{\lim\limits_{x\to x_{0}}g(x)}(B\neq0)$

**推论** 若$\underset{x\to x_0}{\operatorname*{lim}}f(x),\underset{x\to x_0}{\lim\limits}g(x)$存在，则

1. $\lim\limits_{x\to x_{0}}[\alpha f(x)+\beta g(x)]=\alpha\lim\limits_{x\to x_{0}}f(x)+\beta\lim\limits_{x\to x_{0}}g(x);$
2. $\lim\limits_{x\to x_0}\bigl[f(x)\bigr]^n=\bigl[\lim\limits_{x\to x_0}f(x)\bigr]^n(n\in\mathbf{Z}^+);$
3. $\text{若}f(x)\geqslant0,\text{则}\lim\limits_{x\to x_0}\sqrt{f(x)}=\sqrt{\lim\limits_{x\to x_0}f(x)}$

**定理 3(复合函数的极限运算法则)** 设函数$y=f[g(x)]$是由函数$u=g(x)与y=f(u)$复合而成的，$f[g(x)]$在点$x_0$的去心领域内有定义，若$\lim\limits_{x\to x_0}g{(x)}=u_0,\lim\limits_{u\to u_0}f(u)=A$，且存在$\delta_0>0$，当$x\in\mathring{U}(x_0,\delta_0)$时，有$g(x)\neq u_0$，则
$$
\lim\limits_{x\to x_0}f\bigl[g(x)\bigr]=\lim\limits_{u\to u_0}f\bigl(u\bigr)=A.
$$

$g(x)\neq u_0$的作用是说明$f(u)$在$u_0$的去心领域里有定义。

## 极限的性质

**定理 1 (数列极限的唯一性)** 数列$\left\{x_n\right\}$不能收敛于两个不同的极限.

使用反证法结合数列极限的定义可证明。

直观上看，由于$\varepsilon$的任意性，数列的极限像是一个无穷小的领域，它包含了从N开始的后续项，如果数列收敛于两个不同极限，那么它们对应的N是一前一后，前者对应的领域应该包含后者。不会有两个不同的可以无限小的领域存在包含关系，所以就不会收敛于两个不同极限。

**数列${x_n}$有界**是指$\exists M>0$，使$\forall x_n$满足$|x_n| \leqslant M$.

数列的有界和收敛是不同的，*收敛描述数列的发展趋向，有界则指明数列的整体范围*。数列$\{(-1)^n\}$有界，但不收敛。

**定理 2 (收敛数列的有界性）** 如果数列$\{x_n\}$收敛，则该数列一定有界。

设$x_n \to a,\forall \varepsilon > 0,\exists N \in Z^+$,当$n > N$时,$|x_n| = |x_n - a + a| \leqslant |x_n - a| + |a| < \varepsilon + |a|$。取$M = max\{|a_1|,|a_2|,···,|a_N|,\varepsilon + |a|\}$，那么$\forall x_n,|x_n| \leqslant M$。

*数列无界一定发散，数列有界不一定收敛。* 

**定理 3 (收敛级数的保号性)** 如果$x_n \to a(n \to \infty)$且$a > 0(或 a < 0)$，则存在$N > 0$，当$n > N$时，均有$x_n>0$(或$x_n < 0$)。

$a - \varepsilon < x_n < a + \varepsilon$，$a > 0$时只要$\varepsilon < a$即可保证$x_n > 0$，$a < 0$时只要$\varepsilon < -a$即可保证$x_n < 0$。

**推论** 如果${x_n}$满足：$\exists N_1 > 0$，当$n > N_1$时，$x_n \geqslant 0$(或$x_n \leqslant 0$)，且$\lim\limits_{n \to \infty}{x_n} = a$，则$a \geqslant 0$。

在数列$\{x_n\}$中任意抽取*无限项*并保持这些项在原数列中的先后次序，由此得到的一个新数列称为$\{x_n\}$的**子数列(子列)**，记作$\{x_{n_k}\}(n_k \geqslant k)$。

**定理4** 如果数列$\{x_n\}$收敛于$a$，则它的子数列$\{x_{n_k}\}$也收敛于$a$。

由于数列$\{x_n\}$收敛于$a$，则$\forall \varepsilon, \exists N \gt 0$，当$n > N$时，恒有$|x_n - a| < \varepsilon$。

当$k > N$时，$n_k \gt n_N \gt N$，有$|x_{n_k} - a| \lt \varepsilon$。

故数列$\{x_{n_k}\}$也收敛于$a$。

由定理4推理可知，*若数列的两个子数列收敛于不同的极限，那么该数列必定发散*。

且可证明：==若数列$\{x_n\}$的奇数子数列$\{x_{2n+1}\}$和偶数子数列$\{x_{2n}\}$均收敛于$a$，则数列$\{x_n\}$也收敛于$a$==。

$\forall \varepsilon, \exists N_1 \gt 0$，当$odd > N_1$时，恒有$|x_{odd} - a| < \varepsilon$

$\forall \varepsilon, \exists N_2 \gt 0$，当$even > N_2$时，恒有$|x_{even} - a| < \varepsilon$

取$N=max\{N_1,N_2\}$时，当$n>N$时，恒有$|x_{n} - a| < \varepsilon$，即数列$\{x_n\}$也收敛于$a$。

