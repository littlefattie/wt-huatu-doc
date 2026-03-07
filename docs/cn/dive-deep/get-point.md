# 获取点

点是我们定义图形的基础，也是我们指定各种图形位置的重要参考。除了显式地用$x$和$y$来给出一个点的座标外，我们也可以从已定义的基本图形中获取点。

## 计算点

计算点意为computed points，是通过给定一些基本图形的参数，来获取对应的点。在设计文件中，计算点将放在`cpoints`标签下。计算点使用的参数有两个，`arg`和`arg2`，`arg1`是首要参数，`arg2`是次要参数。在如下的示例中，我们定义一个计算点，取名为`cpt1`，它将在基本图形`shape1`上计算，两个参数分别为`arg1`和`arg2`。

```wtht
cpoints:
  cpt1: shape1; arg1; arg2;
```
根据基本图形的不同，我们计算点所使用的参数也会有所不同。

### 直线

直线计算点只使用`arg1`一个参数。如果它是`center`, `c`, `o`, `a`,`anchor`关键字中的一个，那么将得到定义直线时的**锚点**。如果它是一个数字，那么它将是一个长度，从锚点出发沿着直线角度方向行走特定长度所到达的点。

```wtht
shapes:
  line1: line; 0,0; -40
cpoints:
  cpO: line1; o
  cpA: line1; a
  cpL: line1; 120

draw:
  - circ; cpA; 15; ||| fill, green;
  - circ; cpO; 10; ||| fill, black;
  - circ; cpL; 5;  ||| fill, blue;
```

[wtht] # Get Point from Line
noTitle: yes
shapes:
  line1: line; 0,0; -40
  ls1: line; line1; -80; 180;
cpoints:
  cpO: line1; o
  cpA: line1; a
  cpL: line1; 120

draw:
  - circ; cpA; 15; ||| fill, green;
  - circ; cpO; 10; ||| fill, black;
  - circ; cpL; 5; ||| fill, blue;
  - ls1; stroke,1,black; dash, 3,3
  - label; cpO; anchor; tl; offset;  15,  15; w; 20;h;20; color; black; ||| o
  - label; cpA; anchor; br; offset; -15, -15; w; 20;h;20; color; green; ||| a
  - label; cpL; anchor; tl; offset;   3,   3; w; 40;h;20; color; blue;  ||| 120
[wtht]

### 线段

线段的计算点也只需要`arg1`一个参数。如果它是`s`,`start`中的一个，将得到线段的起点；如果它是`e`,`end`中的一个，将得到线段的终点；如果它是`m`,`mid`,`middle`中的一个，将得到线段的中点；如果它是$[0,1]$之间的数字$t$，那么将得到从起点至终点间与线段长度成比例的一个点。很显然当$t=0.5$时将是中点，$t=0$时是起点，$t=1$时是终点。

```wtht
shapes:
  ls1: ls; 0,0; 300, -200

cpoints:
  cpS: ls1; s
  cpE: ls1; end
  cpM: ls1; mid
  cpT: ls1; 0.666

draw:
  - circ; cpS; 6 ||| fill, red
  - circ; cpE; 6 ||| fill, blue
  - circ; cpM; 6 ||| fill, magenta
  - circ; cpT; 6 ||| fill, orange
```

[wtht] # Get point from Lineseg
noTitle: yes
markers:
  arrow1: arrow-simple; size; 6;

shapes:
  ls1: ls; 0,0; 300, -200

cpoints:
  cpS: ls1; s
  cpE: ls1; end
  cpM: ls1; mid
  cpT: ls1; 0.666

draw:
  - ls1; stroke, 3, black; marker; arrow1;
  - circ; cpS; 6 ||| fill, red
  - circ; cpE; 6 ||| fill, blue
  - circ; cpM; 6 ||| fill, magenta
  - circ; cpT; 6 ||| fill, orange
  - label; cpS; anchor; tl;w; 20; h; 20; color; red;     ||| s
  - label; cpE; anchor; tl;w; 20; h; 20; color; blue;    ||| e
  - label; cpM; anchor; tl;w; 20; h; 25; color; magenta; ||| m
  - label; cpT; anchor; tl;w; 60; h; 20; color; orange;  ||| 0.666
[wtht]

### 圆

从圆计算点只需要`arg1`一个参数，如果参数为表示方位的关键字`n,s,e,w,ne,se,nw,sw,c`（`c`表示圆心，center）中的一种，那么将取出圆上对应方位的点。如果参数是数字，那么将计算对应角度的点。下面是示例：

```wtht
shapes:
  c1: circ; p0; R

cpoints:
  cpO:   c1; c;
  cpN:   c1; n;
  cpS:   c1; s;
  cpE:   c1; e;
  cpW:   c1; w;
  cpNE:  c1; ne;
  cpNW:  c1; nw;
  cpSE:  c1; se;
  cpSW:  c1; sw;
  cp300: c1; 300
  cp199: c1; 199.99

pointSeries:
  ps1:cpO; cpN; cpS; cpE; cpW; cpNE; cpNW; cpSE; cpSW;

draw:
  - c1; stroke, 2, black; fill, none;
  - ps1; 4; blue;
  - circ; cp300; 4; ||| fill, red;
  - circ; cp199; 4; ||| fill, magenta;
```

[wtht] # Cpoint on Circle
noTitle: yes

# wt-huatu default empty file
vars:
  R: 200

points:
  p0: 0,0

shapes:
  c1: circ; p0; R

cpoints:
  cpO:   c1; c;
  cpN:   c1; n;
  cpS:   c1; s;
  cpE:   c1; e;
  cpW:   c1; w;
  cpNE:  c1; ne;
  cpNW:  c1; nw;
  cpSE:  c1; se;
  cpSW:  c1; sw;
  cp300: c1; 300
  cp199: c1; 199.99

pointSeries:
  ps1:cpO; cpN; cpS; cpE; cpW; cpNE; cpNW; cpSE; cpSW;

draw:
  - c1; stroke, 2, black; fill, none;
  - ps1; 4; blue;
  - label; cpO; anchor; tm; h; 30; w; 25 ||| c
  - label; cpN; anchor; bm; h; 30; w; 25 ||| n
  - label; cpS; anchor; tm; h; 30; w; 25 ||| s
  - label; cpE; anchor; ml; h; 30; w; 25 ||| e
  - label; cpW; anchor; mr; h; 30; w; 25 ||| w
  - label; cpNE; anchor; bl; h; 30; w; 25 ||| ne
  - label; cpSE; anchor; tl; h; 30; w; 25 ||| se
  - label; cpNW; anchor; br; h; 30; w; 25 ||| nw
  - label; cpSW; anchor; tr; h; 30; w; 25 ||| sw
  - circ; cp300; 4; ||| fill, red;
  - label; cp300; anchor; bl; h; 30; w; 32; color; red; ||| 300
  - circ; cp199; 4; ||| fill, magenta;
  - label; cp199; anchor; br; h; 30; w; 52; color; magenta; ||| 199.99

[wtht]


### 圆环

