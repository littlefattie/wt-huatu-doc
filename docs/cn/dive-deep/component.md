# 组件

## 概念

为了复用我们的设计，我们可以将一些可能重复性使用的画图元素组合起来，成为一个组件，这样以来，当我们在画图中需要画很多这些画图元素时，我们可以通过**放置**若干个组件来实现。

一个很实用的场景是当我们需要绘制一幅图用来解释电路工作原理时，我们可能需要放置许多个电阻，电容，三极管，门电路芯片等，我们可以将这些电子元器件都定义成组件，然后按照需要把各个组件放置到原理图中即可。

比如，通过定义各个组件，我们可以绘制如下的电路原理图:

[wtht] # The demo for EE chart of 5V LDO
noTitle: yes

components:
  shtPort:
    type: component
    # --- Input Vars ------
    inputVars:
      labelText: PORT_NAME
      height: 18
      fillColor: yellow
      width: 150
      align: left
      lineWidth: 4

    # --- Vars ------
    vars:
      sw: $lineWidth
      tagW: $width
      tagH: $height
      openDeg: 46
      lenToCut: tagH/2 / dtan(openDeg/2)

    # --- Points ------
    points:
      p0: (0, 0)
      p1: p0.x, p0.y - tagH
      p2: p0.x, p0.y + tagH
      p3: p0.x + tagW - lenToCut, p2.y
      p4: p0.x + tagW, p0.y
      p5: p3.x, p1.y
      p6: p4.x + 4 * sw, p4.y
      pLabel: p0.x + (tagW - lenToCut) / 2 + 5, p0.y

    # --- Shapes ------
    shapes:
      tag: plg; p1; p2; p3; p4; p5;
      bar: line; p4; p6

    # --- Draw --------
    draw:
      - bar; fill,none; stroke, cc, sw; linecap; round;
      - tag; fill, $fillColor; stroke, cc, sw*0.75;
      - label; at; pLabel; anchor; center; w; tagW - lenToCut - 4  * 2 ; h; tagH; align; $align; bold; ||| $labelText

    ports:
      - p; p6; 0; 0
  power:
    # --- Input Vars ------
    inputVars:
      lineWidth: 4
      tagWidth: 40
      color: maroon
      tag: VCC

    # --- Vars ------
    vars:
      sw: $lineWidth
      wBarH: $tagWidth
      hBarV: sw * 5

    # --- Points ------
    points:
      p0: (0, 0)

    # --- Shapes ------
    shapes:
      barH: line; p0.x - wBarH/2, p0.y - hBarV ; p0.x + wBarH/2, p0.y - hBarV;
      barV: line; p0; p0.x, p0.y - hBarV

    # --- Draw --------
    draw:
      - barH; stroke, sw, $color; linecap; round
      - barV; stroke, sw, $color; linecap; round
      - label; at;p0.x, p0.y - hBarV - 2; anchor; bm; w; wBarH * 0.96; h; 20; font; Arial; ||| $tag

    ports:
      - p; p0; 90; 0
  resLine:
    # --- Input Vars ------
    inputVars:
      lineWidth: 4
      color: cc
      dsn: R?
      value: 1k

    # --- Vars ------
    vars:
      sw: $lineWidth
      wAll: sw * 20
      wBody: sw * 12
      hBody: sw * 4
      wBodyLine: wBody / 12
      xStrait: wBody / 2 + (wAll - wBody) / 4

    # --- Points ------
    points:
      p0: (0, 0)
      p1: -wAll / 2, 0
      p2:  wAll / 2, 0
      pB1: -wBody/2, 0
      pB2:  wBody/2, 0
      pBm1: -wBodyLine * 5, -hBody/2
      pBm2: -wBodyLine * 3,  hBody/2
      pBm3: -wBodyLine * 1, -hBody/2
      pBm4:  wBodyLine * 1,  hBody/2
      pBm5:  wBodyLine * 3, -hBody/2
      pBm6:  wBodyLine * 5,  hBody/2


    # --- Shapes ------
    shapes:
      handL: line; p1; -xStrait, 0;
      handR: line; p2;  xStrait, 0;
      body: rect; bycenter; p0; wBody;hBody;
      lines: line; -xStrait, 0; pB1; pBm1; pBm2; pBm3; pBm4; pBm5; pBm6; pB2; xStrait, 0;

    paths:
      bars:
        - handL
        - handR

    # --- Draw --------
    draw:
      - bars; stroke, $color, sw; linecap; round; linejoin; round; fill, none;
      - label; at;body.n; offset; 0, -6; anchor; bm; w; sw * 8; h; 24;  ||| $dsn
      - label; at;body.s; offset; 0,  6; anchor; tm; w; wAll * 0.96; h; 24;   ||| $value
      - lines; stroke, $color, sw; fill,none; linejoin; round;

    ports:
      - 1; p1; -180; 0
      - 2; p2;    0; 0

  resRect:
    # --- Input Vars ------
    inputVars:
      lineWidth: 4
      color: cc
      bodyFill: none
      dsn: R?
      value: 1k

    # --- Vars ------
    vars:
      sw: $lineWidth
      wAll: sw * 20
      wBody: sw * 10
      hBody: sw * 4

    # --- Points ------
    points:
      p0: (0, 0)
      p1: -wAll / 2, 0
      p2:  wAll / 2, 0

    # --- Shapes ------
    shapes:
      handL: line; p1; -wBody/2, 0;
      handR: line; p2;  wBody/2, 0;
      body: rect; bycenter; p0; wBody;hBody;

    paths:
      bars:
        - handL
        - handR

    # --- Draw --------
    draw:
      - bars; stroke, $color, sw; linecap; round; linejoin; round; fill, none;
      - body; stroke, $color, sw; linecap; round; linejoin; round; fill, $bodyFill;
      - label; at;body.n; offset; 0, -6; anchor; bm; w; sw * 8; h; 24;  ||| $dsn
      - label; at;body.s; offset; 0,  6; anchor; tm; w; wAll * 0.96; h; 24;   ||| $value

    ports:
      - 1; p1; -180; 0
      - 2; p2;    0; 0
  ground:
    # --- Input Vars ------
    inputVars:
      lineWidth: 4
      color: cc
      tag: GND

    # --- Vars ------
    vars:
      sw: $lineWidth
      wBar1: sw * 11
      wBar2: sw * 8
      wBar3: sw * 5
      hDelta: sw * 1.6
      hBarV: sw * 4

    # --- Points ------
    points:
      p0: (0, 0)
      p1: p0.x, p0.y + hBarV
      p2: p1.x, p1.y + hDelta
      p3: p2.x, p2.y + hDelta
      pLabel: p3.x, p3.y + sw

    # --- Shapes ------
    shapes:
      bar1: line; p1.x - wBar1 / 2, p1.y; p1.x + wBar1 / 2, p1.y;
      bar2: line; p2.x - wBar2 / 2, p2.y; p2.x + wBar2 / 2, p2.y;
      bar3: line; p3.x - wBar3 / 2, p3.y; p3.x + wBar3 / 2, p3.y;
      barV: line; p0 ; p0.x, p0.y + hBarV;

    # --- Draw --------
    draw:
      - barV; stroke, sw, $color; linecap; round
      - bar1; stroke, sw, $color; linecap; round
      - bar2; stroke, sw, $color; linecap; round
      - bar3; stroke, sw, $color; linecap; round
      - label; at;pLabel; anchor; tm; w; wBar1 * 2; h; 20;  ||| $tag

    ports:
      - p; p0; -90; 0
  bjtNPN:
    inputVars:
      lineWidth: 4
      color: cc
      dsn: Q?              # Designator
      partNo: 2N2222
      circleFill: yellow
      circleStrokeWidth: 4

    vars:
      swArm: $lineWidth
      degC: 42
      degE: degC
      bBarOffsetX: 8 * swArm
      vwBBar: 2.2 * swArm
      vlBBar: 11.8 * swArm
      dCEonBar: 0.32 * vlBBar
      lenArmCE: 5 * swArm
      lenArmB: lenArmCE * 2
      triDeg: 43
      triLen: 5.5 * swArm
      rCirc: vlBBar * 0.95

    points:
      pBarB: -bBarOffsetX, 0
      pCircCenter: -bBarOffsetX * 0.44, 0
      pBarC: pBarB.x, pBarB.y - dCEonBar / 2
      pBarE: pBarB.x, pBarB.y + dCEonBar / 2
      pTurnC: 0, -bBarOffsetX * dtan(degC) + pBarC.y
      pTurnE: 0,  bBarOffsetX * dtan(degE) + pBarE.y
      pPortB: pBarB.x - lenArmB, pBarB.y
      pPortC: pTurnC.x, pTurnC.y - lenArmCE
      pPortE: pTurnE.x, pTurnE.y + lenArmCE

    shapes:
      cBounding: circ; pCircCenter;rCirc
      bBar: rect; bycenter; pBarB; vwBBar; vlBBar
      armB: line; pBarB; pPortB;
      armC: line; pBarC; pTurnC; pPortC;
      armE: line; pBarE; pTurnE; pPortE;
      lArrLow:  line; pTurnE; 180 + degE - triDeg / 2
      lArrHigh: line; pTurnE; 180 + degE + triDeg / 2
    cpoints:
      pTri1: lArrLow;  triLen / dcos(triDeg/2)
      pTri2: lArrHigh; triLen / dcos(triDeg/2)

    shapes2:
      triOnE: polygon; pTri1; pTurnE; pTri2

    draw:
      - cBounding; fill, $circleFill; stroke, $circleStrokeWidth, $color;
      - armB; stroke, swArm, $color; linejoin; round; linecap; round; fill,none;
      - armC; stroke, swArm, $color; linejoin; round; linecap; round; fill,none;
      - armE; stroke, swArm, $color; linejoin; round; linecap; round; fill,none;
      - bBar; fill, $color
      - triOnE; fill, $color;
      - label; rCirc -bBarOffsetX * 0.2, -bBarOffsetX * 0.1; w; bBarOffsetX * 2; h; 16; ml; left; ||| $dsn
      - label; pBarB.x, pTurnE.y + 3* swArm; w; bBarOffsetX * 2; h; 16; tr; right; ||| $partNo

    ports:
      - B; pPortB; 180; lenArmCE
      - C; pPortC; -90; lenArmCE
      - E; pPortE;  90; lenArmCE
  tl431:
    # --- Input Vars ------
    inputVars:
      lineWidth: 4
      color: cc
      dsn: D?
      partNo: TL431

    # --- Vars ------
    vars:
      sw: $lineWidth
      hAll: sw * 32
      hHand: sw * 12
      wBar: sw * 8
      wBarTurn: wBar * 0.92
      hBarTurn: sw * 1.88
      wTri: wBar * 0.96
      yBar: -hAll/2 + hHand

    # --- Points ------
    points:
      p0: (0, 0)
      pA: 0,  hAll / 2
      pK: 0, -hAll / 2
      pR: wBar * 0.5 * 4, p0.y
      pBarEnd1:  -wBar / 2, yBar + hBarTurn
      pBarTurn1: -wBarTurn / 2, yBar
      pBarTurn2:  wBarTurn / 2, yBar
      pBarEnd2:   wBar / 2, yBar - hBarTurn

    # --- Shapes ------
    shapes:
      handA: line; pA; 0, -yBar;
      handK: line; pK; 0,  yBar;
      handR: line; pR; wTri / 4, pR.y
      barH:  line; pBarEnd1; pBarTurn1; pBarTurn2; pBarEnd2;
      mark:  tri; -wTri/2, -yBar; wTri/2, -yBar; 0, yBar;
      box:   rect; bycenter; p0; wBar * 2.25; wBar * 2.25

    paths:
      bars:
        - handA
        - handK
        - handR
        - ...box;
        - ...barH;

    # --- Draw --------
    draw:
      - bars; stroke, $color, sw; linecap; round; linejoin; round; fill,none;
      - mark; stroke, $color, sw; linecap; round; linejoin; round; fill, $color;
      - label; at;box.w; offset; -5,0; anchor; mr; w; sw * 8; h; 24; align; right;  ||| $dsn
      - label; at;box.se; offset; -wBar * 0.7,0; anchor; tl; w; wBar * 2.2; h; 24; align;left;  ||| $partNo

    ports:
      - A; pA;  90; 0
      - K; pK; 270; 0
      - R; pR;   0; 0

  capPoly:
    inputVars:
      lineWidth: 4
      color: cc
      dsn: C?
      value: 10uF

    # --- Vars ------
    vars:
      sw: $lineWidth
      wBar: sw * 7
      dBar: sw * 3
      hAll: sw * 16

    # --- Points ------
    points:
      p0: (0, 0)
      p1: 0, -hAll / 2
      p2: 0,  hAll / 2
      pBar1: 0, -dBar / 2
      pBar2: 0,  dBar / 2
      pArcL: -wBar/2, dBar/2 * 1.3
      pArcR:  wBar/2, dBar/2 * 1.3
      pArcCtrl: 0, -dBar/2 * 0.5

    # --- Shapes ------
    shapes:
      handU: line; p1; pBar1;
      #handD: line; p2; pBar2;
      barPlus:  line; -wBar/2, -dBar/2;  wBar/2, -dBar/2;
      arcMinus: b2; pArcL; pArcCtrl; pArcR;
    shapes2:
      handD: line; p2; arcMinus.m
    paths:
      bars:
        - handU
        - handD
        - barPlus
        - arcMinus

    # --- Draw --------
    draw:
      - bars; stroke, $color, sw; linecap; round; linejoin; round; fill, none;
      - label; at;-wBar/2, 0; offset; -6,0; anchor; mr; w; sw * 10; h; 24; align; right;  ||| $dsn
      - label; at; wBar/2, 0; offset;  6,0; anchor; ml; w; sw * 14; h; 24; align;left;   ||| $value

    ports:
      - p; p1; 270; 0
      - m; p2;  90; 0


