# Basics

## Coordinates

We use the classical **Cartesian Coordinate System**, but the difference is that the positive direction of Y-axis is going down, rather not up. This is the general case in computer graphics, since pages and publications are normally scrolling down, having the top as a starting point, while the bottom/end is endless.

[wtht]
name: basicCoordinates
# Example WT-HUATU design file
# --- Vars ------
noTitle: yes

markers:
  arr1: arrow; size; 10

# --- Points ------
points:
  pO: (0, 0)
  pPxy: 100, 100

# --- Shapes ------
shapes:
  lnX: line; pO; 0
  lnY: line; pO; 90
  lsX: line; lnX; -20; 200;
  lsY: line; lnY; -20; 200;
  pXY: circ; pPxy; 3

# --- Draw --------
draw:
  - lsX; fill, none; stroke; 1, cc; marker; arr1
  - lsY; fill, none; stroke; 1, cc; marker; arr1
  - pXY; fill, cc;
  - line; pPxy; pPxy.x, 0; ||| fill, none; stroke; 0.5, cc; dash; 3,3
  - line; pPxy; 0, pPxy.y; ||| fill, none; stroke; 0.5, cc; dash; 3 3
  - circ; pO; 3; ||| fill, cc;
  - label; at; pPxy.x, 0; w; 25; h; 22; anchor; bm ||| $x_0$
  - label; at; 0, pPxy.y; w; 25; h; 22; anchor; mr ||| $y_0$
  - label; at; lsX.e; w; 25; h; 22; anchor; ml ||| $X$
  - label; at; lsY.e; w; 25; h; 22; anchor; tm ||| $Y$
  - label; at; pPxy; w; 80; h; 22; anchor; tl;left ||| $(x_0, y_0)$
  - label; at; pO; w; 25; h; 22; anchor; br; ||| $O$
[wtht]

## Variables

We can define some variables, to save the key parameters of our design, and to easily derive new shapes based on existing ones. At the same time, geometry relation would be kept. We can refer to existing defined variables when defining new variables, and using mathematical functions. e.g. in following example, var `a` and `k` are explicit pure numbers, while `b` is the result of `a` multiplied by `k`, and `c` is the result of `b` multiplied by square root of `k`, i.e. $b = a \time k$, $c = b \times \sqrt{k} = a \times k \times \sqrt{k}$.

```wtht
vars:
  a: 5
  k: 1.2
  b: a * k
  c: b * sqrt(k)
```

## Angles

In all the places where some **angle** is specified, we use the unit of degrees ($\degree$). Maybe in some aspect, radians are more mathematical, but calculating of multiple of $\pi$ is not very easy and intuitive, so we prefer to use degrees.

The angle starts (0$\degree$) at the direction of positive direction of x-axis, and increase as going **clockwise**. The range of angle is normally in $[0\degree, 360\degree)$($360\degree$ is identical as $0\degree$), but in real designs, we can use negative degrees, and degrees over $360\degree$, when using these values, the actually used degree will be converted(rounded) into the range of $[0\degree, 360\degree)$.

In following example, we drew some rays starting from the origin with different angles, by giving different angle values(degrees).

[wtht]
name: angleUsage
# Example WT-HUATU design file
# --- Vars ------
noTitle: yes

vars:
  lRay: 240

markers:
  arr1: arrow; size; 10

# --- Points ------
points:
  pO: (0, 0)
  pPxy: 100, 100
  pL0: lRay * dcos(0), lRay * dsin(0)
  pL1: lRay * dcos(33), lRay * dsin(33)
  pL2: lRay * dcos(88), lRay * dsin(88)
  pL3: lRay * dcos(167), lRay * dsin(167)
  pL4: lRay * dcos(-58), lRay * dsin(-58)
  pL5: lRay * dcos(243), lRay * dsin(243)

# --- Shapes ------
shapes:
  lsX: line; pO; pL0.x + 30, pL0.y
  ln0: line; pO; pL0
  ln1: line; pO; pL1
  ln2: line; pO; pL2
  ln3: line; pO; pL3
  ln4: line; pO; pL4
  ln5: line; pO; pL5

# --- Draw --------
draw:
  - angleMark; pL0; pO; pL1; arrow-solid;red;size; 96; showdeg; linewidth;2; anchor; tl;lw; 70; lh; 18; textColor; red;labelpos; 0.4
  - angleMark; pL0; pO; pL2; arrow-solid;blue;size; 64; showdeg; linewidth;2; anchor; tl;lw; 60; lh; 32; textColor; blue; labelpos; 0.62
  - angleMark; pL0; pO; pL3; arrow-solid;maroon;size; 32; showdeg; linewidth;2; anchor; tr;lw; 120; lh; 22;textColor; maroon; labelpos; 0.75
  - angleMark; pL0; pO; pL4; arrow-solid;magenta;size; 40; linewidth;2; anchor; bl; label; $-58\degree$; lw; 80; lh; 18; textColor; magenta; labelpos; 0.35
  - angleMark; pL0; pO; pL5; arrow-simple;indianred;size; 132; large; linewidth;2; anchor; br;showdeg; lw; 80; lh; 18; textColor; indianred; labelpos; 0.84
  - ln1; fill, none; stroke; 1, red; marker; arr1
  - ln2; fill, none; stroke; 1, blue; marker; arr1
  - ln3; fill, none; stroke; 1, maroon; marker; arr1
  - ln4; fill, none; stroke; 1, magenta; marker; arr1
  - ln5; fill, none; stroke; 1, indianred; marker; arr1  
  - lsX; fill, none; stroke; 2, cc; marker; arr1
  - circ; pO; 6; ||| fill, cc;
  - label; at; lsX.e; w; 25; h; 22; anchor; ml ||| $X$
  - label; at; pO; w; 34; h; 22; anchor; br; offset; 0, 4||| $O$
