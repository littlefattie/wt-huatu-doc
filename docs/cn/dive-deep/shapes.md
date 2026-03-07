# 基本图形

## 点

点通常表示一个位置，一个座标，以数字对$(x,y)$的形式存在，在我们的设计文件中，我们可以显式地将点的定义写在`points`, `points2`, `points3`, `points4`条目下。
点的定义中$x$的部分和$y$的部分需要使用逗号`,`分隔，而完整的点的定义可以使用圆括号`()`括起来，也可以不用。比如下面两个点的定义`pt1`和`pt2`都是可以的。

```wtht
points:
  pt1: 3,4
  pt2: (5,6)
```

点可以直接定义（有名字），或者在定义相关图形时临时定义（无名字）。同时，点在定义时可以引用已定义的点的座标$x$或$y$分量来形成新的点，并且对于两个分量$x$，$y$，我们在定义时还可以进行一些运算，相当于有两个新的临时变量$V_x$, $V_y$拼在一起构成点的定义。比如如下的例子中， 点`pt1`的定义是显式的，非常确切的数字座标，而点`pt2`相当于点`pt1`的基础上进行了一个增量为`(5, 0)`的偏移，点`pt3`则是在`pt2`的基础上对$x$和$y$都进行了$\sqrt{6}$的偏移，这里我们不需要将$\sqrt{6}$的值计算出来，我们只需要使用相应的函数即可。

```wtht
points:
  pt1: 3, 4
  pt2: pt1.x + 5, pt1.y
  pt3: pt2.x + sqrt(6), pt2.y + sqrt(6)
```

## 基本图形

我们可以通过最基本的图形，线（线段），圆，椭圆，圆弧，以及Bezier曲线来构造出复杂的图形。所以在层次化的设计中，我们最开始要做的就是定义好一些基本的图形。在我们的设计文件中，我们需要将这些基本图形都放在shapes标签下，我们也可以在绘制时直接定义，即不给它取名字。例如：

```wtht
shapes:
  circ1: circ; ... # 定义了一个圆，取名为circ1

draw:
  - circ1; stroke, 1, black;       # 将圆circ1绘制出来，描边为黑色
  - circ; ... ||| stroke, 1, red;  # 我们绘制另一个圆，描边为红色。
```

图形的定义一般形式为 `SHAPE_TYPE; ...`，这里在最前面的是表示这个图形种类的关键字，后面接着的内容是定义这个图形的参数。下面的章节将说明每种基本图形定义所使用的关键字和参数.

### 直线

定义直线，我们需要使用`line`（可简写做`ln`）作为关键字。给定一个点（比如$P_0$，我们可称此点为锚点），和一个角度（比如45$\degree$）,我们就定义了一条通过点$P_0$，与X轴正向夹角为45$\degree$的直线。即

```wtht
points:
  p0: 20, 30
shapes:
  line1: line; p0; 45
```

需要注意的是这里的直线是数学意义上的直线，即向两端无限延长，因此我们不可能把它绘制出来，我们只能画出它的一部分，即线段。所以直线只是用来作为参考和计算使用的，我们无法绘制他，即便在最后的绘制阶段它被指定也将会被忽略。

```wtht
draw:
  - line1; stroke, 1,red;     # 这一行将被忽略
```

### 线段

