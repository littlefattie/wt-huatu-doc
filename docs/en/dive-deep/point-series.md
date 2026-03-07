# Series

## Point Series

To easily reuse, we can merge some points into a series (like a set), thus we can design some shapes upon this. For example, if we need to connect discrete points to make a polyline, then we can add all the points into a series first, then define the polyline directly on the series. It makes no advance for just the polyline, but if we need to draw/mark all the points, it would be much easier, because we can draw the point series directly rather not a collection of circles.

It is very easy to define ordinary point series, just add them one by one after the name.

```wtht
points:
  p0: 3,3
  p1: 4,4
  p2: 5,6
pointSeries:
  # Add the points one by one, please know that the order matters
  ps1: p0; p1; p2; -3, 0; -5, -4; ... 
```

Besides the ordinary point series, which we create by manually adding points into it, we can create point series by calculation. For example, we can make a point series of which all the points are on the same circle but with specified rotating angle. If we define a **number series** of the angles, then the point series are calculated from the angles. So at first, we need to introduce the idea of **number series**.

## Number Series

Number series are not required to be defined explicitly, i.e. we don't need to name it. It is presented in the definition of point series, or shape series. We just present a form of number series definition, the parser will automatically translate it. There are two ways to present a number series. 1) directly present discrete numbers separated with coma (,); 2) use the form of `start:step:end` form, the sign (+/-) of `step` is determined by whether `end` is larger than `start`. If `end` is larger, `step` is positive, and vice versa. And the `step` shall **not be zero**. The colon (`:`) used in `start:step:end` can also be a vertical bar (`|`), i.e. `start|step|end`, you can use either char as you favor.

## Calculated Point Series

The way how point series are resolved is based on [Calculated Points](./get-point.md#Calculated%20Points). When we replace the only accepted parameter with multiple parameter values, we will get a point series like we make calculated point multiple times. When the shape accept two parameter to calculate a point, we can replace the **number-possible parameter** to number series, the form of `start:step:end` or discrete numbers, then we will also get a point series as when we change the number parameter with each value in the number series and get the point calculated. If you are present discrete numbers to make a number series, please use coma (`,`) to separate, not semi-colon.

Following are some examples showing how to make calculated point series from Circle, Ellipse, Line, Line Segment, and Bezier Curve.

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
  # for Line Segment
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

We have two ways to calculate point series form Polyline or Polygon:

1. Use two parameter, the first is $n$, means the $n$-th segment/edge, the second is a series of $t$, discrete or equally-stepped
2. Use only one parameter, which is composed of discrete integers between 1 and N, this will return the corresponding $n$-th cortex.

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

[i] Note: for the same vertex count N, Polyline has only N-1 segments, while Polygon has N edges.

Calculating from triangles are similar to Polygons, when giving only one parameter, it could be the location/direction tags, or it can be the $n$-th edge and a number series of $t$ to get point series on the $n$-th edge.

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

Calculating point series from Rectangle is a little different, we have two ways:

1. use only one parameter, the directional or locational tags, like `nw`,`ne`,`s`,`e`, `c` etc.
2. use two parameters, the first need to be one of `north`,`south`,`east`,`west`, means which edge, the second is some number series of $t$, discrete or equally-stepped.

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

There is only one way to calculate point series from Arcs, that is providing a number series of $t$, discrete or equally-stepped.

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
