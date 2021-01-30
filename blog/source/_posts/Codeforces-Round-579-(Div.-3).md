---
title: 	Codeforces Round 579 (Div. 3)
date:   2019-08-14 10:20:00 +0800
tags: [Codeforces]
description: 	Codeforces Round 579 (Div. 3)
---


## A.Circle of Students

签到

```C++

#include <bits/stdc++.h>

using namespace std;

#define N 100010

int n;
int a[N];

bool solve() {
    int p = -1;
    for (int i = 0; i < n; ++i) {
        if (a[i] == 1) {
            p = i;
            break;
        }
    }
    int now = 1;
    for (int i = p, j = 0; j < n; i = (i + 1) % n, ++j) {
        if (a[i] == now) {
            now++;
        } else {
            break;
        }
    }
    if (now == n + 1) {
        return true;
    }
    now = 1;
    for (int i = p, j = 0; j < n; i = (i - 1 + n) % n, ++j) {
        if (a[i] == now) {
            now++;
        } else {
            break;
        }
    }
    return now == n + 1;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        for (int i = 0; i < n; ++i) {
            scanf("%d", a + i);
        }
        puts(solve() ? "YES" : "NO");
    }
    return 0;
}

```

## B.Equal Rectangles

签到

```C++

#include<bits/stdc++.h>

using namespace std;

#define N 500010

typedef long long ll;

int n;
ll a[N];

bool solve() {
    int m = 4 * n;
    sort(a + 1, a + 1 + m);
    if (a[1] != a[2] || a[m] != a[m - 1]) {
        return false;
    }
    ll S = a[1] * a[m];
    for (int i = 3, j = m - 2; i <= 2 * n; i += 2, j -= 2) {
        if (a[i] != a[i + 1] || a[j] != a[j - 1]) {
            return false;
        }
        if (a[i] * a[j] != S) {
            return false;
        }
    }
    return true;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &n);
        for (int i = 1; i <= 4 * n; ++i) {
            scanf("%lld", a + i);
        }
        puts(solve() ? "YES" : "NO");
    }
}

```

## C.Common Divisors

签到

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 400010

int n;
ll a[N];

ll solve() {
    ll res = a[1];
    for (int i = 2; i <= n; ++i) {
        if (a[i] % res != 0) {
            res = __gcd(res, a[i]);
            if (res == 1) {
                return res;
            }
        }
    }
    int tmp = 0;
    for (ll i = 1; i * i <= res; ++i) {
        if (res % i == 0) {
            ++tmp;
            if (res / i != i) {
                ++tmp;
            }
        }
    }
    res = tmp;
    return res;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    while (~scanf("%d", &n)) {
        for (int i = 1; i <= n; ++i) {
            scanf("%lld", a + i);
        }
        printf("%lld\n", solve());
    }
    return 0;
}

```

## D1.Remove the Substring (easy version)

题意：给定$s, t$串，删去$s$的一段区间，使得$t$是$s$的子序列，问最大的区间

思路：正着算一遍$t$每个位置在$s$中第一次满足的下标，倒着算一遍最后满足的下标，答案就是$t_i$和$t_{i+1}$的区间长度的$MAX$

```C++

#include<bits/stdc++.h>

using namespace std;

#define N 500010

typedef long long ll;

char s[N], t[N];
int a[N], b[N];

int solve() {
    memset(a, 0, sizeof a);
    memset(b, 0, sizeof b);
    int len1 = strlen(s + 1), len2 = strlen(t + 1);
    int p = 1;
    for (int i = 1; i <= len1; ++i) {
        if (s[i] == t[p]) {
            a[p] = i;
            ++p;
        }
    }
    p = len2;
    for (int i = len1; i >= 1; --i) {
        if (s[i] == t[p]) {
            b[p] = i;
            --p;
        }
    }
    int res = 0;
    res = max(res, max(len1 - a[len2], b[1] - 1));
    for (int i = 1; i <= len2 - 1; ++i) {
        res = max(res, b[i + 1] - a[i] - 1);
    }
    return res;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    while (~scanf("%s", s + 1)) {
        scanf("%s", t + 1);
        printf("%d\n", solve());
    }
}

```

## D2.Remove the Substring (hard version)

题意：给定$s, t$串，删去$s$的一段区间，使得$t$是$s$的子序列，问最大的区间

思路：正着算一遍$t$每个位置在$s$中第一次满足的下标，倒着算一遍最后满足的下标，答案就是$t_i$和$t_{i+1}$的区间长度的$MAX$

```C++

#include<bits/stdc++.h>

using namespace std;

#define N 500010

typedef long long ll;

char s[N], t[N];
int a[N], b[N];

