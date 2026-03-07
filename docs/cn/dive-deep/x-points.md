# 交点

通过已定义的基本图形，我们可以获取（并命名）各个基本图形间的交点，我们可以定义单个**点**（在`xpoints`项下），或者**点序列**（在`pointSeries`项下）。

考虑到两个图形之间可能没有交点，只有一个交点（比如两条直线），或者很多个交点。在定义（单个）交点时，我们需要指定一个表示**方位**的关键字，来指定需要选取众多交点中的哪一个。而定义点序列时，则不需要，点序列将包含两个图形所有的交点。

```wtht
shapes:
  shape1: what ever you want to define...
  shape2: what ever you want to define...
xpoints:
  xp1: shape1; shape2;          # 当未指定方位时，将返回计算出的交点中的第一个，
                                # 不确定是哪个，故当交点个数较多时，最好指定一个方位
  xp2: shape1; shape2; n;       # 方位未N，即北，表示取最靠上面的那个
  xp3: shape1; shape2; se;      # 方位为SE，即东南，意味最东最南（最右最下）
pointSeries:
  ps1: shape1; x; shape2;       # 当定义点序列的表达式中第二个关键字为 x 时，表示
                                # 这个点序列是两个图形给的交点的集合
```
所有可用的方位关键字如下：

- `n`: 表示最北边，最靠上的那个
- `s`: 表示最南边，最靠下的那个
- `w`: 表示最西边，最靠左的那个
- `e`: 表示最东边，最靠右的那个
- `ne`: 表示东北角的那个，最靠右上的那个
- `nw`: 表示西北角的那个，最靠左上的那个
- `se`: 表示东南角的那个，最靠右下的那个
- `sw`: 表示西南角的那个，最靠左下的那个
- `xmiddle`: `x`坐标最中间的那个
- `ymiddle`: `y`坐标相对最中间的那个
- `xymiddle`或`center`: 交点中相加相对最中间的那个


[i] 请注意，方位关键字的是用方法是将所有计算出的交点按照某种条件**进行排序**，然后挑选排序后的序列中的**第一个**，或者**最中间**那个，有一定的概率，你给定的关键字所挑出的交点不是你所期望的那个，并且随着计算交点的原基本图形参数发生变化，相同方位关键字所取出的交点也可能不同，在使用和设计时请注意使用合适的方位关键字，并尝试多种可能，以便尽可能地固定交点的（相对）位置。比如某个交点**靠右**非常多，那很可能当我们指定`ne`关键字时，取得的交点是这个最靠东的点，而不是另外一个比较靠北，但并没有很靠东的点。`xmiddle`,`ymiddle`, `xymiddle`这三个关键字如果遇到是奇数个交点，则会挑选最中间的那个，如果是偶数个交点，则会挑选中间两个点中参考值相对较小的那个，比如一共六个交点，则会挑选排序后的第3号。

下面是一个简单的例子，我们可以通过两种方式来定义交点或者交点序列。
```wtht
vars:
  rx: 60
  ry: rx * 2.444

points:
  p0: 0,0

shapes:
  ell1: ell; p0; rx; ry; 0;
  ell2: ell; p0; rx; ry; 60;
  ell3: ell; p0; rx; ry; 120;

xpoints:
  xp12NE: ell1; ell2; ne;
  xp12E:  ell1; ell2; e;
  xp23E:  ell2; ell3; e;
  xp13SE: ell1; ell3; se;
  xp12SW: ell1; ell2; sw;
  xp23W:  ell2; ell3; w;
  xp13NW: ell1; ell3; nw;
  xp13NE: ell1; ell3; ne;
  xp12SE: ell1; ell2; se;
  xp23S:  ell2; ell3; s;
  xp13SW: ell1; ell3; sw;
  xp12NW: ell1; ell2; nw;
  xp23N:  ell2; ell3; n;

pointSeries:
  # Define Point Series by intersection points
  ps12: ell1; x; ell2;
  ps13: ell1; x; ell3;
  ps23: ell2; x; ell3;
  # Pick defined points to make Point Series
  psOuter: xp12NE; xp23E; xp13SE; xp12SW; xp23W; xp13NW;
  psInner: xp13NE; xp12SE; xp23S; xp13SW; xp12NW; xp23N;
blocks:
  ells:
    - ell1; stroke, 1, #000; 
    - ell2; stroke, 1, #666; 
    - ell3; stroke, 1, #aaa; 
  blk1:
    - ells; fill, none;
    - circ; xp12NE; 8; ||| fill, cyan;
    - circ; xp12E;  8; ||| fill, magenta;
    - ps12; 4; royalblue;
  blk2:
    - ells; stroke, 1, cc; fill, none;
    - psOuter; 4; red;
    - psInner; 4; blue;
draw:
  - blk1;
  - blk2; move; ry * 2.2, 0;
```


