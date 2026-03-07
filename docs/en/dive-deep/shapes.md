# Basic Shapes

## Points

Point normally is a location, a coordinate, showing as a pair of numbers $(x,y)$, in the design file, we can explicitly define the points under the root tag of `points`, `points2`, `points3`, `points4`, etc. We need to separate the $x$ part and $y$ part of the coordinate with coma (,), and we can optionally using a pair of parenthesis to surround the coordinates, both are OK。

```wtht
points:
  pt1: 3,4
  pt2: (5,6)
```

Point can be directly defined (with a name), or it can be temporarily defined/referenced (without a name). Meanwhile, point can be composed by giving complicated calculated result of expression, for both $x$ part and $y$ part. It is like that we temporarily defined two vars $V_x$ and $V_y$, which are presented by some definitions, and then we make a new point $(V_x, V_y)$. In following example, the point `pt1` is explicitly defined with pure numbers, and `pt2` is based on `pt1`, in geometric, it is like that `pt2` is moving `pt1` by a offset of $(5,0)$ to make a new point. `pt3` is offsetting `pt2` by $(\sqrt{6}, \sqrt{6})$. Here we don't need to exactly know how large $\sqrt{6}$ is, we just use the result, we want the point be moved $45\degree$ (southeast) with length of 6, and it is.

```wtht
points:
  pt1: 3, 4
  pt2: pt1.x + 5, pt1.y
  pt3: pt2.x + sqrt(6), pt2.y + sqrt(6)
```

## Basic Shapes

We can build complex graphs by basic graph/shapes, like, Lines, Line Segments, Circles, Ellipses, Arcs, or Bezier Curves. In a hierarchical design, the first things are building the basic shapes. In the design file, all the basic shapes are presented under the root tag of `shapes`. 

In some cases, we want a very simple and compact design file, or the shapes that need to be drawn will not be referred, we can directly define them in the **final draw** section without a name. we can call this way as ad-hoc define.

```wtht
shapes:
  circ1: circ; ... # we define a circle named as circ1

draw:
  - circ1; stroke, 1, black;       # draw circ1 out, stroke it black
  - circ; ... ||| stroke, 1, red;  # we draw some other circle, stroke it red
```

The normal form of defining a shape is `SHAPE_TYPE; ...`, here the `SHAPE_TYPE` keyword will indicate which shape it will define, the following are parameters of the shape. Following sections will introduce the parameters and usages for each type of shape.

### Line

By using the key word of `line` (short as `ln`), we can define a Line. It needs a point (we can call it the anchor point) and an angle. For example, by giving a point $P_0$, and angle $45\degree$, we get a line going through point $P_0$ and having the direction of 45$\degree$. i.e.

```wtht
points:
  p0: 20, 30
shapes:
  line1: line; p0; 45
```
Please be aware that, here we are defining a mathematical line , which is going to infinite on both ends, thus we cannot draw it out, we can only draw a little portion of it, i.e. a segment. So Line is only for reference and calculation usage, we can not draw it. If you present it in the **final draw** section, it will also be ignored.


```wtht
draw:
  - line1; stroke, 1,red;     # this will be ignored
```

### Line Segment