points:
  p0: 0,0
  pLP: p0.x, p0.y
  pLG: pLP.x, pLP.y + 380
  pQ1: pLP.x + 300, pLP.y
  pTL1: pQ1.x, pQ1.y + 200
  pR1: pQ1.x + 120, pTL1.y - 80
  pR2: pR1.x, pTL1.y + 80
  pV5: pR1.x + 90, pQ1.y - 10
  pCo: pV5.x, pTL1.y
  pGnd1: pQ1.x, pLG.y + 10
  pGnd2: pCo.x, pGnd1.y
  pR3: pQ1.x - 100, pQ1.y + 100

placements:
  shtPortVin: shtPort; pLP; $labelText; VIN; $width; 70;
  shtPortGnd: shtPort; pLG; $labelText; GND; $width; 70; $fillColor; silver;
  q1:  bjtNPN; pQ1; $dsn; Q1; $partNo; [[EMPTY]]; $circleFill; red; rotDeg; -90;
  tl1: tl431; pTL1; $dsn; TL1;
  power5V: power; pV5; $tag; +5V;
  r1: resRect; pR1; rotDeg; 90; $dsn; R1; $value; 33k;
  r2: resRect; pR2; rotDeg; 90; $dsn; R2; $value; 33k;
  r3: resLine; pR3; $dsn; R3; $value; 5k1;
  co: capPoly; pCo; $dsn; Co; $value; 220u;
  gnd1: ground; pGnd1;
  gnd2: ground; pGnd2;

