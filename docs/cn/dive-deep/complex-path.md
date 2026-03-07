# 复合图形

除了[基本图形](./shapes.md)以外，我们还可以定义复杂的**复合图形**(complex path，一般也称复合路径)，比如我们可以定义一个扇形，上面挖出三个洞，这样它就看起来有点像奶酪的样子，但它不是一个扇形+三个圆，而是一个具有复杂边界的图形，它最外侧是扇形，但是中间还有三个圆形的边界，当我们给他填色的时候，中间的三个洞将不会被填充。

[wtht] # Fig Description...
title: 复合图形示例
noTag: yes

vars:
  d: 200
  deg1: 130
  deg2: 170
points:
  p0: 0,0

shapes:
  cCheese: circ; p0; d;
  arcCheese: arc; cCheese; 130; 170; cw;
  cHole1: circ; p0.x - d/3,   p0.y + d * 0.2;  d/20
  cHole2: circ; p0.x - d/1.8, p0.y + d * 0.46; d/12
  cHole3: circ; p0.x - d/1.3, p0.y + d * 0.26; d/24

cpoints:
  cpFan1: cCheese; deg1
  cpFan2: cCheese; deg2

paths:
  cheese:
    - line; p0;  cpFan1
    - arcCheese
    - line; cpFan2; p0;
    - close
    - ...cHole1; ccw;
    - ...cHole2; ccw;
    - ...cHole3; ccw;

blocks:
  blkCheese:
    - cheese; fill, orange; stroke, 2, black;

figure:
  - blkCheese

[wtht]

## 如何创建

创建复合图形的方式为在根级的`paths`标签下添加，像创建基本图形那样，先给出一个名字，然后开始定义。复合图形的定义有两种形式，第一种是单行的定义，此时跟基本图形的定义相同，定义出的结果也是基本图形，而并无**复合**，第二种方式是列表式，此时我们可以按照顺序添加复合图形的每一个**小段**，比如一个扇形有两条线段和一段圆弧组成，我们按照顺序依次添加即可。

```wtht
shapes:
  arc1: arc; ...
  line2: line;...
paths:
  cp1: circ; p0; 5    # 单行式定义，类同于基本图形
  cp2:
    - line; p0; p1;   # 现场定义一个段 (线段)
    - arc1    # 引用已定义的段 (圆弧)
    - line2   # 引用已定义的段 (线段)
    - close   # close 关键字，表示封闭当前的路径
```

复合图形的段可以现场定义，也可以引用已经定义的基本图形，但只能是下面这4种基本图形：

- 线段（注意，不是直线）
- 圆弧
- Bezier2 曲线
- Bezier3 曲线

添加其他种类的基本路径，将被忽略。

## 扩展符

为了添加其他基本图形，比如圆，矩形，我们设计了**扩展符**，可以将其他基本图形或者复合图形**扩展**为段的列表，在对应位置插入。比如，我们想在扇形上面挖洞，但圆形是无法作为段来添加的，我们可通过扩展符来将它扩展后添加，实际添加的是首位相连的四个圆弧段。扩展符是三个连续的圆点`...`（注意，是英文的句号圆点，不是中文的句号，或者居中的圆点），后面跟待扩展的基本图形的名字。

考虑到封闭的基本图形，比如圆，矩形，等，扩展时会有行走方向的关系，所以扩展时我们可以指定第二个行走方向的参数，`cw`（顺时针）或`ccw`（逆时针）。对于矩形，三角形，多边形，多线段，我们还可以指定一个**圆角半径**的参数`radius`，这样在扩展时会在原来顶点位置自动添加（替换为）圆弧，以实现圆角目的。`radius`参数可以是一个数字，也可以是数字序列（不可包含变量）,若为数字序列，则按多边形的第N个角依次对应数字序列的第N个数作为圆角半径。

我们可扩展的基本图形有：

- 三角形
- 矩形
- 多线段
- 多边形
- 圆
- 椭圆


另外，已定义的复合图形也可以被扩展，进而重新添加到新的复合路径中，此时，我们可以指定一个关键字`reverse`来决定是否将复合路径**倒序**添加。

