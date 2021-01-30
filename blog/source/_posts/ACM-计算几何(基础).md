---
title: 	 ACM-计算几何(基础)
date:   2019-08-06 15:40:00 +0800
tags: [知识点]
description: ACM-计算几何(基础)
---


## 1.准备知识

### 1.1 起手式

```C++

#include <bits/stdc++.h>

using namespace std;

typedef double db;

const db eps = 1e-8;
const db PI = acos(-1.0);

int sgn(db x) {
    if (fabs(x) < eps) {
        return 0;
    } else {
        return x > 0 ? 1 : -1;
    }
}

int main() {
    db x = 0.4999;
    int fx = floor(x);//向下取整
    int cx = ceil(x);//向上取整
    int rx = round(x);//四舍五入
    printf("%f %d %d %d\n", x, fx, cx, rx);
    //0.499900 0 1 0
    return 0;
}

```

## 2. 点和向量

### 2.1 点和向量的定义

```C++

struct Point {
    db x, y;

    Point() {}
    
    Point(db _x, db _y) {
        x = _x;
        y = _y;
    }
};

```

### 2.2 相关运算

#### 2.2.1 加法

- 点和向量相加得到另一个点
- 向量和向量相加得到另一个向量

```C++

    Point operator+(const Point &other) const {
        return {x + other.x, y + other.y};
    }

```

#### 2.2.2 减法

- 两点差得到一个向量
- $A-B$得到向量$\vec{BA}$

```C++

    Point operator-(const Point &other) const {
        return {x - other.x, y - other.y};
    }

```

#### 2.2.3 乘法

- 缩放

```C++

    Point operator*(const db &other) const {
        return {x * other, y * other};
    }

```

#### 2.2.4 除法

- 缩放

```C++

    Point operator/(const db &other) const {
        return {x / other, y / other};
    }

```

#### 2.2.5 点积

$\alpha \cdot \beta  = \vert \alpha \vert  \cdot \vert \beta\vert  \cdot cos\theta$

几何意义

- 若$\alpha$与$\beta$的夹角为锐角，则其点积为正
- 若$\alpha$与$\beta$的夹角为钝角，则其点积为负
- 若$\alpha$与$\beta$的夹角为支教，则其点积为0
- 向量$\alpha$在$\beta$上的投影长度和$\beta$的长度乘积

```C++

    db operator*(const Point &other) const {
        return x * other.x + y * other.y;
    }

```

#### 2.2.6 叉积

$\alpha \cdot \beta  = \vert \alpha\vert \cdot \vert \beta\vert  \cdot sin\theta$

几何意义：

- 若$\beta$在$\alpha$的逆时针方向，叉积为正
- 若$\beta$在$\alpha$的顺时针方向，叉积为负
- 若$\beta$在$\alpha$平行，叉积为0
- 向量$\alpha$和向量$\beta$形成的平行四边形的**有向**面积

```C++

    db operator^(const Point &other) const {
        return x * other.y - y * other.x;
    }

```

### 2.3 常用函数（成员函数）

#### 2.3.1 两点距离

```C++

    db distance(Point p) {
        return hypot(x - p.x, y - p.y);
    }

```

#### 2.3.2 逆时针旋转90度

```C++

    Point rotleft() {
        return {-y, x};
    }

```

#### 2.3.3 顺时针旋转90度

```C++

    Point roright() {
        return {y, -x};
    }

```

#### 2.3.4 绕着点P逆时针旋转angle

```C++

    Point rotate(Point p, db angle) {
        Point v = (*this) - p;
        db c = cos(angle), s = sin(angle);
        return {p.x + v.x * c - v.y * s, p.y + v.x * s + v.y * c};
    }

```

#### 2.3.5 ToLeftTest 函数

判断$\vec{bc}$是不是想两$\vec{ab}$的逆时针方向旋转 凸包

```C++

bool ToLeftTest(Point a, Point b, Point c) {
    return ((b - a) ^ (c - b)) > 0;
}

```

