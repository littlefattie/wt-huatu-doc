# Shape Transform

After some shape is defined, it is straightforward to make duplications by moving (**Translate**) it to some other place, or rotating (**Rotate**) around some point. For instance, I want to design some graph, where there are 6 five-angle regular stars placed equally on a large circle, and all their `top-angle` pointing to the center of the large circle, then all I need to do is just defining the star and moving and rotating them to make them placed on the circle with proper direction.

[i] Here the **Translate** and **Rotate** operations are ways of duplicating the already defined shapes. It is as similar as our way of defining basic shapes. Difference is that we use the *operation keywords* rather than *shape type keywords* to present the define.


## Translate

We defined three ways to translate, **Translate along x-axis**, **Translate along y-axis**, and **Translate on both X/Y axis**, the differences are that different *operation keyword* is used and parameters required are different.

```wtht
shapes:
  shape1: polygon; ... # Other parameters to define your basic shape
  # use moveX to move along x-axis, present your target shape to move and the x-offset
  shape1MoveX:   moveX; shape1; xOffset
  # use moveY to move along y-axis, present your target shape to move and the y-offset
  shape1MoveY:   moveY; shape1; yOffset
  # use moveby (or move) to move along both X and Y axis
  # offsetX and offsetY are presented in the form of number pair or as a point
  shape1MoveBy:  move;   shape1; offsetX, offsetY
  shape1MoveBy2: moveby; shape1; offsetX, offsetY
```

## Rotate

We use the `rotateby`(or `rotate`) keyword to make rotate duplications, you need to present the target shape to be rotated and degrees you want to rotate, and one optional rotating center, if the center is not present, then `(0, 0)` will be used.

```wtht
points:
  pRotateCenter: ...   # Center of the rotation
shapes:
  shape1: polygon; ... # parameters you give to define the shape
  # use rotateby to make a rotation, present the target, degrees and rotating center
  shape1rotate:  rotateby; shape1; degOfRotate; pRotateCenter
  # use rotate is just OK, if rotating center is not present, will use (0,0)
  shape1rotate2: rotate; shape1; degOfRotate
```

## Example

Here is an example to draw out the 6 stars we imaged in the beginning. We define the basic star, and move and rotate it to duplicate to other six.

```wtht
vars:
  # Outer circle of the 5-angle regular star
  rStar: 80
  # Calculate the radius of the inner circle
  rStar2: rStar * dcos(72) / dcos(36)
  rCenters: 4 * rStar
points:
  p0: 0,0

shapes:
  # The reference circles
  cStar:  circ; p0; rStar;
  cStar2: circ; p0; rStar2;
  cCenters: circ; p0; rCenters;

pointSeries:
  # Outer vertices of the star
  psStar:  cStar; 270 : -72 : 270 - 4 * 72
  # Inner vertices of the star
  psStar2: cStar2; 270 - 36 : -72 : 270 - 4 * 72 -36
  # centers of the stars, on the large circle
  psCenters: cCenters; 0: 360 / 6 : 360

shapes2:
  # Make the star, it is an polygon actually
  star: plg; psStar[1]; psStar2[1]; psStar[2]; psStar2[2];
        psStar[3]; psStar2[3]; psStar[4]; psStar2[4];
        psStar[5]; psStar2[5];
  # Move along x-axis
  starMoveX: movex; star; rCenters / 2
  # Move along y-axis
  starMoveY: movey; star; -rCenters * 1.2
  # moveby: both X and Y axis
  # Please know that, the offset here is provided as a point, but it should
  # be offsets, it is OK and not ambiguous because the reference center point is (0,0)
  # the location equals offsets, please know the logical difference in your own case
  starMove1: move; star; psCenters[1]
  starMove2: move; star; psCenters[2]
  starMove3: move; star; psCenters[3]
  starMove4: move; star; psCenters[4]
  starMove5: move; star; psCenters[5]
  starMove6: move; star; psCenters[6]
  # rotate: after translate, we rotate the stars at their own center
  starRot1: rotate; starMove1; -90 + 60 * 0;  psCenters[1];
  starRot2: rotate; starMove2; -90 + 60 * 1;  psCenters[2];
  starRot3: rotate; starMove3; -90 + 60 * 2;  psCenters[3];
  starRot4: rotate; starMove4; -90 + 60 * 3;  psCenters[4];
  starRot5: rotate; starMove5; -90 + 60 * 4;  psCenters[5];
  starRot6: rotate; starMove6; -90 + 60 * 5;  psCenters[6];

jobs:
  moveby:
    - cCenters; fill,none; stroke,1, grey; dash; 3,3;
    - star; fill, grey, 0.3; stroke, black, 1; dash; 2,2;
    - starMove1; fill, red, 0.4; stroke, black, 1;
    - starMove2; fill, red, 0.4; stroke, black, 1;
    - starMove3; fill, red, 0.4; stroke, black, 1;
    - starMove4; fill, red, 0.4; stroke, black, 1;
    - starMove5; fill, red, 0.4; stroke, black, 1;
    - starMove6; fill, red, 0.4; stroke, black, 1;
    - psCenters; 3; black;
    - circ; p0; 3 ||| fill, magenta;
  rotate:
    - cCenters; fill,none; stroke,1, grey; dash; 3,3;
    - star; fill, grey, 0.3; stroke, black, 1; dash; 2,2;
    - starRot1; fill, red, 0.4; stroke, black, 1;
    - starRot2; fill, red, 0.4; stroke, black, 1;
    - starRot3; fill, red, 0.4; stroke, black, 1;
    - starRot4; fill, red, 0.4; stroke, black, 1;
    - starRot5; fill, red, 0.4; stroke, black, 1;
    - starRot6; fill, red, 0.4; stroke, black, 1;
    - psCenters; 3; black;
    - circ; p0; 3 ||| fill, magenta;
    - line; psCenters[1]; p0; ||| stroke,0.5, cyan;
    - line; psCenters[2]; p0; ||| stroke,0.5, cyan;
    - line; psCenters[3]; p0; ||| stroke,0.5, cyan;
    - line; psCenters[4]; p0; ||| stroke,0.5, cyan;
    - line; psCenters[5]; p0; ||| stroke,0.5, cyan;
    - line; psCenters[6]; p0; ||| stroke,0.5, cyan;

draw:
  - cCenters; fill,none; stroke,1, grey; dash; 3,3;
  - star; fill, grey, 0.3; stroke, black, 1; dash; 2,2;
  - starMoveX; fill, red, 0.3; stroke, black,1;
  - starMoveY; fill, green, 0.3; stroke, black,1;
```