```wtht
paths:
  complexPath1:
    - line; pM; pN;
    - line; pN; pQ;
    - arc; pQ; R1; R2; large; ccw; PR;
    - Bezier2; PR; pRm; pS;
    - Bezier3; pS; pSm1; pSm2; pT;
    - close;
  expandedPath:
    - ...circ1; cw;
    - ...rect1; ccw;
    - ...complexPath1;      # 可以扩展
  expandedPath2:
    - ...rect1; ccw; radius; 5      # 扩展的同时添加圆角
    - ...expandedPath; reverse      # 扩展已定义的复合图形，但倒序添加
```

## 示例分解

下面我们将分步骤将上图的奶酪使用复合图形画出来。

1. 定义关键变量扇形大小，圆心位置，扇形起始边和结束边的角度等。

```wtht
vars:
  d: 200          # 扇形的半径
  deg1: 130       # 扇形的起始角
  deg2: 170       # 扇形的结束角
points:
  p0: 0,0
```

2. 定义基本图形，圆，圆弧，线段，获取圆弧的起始点结束点。

```wtht

shapes:
  # 扇形所在的圆
  cCheese: circ; p0; d;
  # 扇形的圆弧
  arcCheese: arc; cCheese; 130; 170; cw;
  # 三个待挖的洞
  cHole1: circ; p0.x - d/3,   p0.y + d * 0.2;  d/20
  cHole2: circ; p0.x - d/1.8, p0.y + d * 0.46; d/12
  cHole3: circ; p0.x - d/1.3, p0.y + d * 0.26; d/24

cpoints:
  # 定义扇形圆弧的起始点和结束点，便于后面使用
  cpFan1: cCheese; deg1
  cpFan2: cCheese; deg2
```

3. 定义复合路径，我们可以为他取个名字，就叫奶酪(cheese)。

```wtht
# 复合路径定义
paths:
  cheese:
    - line; p0;  cpFan1     # 扇形起始边
    - arcCheese             # 扇形圆弧
    - line; cpFan2; p0;     # 扇形结束边
    - close                 # 封闭这个扇形
    - ...cHole1; ccw;       # 挖第一个洞
    - ...cHole2; ccw;       # 挖第二个洞
    - ...cHole3; ccw;       # 挖第三个洞
```

4. 最后，画出我们的奶酪即可。

```wtht
draw:
  - cheese; fill, orange; stroke, 2, black;
```
## 扩展符参数示例

我们可以使用下面的代码画一个圆，内部挖一个圆角矩形的洞。

```wtht
vars:
  r1: 300
  w1: r1 * 1.2
  h1: w1 * 0.6

points:
  p0: 0,0

shapes:
  rect1: rect; bycenter; p0; w1; h1
  circ1: circ; p0; r1

paths:
  sign1:
    - ...circ1; cw;                           # 外圆
    - ...rect1; ccw; radius; 35, 20, 10, 60   # 挖洞，圆角矩形，圆角半径有4个，且不同
  sign2:
    - ...sign1; reverse
blocks:
  blk1:
    - sign1; fill, orange; stroke, 2, black; marker; mid; arrow2;
  blk2:
    - sign2; fill; red; stroke,2; black; marker; mid; arrow2;

draw:
  - blk1
  - blk2; move; 0, 700;
```

[wtht] # Fig Description...
name: complexPathDemo
noTag: yes
title: sign1，扩展时添加圆角
vars:
  r1: 300
  w1: r1 * 1.2
  h1: w1 * 0.6

points:
  p0: 0,0

shapes:
  rect1: rect; bycenter; p0; w1; h1
  circ1: circ; p0; r1

paths:
  sign1:
    - ...circ1; cw;
    - ...rect1; ccw; radius; 35, 20, 10, 60
  sign2:
    - ...sign1; reverse
blocks:
  blk1:
    - sign1; fill, orange; stroke, 2, black; marker; mid; arrow2;
  blk2:
    - sign2; fill; red; stroke,2; black; marker; mid; arrow2;

figure:
  - blk1
[wtht]

[wtht] # Fig Description...
use: complexPathDemo
noTag: yes
title: sign2，复合图形扩展，reverse
figure:
  - blk2
[wtht]
