# Intersection Points

Having the basic shapes we have defined, we can define the intersection points of two shapes. We can define a single intersection point under the `xpoints` root tag, or we can define a point series which contains all the intersection points of two shapes under the `pointSeries` root tag.

Consider that there might be two or more intersection points for two given shapes, we need some **directional** indicator to tell which point should be picked when there are multiple intersections resolved when defining a named point under `xpoints`.

When define point series of intersections, no directional indicator is required, all the intersections will be included in the series.

Following sample will demonstrate how the xpoint or point series is defined.

```wtht
shapes:
  shape1: what ever you want to define...
  shape2: what ever you want to define...
xpoints:
  xp1: shape1; shape2;          # when no directional indicator specified,
                                # a 'dummy first' point will be returned
                                # it is uncertain, so it is better to present the indicator
  xp2: shape1; shape2; n;       # north, the top-most/northern-most one
  xp3: shape1; shape2; se;      # southeast, the right-bottom/south-eastern-most one
pointSeries:
  ps1: shape1; x; shape2;       # put an 'x' as the second parameter when defining
                                # point series will get a point series of intersections
```
All the possible directional indicators are:

- `n`: the northern-most or top-most
- `s`: the southern-most or bottom-most
- `w`: the western-most or left-most
- `e`: the eastern-most or right-most
- `ne`: the north-eastern or most top-right
- `nw`: the north-western or most top-left
- `se`: the south-eastern or most bottom-right
- `sw`: the south-western or most bottom-left
- `xmiddle`: the point having `x` coordinate in the middle when sorted
- `ymiddle`: the point having 'y' coordinate in the middle when sorted
- `xymiddle` or `center`: the point relatively at center of all the points


[i] Note: the directional indicator will be translated into some condition to sort the resolved intersection points, and pick the first one or middle one after sorted. So it is possible that the indicator you gave doesn't pick the point you expected, and as the base shapes are changing as of parameter changed, the picked point might vary. So please use appropriate indicator to pick the point you want and try multiple times. And try to make the point relatively fixed to make a sound design. One example to explain this is this: the two shapes have multiple intersection points, one is quite far from others, say, stays at right, then when you specify the point at `ne` might return this "eastern-most" point, not the one which is more northern. In this case, there is no significantly "north-eastern" point, so you can just use `e` to pick the "easter-most" one and `n` to pick the northern one. For `xmiddle`, `ymiddle`, and `xymiddle`, after sorted, the point in middle will be picked when count is odd, and the point having smaller value of middle two will be picked if count is even. i.e. point 3 will be picked if count 5, and point 4 will picked if count is 8.

Following is a simple example, we can define `xpoint` or point series accordingly.

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
    - label; xp12NE; anchor; bl; w; 60; h; 40; ||| NE 
    - label; xp12E;  anchor; tl; w; 60; h; 30; ||| E 
  blk2:
    - ells; stroke, 1, cc; fill, none;
    - psOuter; 4; red;
    - psInner; 4; blue;
draw:
  - blk1;
  - blk2; move; ry * 2.2, 0;
[wtht]

Following is another complex example, you can resolve intersection points between different shapes.

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

When drawn out:

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

[i] Note: because we use some iterative algorithm to calculate intersection of complex curves, e.g. Arcs and Bezier3 Curves, it might need a lot of time to get the result, especially when the parameters of two shapes are quite critical, e.g. get the intersection of two nearly-parallel portions of two curves. And there is some possibility (though very low) that no intersection can be resolved. So when you encounter some slow-down when previewing or rendering when designing very large and complicated graph that has a lot of intersection involved, please be patient and give the program a little time, or optimize your design to avoid using unnecessary intersection points. 