[wtht] # The figure to demonstrate how to define x-point for xpoints and pointSeries
noTitle: yes

vars:
  rx: 60
  ry: rx * 2.444

points:
  p0: 0,0

shapes:
  ell1: ell; p0; rx; ry; 0;
  ell2: ell; p0; rx; ry; 60;
  ell3: ell; p0; rx; ry; 120;

xpoints:
  xp12NE: ell1; ell2; ne;
  xp12E:  ell1; ell2; e;
  xp23E:  ell2; ell3; e;
  xp13SE: ell1; ell3; se;
  xp12SW: ell1; ell2; sw;
  xp23W:  ell2; ell3; w;
  xp13NW: ell1; ell3; nw;
  xp13NE: ell1; ell3; ne;
  xp12SE: ell1; ell2; se;
  xp23S:  ell2; ell3; s;
  xp13SW: ell1; ell3; sw;
  xp12NW: ell1; ell2; nw;
  xp23N:  ell2; ell3; n;

pointSeries:
  # Define Point Series by intersection points
  ps12: ell1; x; ell2;
  ps13: ell1; x; ell3;
  ps23: ell2; x; ell3;
  # Pick defined points to make Point Series
  psOuter: xp12NE; xp23E; xp13SE; xp12SW; xp23W; xp13NW;
  psInner: xp13NE; xp12SE; xp23S; xp13SW; xp12NW; xp23N;
blocks:
  ells:
    - ell1; stroke, 1, #000; 
    - ell2; stroke, 1, #666; 
    - ell3; stroke, 1, #aaa; 
  blk1:
    - ells; fill, none;
    - circ; xp12NE; 8; ||| fill, cyan;
    - circ; xp12E;  8; ||| fill, magenta;
    - ps12; 4; royalblue;
    - label; xp12NE; anchor; bl; w; 60; h; 40; ||| 方位 NE 
    - label; xp12E;  anchor; tl; w; 60; h; 30; ||| 方位 E 
  blk2:
    - ells; stroke, 1, cc; fill, none;
    - psOuter; 4; red;
    - psInner; 4; blue;
draw:
  - blk1;
  - blk2; move; ry * 2.2, 0;
[wtht]

下面是一个稍微复杂点的例子，各种不同的基本图形之间都可以获取交点：