Using `line` keyword, by providing different parameters, we can define **Line Segment**, and [**Polyline**](#Polyline). Though it is not strict here to use the same keyword, it is a little intuitive. By giving two points, you get a line segment, and giving three and more, you get a polyline.

```wtht
shapes:
  # providing anchor point and angle, we define a line
  line1: line; p0;    deg1;
  # providing two (end) points, we define a line segment
  ls1:   line; p0;    p1;
  # based on a defined line, we can define a line segment by giving a length of l,
  # starting point is the anchor point of the line,
  # ending point is the point going from anchor point by length of l
  ls2:   line; line1; l;
  # based on a defined line, we can define a line segment by giving two lengths of l1 and l2
  # starting and ending points are the points going from anchor point by lengths of l1 and l2
  ls3:   line; line1; l1; l2;
  # When providing three points and more, we get a polyline
  pln1:  line; p0;    p1;   p2;  p3;
```

The way how line segments are made from lines are shown as follows:

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

We can also use keyword `lineseg`(short as `ls`) to define a Line Segment, and we can only define Line Segments. We have three ways: 

```wtht
shapes:
  # On the basis of Line, giving two lengths l1 and l2
  ls4: lineseg; line1; l1; l2;
  # Providing two end points, starting and ending
  ls5: lineseg; p0; p1;
  # Temporarily define a line and then two lengths to make a Line Segment
  ls6: lineseg; p0; deg1; l1; l2;
```

### Polyline

As explained in previous section, we use `line` keyword to define Polyline. Besides this, we can use the keyword `polyline` (short as `pln` or `pline`) to define. There are two ways, either providing discrete points or providing a previously defined **Point Series**.

```wtht
shapes:
  # provide discrete points to make a polyline
  pln1: polyline; p0; p1; p2; p3; ... pN
pointSeries:
  ps1: 3, 3; 4, 2; 5,6; p1; p2; ... pX;
shapes2:
  # use the previously defined Point Series to make a polyline
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

### Circle

Using the keyword `circle`(short as `circ`), we can define a circle. We have two parameters, the first is center (a point) of the circle, and the second is the radius.

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

### Ring

Using the keyword `ring`, we can define a ring, three parameters are needed, the first is center of the ring (center of the two circles), the second and the third are the radius of the two circles, smaller value would be the inner, and the larger is the outer.

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

### Rectangle

Using the keyword `rectangle`(short as `rect`), we can define a rectangle. We have three ways, providing two points (in diagonal), top-left point, width, or height, or center point, width and height.

```wtht
shapes:
  # Providing two cortex points in diagonal
  rect1: rectangle; x1, y1; x2, y2;
  # Top-Left point and width/height
  rect2: rect;      x1, y1; width; height
  # Center Point and width/height
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

### Triangle

Using the keyword `triangle`(short as `tri`) we can define triangles, the parameters are the three points of the triangle.

```wtht
shapes:
  # Providing the three points, we get a triangle
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

### Polygon

Using keyword `polygon` (short as `plg`) we can define a Polygon, like the [**Polyline**](#Polyline), we can use Point Series or Discrete Points to define a polygon.

```wtht
shapes:
  # Providing discrete points to make a polygon
  plg1: polygon; p0; p1; p2; p3; ... pN
pointSeries:
  ps1: 3, 3; 4, 2; 5,6; p1; p2; ... pX;
shapes2:
  # Providing previous defined point series to define a polygon
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

[i] Obviously, we can define Rectangles or Triangles by defining Polygons.


### Regular Polygon

Using the keyword `npolygon`(short as `plgn`), we can define Regular Polygon. We need 4 parameters, the first is count of edges (N), the second is center of the Polygon, the third is **radius** of the outer circle of the polygon, the fourth is the offset angle (with respect to x-axis positive) of the first point of the polygon.

```wtht
shapes:
  # Here we define a regular polygon with N edges, centered at pCenter, 
  # distance from center to vertices is R, and angle of start point is deg_of_start
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
  - label; pCenter.x, pCenter.y - 100; anchor; center; w; 220; h; 20; ||| $N$ Regular Polygon ($N=7$)
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

### Ellipse

Using the keyword `ellipse`(short as `ell`), we can define Ellipse. We need four parameters, center of the ellipse, $R_x$, $R_y$, and the rotation angle.

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


### Bezier 2

Using keyword `bezier2`(short as `b2`), we can define a 2-nd order Bezier Curve. We need three parameters, the first is the starting point, second is the control point, the third is the ending point. 2-nd order Bezier is actually some parabolic curve, the lines from starting/ending point to the control point are the tangent lines of the parabolic curve at starting/ending points. You can refer the SVG specification regarding QuadraticBezier to check the details. ([https://www.w3.org/TR/SVG2/paths.html#PathDataQuadraticBezierCommands](https://www.w3.org/TR/SVG2/paths.html#PathDataQuadraticBezierCommands))

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


### Bezier 3

Using the keyword `bezier3`(short as `b3`), we can define 3-rd order Bezier Curves, four parameters are required, starting point, control point 1, control point 2, and ending point. Please refer to the SVG specification regarding CubicBezier to check the details. ([https://www.w3.org/TR/SVG2/paths.html#PathDataCubicBezierCommands](https://www.w3.org/TR/SVG2/paths.html#PathDataCubicBezierCommands))

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

[i] Generally, we can define N-th Bezier Curve, using the formula:
$$ P(t) = P_{start}\binom{n}{0}(1-t)^n + P_{ctrl1}\binom{n}{1}t(1-t)^{n-1} + P_{ctrl2}\binom{n}{2}t^2(1-t)^{n-2} + \cdots + P_{end}\binom{n}{n}t^n,\\ t\in [0,1]
$$
In normally usage, 2-nd and 3-rd could handle almost using cases, so here we only introduce the 2-nd and 3-rd order curves.

### Arc

Using the keyword `arc`, we can define Elliptical Arcs, both  circular and elliptical arcs are included. We can define the arc based on Circle or Ellipse (the arc is a portion of the circle/ellipse), or we can define using the unique form.

1. Define from Circle or Ellipse. The parameters are the basis (circle or ellipse), starting angle position, ending angle position, and rotating direction, i.e. clockwise(`cw`) or counter-clockwise(`ccw`).


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

2. Using the unique way to define an arc. Parameters are starting point, $R_x$, $R_y$, rotating angle, clockwise/counter-clockwise, large/small arc, and ending point.

```wtht
shapes:
  # cw is clockwise，ccw is counter-clockwise，large/small means the arc will be either the larger or smaller portion of the circle/ellipse
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



3. We can temporarily define a circle/ellipse, then give starting/ending angle position and cw/ccw to define an arc.

```wtht
shapes:
  # The first parameters define an ellipse, then starting/ending angles, and rotating direction, cw/ccw
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