从圆环计算点可以使用一个参数，也可以使用两个参数，当使用一个参数时，如果它是`cin`,`cout`,都将得到圆环的圆心，如果是一个数字，那么它将是一个角度，将得到**外圆**上对应角度的点。如果使用两个参数，则第一个参数只能是`in`,或`out`,第二个参数是一个表示角度的数字。`in`对应在内圆上取点，`out`对应在外圆上取点。如果只给了`in`或`out`,而角度参数并未给出，则将返回圆心。

```wtht
vars:
  R1: 160
  R2: R1 * 1.5
points:
  p0: 0,0
shapes:
  ring1: ring; p0; R1; R2;

cpoints:
  cpCin:     ring1; cin;
  cpCout:    ring1; cout;
  cpIn:      ring1; in;
  cpOut:     ring1; out;
  cpIn_300:  ring1; in;  300
  cpOut_200: ring1; out; 200
  cp255:     ring1; 255

draw:
  - ring1; stroke, 3, black; fill, none; marker;
  - circ; cpIn_300;  6;  ||| fill, red;
  - circ; cpOut_200; 6;  ||| fill, magenta;
  - circ; cp255;     6;  ||| fill, blue;
  - circ; cpCin;     36; ||| fill, black;
  - circ; cpCout;    28; ||| fill, yellow;
  - circ; cpIn;      20; ||| fill, red;
  - circ; cpOut;     12; ||| fill, black;
```

[wtht] # Get point on Ring
noTitle: yes

vars:
  R1: 160
  R2: R1 * 1.5
points:
  p0: 0,0
shapes:
  ring1: ring; p0; R1; R2;

cpoints:
  cpCin:     ring1; cin;
  cpCout:    ring1; cout;
  cpIn:      ring1; in;
  cpOut:     ring1; out;
  cpIn_300:  ring1; in;  300
  cpOut_200: ring1; out; 200
  cp255:     ring1; 255

draw:
  - line; p0.x - R2 * 1.2, p0.y; p0.x + R2 * 1.2, p0.y; ||| stroke, 1, black; dash, 3,3; marker; arrow1;
  - ring1; stroke, 3, black; fill, none; marker;
  # Point on Inner Circle
  - line; p0; cpIn_300;  ||| stroke, 1, red; dash, 3,3;
  - label; cpIn_300; anchor; bl; w; 60; h; 30; color; red ||| in, 300
  - circ; cpIn_300; 6;   ||| fill, red;
  # Point on Outer Circle
  - line; p0; cpOut_200; ||| stroke, 1, magenta; dash, 3,3;
  - label; cpOut_200; anchor; br; w; 80; h; 30; color; magenta ||| out, 200
  - circ; cpOut_200; 6;  ||| fill, magenta;
  # Point without specify inner/outer circle
  - line; p0; cp255; ||| stroke, 1, blue; dash, 3,3;
  - label; cp255; anchor; br; w; 50; h; 30; ||| 255
  - circ; cp255; 6;      ||| fill, blue;
  # Center Point
  - circ; cpCin; 36;  ||| fill, black;
  - circ; cpCout; 28; ||| fill, yellow;
  - circ; cpIn; 20;   ||| fill, red;
  - circ; cpOut; 12;  ||| fill, black;
  - label; cpCin; anchor; tm; w; 120; h; 30; offset; 0, 40 ||| cin, cout, in, out

[wtht]

### 椭圆

从椭圆计算点，我们只使用一个参数`arg1`，当它是一个数字时，取得的点是椭圆的自然角对应的点，即点$(R_x\cos\theta, R_y\sin\theta)$（当然，椭圆的旋转角也应当考虑进去），当`arg1`为表示方位的关键字时取的是下列点：
- `c`(或`center`): 椭圆的圆心
- `n`: 椭圆上最靠北的点（y最小的点）
- `s`: 椭圆上最靠南的点（y最大的点）
- `e`: 椭圆上最靠东的点（x最大的点）
- `w`: 椭圆上最靠西的点（x最小的点）
- `nw`: 椭圆上与西北向射线的交点
- `ne`: 椭圆上与东北向射线的交点
- `sw`: 椭圆上与西南向射线的交点
- `se`: 椭圆上与东南向射线的交点

```wtht
vars:
  rx: 200
  ry: rx * 0.76
  deg1: -32
points:
  p0: 0,0
shapes:
  ell1: ell; p0; rx; ry; deg1;
cpoints:
  cpC:  ell1; c;
  cpE:  ell1; e;
  cpN:  ell1; n;
  cpW:  ell1; w;
  cpS:  ell1; s;
  cpNE: ell1; ne;
  cpSE: ell1; se;
  cpNW: ell1; nw
  cpSW: ell1; sw
  cp90: ell1; 90

pointSeries:
  psNSWE: cpE; cpS; cpN; cpW;
  ps8: cpNE; cpSE; cpNW; cpSW;

draw:
  - ell1; fill, none; stroke, 3, black;
  - psNSWE; 6; blue;
  - ps8; 6; maroon;
  - label; cpE;  anchor; ml; w; 40; ||| e
  - label; cpW;  anchor; mr; w; 40; ||| w
  - label; cpN;  anchor; bm; w; 40; h; 35; ||| n
  - label; cpS;  anchor; tm; w; 40; h; 35; ||| s
  - label; cpNE; anchor; ml; w; 45; h; 35; ||| ne
  - label; cpNW; anchor; mr; w; 45; h; 35; ||| nw
  - label; cpSW; anchor; mr; w; 45; h; 35; ||| sw
  - label; cpSE; anchor; ml; w; 45; h; 35; ||| se
  - circ;  cpC;  8; ||| fill, black;
  - label; cpC;  anchor; bm; w; 45; h; 45; ||| c
  - circ;  cp90; 6; ||| fill, red;
  - label; cp90; anchor; tm; w; 30; h; 40; red; ||| 90
```

[wtht] # Get point from Ellipse
noTitle: yes

vars:
  rx: 200
  ry: rx * 0.76
  deg1: -32
points:
  p0: 0,0
shapes:
  ell1: ell; p0; rx; ry; deg1;
  lnx: line; p0; 0;
  lnRx: line; p0; deg1;
  lnRy: line; p0; deg1 - 90;
  lnYpX: line; p0; -45
  lnYmX: line; p0;  45
cpoints:
  cpC: ell1; c;
  cpE: ell1; e;
  cpN: ell1; n;
  cpW: ell1; w;
  cpS: ell1; s;
  cpNE: ell1; ne;
  cpSE: ell1; se;
  cpNW: ell1; nw
  cpSW: ell1; sw
  cpYpX: lnYpX; -20
  cp90: ell1; 90

pointSeries:
  psNSWE: cpE; cpS; cpN; cpW;
  ps8: cpNE; cpSE; cpNW; cpSW;

blocks:
  blkNSWEMarks:
    - line; cpE; 90; -50; 50; |||
    - line; cpN;  0; -50; 50; |||
    - line; cpW; 90; -50; 50; |||
    - line; cpS;  0; -50; 50; |||
  blkCrossLines:
    - line; lnYpX; -rx * 1.2; rx * 1.2 |||
    - line; lnYmX; -rx * 1.2; rx * 1.2 |||
