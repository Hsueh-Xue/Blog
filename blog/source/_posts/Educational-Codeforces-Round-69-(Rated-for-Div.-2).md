---
title: 	Educational Codeforces Round 69 (Rated for Div. 2)
date:   2019-07-23 14:20:00 +0800
tags: [Codeforces]
description: Educational Codeforces Round 69 (Rated for Div. 2)
---

## A. DIY Wooden Ladder

签到。

```C++

#include <bits/stdc++.h>

using namespace std;

#define N 100010

int n;
int a[N];

int main() {
//    freopen("input.txt", "r", stdin);
    int t;
    scanf("%d", &t);
    while (t--) {
        scanf("%d", &n);
        for (int i = 1; i <= n; ++i) {
            scanf("%d", a + i);
        }
        sort(a + 1, a + 1 + n);
        int ans = n - 2;
        ans = min(ans, a[n] - 1);
        ans = min(ans, a[n - 1] - 1);
        printf("%d\n", ans);
    }
    return 0;
}
```


## B.Pillars

题意：有$n$根柱子，每根柱子上有一个磁盘，每个磁盘都有自己的半径，第$i$根柱子上的磁盘能移动到第$j$根柱子上的前提是：

- $i$和$j$相邻
- 第$i$根柱子上只有一个磁盘
- 第$j$根柱子没有磁盘或者顶部磁盘半径大于第$i$根柱子

问能否将所有磁盘移动到一根柱子上。

思路：很显然只能先递增后递减的形式。

```C++

#include <bits/stdc++.h>

using namespace std;

#define N 200010

int n;
int a[N];

bool solve() {
    int Max = 0, pos = 0;
    for (int i = 1; i <= n; ++i) {
        if (a[i] > Max) {
            Max = a[i];
            pos = i;
        }
    }
    for (int i = 2; i <= pos; ++i) {
        if (a[i] < a[i - 1]) {
            return false;
        }
    }
    for (int i = pos; i < n; ++i) {
        if (a[i + 1] > a[i]) {
            return false;
        }
    }
    return true;
}

int main() {
//    freopen("input.txt", "r", stdin);
    while (scanf("%d", &n) != EOF) {
        for (int i = 1; i <= n; ++i) {
            scanf("%d", a + i);
        }
        puts(solve() ? "YES" : "NO");
    }
    return 0;
}

```



## C.Array Splitting

题意：有一个长度为$n$的序列$a$，将序列分成$k$段，使得$\sum_{i=1}^{k}(max(i)-min(i))$最小，其中$max(i)$表示第$i$段最大值，$min(i)$表示第$i$段最小值，且保证$a_i$非降序。

思路：$\sum_{i=1}^k (max(i)-min(i))=a_n-a_1+\sum_{i=1}^{k-1} a_{b_i}-a_{b_i+1} $其中$b_i$表示第$i$段末尾下标，很显然贪心选取$k-1$个即可。

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
#define INF 0x3f3f3f
#define N 300010

int n, k;
ll a[N];
vector<ll> vec;

int main() {
//    freopen("input.txt", "r", stdin);
    while (scanf("%d %d", &n, &k) != EOF) {
        vec.clear();
        for (int i = 1; i <= n; ++i) {
            scanf("%lld", a + i);
            if (i != 1) {
                vec.push_back(a[i] - a[i - 1]);
            }
        }
        sort(vec.begin(), vec.end());
        ll ans = a[n] - a[1];
        for (int len = vec.size(), i = len - 1; i >= len - k + 1; --i) {
            ans -= vec[i];
        }
        printf("%lld\n", ans);
    }
    return 0;
}

```



## D.Yet Another Subarray Problem

题意：给出一个长度为$n$的序列$a$，以及$m,k$，求$\sum_{i=l}^{r}a_i-k\lceil \frac{r-l+1}{m}\rceil$的最大值

思路：$dp[i]$表示到$i$节点作为末尾的最大值，对于每个节点$i$很显然只能选取$i$前面不到$m$个数，或者选满$m$个数后再加上$dp[i-m]$，由此转移下去去中间值最大值即可。

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
#define INF 0x3f3f3f
#define N 300010

int n, m, k;
ll a[N];
ll dp[N];
ll ans;


int main() {
//    freopen("input.txt", "r", stdin);
    while (scanf("%d %d %d", &n, &m, &k) != EOF) {
        for (int i = 1; i <= n; ++i) {
            scanf("%lld", a + i);
        }
        memset(dp, 0, sizeof dp);
        ans = 0;
        for (int i = 1; i <= n; ++i) {
            ll Max = a[i], sum = 0;
            for (int j = i; j >= 1 && j >= i - m + 1; --j) {
                sum += a[j];
                Max = max(Max, sum);
            }
            dp[i] = max(dp[i], max(Max - k, sum - k + dp[max(i - m, 0)]));
            ans = max(ans, dp[i]);
        }
        printf("%lld\n", ans);
    }
    return 0;
}

```

## E.Culture Code

题意：有$n$个俄罗斯套娃，每个套娃有两个属性$in,out(out>in)$，套娃$i$能放在$j$对的前提是$out_i<=in_j$。

- 定义一组套娃的额外空间为$in_{i_1}+(in_{i_2}-out_{i_1})+(in_{i_3}-out_{i_2})+\cdots+(in_{i_k}-out_{i_{k-1}})$，其中$i_1,i_2,\cdots,i_k$为这组套娃的下标。
- 定义一组套娃是个极大套娃组：当且仅当不能再有另外一个套娃套在他们身上了。

询问有多少种极大套娃组的额外空间最小

思路：$f[i]$表示使用$i$的最小额外空间，$g[i]$为使用$i$的方案数。对于每个套娃，比它大的最小的俄罗斯套娃。如果和$i+1$套娃的额外空间相同，则加上$i+1$的方案数，如果比$i+1$套娃的额外空间大，则舍弃当前套娃的方案数。

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;
typedef pair<ll, ll> pll;

#define INFLL 0x3f3f3f3f3f3f3f
#define N 400010

const ll p = (ll) 1e9 + 7;


int n;
pll a[N];
ll f[N], g[N];

void add(ll &x, ll y) {
    x += y;
    if (x >= p) {
        x -= p;
    }
}

int main() {
//    freopen("input.txt", "r", stdin);
    while (scanf("%d", &n) != EOF) {
        for (int i = 1; i <= n; ++i) {
            scanf("%lld %lld", &a[i].second, &a[i].first);
        }
        sort(a + 1, a + 1 + n);
        f[n + 1] = INFLL;
        for (int i = n; i >= 1; --i) {
            int pos = lower_bound(a + i, a + 1 + n, pll(a[i].second, -1)) - a;
            if (pos == n + 1) {
                f[i] = a[i].first;
                g[i] = 1ll;
            } else {
                f[i] = a[i].first - a[i].second + f[pos];
                g[i] = g[pos];
            }
            if (f[i] == f[i + 1]) {
                add(g[i], g[i + 1]);
            } else if (f[i] > f[i + 1]) {
                f[i] = f[i + 1];
                g[i] = g[i + 1];
            }
        }
        printf("%lld\n", g[1]);
    }
    return 0;
}

```