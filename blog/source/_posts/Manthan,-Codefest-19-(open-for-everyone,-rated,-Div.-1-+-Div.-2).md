---
title: Manthan, Codefest 19 (open for everyone, rated, Div. 1 + Div. 2)
date:   2019-08-26 09:30:00 +0800
tags: [ACM]
description: Manthan, Codefest 19 (open for everyone, rated, Div. 1 + Div. 2)
---


## A. XORinacci

签到

## B. Uniqueness

题意：删除一个最短的子串，使得剩下的数字唯一出现

思路：枚举左端点，右边不断向左扩展。

## C. Magic Grid

题意：构造一个$n\cdot n$的矩阵，里面填入$0-n^2-1$，使得每行每列异或和都相同。

思路：对于每个$4\cdot 4$的矩阵都构造出


$$
  \begin{matrix}
   0 & 1 & 2 & 3 \\
  4 & 5 & 6 & 7 \\
  8 & 9 & 10 & 11\\
  12 & 13 & 14 & 15
  \end{matrix}
$$


## D. Restore Permutation

题意：给出一个序列，$a_i$ 的权值是满足$j<i \& a_j<a_i$的$a_j$和，现在给出权值，问原序列

思路：倒过来求，在权值线段树上二分

## E. Let Them Slide

题意：有$n$个滑块，滑块长度不同，每个滑块第$i$列有权值，单独求每列的和的最大值。

思路：$multiset$维护第$i$列可选取的滑块，维护即可。