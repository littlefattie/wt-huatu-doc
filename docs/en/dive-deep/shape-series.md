# Shape Series

Like how we define the [Basic Shapes](./shapes.md), if we replace the parameters into **Point Series**, or **Number Series**, then we will get some Shape Series, after defined, we can draw the series as a whole.

[i] Note: we can replace all the parameters into series, but the final shape count of the series will be determined by the parameter which has the least count. If some parameter has only one value, then this only value will be duplicated to align with other parameters.

## Line Segment Series

[i] Note: because Lines are infinite in length, we cannot draw them, so in Shape Series part, we choose to not define Line Series, but only Line Segment Series, if you really need some "line series", please define Line Segment Series instead and draw them in the final draw part.

As when we define [Line Segment](./shapes.md#Line%20Segment), we use the same shape type keyword `line`(short as `ln`), or `lineseg`(short as `ls`) to define Line Segment Series. We use the same keywords for simplicity, but please be aware that you will not be able to use the keywords `line`,`ln`,`lineseg`,`ls` to define Polyline Series, please use `polyline`(short as `pln`), you will read this in following sections.

We have two ways to define Line Segment Series:

1. Define some "Line Series" virtually, which needs two parameters, the first is point series, and the second is number series of degrees. Then we provide one or two number series of length. Totally we need three or four parameters. If only one "length series" is provided, it means segment ends, the starts will be anchor points of the lines, like the [Line Segment](./shapes.md#Line%20Segment) definition.

```wtht
shapeSeries:
  # ps1 is a point series，degSeries1 is a number series of degrees(angles)，
  # lSeries is a number series of lengths
  ssLn1: line; ps1; degSeries1; lSeries
  # ps2 is a point series，degSeries1 is a number series of degrees(angles)，
  # l1Series and l2Series are both number series of lengths
  ssLn2: line; ps2; degSeries1; l1Series; l2Series
```

Following are some examples:

```wtht
points:
  p0: 0,0

shapes:
  c1: circ; p0; 60;
pointSeries:
  psFromC1: c1; 0: 60: 350

shapeSeries:
  # Fixed anchor point，degrees，ends L1 and L2 are length series
  ssLs1:  line; p0; 20:15:170; -80:10:0; 20:18:300
  # Fixed anchor point，degrees is number series，L1 fixed，L2 is length series
  ssLs2:  line; p0; 20:15:170; -60; 30:22:340;
  # Anchor points are point series，the degree，L1 and L2 both are fixed lengths
  ssLsC1: line; psFromC1; -30; -30; 30;
  # Anchor points are series，Degrees are series，only one length which is series
  ssLsC2: line; psFromC1; -30: 10: 30; -30:5:0;
  # Anchor points are series, degree and L1 are series, L2 is fixed
  ssLsC3: line; psFromC1; -30: 10: 30; -30:5:0; 30;
  # Anchor points are series, degree is fixed, L1 and L2 are length series
  ssLsC4: line; psFromC1; -45; -20: 4: 0; 5: 5: 40;

blocks:
  blkL1:
    - ssLs1; stroke, 1, red;
  blkL2:
    - ssLs2; stroke, 1, blue;
  blkC1:
    - c1; stroke, 1, grey; fill, none;
    - psFromC1; 4; red; 
    - ssLsC1; stroke,1, blue;
  blkC2:
    - c1; stroke, 1, grey; fill, none;
    - psFromC1; 4; red; 
    - ssLsC2; stroke,1, blue;
  blkC3:
    - c1; stroke, 1, grey; fill, none;
    - psFromC1; 4; red; 
    - ssLsC3; stroke,1, blue;
  blkC4:
    - c1; stroke, 1, grey; fill, none;
    - psFromC1; 4; red; 
    - ssLsC4; stroke,1, blue;

draw:  
  - blkL1;
  - blkL2; move; 340, 0;
  - blkC1; move;0, 280
  - blkC2; move;300, 280
  - blkC3; move;0, 480
  - blkC4; move;300, 480
```

[wtht] # Make Line Series in line defining form
noTitle: yes

points:
  p0: 0,0

shapes:
  c1: circ; p0; 60;
pointSeries:
  psFromC1: c1; 0: 60: 350

shapeSeries:
  ssLs1: line; p0; 20:15:170; -80:10:0; 20:18:300
  ssLs2: line; p0; 20:15:170; -60; 30:22:340;
  ssLsC1: line; psFromC1; -30; -30; 30;
  ssLsC2: line; psFromC1; -30: 10: 30; -30:5:0;
  ssLsC3: line; psFromC1; -30: 10: 30; -30:5:0; 30;
  ssLsC4: line; psFromC1; -45; -20: 4: 0; 5: 5: 40;

blocks:
  blkL1:
    - ssLs1; stroke, 1, red;
    - label; p0.x, p0.y - 65; anchor; bm; w; 120; h; 46; bdrcolor; black; ||| $L_1$ Decreasing, $L_2$ Increasing
  blkL2:
    - ssLs2; stroke, 1, blue;
    - label; p0.x-32, p0.y - 65; anchor; bm; w; 140; h; 46; bdrcolor; black; ||| $L_1$ Fixed，$L_2$ Increasing
  refCircle:
    - c1; stroke, 1, grey; fill, none;
  blkC1:
    - refCircle
    - psFromC1; 4; red; 
    - ssLsC1; stroke,1, blue;
    - label; p0.x, p0.y - 75; anchor; bm; w; 200; h;62; bdrcolor; black; ||| Multiple anchor points, Single Degree, both ends fixed and equal
  blkC2:    
    - refCircle
    - psFromC1; 4; red; 
    - ssLsC2; stroke,1, blue;
    - label; p0.x, p0.y - 75; anchor; bm; w; 200; h; 62; bdrcolor; black; ||| Multiple anchor points, degree varies, only one length $L$ and varies
  blkC3:
    - refCircle
    - psFromC1; 4; red; 
    - ssLsC3; stroke,1, blue;
    - label; p0.x, p0.y - 80; anchor; bm; w; 200; h; 62; bdrcolor; black; ||| Multiple anchor points, degree varies, $L_1$ Decreasing, $L_2$ fixed
  blkC4:
    - refCircle
    - psFromC1; 4; red; 
    - ssLsC4; stroke,1, blue;
    - label; p0.x, p0.y - 80; anchor; bm; w; 200; h; 62; bdrcolor; black; ||| Multiple anchor points, degree fixed, $L_1$ Decreasing, $L_2$ Increasing

draw:  
  - blkL1;
  - blkL2; move; 340, 0;
  - blkC1; move;0, 320
  - blkC2; move;300, 320
  - blkC3; move;0, 560
  - blkC4; move;300, 560
[wtht]


2. We can provide two point series as the starting and ending points of the Line segments, and another keyword `zip`(or `pair`), `cross`(or `interleave`) to indicate how to connect points from both series. `zip` means the first in starting series will match the first in ending series, `cross` means each point in starting series will match all the points in ending series, it is like multiplication or crossing. If the starting series has $M$ points, ending series has $N$ points, then `zip` with get a series of $\min(M,N)$ Line Segments, while `cross` will get a series of $M \times N$ Line Segments。


```wtht
vars:
  w: 110
  h: 320
points:
  pTop: 0,0;
shapes:
  ln1: line; pTop; -w, h;
  ln2: line; pTop;  w, h;

pointSeries:
  ps1:  ln1; 0.2:  0.1: 1
  # pick points from ln2, t is increasing
  ps21: ln2; 0.2 : 0.1: 1
  # pick points from ln2, t is decreasing, different with ps21
  ps22: ln2; 1 : 0.1: 0.2

shapeSeries:
  ssPs1: circ; ps1; 4;
  ssPs21: circ; ps21; 4;
  ss1Zip: line; ps1; ps21; zip;
  ss1Cross: line; ps1; ps21; cross;
  ssPs22: circ; ps22; 4;
  ss2Zip: line; ps1; ps22; zip;
  ss2Cross: line; ps1; ps22; cross;

blocks:  
  blk1:
    - ss1Zip; stroke, 1, green;
    - ssPs1; fill, gold;
    - ssPs21; fill, gold;
  blk2:
    - ss2Zip; stroke, 1, magenta;
    - ssPs1; fill, gold;
    - ssPs22; fill, gold;
  blk3:
    - ss1Cross; stroke, 1, blue;
    - ssPs1; fill, gold;
    - ssPs21; fill, gold;
  blk4:
    - ss2Cross; stroke, 1, maroon;
    - ssPs1; fill, gold;
    - ssPs22; fill, gold;

draw:
  - blk1
  - blk2; move; 250, 0
  - blk3; move;   0, 300
  - blk4; move; 250, 300
```

[wtht] # Make Line Series in Line Seg form
noTitle: yes

vars:
  w: 110
  h: 320
points:
  pTop: 0,0;
shapes:
  ln1: line; pTop; -w, h;
  ln2: line; pTop;  w, h;

pointSeries:
  ps1:  ln1; 0.2:  0.1: 1
  ps21: ln2; 0.2 : 0.1: 1
  ps22: ln2; 1 : 0.1: 0.2

shapeSeries:
  ssPs1: circ; ps1; 4;
  ssPs21: circ; ps21; 4;
  ss1Zip: line; ps1; ps21; zip;
  ss1Cross: line; ps1; ps21; cross;
  ssPs22: circ; ps22; 4;
  ss2Zip: line; ps1; ps22; zip;
  ss2Cross: line; ps1; ps22; cross;

blocks:  
  blk1:
    - ss1Zip; stroke, 1, green;
    - ssPs1; fill, gold;
    - ssPs21; fill, gold;
  blk2:
    - ss2Zip; stroke, 1, magenta;
    - ssPs1; fill, gold;
    - ssPs22; fill, gold;
  blk3:
    - ss1Cross; stroke, 1, blue;
    - ssPs1; fill, gold;
    - ssPs21; fill, gold;
  blk4:
    - ss2Cross; stroke, 1, maroon;
    - ssPs1; fill, gold;
    - ssPs22; fill, gold;

draw:
  - blk1
  - blk2; move; 250, 0
  - label; 125, 120; anchor; center; w; 60; h; 20; ||| zip
  - blk3; move;   0, 300
  - blk4; move; 250, 300
  - label; 125, 430; anchor; center; w; 60; h; 20; ||| cross


[wtht]


## Circle Series

We use the same keyword `circle`(short as `circ`) to define Circle Series as defining [Circle](./shapes.md#Circle). Change center and radius with Point Series and Number series, it is done.

```wtht
shapes:
  c1: circ; 0,0; 120;
pointSeries:
  psC1: c1; 0: 25: 280;
  psRandom: c1.25; c1.140; c1.230; c1.c; -50, c1.r / 2;
shapeSeries:
  ssC1: circ; psC1; 2: 1: 10; # count of circles is determined by the series which counts least
  ssC2: circ; psC1; 4;        # count of circles is determined by count of psC1
                              # If the series has only element, it will be duplicated
                              # to the minimal count of other series
  # Center is fixed, radius is series
  ssC3: circ; c1.c; c1.r / 2 : c1.r/8 : c1.r
  # Center is point series, radius is series (discrete numbers)
  ssC4: circ; psRandom; 4, 15, 7, 12, 6

blocks:
  blkC1:
    - c1; stroke, 1, black; dash, 3,3; fill, none;
    - ssC1; fill, red;
  blkC2:
    - c1; stroke, 1, black; dash, 3,3; fill, none;
    - ssC2; fill, red;
  blkC3:
    - ssC3; fill, none; stroke, 1, red;
  blkC4:
    - c1; stroke,1, black; dash, 3,3; fill, none;
    - ssC4; fill, royalblue, 0.6;
draw:
  - blkC1
  - blkC2; move; 260, 0;
  - blkC3; move; 0, 260;
  - blkC4; move; 260, 260;
```

[wtht] # Circle series
noTitle: yes

shapes:
  c1: circ; 0,0; 120;
pointSeries:
  psC1: c1; 0: 25: 280;
  psRandom: c1.25; c1.140; c1.230; c1.c; -50, c1.r / 2;
shapeSeries:
  ssC1: circ; psC1; 2: 1: 10; # count of circles is determined by the series which counts least
  ssC2: circ; psC1; 4;        # count of circles is determined by count of psC1
                              # If the series has only element, it will be duplicated
                              # to the minimal count of other series
  ssC3: circ; c1.c; c1.r / 2: c1.r/8: c1.r
  ssC4: circ; psRandom; 4, 15, 7, 12, 6

blocks:
  blkC1:
    - c1; stroke, 1, black; dash, 3,3; fill, none;
    - ssC1; fill, red;
    - label; c1.c; w; 150; h; 60; anchor; center; ||| Center is series, radius is series and increasing
  blkC2:
    - c1; stroke, 1, black; dash, 3,3; fill, none;
    - ssC2; fill, red;
    - label; c1.c; w; 150; h; 40; anchor; center; ||| Center is series, radius is fixed
  blkC3:
    - ssC3; fill, none; stroke, 1, red;
    - label; c1.c; w; 100; h;50; anchor; center; ||| Center is fixed, radius is series
  blkC4:
    - c1; stroke,1, black; dash, 3,3; fill, none;
    - ssC4; fill, royalblue, 0.6;
    - label; 0, -c1.r/2.2; w; 140; h;80; anchor; center; ||| Center is a series of discrete points, radius is a series of discrete numbers
draw:
  - blkC1
  - blkC2; move; 260, 0;
  - blkC3; move; 0, 260;
  - blkC4; move; 260, 260;
[wtht]


## Ellipse Series

We use the same keyword `ellipse`(short as `ell`) to define Ellipse Series as defining [Ellipse](./shapes.md#Ellipse). Change center, radius X, radius Y, and rotating degree with point series and number series, it is done.

```wtht
points:
  p0: 0,0

shapes:
  ln1: line; p0; -42
  c1:  circ; p0; 300

pointSeries:
  # Pick Points on Line, equally distanced
  psLn1:   ln1; -200 : 50 : 200
  # Pick points on a Circle by providing degrees
  psOnC1:  c1; 90, 130, 160, 180, 222, 255, 300, 333; 
  # Pick points on circle with degree series
  ps2OnC1: c1; 0 : 30 : 359

shapeSeries:
  # Center is series，Rx and Ry fixed，Rotating degree varies, is a series
  ssEll1:  ell; psLn1; 20; 12; -80: 20: 80;
  # Center fixed, Rx and Ry are series, rotating degree is series
  ssEll2:  ell; p0; 20: 20: 100; 14: 10: 70; -60: 20: 90;
  # Center varies, Rx, Ry are series, rotating angle is 0
  ssEll3:  ell; psOnC1; 20: 12: 104; 104: 12: 20; 
  # Center is fixed，Rx, Ry varies, rotating angle is zero
  ssEll3a: ell; c1.40; 20: 12: 104; 104: 12: 20; 
  # Center is point series, Rx and Ry fixed, rotating angle is series
  ssEll4:  ell; ps2OnC1; 60; 20; 0: 30: 359
blocks:
  blkEll1:
    - ssEll1; fill, royalblue, 0.4; stroke, 1, black;
    - psLn1; 2; black;
    - line; ln1; -220; 220; ||| stroke,1, black; dash; 3,3;
    - c1; stroke,1, black; dash, 3,3; fill, none;
    - ssEll3; fill, magenta, 0.4; stroke, 1, black;
    - psOnC1; 6; black;
    - ssEll3a; fill, magenta, 0.2; stroke, 1, black;
    - circ; c1.40; 6; ||| fill, yellow;
  blkEll2:
    - ssEll2; fill, none; stroke, 2, black;
    - c1; stroke,1, black; dash, 3,3; fill, none;
    - ssEll4; fill, yellow; stroke, 1, black;
    - ps2OnC1; 6; black;
    - circ; p0; 4 ||| fill, red;
draw:
  - blkEll1;
  - blkEll2; move; 0, 800
```
[wtht] # Make Ellipse Series
noTitle: yes
name: ellipseSeriesExample

points:
  p0: 0,0

shapes:
  ln1: line; p0; -42
  c1:  circ; p0; 300

pointSeries:
  # 在直线上等距取点
  psLn1:   ln1; -200 : 50 : 200
  # 在圆上随机按角度取点
  psOnC1:  c1; 90, 130, 160, 180, 222, 255, 300, 333; 
  # 在圆上等角距取点
  ps2OnC1: c1; 0 : 30 : 359

shapeSeries:
  # 圆心是点列，Rx 和 Ry 不变，旋转角变化，是数列
  ssEll1:  ell; psLn1; 20; 12; -80: 20: 80;
  # 圆心不变， Rx 和 Ry 变化是数列，旋转角变化，是数列
  ssEll2:  ell; p0; 20: 20: 100; 14: 10: 70; -60: 20: 90;
  # 圆心变化，Rx, Ry变化，旋转角不变，且为0
  ssEll3:  ell; psOnC1; 20: 12: 104; 104: 12: 20; 
  # 圆心不变，Rx, Ry变化，旋转角不变，且为0
  ssEll3a: ell; c1.40; 20: 12: 104; 104: 12: 20; 
  # 圆心为点列，Rx, Ry不变，旋转角变化，但和圆心在圆上的分布角相同
  ssEll4:  ell; ps2OnC1; 60; 20; 0: 30: 359
blocks:
  blkEll1:
    - ssEll1; fill, royalblue, 0.4; stroke, 1, black;
    - psLn1; 2; black;
    - line; ln1; -220; 220; ||| stroke,1, black; dash; 3,3;
    - c1; stroke,1, black; dash, 3,3; fill, none;
    - ssEll3; fill, magenta, 0.4; stroke, 1, black;
    - psOnC1; 6; black;
    - ssEll3a; fill, magenta, 0.2; stroke, 1, black;
    - circ; c1.40; 6; ||| fill, yellow;
  blkEll2:
    - ssEll2; fill, none; stroke, 2, black;
    - c1; stroke,1, black; dash, 3,3; fill, none;
    - ssEll4; fill, yellow; stroke, 1, black;
    - ps2OnC1; 6; black;
    - circ; p0; 4 ||| fill, red;
draw:
  - blkEll1;

[wtht]

[wtht] # Make Ellipse Series
noTitle: yes
use: ellipseSeriesExample
figure:
  - blkEll2;

[wtht]

## Rectangle Series

To define Rectangle Series, we use the same keyword `rectangle`(short as `rect`) as define [Rectangle](./shapes.md#Rectangle), we have three ways to define:

1. provide diagonal points, both could be point series
2. provide center and width/height, center could be point series, width/height could be number series
3. provide the top-left corner and width/height, all could be series

```wtht
shapeSeries:
  # diagonal points
  ssRect1: rect; psNW; psSE;
  # center + width/height
  ssRect2: rect; bycenter; wSeries; hSeries;
  # top-left corner + width/height
  ssRect3: rect; topLeftCornerSeries; wSeries; hSeries;
```

Following are some examples:

```wtht
points:
  p0: 0, 0

shapes:
  ln1: line; p0; 160;
  ln2: line; p0; 112;
  ln3: line; p0; 52;
  ls1: line; ln1;  100; 400;
  ls2: line; ln2;  60; 280;
  ls3: line; ln3; 60; 400;
  arc1: arc; p0; 400; 200; 0; 33; 180-33; cw;
  
pointSeries:
  psLs1:  ls1;  0 : 0.25 : 1
  psLs2:  ls2;  0 : 0.25 : 1
  psLs3:  ls3;  0 : 0.2  : 1
  psArc1: arc1; 0.2: 0.15: 1

shapeSeries:
  # By diagonal points
  ssLs1: rect; psLs1; psLs2;
  # By top-left corner and width/height series
  ssLs3: rect; psLs3; 100:10:10; 60: 5: 20;
  # By center and width/height
  ssLsC: rect; bycenter; p0; 160:20:80; 80:20:160;
  # center and width/height all are series
  ssRectOnArc: rect; bycenter; psArc1; 160:20:80; 80:20:160;
  # top-left corner is fixed, width and height are series
  ssRect1: rect; p0; 100: 10: 20; 80: 8: 16;

blocks:
  blkRect1:
    - ssLs1; fill, yellow, 0.6; stroke, 1, black;
    - line; ln1; 450; -10; ||| stroke, 1, black; dash, 3,3;
    - line; ln2; 330; -10; ||| stroke, 1, black; dash, 3,3;
    - ls1; stroke, 8, royalblue; opaque; 0.4;
    - ls2; stroke, 8, royalblue; opaque; 0.4;
    - psLs1; 3; red;
    - psLs2; 3; red;
    - circ; p0; 5; ||| fill, blue;
    - ls3; stroke, 8, royalblue; opaque; 0.4;
    - line; ln3; -10; 450; ||| stroke, 1, black; dash, 3,3;
    - ssLs3; fill, magenta, 0.4; stroke, 1, black;
    - psLs3; 3; red;
  blkRect2:
    - ssLsC;fill, magenta; 0.4; stroke, 1, black;
    - circ; p0; 3; ||| fill, black;
    - arc1; stroke, 1, black; dash, 3,3; fill, none;
    - psArc1; 3; black;
    - ssRectOnArc; fill, magenta, 0.4; stroke, 1, black;
draw:
  - blkRect1
  - blkRect2;  move; -50, 420
  - ssRect1; fill, magenta, 0.4; stroke, 1, black; move; -350, 380  
```


[wtht] # Make Rectangle Series
noTitle: yes

points:
  p0: 0, 0

shapes:
  ln1: line; p0; 160;
  ln2: line; p0; 112;
  ln3: line; p0; 52;
  ls1: line; ln1;  100; 400;
  ls2: line; ln2;  60; 280;
  ls3: line; ln3; 60; 400;
  arc1: arc; p0; 400; 200; 0; 33; 180-33; cw;
  
pointSeries:
  psLs1: ls1; 0 : 0.25 : 1
  psLs2: ls2; 0 : 0.25 : 1
  psLs3: ls3; 0 : 0.2  : 1
  psArc1: arc1; 0.2: 0.15: 1

shapeSeries:
  # By diagonal points
  ssLs1: rect; psLs1; psLs2;
  # By top-left corner and width/height series
  ssLs3: rect; psLs3; 100:10:10; 60: 5: 20;
  # By center and width/height
  ssLsC: rect; bycenter; p0; 160:20:80; 80:20:160;
  # center and width/height all are series
  ssRectOnArc: rect; bycenter; psArc1; 160:20:80; 80:20:160;
  # top-left corner is fixed, width and height are series
  ssRect1: rect; p0; 100: 10: 20; 80: 8: 16;

blocks:
  blkRect1:
    - ssLs1; fill, yellow, 0.6; stroke, 1, black;
    - line; ln1; 450; -10; ||| stroke, 1, black; dash, 3,3;
    - line; ln2; 330; -10; ||| stroke, 1, black; dash, 3,3;
    - ls1; stroke, 8, royalblue; opaque; 0.4;
    - ls2; stroke, 8, royalblue; opaque; 0.4;
    - psLs1; 3; red;
    - psLs2; 3; red;
    - circ; p0; 5; ||| fill, blue;
    - ls3; stroke, 8, royalblue; opaque; 0.4;
    - line; ln3; -10; 450; ||| stroke, 1, black; dash, 3,3;
    - ssLs3; fill, magenta, 0.4; stroke, 1, black;
    - psLs3; 3; red;
    - label; psLs1[5]; w; 180; h; 40; anchor; ml; offset; -20, -80; left;  ||| By diagonal points, all series
    - label; psLs3[1]; w; 160; h; 52; anchor; bl; offset; 30, -10; left;  ||| By Top-Left corner and width/height, all series
  blkRect2:
    - ssLsC;fill, magenta; 0.4; stroke, 1, black;
    - circ; p0; 3; ||| fill, black;
    - arc1; stroke, 1, black; dash, 3,3; fill, none;
    - psArc1; 3; black;
    - ssRectOnArc; fill, magenta, 0.4; stroke, 1, black;
    - label; p0.x + 100, p0.y; w; 180; h; 40; anchor; ml; ||| bycenter, center fixed, width/height are series
    - label; psArc1[3]; w; 180; h; 20; anchor; tm; offset; 0, 80; ||| bycenter, all series
draw:
  - blkRect1
  - blkRect2;  move; -50, 420
  - ssRect1; fill, magenta, 0.4; stroke, 1, black; move; -350, 380
  - label; p0.x -350 - 20, p0.y + 380 - 60; w; 140; h; 60; ||| Top-left corner fixed, width/height are series
  
[wtht]

## Triangle Series

We use the same keyword `triangle`(short as`tri`) as [Triangle](./shapes.md#Triangle) to define Triangle Series. There is only one way to define it, provide the three vertices as point series.

```wtht
shapeSeries:
  ssTri: triangle; psPointA; psPointB; psPointC
```

Following is some example:

```wtht
points:
  p0: -230, 0
  p1: p0.x + 180, p0.y + 20
shapes:
  tri0: tri; -50, -240; -300, 40; 220, 130;
  ls1: line; p0; -62; 20; 300;
  ls2: line; p0; -32; 18; 400;

pointSeries:
  psA: tri0; 1; 0.1 : 0.1 : 0.9
  psB: tri0; 2; 0.1 : 0.1 : 0.9
  psC: tri0; 3; 0.1 : 0.1 : 0.9
  psLs1: ls1; 0.2 : 0.2 : 1
  psLs2: ls2; 0.2 : 0.2 : 1
shapeSeries:
  ssTri1: triangle; psA; psB; psC;
  ssTri2: triangle; psLs1; p1; psLs2;
blocks:
  blkTri1:
    - ssTri1; fill, magenta, 0.2; stroke, 1, black;
    - tri0; fill, none; stroke, 2, black;
  blkTri2:
    - line; p0; ls1.deg; -20; ls1.len + 40; ||| stroke, 1, black; dash, 3,3;
    - line; p0; ls2.deg; -20; ls2.len + 40; ||| stroke, 1, black; dash, 3,3;
    - circ; p0; 4; ||| fill, red;
    - ssTri2; fill, blue, 0.2; stroke, 1, grey;
    - psLs1; 3; blue;
    - psLs2; 3; blue;
    - circ; p1; 4; ||| fill, red, 0.4;
draw:
  - blkTri1;
  - blkTri2; move; 0, 400;
```

[wtht] # Make Triangle Series
noTitle: yes

points:
  p0: -230, 0
  p1: p0.x + 180, p0.y + 20
shapes:
  tri0: tri; -50, -240; -300, 40; 220, 130;
  ls1: line; p0; -62; 20; 300;
  ls2: line; p0; -32; 18; 400;

pointSeries:
  psA: tri0; 1; 0.1 : 0.1 : 0.9
  psB: tri0; 2; 0.1 : 0.1 : 0.9
  psC: tri0; 3; 0.1 : 0.1 : 0.9
  psLs1: ls1; 0.2 : 0.2 : 1
  psLs2: ls2; 0.2 : 0.2 : 1
shapeSeries:
  ssTri1: tri; psA; psB; psC;
  ssTri2: triangle; psLs1; p1; psLs2;
blocks:
  blkTri1:
    - ssTri1; fill, magenta, 0.2; stroke, 1, black;
    - tri0; fill, none; stroke, 2, black;
  blkTri2:
    - line; p0; ls1.deg; -20; ls1.len + 40; ||| stroke, 1, black; dash, 3,3;
    - line; p0; ls2.deg; -20; ls2.len + 40; ||| stroke, 1, black; dash, 3,3;
    - circ; p0; 4; ||| fill, red;
    - ssTri2; fill, blue, 0.2; stroke, 1, grey;
    - psLs1; 3; blue;
    - psLs2; 3; blue;
    - circ; p1; 4; ||| fill, red, 0.4;
draw:
  - blkTri1;
  - blkTri2; move; 0, 400;
[wtht]


## Polyline Series

We use the same keyword `polyline`(short as `pline` or `pln`) as [Polyline](./shapes.md#Polyline) to define Polyline Series. Only one way is used to present the parameters, just list the points, either a single point or point series. When it is a single point, all the polylines will go through it. If all the points given are series, then the polyline series will have a count as the point series which has least count.

```wtht
vars:
  w: 700
  h: 400

points:
  pFixed: w / 3, 20

shapes:
  l1:  line; -w/2.4, -h/8; -74;
  ls1: line; l1; -200; 200;
  l2:  line; -w/6, -h/6; 72;
  ls2: line; l2; 100; -100;
  l3:  line; w/10, 0; -58;
  ls3: line; l3; -h/3; h/2.5;
  l4:  line; w/2.1, 0; 90;
  ls4: line; l4; -h/4; h/4;

pointSeries:
  psLs1: ls1; 0.1: 0.1: 0.9
  psLs2: ls2; 0.1: 0.1: 0.9
  psLs3: ls3; 0.1: 0.1: 0.9
  psLs4: ls4; 0.1: 0.1: 0.9
  psLs3a: ls3; 0.2, 0.3, 0.5, 0.7;

shapeSeries:
  ssPolyLines: pln; psLs1; psLs2; psLs3; pFixed; psLs4;
  ssPolyLines2: pln; psLs1; psLs2; psLs3a; psLs4;
blocks:
  blkCoordinates:
    - line; 0,0; 0; -w/2; w/2; ||| stroke, 1, grey; dash, 3,3;
    - line; 0,0; 90; -h/2; h/2.5; ||| stroke,1, grey; dash, 3,3;
  blkLines:
    - ls1; stroke, 1, black;
    - psLs1; 3; blue;
    - ls2; stroke, 1, black;
    - psLs2; 3; blue;
    - ls3; stroke, 1, black;
    - psLs3; 3; blue;
    - ls4; stroke, 1, black;
    - psLs4; 3; blue;
    - circ; pFixed; 4; ||| fill, black;;
  blkSS1:
    - blkCoordinates
    - blkLines;
    - ssPolyLines; stroke,1,red; fill, none;
  blkSS2:
    - blkCoordinates
    - blkLines;
    - ssPolyLines2; stroke, 1, green; fill, none;
draw:
  - blkSS1
  - blkSS2;move; 0, h;
```

[wtht] # Make Polyline series
noTitle: yes

vars:
  w: 700
  h: 400

points:
  pFixed: w / 3, 20

shapes:
  l1: line; -w/2.4, -h/8; -74;
  ls1: line; l1; -200; 200;
  l2: line; -w/6, -h/6; 72;
  ls2: line; l2; 100; -100;
  l3: line; w/10, 0; -58;
  ls3: line; l3; -h/3; h/2.5;
  l4: line; w/2.1, 0; 90;
  ls4: line; l4; -h/4; h/4;

pointSeries:
  psLs1: ls1; 0.1: 0.1: 0.9
  psLs2: ls2; 0.1: 0.1: 0.9
  psLs3: ls3; 0.1: 0.1: 0.9
  psLs4: ls4; 0.1: 0.1: 0.9
  psLs3a: ls3; 0.2, 0.3, 0.5, 0.7;

shapeSeries:
  ssPolyLines: pln; psLs1; psLs2; psLs3; pFixed; psLs4;
  ssPolyLines2: pln; psLs1; psLs2; psLs3a; psLs4;
blocks:
  blkCoordinates:
    - line; 0,0; 0; -w/2; w/2; ||| stroke, 1, grey; dash, 3,3;
    - line; 0,0; 90; -h/2; h/2.5; ||| stroke,1, grey; dash, 3,3;
  blkLines:
    - ls1; stroke, 1, black;
    - psLs1; 3; blue;
    - ls2; stroke, 1, black;
    - psLs2; 3; blue;
    - ls3; stroke, 1, black;
    - psLs3; 3; blue;
    - ls4; stroke, 1, black;
    - psLs4; 3; blue;
    - circ; pFixed; 4; ||| fill, black;;
  blkSS1:
    - blkCoordinates
    - blkLines;
    - ssPolyLines; stroke,1,red; fill, none;
    - label; psLs4[1]; offset; -10, -h/4; w; 200; h; 50; align; right; anchor; mr; ||| When some point is a single point, all the polylines in series will go through it
  blkSS2:
    - blkCoordinates
    - blkLines;
    - ssPolyLines2; stroke, 1, green; fill, none;  
    - label; psLs4[1]; offset; -10, -h/4; w; 240; h; 50; align; right; anchor; mr; ||| When all are point series, count of polylines will be equal to the least count of point series
draw:
  - blkSS1
  - blkSS2;move; 0, h;
[wtht]


## Polygon Series

We use the same keyword `polygon`(short as `plg`) as defining [Polygon](./shapes.md#Polygon) to define Polygon Series. We have only one way to define, just providing the vertices, either single point or point series, similar to [Polyline Series](#Polyline%20Series).

```wtht
vars:
  R: 120

shapes:
  c1: circ; 0,0; R;
  ln1: line; c1.60;   30;
  ln2: line; c1.120;  90;
  ln3: line; c1.180; 150;
  ln4: line; c1.240; 210;
  ln5: line; c1.300; 270;
  ln6: line; c1.0;   330;
pointSeries:
  psLs1: ln1; -R/2: R/5: R/2;
  psLs2: ln2; -R/2: R/5: R/2;
  psLs3: ln3; -R/2: R/5: R/2;
  psLs4: ln4; -R/2: R/5: R/2;
  psLs5: ln5; -R/2: R/5: R/2;
  psLs6: ln6; -R/2: R/5: R/2;
  psLs5a: psLs5[2]; psLs5[6]; psLs5[4];
shapeSeries:
  ssPlg1: polygon; psLs1; psLs2; psLs3; psLs4; psLs5; psLs6;
  ssPlg2: polygon; psLs1; psLs2; psLs3(4); psLs4; psLs5; psLs6;
  ssPlg3: polygon; psLs1; psLs2; psLs3; psLs4; psLs5a; psLs6;
blocks:
  refLines:
    - c1; stroke, 1, red; fill, none; dash; 3,3;
    - psLs1; 3; blue;
    - line; psLs1[1]; psLs1[6]; ||| stroke, 1, grey;
    - psLs2; 3; blue;
    - psLs3; 3; blue;
    - psLs4; 3; blue;
    - psLs5; 3; blue;
    - psLs6; 3; blue;
  polygons:
    - refLines;
    - ssPlg1; fill, none; stroke, 1, black;
  polygons2:
    - refLines;
    - ssPlg2; fill, none; stroke, 1, black;
  polygons3:
    - refLines;
    - ssPlg3; fill, none; stroke, 1, black;
draw:
  - polygons;
  - polygons2; move; R * 3, 0;
  - polygons3; move; R * 3 / 2, R * 3;
```

[wtht] # Make Polygon series
noTitle: yes

vars:
  R: 120

shapes:
  c1: circ; 0,0; R;
  ln1: line; c1.60;   30;
  ln2: line; c1.120;  90;
  ln3: line; c1.180; 150;
  ln4: line; c1.240; 210;
  ln5: line; c1.300; 270;
  ln6: line; c1.0;   330;
pointSeries:
  psLs1: ln1; -R/2: R/5: R/2;
  psLs2: ln2; -R/2: R/5: R/2;
  psLs3: ln3; -R/2: R/5: R/2;
  psLs4: ln4; -R/2: R/5: R/2;
  psLs5: ln5; -R/2: R/5: R/2;
  psLs6: ln6; -R/2: R/5: R/2;
  psLs5a: psLs5[2]; psLs5[6]; psLs5[4];
shapeSeries:
  ssPlg1: polygon; psLs1; psLs2; psLs3; psLs4; psLs5; psLs6;
  ssPlg2: polygon; psLs1; psLs2; psLs3(4); psLs4; psLs5; psLs6;
  ssPlg3: polygon; psLs1; psLs2; psLs3; psLs4; psLs5a; psLs6;

blocks:
  refLines:
    - c1; stroke, 1, red; fill, none; dash; 3,3;
    - psLs1; 3; blue;
    - line; psLs1[1]; psLs1[6]; ||| stroke, 1, grey;
    - psLs2; 3; blue;
    - psLs3; 3; blue;
    - psLs4; 3; blue;
    - psLs5; 3; blue;
    - psLs6; 3; blue;
  polygons:
    - refLines;
    - ssPlg1; fill, none; stroke, 1, black;
    - label; 0,0; w; 80; h; 50; anchor; center ||| All vertices are point series
  polygons2:
    - refLines;
    - ssPlg2; fill, none; stroke, 1, black;
    - label; 0,0; w; 80; h; 40; anchor; center ||| One vertex is fixed
  polygons3:
    - refLines;
    - ssPlg3; fill, none; stroke, 1, black;
    - label; 0,0; w; 100; h; 60; anchor; center ||| Point Count of Series varies

draw:
  - polygons;
  - polygons2; move; R * 3, 0;
  - polygons3; move; R * 3 / 2, R * 3;
[wtht]


## Regular Polygon Series

We use the same keyword `npolygon`(short as `plgn`) as [Regular Polygon](./shapes.md#Regular%20Polygon) to define Regular Polygon Series. It is quite similar to define Regular Polygon, just replace number to number series, and point to point series.

```wtht
vars:
  w: 666

shapes:
  lsX: line; 0,0; 0; -w/2; w/2;

pointSeries:
  psX: lsX; 0, 0.08, 0.22, 0.4, 0.63, 0.9;

shapeSeries:
  ssNplg1: plgn; 3:1:8; psX; 20: (w/5-20)/7: w/5; 0: 33: 360;
  ssNplg2: plgn; 3:1:8; 0,0; 20: (w/5-20)/7: w/5; 0: 33: 360;
  ssNplg3: plgn; 7;     0,0; 20: (w/5-20)/7: w/5; 0: 33: 360;
blocks:
  blkNplg1:
    - lsX; stroke, 1, grey; dash, 3,3;
    - psX; 3; blue;
    - ssNplg1; fill, magenta, 0.4; stroke, 1, black;
  blkNplg2:
    - ssNplg2; fill, royalblue, 0.3; stroke, 1, black;
  blkNplg3:
    - ssNplg3; fill, silver, 0.3; stroke, 1, black;
draw:
  - blkNplg1;
  - blkNplg2; move; -w/4, w/2.2;
  - blkNplg3; move;  w/4, w/2.2;
```

[wtht] # Make N-Polygon Series
noTitle:yes
vars:
  w: 666

shapes:
  lsX: line; 0,0; 0; -w/2; w/2;

pointSeries:
  psX: lsX; 0, 0.08, 0.22, 0.4, 0.63, 0.9;

shapeSeries:
  ssNplg1: plgn; 3:1:8; psX; 20: (w/5-20)/7: w/5; 0: 33: 360;
  ssNplg2: plgn; 3:1:8; 0,0; 20: (w/5-20)/7: w/5; 0: 33: 360;
  ssNplg3: plgn; 7;     0,0; 20: (w/5-20)/7: w/5; 0: 33: 360;
blocks:
  blkNplg1:
    - lsX; stroke, 1, grey; dash, 3,3;
    - psX; 3; blue;
    - ssNplg1; fill, magenta, 0.4; stroke, 1, black;
    - label; 0, -w/7; anchor; bm; w; 260; h; 40; ||| All the parameters are series
  blkNplg2:
    - ssNplg2; fill, royalblue, 0.3; stroke, 1, black;
    - label; 0, -w/6; anchor; bm; w; 160; h; 40;  ||| Center is fixed, others are series
  blkNplg3:
    - ssNplg3; fill, silver, 0.3; stroke, 1, black;
    - label; 0, -w/5; anchor; bm; w; 160; h; 40; ||| Center is fixed, edge count (17) is fixed
draw:
  - blkNplg1;
  - blkNplg2; move; -w/4, w/2.2;
  - blkNplg3; move;  w/4, w/2.2;
[wtht]

## Bezier Curve Series

We use the same keyword as Bezier Curve to define Series, `bezier2`(short as `b2`) is for Bezier 2, and `bezier3`(short as `b3`) is for Bezier 3. By replacing points to point series, we will define shape series.

- Bezier 2 Series

```wtht
vars:
  w: 300
  d: w/3
points:
  p1: -d, 0;
  p2: 0,d;

shapes:
  lnYpX: line; 0,0; -45; 0; w * 0.7;
  ls2: line; lnYpX.e; 100; 300; 100;

pointSeries:
  ps1: lnYpX; 0.2: 0.2: 1
  psMarks: p1; p2; lnYpX.e;
  ps2: ls2; 0.2: 0.2: 1

shapeSeries:
  ssB2:  b2; p1; ps1; p2;
  ssB2a: b2; p1; ps1; ps2;

blocks:
  refCoordinates:
    - line; 0,0;   0; -w/2; w/2; ||| stroke, 1, grey; 
      dash; 3,3; marker, arrow1;
    - line; 0,0; -90; -w/2; w/2; ||| stroke, 1, grey; 
      dash; 3,3; marker, arrow1;
  blkB2:
    - refCoordinates;
    - ssB2; fill,royalblue, 0.2; stroke, 1, black;
  blkB2a:
    - refCoordinates;
    - ssB2a; fill,royalblue, 0.2; stroke, 1, black;

draw:
  - blkB2;
  - blkB2a; move; w*1.1, 0;
```

[wtht] # Make Bezier2 Series
noTitle: yes

vars:
  w: 300
  d: w/3
points:
  p1: -d, 0;
  p2: 0,d;

shapes:
  lnYpX: line; 0,0; -45; 0; w * 0.7;
  ls2: line; lnYpX.e; 100; 300; 100;

pointSeries:
  ps1: lnYpX; 0.2: 0.2: 1
  psMarks: p1; p2; lnYpX.e;
  ps2: ls2; 0.2: 0.2: 1

shapeSeries:
  ssB2:  b2; p1; ps1; p2;
  ssB2a: b2; p1; ps1; ps2;

blocks:
  refCoordinates:
    - line; 0,0;   0; -w/2; w/2; ||| stroke, 1, grey; 
      dash; 3,3; marker, arrow1;
    - line; 0,0; -90; -w/2; w/2; ||| stroke, 1, grey; 
      dash; 3,3; marker, arrow1;
  blkB2:
    - refCoordinates;
    - line; p1; lnYpX.e; ||| stroke, 1, aqua;
    - line; p2; lnYpX.e; ||| stroke, 1, aqua;
    - line; p1; ps1[1]; ||| stroke,0.5, magenta;
    - line; p2; ps1[1]; ||| stroke,0.5, magenta;
    - line; p1; ps1[2]; ||| stroke,0.5, magenta;
    - line; p2; ps1[2]; ||| stroke,0.5, magenta;
    - line; p1; ps1[3]; ||| stroke,0.5, magenta;
    - line; p2; ps1[3]; ||| stroke,0.5, magenta;
    - line; p1; ps1[4]; ||| stroke,0.5, magenta;
    - line; p2; ps1[4]; ||| stroke,0.5, magenta;
    - ssB2; fill,royalblue, 0.2; stroke, 1, black;
    - ps1; 3; blue;
    - psMarks; 3; magenta;
    - label; 0, w/2; anchor; tm; w; 180; h; 50; ||| Starting and Ending points are fixed, the control point are series
  blkB2a:
    - refCoordinates;
    - line; p1; lnYpX.e; ||| stroke, 1, aqua;
    - line; ps2[1]; lnYpX.e; ||| stroke, 1, aqua;
    - line; p1; ps1[1]; ||| stroke,0.5, magenta;
    - line; ps2[1]; ps1[1]; ||| stroke,0.5, magenta;
    - line; p1; ps1[2]; ||| stroke,0.5, magenta;
    - line; ps2[2]; ps1[2]; ||| stroke,0.5, magenta;
    - line; p1; ps1[3]; ||| stroke,0.5, magenta;
    - line; ps2[3]; ps1[3]; ||| stroke,0.5, magenta;
    - line; p1; ps1[4]; ||| stroke,0.5, magenta;
    - line; ps2[4]; ps1[4]; ||| stroke,0.5, magenta;
    - ssB2a; fill,royalblue, 0.2; stroke, 1, black;
    - ps1; 3; blue;
    - ps2; 3; magenta;
    - circ; p1; 3; ||| fill, magenta;
    - label; 0, w/2; anchor; tm; w; 180; h; 50; ||| starting point is fixed, control point and end point are series

draw:
  - blkB2;
  - blkB2a; move; w*1.1, 0;

[wtht]


- Bezier 3 Series

```wtht

vars:
  w: 260
  d: w/3
points:
  p1: -d, -d;
  p2:  d,  d;

shapes:
  lnH1: line; p1; 0; w
  lnH2: line; p2; 180; w
  lnSlope1: line; p1; -45; -w/2; w/2;
  lnSlope2: line; p2; -45; -w/2; w/2;

pointSeries:
  ps1: lnH1; 0.2: 0.2: 1
  ps2: lnH2; 0.2: 0.2: 1
  ps3: lnSlope1; 0.2: 0.2: 1
  ps4: lnSlope2; 0.2: 0.2: 1
  psMarks: p1; p2;

shapeSeries:
  ssB3:  b3; p1; ps1; ps2; p2;
  ssB3a: b3; ps3; ps1; ps2; ps4;

blocks:
  refCoordinates:
    - line; 0,0;   0; -w/2; w/2; ||| stroke, 1, grey; 
      dash; 3,3; marker, arrow1;
    - line; 0,0; -90; -w/2; w/2; ||| stroke, 1, grey;
      dash; 3,3; marker, arrow1;
  blkB3:
    - refCoordinates;
    - ssB3; fill,royalblue, 0.2; stroke, 1, black;
  blkB3a:
    - refCoordinates;
    - ssB3a; fill,royalblue, 0.2; stroke, 1, black;

draw:
  - blkB3;
  - blkB3a; move; w*1.4, 0;

```

[wtht] # Make Bezier3 Series
noTitle: yes

vars:
  w: 260
  d: w/3
points:
  p1: -d, -d;
  p2:  d,  d;

shapes:
  lnH1: line; p1; 0; w
  lnH2: line; p2; 180; w
  lnSlope1: line; p1; -45; -w/2; w/2;
  lnSlope2: line; p2; -45; -w/2; w/2;

pointSeries:
  ps1: lnH1; 0.2: 0.2: 1
  ps2: lnH2; 0.2: 0.2: 1
  ps3: lnSlope1; 0.2: 0.2: 1
  ps4: lnSlope2; 0.2: 0.2: 1
  psMarks: p1; p2;

shapeSeries:
  ssB3:  b3; p1; ps1; ps2; p2;
  ssB3a: b3; ps3; ps1; ps2; ps4;

blocks:
  refCoordinates:
    - line; 0,0;   0; -w/2; w/2; ||| stroke, 1, grey;
      dash; 3,3; marker, arrow1;
    - line; 0,0; -90; -w/2; w/2; ||| stroke, 1, grey;
      dash; 3,3; marker, arrow1;
  blkB3:
    - refCoordinates;
    - line; p1; ps1[5]; ||| stroke, 1, aqua;
    - line; p2; ps2[5]; ||| stroke, 1, aqua;
    - ps1; 3; blue;
    - ps2; 3; blue;
    - ssB3; fill,royalblue, 0.2; stroke, 1, black;
    - psMarks; 3; magenta;
    - label; 0, w/1.7; anchor; tm; w; 180; h; 60; ||| Starting and Ending points are fixed, control points are series
  blkB3a:
    - refCoordinates;
    - ps3; 3; magenta;
    - ps4; 3; magenta;
    - line; ps3[1]; ps1[1]; ||| stroke,1, aqua;
    - line; ps3[2]; ps1[2]; ||| stroke,1, aqua;
    - line; ps3[3]; ps1[3]; ||| stroke,1, aqua;
    - line; ps3[4]; ps1[4]; ||| stroke,1, aqua;
    - line; ps3[5]; ps1[5]; ||| stroke,1, aqua;
    - line; ps2[1]; ps4[1]; ||| stroke,1, aqua;
    - line; ps2[2]; ps4[2]; ||| stroke,1, aqua;
    - line; ps2[3]; ps4[3]; ||| stroke,1, aqua;
    - line; ps2[4]; ps4[4]; ||| stroke,1, aqua;
    - line; ps2[5]; ps4[5]; ||| stroke,1, aqua;    
    - ssB3a; fill,royalblue, 0.2; stroke, 1, black;
    - ps1; 3; blue;
    - ps2; 3; blue;
    - label; 0, w/1.7; anchor; tm; w; 180; h; 60; ||| Starting, Ending and Controlling Points are all Series

draw:
  - blkB3;
  - blkB3a; move; w*1.4, 0;

[wtht]


## Arc Series

We use the same keyword `arc` as [Arc](./shapes.md#Arc) to define Arc Series. To not make it too complicated, we can only make Arc Series from Circles or Ellipses, thus when defining Arc Series, we need to define the Circle Series or Ellipse Series first. After that, we will refer to it and provide starting and ending degrees and walking direction, i.e. `cw` or `ccw` to build the arcs.

```wtht
shapeSeries:
  ssCircle: circle; psCenter; Rmin | Rstep | Rmax;
  ssEllipse: ellipse; psCenter; 
    RxMin | RxStep | RxMax; 
    RyMin | RyStep | RyMax; 
    rotDegMin | rotDegStep | rotDegMax;
  ssArc1: ssCircle; 
    degStart | degStartStep | degStartMax;
    degEnd | degEndStep | degEndMax;
    cw;  # cw and ccw are rotating directions
  ssArc2: ssEllipse;
    degStart | degStartStep | degStartMax;
    degEnd | degEndStep | degEndMax;
    cw;  # cw and ccw are rotating directions
```

Following are some examples:

```wtht
vars:
  Rmax: 150
points:
  p0: 0,0

shapeSeries:
  ssCirc: circ; p0; Rmax/8: Rmax/10: Rmax
  ssArc1: arc; ssCirc; 222; 333; cw
  ssArc2: arc; ssCirc; 0; 30: 15: 300; cw;
  ssArc3: arc; ssCirc; 0: 10: 200; 30: 20: 360; cw;
  ssEll:  ell; p0; 
    Rmax * 0.75 / 4 : Rmax * 0.75 / 10 : Rmax * 0.75;
    Rmax / 4 : Rmax / 10 : Rmax;
    30: 15: 300;
  ssArc4: arc; ssEll; 100; 120: 20: 300; ccw;

blocks:
  blkCircRef:
    - ssCirc; fill, none; stroke, 1, black; dash, 3,3;
  blkArc1:
    - blkCircRef
    - ssArc1; fill, none; stroke, 6, black; opaque; 0.4;
  blkArc2:
    - blkCircRef
    - ssArc2; fill, none; stroke, 6, red;   opaque; 0.4;
  blkArc3:
    - blkCircRef
    - ssArc3; fill, none; stroke, 6, magenta; opaque; 0.4;
  blkEllRef:
    - ssEll;  fill, none; stroke, 1, black; dash, 3,3;
  blkArc4:
    - blkEllRef
    - ssArc4; fill, none; stroke, 6, aqua; opaque, 0.4;
  
draw:
  - blkArc1
  - blkArc2; move; Rmax * 2.2, 0;
  - blkArc3; move; 0, Rmax * 2.2
  - blkArc4; move; Rmax * 2.2, Rmax * 2.2
```

[wtht] # Make Arc Series
noTitle: yes

vars:
  Rmax: 150
points:
  p0: 0,0

shapeSeries:
  ssCirc: circ; p0; Rmax/8: Rmax/10: Rmax
  ssArc1: arc; ssCirc; 222; 333; cw
  ssArc2: arc; ssCirc; 0; 30: 15: 300; cw;
  ssArc3: arc; ssCirc; 0: 10: 200; 30: 20: 360; cw;
  ssEll:  ell; p0; 
    Rmax * 0.75 / 4: Rmax * 0.75 / 10: Rmax * 0.75;
    Rmax / 4: Rmax / 10: Rmax; 
    30: 15: 300;
  ssArc4: arc; ssEll; 100; 120: 20: 300; ccw;

blocks:
  blkCircRef:
    - ssCirc; fill, none; stroke, 1, black; dash, 3,3;
  blkArc1:
    - blkCircRef
    - ssArc1; fill, none; stroke, 6, black; opaque; 0.4;
  blkArc2:
    - blkCircRef
    - ssArc2; fill, none; stroke, 6, red; opaque; 0.4;
  blkArc3:
    - blkCircRef
    - ssArc3; fill, none; stroke, 6, magenta; opaque; 0.4;
  blkEllRef:
    - ssEll;  fill, none; stroke, 1, black; dash, 3,3;
  blkArc4:
    - blkEllRef
    - ssArc4; fill, none; stroke, 6, aqua; opaque, 0.4;
  
draw:
  - blkArc1
  - blkArc2; move; Rmax * 2.2, 0;
  - blkArc3; move; 0, Rmax * 2.2
  - blkArc4; move; Rmax * 2.2, Rmax * 2.2

[wtht]
