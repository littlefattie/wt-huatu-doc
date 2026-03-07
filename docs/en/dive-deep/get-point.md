# Get Point

Points are the basis to define shapes, and important reference to give locations. Besides the explicit way to present $x$ and $y$ coordinate values, we can get specific points of the shapes, or calculate points from them by giving certain parameters.

## Calculated Points

Calculated Points are the points we calculate from given parameters. All the calculated points are placed under the root tag `cpoints`. Two parameters are required to calculate a point on specific shape. For different shapes, the parameters would be different, the unified form of defining `cpoint` would be:

```wtht
cpoints:
  cpt1: shape1; arg1; arg2;
```

### Line

Only one parameter is required to calculate point on Line. When it is a number, it is the length $l$, the distance from the anchor point when walking on the line. When it is one of the identifiers `center`, `c`, `o`, `a`,`anchor`, it would be the **anchor point**. 

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

### Line Segment

Only one parameter is required to calculate point on Line Segment. If it is `s` or `start`, then it is the starting point, if `e` or `end`, then it is the ending point, if `m`, `mid`, or `middle`, then it is the middle point of the Line Segment, if a number between $[0,1]$, then it is the point proportional to the length of the Line Segment from starting to ending point. Obviously $t=0$ means starting point, $t=0.5$ means middle point, $t=1$ means ending point.

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

### Circle

Only one parameter is required to calculate point on a Circle. If it is a number, then it is the point calculated by ($R\cos\theta$, $R\sin\theta$) where the number is $\theta$. If it is the identifier of one of `n,s,e,w,ne,se,nw,sw,c`(`c` means center), then corresponding point at specific direction will the point. Following are examples:

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


### Ring

Two parameters could be used to calculate a point on Ring, the second is optional. When the first is `cin` or `cout`, then it is the center of the Ring. When the first is `in`, or `out`, it will also be the center of the Ring if second parameter is not provided, it is the point on inner circle (`in`), or outer circle(`out`) if the second point is a number. If the first parameter is a number, then the point is the point on **outer** circle with the number as parameter.

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

### Ellipse

Only one parameter is needed to calculate point on Ellipse. When it is a number, then it is angle $\theta$, the point will be $(R_x\cos\theta, R_y\sin\theta)$ (of course, the rotating angle need to be considered). When the parameter is one of the following location identifiers, the point will be:
- `c`(or `center`): center of the ellipse
- `n`: the most northern point of the Ellipse (having minimal y)
- `s`: the most southern point of the Ellipse (having maximal y)
- `e`: the most eastern point of the Ellipse (having maximal x)
- `w`: the most western point of the Ellipse (having minimal x)
- `nw`: the crossing point of the Ellipse and ray from center going northwest
- `ne`: the crossing point of the Ellipse and ray from center going northeast
- `sw`: the crossing point of the Ellipse and ray from center going southwest
- `se`: the crossing point of the Ellipse and ray from center going southeast

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


### Triangle

Two parameters are used to calculate a point on Triangle, the second parameter is optional. When the first parameter is one of following, corresponding point of specific location will be the point:

- `cin`: center of the inner tangent circle of the triangle
- `cout`: center of the outer surrounding circle of the triangle
- `cg`(or `cgravity`): gravity point of the triangle
- `a`: the first point when defining the triangle
- `b`: the second point when defining the triangle
- `c`: the third point when defining the triangle

If the second parameter is provided, the first parameter should be `1`,`2`, or `3`, which means the first/second/third edge of the triangle, i.e. Line Segment $\overrightharpoon{AB}$, $\overrightharpoon{BC}$, $\overrightharpoon{CA}$, and the second parameter $t$ should be $t\in[0,1]$, thus the combination would be the same as calculating point on Line Segment.


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

### Rectangle

Two parameters could be used to calculate point on Rectangle, the second is optional. When the first parameter is one of the following, the point would be:

- `c`(or `center`): center point of the Rectangle
- `n`(or `nm`,`top-middle`,`topmiddle`): middle point of the top/northern edge
- `s`(or `sm`,`bottom-middle`,`bottommiddle`): middle point of the bottom/southern edge
- `e`(or `em`,`right-middle`,`rightmiddle`): middle point of the right/eastern edge
- `w`(or `wm`,`left-middle`,`leftmiddle`): middle point of the left/western edge
- `nw`(or `topleft`,`top-left`): top-left corner point of the Rectangle
- `ne`(or `topright`,`top-right`): top-right corner point of the Rectangle
- `sw`(or `bottomleft`,`bottom-left`): bottom-left corner point of the Rectangle
- `se`(or `bottomright`,`bottom-right`): bottom-right corner point of the Rectangle

If the first parameter is one of `north`(or `top`),`west`(or `left`),`south`(or `bottom`),`east`(or `right`), the second parameter should be a number in $[0,1]$, then a point on corresponding edge will be returned, like calculating point on a Line Segment. The `north` and `south` edges will measure distance from **Left**, while the `west` and `east` edges will measure distance from **Top**。

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

### Polyline

To calculate a point on Polyline, we can use just one parameter, if it is `s`(or `start`, `first`), we will get the first/starting point of the Polyline, if it is `e`(or `end`, `last`), it is the last/ending point of the Polyline. If the first point is an integer $n$ between 1 and N-1(N is the point count), the starting point of the $n$-th Segment of the Polyline will be returned if no second parameter, or a point on the $n$-th segment will be returned if the second parameter is $t\in[0,1]$ , it is like calculating point on a Line Segment, i.e. $P(n,t) = LS_n(t) = P_{n,start} + t \cdot L({LS_n})$.

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
  - label; cpStart; anchor; br; w; 50;h; 40; left; ||| $P_{start}$
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
  - label; cpStart; anchor; br; w; 50; h; 40; left; ||| $P_{start}$
  - circ; cpStart; 8; ||| fill, royalblue, 0.6;
  - label; cpEnd; anchor; tm; w; 50; h; 40; ||| $P_{end}$
  - circ; cpEnd;   8; ||| fill, royalblue, 0.6;
  - circ; cp3;  8; ||| fill, royalblue, 0.6;
  - label; cp3; anchor; tr; w; 50; h; 40; ||| $cp3$
  - circ; cp3_p4; 8; ||| fill, maroon;
  - label; cp3_p4; anchor; tr; w; 50; h; 40; ||| $3, 0.4$
  - label; cp3_p4; anchor; ml; w; 60; h; 54; 
    offset; 30,8; blue; ||| 3-rd seg of the polyline
  - dimmark; cp3; p3; gap; 50; linewidth; 1.5; barwidth; 1.5; linepos; 0.5; label; $L$
  - dimmark; cp3; cp3_p4; left; gap; 40; linewidth; 1.5; barwidth; 1.5; linepos; 0.5; label; $0.4L$
[wtht]


### Polygon

To calculate a point on Polygon, the first parameter $n$ must be an integer between 1 and N (N is the edge count of the Polygon), if the second parameter is not present, then the point will be the $n$-th vertex, or if the second parameter is present, it should be a number $t\in[0,1]$, then the point will be like calculating a point on a Line Segment, which is the $n$-th edge of the polygon.

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
  - label; cp3_p666; w; 100; h; 35;anchor; br; offset; -40,28; blue;||| 3-rd Edge of the Polygon
  - dimmark; p3; p4; linewidth; 2; left; barwidth; 1.5; barlen; 100; linepos; 0.8; label; $L$
  - dimmark; p3; cp3_p666; linewidth; 2; barwidth; 1.5; barlen; 40; linepos; 0.8; label; $0.666L$
[wtht]


### Arc

To calculate a point on Arc, only one parameter is required. If it is one of the following:

