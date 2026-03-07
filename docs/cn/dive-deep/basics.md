# 基本知识

## 坐标系

我们采用笛卡尔平面直角座标系。与通常教科书中不同的是，我们采用计算机处理图像时常用的Y轴正向朝下的方式，而非朝上。

[wtht]
name: basicCoordinates
# Example WT-HUATU design file
# --- Vars ------
noTitle: yes

markers:
  arr1: arrow; size; 10

# --- Points ------
points:
  pO: (0, 0)
  pPxy: 100, 100

# --- Shapes ------
shapes:
  lnX: line; pO; 0
  lnY: line; pO; 90
  lsX: line; lnX; -20; 200;
  lsY: line; lnY; -20; 200;
  pXY: circ; pPxy; 3


# --- Draw --------
draw:
  - lsX; fill, none; stroke; 1, cc; marker; arr1
  - lsY; fill, none; stroke; 1, cc; marker; arr1
  - pXY; fill, cc;
  - line; pPxy; pPxy.x, 0; ||| fill, none; stroke; 0.5, cc; dash; 3,3
  - line; pPxy; 0, pPxy.y; ||| fill, none; stroke; 0.5, cc; dash; 3 3
  - circ; pO; 3; ||| fill, cc;
  - label; at; pPxy.x, 0; w; 25; h; 22; anchor; bm ||| $x_0$
  - label; at; 0, pPxy.y; w; 25; h; 22; anchor; mr ||| $y_0$
  - label; at; lsX.e; w; 25; h; 22; anchor; ml ||| $X$
  - label; at; lsY.e; w; 25; h; 22; anchor; tm ||| $Y$
  - label; at; pPxy; w; 80; h; 22; anchor; tl;left ||| $(x_0, y_0)$
  - label; at; pO; w; 25; h; 22; anchor; br; ||| $O$
[wtht]

## 变量

我们可以定义一些变量，用以保存一些特定的设计参数，以便于在其基础之上进行计算等来创建由一定几何关系的点，线等相关图形。
变量的定义中可以引用已定义的变量，还可以引入一些对数字或已有变量进行数学运算后的结果。比如下面的定义中，变量`a`和`k`是显式的数字，但变量`b`就是`a`乘以`k`的结果，而变量`c`是`k`开方之后再乘以`b`的结果。
```wtht
vars:
  a: 5
  k: 1.2
  b: a * k
  c: b * sqrt(k)
```

## 角度

在所有我们需要确定角度的地方，我们输入的都是角度，尽管在数学上，可能弧度(rad)更加普遍一些，但我们还是选择使用角度，这样更加贴近实际使用情形。

角度以$x$轴正方向为0，然后朝**顺时针**旋转的方向增加。角度的通常使用范围为$[0, 360)$ ($360\degree$ 同 $0\degree$是相同的), 但在实际使用时，我们也可以使用负角度，以及超过$360\degree$的数值，使用这些数值时，将会被整理到$[0, 360)$范围内。

比如下面图中的例子，我们绘制了几条从原点出发的不同角度的射线。


[wtht]
name: angleUsage
# Example WT-HUATU design file
# --- Vars ------
noTitle: yes

vars:
  lRay: 240

markers:
  arr1: arrow; size; 10

# --- Points ------
points:
  pO: (0, 0)
  pPxy: 100, 100
  pL0: lRay * dcos(0), lRay * dsin(0)
  pL1: lRay * dcos(33), lRay * dsin(33)
  pL2: lRay * dcos(88), lRay * dsin(88)
  pL3: lRay * dcos(167), lRay * dsin(167)
  pL4: lRay * dcos(-58), lRay * dsin(-58)
  pL5: lRay * dcos(243), lRay * dsin(243)

# --- Shapes ------
shapes:
  lsX: line; pO; pL0.x + 30, pL0.y
  ln0: line; pO; pL0
  ln1: line; pO; pL1
  ln2: line; pO; pL2
  ln3: line; pO; pL3
  ln4: line; pO; pL4
  ln5: line; pO; pL5

# --- Draw --------
draw:
  - angleMark; pL0; pO; pL1; arrow-solid;red;size; 96; showdeg; linewidth;2; anchor; tl;lw; 70; lh; 18; textColor; red;labelpos; 0.4
  - angleMark; pL0; pO; pL2; arrow-solid;blue;size; 64; showdeg; linewidth;2; anchor; tl;lw; 60; lh; 32; textColor; blue; labelpos; 0.62
  - angleMark; pL0; pO; pL3; arrow-solid;maroon;size; 32; showdeg; linewidth;2; anchor; tr;lw; 120; lh; 22;textColor; maroon; labelpos; 0.75
  - angleMark; pL0; pO; pL4; arrow-solid;magenta;size; 40; linewidth;2; anchor; bl; label; $-58\degree$; lw; 80; lh; 18; textColor; magenta; labelpos; 0.35
  - angleMark; pL0; pO; pL5; arrow-simple;indianred;size; 132; large; linewidth;2; anchor; br;showdeg; lw; 80; lh; 18; textColor; indianred; labelpos; 0.84
  - ln1; fill, none; stroke; 1, red; marker; arr1
  - ln2; fill, none; stroke; 1, blue; marker; arr1
  - ln3; fill, none; stroke; 1, maroon; marker; arr1
  - ln4; fill, none; stroke; 1, magenta; marker; arr1
  - ln5; fill, none; stroke; 1, indianred; marker; arr1  
  - lsX; fill, none; stroke; 2, cc; marker; arr1
  - circ; pO; 6; ||| fill, cc;
  - label; at; lsX.e; w; 25; h; 22; anchor; ml ||| $X$
  - label; at; pO; w; 34; h; 22; anchor; br; offset; 0, 4||| $O$