draw:
  - line; lnx; -rx * 1.2; rx * 1.2 ||| stroke, 1, red; dash, 3,3;
  - line; lnRx; -rx * 1.2; rx * 1.2 ||| stroke, 1, black; dash, 3,3;
  - line; lnRy;-ry * 1.2; ry * 1.2 ||| stroke, 1, black; dash, 3,3;
  - anglemark; lnx.20; lnx.a; lnRx.30; lineWidth; 2; size; 65;arrow-simple; showdeg; labelpos; 0.3;
  - ell1; fill, none; stroke, 3, black;
  - psNSWE; 6; blue;
  - blkNSWEMarks; stroke, 1, magenta
  - blkCrossLines; stroke,0.5, maroon;
  - ps8; 6; maroon;
  - anglemark; lnx.20; lnx.a; lnYmX.30; lineWidth; 2; size; 75;arrow-simple; anchor; tl; showdeg; labelpos; 0.4; maroon; textcolor; maroon;
  - anglemark; lnx.20; lnx.a; cpYpX; lineWidth; 2; size; 110;arrow-simple; anchor; tr; showdeg; labelpos; 0.65; lh; 30; maroon; textcolor; maroon;
  - label; cpE;  anchor; ml; w; 40; ||| e
  - label; cpW;  anchor; mr; w; 40; ||| w
  - label; cpN;  anchor; bm; w; 40; h; 35; ||| n
  - label; cpS;  anchor; tm; w; 40; h; 35; ||| s
  - label; cpNE; anchor; ml; w; 45; h; 35; ||| ne
  - label; cpNW; anchor; mr; w; 45; h; 35; ||| nw
  - label; cpSW; anchor; mr; w; 45; h; 35; ||| sw
  - label; cpSE; anchor; ml; w; 45; h; 35; ||| se
  - circ;  cpC; 8; ||| fill, black;
  - label; cpC; anchor; bm; w; 45; h; 45; ||| c
  - circ; cp90; 6; ||| fill, red;
  - label; cp90; anchor; tm; w; 30; h; 40; red; ||| 90
[wtht]


### 三角形

从三角形计算点，可以提供一个参数，也可以提供两个参数。如果只提供一个参数，将返回三角形的特定点:

- `cin`: 三角形内切圆的圆心
- `cout`: 三角形外接圆的圆心
- `cg`(或`cgravity`): 三角形的重心
- `a`: 定义三角形时给定的第一个点
- `b`: 定义三角形时给定的第二个点
- `c`: 定义三角形时给定的第三个点

如果提供第二个参数，则第一个参数需要为`1`,`2`,`3`,中的一个，第二个参数为$t$,$t\in[0,1]$，第一个参数1/2/3依次表示三角形的三条边(即线段$\overrightharpoon{AB}$, $\overrightharpoon{BC}$, $\overrightharpoon{CA}$)，第二个参数$t$表示从线段起点到终点与线段长度成比例的位置，如果$t$未指定，则表示线段起点。

```wtht
points:
  pA: 300, -300;
  pB: -200, -40;
  pC: 320, 140;

shapes:
  tri1: triangle; pA; pB; pC;

cpoints:
  cpIn:    tri1; cin;
  cpOut:   tri1; cout;
  cpG:     tri1; cg; # cg could be cgravity
  cpA:     tri1; a;
  cpB:     tri1; b;
  cpC:     tri1; c;
  cp1:     tri1; 1;
  cp2:     tri1; 2;
  cp3:     tri1; 3;
  cp1_3rd: tri1; 1; 1/3;
  cp2_p4:  tri1; 2; 0.4;
  cp3_p7:  tri1; 3; 0.7;

pointSeries:
  pAll: cpA; cpB; cpC;

draw:
  - tri1; stroke, 3, black; fill, none; linejoin; round;
  - pAll; 6; blue; 0.6
  - circ; cpIn;    6; ||| fill, red
  - circ; cpOut;   6; ||| fill, magenta
  - circ; cpG;     6; ||| fill, black;
  - circ; cp1_3rd; 6; ||| fill, gold;
  - circ; cp2_p4;  6; ||| fill, gold;
  - circ; cp3_p7;  6; ||| fill, gold;

```

[wtht] # Get Point from Triangle
noTitle: yes

points:
  pA: 300, -300;
  pB: -200, -40;
  pC: 320, 140;

shapes:
  tri1: triangle; pA; pB; pC;
  cIn1: circ; tri1.cin; tri1.rin;
  cOut1: circ; tri1.cout; tri1.rout;
  lsBc: ls; pB; pC;
  lsAb: ls; pA; pB;

cpoints:
  cpIn: tri1; cin;
  cpOut: tri1; cout;
  cpG: tri1; cg; # cg could be cgravity
  cpA: tri1; a;
  cpB: tri1; b;
  cpC: tri1; c;
  cp1: tri1; 1;
  cp2: tri1; 2;
  cp3: tri1; 3;
  cp1_3rd: tri1; 1; 1/3;
  cp2_p4: tri1; 2; 0.4;
  cp3_p7: tri1; 3; 0.7;

pointSeries:
  pAll: cpA; cpB; cpC;
  psG: lsBc.m; lsAb.m;

draw:
  # The triangle
  - tri1; stroke, 3, black; fill, none; linejoin; round;
  - pAll; 6; blue; 0.6
  - label; cpA; anchor; bl; w; 20; h; 30; ||| a
  - label; cpB; anchor; mr; w; 30; h; 30; ||| b
  - label; cpC; anchor; tl; w; 20; h; 20; ||| c
  # inner circle
  - circ; cpIn; 6 ||| fill, red
  - label; cpIn; anchor; ml; w; 40; h; 20; ||| cin
  - cIn1;  fill, none; stroke, 1, red;     dash, 3,3;
  - dimmark; cpIn; cIn1.311; gap; 0; linepos; 0; nostartarrow; nostartbar; noendbar;lineWidth;2; linecolor; red; textcolor; red; label;  $R_{in}$
  # outer circle
  - circ; cpOut; 6 ||| fill, magenta
  - label; cpOut; anchor; mr; w; 60; h; 20; ||| cout
  - cOut1; fill, none; stroke, 1, magenta; dash, 4,4;
  - dimmark; cpOut; cOut1.222; gap; 0; linepos; 0; nostartarrow; nostartbar; noendbar;lineWidth;2; linecolor; magenta; textcolor; magenta; label;  $R_{out}$
  - ls; cpA; lsBc.m; ||| stroke, 1, black; dash, 3,3;
  - ls; cpC; lsAb.m; ||| stroke, 1, black; dash, 3,3;
  # Gravity Point
  - circ; cpG; 6 ||| fill, black;
  - label; cpG;   anchor; bm; w; 40; h; 50; ||| cg
  - psG; 6; black;
  # Point On Edges
  - circ; cp1_3rd; 6; ||| fill, gold;
  - label; cp1_3rd; anchor; br; w; 60; h; 20; color; gold ||| 1, 1/3
  - dimmark; cp1; cp1_3rd; lineWidth; 1.5; barlen; 40; linepos; 0.7; label; $1/3L_{AB}$
  - circ; cp2_p4; 6; ||| fill, gold;
  - label; cp2_p4; anchor; tr; w; 60; h; 20; color; gold ||| 2, 0.4
  - circ; cp3_p7; 6; ||| fill, gold;
  - dimmark; cp2; cp2_p4; lineWidth; 1.5; barlen; 50; linepos; 0.8; label; $0.4L_{BC}$
  - label; cp3_p7; anchor; bl;offset; 0, -8; w; 60; h; 20; color; gold ||| 3, 0.7
  - dimmark; cp3; cp3_p7; lineWidth; 1.5; barlen; 40; linepos; 0.7; label; $0.7L_{CA}$
