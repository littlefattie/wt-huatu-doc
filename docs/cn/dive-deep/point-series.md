# 序列

## 点序列

为了方便重复使用，我们可以将一些点集合在一起，形成一个点序列（或者叫点集合）。这样我们可以基于这个点序列绘制图形，比如如果这些点是某一些特殊标记的点，那么我们可以绘制成统一的点；或者它是一条线的各个端点，那么我们可以据此序列生成一个折线，来将他们连接起来。

定义点序列非常简单，只需要将要添加的点顺序添加即可。

```wtht
points:
  p0: 3,3
  p1: 4,4
  p2: 5,6
pointSeries:
  # 按顺序添加各个点即可，请注意这里的点是有顺序的
  ps1: p0; p1; p2; -3, 0; -5, -4; ... 
```

除了通过添加离散点的方式来创建点序列，我们也可以使用计算点的方式，比如在一个圆上取一系列点。在此之前，我们需要定义一个**数序列**。

## 数序列

数序列并不需要显式定义（为它命名），我们只需要在定义点序列和图形序列的时候指定即可。指定数序列，我们可以添加一些离散的数，或者定义一个等差数列，即给定等差数列的起始值`start number`，公差`step`，和终到值`end number`，公差可正可负，但实际使用的值是其绝对值，真正表示增减的是起始值和终到值的相对大小，`end number`比`start number`大的话，公差为正，`end number`比`start number`小的话，则为负。请注意，公差`step`**不可以为0**。

定义等差数列的方式是采用`start number : step : end number`的格式，其中冒号`:`是分隔符，也可以是用竖线`|`, 即`start number | step | end number`也是可以的。


## 计算点序列

