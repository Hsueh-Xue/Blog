---
title: 	Educational Codeforces Round 70 (Rated for Div. 2)
date:   2019-08-09 10:20:00 +0800
tags: [Codeforces]
description: Educational Codeforces Round 70 (Rated for Div. 2)
---


## A. You Are Given Two Binary Strings...

签到

```C++

#include <bits/stdc++.h>


using namespace std;

typedef long long ll;

#define N 500010


char x[N], y[N];

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%s", x + 1);
        scanf("%s", y + 1);
        int cnt1 = 0;
        for (int i = strlen(y + 1); i >= 1; --i) {
            if (y[i] != '0') {
                break;
            }
            cnt1++;
        }
        int res = 0;
        for (int i = strlen(x + 1) - cnt1; i >= 1; --i) {
            if (x[i] != '0') {
                break;
            } else {
                res++;
            }
        }
        printf("%d\n", res);
    }
    return 0;
}

```

## B. You Are Given a Decimal String...

题意：给定一个$x-y$计数器，每次只能加$x$或者$y$，且每次输出个位，现在有一个缺失了的打印结果，问对于一个$x-y(0\leq x\leq 9, 0\leq y\leq0)$最少的填充字符数

思路：预处理$x-y$计数器的$i-j(0\leq i\leq9,0\leq j\leq 9)$从$i$到达$j$的最小填充数字。

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 2000010
#define INF 0x3f3f3f3f

char s[N];
ll ans[20][20];
int a[20][20][20][20];

void Init() {
    memset(a, 0x3f, sizeof a);
    for (int x = 0; x < 10; ++x) {
        for (int y = 0; y < 10; ++y) {
            for (int o1 = 0; o1 < 10; ++o1) {
                for (int o2 = 0; o2 < 10; ++o2) {
                    for (int i = 0; i < 100; ++i) {
                        for (int j = 0; j < 100; ++j) {
                            if (i == 0 && j == 0) {
                                continue;
                            }
                            if ((o1 + i * x + j * y) % 10 == o2) {
                                a[x][y][o1][o2] = min(a[x][y][o1][o2], i + j);
                            }
                        }
                    }
                }
            }
        }
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    Init();
    while (scanf("%s", s + 1) != EOF) {
        int len = strlen(s + 1);
        for (int x = 0; x < 10; ++x) {
            for (int y = 0; y < 10; ++y) {
                ans[x][y] = 0;
                if (len == 1) {
                    continue;
                }
                for (int i = 2; i <= len; ++i) {
                    int c1 = s[i] - '0', c2 = s[i - 1] - '0';
                    if (a[x][y][c2][c1] == INF) {
                        ans[x][y] = -1;
                        break;
                    } else {
                        ans[x][y] += a[x][y][c2][c1] - 1;
                    }
                }
            }
        }
        for (int x = 0; x < 10; ++x) {
            for (int y = 0; y < 10; ++y) {
                printf("%lld%c", ans[x][y], " \n"[y == 9]);
            }
        }
    }
    return 0;
}

```

## C. You Are Given a WASD-string...

题意：给定一个机器人，最多填充一个命令，使得包围住机器人走过路径的矩形的最小面积

思路：填充一个命令$=$回推一步，枚举到达上下左右边界的位置，回推一步后算面积

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 2000010
#define INF 0x3f3f3f3f

char s[N];
int Left[N], Right[N], Up[N], Down[N];

void change(int &x, int &y, char c) {
    if (c == 'W') {
        ++y;
    } else if (c == 'A') {
        --x;
    } else if (c == 'S') {
        --y;
    } else if (c == 'D') {
        ++x;
    }
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%s", s + 1);
        int len = strlen(s + 1);
        int x = 0, y = 0;
        int x1 = INF, x2 = INF, y1 = INF, y2 = INF;
        int left = 0, right = 0, up = 0, down = 0;
        for (int i = 1; i <= len; ++i) {
            change(x, y, s[i]);
            if (left > x) {
                x1 = i;
                left = x;
            }
            if (right < x) {
                x2 = i;
                right = x;
            }
            if (up < y) {
                y1 = i;
                up = y;
            }
            if (down > y) {
                y2 = i;
                down = y;
            }
        }
        ll ans = 1ll * (up - down + 1) * (right - left + 1);
        //right
        if (x1 != INF) {
            x = y = left = right = up = down = 0;
            for (int i = 1; i <= len; ++i) {
                if (i == x1) {
                    change(x, y, 'D');
                    left = min(left, x);
                    right = max(right, x);
                    up = max(up, y);
                    down = min(down, y);
                }
                change(x, y, s[i]);
                left = min(left, x);
                right = max(right, x);
                up = max(up, y);
                down = min(down, y);
            }
            ans = min(ans, 1ll * (up - down + 1) * (right - left + 1));
        }
        if (x2 != INF) {
            x = y = left = right = up = down = 0;
            for (int i = 1; i <= len; ++i) {
                if (i == x2) {
                    change(x, y, 'A');
                    left = min(left, x);
                    right = max(right, x);
                    up = max(up, y);
                    down = min(down, y);
                }
                change(x, y, s[i]);
                left = min(left, x);
                right = max(right, x);
                up = max(up, y);
                down = min(down, y);
            }
            ans = min(ans, 1ll * (up - down + 1) * (right - left + 1));
        }
        if (y1 != INF) {
            x = y = left = right = up = down = 0;
            for (int i = 1; i <= len; ++i) {
                if (i == y1) {
                    change(x, y, 'S');
                    left = min(left, x);
                    right = max(right, x);
                    up = max(up, y);
                    down = min(down, y);
                }
                change(x, y, s[i]);
                left = min(left, x);
                right = max(right, x);
                up = max(up, y);
                down = min(down, y);
            }
            ans = min(ans, 1ll * (up - down + 1) * (right - left + 1));
        }
        if (y2 != INF) {
            x = y = left = right = up = down = 0;
            for (int i = 1; i <= len; ++i) {
                if (i == y2) {
                    change(x, y, 'W');
                    left = min(left, x);
                    right = max(right, x);
                    up = max(up, y);
                    down = min(down, y);
                }
                change(x, y, s[i]);
                left = min(left, x);
                right = max(right, x);
                up = max(up, y);
                down = min(down, y);
            }
            ans = min(ans, 1ll * (up - down + 1) * (right - left + 1));
        }
        printf("%lld\n", ans);
    }
    return 0;
}

```

## D. Print a 1337-string...

题意：构造一个长度不大于$1e5$的字符串，使得$1337$子串出现次数为$n$

思路：在字符串末尾放一个$7$，那么$1337$出现次数为每个$1$到$7$的$3$的个数$x$,贡献为$\frac{x(x-1)}{2}$，同时$n$可以分解为若干个$\frac{x(x-1)}{2}$的累加，构造即可

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 2000010

ll n;
char str[N];
int cnt[N];

ll calc(ll x) {
    return x * (x - 1) >> 1;
}

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%lld", &n);
        cnt[0] = 0;
        for (int i = 100000; i >= 1; --i) {
            ll C = calc(i);
            if (C == 0) {
                continue;
            }
            while (n >= C) {
                n -= C;
                cnt[++cnt[0]] = i;
            }
        }
        str[1] = '7';
        int pos = 1, c1 = 0;
        for (int i = cnt[0]; i >= 1; --i) {
            for (int j = c1; j < cnt[i]; ++j) {
                str[++pos] = '3';
                ++c1;
            }
            str[++pos] = '1';
        }
        reverse(str + 1, str + 1 + pos);
        str[++pos] = 0;
        puts(str + 1);
    }
    return 0;
}