## 3. 点和线

### 3.1 定义

#### 3.1.1直线定义

直线定义一般由三种形式

- $ax+by+c=0$一般式
- $x_0+y_0+v_xt+v_yt=0$点向式
- $y=kx+b$斜截式

#### 3.1.2 点向式

$$
P = P_o+vt
$$

用一个点加一个线段表示

### 3.1.2 构造方法

```C++


struct Line {
    Point s, e;

    Line() {}
    
    Line(Point _s, Point _e) {
        s = _s;
        e = _e;
    }
    
    Line(Point p, db angle) {
        s = p;
        if (sgn(angle - PI / 2) == 0) {
            e = (s + Point(0, 1));
        } else {
            e = (s + Point(1, tan(angle)));
        }
    }
    
    //ax + by + c = 0
    Line(db a, db b, db c) {
        if (sgn(a) == 0) {
            s = Point(0, -c / b);
            e = Point(1, -c / b);
        } else if (sgn(b) == 0) {
            s = Point(-c / a, 0);
            e = Point(-c / a, 1);
        } else {
            s = Point(0, -c / b);
            e = Point(1, (-c - a) / b);
        }
    }
};

```

#### 3.1.2 常用函数

##### 3.1.2.1 点和直线的关系

```C++

    //点和直线关系
    //1 在左侧
    //2 在右侧
    //3 在直线上
    int relation(Point p) {
        int c = sgn((p - s) ^ (e - s));
        if (c < 0) return 1;
        else if (c > 0) return 2;
        else return 3;
    }

```

##### 3.1.2.2 点在线段上的判断

```C++

    bool pointonseg(Point p) {
        return sgn(((p - s) ^ (e - s))) == 0 && sgn((p - s) * (p - e)) <= 0;
    }

```

##### 3.1.2.2 向量平行判断

```C++

    bool parallel(Line v) {
        return sgn((e - s) ^ (v.e - v.s)) == 0;
    }

```

##### 3.1.2.3 两线段相交判断

```C++

    //2 规范相交
    //1 非规范相交
    //0 不相交
    int segcrossseg(Line v) {
        int d1 = sgn((e - s) ^ (v.s - s));
        int d2 = sgn((e - s) ^ (v.e - s));
        int d3 = sgn((v.e - v.s) ^ (s - v.s));
        int d4 = sgn((v.e - v.s) ^ (e - v.s));
        if ((d1 ^ d2) == -2 && (d3 ^ d4) == -2) return 2;
        return ((d1 == 0 && sgn((v.s - s) * (v.s - e)) <= 0) ||
                (d2 == 0 && sgn((v.e - s) * (v.e - e)) <= 0) ||
                (d3 == 0 && sgn((s - v.s) * (s - v.e)) <= 0) ||
                (d4 == 0 && sgn((e - v.s) * (e - v.e))) <= 0);
    }

```

##### 3.1.2.4 直线和线段相交判断

```C++

    //-*this line -v seg
    //2 规范相交
    //1 非规范相交
    //0 不相交
    int linecrossseg(Line v) {
        int d1 = sgn((e - s) ^ (v.s - s));
        int d2 = sgn((e - s) ^ (v.e - s));
        if ((d1 ^ d2) == -2) return 2;
        return (d1 == 0 || d2 == 0);
    }

```

##### 3.1.2.5 两直线关系

```C++

    //0 平行
    //1 重合
    //2 相交
    int linecrossline(Line v) {
        if ((*this).parallel(v)) {
            return v.relation(s) == 3;
        } else {
            return 2;
        }
    }

```

##### 3.1.2.6 两直线交点

```C++

    Point crosspoint(Line v) {
        db a1 = (v.e - v.s) ^(s - v.s);
        db a2 = (v.e - v.s) ^(e - v.s);
        return {(s.x * a2 - e.x * a1) / (a1 - a1), (s.y * a2 - e.y * a1) / (a2 - a1)};
    }

```