可以参考[计算点](./get-point.md#计算点)章节，我们只使用第一个`arg1`参数，然后把`arg1`换成**数序列**或者**标记序列**(指`n`,`s`等的序列)即可。请注意，如果给定的数序列不是等差数列，请用逗号(`,`)分隔。
下面是从圆，椭圆，直线，线段，Bezier曲线计算点序列的例子。

```wtht
shapes:
  ln1: line; 0,0; -32
  ls1: line; -40, -40; -180, 280;
  c1:  circ; 0,0; 40
  ell1: ell; 0,0; 40; 20; -25; 
  b2a: b2; -10, -10; 20, -40; 30, 20;
  b3a: b3; -10, -10; 20, -40; -30, 25; 30, 20;
pointSeries:
  # for Line
  psLn1: ln1; 20, 30, 0, -20, -30;  # Give discrete 'lengths`
  psLn2: ln1; a, 10, 20;
  psLn3: ln1; -20:8:30;             # Give a range of length
  # for Line segment
  psLs1: ls1; 0.1, 0.3, 0.7, 0.888; # Give discrete t 
  psLs2: ls1; 1 : 0.1 : 0;          # Give range of t 
  # for Circle
  psC1:  c1; c, 12, 66, 90, 180;    # Give discrete tags/degrees
  psC2:  c1; 0: 15: 120;            # Give range of degree
  # for Ellipse
  psEll1: ell1; e; s; w; 40; 100;   # Give discrete tags/degrees
  psEll2: ell1; 40:20:270;          # Give range of degree
  # for Bezier2
  psB2a1: b2a; s;e;m; 0.777;        # Give discrete tags/t
  psB2a2: b2a; 0.2: 0.25: 0.9;      # Give range of t
  # for Bezier3
  psB3a1: b3a; s;e;m; 0.777;        # Give discrete tags/t
  psB3a2: b3a; 0.2: 0.25: 0.9;      # Give range of t
blocks:
  blkLn1:
    - line; ln1; -50; 50; ||| stroke, 1, black; dash, 3,3;
    - psLn1; 4; blue;
    - psLn2; 2; red;
  blkLn2:
    - line; ln1; -50; 50; ||| stroke, 1, black; dash, 3,3;
    - psLn3; 2; magenta;
  blkLs1:
    - ls1; stroke, 2, black;
    - psLs1; 3; blue;
  blkLs2:
    - ls1; stroke, 2, black;
    - psLs2; 3; magenta;
  blkC1:
    - c1; stroke, 1, black; fill, none;
    - psC1; 3; blue;
  blkC2:
    - c1; stroke, 1, black; fill, none;
    - psC2; 3; magenta;
  blkEll1:
    - ell1; stroke, 1, black; fill, none;
    - psEll1; 3; blue;
  blkEll2:
    - ell1; stroke, 1, black; fill, none;
    - psEll2; 3; magenta;
  blkB2a1:
    - b2a; stroke,1, black; fill, none;
    - psB2a1; 3; blue;
  blkB2a2:
    - b2a; stroke,1, black; fill, none;
    - psB2a2; 3; magenta;
  blkB3a1:
    - b3a; stroke,1, black; fill, none;
    - psB3a1; 3; blue;
  blkB3a2:
    - b3a; stroke,1, black; fill, none;
    - psB3a2; 3; magenta;
draw:
  - blkLn1;
  - blkLn2;  move; 0, 20;
  - blkLs1;
  - blkLs2;  move; -20, 0;
  - blkC1;   move;   0, 100;
  - blkC2;   move; -40, 200;
  - blkEll1; move; 100, 0;
  - blkEll2; move; 100, 60;
  - blkB2a1; move; 100, 120;
  - blkB2a2; move; 80, 140;
  - blkB3a1; move; 90, 160;
  - blkB3a2; move; 70, 180;

```

[wtht] # Point Series 1
noTitle: yes

shapes:
  ln1: line; 0,0; -32
  ls1: line; -40, -40; -180, 280;
  c1:  circ; 0,0; 40
  ell1: ell; 0,0; 40; 20; -25; 
  b2a: b2; -10, -10; 20, -40; 30, 20;
  b3a: b3; -10, -10; 20, -40; -30, 25; 30, 20;
pointSeries:
  # for Line
  psLn1: ln1; 20, 30, 0, -20, -30;  # Give discrete 'lengths`
  psLn2: ln1; a, 10, 20;
  psLn3: ln1; -20:8:30;             # Give a range of length
  # for Line segment
  psLs1: ls1; 0.1, 0.3, 0.7, 0.888; # Give discrete t 
  psLs2: ls1; 1 : 0.1 : 0;          # Give range of t 
  # for Circle
  psC1:  c1; c, 12, 66, 90, 180;    # Give discrete tags/degrees
  psC2:  c1; 0: 15: 120;            # Give range of degree
  # for Ellipse
  psEll1: ell1; e; s; w; 40; 100;   # Give discrete tags/degrees
  psEll2: ell1; 40:20:270;          # Give range of degree
  # for Bezier2
  psB2a1: b2a; s;e;m; 0.777;        # Give discrete tags/t
  psB2a2: b2a; 0.2: 0.25: 0.9;      # Give range of t
  # for Bezier3
  psB3a1: b3a; s;e;m; 0.777;        # Give discrete tags/t
  psB3a2: b3a; 0.2: 0.25: 0.9;      # Give range of t
blocks:
  blkLn1:
    - line; ln1; -50; 50; ||| stroke, 1, black; dash, 3,3;
    - psLn1; 4; blue;
    - psLn2; 2; red;
  blkLn2:
    - line; ln1; -50; 50; ||| stroke, 1, black; dash, 3,3;
    - psLn3; 2; magenta;
  blkLs1:
    - ls1; stroke, 2, black;
    - psLs1; 3; blue;
  blkLs2:
    - ls1; stroke, 2, black;
    - psLs2; 3; magenta;
  blkC1:
    - c1; stroke, 1, black; fill, none;
    - psC1; 3; blue;
  blkC2:
    - c1; stroke, 1, black; fill, none;
    - psC2; 3; magenta;
  blkEll1:
    - ell1; stroke, 1, black; fill, none;
    - psEll1; 3; blue;
  blkEll2:
    - ell1; stroke, 1, black; fill, none;
    - psEll2; 3; magenta;
  blkB2a1:
    - b2a; stroke,1, black; fill, none;
    - psB2a1; 3; blue;
  blkB2a2:
    - b2a; stroke,1, black; fill, none;
    - psB2a2; 3; magenta;
  blkB3a1:
    - b3a; stroke,1, black; fill, none;
    - psB3a1; 3; blue;
  blkB3a2:
    - b3a; stroke,1, black; fill, none;
    - psB3a2; 3; magenta;
draw:
  - blkLn1;
  - blkLn2;  move; 0, 20;
  - blkLs1;
  - blkLs2;  move; -20, 0;
  - blkC1;   move;   0, 100;
  - blkC2;   move; -40, 200;
  - blkEll1; move; 100, 0;
  - blkEll2; move; 100, 60;
  - blkB2a1; move; 100, 120;
  - blkB2a2; move; 80, 140;
  - blkB3a1; move; 90, 160;
  - blkB3a2; move; 70, 180;

[wtht]

从多线段或者多边形计算点序列时有两种方式。

1. 使用两个参数，第一个是N，表示多线段的第几段（多边形的第N条边），第二个参数是关于$t$的数列，离散列或者等差列。
2. 使用一个参数，是1到N之间的整数数列，对应多线段或多边形的第n个顶点。

```wtht
shapes:
  pln1: pln; -40, -40; -60, 50; 50, 40; 20, -30; -10, 0;
  plg1: plg; -40, -40; -60, 50; 50, 40; 20, -30; -10, 0;

pointSeries:
  psPln1: pln1; 2; 0.1, 0.2, 0.4;     # discrete t's on 2nd segment
  psPln2: pln1; 3; 0.1: 0.15: 0.7;    # ranged t's on 3rd segment
  psPln3: pln1; 1,2,4;                # The 1st, 2nd, 4th vertices
  psPlg1: plg1; 2; 0.1, 0.2, 0.4;     # discrete t's on 2nd edge
  psPlg2: plg1; 5; 0.1: 0.15: 0.7;    # ranged t's on 5th edge
  psPlg3: plg1; 1,3,5;                # The 1st, 3rd, 5th vertices
blocks:
  blkPln1:
    - pln1; stroke,1,black; fill, none;
    - psPln1; 2; blue;
  blkPln2:
    - pln1; stroke,1,black; fill, none;
    - psPln2; 2; red;
  blkPln3:
    - pln1; stroke,1,black; fill, none;
    - psPln3; 2; magenta;
  blkPlg1:
    - plg1; stroke,1,black; fill, none;
    - psPlg1; 2; blue;
  blkPlg2:
    - plg1; stroke,1,black; fill, none;
    - psPlg2; 2; red;
  blkPlg3:
    - plg1; stroke,1,black; fill, none;
    - psPlg3; 2; magenta;
draw:
  - blkPln1
  - blkPln2; move; 150,0;
  - blkPln3; move; 300,0;
  - blkPlg1; move; 0,  110;
  - blkPlg2; move; 150,110;
  - blkPlg3; move; 300,110;
```

[wtht] # Get Point Series from Polyline
noTitle: yes
shapes:
  pln1: pln; -40, -40; -60, 50; 50, 40; 20, -30; -10, 0;
  plg1: plg; -40, -40; -60, 50; 50, 40; 20, -30; -10, 0;

pointSeries:
  psPln1: pln1; 2; 0.1, 0.2, 0.4;     # discrete t's on 2nd segment
  psPln2: pln1; 3; 0.1: 0.15: 0.7;    # ranged t's on 3rd segment
  psPln3: pln1; 1,2,4;                # The 1st, 2nd, 4th vertices
  psPlg1: plg1; 2; 0.1, 0.2, 0.4;     # discrete t's on 2nd edge
  psPlg2: plg1; 5; 0.1: 0.15: 0.7;    # ranged t's on 5th edge
  psPlg3: plg1; 1,3,5;                # The 1st, 3rd, 5th vertices
blocks:
  blkPln1:
    - pln1; stroke,1,black; fill, none;
    - psPln1; 2; blue;
  blkPln2:
    - pln1; stroke,1,black; fill, none;
    - psPln2; 2; red;
  blkPln3:
    - pln1; stroke,1,black; fill, none;
    - psPln3; 2; magenta;
  blkPlg1:
    - plg1; stroke,1,black; fill, none;
    - psPlg1; 2; blue;
  blkPlg2:
    - plg1; stroke,1,black; fill, none;
    - psPlg2; 2; red;
  blkPlg3:
    - plg1; stroke,1,black; fill, none;
    - psPlg3; 2; magenta;
draw:
  - blkPln1
  - blkPln2; move; 150,0;
  - blkPln3; move; 300,0;
  - blkPlg1; move; 0,  110;
  - blkPlg2; move; 150,110;
  - blkPlg3; move; 300,110;
[wtht]

[i] 请注意，对于同样的N个顶点，多线段将只有N-1段，而多边形有N条边。

从三角形计算点和多边形类似，指定一个参数时可以指定从三角形计算点时使用的各种标记。

```wtht
shapes:
  tri1: tri; -100, 100; 150, 80; -10, -90;

pointSeries:
  ps1: tri1; 1,2,3, cin, cout, cg;    # discretely present the tags
  ps2: tri1; 1; 0.1, 0.2, 0.7;        # discrete t's on 1st edge 
  ps3: tri1; 2; 0.2: 0.15: 0.7;       # range of t's on 2nd edge
blocks:
  blk1:
    - tri1; stroke, 1, black; fill, none;
    - ps1; 3; blue;
    - ps2; 3; magenta;
    - ps3; 3; red;
draw:
  - blk1;
```

[wtht] # Make Point Series from triangle
noTitle: yes

shapes:
  tri1: tri; -100, 100; 150, 80; -10, -90;

pointSeries:
  ps1: tri1; 1,2,3, cin, cout, cg;    # discretely present the tags
  ps2: tri1; 1; 0.1, 0.2, 0.7;        # discrete t's on 1st edge 
  ps3: tri1; 2; 0.2: 0.15: 0.7;       # range of t's on 2nd edge
blocks:
  blk1:
    - tri1; stroke, 1, black; fill, none;
    - ps1; 3; blue;
    - ps2; 3; magenta;
    - ps3; 3; red;
draw:
  - blk1;
[wtht]

从矩形计算点序列稍有不同，有两种方式。
1. 使用一个参数，指定计算点时用的各种标记，`nw`,`ne`,`s`,`e`, `c`等。
2. 使用两个参数，第一个参数是`north`,`south`,`east`,`west`中的一个表示一条边，第二个参数是$t$，可以是离散数列，也可以是等差数列，用来在对应边上取出各个$t$对应的点。

```wtht
shapes:
  rect1: rect;bycenter; 0,0; 400; 260;

pointSeries:
  ps1: rect1; nw; ne; s;w; c;
  ps2: rect1; north; 0.1, 0.3, 0.7
  ps3: rect1; east; 0.2: 0.15: 0.8

draw:
  - rect1; stroke, 1, black; fill, none;
  - ps1; 3; black;
  - ps2; 3; blue;
  - ps3; 3; magenta;
```

[wtht] # Make point series from Rectangle
noTitle:yes

shapes:
  rect1: rect;bycenter; 0,0; 400; 260;

pointSeries:
  ps1: rect1; nw; ne; s;w; c;
  ps2: rect1; north; 0.1, 0.3, 0.7
  ps3: rect1; east; 0.2: 0.15: 0.8

draw:
  - rect1; stroke, 1, black; fill, none;
  - ps1; 3; black;
  - ps2; 3; blue;
  - ps3; 3; magenta;
[wtht]


从圆弧计算点序列只有一种方式，提供$t$的数列，离散列或者等差列。

```wtht
shapes:
  arc1: arc; 0,0; 100; 80; 0; 330; 120; ccw;
  ell1: ell; 0,0; 100; 80; 0;
pointSeries:
  ps1: arc1; 0.1, 0.2, 0.7;
  ps2: arc1; 0.1: 0.15: 0.8;
blocks:
  blk1:
    - ell1; stroke,1, black; dash, 3,3; fill, none;
    - arc1; stroke, 4, royalblue; opaque; 0.6; fill, none;
    - ps1; 4; black;

  blk2:
    - ell1; stroke,1, black; dash, 3,3; fill, none;
    - arc1; stroke, 4, royalblue; opaque; 0.6; fill, none;
    - ps2; 4; maroon;

draw:
  - blk1
  - blk2; move; 250, 0;
```

[wtht] # Make point series from Arc
noTitle: yes

shapes:
  arc1: arc; 0,0; 100; 80; 0; 330; 120; ccw;
  ell1: ell; 0,0; 100; 80; 0;
pointSeries:
  ps1: arc1; 0.1, 0.2, 0.7;
  ps2: arc1; 0.1: 0.15: 0.8;
blocks:
  blk1:
    - ell1; stroke,1, black; dash, 3,3; fill, none;
    - arc1; stroke, 4, royalblue; opaque; 0.6; fill, none;
    - ps1; 4; black;

  blk2:
    - ell1; stroke,1, black; dash, 3,3; fill, none;
    - arc1; stroke, 4, royalblue; opaque; 0.6; fill, none;
    - ps2; 4; maroon;

draw:
  - blk1
  - blk2; move; 250, 0;

[wtht]