[wtht] # Example of the 6-star
name: SixStarOnCircle

asSource: yes
vars:
  # Outer circle of the 5-angle regular star
  rStar: 80
  # Calculate the radius of the inner circle
  rStar2: rStar * dcos(72) / dcos(36)
  rCenters: 4 * rStar
points:
  p0: 0,0

shapes:
  # The reference circles
  cStar:  circ; p0; rStar;
  cStar2: circ; p0; rStar2;
  cCenters: circ; p0; rCenters;

pointSeries:
  # Outer vertices of the star
  psStar:  cStar; 270 : -72 : 270 - 4 * 72
  # Inner vertices of the star
  psStar2: cStar2; 270 - 36 : -72 : 270 - 4 * 72 -36
  # centers of the stars, on the large circle
  psCenters: cCenters; 0: 360 / 6 : 360

shapes2:
  # Make the star, it is an polygon actually
  star: plg; psStar[1]; psStar2[1]; psStar[2]; psStar2[2];
        psStar[3]; psStar2[3]; psStar[4]; psStar2[4];
        psStar[5]; psStar2[5];
  # Move along x-axis
  starMoveX: movex; star; rCenters / 2
  # Move along y-axis
  starMoveY: movey; star; -rCenters * 1.2
  # moveby: both X and Y axis
  # Please know that, the offset here is provided as a point, but it should
  # be offsets, it is OK and not ambiguous because the reference center point is (0,0)
  # the location equals offsets, please know the logical difference in your own case
  starMove1: move; star; psCenters[1]
  starMove2: move; star; psCenters[2]
  starMove3: move; star; psCenters[3]
  starMove4: move; star; psCenters[4]
  starMove5: move; star; psCenters[5]
  starMove6: move; star; psCenters[6]
  # rotate: after translate, we rotate the stars at their own center
  starRot1: rotate; starMove1; -90 + 60 * 0;  psCenters[1];
  starRot2: rotate; starMove2; -90 + 60 * 1;  psCenters[2];
  starRot3: rotate; starMove3; -90 + 60 * 2;  psCenters[3];
  starRot4: rotate; starMove4; -90 + 60 * 3;  psCenters[4];
  starRot5: rotate; starMove5; -90 + 60 * 4;  psCenters[5];
  starRot6: rotate; starMove6; -90 + 60 * 5;  psCenters[6];