使用`line`关键字。根据提供的参数的多寡和不同，我们也可定义出**线段**，和[**多线段**](#多线段)(**折线**). 这里都使用`line`并非不严谨，只是随性使然。

```wtht
shapes:
  # 给定点和角度，定义直线
  line1: line; p0;    deg1;
  # 给定两个（端）点，我们定义了一条线段
  ls1:   line; p0;    p1;
  # 在直线的基础上，给定一个长度l，我们定义一条线段，
  # 起点是定义直线时的锚点，终点是从该锚点往角度方向行走l距离后的点
  ls2:   line; line1; l;
  # 在直线的基础上，给定两个长度l1 和 l2，我们定义一条线段
  # 起点和终点是从锚点沿角度方向行走l1和l2后得到的点，l1对应起点，l2对应终点
  ls3:   line; line1; l1; l2;
  # 当给定3个及以上的点时，我们就定义了一段折线
  pln1:  line; p0;    p1;   p2;  p3;
```

从直线生成线段的方法可以参考下图:

[wtht] # Line Seg from Line
noTitle: yes
# The example to show how to design line shapes
vars:
  degLine: -40
  l: 160
  l1: 100
  l2: 200
  lenExtrude: 40

points:
  p0: 0,0
  p0Xminus: p0.x -  50, p0.y;
  p0Xplus:  p0.x + 150, p0.y;

markers:
  arrow1: arrow-simple;smooth;

shapes:
  line1: line; p0; degLine
  lsBase: line; line1; -l1 - lenExtrude; l2 + lenExtrude;
  ls1: line; line1; l
  ls2: line; line1; -l1; l2;

cpoints:
  cpLs1end: line1; l;
  cpLs1: line1; -l1
  cpLs2: line1; l2
  cpLHalfExtrude: line1; -l1;

blocks:
  base:
    - circ; p0; 5; ||| fill, red;
    - lsBase; stroke, 1, grey; dash; 3,3; marker; arrow1;
    - anglemark; p0.x + 50, p0.y; p0; cpLs2; 
      size; 80; arrow-solid;  labelpos; 0.34; lw; 40; linewidth; 2; lh; 20;  
      strokecolor; green;fill; yellow;  anchor; bl; label; deg1; offset; 5, 0;
    - line; p0Xminus; p0Xplus;   ||| stroke, 1, black; marker; arrow1;
    - label; p0Xplus; anchor; ml; w; 60; left; ||| $X$
    - label; cpLHalfExtrude; rotdeg; -40; anchor; br; w; 60; left; offset; 0,-7 ||| line1
  
  fig1:
    - base;
    - ls1; stroke, 4, black;
    - dimmark; p0; cpLs1end; label; $l$; left;barlen; 40; linepos; 0.7; noStartArrow;
  
  fig2:
    - base;
    - ls2; stroke, 4, black; 
    - dimmark; cpLs1; p0; label; $l_1$; left; barlen; 60; linepos; 0.7; noendarrow; 
    - dimmark; p0; cpLs2; label; $l_2$; left; barlen; 60; linepos; 0.6; nostartbar; nostartarrow;
    - circ; cpLs1; 4; ||| fill, blue, 0.6;
    - label; cpLs1; anchor; tl; left; h; 24; font; times; offset; 4,0 ||| point at [$-l_1$] of line1
    - circ; cpLs2; 4; ||| fill, blue, 0.6; 
    - label; cpLs2; anchor; tl; left;w;140; h; 30; font; times; ||| point at [$ l_2$] of line1

draw:
  - fig1;
  - fig2; move; 340, 0;

[wtht]

定义线段我们还可以使用关键字`lineseg` 和简写形式`ls`，这样我们只能定义线段，与使用`line`关键字类似，我们有三种形式：

```wtht
shapes:
  # 在直线的基础上，给定两个长度l1 和 l2，我们得到一条线段
  ls4: lineseg; line1; l1; l2;
  # 给出线段的两个端点p0 和 p1
  ls5: lineseg; p0; p1;
  # 先定义出一条直线(使用点p0，和角度deg1)，然后给出两个长度l1和l2来定义端点
  ls6: lineseg; p0; deg1; l1; l2;
```

### 多线段

除了使用`line`关键字定义多线段以外，我们还可以使用专门的`polyline`关键字来定义(简写形式为`pln`或`pline`)，参数有两种形式，提供一个已定义的点序列(point series)或者提供一个个离散的点。

```wtht
shapes:
  # 提供一系列的点将创建一个折线
  pln1: polyline; p0; p1; p2; p3; ... pN
pointSeries:
  ps1: 3, 3; 4, 2; 5,6; p1; p2; ... pX;
shapes2:
  # 提供一个已定义的点序列也将创建一个折线
  pln2: polyline; ps1;
```

[wtht] # Polyline
noTitle: yes

# --- Vars ------
vars:
  a: 3
  b: 4

# --- Points ------
points:
  p0: 0, 0
  p1: p0.x + 120, p0.y - 170
  p2: p1.x + 50, p1.y + 120
  p3: p2.x + 120, p2.y + 120
  p4: p3.x + 130, p3.y - 220
  p5: p4.x + 116, p4.y + 120
  p6: p5.x + 180, p5.y

pointSeries:
  ps1: p0; p1;  p2; p3; p4; p5; p6;
# --- Shapes ------
shapes:
  pln1: pln; p0; p1; p2; p3; p4; p5; p6;
  
# --- Draw --------
draw:
  - pln1; stroke, black, 2; linejoin; round; fill, none;
  - label; p0; anchor; tl; w; 30; left; ||| $P_0$
  - label; p1; anchor; bm; w; 30; h; 20;  ||| $P_1$
  - label; p2; anchor; bl; w; 30; h; 20;  ||| $P_2$
  - label; p3; anchor; tm; w; 30; h; 30;  ||| $P_3$
  - label; p4; anchor; bm; w; 30; h; 20;  ||| $P_4$
  - label; p5; anchor; tr; w; 30; h; 20;  ||| $P_5$
  - label; p6; anchor; bm; w; 30; h; 30;  ||| $P_6$
  - ps1; 4; red;
[wtht]

### 圆

使用关键字`circle`(可简写为`circ`)，我们将创建一个圆。参数有两个，第一个是圆心，第二个是圆的半径。

```wtht
vars:
  r: 5
shapes:
  c1: circle; (0, 0); r
  c2: circ;  3, 5; r + 5
```

[wtht] # Circle
noTitle: yes

vars:
  a: 100

points:
  pC1: (0, 0)

shapes:
  c1: circle; pC1; a

draw:
  - c1; stroke; 3, black; fill, none;
  - dimmark; pC1; c1.320; linewidth; 2; nostartbar; noendbar; noStartArrow; linepos; 0; gap;0; barlen;1; left; label; $r$
  - circ; c1.c; 3 ||| fill, red;

[wtht]

### 圆环

使用关键字`ring`，我们可以定义一个圆环，参数有三个，第一个是圆心，第二个和第三个是内径和外径，相对大的那个是外径，相对小的是内径。

```wtht
vars:
  rIn: 80
  thickness: 40
shapes:
  ring1: ring; 0,0; rIn; thickness + rIn
```

[wtht] # Ring
noTitle: yes

vars:
  rIn: 80
  thickness: 40
points:
  p0: 0,0
shapes:
  ring1: ring; p0; rIn; thickness + rIn

cpoints2:
  pIn1: ring1; in; 330
  pOut1: ring1; out; 330
  pOut2: ring1; out; 300

draw:
  - ring1; fill, yellow, 0.3; stroke, black; 3
  - dimmark; p0; pIn1;  gap; 0; linepos;0; nostartbar; noendbar; linewidth;1; nostartarrow; label; $R_{in}$;
  - dimmark; p0; pOut2; gap; 0; linepos;0; nostartbar; noendbar; linewidth;1; nostartarrow; left; label; $R_{out}$;
  - dimmark; pIn1; pOut1; gap; 0; linepos; 0;nostartbar; noendbar; linewidth; 1; remotepos;  100, -100; lw; 54; lh; 14; textsize; 12; label; thickness
  - circ; p0; 3 ||| fill, red;

[wtht]

### 矩形

使用`rectangle`(简写为`rect`)，我们可以定义一个矩形。有三种方法。我们可以通过两个点（相对的两个顶点）来定义，或者左上角加宽高来定义，或者中心点加宽高来定义。

```wtht
shapes:
  # 通过两个相对的顶点来指定
  rect1: rectangle; x1, y1; x2, y2;
  # 指定左上角和宽高
  rect2: rect;      x1, y1; width; height
  # 指定中心点和宽高
  rect3: rect;      bycenter; pCenter; width; height;
```

[wtht] # Rectangle
noTitle: yes
# --- Vars ------
vars:
  rectW: 80
  rectH: 60

# --- Points ------
points:
  pCenter: 0,0
  pAnchor: -50, -40
  pFar: 60, 30

# --- Shapes ------
shapes:
  rect1:rect; bycenter; pCenter; rectW; rectH;
  rect2:rect; pAnchor; pFar;
  rect3:rect; pAnchor; rectW; rectH

blocks:
  blk1:
    - rect1; stroke, 2, black; fill, none;
    - circ; pCenter; 4; ||| fill, red;
    - label; pCenter; anchor; tm; w; 60; ||| $P_{center}$
    - dimMark; rect1.sw; rect1.se; lw; 20; label; $W$
    - dimMark; rect1.se; rect1.ne; lw; 20; label; $H$

  blk2:
    - rect2; stroke, 2, black; fill, none;
    - circ; pAnchor; 4; ||| fill, red;
    - circ; pFar;    4; ||| fill, red;
    - label; pAnchor; anchor; bm; w; 60; h; 24;  ||| $(x_1, y_1)$
    - label; pFar; anchor; tm; w; 60; h; 24; ||| $(x_2, y_2)$

  blk3:
    - rect3; stroke, 2, black; fill, none;
    - circ; pAnchor; 4; ||| fill, red;
    - label; pAnchor; anchor; bm; w; 60; h; 24;  ||| $(x_1, y_1)$
    - dimMark; rect3.sw; rect3.se; lw; 20; label; $W$
    - dimMark; rect3.se; rect3.ne; lw; 40; label; $H$

# --- Draw --------
draw:
  - blk2;
  - blk3; move; 240, 0;
  - blk1; move; 450, 0;
[wtht]

### 三角形

使用`triangle`(可简写为`tri`)关键字，我们可以定义一个三角形。参数就是三角形的三个顶点。

```wtht
shapes:
  # 给出A，B，C三个点，我们就定义了一个三角形
  tri1: triangle; pA; pB; pC;

```

[wtht] # Triangle
noTitle: yes

vars:
  a: 3
  b: 4

# --- Points ------
points:
  pA: 100, 30
  pB: 0, -90
  pC: -80, 15

# --- Shapes ------
shapes:
  tri1: tri; pA; pB; pC;

# --- Draw --------
draw:
  - tri1; stroke, 2, black; fill, none; linejoin; round;  
  - label; pA; anchor; tl; w; 30; left; h; 20; ||| $P_a$
  - label; pB; anchor; bm; w; 30; right; ||| $P_b$
  - label; pC; anchor; mr; w; 30; right; ||| $P_c$

[wtht]

### 多边形

使用`polygon` (可简写为`plg`)，我们可以定义多边形。同**多线段**的定义一项，我们可以使用**点序列**，或者**离散的点**来定义一个多边形。

```wtht
shapes:
  # 提供一系列的点来创建多边形
  plg1: polygon; p0; p1; p2; p3; ... pN
pointSeries:
  ps1: 3, 3; 4, 2; 5,6; p1; p2; ... pX;
shapes2:
  # 提供一个已定义的点序列也可以用来创建多边形
  plg2: polygon; ps1;
```

[wtht] # Polygon
noTitle: yes

# --- Points ------
points:
  p1: 160, 200
  p2: -200, 250
  p3: -300, -200
  p4: -20, -400
  p5: 200, -220
  p6: 230, 00
  
# --- Shapes ------
shapes:
  plg1: polygon; p1; p2; p3; p4; p5; p6

# --- Draw --------
draw:
  - plg1; fill, yellow, 0.3; stroke, 3; black;
  - label; p1; anchor; tl; w; 30; ||| $P_1$
  - label; p2; anchor; tr; w; 30; ||| $P_2$
  - label; p3; anchor; mr; w; 30; ||| $P_3$
  - label; p4; anchor; bm; w; 30; h; 25 ||| $P_4$
  - label; p5; anchor; bl; w; 30; ||| $P_5$
  - label; p6; anchor; ml; w; 30; ||| $P_6$
[wtht]

[i] 很显然，使用多边形，我们可以创建三角形和矩形。


### 正多边形

使用`npolygon`(可简写为`plgn`), 我们可以定义正多边形。需要四个参数，第一个是**几**边形，我们用$N$表示，第二个参数是中心点，即正多边形的**外接圆的圆心**，第三个参数是圆心到顶点的距离，即**外接圆的半径**，第四个参数是第一个点相对于$X$轴正向的旋转角。

```wtht
shapes:
  # 这里我们定义一个正N边形，中心点为pCenter, 中心点到顶点距离为R，起始点旋转角
  # 为deg_of_start
  nplg1: npolygon; N; pCenter; R; deg_of_start
```

[wtht] # N Regular Polygon
noTitle: yes

vars:
  R: 200
  degOfStart: -20
markers:
  arrow1: arrow-solid; size; 10;

# --- Points ------
points:
  pCenter: 0,0
  pXplus: R * 1.4, 0

# --- Shapes ------
shapes:
  nplgn1: plgn; 7; pCenter; R; degOfStart

cpoints:
  pR1: nplgn1; 7;
  pR2: nplgn1; 1;

# --- Draw --------
draw:
  - nplgn1; stroke, 3, black; fill, none;
  - line; pCenter; pXplus; ||| stroke, 1, grey; dash, 3,3; marker; arrow1;
  #- line; pCenter; pR2; ||| stroke, 1, black; 
  - dimMark; pCenter; pR2; gap; 0; linepos; 0; nostartbar; noendbar; linewidth; 2; nostartarrow;left; label; $R$; 
  - label; pXplus; anchor; br; w; 20; h; 25; ||| $X$ 
  - label; pCenter; anchor; tr; w; 60; h; 25; ||| $P_{center}$
  - label; pCenter.x, pCenter.y - 100; anchor; center; w; 160; h; 20; ||| 正$N$边形 ($N=7$)
  - anglemark; pXplus; pCenter; pR2; size; 70; linewidth; 2; arrow-solid; lw; 150; labelpos; 0.3;  offset; 5, 0;label; deg_of_start 
  - circ; pCenter; 4 ||| fill, red;
  - label; nplgn1.1; anchor; bl; w; 25; ||| $P_1$
  - label; nplgn1.2; anchor; tl; w; 20; h; 20; ||| $P_2$
  - label; nplgn1.3; anchor; tm; w; 20; h; 20; ||| $P_3$
  - label; nplgn1.4; anchor; tr; w; 20; h; 20; ||| $P_4$
  - label; nplgn1.5; anchor; mr; w; 25; h; 20; ||| $P_5$
  - label; nplgn1.6; anchor; br; w; 25; ||| $P_6$
  - label; nplgn1.7; anchor; bl; w; 30; ||| $P_7$
[wtht]

### 椭圆

使用`ellipse`(可简写为`ell`)，我们可以定义椭圆。椭圆的参数共有4个：椭圆的圆心，$X$轴半径，$Y$轴半径，和旋转角度。

```wtht
shapes:
  ell1: ellipse; pCenter; rx; ry; rotDeg
```

[wtht] # Ellipse
noTitle: yes
vars:
  rx: 220
  ry: 150
  rotDeg: 24

markers:
  arrow1: arrow-solid; size; 12;
# --- Points ------
points:
  pCenter: (0, 0)
  pXplus: rx * 1.3, 0

# --- Shapes ------
shapes:
  ell1: ell; pCenter; rx; ry; rotDeg;

cpoints:
  cpEast: ell1; 0;
  cpNorth: ell1; 270;
shapes2:
  lsRx: line; pCenter; cpEast;
  lsRy: line; pCenter; cpNorth;
  lnRx: line; pCenter; lsRx.deg;
  lnRy: line; pCenter; lsRy.deg;
  lsRx1: line; lnRx; -rx * 1.2; rx * 1.3
  lsRy1: line; lnRy; -ry * 1.2; ry * 1.4
  

# --- Draw --------
draw:
  - ell1; stroke, 3, black; fill, none;
  - lsRx1; stroke, 1, silver; dash; 2,2; marker; arrow1;
  - lsRy1; stroke, 1, silver; dash; 3,3; marker; arrow1;

  - line; pCenter; pXplus; ||| stroke, 1, grey; dash; 3,3; marker; arrow1;
  - dimmark; pCenter; cpEast; nostartbar; noendbar; nostartarrow; gap;0; linepos; 0; linewidth; 2; label; $R_x$
  - dimmark; pCenter; cpNorth; left; nostartbar; noendbar; nostartarrow; gap;0; linepos; 0; linewidth; 2; label; $R_y$
  - anglemark; pXplus; pCenter; cpEast; arrow-solid; size; 130; linewidth; 2; anchor; ml; offset; 7,0; labelpos; 0.46; label; rotDeg;
  - circ; pCenter; 4 ||| fill, red;

[wtht]


### 二次Bezier曲线

使用`bezier2`(可简写为`b2`)，我们可以定义二次Bezier曲线。参数有三个，第一个为起点，第二个为控制点，第三个为终点。二次bezier曲线实际上是抛物线，起点和终点到控制点的直线是抛物线分别在起点和终点处的切线。详细参考可查阅SVG技术规范([https://www.w3.org/TR/SVG2/paths.html#PathDataQuadraticBezierCommands](https://www.w3.org/TR/SVG2/paths.html#PathDataQuadraticBezierCommands))

```wtht
shapes:
  bez1: bezier2; pStart; pCtrl; pEnd;
```

[wtht] # Bezier2
noTitle: yes

points:
  pStart: -20, -20
  pEnd:   320, 180
  pCtrl: pStart.x + 320, pStart.y - 140

blocks:
  refLines:
    - line; pStart; pCtrl; |||
    - line; pCtrl; pEnd; |||
  refAll:
    - refLines; stroke, 1, aqua
    - circ; pStart; 6; ||| fill, aqua
    - circ; pEnd; 6;   ||| fill, aqua
    - circ; pCtrl; 6;  ||| fill, magenta
  
draw:
  - refAll;
  - b2; pStart; pCtrl; pEnd ||| stroke, 6, black; fill, none; opaque; 0.2;
  - label; pStart; anchor; tr; w; 70; ||| pStart
  - label; pEnd; anchor; tl; w; 70; ||| pEnd
  - label; pCtrl; anchor; bl; w; 70; color; magenta ||| pCtrl
[wtht]


### 三次Bezier曲线

使用`bezier3`(可简写为`b3`)，我们可以定义三次Bezier曲线。参数有四个，第一个为起点，第二个为控制点1，第二个为控制点2，第三个为终点。详细参考可查阅SVG技术规范([https://www.w3.org/TR/SVG2/paths.html#PathDataCubicBezierCommands](https://www.w3.org/TR/SVG2/paths.html#PathDataCubicBezierCommands))

```wtht
shapes:
  bez1: bezier3; pStart; pCtrl1; pCtrl2; pEnd;
```

[wtht] # Bezier3
noTitle: yes

points:
  pStart: -20, -20
  pEnd:   320, 180
  pCtrl1: pStart.x + 320, pStart.y - 140
  pCtrl2: pEnd.x - 100, pEnd.y + 40

blocks:
  refLines:
    - line; pStart; pCtrl1; |||
    - line; pCtrl2; pEnd; |||
  refAll:
    - refLines; stroke, 1, aqua
    - circ; pStart; 6; ||| fill, aqua
    - circ; pEnd; 6;   ||| fill, aqua
    - circ; pCtrl1; 6;  ||| fill, magenta
    - circ; pCtrl2; 6;  ||| fill, magenta
  
draw:
  - refAll;
  - b3; pStart; pCtrl1; pCtrl2; pEnd ||| stroke, 6, black; fill, none; opaque; 0.2;
  - label; pStart; anchor; tr; w; 70; ||| pStart
  - label; pEnd; anchor; ml; w; 70; ||| pEnd
  - label; pCtrl1; anchor; bl; w; 70; color; magenta ||| pCtrl1
  - label; pCtrl2; anchor; tr; w; 70; color; magenta ||| pCtrl2

[wtht]

[i] **笔记:**
 一般性地，我们可以定义N次Bezier曲线，公式为：
$$ P(t) = P_{start}\binom{n}{0}(1-t)^n + P_{ctrl1}\binom{n}{1}t(1-t)^{n-1} + P_{ctrl2}\binom{n}{2}t^2(1-t)^{n-2} + \cdots + P_{end}\binom{n}{n}t^n,\\ t\in [0,1]
$$
通常二次和三次已能够满足需求，故只定义了二次和三次。

### 弧线

使用`arc`关键字，我们可以定义圆弧或者椭圆弧（即椭圆的一部分）。我们可以在圆或者椭圆的基础上进行定义，或者使用统一的方式定义，参数各有不同。

1. 基于圆或者椭圆来定义，参数为圆弧在对应圆或者椭圆上的方位角，以及沿着圆或者椭圆行走的方向，`cw`表示顺时针，`ccw`表示逆时针

```wtht
shapes:
  circ1: circ; p0; R
  arc1:  arc; circ1; deg1; deg2; cw;
  ell1:  ell; p0; Rx; Ry; rotDeg;
  arc2:  arc; ell1; deg3; deg4; ccw;
```

[wtht] # Make Arc from Circle/Ellipse
noTitle: yes

# --- Vars ------
vars:
  r: 140
  rx: r * 1.5
  ry: r
  deg1: 30
  deg2: 120
  deg3: 330
  deg4: 160

markers:
  arrow1: arrow-simple; smooth; size; 4;

# --- Points ------
points:
  pCenter: (0, 0)

# --- Shapes ------
shapes:
  circ1: circle; pCenter; r
  ell1: ell; pCenter; rx; ry;
  arc1: arc; circ1; deg1; deg2; cw;
  arc2: arc; circ1; deg3; deg4; ccw;
  arcCw: arc; pCenter; r*0.8; r*0.8; 0; 45; 110; cw;
  arcCcw: arc; pCenter; r*0.8; r*0.8; 0; 300; 220; ccw;
  arc3: arc; ell1; deg1; deg2; cw;
  arc4: arc; ell1; deg3; deg4; ccw;
  arcCw2: arc; pCenter; rx*0.8; ry*0.8; 0; 45; 110; cw;
  arcCcw2: arc; pCenter; rx*0.8; ry*0.8; 0; 300; 220; ccw;

cpoints:
  cpCirc1: circ1; deg1
  cpCirc2: circ1; deg2
  cpCirc3: circ1; deg3
  cpCirc4: circ1; deg4
  cpEll1:  ell1; deg1;
  cpEll2:  ell1; deg2;
  cpEll3:  ell1; deg3;
  cpEll4:  ell1; deg4;

blocks:
  blkCircle:
    #- line; pCenter; pCenter.x + r * 1.2, pCenter.y; ||| stroke, 1, grey; marker; arrow1;
    - circ; pCenter; 4; ||| fill,red;
    - circ1; stroke, 2, grey; dash; 3,3;fill,none;
    - arc1; stroke, 6, red; opaque;0.4; fill, none;
    #- arc2; stroke, 6, green; opaque; 0.5; fill, none;
    - arcCw; stroke, 2, silver; marker; arrow1; fill, none;
    #- arcCcw; stroke, 2, silver; marker; arrow1; fill, none;
    - label; arcCw.m; anchor; br; w; 30; h; 20; offset; 0, -20; ||| cw
    #- label; arcCcw.m; anchor; tl; w; 30; h; 20; offset; 0, 15; ||| ccw
    - circ; cpCirc1; 6; ||| fill, blue;
    - label; cpCirc1; anchor; tl; w; 60; h; 40 ||| point at deg1
    - circ; cpCirc2; 6; ||| fill, blue;
    - label; cpCirc2; anchor; tr; w; 60; h; 40 ||| point at deg2
    - dimmark; pCenter; circ1.0; gap;0;linepos;0; nostartbar; noendbar; nostartarrow; left; label; $R$; linewidth; 2;
  blkEllipse:
    - circ; pCenter; 4; ||| fill,red;
    - ell1; stroke, 2, grey; dash; 3,3; fill, none;
    #- arc3; stroke, 6, maroon; opaque;0.4; fill, none;
    - arc4; stroke, 6, royalblue; opaque; 0.5; fill, none;
    #- arcCw2; stroke, 2, silver; marker; arrow1; fill, none;
    - arcCcw2; stroke, 2, silver; marker; arrow1; fill, none;
    #- label; arcCw2.m; anchor; br; w; 30; h; 20; offset; 0, -20; ||| cw
    - label; arcCcw2.m; anchor; tl; w; 30; h; 20; offset; 0, 15; ||| ccw
    - circ; cpEll3; 6; ||| fill, blue;
    - label; cpEll3; anchor; tr; w; 60; h; 40 ||| point at deg3
    - circ; cpEll4; 6; ||| fill, blue;
    - label; cpEll4; anchor; bl; w; 60; h; 40 ||| point at deg4
    - dimmark; pCenter; ell1.0; gap;0;linepos;0; nostartbar; noendbar; nostartarrow; label; $R_x$; linewidth; 2; left;
    - dimmark; pCenter; ell1.90; gap;0;linepos;0; nostartbar; noendbar; nostartarrow; label; $R_y$; linewidth; 2;

# --- Draw --------
draw:
  - blkCircle;
  - blkEllipse; move; 420, 0

[wtht]

2. 使用统一的方式来定义弧线。参数依次为：**起始点**，$R_x$，$R_y$，**旋转角**，**顺时针否**，**优弧/劣弧**，**终点**。

```wtht
shapes:
  # cw表示顺时针，ccw表示逆时针，large表示优弧，small表示劣弧
  arc1: arc; pStart; Rx1; Ry1; rotDeg1; cw; large; pEnd;
  arc2: arc; pStart; Rx2; Ry2; rotDeg2; ccw; small; pEnd;
```
[wtht] # Make Unified Arc
noTitle: yes
# --- Vars ------
vars:
  r: 70
  rx: r * 1.5
  ry: r
  xDist: 400
  yDist: 250
  
markers:
  arrow1: arrow-simple; smooth; size; 8;

# --- Points ------
points:
  pStart: 0, 60
  pEnd: 80, 0

# --- Shapes ------
shapes:
  arc1: arc; pStart; rx; ry;0; cw;  large; pEnd;
  arc2: arc; pStart; rx; ry;0; cw;  small; pEnd;
  arc3: arc; pStart; rx; ry;0; ccw; large; pEnd;
  arc4: arc; pStart; rx; ry;0; ccw; small; pEnd;
  arcAux1: arc; pStart.x - 20, pStart.y - 20; rx * 0.7; ry * 0.7; 0; cw; large; pEnd.x - 40, pEnd.y - 30;
  arcAux2: arc; pStart.x + 11, pStart.y - 6; rx * 0.8; ry * 0.8; 0; cw; small; pEnd.x - 11, pEnd.y + 12;
  arcAux3: arc; pStart.x + 25, pStart.y + 16; rx * 0.7; ry * 0.7; 0; ccw; large; pEnd.x + 12, pEnd.y + 22;
  arcAux4: arc; pStart.x + 12, pStart.y - 16; rx * 0.9; ry * 1.1; 0; ccw; small; pEnd.x - 12, pEnd.y - 22;
  
blocks:
  arcAll:
    - arc1;
    - arc2;
    - arc3;
    - arc4;
  bg:
    - arcAll; stroke, 1, grey; dash; 3,3; fill,none;
    - circ; pStart; 6; ||| fill, red;
    - circ; pEnd; 6; ||| fill, orange;
    - label; pStart; anchor; tr; w; 60; offset; -4, 12  ||| pStart
    - label; pEnd;   anchor; bl; w; 60; offset; 2, -12; ||| pEnd
  cwLarge:
    - bg
    - arc1; stroke, 6, royalblue; opaque; 0.5; fill, none;
    - arcAux1; stroke, 1, black; fill, none; marker; arrow1;
    - label; arcAux1.m; w; 60; align; right; ||| cw
    - label; arc1.m; w; 60; anchor; br;h;25;||| large
  cwSmall:
    - bg
    - arc2; stroke, 6, royalblue; opaque; 0.5; fill, none;
    - arcAux2; stroke, 1, black; fill, none; marker; arrow1;
    - label; arcAux2.m; w; 20; align; right; offset; 2, -5 ||| cw
    - label; arc2.m; w; 60; anchor; br;h;25;||| small
  ccwLarge:
    - bg
    - arc3; stroke, 6, royalblue; opaque; 0.5; fill, none;
    - arcAux3; stroke, 1, black; fill, none; marker; arrow1;
    - label; arcAux3.m; w; 30; align; right; anchor; br; offset; -10,0 ||| ccw
    - label; arc3.m; w; 60; anchor; tl;h;25;||| large
  ccwSmall:
    - bg
    - arc4; stroke, 6, royalblue; opaque; 0.5; fill, none;
    - arcAux4; stroke, 1, black; fill, none; marker; arrow1;
    - label; arcAux4.m; w; 30; align; right; anchor; br; offset; -10,0 ||| ccw
    - label; arc4.m; w; 60; anchor; tl;h;25;||| small

# --- Draw --------
draw:
  - cwLarge
  - cwSmall; move; xDist, 0;
  - ccwLarge; move; 0, yDist;
  - ccwSmall; move; xDist, yDist
[wtht]



3. 临时定义一个圆或者椭圆，然后像方法1一样，定义起始终止角，和行走方向。

```wtht
shapes:
  # 前面三个参数定义个一个椭圆(Rx==Ry时就是圆)，然后定义起止角度，和行走方向
  arc1: arc; pCenter; R;  R;  rotDeg; deg1; deg2; cw;
  arc2: arc; pCenter; Rx; Ry; rotDeg; deg3; deg4; ccw;
```
[wtht] # Ad Hoc define arc from circle/ellipse
noTitle: yes

# --- Vars ------
vars:
  r: 140
  rx: r * 1.5
  ry: r
  deg1: 30
  deg2: 120
  deg3: 330
  deg4: 160

markers:
  arrow1: arrow-simple; smooth; size; 4;

# --- Points ------
points:
  pCenter: (0, 0)

# --- Shapes ------
shapes:
  circ1: circle; pCenter; r
  ell1: ell; pCenter; rx; ry;

  arc1: arc;pCenter; r; r; 0; deg1; deg2; cw;
  arc2: arc;pCenter; r; r; 0; deg3; deg4; ccw;
  arc3: arc;pCenter; rx; ry; 0; deg1; deg2; cw;
  arc4: arc;pCenter; rx; ry; 0; deg3; deg4; ccw;

cpoints:
  cpCirc1: circ1; deg1
  cpCirc2: circ1; deg2
  cpCirc3: circ1; deg3
  cpCirc4: circ1; deg4
  cpEll1:  ell1; deg1;
  cpEll2:  ell1; deg2;
  cpEll3:  ell1; deg3;
  cpEll4:  ell1; deg4;

blocks:
  blkCircle:
    - circ; pCenter; 4; ||| fill,red;
    - circ1; stroke, 2, grey; dash; 3,3;fill,none;
    - arc1; stroke, 6, red; opaque;0.4; fill, none; marker;arrow1;
    #- arc2; stroke, 6, green; opaque; 0.5; fill, none;
    - label; arc1.m; anchor; br; w; 30; h; 20; offset; 0, -20; ||| cw
    #- label; arcCcw.m; anchor; tl; w; 30; h; 20; offset; 0, 15; ||| ccw
    - circ; cpCirc1; 6; ||| fill, blue;
    - label; cpCirc1; anchor; br; w; 60; h; 40 ||| point at deg1
    - circ; cpCirc2; 6; ||| fill, blue;
    - label; cpCirc2; anchor; tr; w; 60; h; 40 ||| point at deg2
    - dimmark; pCenter; circ1.0; gap;0;linepos;0; nostartbar; noendbar; nostartarrow; left; label; $R$; linewidth; 2;
  blkEllipse:
    - circ; pCenter; 4; ||| fill,red;
    - ell1; stroke, 2, grey; dash; 3,3; fill, none;
    #- arc3; stroke, 6, maroon; opaque;0.4; fill, none;
    - arc4; stroke, 6, royalblue; opaque; 0.5; fill, none; marker;arrow1;
    #- label; arcCw2.m; anchor; br; w; 30; h; 20; offset; 0, -20; ||| cw
    - label; arc4.m; anchor; tl; w; 30; h; 20; offset; 0, 15; ||| ccw
    - circ; cpEll3; 6; ||| fill, blue;
    - label; cpEll3; anchor; tr; w; 60; h; 40 ||| point at deg3
    - circ; cpEll4; 6; ||| fill, blue;
    - label; cpEll4; anchor; tr; w; 60; h; 40 ||| point at deg4
    - dimmark; pCenter; ell1.0; gap;0;linepos;0; nostartbar; noendbar; nostartarrow; label; $R_x$; linewidth; 2; left;
    - dimmark; pCenter; ell1.90; gap;0;linepos;0; nostartbar; noendbar; nostartarrow; label; $R_y$; linewidth; 2;

# --- Draw --------
draw:
  - blkCircle;
  - blkEllipse; move; 420, 0
[wtht]