[wtht]

### 矩形

从矩形计算点可以使用一个表示方位的参数，如下：

- `c`(或`center`): 将获取矩形的**中心点**
- `n`(或`nm`,`top-middle`,`topmiddle`):将获取矩形顶部中心点
- `s`(或`sm`,`bottom-middle`,`bottommiddle`):将获取矩形底部中心点
- `e`(或`em`,`right-middle`,`rightmiddle`):将获取矩形右侧中心点
- `w`(或`wm`,`left-middle`,`leftmiddle`):将获取矩形左侧中心点
- `nw`(或`topleft`,`top-left`): 将获取矩形左上顶点
- `ne`(或`topright`,`top-right`): 将获取矩形右上顶点
- `sw`(或`bottomleft`,`bottom-left`): 将获取矩形左下顶点
- `se`(或`bottomright`,`bottom-right`): 将获取矩形右下顶点

或者给定第一个参数为`north`(或`top`),`west`(或`left`),`south`(或`bottom`),`east`(或`right`)中的一个，第二个为$[0,1]$之间的数字，这样，将从矩形对应边上取点。 `north`和`south`将从左侧开始测量距离，`west`和`east`将从顶部开始测量距离。距离为矩形**宽**或**高**的比例。

```wtht
vars:
  w: 500
  h: 300

shapes:
  rect1: rect; 0,0; w; h;

cpoints:
  cpTM: rect1; top-middle;    # `top-middle` could be
                              # `topmiddle`, `nm`, or just `n`
  cpS:  rect1; s;             # `s` could be `sm`,
                              # `bottommiddle`, or `bottom-middle`
  cpW:  rect1; leftmiddle;    # `leftmiddle` could be `w`,
                              # `wm`, or `left-middle`
  cpEM: rect1; em;            # `em` could `e`, `rightmiddle`,
                              # or `right-middle`
  cpC:  rect1; c;             # `c` could be `center`
  cpNE: rect1; ne;            # `ne` could be `topright`, or `top-right`
  cpNW: rect1; top-left;      # `top-left` could be `topleft`, or `nw`
  cpSE: rect1; bottomright;   # `bottomright` could be
                              # `bottom-right` or `se`d
  cpSW: rect1; sw;            # `sw` could be `bottom-left` or `bottomleft`
  cp_north_3rd:  rect1; north;   1/3;    # `north` could be `top`
  cp_south_p6:   rect1; south;   0.6;    # `south` could be `bottom`
  cp_left_p333:  rect1; left;  0.333;    # `left`  could be `west`
  cp_right_p777: rect1; right; 0.777;    # `right` could be `east`
pointSeries:
  psDirs: cpTM; cpS; cpW; cpEM; cpNE; cpNW; cpSE; cpSW;
draw:
  - rect1; stroke; 3, black; fill, none;
  - circ; cpC; 6; ||| fill, red;
  - psDirs; 6; blue;
  - circ; cp_north_3rd; 6; ||| fill, maroon;
  - circ; cp_south_p6; 6; ||| fill, maroon;
  - circ; cp_left_p333; 6; ||| fill, maroon;
  - circ; cp_right_p777; 6; ||| fill, maroon;
```

[wtht] # Get Point from Rectangle
noTitle: yes

vars:
  w: 500
  h: 300

shapes:
  rect1: rect; 0,0; w; h;

cpoints:
  cpTM: rect1; top-middle;    # `top-middle` could be `topmiddle`, `nm`, or just `n`
  cpS:  rect1; s;             # `s` could be `sm`, `bottommiddle`, or `bottom-middle`
  cpW:  rect1; leftmiddle;    # `leftmiddle` could be `w`, `wm`, or `left-middle`
  cpEM: rect1; em;            # `em` could `e`, `rightmiddle`, or `right-middle`
  cpC:  rect1; c;             # `c` could be `center`
  cpNE: rect1; ne;            # `ne` could be `topright`, or `top-right`
  cpNW: rect1; top-left;      #  `top-left` could be `topleft`, or `nw`
  cpSE: rect1; bottomright;   # `bottomright` could be `bottom-right` or `se`d
  cpSW: rect1; sw;            # `sw` could be `bottom-left` or `bottomleft`
  cp_north_3rd:  rect1; north;   1/3;    # `north` could be `top`
  cp_south_p6:   rect1; south;   0.6;    # `south` could be `bottom`
  cp_left_p333:  rect1; left;  0.333;    # `left` could be `west`
  cp_right_p777: rect1; right; 0.777;    # `right` could be `east`
pointSeries:
  psDirs: cpTM; cpS; cpW; cpEM; cpNE; cpNW; cpSE; cpSW;
draw:
  - line; cpNW; cpSE; ||| stroke, 1, grey; dash, 3,3;
  - line; cpSW; cpNE; ||| stroke, 1, grey; dash, 3,3;
  - rect1; stroke; 3, black; fill, none;
  - circ; cpC; 6; ||| fill, red;
  - psDirs; 6; blue;
  - label; cpC;  w; 40; color; red;  anchor; tm; offset; 0, 8 ||| c
  - label; cpTM; w; 40; color; blue; anchor; bm; offset; 0, -8 ||| n
  - label; cpS;  w; 40; color; blue; anchor; tm; offset; 0, 8 ||| s
  - label; cpW;  w; 40; color; blue; anchor; mr;  ||| w
  - label; cpEM; w; 40; color; blue; anchor; ml;  ||| e
  - label; cpNW; w; 40; color; blue; anchor; br; offset; 0, -5 ||| nw
  - label; cpNE; w; 40; color; blue; anchor; bl; offset; 0, -5 ||| ne
  - label; cpSW; w; 40; color; blue; anchor; tr;  ||| sw
  - label; cpSE; w; 40; color; blue; anchor; tl;  ||| se
  - dimmark; cpNW; cpNE; gap; 40; lineWidth; 2; label;  $W$; left; barlen; 30;linepos; 0.7;
  - circ; cp_north_3rd; 6; ||| fill, maroon;
  - dimmark; cpNW; cp_north_3rd; lineWidth; 2; label; north, 1/3; labelwidth; 80; left; barlen; 40; linepos; 0.6
  - circ; cp_south_p6; 6; ||| fill, maroon;
  - dimmark; cpSW; cp_south_p6; lineWidth; 2; label; south, 0.6; labelwidth; 80; barlen; 40; linepos; 0.6
  - dimmark; cpNW; cpSW; gap; 70; lineWidth; 2; label;  $H$; labelwidth; 30; barlen; 30;linepos; 0.7;
  - circ; cp_left_p333; 6; ||| fill, maroon;
  - dimmark; cpNW; cp_left_p333; lineWidth; 2; label; left, 0.333; labelwidth; 60; lh; 40; barlen; 65; linepos; 0.35;
  - circ; cp_right_p777; 6; ||| fill, maroon;
  - dimmark; cpSW; cp_south_p6; lineWidth; 2; label; south, 0.6; labelwidth; 80; barlen; 40; linepos; 0.6
  - dimmark; cpNE; cp_right_p777; lineWidth; 2; left; label; right, 0.777; labelwidth; 50; lh; 40; barlen; 65; linepos; 0.65;