int solve() {
    memset(a, 0, sizeof a);
    memset(b, 0, sizeof b);
    int len1 = strlen(s + 1), len2 = strlen(t + 1);
    int p = 1;
    for (int i = 1; i <= len1; ++i) {
        if (s[i] == t[p]) {
            a[p] = i;
            ++p;
        }
    }
    p = len2;
    for (int i = len1; i >= 1; --i) {
        if (s[i] == t[p]) {
            b[p] = i;
            --p;
        }
    }
    int res = 0;
    res = max(res, max(len1 - a[len2], b[1] - 1));
    for (int i = 1; i <= len2 - 1; ++i) {
        res = max(res, b[i + 1] - a[i] - 1);
    }
    return res;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    while (~scanf("%s", s + 1)) {
        scanf("%s", t + 1);
        printf("%d\n", solve());
    }
}

```

## E.Boxers

题意：每个数可以进行$+1,-1$以及不变，问变化后序列中拥有的不同元素个数

思路：对于每个数，优先$-1$再不变在$+1$即可。

```C++

#include<bits/stdc++.h>

using namespace std;

#define N 500010

typedef long long ll;

int n;
int a[N];
int cnt[N];

int solve() {
    memset(cnt, 0, sizeof cnt);
    sort(a + 1, a + 1 + n);
    int res = 0;
    for (int i = 1; i <= n; ++i) {
        if (a[i] - 1 >= 1 && !cnt[a[i] - 1]) {
            cnt[a[i] - 1] = 1;
            res++;
        } else if (!cnt[a[i]]) {
            cnt[a[i]] = 1;
            res++;
        } else if (!cnt[a[i] + 1]) {
            cnt[a[i] + 1] = 1;
            res++;
        }
    }
    return res;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    while (~scanf("%d", &n)) {
        for (int i = 1; i <= n; ++i) {
            scanf("%d", a + i);
        }
        printf("%d\n", solve());
    }
}

```

## F1.Complete the Projects (easy version)

题意：有$n$件事，每件事有$a, b$表示当你的$Rank$大于等于$a$的时候才能做，做完后$Rank$变化$b$，其中$Rank$不能为负，现在有一个初始$Rank$问能否做完所有事情。

思路：优先做$b$非负的事情，对于$b$为负的，按照$a+b$排序，模拟一边即可。

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 110

struct node {
    ll a, b, vis;

    void input() {
        scanf("%lld %lld", &a, &b);
        vis = 0;
    }
} a[N];

int n, r;

bool cmp1(node x, node y) {
    return x.a < y.a;
}

bool cmp2(node x, node y) {
    return x.a + x.b > y.a + y.b;
}

bool solve() {
    int tr = r;
    sort(a + 1, a + 1 + n, cmp1);
    for (int i = 1; i <= n; ++i) {
        if (a[i].b >= 0) {
            if (a[i].a <= tr) {
                tr += a[i].b;
                a[i].vis = 1;
            }
        }
    }
    sort(a + 1, a + 1 + n, cmp2);
    for (int i = 1; i <= n; ++i) {
        if (a[i].vis) continue;
        if (a[i].a <= tr && tr + a[i].b >= 0) {
            tr += a[i].b;
            a[i].vis = 1;
        }
    }
    for (int i = 1; i <= n; ++i) {
        if (!a[i].vis) {
            return false;
        }
    }
    return true;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    while (~scanf("%d %d", &n, &r)) {
        for (int i = 1; i <= n; ++i) {
            a[i].input();
        }
        puts(solve() ? "YES" : "NO");
    }
    return 0;
}

```

## F2.Complete the Projects (hard version)

题意：有$n$件事，每件事有$a, b$表示当你的$Rank$大于等于$a$的时候才能做，做完后$Rank$变化$b$，其中$Rank$不能为负，现在有一个初始$Rank$问能否做的最大数量。

思路：优先做$b$非负的事情，对于$b$为负的，按照$a+b$排序，进行$01$背包

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 110
#define M 60010
#define INF 0x3f3f3f3f

struct node {
    ll a, b, vis;

    void input() {
        scanf("%lld %lld", &a, &b);
        vis = 0;
    }
     
    bool operator<(const node &other) const {
        return a < other.a;
    }
} a[N];

bool cmp(node x, node y) {
    return x.a + x.b > y.a + y.b;
}

int n, r;
int dp[M];

int solve() {
    sort(a + 1, a + 1 + n);
    memset(dp, -INF, sizeof dp);
    int tr = r;
    int res = 0;
    for (int i = 1; i <= n; ++i) {
        if (a[i].b >= 0) {
            if (a[i].a <= tr) {
                tr += a[i].b;
                a[i].vis = 1;
                res++;
            }
        }
    }
    sort(a + 1, a + 1 + n, cmp);
    dp[tr] = res;
    int ans = res;
    for (int i = 1; i <= n; ++i) {
        if (a[i].b >= 0) continue;
        for (int j = 0; j <= tr; ++j) {
            if (dp[j] == -INF) continue;
            if (a[i].a <= j && j + a[i].b >= 0) {
                int tmp = j + a[i].b;
                dp[tmp] = max(dp[tmp], dp[j] + 1);
                ans = max(ans, dp[tmp]);
            }
        }
    }
    return ans;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    while (~scanf("%d %d", &n, &r)) {
        for (int i = 1; i <= n; ++i) {
            a[i].input();
        }
        printf("%d\n", solve());
    }
    return 0;
}

```