```

## E.You Are Given Some Strings...

题意：定义$f(t,s)$表示字符串$s$在$t$中出现的次数，现在给定一个$t$以及$n$个$s$，问$\sum_{i=1}^{n}\sum_{j=1}^{n}f(t, s_i+s_j)$。

思路：单独考虑每个$s_i$的贡献，那么如果$s_i$出现的末尾位置是$L_i$，那么他能和出现的初始位置为$L_i+1$的每个$j$产生一个贡献。我们用$L_i$表示在$s$中末尾位置在$t$字符串的$i$位置出现字符串个数，$R_i$表示$s$中起始位置在$t$字符串的$i$位置出现的字符串个数，那么答案就是$\sum_{i=1}^{len-1}L_i\cdot R_i+1$其中$len$表示$t$的长度。对于$L,R$可以用$ac$自动机处理。

```C++

#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

#define N 200010

struct Trie {
    struct node {
        int nxt[26], fail, cnt;

        node() {
            memset(nxt, -1, sizeof nxt);
            cnt = 0;
        }
    } t[N];
    
    int root, L;
    
    int newnode() {
        t[++L] = node();
        return L;
    }
    
    void Init() {
        L = 0;
        root = newnode();
    }
    
    void insert(const string &s) {
        int now = root;
        for (char i : s) {
            if (t[now].nxt[i - 'a'] == -1)
                t[now].nxt[i - 'a'] = newnode();
            now = t[now].nxt[i - 'a'];
        }
        ++t[now].cnt;
    }
    
    void build() {
        queue<int> q;
        t[root].fail = root;
        for (int i = 0; i < 26; ++i) {
            if (t[root].nxt[i] == -1) {
                t[root].nxt[i] = root;
            } else {
                t[t[root].nxt[i]].fail = root;
                q.push(t[root].nxt[i]);
            }
        }
        while (!q.empty()) {
            int now = q.front();
            q.pop();
            t[now].cnt += t[t[now].fail].cnt;
            for (int i = 0; i < 26; ++i) {
                if (t[now].nxt[i] == -1) {
                    t[now].nxt[i] = t[t[now].fail].nxt[i];
                } else {
                    t[t[now].nxt[i]].fail = t[t[now].fail].nxt[i];
                    q.push(t[now].nxt[i]);
                }
            }
        }
    }
    
    void query(string s, ll *a) {
        int now = root;
        for (int i = 0, len = s.length(); i < len; ++i) {
            now = t[now].nxt[s[i] - 'a'];
            a[i] = t[now].cnt;
        }
    }
    
    void debug() {
        for (int i = 1; i <= L; ++i) {
            cout << t[i].cnt << " \n"[i == L];
        }
    }
} trie;

int n;
string s[N], t, tr;
ll L[N], R[N];

int main() {
#ifdef LOCAL_JUDGE
    freopen("input.txt", "r", stdin);
#endif
    ios::sync_with_stdio(false);
    cin.tie(nullptr), cout.tie(nullptr);
    while (cin >> t) {
        tr = t;
        reverse(tr.begin(), tr.end());
        memset(L, 0, sizeof L);
        memset(R, 0, sizeof R);
        cin >> n;
        for (int i = 1; i <= n; ++i) {
            cin >> s[i];
        }
        trie.Init();
        for (int i = 1; i <= n; ++i) {
            trie.insert(s[i]);
        }
        trie.build();
        trie.query(t, L);;

        trie.Init();
        for (int i = 1; i <= n; ++i) {
            reverse(s[i].begin(), s[i].end());
            trie.insert(s[i]);
        }
        trie.build();
        trie.query(tr, R);
    
        int len = t.length();
        reverse(R, R + len);
    
        ll ans = 0;
        for (int i = 0; i < len; ++i) {
            ans += L[i] * R[i + 1];
        }
        cout << ans << "\n";
    }
    return 0;
}

```