[wtht]

### 多线段

从多线段计算点可以使用一个参数，如果它是`s`(或`start`，`first`)，将获得多线段的起点，如果它是`e`(或`end`,`last`)，将获取多线段的终点。如果第一个参数是1到N-1之间的整数n(N为多线段的顶点数，N-1即多线段的线段数), 第二个参数未提供时将获取第n条线段的起点，如果第二个参数是$[0,1]$间的数字，则将获取第n条线段上从起点到终点与线段长度成比例的那个点。即$P(n,t) = LS_n(t) = P_{n,start} + t \cdot L({LS_n})$.

```wtht

# --- Points ------
points:
  p0: 0, 0
  p1: p0.x + 120, p0.y - 170
  p2: p1.x + 50, p1.y + 120
  p3: p2.x + 120, p2.y + 120
  p4: p3.x + 130, p3.y - 220
  p5: p4.x + 116, p4.y + 120
  p6: p5.x + 160, p5.y

# --- Shapes ------
shapes:
  pln1: pln; p0; p1; p2; p3; p4; p5; p6;

cpoints:
  cpStart:pln1; s;        # `s` could be `start`, or `first`
  cpEnd:  pln1; end;      # `end` could be `e`, or `last`
  cp3:    pln1; 3;
  cp3_p4: pln1; 3; 0.4;

# --- Draw --------
draw:
  - pln1; stroke, black, 2; linejoin; round; fill, none;
  - circ;  cpStart;8; ||| fill, royalblue, 0.6;
  - label; cpStart; anchor; br; w; 50; left; ||| $P_{start}$
  - circ;  cpEnd;  8; ||| fill, royalblue, 0.6;
  - label; cpEnd; anchor; tm; w; 50; h; 40;  ||| $P_{end}$
  - circ;  cp3;    8; ||| fill, royalblue, 0.6;
  - label; cp3; anchor; tr; w; 50; h; 40;    ||| $cp3$
  - circ;  cp3_p4; 8; ||| fill, maroon;
  - label; cp3_p4; anchor; tr; w; 50; h; 40; ||| $3, 0.4$
```

[wtht] # Get point from Polyline
noTitle: yes
# --- Points ------
points:
  p0: 0, 0
  p1: p0.x + 120, p0.y - 170
  p2: p1.x + 50, p1.y + 120
  p3: p2.x + 120, p2.y + 120
  p4: p3.x + 130, p3.y - 220
  p5: p4.x + 116, p4.y + 120
  p6: p5.x + 160, p5.y

pointSeries:
  ps1: p0; p1;  p2; p3; p4; p5; p6;
# --- Shapes ------
shapes:
  pln1: pln; p0; p1; p2; p3; p4; p5; p6;

cpoints:
  cpStart:pln1; s;        # `s` could be `start`, or `first`
  cpEnd:  pln1; end;      # `end` could be `e`, or `last`
  cp3:    pln1; 3;
  cp3_p4: pln1; 3; 0.4;

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
  - line; pln1.3; pln1.4; ||| stroke, 8; royalblue; opaque; 0.6;
  - label; cpStart; anchor; br; w; 50; left; ||| $P_{start}$
  - circ; cpStart; 8; ||| fill, royalblue, 0.6;
  - label; cpEnd; anchor; tm; w; 50; h; 40; ||| $P_{end}$
  - circ; cpEnd;   8; ||| fill, royalblue, 0.6;
  - circ; cp3;  8; ||| fill, royalblue, 0.6;
  - label; cp3; anchor; tr; w; 50; h; 40; ||| $cp3$
  - circ; cp3_p4; 8; ||| fill, maroon;
  - label; cp3_p4; anchor; tr; w; 50; h; 40; ||| $3, 0.4$
  - label; cp3_p4; anchor; ml; w; 60; h; 40; 
    offset; 30,8; blue; ||| 多线段的第3段
  - dimmark; cp3; p3; gap; 50; linewidth; 1.5; barwidth; 1.5; linepos; 0.5; label; $L$
  - dimmark; cp3; cp3_p4; left; gap; 40; linewidth; 1.5; barwidth; 1.5; linepos; 0.5; label; $0.4L$
[wtht]


### 多边形

从多边形计算点时，第一个参数`arg1`只能是1到N之间的整数n（N是多边形的边数），如果第二个参数`arg2`没提供，则获取的是第n条边的起始点，如果第二个参数`arg2`是$[0,1]$之间的数字，则获取的点是多边形第n条边上从起点到终点与边长成比例的点。

```wtht

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
cpoints:
  cp2:      plg1; 2
  cp3_p666: plg1; 3; 0.666

# --- Draw --------
draw:
  - plg1; fill, yellow, 0.3; stroke, 3; black;
  - circ;  cp2; 8; ||| fill, blue;
  - label; cp2; anchor; bl; w; 30; offset; 5, -14 ||| $cp2$
  - circ;  cp3_p666; 12; ||| fill, blue;
  - label; cp3_p666; anchor; tl; w; 60; offset; 12, 4 ||| $3, 0.666$
```

[wtht] # Get Point from Polygon
noTitle: yes

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
cpoints:
  cp2: plg1; 2
  cp3_p666: plg1; 3; 0.666

# --- Draw --------
draw:
  - plg1; fill, yellow, 0.3; stroke, 3; black;
  - label; p1; anchor; tl; w; 30; ||| $P_1$
  - label; p2; anchor; tr; w; 30; ||| $P_2$
  - label; p3; anchor; mr; w; 30; ||| $P_3$
  - label; p4; anchor; bm; w; 30; h; 25 ||| $P_4$
  - label; p5; anchor; bl; w; 30; ||| $P_5$
  - label; p6; anchor; ml; w; 30; ||| $P_6$
  - line; plg1.3; plg1.4; ||| stroke, 12; royalblue; opaque; 0.6;
  - circ; cp2; 8; ||| fill, blue;
  - label; cp2; anchor; bl; w; 30; offset; 5, -14 ||| $cp2$
  - circ; cp3_p666; 12; ||| fill, blue;
  - label; cp3_p666; anchor; tl; w; 60; offset; 12, 4 ||| $3, 0.666$
  - label; cp3_p666; w; 70; h; 45;anchor; br; offset; -10,8; blue;||| 多边形的第3条边
  - dimmark; p3; p4; linewidth; 2; left; barwidth; 1.5; barlen; 100; linepos; 0.8; label; $L$
  - dimmark; p3; cp3_p666; linewidth; 2; barwidth; 1.5; barlen; 40; linepos; 0.8; label; $0.666L$
[wtht]


### 圆弧

从圆弧计算点时，只是用一个参数`arg1`。如果它是下列值时将取得圆弧对应的点。

- `c`(或`center`): 将获取圆弧/椭圆弧的圆心
- `m`(或`mid`, `middle`): 将获取圆弧的中点，即圆弧的起始角和终到角的中间值所对应的点
- `s`(或`start`): 将获取圆弧的起点
- `e`(或`end`): 将获取圆弧的终点