blocks:
  sheet:
    - shtPortVin;
    - shtPortGnd;
    - q1;
    - tl1;
    - power5V;
    - r1;
    - r2
    - r3
    - co;
    - gnd1
    - gnd2
    - wire; shtPortVin.p; r3.1; net; netVin;
    - wire; q1.C; net; netVin;
    - wire; r3.2; tl1.K; net; tl1K;
    - wire; q1.B; net; tl1K;
    - wire; tl1.R; r2.1; net; tl1R;
    - wire; r1.2; net; tl1R;
    - wire; q1.E; r1.1;net; netV5;
    - wire; power5V.p; net; netV5;
    - wire; gnd2.p; shtPortGnd.p; net; netGND;
    - wire; gnd1.p; net;netGND;
    - wire; tl1.A; net; netGND;
    - wire; r2.2; net; netGND;
    - wire; co.p; net; netV5;
    - wire; co.m; net; netGND;

draw:
  - sheet

[wtht]

对于使用了组件的的画图，其设计文件(代码)大概如下：

```wtht
# 定义组件
components:
  # 现场定义的组件
  comp1:
    ...
    draw:
      - item1
      - item2
      ...
  # 引用的在线组件
  comp2: http://...someurl.yml
# 放置组件
placements:
  plc11: comp1; x11,y11; ...
  plc12: comp1; x12,y12; ...
  plc2: comp2; x2, y2; ...
# 按需要将一个个放置画出来
blocks:
  blk1:
    - plc11
    - plc2
    ...
# 绘制
draw:
  - plc12
  - plc2
  ...
jobs:
  exmaple2:
    - plc11
    - blk1; fill; red;
    ...
```