```wtht
points:
  p0: 0,0

shapes:
  line1: line; p0; -85
  ls1: line; line1; -100; 160;
  rect1: rect; bycenter; p0; 450; 300;
  tri1:  tri; -330, 60; 270, 0; -60, -390
  c1: circ; tri1.cin; tri1.rIn * 1.333
  ell1: ell; p0; 100; 375; -43
  bc2: b2; -270, 90; 160, -600; 198, 180 
  bc3: b3; -300, -300; 150, -240; -240, 300; 360, -120;

pointSeries:
  psxLn1Bc3:   ls1;  x; bc3;
  psxRec1Ell1: rect1;x; ell1;
  psxTri1C1:   tri1; x; c1;
  psxtri1Ell1: tri1; x; ell1;
  psxtri1BC2:  tri1; x; bc2;
  psxtri1BC3:  tri1; x; bc3;
  psxC1Ell1:   c1;   x; ell1;
  psxC1Bc2:    c1;   x; bc2;
  psxC1Bc3:    c1;   x; bc3;
  psxEll1Bc2:  ell1; x; bc2;
  psxEll1Bc3:  ell1; x; bc3;
  psxBc2Bc3:   bc2;  x; bc3;

jobs:
  ln1Bc3:
    - ls1;  stroke, 3, cc; fill,none;
    - bc3;  stroke, 3, cc; fill,none;
    - psxLn1Bc3; 8; red; 0.6
  rectEll1:
    - rect1;  stroke, 3, cc; fill,none;
    - ell1;   stroke, 3, cc; fill,none;
    - psxRec1Ell1; 8; red; 0.6
  tri1C1:
    - tri1; stroke, 3, cc; fill,none;
    - c1;   stroke, 3, cc; fill,none;
    - psxTri1C1; 8; red; 0.6
  tri1Bc2:
    - tri1; stroke, 3, cc; fill,none;
    - bc2;  stroke, 3, cc; fill,none;
    - psxtri1BC2; 8; red; 0.6
  tri1Bc3:
    - tri1; stroke, 3, cc; fill,none;
    - bc3;  stroke, 3, cc; fill,none;
    - psxtri1BC3; 8; red; 0.6
  tri1ELl1:
    - tri1; stroke, 3, cc; fill,none;
    - ell1; stroke, 3, cc; fill,none;
    - psxtri1Ell1; 8; red; 0.6
  c1ELl1:
    - c1; stroke, 3, cc; fill,none;
    - ell1; stroke, 3, cc; fill,none;
    - psxC1Ell1; 8; red; 0.6
  c1Bc2:
    - c1; stroke, 3, cc; fill,none;
    - bc2; stroke, 3, cc; fill,none;
    - psxC1Bc2; 8; red; 0.6
  c1Bc3:
    - c1; stroke, 3, cc; fill,none;
    - bc3; stroke, 3, cc; fill,none;
    - psxC1Bc3; 8; red; 0.6
  ellB2:
    - ell1; stroke, 3, cc; fill,none;
    - bc2;  stroke, 3, cc; fill,none;
    - psxEll1Bc2; 8; red; 0.6
  ellB3:
    - ell1; stroke, 3, cc; fill,none;
    - bc3;  stroke, 3, cc; fill,none;
    - psxEll1Bc3; 8; red; 0.6
  b2B3:
    - bc2; stroke, 3, cc; fill,none;
    - bc3;  stroke, 3, cc; fill,none;
    - psxBc2Bc3; 8; red; 0.6
```

画出来的图形大概为:

[wtht] # Fig Description...
noDraw: yes
name: xpointSet

points:
  p0: 0,0

shapes:
  line1: line; p0; -85
  ls1: line; line1; -100; 160;
  rect1: rect; bycenter; p0; 450; 300;
  tri1:  tri; -330, 60; 270, 0; -60, -390
  c1: circ; tri1.cin; tri1.rIn * 1.333
  ell1: ell; p0; 100; 375; -43
  bc2: b2; -270, 90; 160, -600; 198, 180 
  bc3: b3; -300, -300; 150, -240; -240, 300; 360, -120;

pointSeries:
  psxLn1Bc3:   ls1;  x; bc3;
  psxRec1Ell1: rect1;x; ell1;
  psxTri1C1:   tri1; x; c1;
  psxtri1Ell1: tri1; x; ell1;
  psxtri1BC2:  tri1; x; bc2;
  psxtri1BC3:  tri1; x; bc3;
  psxC1Ell1:   c1;   x; ell1;
  psxC1Bc2:    c1;   x; bc2;
  psxC1Bc3:    c1;   x; bc3;
  psxEll1Bc2:  ell1; x; bc2;
  psxEll1Bc3:  ell1; x; bc3;
  psxBc2Bc3:   bc2;  x; bc3;