[wtht]

If you need to draw some graph using polar system, please be aware that the angle grows clockwise, rather not counter-clockwise which is normally presented in text books.


## Mathematical Functions

When defining variables, we can use some mathematical functions to calculate from numbers or existing vars, to keep geometry relations. We only adopted a limited set of math functions, which are as follows:

- **sin** : the function *sin*, unit of input is radian (rad)
- **cos** : the function *cos*, unit of input is radian (rad)
- **tan** : the function *tan*, unit of input is radian (rad)
- **asin** : the function *asin*, unit of the result is radian (rad)
- **acos** : the function *acos*, unit of the result is radian (rad)
- **atan** : the function *atan*, unit of the result is radian (rad)

- **dsin** : the function *sin*, unit of input is **degrees**($\degree$)
- **dcos** : the function *cos*, unit of input is **degrees**($\degree$)
- **dtan** : the function *tan*, unit of input is **degrees**($\degree$)
- **dasin** : the function *asin*, unit of the result is **degrees**($\degree$)
- **dacos** : the function *acos*, unit of the result is **degrees**($\degree$)
- **datan** : the function *atan*, unit of the result is **degrees**($\degree$)

- **exp** : the function of $f(x) = e^x$
- **log** : the logarithm function with base $e$, i.e. $f(x) = \lg x$ or $f(x) = \log_e x$
- **log10** : the logarithm function with base 10, i.e. $f(x) = \log_{10} x$
- **sqrt** : the function to calculate square root, i.e. $f(x) = \sqrt{x}$

Besides of these functions, we can directly use `pi` to call $\pi$ in our calculation, so be noticed that we should not use `pi` as our var name when defining variables.

Following are some examples:

```wtht
vars:
  # Define one angle
  ang1: pi / 6
  # Define its degree
  deg1: ang1 * 180 / pi
  # define two vars using sin and dcos
  k1: sin(ang1)
  k2: dcos(deg1)
  # define basic len
  len: 320
  lenW: len * k1
  lenH: len * k2
  len2: sqrt(len * len / 2)

points:
  pO: 0, 0
  pW: lenW, 0
  pH: 0, -lenH

shapes:
  c1: circ; pW; len2;
  c2: circ; pH; len2;

xpoints:
  pX1: c1; c2; e;

draw:
  # draw some triangle
  - tri; pO; pW; pH; ||| stroke, 2, cc; fill, none; linejoin; round;
  # mark one angle
  - angleMark; pO; pW;  pH; arrow-solid;cc; size; 23; showdeg; linewidth;2;
    anchor; br;lw; 70; lh; 18;
  # mark the potential perpendicular angle
  - angleMark; pW; pX1; pH; arrow-solid;red;size; 12; showperp;linewidth;2;
    fill, yellow
  # draw the new triangle sides
  - line; pW; pX1; pH; ||| stroke, 2, red; fill, none; linecap; round;
```

After dawn, it will be like this:

[wtht]
name: functionUsage
# Example WT-HUATU design file
# --- Vars ------
noTitle: yes

vars:
  # Define one angle
  ang1: pi / 6
  # Define its degree
  deg1: ang1 * 180 / pi
  # define two vars using sin and dcos
  k1: sin(ang1)
  k2: dcos(deg1)
  # define basic len
  len: 320
  lenW: len * k1
  lenH: len * k2
  len2: sqrt(len * len / 2)

points:
  pO: 0, 0
  pW: lenW, 0
  pH: 0, -lenH

shapes:
  c1: circ; pW; len2;
  c2: circ; pH; len2;

xpoints:
  pX1: c1; c2; e;

draw:
  # draw some triangle
  - tri; pO; pW; pH; ||| stroke, 2, cc; fill, none; linejoin; round;
  # mark one angle
  - angleMark; pO; pW; pH; arrow-solid;cc;size; 23; showdeg; linewidth;2; anchor; br;lw; 70; lh; 18;
  # mark the potential perpendicular angle
  - angleMark; pW; pX1; pH; arrow-solid;red;size; 12; showperp; linewidth;2; fill, yellow
  # draw the new triangle sides
  - line; pW; pX1; pH; ||| stroke, 2, red; fill, none; linecap; round;

[wtht]