## 组件定义

参照[快速上手](../quick-start.md#组件)章节所讲述的内容，定义组件和通常设计一个画图类似，只是所绘制的内容要简单和稀少很多，同时不支持复杂的层级关系。定义组件的设计文件需要在最高层级（根级）指定类型为`component`，即添加`type: component`的键值对。或者当根级有名为`inputVars`的键名(key)时，将自动解析为组件。

组件的设计文件中不支持如下的定义，不支持意为**不解析**，即便你在设计时添加了这些内容，在解析时也会被忽略。

- **jobs**, 组件不可以定义输出任务，它将有且只有一个任务，必须都写在`draw`下面
- **clips**, 组件不可以定义蒙版/遮罩
- **components** 和 **placements**, 组件不可以再包含另一个组件，即组件不支持嵌套，因此，组件中也不可以定义组件的放置/实例
- **symbols** 和 **arrays**, 组件不支持定义及使用符号，因此也不支持符号阵列
- **blocks**, 组件不支持模块
- **layers**, 组件也不支持使用图层

当一个普通画图文件需要使用组件时，需要将现场定义的组件或者在线引用的组件都放在根级的`components`下面。

## 组件参数

为了更方便地复用已设计好的组件，我们为组件定义了输入参数，在放置组件时，我们可以给这些参数进行重新赋值，以得到不同外在表现的组件，比如大小，颜色，文本内容等。

组件参数共有三种类型，分别为**数字**，**颜色**，**文本**，需要在定义时指定（分别使用`number`,`color`,`text`关键字）。具体方式如下：

```wtht
type: component
inputVars:
  # 定义一个 颜色 型参数
  color1: color; red;
  # 定义一个 数字 型参数
  width: number; 35
  # 定义一个 文本 型参数
  someText: text; yes!good!
```
这里的类型关键字(`number`,`color`,`text`)可以忽略，此时的类型将根据具体的值自动判断，比如例子中，各个输入变量将自动被解析为相应类型：

```wtht
inputVars:
  # 值'red'是一种颜色，故此类型为 color
  color1: red;
  # 值35是一个数字，故其类型为 number
  width: 35
  # 值既不是颜色，也不是数字，那么他就是文本 text
  someText: yes!good!
```
当定义了组件参数之后，在余下的组件定义中，就可以引用这些参数，引用方式是在参数名字前面添加一个美元符号`$`, 比如下面的例子中，组件有宽，高，圆角三个数字型参数，一个填充颜色的参数，一个内部文本参数。最后只画了一个矩形和一个文本标签，分别将上面的参数按需要填进去即可。

请注意，组件参数使用的是**替换**的方式，也就是说，组件在解析之前会把各个参数的值替换成新的值，然后进行解析，没有指定新值时，将使用组件定义时给定的值（即默认值）。

```wtht
inputVars:
  # 这里给出的值是组件参数的默认值
  width: 200
  height: 100
  radius: 20
  fillColor: yellow
  text: yes!good!
draw:
  - rect; bycenter; 0,0; $width; $height ||| fill; $fillColor; stroke,2,black; radius; $radius
  - label; 0,0; anchor; center; width; $width; height; $height; ||| $text
```
现在，我们可以将上面的组件取名为`examRect`然后给定不同的参数，画两个简单的示例：

```wtht
components:
  examRect:
    inputVars:
      width: 200
      height: 100
      radius: 20
      fillColor: yellow
      text: yes!good!
    draw:
      - rect; bycenter; 0,0; $width; $height ||| fill; $fillColor; stroke,2,black; radius; $radius
      - label; 0,0; anchor; center; width; $width; height; $height; ||| $text
placements:
  rect1: examRect; 0,0; $fillColor;  red;$text; no!bad!
  rect2: examRect; 300, 0; $width; 100; $height; 120; $radius; 8

draw:
  - rect1
  - rect2
```

[wtht] # Component Round-rectangle
noTitle: yes

components:
  examRect:
    inputVars:
      width: 200
      height: 100
      radius: 20
      fillColor: yellow
      text: yes!good!
    draw:
      - rect; bycenter; 0,0; $width; $height ||| fill; $fillColor; stroke,2,black; radius; $radius;
      - label; 0,0; anchor; center; width; $width; height; $height; ||| $text
placements:
  rect1: comp; examRect; location; 0,0; $fillColor;  red;$text; no!bad!
  rect2: examRect; 300, 0; $width; 100; $height; 120; $radius; 8

draw:
  - rect1
  - rect2
  - rect1c
[wtht]

## 组件放置

组件定义好之后，实例化的过程，我们叫做**放置**，(placement), 比如上面的例子中，我们分别放置了两个实例`rect1`, `rect2`。

```wtht
placements:
  rect1: examRect; 0,0; $fillColor;  red;$text; no!bad!
  rect2: examRect; 300, 0; $width; 100; $height; 120; $radius; 8
```

放置实例时需要指定一些参数，这些参数有两类，一类是组件的种类（定义），位置等普遍性参数，一类是前面描述的[**组件参数**](#组件参数)。

**普遍性参数**有如下一些：

- `component` (或`comp`, `def`): 放置哪个组件，取值为定义组件时给定的名称，即在`components`下面列出来的名字
- `location` (或`at`): 放置组件的位置（坐标），取值为一个点，或可解析为点表达式
- `rotDeg` (或`rotate`， `rotateDeg`): 放置组件时的旋转角(单位为角度），虽然通常情况下，旋转用的不多，但还是可以指定旋转角度的，取值为一个数字，或者可解析为数字的表达式
- `mirror`: 是否翻转，取值为`x`(表示沿垂直方向镜像， 即沿Y轴旋转180$\degree$),`y`（表示沿水平方向镜像， 即沿X轴旋转180$\degree$)，或`xy`(表示沿垂直和水平方向均进行镜像)，请注意，组件中定义的文本元素（label）将不会被镜像。
- `refPoint` (或`ref`): 参考点，放置组件的位置对应组件上的坐标点，取值为`origin`或`center`, `origin`为默认值，表示组件放置的位置对应组件的坐标零点(0,0), `center`表示组件放置的位置对应于组件的几何中心。请注意，如果组件本身的几何中心就在（0，0），那么这两个取值将等价。
- `rotateLabel`: 一个开关，不需要给定值。考虑到组件中定义的文本标签如果旋转的话可能影响阅读，所以默认情况下，文本将不会被旋转，如果你需要旋转文本，请添加此开关。

[i] 像我们的例子一样，给定这些普遍性参数时，可以省略部分前面的参数名，第一个没指定参数名的值将赋给`component`, 第二个没指定参数名的值将赋给`location`, 省下的没指定参数的值，如果时`x`,`y`,`xy`中的一个，那么将自动指定给`mirror`, 如果是`origin`,或`center`,则自动指定给`refPoint`。其他参数都需要指定参数名。

```wtht
placements:
  #下面的 rect1a 和 rect1b 实际上是一样的
  rect1a: comp; examRect; location;  0,0; $fillColor; red; $text; no!bad!
  rect1b: examRect; 0,0; $fillColor; red; $text; no!bad!
```

**组件参数**只需要像上面例子中那样依次列出即可，请注意，给组件参数赋新值时组件参数的名字前面需要添加`$`符号，就像组件定义时引用参数一样。

这些参数可以以`key1;value1; key2; value2;`的方式写成一行，也可以写成如下`rect1c`一般的层级结构：
```wtht
placements:
  rect1a: comp; examRect; location;  0,0; $fillColor; red; $text; no!bad!
  rect1b: examRect; 0,0; $fillColor; red; $text; no!bad!
  rect1c:
    comp: examRect
    location: 0,0
    $fillColor: red
    $text: no!bad!
```
[i] 请注意，组件放置后，并不会自动地被画出来，放置只相当于定义了一个新的`block`, 而你需要将此`block`直接或间接地添加到某一个输出任务(job)中才会被最终画出来。

## 组件端口

回到一开始的问题，我们为了复用一些画图元素，比如电阻，用来画电路图，但是一个电路图可能需要很多个电阻，各个电阻之间需要一些线连起来，表达连接关系。基于此，我们为组件引入了**端口**的概念，这样以来，我们可以在组件中画图元素的特定位置定义一个端口，待组件放置好以后，我们引用**这个放置的这个端口**，和**那个放置的那个端口**，然后再定义一条连接这两个端口的连线，就可以把组件连起来。如果组件的位置需要调整，那我们只需要调整一下组件放置的坐标即可，连线会自己找到新的位置，并重新连接。


定义组件端口的参数有如下一些：

- `name`: 端口的名称，可以是用纯数字，或者像通常变量名一样定义，请尽量简洁
- `anchor`(或`at`): 端口的挂载点，即坐标
- `rayDeg`(或`ray`,`deg`): 端口的发射角，我们可以定义这样一个角度，端口从挂载点出发需要像特定的方向行走一段距离
- `minHoldLen`(或`holdLen`,`hold`): 保持长度，端口沿着发射角需要保持如此长的距离，然后才可以拐弯或者往其他端口或离散的点进行连接
- `attr`(或`dir`,`type`): 端口的属性，比如是输出还是输入，高阻态等，此属性主要针对电路图，**暂未启用**。

关于端口的发射角和最小保持长度的含义，可以参考下图：

[wtht] # Demo for Component Port definition
noTitle:yes
compPreview:
  scale: 4
inputVars:
  # 这里给出的值是组件参数的默认值
  width: 120
  height: 80
  radius: 20
  fillColor: yellow
  text: yes!good!
vars:
  slopeDeg: -45
shapes:
  rectMain: rect; bycenter; 0,0; $width; $height;
cpoints:
  cpPort: rectMain; east; 0.3;
  cpBott: rectMain; east; 0.6
shapes2:
  lnRay: line; cpPort; slopeDeg;
  lsRay: line; lnRay; 0; 80
  lnBottom: line; cpBott; 0;
cpoints2:
  cpDimEnd: lnRay; 80
  cpRayEnd: lsRay; 0.8
xpoints2:
  xpBott: lnRay; lnBottom;
points3:
  pPortDescTurn: cpPort.x + 12, cpBott.y + 20
  pPortDescFar: pPortDescTurn.x + 50, pPortDescTurn.y
  pPortOffset: cpPort.x + 0.5, cpPort.y + 1.2
draw:
  - rectMain; fill; $fillColor; stroke,2,black; radius; $radius
  - label; 0,0; anchor; center; width; $width; height; $height; ||| $text
  - dimmark; cpPort; cpDimEnd; label; 最小保持长度(minHoldLen); textsize; 8; rotateLabel; gap; 6; barlen; 10; dir; left;
  #- lsRay; stroke,1,red;
  - anglemark; cpPort; xpBott; cpBott; red; size; 42;offset; 4,0; fill; #ccc; textsize; 8; lw; 62; lh; 10;  label; 发射角(rayDeg); textcolor; red; anchor; bl;
  - circ; cpPort; 3; ||| fill, red, 1;
  - line; cpPort; xpBott; cpBott.x + 40, cpBott.y ||| stroke, red,0.5; fill,none; dash; 1,1
  - line; cpPort; cpRayEnd ||| stroke, 1, red; marker;end; arrow1;
  - line; pPortDescFar; pPortDescTurn; pPortOffset; ||| stroke, 0.5, blue; marker; arrow3; fill,none;
  - label; pPortDescTurn; fs; 8; h; 10; anchor;bl;w; 80; left; ||| 端口挂载点(anchor)
ports:
  - name; p; cpPort; slopeDeg; 80
[wtht]

组件端口的定义只有一种方式，即在组件定义的根级`ports`标签之下，采用列表的方式依次列出来。内容是`key1;value1;key2;value2;...`的方式。比如：

```wtht
inputVars:
  holdLen: 10
  ...
vars:
  rayDeg1: 30
  ...
points:
  pA: 5, 10
draw:
  ...
ports:
  # 端口名字可以是字母，数字，或字母数字的组合，取值亦可引用已定义的变量，或组件参数
  - name; n;   anchor; 0,0; rayDeg; rayDeg1; minHoldLen; 5
  - name; 100; anchor; pA;  rayDeg; 90;      minHoldLen; $holdLen
  # 如果觉得有些繁琐，参数的名字 name, anchor, rayDeg, minHoldLen可依次省略,请注意给定合适且可解析的值，否则将会出错
  - se; pA.x + 5, 100;  -50; 40
```

[i] 请注意，如果你省略参数的名字，给定的值需要按照`name`,`anchor`, `rayDeg`, `minHoldLen`的顺序，这不包括那些已给定参数名字的变量，比如`se; rayDeg; 33; pA.x + 2, 10; 20` 将定义 `name`为`se`, `rayDeg` 为`33`, `anchor`为`(pA.x + 33, 10)`, `minHoldLen`为`20`的端口。

## 连线

连线是将两个已定义的端口通过简单的线段连接起来，比如在本文开篇，我们将电阻的一端连接到三极管的一端。考虑到端口本身也是一个点，所以我们也可以将连线扩展为 **点$\longleftrightarrow$点**，**端口$\longleftrightarrow$端口**，**点$\longleftrightarrow$端口** 等情况。 因为连线本身已较为复杂，将在另外的文档([连线](./wire.md))中专门叙述，本文到此为止。

## 渲染逻辑

每个组件放置的渲染结果对应于画图中的一个`block`，尽管在画图中我们将每个组件放置像普通画图元素一样对待，他本质上是一个block, 因为block本身可以嵌套，且SVG语法中`<g>`也是可以嵌套的，所以这样做并没有什么问题，从逻辑上将也更直观简洁。但请注意，组件放置的渲染结果将仅包含组件本体部分，即`draw`下面定义的内容，端口部分将不包含，端口只有当对应端口有连线连接到它时才会有所体现，否则将不包含任何渲染结果。

## 要点总结

关于组件的定义和应用，如下是一些要点

- 组件本身也是一个画图，只不过它能包含和接受的画图元素较少
- 组件在画图中可以现场定义，也可以通过URL在线引用资源
- 组件可以接受输入参数来对组件的放置实例进行个性化
- 组件可以定义端口，用来在组件之间快速连线
- 组件被放置到画图中时，会创建一个此组件的实例，相当于定义了一个画图元素
- 组件的放置并不表示它会出现在任何层级的画图中，我们需要显式地在`block/layer/job`等层级中来添加
- 组件在最终渲染生成图片时，使用的是`block`，添加一个组件实例，相当于在对应位置添加了一个`block`，但此`block`是不可以被引用的