如果参数`arg1`是一个在$[0,1]$之间的数字$t$，则将获取圆弧上从起点到终点间与**自然角**成比例的角对应的那个点，即 ($D$ 表示角度)

$$
D_t = D_{start} \cdot (1-t) + D_{end} \cdot t \\
P(t) = P_{arc}(D_t)
$$

```wtht
vars:
  R: 150
  rx: 150
  ry: rx * 0.76
  deg1: -32
points:
  p0: 0,0
shapes:
  circ1: circ; p0; R
  arc1:  arc;  circ1; -40; -170; ccw;
  ell1:  ell;  p0; rx; ry; deg1;
  arc2:  arc;  ell1;   20;  210;  cw;
  ell2:  ell;  p0; rx * 1.2; ry; -deg1;

cpoints:
  cpArc1C:    arc1; c;    # `c` could be `center`
  cpArc1S:    arc1; s;    # `s` could be `start`
  cpArc1E:    arc1; e;    # `e` could be `end`
  cpArc1M:    arc1; m;    # `m` could be `mid`
  cpArc1P333: arc1; 0.333;

  cpArc2C:    arc2; c;
  cpArc2S:    arc2; s;
  cpArc2E:    arc2; e;
  cpArc2M:    arc2; m;
  cpArc2P333: arc2; 0.333;
  
  pArc3Start: ell2; 25;
  pArc3End:   ell2; 100;

pointSeries:
  psArc1: cpArc1S; cpArc1M; cpArc1E; 
  psArc2: cpArc2S; cpArc2M; cpArc2E; 

shapes2:
  arc3: arc; pArc3Start; rx * 1.2; ry; -deg1; small; ccw; pArc3End;
  ell3: ell; arc3.c; rx* 1.2; ry; -deg1;

cpoints2:
  cpArc3C:    arc3; c;
  cpArc3S:    arc3; s;
  cpArc3E:    arc3; e;
  cpArc3M:    arc3; m;
  cpArc3P333: arc3; 0.333;

pointSeries2:
  psArc3: cpArc3S; cpArc3M; cpArc3E; 

blocks:
  blkArc1:
    - circ1; stroke,1, black; dash, 3,3; fill, none;
    - arc1;  stroke, 6, royalblue; opaque, 0.6; fill, none;
    - psArc1; 6; black;
    - circ; cpArc1C;    6; ||| fill, blue
    - circ; cpArc1P333; 6; ||| fill, red
  blkArc2:
    - ell1; stroke, 1, black; dash, 3,3; fill, none;
    - arc2; stroke, 6, royalblue; opaque, 0.6; fill, none;
    - psArc2; 6; black;
    - circ; cpArc2C;    6; ||| fill, blue
    - circ; cpArc2P333; 6; ||| fill, red
  blkArc3:
    - ell2; stroke, 1, black; dash, 3,3; fill, none;
    - ell3; stroke, 1, red;   dash, 3,3; fill, none;
    - arc3; stroke, 6; royalblue; opaque, 0.6; fill, none;
    - psArc3; 6; black;
    - circ; cpArc3C;    6; ||| fill, blue
    - circ; cpArc3P333; 6; ||| fill, red
draw:
  - blkArc1
  - blkArc2; move; R * 2.6, 0;
  - blkArc3; move; R * 1.3, ry * 2.5;
```

[wtht] # Get Point from Arc
noTitle: yes

vars:
  R: 150
  rx: 150
  ry: rx * 0.76
  deg1: -32
points:
  p0: 0,0
shapes:
  circ1: circ; p0; R
  arc1:  arc;  circ1; -40; -170; ccw;
  ell1:  ell;  p0; rx; ry; deg1;
  arc2:  arc;  ell1;   20;  210;  cw;
  arc1Arrow:  arc; arc1.c; R * 0.7; R * 0.7; 0; -60; -150; ccw
  arc2Arrow:  arc; arc2.c; rx * 0.7; ry * 0.7; ell1.rotdeg; 48; 200; cw
  ell2:  ell;  p0; rx * 1.2; ry; -deg1;

  # Auxiliary lines
  lnx: line; p0; 0;
  lnRx: line; p0; deg1;
  lnRy: line; p0; deg1 - 90;

cpoints:
  cpArc1C: arc1; c;    # `c` could be `center`
  cpArc1S: arc1; s;    # `s` could be `start`
  cpArc1E: arc1; e;    # `e` could be `end`
  cpArc1M: arc1; m;    # `m` could be `mid`
  cpArc1P333: arc1; 0.333;

  cpArc2C: arc2; c;
  cpArc2S: arc2; s;
  cpArc2E: arc2; e;
  cpArc2M: arc2; m;
  cpArc2P333: arc2; 0.333;
  
  pArc3Start: ell2; 25;
  pArc3End: ell2; 100;

pointSeries:
  psArc1: cpArc1S; cpArc1M; cpArc1E; 
  psArc2: cpArc2S; cpArc2M; cpArc2E; 

shapes2:
  arc3: arc; pArc3Start; rx * 1.2; ry; -deg1; small; ccw; pArc3End;
  arc3Arrow:  arc; arc3.c; rx * 1.41; ry * 1.2; -deg1; 270; 220; ccw
  ell3: ell; arc3.c; rx* 1.2; ry; -deg1;

cpoints2:
  cpArc3C: arc3; c;
  cpArc3S: arc3; s;
  cpArc3E: arc3; e;
  cpArc3M: arc3; m;
  cpArc3P333: arc3; 0.333;

pointSeries2:
  psArc3: cpArc3S; cpArc3M; cpArc3E; 