jobs:
  ln1Bc3:
    - ls1;  stroke, 3, cc; fill,none;
    - bc3;  stroke, 3, cc; fill,none;
    - psxLn1Bc3; 8; red; 0.6
  rectEll1:
    - rect1;  stroke, 3, cc; fill,none;
    - ell1;   stroke, 3, cc; fill,none;
    - psxRec1Ell1; 8; red; 0.6
  tri1C1:
    - tri1; stroke, 3, cc; fill,none;
    - c1;   stroke, 3, cc; fill,none;
    - psxTri1C1; 8; red; 0.6
  tri1Bc2:
    - tri1; stroke, 3, cc; fill,none;
    - bc2;  stroke, 3, cc; fill,none;
    - psxtri1BC2; 8; red; 0.6
  tri1Bc3:
    - tri1; stroke, 3, cc; fill,none;
    - bc3;  stroke, 3, cc; fill,none;
    - psxtri1BC3; 8; red; 0.6
  tri1ELl1:
    - tri1; stroke, 3, cc; fill,none;
    - ell1; stroke, 3, cc; fill,none;
    - psxtri1Ell1; 8; red; 0.6
  c1ELl1:
    - c1; stroke, 3, cc; fill,none;
    - ell1; stroke, 3, cc; fill,none;
    - psxC1Ell1; 8; red; 0.6
  c1Bc2:
    - c1; stroke, 3, cc; fill,none;
    - bc2; stroke, 3, cc; fill,none;
    - psxC1Bc2; 8; red; 0.6
  c1Bc3:
    - c1; stroke, 3, cc; fill,none;
    - bc3; stroke, 3, cc; fill,none;
    - psxC1Bc3; 8; red; 0.6
  ellB2:
    - ell1; stroke, 3, cc; fill,none;
    - bc2;  stroke, 3, cc; fill,none;
    - psxEll1Bc2; 8; red; 0.6
  ellB3:
    - ell1; stroke, 3, cc; fill,none;
    - bc3;  stroke, 3, cc; fill,none;
    - psxEll1Bc3; 8; red; 0.6
  b2B3:
    - bc2; stroke, 3, cc; fill,none;
    - bc3;  stroke, 3, cc; fill,none;
    - psxBc2Bc3; 8; red; 0.6
[wtht]

[wtht] # Fig Description...
use: xpointSet
title: Line x Bezier3
noTag: yes
figure:ln1Bc3
[wtht]

[wtht] # Fig Description...
use: xpointSet
title: Rectangle x Ellipse
noTag: yes
figure:rectEll1
[wtht]

[wtht] # Fig Description...
use: xpointSet
title: Triangle x Circle
noTag: yes
figure:tri1C1
[wtht]

[wtht] # Fig Description...
use: xpointSet
title: Circle x Ellipse
noTag: yes
figure:c1ELl1
[wtht]

[wtht] # Fig Description...
use: xpointSet
title: Circle x Bezier2
noTag: yes
figure:c1Bc2
[wtht]

[wtht] # Fig Description...
use: xpointSet
title: Circle x Bezier3
noTag: yes
figure:c1Bc3
[wtht]

[wtht] # Fig Description...
use: xpointSet
title: Ellipse x Bezier2
noTag: yes
figure:ellB2
[wtht]

[wtht] # Fig Description...
use: xpointSet
title: Ellipse x Bezier3
noTag: yes
figure:ellB3
[wtht]

[wtht] # Fig Description...
use: xpointSet
title: Bezier2 x Bezier3
noTag: yes
figure:b2B3
[wtht]

[i] **请注意**, 因为部分曲线之间(比如圆弧和Bezier3曲线)交点的计算较为复杂，采用的是迭代算法，需要迭代的次数会随着曲线具体的参数不同而变化，极端情况下，交点需要较多的迭代次数才能解出，也有一定的概率无法求解出想要的交点(虽然概率较小)。故当某一个设计中有很多的交点需要求解时，渲染预览将会消耗更多的时间，直观的感觉就是反应会变慢。