jobs:
  moveby:
    - cCenters; fill,none; stroke,1, grey; dash; 3,3;
    - star; fill, grey, 0.3; stroke, black, 1; dash; 2,2;
    - starMove1; fill, red, 0.4; stroke, black, 1;
    - starMove2; fill, red, 0.4; stroke, black, 1;
    - starMove3; fill, red, 0.4; stroke, black, 1;
    - starMove4; fill, red, 0.4; stroke, black, 1;
    - starMove5; fill, red, 0.4; stroke, black, 1;
    - starMove6; fill, red, 0.4; stroke, black, 1;
    - psCenters; 3; black;
    - circ; p0; 3 ||| fill, magenta;
  rotate:
    - cCenters; fill,none; stroke,1, grey; dash; 3,3;
    - star; fill, grey, 0.3; stroke, black, 1; dash; 2,2;
    - starRot1; fill, red, 0.4; stroke, black, 1;
    - starRot2; fill, red, 0.4; stroke, black, 1;
    - starRot3; fill, red, 0.4; stroke, black, 1;
    - starRot4; fill, red, 0.4; stroke, black, 1;
    - starRot5; fill, red, 0.4; stroke, black, 1;
    - starRot6; fill, red, 0.4; stroke, black, 1;
    - psCenters; 3; black;
    - circ; p0; 3 ||| fill, magenta;
    - line; psCenters[1]; p0; ||| stroke,0.5, cyan;
    - line; psCenters[2]; p0; ||| stroke,0.5, cyan;
    - line; psCenters[3]; p0; ||| stroke,0.5, cyan;
    - line; psCenters[4]; p0; ||| stroke,0.5, cyan;
    - line; psCenters[5]; p0; ||| stroke,0.5, cyan;
    - line; psCenters[6]; p0; ||| stroke,0.5, cyan;

draw:
  - cCenters; fill,none; stroke,1, grey; dash; 3,3;
  - star; fill, grey, 0.3; stroke, black, 1; dash; 2,2;
  - starMoveX; fill, red, 0.3; stroke, black,1;
  - starMoveY; fill, green, 0.3; stroke, black,1;

[wtht]

After drawn, it will be like this:

1. the basic star, and two duplications by `movex` and `movey`.


[wtht] # Example of the 6-star
use: SixStarOnCircle
noTitle: yes

figure: draw
[wtht]


2. The stars after translate by both X and Y axis (`moveby`).


[wtht] # Example of the 6-star
use: SixStarOnCircle
noTitle: yes

figure: moveby

[wtht]



3. Rotate the stars to make their `top-angle` pointing to center of the big circle.


[wtht] # Example of the 6-star
use: SixStarOnCircle
noTitle: yes

figure: rotate

[wtht]


[i] You might wonder if I can enlarge the star or make it smaller? Please don't worry, in this short article we only discuss the operation of translate and rotate to **duplicate** shapes, we have other ways to scale shapes or drawing combinations in other modules/sections.

[i] For how to draw the regular five-angle star, following graph will illustrate the secrets.


[wtht] # how to draw a 5-angle star

noTitle: yes

vars:
  # Radius of the outer circle
  rStar: 300
  # Calculate inner radius
  rStar2: rStar * dcos(72) / dcos(36)
  rCenters: 4 * rStar
points:
  p0: 0,0

shapes:
  # reference circles
  cStar:  circ; p0; rStar;
  cStar2: circ; p0; rStar2;

pointSeries:
  # outer vertices
  psStar:  cStar; 270 : -72 : 270 - 4 * 72
  # inner vertices
  psStar2: cStar2; 270 - 36 : -72 : 270 - 4 * 72 -36

shapes2:
  star: plg; psStar[1]; psStar2[1]; psStar[2]; psStar2[2];
        psStar[3]; psStar2[3]; psStar[4]; psStar2[4];
        psStar[5]; psStar2[5];
  lineR1: line; p0; psStar[1];
  lineR2: line; p0; psStar[2];
  lineR1i: line; p0; psStar2[1];
  lsV: line; p0; 270; rStar2 * dcos(36)
  lsH: line; psStar2[1]; 0; rStar2 * dsin(36) * 2

draw:
  - cStar;  fill, none; stroke, 1, grey;
  - cStar2; fill, none; stroke, 1, grey;
  - star; fill, red, 0.4; stroke, 3, black;
  - lineR1; stroke, 1, black;
  - lineR2; stroke, 4, magenta;
  - lineR1i; stroke, 4, magenta;
  - lsV; stroke, 4, cyan;
  - lsH; stroke, 1, yellow; dash; 3,3
  - label; lsV.m; anchor; ml; w; 160; h; 80; ||| $ H = R_{star} \cdot \cos 72 \degree \\ = R_{star2} \cdot \cos 36\degree$
  - label; lineR2.m; anchor; ml; w; 100; h; 80; offset; -40, -16 ||| $ R_{star}$
  - label; lineR1i.m; anchor; ml; w; 100; h; 80; align;left; offset; -16, -28 ||| $ R_{star2}$
  - angleMark;  psStar[1]; p0; psStar2[1];  arrow-solid; black;size;60;textSize; 12; anchor;tl;labelpos;0.6;lh;18;lw;20;showDeg;lineWidth; 2; fill; yellow;fillopacity; 0.6;
  - angleMark;  psStar[1]; p0; psStar[2];  arrow-solid; black;size;40; textSize; 12; anchor;tl;labelpos;0.6;lh;18;lw;20;showDeg;lineWidth; 2; fill; yellow;fillopacity; 0.6;
  - label; psStar2[1]; anchor; br; w; 200; h; 120; align;right; ||| $ R_{star2} = \frac{R_{star} \cdot \cos 72 \degree}{\cos 36\degree}  $

[wtht]