- `c`(or `center`): will ge the center of the circle/ellipse that the arc is originated from
- `m`(or `mid`, `middle`): will get the middle point of the arc, it is not per length, but per angle
- `s`(or `start`): will get the starting point of the arc
- `e`(or `end`): will get the ending point of the arc

If the parameter is a number $t\in[0,1]$, then a point corresponding to the angle proportional to angle range from starting angle to ending angle will be returned. i.e.

$$
D_t = D_{start} \cdot (1-t) + D_{end} \cdot t \\
P(t) = P_{arc}(D_t)
$$

where $D$ is the angle (unit is degree) corresponding to the point on arc. Obviously staring point means $t=0$, ending point means $t=1$, and middle point means $t=0.5$.

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
    - label; ell2.240; tm; h; 60; w; 180; offset; -26, 10 ||| The Ellipse which starting/ending points are from
    - label; ell3.110; anchor; ml; h; 60; w; 120; red ||| Essential Ellipse of the Arc
draw:
  - blkArc1
  - blkArc2; move; R * 2.6, 0;
  - blkArc3; move; R * 1.3, ry * 2.5;

[wtht]


### Bezier Curves

For Bezier Curves, the points on it could be calculated by the function of $t$ ($t\in [0,1]$) as follows:

$$
B_2(t) = P_{start} \cdot (1-t)^2 + P_{ctrl}\cdot (1-t)t + P_{end}\cdot t^2 \\
B_3(t) = P_{start} \cdot (1-t)^3 + P_{ctrl1}\cdot (1-t)^2t + P_{ctrl2}\cdot (1-t)t^2 + P_{end}\cdot t^3
$$

So when calculating a point on Bezier Curve, only one parameter $t$ is required. It could be a number between $[0,1]$, then it is $t$ itself, point $B_2(t)$ or $B_3(t)$ will be returned. Or it could be some shortcut identifier to get specific points. 

- `m`(or `mid`, `middle`): will get the middle point, means $t=\frac{1}{2}$
- `s`(or `start`): will get the starting point means $t=0$
- `e`(or `end`): will get the ending point, means $t=1$

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


## Referencing Point

For some special points of shapes (e.g. center of a circle), we want to use or refer to it, but we don't want to define it as a calculated point (under `cpoints`) because we might use it only once, we don't want to make such a define, it seems a little over-expense. Then we have a quick way to do so, i.e. Referencing Point.

It is not a define, it is just referencing, we use the form of `ShapeName.tag`, `ShapeName` is name of the shape, `tag` is some special identifier, used to get the special point of the shape.

In the following example, we define a circle `c2` using the same center as `c1` by present the center parameter as `c1.c` which means center of `c1`.

```wtht
shapes:
  c1: circ; p0; R
  c2: circ; c1.c; R + 3
```

Regarding these special identifier, we have directions like `n`,`e`,`s`,`w`,`ne`,`nw`,`se`,`sw`, and some special meaningful tags like `a`,`b`,`c`, `o`, `m`, `cin`, `cout`, `cg`, etc. These identifiers are subset of the **one-parameter** calculations in previous section and will play as the same effect when used. E.g. when using `c1.c` it is equivalent to use a point `c1c` defined as `c1c: c1;c` under `cpoints` root tag.


```wtht
cpoints:
  cp1: c1; c    # Here, cp1 is equivalent to `c1.c`
```
[i] Please be aware of that, we have only defined limited identifiers to be used as Referencing Point, if you encounter some error like `cannot be resolved to a point!`, it is probably because you are not using a supported identifier to get point. And also be aware of that same identifier might play as different meaning for different type of shapes, e.g. `s` means starting point in Bezier Curves, but means Southern-most point in Circles.

The specific identifier can also be integer, like `shapeName.ddd`, `ddd` can only be integer, when used, it will play as the first parameter passed to the calculating point function. It is mainly used to quickly get a point on circle or ellipse which has a integer angle corresponding to. And it can also be used to get a point on Line (not Line Segment) in positive ray-direction. 

Following are some examples of Referencing Point usage.

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