blocks:
  blkArc1:
    - circ1; stroke,1, black; dash, 3,3; fill, none;
    - arc1;  stroke, 6, royalblue; opaque, 0.6; fill, none;
    - psArc1; 6; black;
    - circ; cpArc1C;  6; ||| fill, blue
    - circ; cpArc1P333; 6; ||| fill, red
    - label; cpArc1C; w; 20; h;30; anchor; tm; ||| c
    - label; cpArc1P333; w; 60; h;30; anchor; bl; ||| t=0.333
    - label; cpArc1M; w; 20; h;30; anchor; tm; ||| m
    - label; cpArc1S; w; 20; h;30; anchor; bl; ||| s
    - label; cpArc1E; w; 30; h;30; anchor; mr; ||| e
    - arc1Arrow; stroke, 1; black; fill, none; marker; arrow1;
    - label; arc1Arrow.m; w; 30; h;30; anchor; tm; ||| ccw
  blkArc2:
    - ell1; stroke, 1, black; dash, 3,3; fill, none;
    - arc2; stroke, 6, royalblue; opaque, 0.6; fill, none;
    - psArc2; 6; black;
    - circ; cpArc2C;  6; ||| fill, blue
    - circ; cpArc2P333; 6; ||| fill, red
    - label; cpArc2C; w; 20; h;30; anchor; tm; ||| c
    - label; cpArc2P333; w; 60; h;30; anchor; tl; ||| t=0.333
    - label; cpArc2M; w; 20; h;30; anchor; tm; ||| m
    - label; cpArc2S; w; 20; h;30; anchor; bl; ||| s
    - label; cpArc2E; w; 30; h;30; anchor; mr; ||| e
    - arc2Arrow; stroke, 1; black; fill, none; marker; arrow1;
    - label; arc2Arrow.m; w; 30; h;30; anchor; bm; offset; -12, -6; ||| ccw
    # Auxiliary mark ellipse parameters
    - line; lnx; -rx * 1.2; rx * 1.2 ||| stroke, 1, red; dash, 3,3;
    - line; lnRx; -rx * 1.2; rx * 1.2 ||| stroke, 1, black; dash, 3,3; marker; arrow1;
    - label; lnRx.180; w; 30; h; 30; anchor; bl; ||| $R_x$
    - line; lnRy;-ry * 1.2; ry * 1.2 ||| stroke, 1, black; dash, 3,3;  marker; arrow1;
    - label; lnRy.120; w; 45; h; 30; anchor; br; ||| $R_y$
    - anglemark; lnx.20; lnx.a; lnRx.30; lineWidth; 2; size; 65;arrow-simple; labelpos; 0.3; label; rot_deg;  offset; 5, 0;
  blkArc3:
    - ell2; stroke, 1, black; dash, 3,3;fill, none;
    - ell3; stroke, 1, red; dash, 3,3;fill, none;
    - arc3; stroke, 6; royalblue; opaque, 0.6; fill, none;
    - psArc3; 6; black;
    - circ; cpArc3C;  6; ||| fill, blue
    - circ; cpArc3P333; 6; ||| fill, red
    - label; cpArc3C; w; 20; h;30; anchor; tm; ||| c
    - label; cpArc3P333; w; 60; h;40; anchor; tm; ||| t=0.333
    - label; cpArc3M; w; 20; h;30; anchor; tr; ||| m
    - label; cpArc3S; w; 20; h;30; anchor; bl; ||| s
    - label; cpArc3E; w; 30; h;30; anchor; mr; ||| e
    - arc3Arrow; stroke, 1; black; fill, none; marker; arrow1;
    - label; arc3Arrow.m; w; 80; h;30; anchor; bm; offset; -12, -6; rotdeg; 8; ||| ccw, small
    # Auxiliary marks
    - label; ell2.240; tm; h; 60; w; 80; ||| 起点和终点所在的椭圆
    - label; ell3.110; anchor; ml; h; 60; w; 80; red ||| 圆弧定义出的椭圆
draw:
  - blkArc1
  - blkArc2; move; R * 2.6, 0;
  - blkArc3; move; R * 1.3, ry * 2.5;

[wtht]


### Bezier曲线

Bezier曲线上从起点到终点的任一点可以用$t$的函数($t\in [0,1]$)来表示：
$$
B_2(t) = P_{start} \cdot (1-t)^2 + P_{ctrl}\cdot (1-t)t + P_{end}\cdot t^2 \\
B_3(t) = P_{start} \cdot (1-t)^3 + P_{ctrl1}\cdot (1-t)^2t + P_{ctrl2}\cdot (1-t)t^2 + P_{end}\cdot t^3
$$

从Bezier曲线计算点时，只是用一个参数`arg1`。如果它是下列值时将取得曲线上对应的点。

- `m`(或`mid`, `middle`): 将获取曲线的中点，对应于$t=\frac{1}{2}$
- `s`(或`start`): 将获取曲线的起点，对应于$t=0$
- `e`(或`end`): 将获取曲线的终点，，对应于$t=1$

如果参数`arg1`是一个在$[0,1]$之间的数字$t$，则它就是$t$本身，将返回$B_2(t)$或$B_3(t)$

```wtht
points:
  pStart: -20, -20
  pEnd:   320, 180
  pCtrl1: pStart.x + 320, pStart.y - 140
  pCtrl2: pEnd.x - 100, pEnd.y + 40

shapes:
  b2Curve: b2; pStart; pCtrl1; pEnd;
  b3Curve: b3; pStart; pCtrl1; pCtrl2; pEnd;

cpoints:
  cpB2s:    b2Curve; s    # `s` could be `start`
  cpB2e:    b2Curve; e    # `e` could be `end`
  cpB2m:    b2Curve; m    # `m` could be `mid` or `middle`
  cpB2p666: b2Curve;0.666
  cpB3s:    b3Curve; s
  cpB3e:    b3Curve; e
  cpB3m:    b3Curve; m
  cpB3p666: b3Curve;0.666

pointSeries:
  psB2: cpB2s; cpB2m; cpB2e;
  psB3: cpB3s; cpB3m; cpB3e;

blocks:
  blkB2:
    - b2Curve; stroke, 3, black; fill,none;
    - psB2; 6; black;
    - circ;  cpB2p666; 6 ||| fill, red;
  blkB3:
    - b3Curve; stroke, 3, black; fill,none;
    - psB3; 6; black;
    - circ;  cpB3p666; 6 ||| fill, red;

draw:
  - blkB2
  - blkB3; move; 360, 0;
```

[wtht] # Get Point from B2/B3 curves
noTitle: yes

points:
  pStart: -20, -20
  pEnd:   320, 180
  pCtrl1: pStart.x + 320, pStart.y - 140
  pCtrl2: pEnd.x - 100, pEnd.y + 40

shapes:
  b2Curve: b2; pStart; pCtrl1; pEnd;
  b3Curve: b3; pStart; pCtrl1; pCtrl2; pEnd;

cpoints:
  cpB2s:    b2Curve; s    # `s` could be `start`
  cpB2e:    b2Curve; e    # `e` could be `end`
  cpB2m:    b2Curve; m    # `m` could be `mid` or `middle`
  cpB2p666: b2Curve;0.666
  cpB3s:    b3Curve; s
  cpB3e:    b3Curve; e
  cpB3m:    b3Curve; m
  cpB3p666: b3Curve;0.666

pointSeries:
  psB2: cpB2s; cpB2m; cpB2e;
  psB3: cpB3s; cpB3m; cpB3e;

blocks:
  refLinesB2:
    - line; pStart; pCtrl1; ||| stroke, 1, aqua;
    - line; pCtrl1; pEnd; ||| stroke, 1, aqua;    
    - circ; pCtrl1; 6;  ||| fill, magenta
    - label; pCtrl1; anchor; bl; w; 70; color; magenta ||| pCtrl
    - label; cpB2m; anchor; tr; w; 100; h; 20; offset; -50, 80 ||| Bezier 2
  refLinesB3:
    - line; pStart; pCtrl1; ||| stroke, 1, aqua;
    - line; pCtrl2; pEnd; ||| stroke, 1, aqua;    
    - circ; pCtrl1; 6;  ||| fill, magenta
    - circ; pCtrl2; 6;  ||| fill, magenta
    - label; pCtrl1; anchor; tm; w; 70; h; 40; color; magenta ||| pCtrl1
    - label; pCtrl2; anchor; tr; w; 70; color; magenta ||| pCtrl2
    - label; cpB3m; anchor; tr; w; 100; h; 20; offset; -50, 20 ||| Bezier 3
  blkB2:
    - b2Curve; stroke, 3, black; fill,none;
    - psB2; 6; black;
    - circ;  cpB2p666; 6 ||| fill, red;
    - label; cpB2p666; anchor; tr; w; 70; ||| 0.666
    - label; cpB2s; anchor; mr; w; 40; ||| s
    - label; cpB2e; anchor; mr; w; 40; ||| e
    - label; cpB2m; anchor; tr; w; 40; ||| m
    - refLinesB2
  blkB3:
    - b3Curve; stroke, 3, black; fill,none;
    - psB3; 6; black;
    - circ;  cpB3p666; 6 ||| fill, red;
    - label; cpB3p666; anchor; tr; w; 70; ||| 0.666
    - label; cpB3s; anchor; mr; w; 40; ||| s
    - label; cpB3e; anchor; ml; w; 30; ||| e
    - label; cpB3m; anchor; tr; w; 40; ||| m
    - refLinesB3