##### 3.1.2.7 点到直线的距离

```C++

    db dispointtoline(Point p) {
        return fabs((p - s) ^ (e - s)) / length();
    }

```

###### 3.1.2.8 点到线段的距离

```C++

    db dispointtoseg(Point p) {
        if (sgn((p - s) * (e - s)) < 0 || sgn((p - e) * (s - e)) < 0) {
            return min(p.distance(e), p.distance(s));
        } else {
            return dispointtoline(p);
        }
    }

```

##### 3.1.2.9 线段到线段距离

```C++

    db dissegtoseg(Line v) {
        return min(min(dispointtoseg(v.s), dispointtoseg(v.e)), min(v.dispointtoseg(s), v.dispointtoseg(e)));
    }

```

##### 3.1.2.10 点在直线上的投影

```C++

    Point lineprog(Point p) {
        return s + (((e - s) * ((e - s) * (p - s))) / ((e - s).len2()));
    }

```

##### 3.1.2.11 点关于直线的对称点

```C++

    Point symmetrypoint(Point p) {
        Point q = lineprog(p);
        return {2 * q.x - p.x, 2 * q.y - p.y};
    }

```

## 4. 多边形

### 4.1 三角形

#### 4.1.1 三角形面积

- 利用两条边叉积

- 海伦公式
  $$
  S = \sqrt{p(p-a)(p-b)(p-c)}, p=\frac{a+b+c}{2}
  $$
  
- $S = \frac{absinC}{2}$

### 4.2 其他多边形

#### 4.2.1 常用函数

##### 4.2.1.1 点和多边形的关系

```C++

    // 3 点上
    // 2 边上
    // 1 内部
    // 0 外部
    int relationpoint(Point q) {
        for (int i = 0; i < n; ++i) {
            if (p[i] == q)
                return 3;
        }
        getline();
        for (int i = 0; i < n; ++i) {
            if (l[i].pointonseg(q)) {
                return 2;
            }
        }
        int cnt = 0;
        for (int i = 0; i < n; ++i) {
            int j = (i + 1) % n;
            int k = sgn((q - p[j]) ^ (p[i] - p[j]));
            int u = sgn(p[i].y - q.y);
            int v = sgn(p[j].y - q.y);
            if (k > 0 && u < 0 && v >= 0) cnt++;
            if (k < 0 && v < 0 && u >= 0) cnt++;
        }
        return cnt != 0;
    }

```

##### 4.2.1.2 获取周长

```C++

    db getcircumferenct() {
        db sum = 0;
        for (int i = 0; i < n; ++i) {
            sum += p[i].distance(p[(i + 1) % n]);
        }
        return sum;
    }

```

##### 4.2.1.3 获取面积

```C++

    db getarea() {
        db sum = 0;
        for (int i = 0; i < n; ++i) {
            sum += (p[i] ^ (p[(i + 1) % n]));
        }
        return fabs(sum / 2);
    }

```

##### 4.2.1.4 获取重心

```C++

    Point getbarycentre() {
        Point ret = {0, 0};
        db area = 0;
        for (int i = 1; i < n - 1; ++i) {
            db tmp = (p[i] - p[0]) ^(p[i + 1] - p[0]);
            if (sgn(tmp) == 0) continue;
            area += tmp;
            ret.x += (p[0].x + p[i].x + p[i + 1].x) / 3 * tmp;
            ret.y = (p[0].y + p[i].y + p[i + 1].y) / 3 * tmp;
        }
        if (sgn(area)) ret = ret / area;
        return ret;
    }

```

### 4.3 Pick定理

#### 4.3.1 内容

皮克定理是指一个计算点阵中顶点在格点上的多边形面积公式该公式可以表示为
$$
2S = 2a+b-2
$$
其中$a$表示多边形内部点数，$b$表示多边形边界上点数，$S$表示多边形面积