[wtht]

如果您需要绘制或设计有关极座标系的图形，请注意这里我们角度增加的方向是**顺时针**,而非通常教科书中的**逆时针**。


## 数学函数

在定义变量进行计算的时候，我们引入了一些常用的数学函数，以下是详细说明:

- sin : 三角函数中的求正弦，输入单位为弧度(rad)
- cos : 三角函数中的求余弦，输入单位为弧度(rad)
- tan : 三角函数中的求正切，输入单位为弧度(rad)
- asin : 三角函数中的求反正弦，计算结果单位为弧度(rad)
- acos : 三角函数中的求反余弦，计算结果单位为弧度(rad)
- atan : 三角函数中的求反正切，计算结果单位为弧度(rad)

- dsin : 三角函数中的求正弦，输入单位为角度($\degree$)
- dcos : 三角函数中的求余弦，输入单位为角度($\degree$)
- dtan : 三角函数中的求正切，输入单位为角度($\degree$)
- dasin : 三角函数中的求反正弦，计算结果单位为角度($\degree$)
- dacos : 三角函数中的求反余弦，计算结果单位为角度($\degree$)
- datan : 三角函数中的求反正切，计算结果单位为角度($\degree$)

- exp : 指数函数，即$f(x) = e^x$
- log : 以自然数$e$为底的对数函数，即$f(x) = \lg x$ 或$f(x) = \log_e x$
- log10 : 以10为底的对数函数，即$f(x) = \log_{10} x$
- sqrt : 开方运算， 即$f(x) = \sqrt{x}$

在此之外，我们还可以直接写`pi`来表示圆周率$\pi$，因此请注意，定义变量的时候请不要使用`pi`作为变量名。

下面是一些例子:

```wtht
vars:
  # Define one angle
  ang1: pi / 6
  # Define its degree
  deg1: ang1 * 180 / pi
  # define two vars using sin and dcos
  k1: sin(ang1)
  k2: dcos(deg1)
  # define basic len
  len: 320
  lenW: len * k1
  lenH: len * k2
  len2: sqrt(len * len / 2)

points:
  pO: 0, 0
  pW: lenW, 0
  pH: 0, -lenH

shapes:
  c1: circ; pW; len2;
  c2: circ; pH; len2;

xpoints:
  pX1: c1; c2; e;

draw:
  # draw some triangle
  - tri; pO; pW; pH; ||| stroke, 2, cc; fill, none; linejoin; round;
  # mark one angle
  - angleMark; pO; pW;  pH; arrow-solid;cc; size; 23; showdeg; linewidth;2;
    anchor; br;lw; 70; lh; 18;
  # mark the potential perpendicular angle
  - angleMark; pW; pX1; pH; arrow-solid;red;size; 12; showperp;linewidth;2;
    fill, yellow
  # draw the new triangle sides
  - line; pW; pX1; pH; ||| stroke, 2, red; fill, none; linecap; round;
```

将此画出来之后是这个样子：

[wtht]
name: functionUsage
# Example WT-HUATU design file
# --- Vars ------
noTitle: yes

vars:
  # Define one angle
  ang1: pi / 6
  # Define its degree
  deg1: ang1 * 180 / pi
  # define two vars using sin and dcos
  k1: sin(ang1)
  k2: dcos(deg1)
  # define basic len
  len: 320
  lenW: len * k1
  lenH: len * k2
  len2: sqrt(len * len / 2)

points:
  pO: 0, 0
  pW: lenW, 0
  pH: 0, -lenH

shapes:
  c1: circ; pW; len2;
  c2: circ; pH; len2;

xpoints:
  pX1: c1; c2; e;

draw:
  # draw some triangle
  - tri; pO; pW; pH; ||| stroke, 2, cc; fill, none; linejoin; round;
  # mark one angle
  - angleMark; pO; pW; pH; arrow-solid;cc;size; 23; showdeg; linewidth;2; anchor; br;lw; 70; lh; 18;
  # mark the potential perpendicular angle
  - angleMark; pW; pX1; pH; arrow-solid;red;size; 12; showperp; linewidth;2; fill, yellow
  # draw the new triangle sides
  - line; pW; pX1; pH; ||| stroke, 2, red; fill, none; linecap; round;

[wtht]

