---
title: The Preliminary Contest for ICPC Asia Xuzhou 2019
date:  2019-09-07 22:00:00 +0800
tags: [online]
description: The Preliminary Contest for ICPC Asia Xuzhou 2019
---


## A. Who is better?

$CRT+Fibonacci$博弈

## B. so easy

题意：两种操作

- 将$x$设置为不可用
- 查询$x$之后第一个可用的

思路：

离线哈希后并查集维护。

## C. Buy Watermelon

**题

## D. Carneginon

分类讨论+$KMP$

## E. XKC's basketball team

维护一个单调栈，权值递增，下标递减。二分

## F. Little M's attack plan

## G. Colorful String

$PAM$

## H. function

## I. query

树状数组统计二分偏序的个数

## J. Random Access Iterator

树上概率$dp$， $f[u]$表示选了$u$节点能到达最大深度的概率

令深度最大的叶子节点$f[u]=1$，其他叶子节点为$f[u]=0$

那么$f[u]=(\sum f[v])^{sze[u]}$

答案为$1-f[1]$

## K. Center

## L. Dice

打表找规律后状压$DP$

## M. Longest subsequence

序列自动机匹配，每次找大于等于的。