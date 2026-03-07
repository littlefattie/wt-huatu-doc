# Quick Start

Since `wt-huatu` is a programming language, what we need to do first is to "speak" with it, or "write" in it, to accomplish some design of drawings.
To instantly get feedback and the potential rendering result as per what we input in to the design, we need some editor. So before you make any move, please open our editor, and switch "preview" to ON to see the latest rendered result.

## Editor

We developed our dedicated editor based on Microsoft's `Monaco Editor` (here thanks to Monaco Editor and Microsoft), you can design your own huatu input, save it locally or on the server (you need to register some account). The editor is hosted at: [https://wt-huaut.org/editor/](https://wt-huaut.org/editor/)

## Design input/source code

Considering that design drawings with `wt-huatu` is equivalent to some behavior of programming, we can treat the design input as source code. And we could also call this source code as `design file`, or `design doc`.

## Design file

### File layout

We adopt the `yaml` file format (if you are a programmer, and have some interest about this format, please go to [https://yaml.org](https://yaml.org)) as the format of our design files, but only use a subtracted set. The basic layout of our design file could be presented as this:

```wtht
# This is some comment

# key:value pair at root
TAG0: content under name TAG0

# A list named as TAG1
TAG1:
  - item 1
  - item 2
  - ...

# complex/nesting defines under the name TAG2
TAG2:
  sub_tag:
    sub_sub_tag: content of the tag
    sub_sub_tag_of_list:
      - item 1
      - item 2
      - ...
  sub_tag_list:
    - item 1
    - item 2
    - item 3
  sub_tag_complex:
    stc_tag1: description of this
    stc_tag2_more_sub:
      stc_tag_sub1: more description
      stc_tag_sub2:
        - list item 1
        - list item 2
        - ...
      stc_tag_sub3:
        stc_tag_sub_sub:
          ...   # whatever more complex content
```
We only use two main syntaxes, one is `key:value` pair, and the other is list, `- list item`. And we also use the nesting structure by put `key:value` pair or list as the `value` part assigning to some `key`. Please note that when you are defining some list, at least one space must be added after the hyphen sign (`-`), if not, then the text would might be not recognized as list item, or causing parsing error. i.e. we need to write `- list item 1`, rather not `-list item 1`。

Obviously previous pseudo code is not what our code is going to be like, it only tells the structure. We need to replace the keys (`TAG1` for instance) to our dedicated ones.

Currently we designed two types of design file, one is normal **Huatu**, another is **Huatu Component**. They share almost the same parameters, but slightly a little different.

### Normal Huatu

Some full example of Huatu is like this: 

```wtht
# The example of full-elements design

# You can give some optional title for your huatu
title: optional title, if you would like to set

# And any description if you like
desc: Example Design file

# You can also specify the size of this huatu, please know that it is optional
# And it has no effect when previewing
size: 400, 600

# you can specify some viewBox to crop the final rendered huaut to specific region you want
# And please know that if you didn't specify viewbox in all the "jobs", this viewbox setting
# will be copied to all the jobs, unless some of them has their own viewbox setting
#viewBox: -20, -100; 100; 100

# vars
vars:
  a: 60
  b: 80

# points definition
points:
  pC1: (0, 0)
  pC2: (b, 0)

# shapes definition
shapes:
  c1: circle; pC1; a
  c2: circle; pC2; b
  line1: line; (0,0); 30;
  lineseg1: line; line1; -20; 100
  c2moved1: movex; c1; 50

# some 'calculated' points from defined shapes
cpoints:
  pcC1: c1; 45
  pcC2: c2; 90

# come intersection point 
xpoints:
  pxC1C2_N: c1; c2; n
  pxC1C2_S: c1; c2; s

# Point series
pointSeries:
  psPC: pcC1; pcC2;
  psL1: lineseg1; 0: 0.1: 1
  # With the 'x' mark, you include all the intersection points into one pointSeries
  psC1xC2: c1; x; c2;

# shape series
shapeSeries:
  ss1: circle; psL1; 2: 0.3: 120; cross

# after the "first" round of points/shapes/cpoints/xpoints/pointseries/shapeseries,
# you can make other groups of three, say points2/..., points3/..., points4/...
points2:
shapes2:
  lineBetweenXpoints: line; pxC1C2_N; pxC1C2_S
cpoints2:
xpoints2:
pointseries2:
shapeseries2:

points3:
shapes3:
cpoints3:
xpoints3:
pointseries3:
shapeseries3:

points4:
shapes4:
cpoints4:
xpoints4:
pointseries4:
shapeseries4:

# You can import other componenets or make your own component
components:
  comp1:
    vars:
      a:3
    draw:
      - circ; (0,0); a ||| stroke, 1, aqua
  comp2: https://wt-huatu.org/component/some-component-defined.yml

# after you imported/designed any components, you can place them into your huatu
placements:
  plcComp1: comp1; 100, 50; 
  plcComp2: comp2; 200, 100;

# If basic shape defines are out of needs, you could design complex path by defining
# its segments and adding 'close' keyword where close-path action required
paths:
  complexPath1:
    - line; (0, 0); (50, 50)
    - arc; (50, 50); 30; 40; 0; cw; large; (60, 72)
    - line; (60,72); (-10, 90)
    - close

# If you need mark something, e.g. arrow head for some line, you can define markers
markers:
  arrow1: arrow; len; 8; color; cc;
  arrow2: arrow-double; len; 12; color; cc; k;0.4
  arrow3: arrow-simple; len;10; cc; smooth

# Fill fill some shape with pattern, you can define patterns to be used later
patterns:
  ptn1: type;lines; density, 12; color; yellow; linewidth, 4; deg; -45;
  ptnGrid1:
    type: grid
    size: 4
    color: maroon
    linewidth: 1
    sparsek: 0.3

# clip-path, when you want to clip out some drawings, you can define clips here
clips:
  clip1:
    - rect; -50, -50; 250, 50

# you can define styles here to reuse them in later drawings
styles:
  sty1Fillred: fill, red, 0.6;
  sty2Stroke2black: stroke,2, black;
  sty3complicated: fill, pink, 0.2; stroke, 1, aqua; pattern; ptnGrid1

# you can also specify pre-defined style for labels
labelStyles:
  lblSty1: 
    color: red
    font:  times
    align: center;
    size: 8

# you can make up a symbol to reuse it later, e.g make arrays of symbol
symbols:
  triangle1: 
    - triangle; 0, 2; 0, -2; 4, 0 ||| fill, green;
    - circ; 2, 0; 0.5 ||| fill, white;

# After symbols defined, you can create array form them
arrays:
  array1: triangle1; polar; center; c2.c; count; 8; 
          startdeg; 0; deltaDeg; 30; r; c2.r ;scale;3.5; aligncenter

# you can define blocks to reuse them later, block is some kind of "drawing group"
# you can either add style or not when adding drawing elements, because you can add
# style later when drawing the block it self
blocks:
  blk1:
    - c1; 
    - c2;

# And you can hierarchy your design with layers, layer is almost same with blocks, except that
# - block can includde another block, while layer could not include layer, it can include block
# - you cannot apply rotating or translating to layers, it is a logically container, and not some
#   componenet you can re-use
layers:
  layerBackground:
    - rect; -100, -100; 220, 150 ||| stroke, 1, aqua; fill, #eee

# You can pick only what you wanted to be included in the final output
# e.g. you can add some reference lines when designing, but don't include them in final output
# The default drawing job, you just add what you want under this item
draw:
  - c1; stroke; 1, red; fill, red, 0.2; opaque; 0.3
  - c2; stroke; 0.5, black; fill, none;
  - lineBetweenXpoints; stroke, 2 blue; marker; arrow1
  - circ; psPC(1); 3; ||| fill, yellow;

# And you can add as many "jobs" as per your plan/design
# It could be some alternative outputs, or auxiliary/test jobs
# Please know that, you cannot make a "draw" job, is can only be presented under root
jobs:
  showPathSeries:
    - ss1; fill, red;
  placementsTest:
    - plcComp1; fill, pink
  complexPathTest:
    - complexPath1; stroke, 1, aqua; fill, pink, 0.3
  drawWithPattern:
    - c1; pattern; ptnGrid1; stroke, 2, red;
    - c2; pattern; ptn1; stroke, 1, black; clip; clip1
  testStyles:
    - c1; sty2Stroke2black
    - c2; sty3complicated
    - c2moved1; sty1Fillred
  arrayTest:
    - layerBackground
    - c2; stroke, 1, pink; fill, none;
    - array1
    - plcComp1;
  labelTest:
    - plcComp1;
    - label; lblSty1; w; 90; h;20; at; 25,0;anchor; middleleft;background; lightgrey;left ||| This some label style test
    - circ; 25,0; 2 ||| fill, blue
  blockTest:
    - blk1; stroke, 2, magenta; fill, none;
```

### Huatu Component

Huatu Component is actually a tailored Normal Huatu, it could have almost all the structures and elements except:

- **jobs**, component cannot have jobs, all the to-be-drawn elements must be presented under the `draw` tag under root
- **clips**, component cannot have clip paths
- **components** and **placements**, component cannot include another component, i.e. nesting/cascading is not allowed with component
- **symbols** and **arrays**, component cannot have symbol defined and symbol arrays neither
- **blocks**, component cannot have blocks
- **layers**, component cannot have layers

And some two features are added to component:

1. **Input Var**, by adding `inputVars`, you make some configuring parameters of building the component, e.g. you can make a component of rectangle box with width and height changeable by defining its width and height as **input vars**, then when placing the component, you can give new (and different) values to those vars.
2. **Port**, you can define some ports to a component, then when multiple components placed, you can add wires to connect ports between each component. One example would be that, you can define a component as electronic resistor with two ports (terminals), then you place multiple of them, and then you connect the terminals to build/draw a resistor network.

When using Huatu Component, we can treat it as a black box, with only the **input vars** as parameters and **ports** as interfaces.

Following would be an full example of Huatu Component:

```wtht
# You can explicitly define a "type" tag to indicate that this design file is Component
type: component

# Define Input Vars
# All the values given here are "default" ones, if certain values are not given 
# when placing the component, the var will pick this default one as value
inputVars:
  lineWidth: 3
  color: red
  fill: lightgrey
  border: blue
  borderWidth: 2
  width: 300
  height: 200
  content: block text
  corner: 15

# Define Vars
vars:
  bw: $width        # Here when referring input var, use $inputVarName to call it
  bh: $height       # The input var referring use a method of *full replacing**
  lportW: bw/10
  lportH: bh/10
  lportDiag: sqrt(lportW * lportW + lportH * lportH) * 1.1

  cornerShrink: $corner *(1 - sqrt(0.5))

# Define points
points:
  pCenter: (0, 0)
  pne: pCenter.x + bw/2 - cornerShrink, pCenter.y - bh/2 + cornerShrink
  pse: pCenter.x + bw/2 - cornerShrink, pCenter.y + bh/2 - cornerShrink
  pnw: pCenter.x - bw/2 + cornerShrink, pCenter.y - bh/2 + cornerShrink
  psw: pCenter.x - bw/2 + cornerShrink, pCenter.y + bh/2 - cornerShrink
  
# Define shapes
shapes:
  container: rect; bycenter; pCenter; bw; bh;
  lne: line; pCenter; pne
  lse: line; pCenter; pse
  lnw: line; pCenter; pnw
  lsw: line; pCenter; psw

# All the elements that need to be drawn must be listed here
draw:
  - container; fill; $fill; stroke; $border, $borderWidth; radius; $corner
  - label; at; pCenter; anchor; center; w; bh; h; bh ||| $content

# we define 8 ports n/e/s/w/ne/se/nw/sw
ports:
  - w; container.w; 180; lportW
  - e; container.e;   0; lportW
  - n; container.n; 270; lportH
  - s; container.s;  90; lportH
  - ne; pne; lne.deg; lportDiag
  - nw; pnw; lnw.deg; lportDiag
  - se; pse; lse.deg; lportDiag
  - sw; psw; lsw.deg; lportDiag
```

### Minimum Design

If complex design is not what you want, you can make things a lot simpler. For example, by only just two lines, you get some Huatu of drawing one line segment here.

```wtht
draw:
  - line; 0, 0; 100, 100; ||| stroke, 1, cc;
```