draw:
  - blkB2
  - blkB3; move; 360, 0;

[wtht]


## 引用点

如果关于基本图形的某些点，我需要使用它，但又不是特别频繁的使用它，那么我们可以不专门为它取一个名字，在`cpoints`里定义，我们可以使用一种相对快捷的方式，即采用`ShapeName.tag`的方式，`ShapeName`是基本图形的名字，`tag`是一些特殊标记，可以用来获取`ShapeName`这个基本图形上对应的点。

比如在下边的例子中，我们定义`c2`时就直接使用`c1.c`作为它的圆心，也就是使用和`c1`相同的圆心。

```wtht
shapes:
  c1: circ; p0; R
  c2: circ; c1.c; R + 3
```
关于这些特殊标记，有表示方位的`n`,`e`,`s`,`w`,`ne`,`nw`,`se`,`sw`，有表示图形属性的，`a`,`b`,`c`, `o`, `m`, `cin`, `cout`, `cg`等。使用这些标记对应于对该基本图形执行前面小节所述的计算点操作。即`c1.c` 对应于

```wtht
cpoints:
  cp1: c1; c    # Here, cp1 is equivalent to `c1.c`
```

请注意，目前只定义了这些标记，使用其他的字符会被提示`cannot be resolved to a point!`错误。另外，不同的基本图形可接受的标记也不尽相同，比如三角形不接受`ne`,`se`等；相同的标记在不同的图形中所取得的点也不相同，比如`s`对于矩形将获取底边中点，而对于Bezier曲线来说将获取起点。

除了使用这些字母标记之外，我们还可以使用`shapeName.ddd`的形式来引用点，这里的`ddd`是一个**整数**，注意，只能是整数。使用它获取到的点为前一小节计算点中将第一个参数`arg1`赋值为这个整数所得到的点，请注意如果`arg1`可接受的数字只能为$[0,1]$之间的$t$的话，将不适用。所以这个功能主要应用于圆或者椭圆上取点。比如`c1.333`将获取圆`c1`上对应角度为$333\degree$的点。另外，直线上取点也可使用，注意，只能是**直线**(而非线段)，且是直线角度的**正向**.

下面是一个简单的例子：
```wtht
shapes:
  rect1:   rect; bycenter; 0,0; 100; 80
  c1:      circ; 0,0; 40
  tri1:    tri; -40, -40; 80, 0; -16,40;
  line1:   line; 0, 0; -40;
  ls1Back: line; line1; -30; 70
  ls1:     line; line1.o; line1.50;     # Point of length of 50 on line1
  ring1:   ring; c1.c; 30; 40;
  bezier1: b3; -30, -30; -10, -30; 5, 20; 20, 4;

pointSeries:
  ps1: rect1.n; rect1.s; rect1.w; rect1.e;
  ps2: rect1.sw; rect1.se; rect1.nw; rect1.ne
  ps3: c1.c; c1.222; c1.333             # Point of Deg 222/333 on c1
  ps4: tri1.a; tri1.b; tri1.c;
  ps5: tri1.cin; tri1.cout; tri1.cg
  ps6: ls1.s; ls1.e;
  ps7: ring1.cin; ring1.333; ring1.222  # Point of Deg 222/333 on ring1
  ps8: bezier1.s; bezier1.m; bezier1.e
  
blocks:
  blkRect1:
    - rect1; stroke, 2, grey; fill, none;
    - ps1; 4; black;
    - ps2; 4; red;
  blkC1:
    - c1; stroke,2, grey; fill, none;
    - ps3; 4; black;
  blkTri1:
    - tri1; stroke,2, grey; fill, none;
    - ps4; 4; black;
    - ps5; 4; red;
  blkLine1:
    - ls1Back; stroke,1, black; dash,3,3
    - ls1; stroke, 4, royalblue; opaque, 0.6;
    - ps6; 4; black;
  blkRing1:
    - ring1; stroke, 2, grey; fill, none;
    - ps7; 4; black;
  blkBezier1:
    - bezier1; stroke, 2, grey; fill, none;
    - ps8; 4; red; 0.4;
draw:
  - blkRect1;
  - blkC1;      move; 120, 0
  - blkTri1;    move; 230, 0
  - blkLine1;   move; -10, 120
  - blkRing1;   move; 120, 100
  - blkBezier1; move; 245, 110
```

[wtht] # Referencing Point
noTitle: yes
shapes:
  rect1:   rect; bycenter; 0,0; 100; 80
  c1:      circ; 0,0; 40
  tri1:    tri; -40, -40; 80, 0; -16,40;
  line1:   line; 0, 0; -40;
  ls1Back: line; line1; -30; 70
  ls1:     line; line1.o; line1.50;     # Point of length of 50 on line1
  ring1:   ring; c1.c; 30; 40;
  bezier1: b3; -30, -30; -10, -30; 5, 20; 20, 4;

pointSeries:
  ps1: rect1.n; rect1.s; rect1.w; rect1.e;
  ps2: rect1.sw; rect1.se; rect1.nw; rect1.ne
  ps3: c1.c; c1.222; c1.333             # Point of Deg 222/333 on c1
  ps4: tri1.a; tri1.b; tri1.c;
  ps5: tri1.cin; tri1.cout; tri1.cg
  ps6: ls1.s; ls1.e;
  ps7: ring1.cin; ring1.333; ring1.222  # Point of Deg 222/333 on ring1
  ps8: bezier1.s; bezier1.m; bezier1.e
  
blocks:
  blkRect1:
    - rect1; stroke, 2, grey; fill, none;
    - ps1; 4; black;
    - ps2; 4; red;
  blkC1:
    - c1; stroke,2, grey; fill, none;
    - ps3; 4; black;
  blkTri1:
    - tri1; stroke,2, grey; fill, none;
    - ps4; 4; black;
    - ps5; 4; red;
  blkLine1:
    - ls1Back; stroke,1, black; dash,3,3
    - ls1; stroke, 4, royalblue; opaque, 0.6;
    - ps6; 4; black;
  blkRing1:
    - ring1; stroke, 2, grey; fill, none;
    - ps7; 4; black;
  blkBezier1:
    - line; bezier1.s; -10, -30; ||| stroke, 1, aqua;
    - line; bezier1.e; 5, 20; ||| stroke, 1, aqua;
    - circ; 5, 20; 3; ||| fill, magenta
    - circ; -10, -30; 3; ||| fill, magenta
    - bezier1; stroke, 2, grey; fill, none;
    - ps8; 4; red; 0.4;
draw:
  - blkRect1;
  - blkC1;      move; 120, 0
  - blkTri1;    move; 230, 0
  - blkLine1;   move; -10, 120
  - blkRing1;   move; 120, 100
  - blkBezier1; move; 245, 110
[wtht]
