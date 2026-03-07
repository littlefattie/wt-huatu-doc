# 连线

我们可以在画图中添加一些**连线**，来将两个不同的**点**或者[**组件端口**](./component.md#组件端口)（某种程度上）自动地连接起来。

考虑到并不是所有的连接都是一个起点一个终点这样一对一的连接，比如电路图中，我们可能需要将许多个组件的端口都接到电源，或者接地，这样就形成了网点（网络）的概念，一个网络里有许多个点，这许多个点需要通过某种方式连接到一起。基于此，我们定义两种类型的连线，[**有网点的连线**](#有网点的连线) 和[**普通连线**](#普通连线)。

## 连线定义

连线只有一种定义方式，即在`block/layer/job`中作为一项绘图元素来添加，通常是绘图元素列表的一项，且已`wire;`开头，后跟绘制连线的各个参数及参数值，即

```wtht
blocks:
  blk1:
    - wire; key1; value1; key2; value2; ...
layers:
  layer1:
    - blk1
    - wire; key1; value1; key2; value2; ...
draw:
  - blk1
  - layer1
  - wire; key1; value1; key2; value2; ...
  - wire; ...
  ...
```

定义连线的参数因连线的种类会有所不同，但有一些任何连线都会使用的参数，分两部分，一是定义连线的参数，比如起点终点等，一是风格化参数，比如划线颜色粗细等。

公共参数：

- `from`: 连线的起点，它可以是某个组件放置的**端口**，或者某个**自由的点**
- `to`: 连线的终点，它可以是某个组件放置的**端口**，或者某个**自由的点**，根据连线类型的不同，它也可能是一个数字，或者空白

端口的指定采用`plcName.portName`的方式，`plcName` 是组件放置的名字(请注意，不是组件定义的名字), `portName`是组件定义时为端口取的名字，比如`n`, `90`等，需要具体查看组件的定义。

自由点可以采用现场临时定义的方式，或者引用已经定义（取过名字）的点。

下面是一个简单的例子：

```wtht
components:
  # 定义一个组件
  comp1:
    ...
    # 定义端口
    ports:
      - name; n; 0, -50; 270; 20
      - name; 90; anchor; 0, 50; rayDeg; 90; minHoldLen; 20
points:
  p1: 100, 0
  p2: 200, 100
placements:
  # 放置两个实例
  plc1: comp1; 0,0;
  plc2: comp1; 0, 100;
draw:
  # 连线 放置1 到 放置2， 端口间互联
  - wire; from; plc1.n;  to; plc2.90;
  - wire; from; plc1.90; to; plc2.n;
  # 连线 点 到 端口
  - wire; p1; plc1.90;
  # 点 和 点 之间连线
  - wire; p1; p2;
```

风格化参数：

- `lineWidth` or `width`: 绘制连线时的**线宽**
- `color` or `stroke`: 绘制连线时给定的**颜色**

```wtht
...
draw:
  # 连线绘制为红色，线宽为2
  - wire; plc1.90; plc2.n; color; red; lineWidth; 2
```


## 有网点的连线

当我们在连线时指定一个`net`参数时，将定义一条有网点的连线，参数的值即为网点的名字。网点名相同的连线将自动地被包括在同一个网络之内。有网点时，我们可以指定也可以不指定连线的终点，即不给出`to`的值，此时，这条连线的定义表示的意思是，将这个点`from`连到网络中。需要注意的是，如果我们某条连线是在一个网络中，有网点的名字，但是它没有终点，该网络中也没有其他的连线添加进来，那么我们将无法画出此连线，我们至少需要另一条同样添加到此网点的连线。

```wtht
points:
  p1: 0,0
  p2: 100, 20
  p3: 20, 50
...
draw:
  # 这条连线将无法画出，因为NET1中只有 p1 一个点
  - wire; p1; net; NET1
  # 下面两条连线可以画出，因为NET2中有 p2 和 p3 两个点
  - wire; p2; net; NET2
  - wire; p3; net; NET2
```

当我们定义有网点的连线时，我们可以使用额外的参数：

- `net`: 网点名，当给出网点名时即表示此连线为有网点的连线。
- `netColor`: 连接到此网点的连线的颜色
- `netLineWidth` (或 `netlw`): 连接到此网点的连线的线宽
- `joint`: 一个**颜色值**，当此连线经过解析，是连接到一条其他的连线时，将产生连接点，指定此颜色值将绘制出这个连接点，默认时一个圆点，圆的大小（直径）由`jointSize`指定
- `jointSize`: 连线连接点的直径（如果是圆）或边长（如果是正方形）
- `squareJoint`: 是否使用**正方形块**来替换圆形连接点

## 网点如何解析

为简化设计，网点之间的彼此连接将采用“**横平竖直**”的方式，因此，网点解析时的第一步是确定一个**主干**，主干确定后，新添加到此网点中的连线将拆解为**两个点连接到主干**的操作。所以在设计网点连线时，彼此出现的顺序将变得重要，先定义的连线将确定主干，后出现的连线只能被动连接。所以在将许多个点连接到同一个网点时，选择合适的连线最先列出来确定主干会显得比较重要。

网点主干可以有多个，但分两类，**水平**，或**竖直**方向。端口（组件端口，下同）连接到主干时，优先选择与自己的发射角垂直的方向，同时会根据端口的发射角就近确定为一个东南西北的方向，因而总是水平或竖直的，水平方向的发射角连到竖直的主干上，竖直的发射角连到水平的主干上。点（自由点）连接到主干时，将选择**离自己最近**的那个主干来连接。

当网点还没有确定主干时，第一个有起点和终点的连线将被选择作为目标尝试确定一些主干。端口连接到端口或者点连接到端口将确定解析出一些主干，点对点的连线将被挂起，待网点中的所有连线都完成解析且仍为确定主干时才会被选用来确定主干。当所有的连线都没有终点时，第一个出现和第二个出现的连线将被**连成一对**以确定主干。

为便于行文，下面的例子中引用如下定义的简单圆角矩形作为组件的示例，定义了8个端口，中心点（坐标零点）为矩形中心：

[wtht] # Fig Description...
noTitle: yes
compPreview:
  scale: 4

inputVars:
  lineWidth: 3
  color: red
  fill: lightgrey
  border: blue
  borderWidth: 2
  width: 100
  height: 80
  content: block text
  corner: 15

# --- Vars ------
vars:
  bw: $width
  bh: $height
  lportW: bw/10
  lportH: bh/10
  lportDiag: sqrt(lportW * lportW + lportH * lportH) * 1.1

  cornerShrink: $corner *(1 - sqrt(0.5))

# --- Points ------
points:
  pCenter: (0, 0)
  pne: pCenter.x + bw/2 - cornerShrink, pCenter.y - bh/2 + cornerShrink
  pse: pCenter.x + bw/2 - cornerShrink, pCenter.y + bh/2 - cornerShrink
  pnw: pCenter.x - bw/2 + cornerShrink, pCenter.y - bh/2 + cornerShrink
  psw: pCenter.x - bw/2 + cornerShrink, pCenter.y + bh/2 - cornerShrink

# --- Shapes ------
shapes:
  container: rect; bycenter; pCenter; bw; bh;
  lne: line; pCenter; pne
  lse: line; pCenter; pse
  lnw: line; pCenter; pnw
  lsw: line; pCenter; psw

# --- Draw --------
draw:
  - container; fill; $fill; stroke; $border, $borderWidth; radius; $corner
  - label; at; pCenter; anchor; center; w; bh; h; bh ||| $content

ports:
  - w; container.w; 180; lportW
  - e; container.e;   0; lportW
  - n; container.n; 270; lportH
  - s; container.s;  90; lportH
  - ne; pne; lne.deg; lportDiag
  - nw; pnw; lnw.deg; lportDiag
  - se; pse; lse.deg; lportDiag
  - sw; psw; lsw.deg; lportDiag

[wtht]

```wtht
components:
  rbox:
    inputVars:
      lineWidth: 3
      color: red
      fill: lightgrey
      border: blue
      borderWidth: 2
      width: 100
      height: 80
      content: block text
      corner: 15

    # --- Vars ------
    vars:
      bw: $width
      bh: $height
      lportW: bw/10
      lportH: bh/10
      lportDiag: sqrt(lportW * lportW + lportH * lportH) * 1.1
      cornerShrink: $corner *(1 - sqrt(0.5))

    # --- Points ------
    points:
      pCenter: (0, 0)
      pne: pCenter.x + bw/2 - cornerShrink, pCenter.y - bh/2 + cornerShrink
      pse: pCenter.x + bw/2 - cornerShrink, pCenter.y + bh/2 - cornerShrink
      pnw: pCenter.x - bw/2 + cornerShrink, pCenter.y - bh/2 + cornerShrink
      psw: pCenter.x - bw/2 + cornerShrink, pCenter.y + bh/2 - cornerShrink

    # --- Shapes ------
    shapes:
      container: rect; bycenter; pCenter; bw; bh;
      lne: line; pCenter; pne
      lse: line; pCenter; pse
      lnw: line; pCenter; pnw
      lsw: line; pCenter; psw

    # --- Draw --------
    draw:
      - container; fill; $fill; stroke; $border, $borderWidth; radius; $corner
      - label; at; pCenter; anchor; center; w; bh; h; bh ||| $content

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

### 端口到端口确定主干

端口到端口确定主干的过程依赖于端口的发射角和端口的实际坐标（组件放置后解析出来的具体值）。端口发射角如果不是东南西北（$0\degree$, $90\degree$, $180\degree$, 或$270\degree$），则会被转换为东南西北，转换规则为**更靠近哪个就是哪个**，即北偏东是北，南偏东是南，东偏南是东，分界点$45\degree$的方向则是南北优先，即东北(NE)西北(NW)会变成北，东南(SE)西南(SW)会变成南。

[wtht] # The demo source code for wire net trunk resolving
asSource: yes
name: port2portSource

components:
  rbox:
    inputVars:
      lineWidth: 3
      color: red
      fill: lightgrey
      border: blue
      borderWidth: 2
      width: 100
      height: 80
      content: block text
      corner: 15

    # --- Vars ------
    vars:
      bw: $width
      bh: $height
      lportW: bw/10
      lportH: bh/10
      lportDiag: sqrt(lportW * lportW + lportH * lportH) * 1.1

      cornerShrink: $corner *(1 - sqrt(0.5))

    # --- Points ------
    points:
      pCenter: (0, 0)
      pne: pCenter.x + bw/2 - cornerShrink, pCenter.y - bh/2 + cornerShrink
      pse: pCenter.x + bw/2 - cornerShrink, pCenter.y + bh/2 - cornerShrink
      pnw: pCenter.x - bw/2 + cornerShrink, pCenter.y - bh/2 + cornerShrink
      psw: pCenter.x - bw/2 + cornerShrink, pCenter.y + bh/2 - cornerShrink

    # --- Shapes ------
    shapes:
      container: rect; bycenter; pCenter; bw; bh;
      lne: line; pCenter; pne
      lse: line; pCenter; pse
      lnw: line; pCenter; pnw
      lsw: line; pCenter; psw


    # --- Draw --------
    draw:
      - container; fill; $fill; stroke; $border, $borderWidth; radius; $corner
      - label; at; pCenter; anchor; center; w; bh; h; bh ||| $content

    ports:
      - w; container.w; 180; lportW
      - e; container.e;   0; lportW
      - n; container.n; 270; lportH
      - s; container.s;  90; lportH
      - ne; pne; lne.deg; lportDiag
      - nw; pnw; lnw.deg; lportDiag
      - se; pse; lse.deg; lportDiag
      - sw; psw; lsw.deg; lportDiag

vars:
  dH: 200
  dV: 300
  wB: 100
  hB: 80
  rB: 15
  dRB: rB * (1 - sqrt(0.5))
  mhCorner: sqrt(10 ** 2 + 8  ** 2) * 1.1

points:
  p11: 0,0
  p12: p11.x + dH, p11.y
  p13: p12.x + dH, p12.y

  p21: p11.x, p11.y + dV
  p22: p21.x + dH, p21.y
  p23: p22.x + dH, p22.y

  p31: p21.x, p21.y + dV
  p32: p31.x + dH, p31.y
  p33: p32.x + dH, p32.y
  p11m22: (p11.x + p22.x) / 2, (p11.y + p22.y) / 2
  p11m22a: (p11.x + p22.x) / 2 + 0.5 * 1, (p11.y + p22.y) / 2 + 0.5 * 1.25
  p11m22b: (p11.x + p22.x) / 2 - 0.5 * 1.25, (p11.y + p22.y) / 2 - 0.5 *  1
  p11se: p11.x + wB/2 - dRB, p11.y + hB/2 - dRB
  p22nw: p22.x - wB/2 + dRB, p22.y - hB/2 + dRB
  p11nw: p11.x - wB/2 + dRB, p11.y - hB/2 + dRB
  p22se: p22.x + wB/2 - dRB, p22.y + hB/2 - dRB
  p11Offset: p11.x + 110, p11.y + 120

  pPtNW:  p22.x - 100, p22.y - 100
  pPtNW2: p22.x - 100, p22.y - 40
  pPtSE:  p22.x + 140, p22.y + 70
  pPtSE2: pPtSE.x, pPtSE.y + 80

shapes:
  lsP22ToNNw: line;  p22nw; p22;
  lP22Se2Nw: line; p22nw; 180 + lsP22ToNNw.deg
  lp11SePerp: line; p11se; 90 + lsP22ToNNw.deg
  lp11SeRay: line; p11se; lsP22ToNNw.deg
  lsSeNwMid: line; p11m22; lsP22ToNNw.deg
  lsNeSwMid: line; p11m22; -lsP22ToNNw.deg
  lP11Nw: line; p11nw; lsP22ToNNw.deg + 180
  lP22Se: line; p22se; lsP22ToNNw.deg
xpoints:
  xpP11SeP22nw: lp11SePerp; lP22Se2Nw
cpoints:
  cpRef1a: lsSeNwMid; -300
  cpRef1b: lsSeNwMid;  300
  cpRef2a: lsNeSwMid; -30
  cpRef2b: lsNeSwMid; -110
  cpRef2c: lsNeSwMid;  30
  cpRef2d: lsNeSwMid;  110
  cp11NwFs: lP11Nw; mhCorner
  cp22SeFs: lP22Se; mhCorner
shapes2:
  lsP22NwP11Se: line; xpP11SeP22nw; p22nw
  npLP11nw: line; cp11NwFs; lsP22ToNNw.deg + 90
cpoints2:
  cpLnDimStart: lP22Se2Nw; mhCorner
  cpLnDimStop: lP22Se2Nw; lsP22NwP11Se.len - mhCorner
xpoints2:
  xpForLen1: lP22Se; npLP11nw
shapes3:
  lsDim:line; cpLnDimStart; cpLnDimStop;
  lsForLen: line; cp11NwFs; xpForLen1;
cpoints3:
  cpTurnDimStart: lp11SeRay; mhCorner
  cpTurn02P11: lp11SeRay; mhCorner + 0.2 * lsDim.len
  cpTurn05P11: lp11SeRay; mhCorner + 0.5 * lsDim.len
  cpTurn33: lsForLen; 0.33
  cpTurn88: lsForLen; 0.88
shapes4:
  lRef33: line; cpTurn33; lsP22ToNNw.deg
  lRef88: line; cpTurn88; lsP22ToNNw.deg

placements:
  b11: rbox; p11; $content; B11
  b12: rbox; p12; $content; B12
  b13: rbox; p13; $content; B13
  b21: rbox; p21; $content; B21
  b22: rbox; p22; $content; B22
  b23: rbox; p23; $content; B23
  b31: rbox; p31; $content; B31
  b32: rbox; p32; $content; B32
  b33: rbox; p33; $content; B33
  b22H: rbox; p22; $content; B22; $width; 80; $height; 100
  b13H: rbox; p13; $content; B13; $width; 80; $height; 100
  b13L: rbox; p13.x, p22.y - 40 -5; $content; B13L;
  b21H: rbox; p21.x, p21.y - 70; $content; B21H;

blocks:
  # Port 2 Port Horizontal Trunks
  # same direction, in queue
  blkHNet_Queue_N:
    - b22
    - b13
    - wire; b22.n; b13.n; net; HNET_QNt; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b22.n; b13.n; net; HNET_QN;  lineWidth; 3; color; red;  netlw; 1;  netcolor; yellow;
    - label; (p22.x + p13.x) / 2, p22.y + 120; anchor; center; ||| 往北，同向
  blkHNet_Queue_N2:
    - b22H
    - b13H
    - wire; b22H.n; b13H.ne; net; HNET_QN2t; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b22H.n; b13H.ne; net; HNET_QN2;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; (p22.x + p13.x) / 2, p22.y + 120; anchor; center; ||| 北和北偏东（近似偏北），同向
  blkHNet_Queue_S:
    - b22
    - b13
    - wire; b22.s; b13.s; net; HNET_QSt; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b22.s; b13.s; net; HNET_QS;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; (p22.x + p13.x) / 2, p22.y + 120; anchor; center; ||| 往南，同向
  blkHNet_Queue:
    - blkHNet_Queue_N;
    - blkHNet_Queue_S;  move; 260, 0;
    #- blkHNet_Queue_N2; move; 600, 0;
  # Facing
  blkHNet_Facing:
    - b22
    - b13
    - wire; b22.n; b13.s; net; HNET_FACEt; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b22.n; b13.s; net; HNET_FACE;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; (p22.x + p13.x) / 2, p22.y + 80; anchor; center; w; 160 ||| 往南，往北，相对
  blkHNet_Facing2:
    - b22H
    - b13H
    - wire; b22H.ne; b13H.sw; net; HNET_FACE2t; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b22H.ne; b13H.sw; net; HNET_FACE2;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; (p22.x + p13.x) / 2, p22.y + 80; anchor; center; w; 220; h;40; ||| 南偏西（近似往南），北偏东(近似往北)，相对
  blkHNet_Facing3:
    - b22
    - b13L
    - wire; b22.n; b13L.s; net; HNET_FACE3t; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b22.n; b13L.s; net; HNET_FACE3;  lineWidth; 3; color; red; netlw; 1; netcolor; yellow;
    - label; (p22.x + p13.x) / 2, p22.y + 50; anchor; tm; w; 200; h; 40; ||| Y向距离不足时会生成一个竖向的主干
  blkHNet_FacingG:
    - blkHNet_Facing;
    - blkHNet_Facing2; move; 350, 0;
  blkHNet_Back1:
    - b12
    - b22
    - wire; b22.s; b12.n; net; HNET_BACK1t; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b22.s; b12.n; net; HNET_BACK1;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; p22.x, p22.y + 100; anchor; center;  ||| B22.s $\to$ B12.n
  blkHNet_Back2:
    - b12
    - b22
    - wire; b12.n; b22.s; net; HNET_BACK2t; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b12.n; b22.s; net; HNET_BACK2;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; p22.x, p22.y + 100; anchor; center;  ||| B12.n $\to$ B22.s
  blkHNet_Back:
    - blkHNet_Back1;
    - blkHNet_Back2; move; 300, 0;

  # Port 2 Port Vertical Trunks
  # Same direction, in queue
  blkVNet_Queue_E:
    - b22
    - b13
    - wire; b22.e; b13.e; net; VNET_QEt; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b22.e; b13.e; net; VNET_QE;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; p13.x - 40, p22.y + 70; anchor; center; ||| 往东，同向
  blkVNet_Queue_W:
    - b22
    - b13
    - wire; b22.w; b13.w; net; VNET_QWt; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b22.w; b13.w; net; VNET_QW;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; p22.x + 40, p22.y + 70; anchor; center; ||| 往西，同向
  blkVNet_Queue:
    - blkVNet_Queue_E;move; 240, 0;
    - blkVNet_Queue_W;

  # Facing
  blkVNet_Facing:
    - b22
    - b13
    - wire; b22.e; b13.w; net; VNET2t; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b22.e; b13.w; net; VNET2;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; (p22.x + p13.x) / 2, p22.y + 80; anchor; center; w; 160 ||| 往东，往西，相对
  blkVNet_Facing2:
    - b22
    - b13
    - wire; b22.se; b13.nw; net; VNET4t; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b22.se; b13.nw; net; VNET4;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; (p22.x + p13.x) / 2, p22.y + 80; anchor; center; w; 250; w; 220; h;40; ||| 东偏南（近似往东），西偏北(近似往西)，相对

  blkVNet_FacingG:
    - blkVNet_Facing;
    - blkVNet_Facing2;move; 400, 0;

  blkVNet_Back1:
    - b12
    - b13
    - wire; b12.w; b13.e; net; VNET_BACK1t; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b12.w; b13.e; net; VNET_BACK1;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; (p12.x + p13.x) / 2, p13.y + 70; anchor; center;  ||| B12.w $\to$ B13.e
  blkVNet_Back2:
    - b12
    - b13
    - wire; b13.e; b12.w; net; VNET_BACK2t; lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; b13.e; b12.w; net; VNET_BACK2;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - label; (p12.x + p13.x) / 2, p13.y + 120; anchor; center;  ||| B13.e $\to$ B12.w
  blkVNet_Back:
    - blkVNet_Back1;
    - blkVNet_Back2; move; 0, 170;
  # Trunk of facing and on same line
  blkFaceSameLine1:
    - b12
    - b22
    - wire; b12.s; b22.n; net; NET_FS_1t; lineWidth; 3; color; none; netlw; 12; netcolor; blue; joint; none;
    - wire; b12.s; b22.n; net; NET_FS_1;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow; jointsize; 6; joint; aqua;
    - wire; p12.x + 130, p22.y  - 60; net; NET_FS_1t;lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; p12.x + 130, p22.y  - 60; net; NET_FS_1; lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - circ; p12.x + 130, p22.y  - 60; 4; ||| fill, black;
    - label; p12.x + 130, p22.y  - 60; anchor;  tm; h; 46 w; 110; ||| 添加此点到net以绘制主干
  blkFaceSameLine2:
    - b23
    - b22
    - wire; b22.e; b23.w; net; NET_FS_2t; lineWidth; 3; color; none; netlw; 12; netcolor; blue; joint; none;
    - wire; b22.e; b23.w; net; NET_FS_2;  lineWidth; 3; color; red;  netlw; 1; netcolor; yellow; jointsize; 6; joint; aqua;
    - wire; p23.x, p23.y  - 100; net; NET_FS_2t;lineWidth; 3; color; none; netlw; 12; netcolor; blue;
    - wire; p23.x, p23.y  - 100; net; NET_FS_2; lineWidth; 3; color; red;  netlw; 1; netcolor; yellow;
    - circ; p23.x, p23.y  - 100; 4; ||| fill, black;
    - label; p13.x, p23.y  - 100; anchor;  bm; h; 46 w; 110; ||| 添加此点到net以绘制主干
  blkFaceSameLineG:
    - blkFaceSameLine1;
    - blkFaceSameLine2; move; 300, -120

  # Perpendicular Trunk
  blkPerp_MeetIn:
    - b22
    - b13
    - wire; b13.w; b22.n; net; PNET_1t; lineWidth; 3; color; none;  netlw; 12; netcolor; blue;
    - wire; b13.w; b22.n; net; PNET_1;  lineWidth; 3; color; red; netlw; 1; netcolor; yellow;
    - label; p22.x + 100, p22.y + 60; anchor; tm; w; 200; h; 40; ||| B13.w $\to$ B22.n, 会相遇，创建两个主干
  blkPerp_MeetOut:
    - b22
    - wire; b22.s;b22.e;  net; PNET_3t; lineWidth; 3; color; none;  netlw; 12; netcolor; blue;
    - wire; b22.s; b22.e; net; PNET_3;  lineWidth; 3; color; red; netlw; 1; netcolor; yellow;
    - label; p22.x, p22.y + 60; anchor; tm; w; 200; h; 40; ||| B22.s $\to$ B22.e, 相互发散，创建两个主干
  blkPerp_Meet:
    - blkPerp_MeetOut
    - blkPerp_MeetIn;move; 200, 0

  blkPerp_Chase1:
    - b12
    - b22
    - wire; b12.s;b22.e;  net; PNET_2t; lineWidth; 3; color; none;  netlw; 12; netcolor; blue;
    - wire; b12.s; b22.e; net; PNET_2;  lineWidth; 3; color; red; netlw; 1; netcolor; yellow;
    - label; p22.x, p22.y + 60; anchor; tm; w; 200; h; 40; ||| B12.s $\to$ B22.e, 无机会相遇，在中间创建主干
  blkPerp_Chase2:
    - b22
    - b21H
    - wire; b21H.s;b22.e;  net; PNET_4t; lineWidth; 3; color; none;  netlw; 12; netcolor; blue;
    - wire; b21H.s; b22.e; net; PNET_4;  lineWidth; 3; color; red; netlw; 1; netcolor; yellow;
    - label; p22.x - 60, p22.y + 110; anchor; tm; w; 200; h; 40; ||| B21H.s $\to$ B22.e, Y向空间不足，往“更前方”放置主干
  blkPerp_Chase:
    - blkPerp_Chase1;
    - blkPerp_Chase2; move; 380, -120

  # Point to Port Trunk
  blkPt2Port_N:
    - b22
    - wire; pPtNW; b22.n; net; NET_PT2P_1t; lineWidth; 3; color; none; netlw; 12;  netcolor; blue;
    - wire; pPtNW; b22.n; net; NET_PT2P_1;  lineWidth; 3; color; red; netlw; 1; netcolor; yellow;
    - circ; pPtNW; 5; ||| fill, black;
    - label; p22.x - 10, p22.y + 70; anchor; tm;  ||| 就近寻找相遇点，创建主干
  blkPt2Port_E:
    - b22
    - wire; pPtNW; b22.e; net; NET_PT2P_2t; lineWidth; 3; color; none; netlw; 12;  netcolor; blue;
    - wire; pPtNW; b22.e; net; NET_PT2P_2;  lineWidth; 3; color; red; netlw; 1; netcolor; yellow;
    - circ; pPtNW; 5; ||| fill, black;
    - label; p22.x - 10, p22.y + 70; anchor; tm;  ||| 就近创建主干，“回头”连接自由点
  blkPt2Port_W:
    - b22
    - wire; pPtNW; b22.w; net; NET_PT2P_3t; lineWidth; 3; color; none; netlw; 12;  netcolor; blue;
    - wire; pPtNW; b22.w; net; NET_PT2P_3;  lineWidth; 3; color; red; netlw; 1; netcolor; yellow;
    - circ; pPtNW; 5; ||| fill, black;
    - label; p22.x - 10, p22.y + 70; anchor; tm;  ||| 就近寻找相遇点，创建主干

  blkPt2Port_S:
    - b22
    - wire; pPtNW; b22.s; net; NET_PT2P_4t; lineWidth; 3; color; none; netlw; 12;  netcolor; blue;
    - wire; pPtNW; b22.s; net; NET_PT2P_4;  lineWidth; 3; color; red; netlw; 1; netcolor; yellow;
    - circ; pPtNW; 5; ||| fill, black;
    - label; p22.x - 10, p22.y + 70; anchor; tm;  ||| 就近创建主干，“回头”连接自由点
  blkPt2Port_NoSpace:
    - b22
    - wire; pPtNW2; b22.e; net; NET_PT2P_5t; lineWidth; 3; color; none; netlw; 12;  netcolor; blue;
    - wire; pPtNW2; b22.e; net; NET_PT2P_5;  lineWidth; 3; color; red; netlw; 1; netcolor; yellow;
    - circ; pPtNW2; 5; ||| fill, maroon;
    - circ; pPtNW; 5; ||| fill, black;
    - label; p22.x - 10, p22.y + 70; anchor; tm; w; 200;h ;36;  ||| Y向空间不足，往外扩展，创建两个主干

  blkPt2Port:
    - blkPt2Port_N;
    - blkPt2Port_E; move; 250, 0;
    - blkPt2Port_W; move; 0, 250;
    - blkPt2Port_S; move; 250, 250;

  blkPt2PortNoSpace:
    - blkPt2Port_E;
    - blkPt2Port_NoSpace; move; 280, 0;

  # Point to Point trunks
  blkPt2Pt1:
    - wire; pPtNW; pPtSE; net; NET_Pt2Pt1t; line;width; 3; color; none; netlw; 12; netcolor; blue;
    - wire; pPtNW; pPtSE; net; NET_Pt2Pt1; line;width; 3; color; red; netlw; 1; netcolor; yellow;
    - circ; pPtNW; 5; ||| fill, black;
    - circ; pPtSE; 5; ||| fill, black;
    - dimmark; pPtNW; pPtSE;type;v; label; $\Delta y$; gap; 10;barLenEqual
    - dimmark; pPtNW; pPtSE;type;h; label; $\Delta x$; gap; 10; dir; left; barLenEqual
    - dimMark; pPtSE; pPtSE.x, (pPtNW.y + pPtSE.y) / 2; v; label; $\frac{\Delta y}{2}$; gap; 10;  lh; 40;
    - label; (pPtNW.x + pPtSE.x) / 2, pPtSE.y + 80; anchor; tm; ||| $\Delta x > \Delta y$, 水平主干
  blkPt2Pt2:
    - wire; pPtNW; pPtSE2; net; NET_Pt2Pt2t; line;width; 3; color; none; netlw; 12; netcolor; blue;
    - wire; pPtNW; pPtSE2; net; NET_Pt2Pt2; line;width; 3; color; red; netlw; 1; netcolor; yellow;
    - circ; pPtNW; 5; ||| fill, black;
    - circ; pPtSE2; 5; ||| fill, black;
    - dimmark; pPtNW; pPtSE2;type;v; label; $\Delta y$; gap; 10; dir; left;barLenEqual
    - dimmark; pPtNW; pPtSE2;type;h; label; $\Delta x$; gap; 10; barLenEqual
    - dimMark; pPtNW;  (pPtNW.x + pPtSE2.x)/2, pPtNW.y; h; left; label; $\frac{\Delta x}{2}$; gap; 10;  lh; 40;
    - label; (pPtNW.x + pPtSE.x) / 2, pPtSE.y + 120; anchor; tm; ||| $\Delta y > \Delta x$, 竖直主干
  blkPt2PtTrunk:
    - blkPt2Pt1
    - blkPt2Pt2; move; 320, 0;
  blkpt2ptDirectH:
    - wire; pPtNW; pPtNW.x + 100, pPtNW.y; net; NET_Pt2Pt3t; line; width;3 color; none; netlw; 12; netcolor; blue;
    - wire; pPtNW; pPtNW.x + 100, pPtNW.y; net; NET_Pt2Pt3; line; width;3 color; red; netlw; 1; netcolor; yellow;
    - circ; pPtNW; 5; ||| fill, black;
    - circ; pPtNW.x + 100, pPtNW.y; 5 ||| fill, black;
    - label; pPtNW.x + 50, pPtNW.y + 40; anchor; tm; w;140; h; 40; ||| 两点在同一水平线上, 水平主干
  blkpt2ptDirectV:
    - wire; pPtNW; pPtNW.x , pPtNW.y+ 80; net; NET_Pt2Pt4t; line; width;3 color; none; netlw; 12; netcolor; blue;
    - wire; pPtNW; pPtNW.x , pPtNW.y+ 80; net; NET_Pt2Pt4; line; width;3 color; red; netlw; 1; netcolor; yellow;
    - circ; pPtNW; 5; ||| fill, black;
    - circ; pPtNW.x , pPtNW.y+ 80; 5 ||| fill, black;
    - label; pPtNW.x, pPtNW.y + 100; anchor; tm; w;140; h; 40; ||| 两点在同一竖直线上，竖直主干

  blkpt2ptDirect:
    - blkpt2ptDirectH;
    - blkpt2ptDirectV; move; 240, -50;

  # wire, turnAT
  blkAuto_TurnAt1:
    - b11
    - b22
    - circ; p11.x + 300, p22.y - 190; 5; ||| fill, red;
    - wire; b11.ne; b22.se; turnAt; p11.x + 300, p22.y - 190;
    - label; p11.x + 300, p22.y - 190; anchor; ml; w; 80; ||| turnAt
  blkAuto_TurnAt2:
    - b11
    - b22
    - circ; p11.x + 300, p22.y - 190; 5; ||| fill, red;
    - wire; b11.ne; b22.se; turnX; p11.x + 300; turnY; p22.y - 190;
    - label; p11.x + 300, p22.y - 190; anchor; ml; w; 130; ||| (turnX, turnY)

  blkAuto_SameLine1:
    - b12
    - b22
    - wire; b12.s; b22.n;
    - label; p22.x, p22.y + 70; anchor; tm; width; 120; h; 40; ||| 端口共线，相对，直连
  blkAuto_SameLine2:
    - b12
    - b22
    - wire; b12.n; b22.n;
    - label; p22.x, p22.y + 70; anchor; tm; width; 130; h; 40; ||| 端口共线，同向，绕到侧边相连
  blkAuto_SameLine3:
    - b12
    - b22
    - wire; b12.n; b22.s;
    - label; p22.x, p22.y + 70; anchor; tm; width; 130; h; 40; ||| 端口共线，相背，绕到侧边相连
  blkAuto_SameLine4:
    - b12
    - b22
    - wire; b22.s; b12.n;
    - label; p22.x, p22.y + 70; anchor; tm; width; 130; h; 40; ||| 端口共线，相背，起点终点互换
  blkAuto_SameLineG1:
    - blkAuto_SameLine1;
    - blkAuto_SameLine2; move; 200, 0;
    - blkAuto_SameLine3; move; 400, 0;
    - blkAuto_SameLine4; move; 600, 0;
  blkAuto_SameLine1Op:
    - b12
    - b22
    - wire; b12.n; b22.n; turnx;300;
    - line; 300, p12.y - 80; 300, p12.y +  300; ||| stroke, 1, red; dash; 3,3;
    - label; 300, p12.y - 80; anchor; bm; w; 70; h; 30;  ||| turnX
    - label; p22.x + 20, p22.y + 70; anchor; tm; width; 150; h; 40; ||| turnX 和 turnLen 含义冲突，turnX优先
  blkAuto_SameLine2Op:
    - b12
    - b22
    - wire; b12.n; b22.n; turnLen;160; turnY; -100;
    - dimmark; p12.x, p12.y - 110; p12.x + 160, p12.y - 110; h; left; label; turnLen;
    - line; p12.x - 50, -100; p12.x + 160 + 50, -100; ||| stroke, 1, red; dash; 3,3
    - label; p12.x - 50, -100; w; 70; h; 20; anchor; mr; ||| turnY
    - label; p22.x + 70, p22.y + 70; anchor; tm; width; 130; h; 40; ||| turnY 和 turnLen 联合使用
  blkAuto_SameLine3Op:
    - b11
    - b12
    - wire; b11.e; b12.e; turny; 120;
    - line; p11.x, 120; p12.x + 100, 120 ||| stroke, 1, red; dash; 3,3
    - label; p11.x, 120; anchor; mr; w; 80; h; 20; ||| turnY
    - label; (p11.x + p12.x) / 2, p12.y + 130; anchor; tm; width; 300; h; 40; ||| turnY 和 turnLen 冲突，turnY 优先
  blkAuto_SameLine4Op:
    - b11
    - b12
    - wire; b11.w; b12.e; turnX; -100; turnLen; 100;
    - line; -100, p11.y + 50; -100, p11.y - 140; ||| stroke, 1, red; dash; 3,3;
    - label; -100, p11.y - 140; anchor; bm; w; 100; h; 20; ||| turnX
    - dimmark; -105, p11.y - 100; -105, p11.y; label; turnLen
    - label; (p11.x + p12.x) / 2, p12.y + 50; anchor; tm; width; 300; h; 40; ||| turnX 和 turnLen 联合使用
  blkAuto_SameLineG2:
    - blkAuto_SameLine1Op;
    - blkAuto_SameLine2Op; move; 350, 0;
  blkAuto_SameLineG3:
    - blkAuto_SameLine3Op;
    - blkAuto_SameLine4Op; move; 0, 350;
  # 端口平行
  blkAuto_Port_Parallel1:
    - b11
    - b22
    - wire; b11.sw; b22.sw;  # 相同方向
    - wire; b11.ne; b22.ne;  # 背靠背
    - line; cpRef2a; cpRef2b; ||| stroke,1,red; dash; 3,3;
    - line; cpRef2c; cpRef2d; ||| stroke,1,red; dash; 3,3;
  blkAuto_Port_Parallel1a:
    - b11
    - b22
    - wire; b11.se; b22.nw; color; red;
    - wire; b11.nw; b22.se;
    - line; cpRef1a; cpRef1b; ||| stroke, 1, red; dash; 3,3;
    - label; p11m22.x + 50, p11m22.y - 50; w; 150; h; 60; anchor; ml; ||| 面对面，端口发射线长度最大化，中间位置画垂线
  blkAuto_Port_Parallel_SD:
    - blkAuto_Port_Parallel1
    - blkAuto_Port_Parallel1a; move; 300, 0;
    - line; p11.x - 50, p22.y + 110; p22.x - 30, p22.y + 110; ||| stroke, 1, red; dash; 3,3;
    - label; p22.x + 20, p22.y + 110; anchor; ml; align; left;||| 中间平行线
  blkAuto_Port_Parallel_Turn_Base:
    - b11
    - b22
    - line; p11se; cpTurnDimStart; ||| stroke, 12, aqua;
    - line; cpLnDimStart; p22nw; ||| stroke, 12, aqua;
  blkAuto_Port_Parallel_Turn1a:
    - blkAuto_Port_Parallel_Turn_Base
    - wire; b11.se; b22.nw;  color; red;
    - line; cpLnDimStop; cpTurnDimStart; ||| stroke, 0.5, grey; dash; 3,3;
    - dimmark; cpLnDimStart; cpLnDimStop; dir; left; label; $D$
    - dimmark; cpTurn05P11;cpTurnDimStart;  label; $0.5D$
  blkAuto_Port_Parallel_Turn1b:
    - blkAuto_Port_Parallel_Turn_Base
    - wire; b11.se; b22.nw;  color; red; turn; 0.2;
    - line; cpLnDimStop; cpTurnDimStart; ||| stroke, 0.5, grey; dash; 3,3;
    - dimmark; cpLnDimStart; cpLnDimStop; dir; left; label; $D$
    - dimmark; cpTurn02P11;cpTurnDimStart;  label; $0.2D$
  blkAuto_Port_Parallel_Turn1:
    - blkAuto_Port_Parallel_Turn1a
    - blkAuto_Port_Parallel_Turn1b; move; 250, 0;
    - label; p22.x + 20, p22.y + 60; w; 360; h; 20; anchor; tm ||| 端口的minHoldLen不包括在距离计算范围内
  blkAuto_Port_Parallel_Turn2:
    - b11
    - b22
    - wire; b11.sw; b22.sw; turnx; p11.x - 120
    - wire; b11.ne; b22.ne; turnY; p11.y - 60;
    - circ; p11; 20; ||| fill, red, 0.3;
    - line; p11.x, p11.y - 60; p11.x + 160, p11.y - 60; ||| stroke, 0.5, red; dash; 3,3
    - line; p11.x - 120, p11.y + 40; p11.x - 120, p11.y + 200; ||| stroke, 0.5, red; dash; 3,3
    - label; p11.x + 160, p11.y - 60; anchor; ml; w; 60; ||| turnY
    - label; p11.x - 120, p11.y + 200; w; 60; h; 40; anchor; tm; ||| turnX
    - dimmark; p11.x, p11.y + 100; p11.x - 120, p11.y + 100; showlength;
    - dimmark; p11.x - 55, p11.y - 60; p11.x-55, p11.y ; showlength;
  blkAuto_Port_Parallel_Turn3a:
    - b11
    - b22
    - wire; b11.nw; b22.se; turn; 0.33; turnx; p11.x - 120
    - line; cpRef1a; cpRef1b; ||| stroke, 1, red; dash; 3,3;
    - line; lRef33; -120; 520; ||| stroke, 1, aqua; dash; 3,3;
    - line; p11.x - 120, p11.y - 60; p11.x - 120, p11.y - 130; ||| stroke, 0.5, red; dash; 3,3
    - label; p11.x - 120, p11.y - 130; w; 60; h; 40; anchor; bm; ||| turnX
    - dimmark; cp11NwFs; xpForLen1; label; $D$; gap; 140;
    - dimmark; cp11NwFs; cpTurn33; label; $0.33D$; gap; 90;
    - line; lP22Se; -600; -10; ||| stroke, 0.5, black; dash; 3,3;
  blkAuto_Port_Parallel_Turn3b:
    - b11
    - b22
    - wire; b11.nw; b22.se; turn; 0.88; turny; p11.y -70
    - line; cpRef1a; cpRef1b; ||| stroke, 1, red; dash; 3,3;
    - line; lRef88; -60; 550; ||| stroke, 1, aqua; dash; 3,3;
    - line; p11.x-120, p11.y - 70; p11.x - 30, p11.y - 70; ||| stroke, 0.5, red; dash; 3,3
    - label; p11.x - 20, p11.y - 70; anchor; ml; w; 60; ||| turnY
    - dimmark; cp11NwFs; xpForLen1; label; $D$; gap; 110;
    - dimmark; cp11NwFs; cpTurn88; label; $0.88D$; gap; 60;
    - line; lP22Se; -600; -10; ||| stroke, 0.5, black; dash; 3,3;
  blkAuto_Port_Parallel_Turn3:
    - blkAuto_Port_Parallel_Turn3a
    - blkAuto_Port_Parallel_Turn3b; move; 300, 0;
    - line; p22.x - 400, p22.y; p22.x - 300, p22.y; ||| stroke, 1, red; dash;3,3;
    - label; p22.x - 280, p22.y; anchor; ml; w; 180; h; 40; ||| 红色线是中间位置(turn没有指定, 默认0.5D)
  # Port-2-port Auto TurnY
  blkAuto_turnY_1a:
    - b11
    - b22
    - wire; b11.se; b22.ne; turny; -70;color; red;
    - line; p11.x - 150, -70; p11.x + 700, -70; ||| stroke, 1, black; dash; 3,3;
    - label; p11.x - 140, -75; w; 100; h; 20; anchor; bl; ||| turnY = -70
  blkAuto_turnY_1b:
    - b11
    - b22
    - wire; b11.se; b22.ne; turny; 100; color; blue;
    - line; p11.x, 100; p11.x + 500, 100; ||| stroke, 1, black; dash; 3,3;
    - label; p11.x + 5, 100; w; 100; h; 20; anchor; tl; ||| turnY = 100
  blkAuto_turnY_1c:
    - b11
    - b22
    - wire; b11.se; b22.ne; turny; 400; color; magenta;
    - line; p11.x, 400; p11.x + 550, 400; ||| stroke, 1, black; dash; 3,3;
    - label; p11.x, 400; w; 100; h; 20; anchor; tl; ||| turnY = 400
  blkAuto_turnY_1:
    - blkAuto_turnY_1a
    - blkAuto_turnY_1b; move; 200, 450
    - blkAuto_turnY_1c; move; -50, 580
  blkAuto_turnY_2:
    - b11
    - b22
    - wire; b11.e; b22.ne; turny; 100; color; red;
    - line; p11.x, 100; p11.x + 500, 100; ||| stroke, 1, black; dash; 3,3;
    - label; p11.x, 100; w; 100; h; 20; anchor; tl; ||| turnY = 100
    - label; p11.x + 70, p11.y; anchor; ml; w; 180; h; 60; ||| 起点端口是水平方向，设置turnY时会直接跳转到y = turnY 的水平线位置
  # Port-2-port Auto TurnX
  blkAuto_turnX_1a:
    - b11
    - b22
    - wire; b11.se; b22.ne; turnx; 100;color; red;
    - line; 100, p11.y + 10; 100, p22.y + 120; ||| stroke, 1, black; dash; 3,3;
    - label; 100, p11.y + 20; w; 100; h; 20; anchor; ml; ||| turnX = 100
  blkAuto_turnX_1b:
    - b11
    - b22
    - wire; b11.se; b22.ne; turnx; -100; color; blue;
    - line; -100, p11.y - 100; -100, p22.y + 260; ||| stroke, 1, black; dash; 3,3;
    - label; -100, 150; w; 120; h; 20; anchor; tl; ||| turnX = -100
  blkAuto_turnX_1c:
    - b11
    - b22
    - wire; b11.s; b22.ne; turnx; 120; color; magenta;
    - line; 120, p11.y; 120, p22.y + 100; ||| stroke, 1, black; dash; 3,3;
    - label; 120, p22.y + 110; w; 100; h; 20; anchor; tm; ||| turnX = 120
    - label; 125, p11.y + 50; anchor; ml; w; 180; h; 60; ||| 起点端口是竖直方向，设置turnX时会直接跳转到x = turnX 的水平线位置
  blkAuto_turnX_1:
    - blkAuto_turnX_1a
    - blkAuto_turnX_1b; move; 440, 0
    - blkAuto_turnX_1c; move; 0, 560
  # Port 2 port perp
  blkAuto_port_perp1a:
    - b11
    - b22
    - wire; b11.e; b22.n; red;
  blkAuto_port_perp1b:
    - b11
    - b22
    - wire; b11.e; b22.s; green;
  blkAuto_port_perp1c:
    - b11
    - b22
    - wire; b11.w; b22.s; magenta;
  blkAuto_port_perp1d:
    - b11
    - b22
    - wire; b11.n; b22.w; maroon;
  blkAuto_port_perp1:
    - blkAuto_port_perp1a
    - blkAuto_port_perp1b; move; 350, 0;
    - blkAuto_port_perp1c; move; 0, 400;
    - blkAuto_port_perp1d; move; 350, 400;
  # Port to Port direct connect
  blkAuto_port_direct:
    - b11
    - b22
    - wire; b11.ne; b22.n;
    - wire; b11.sw; b22.w;
  # Point to Port
  blkAuto_pt2Pa:
    - b11
    - wire; b11.ne; p11Offset
  blkAuto_pt2Pb:
    - b11
    - wire; b11.e; p11Offset
  blkAuto_pt2Pc:
    - b11
    - wire; b11.w; p11Offset
  blkAuto_pt2Pd:
    - b11
    - wire; b11.sw; p11Offset
  blkAuto_pt2Pe:
    - b11
    - wire; b11.n; p11Offset
  blkAuto_pt2Pf:
    - b11
    - wire; b11.s; p11Offset
  blkAuto_pt2P:
    - blkAuto_pt2Pa;
    - blkAuto_pt2Pb; move; 200, 0
    - blkAuto_pt2Pc; move; 400, 0
    - blkAuto_pt2Pd; move;   0, 200
    - blkAuto_pt2Pe; move; 200, 200
    - blkAuto_pt2Pf; move; 400, 200
  # HVH type
  blkHVH_1a:
    - b11
    - b22
    - wire; hvh; b11.e; b22.e; red;
    - label; p11.x, p11.y + 80; anchor; ml; w; 120; h; 40; ||| 未给出参数，等价于turn=0.5
  blkHVH_1b:
    - b11
    - b22
    - wire; hvh; b11.e; b22.e; turnX; p22.x + 80; blue;
    - label; p11.x, p11.y + 80; anchor; ml; w; 120; h; 40; ||| 给定turnX
  blkHVH_1c:
    - b11
    - b22
    - wire; hvh; b11.e; b22.e; turnAt; p22; pink
    - label; p11.x, p11.y + 80; anchor; ml; w; 120; h; 40; ||| 给定turnAt
  blkHVH_1d:
    - b11
    - b22
    - wire; hvh; b11.e; b22.e; turn; 0.2; magenta
    - label; p11.x, p11.y + 80; anchor; ml; w; 120; h; 40; ||| 给定turn
  blkHVH_1:
    - blkHVH_1a
    - blkHVH_1b; move; 400, 0;
    - blkHVH_1c; move; 0, 400;
    - blkHVH_1d; move; 400, 400;
  # VHV type
  blkVHV_1a:
    - b11
    - b22
    - wire; vhv; b11.e; b22.e; red;
    - label; p11.x + 100, p11.y; anchor; ml; w; 120; h; 40; ||| 未给出参数，等价于turn=0.5
  blkVHV_1b:
    - b11
    - b22
    - wire; vhv; b11.e; b22.e; turny; p11.y + 100; blue;
    - label; p11.x, p11.y + 120; anchor; ml; w; 120; h; 40; ||| 给定turnY
  blkVHV_1c:
    - b11
    - b22
    - wire; vhv; b11.e; b22.e; turnAt; p11Offset; pink
    - label; p11.x + 80, p11.y + 80;  anchor; ml; w; 120; h; 40; ||| 给定turnAt
  blkVHV_1d:
    - b11
    - b22
    - wire; vhv; b11.e; b22.e; turn; 0.2; magenta
    - label; p11.x, p11.y + 80; anchor; ml; w; 120; h; 40; ||| 给定turn
  blkVHV_1:
    - blkVHV_1a
    - blkVHV_1b; move; 400, 0;
    - blkVHV_1c; move; 0, 400;
    - blkVHV_1d; move; 400, 400;
  # HVHV type
  blkHVHV_1:
    - b11
    - b22
    - wire; hvhv; b11.e; b22.s; turnx; p22.x + 100; turny; p22.y + 150; red
    - wire; hvhv; b11.nw; b22.sw; turnAt; p21; magenta;
    - line;  p22.x + 100, p11.y - 70; p22.x + 100, p11.y - 10; ||| stroke,1, black; marker; arrow1;
    - label; p22.x + 100, p11.y - 76; w; 30;h;20;anchor;bm; ||| $X_m$
    - line;  p22.x + 170, p22.y + 150; p22.x + 110, p22.y + 150; ||| stroke,1,black; marker; arrow1;
    - label; p22.x + 175, p22.y + 150; anchor;ml; w; 30; h;20 ||| $Y_m$
    - line;  p21.x - 80, p21.y; p21.x - 10, p21.y; ||| stroke,1,black; marker; arrow1;
    - label; p21.x - 85, p21.y; anchor;mr; w; 30; h;20 ||| $Y_m$
    - line;  p21.x, p21.y + 85; p21.x, p21.y + 10; ||| stroke,1,black; marker; arrow1;
    - label; p21.x, p21.y + 89; anchor; tm; w; 30; h; 20; ||| $X_m$
  # VHVH type
  blkVHVH_1:
    - b11
    - b22
    - wire; vhvh; b11.e; b22.s; turnx; p22.x + 100; turny; p22.y + 150; red
    - wire; vhvh; b11.nw; b22.sw; turnAt; p21; magenta;
    - line;  p22.x + 100, p22.y + 10; p22.x + 100, p22.y + 40; ||| stroke,1, black; marker; arrow1;
    - label; p22.x + 100, p22.y + 6; w; 30;h;20;anchor;bm; ||| $X_m$
    - line;  p22.x + 170, p22.y + 150; p22.x + 110, p22.y + 150; ||| stroke,1,black; marker; arrow1;
    - label; p22.x + 175, p22.y + 150; anchor;ml; w; 30; h;20 ||| $Y_m$
    - line;  p21.x - 130, p21.y; p21.x - 70, p21.y; ||| stroke,1,black; marker; arrow1;
    - label; p21.x - 135, p21.y; anchor;mr; w; 30; h;20 ||| $Y_m$
    - line;  p21.x, p21.y + 105; p21.x, p21.y + 50; ||| stroke,1,black; marker; arrow1;
    - label; p21.x, p21.y + 109; anchor; tm; w; 30; h; 20; ||| $X_m$
  # HVHVH type
  blkHVHVH_1a:
    - b11
    - b22
    - wire; hvhvh; b11.ne; b22.se;
    - line; cp22SeFs.x - dH * 2 /3, p11.y - 100; cp22SeFs.x - dH * 2 / 3, p11.y - 50; ||| stroke,1,black; marker; arrow1;
    - label; cp22SeFs.x - dH * 2 / 3, p11.y - 100; anchor; bm; w; 100; h; 60; ||| $X_{m1} = \frac{\Delta x}{3}$
    - line; cp22SeFs.x - dH /3, p11.y +85; cp22SeFs.x - dH / 3, p11.y + 130; ||| stroke,1,black; marker; arrow1;
    - label; cp22SeFs.x - dH / 3, p11.y + 85; anchor; bm; w; 100; h; 60; ||| $X_{m2} = \frac{2\Delta x}{3}$
    - line; cp22SeFs.x - dH * 2 / 3 - 70, (p11.y + p22.y) / 2; cp22SeFs.x - dH * 2 / 3 - 20, (p11.y + p22.y) / 2; ||| stroke,1,black; marker; arrow1;
    - label; cp22SeFs.x - dH * 2 / 3 - 75, (p11.y + p22.y) / 2; w; 100; h; 60; anchor;mr; align;right; ||| $ Y_m=\frac{\Delta y}{2}$
  blkHVHVH_1b:
    - b11
    - b22
    - wire; hvhvh; b11.ne; b22.se; turnAt; p23; turnx; p11.x + 150; turny; p11.y + 110;
    - circ; p23; 6; ||| fill, maroon, 0.4;
    - line; p11.x + 150, p11.y -100; p11.x + 150, p11.y - 50; ||| stroke,1,black; marker; arrow1;
    - label; p11.x + 150, p11.y - 100; anchor; bm; w; 100; h; 60; ||| $X_{m1} = p11.x + 150$
    - line; p23.x, p11.y + 15; p23.x, p11.y + 95; ||| stroke,1, black; marker; arrow1;
    - label; p23.x, p11.y + 15; anchor; bm; w; 120; h; 60; ||| $X_{m2} = \text{turnAt.x}$
    - line;  p11.x + 100,  p11.y + 110;  p11.x + 140,  p11.y + 110; ||| stroke,1,black; marker; arrow1;
    - label; p11.x + 90, p11.y + 110; w; 100; h; 60; anchor;mr; align;right; ||| $ Y_m=p11.y + 110$
    - label; p23; anchor; mr; w; 80; h; 20; ||| turnAt
  blkHVHVH_1:
    - blkHVHVH_1a;
    - blkHVHVH_1b; move; 360, 0;
  # VHVHV type
  blkVHVHV_1a:
    - b11
    - b22
    - wire; vhvhv; b11.ne; b22.se;
    - line; p11.y -20, p11.y +85; p11.y + 35, (cp11NwFs.y * 2 + cp22SeFs.y)/3; ||| stroke,1,black; marker; arrow1;
    - label; p11.y -25, (cp11NwFs.y * 2 + cp22SeFs.y)/3; anchor; mr; w; 100; h; 60; ||| $Y_{m1} = \frac{\Delta y}{3}$
    - line; p22.x - 110, (cp11NwFs.y + 2 *cp22SeFs.y)/3; p22.x  -60, (cp11NwFs.y + 2 *cp22SeFs.y)/3; ||| stroke,1,black; marker; arrow1;
    - label; p22.x - 115, (cp11NwFs.y + 2 *cp22SeFs.y)/3; anchor; mr; w; 100; h; 60; ||| $Y_{m2} = \frac{2\Delta y}{3}$
    - line;  cp22SeFs.x - dH /2, p11.y + 10; cp22SeFs.x - dH /2, p11.y + 60; ||| stroke,1,black; marker; arrow1;
    - label; cp22SeFs.x - dH /2, p11.y; w; 100; h; 40; anchor; bm;   ||| $ X_m=\frac{\Delta x}{2}$
  blkVHVHV_1b:
    - b11
    - b22
    - wire; vhvhv; b11.ne; b22.se; turnAt; p23; turnx; p11.x + 150; turny; p11.y + 110;
    - circ; p23; 6; ||| fill, maroon, 0.4;
    - line; p11.x + 150, p11.y + 50; p11.x + 150, p11.y + 100; ||| stroke,1,black; marker; arrow1;
    - label; p11.x + 150, p11.y + 50; anchor; bm; w; 100; h; 60; ||| $X_{m} = p11.x + 150$
    - line; p22.x -105, p23.y; p22.x - 65, p23.y; ||| stroke,1, black; marker; arrow1;
    - label; p22.x - 110, p23.y; anchor; mr; w; 120; h; 60; ||| $Y_{m2} = \text{turnAt.y}$
    - line;  cp11NwFs.x + 45,  p11.y + 110;  cp11NwFs.x + 90,  p11.y + 110; ||| stroke,1,black; marker; arrow1;
    - label; cp11NwFs.x + 40, p11.y + 110; w; 100; h; 60; anchor;mr; align;right; ||| $ Y_{m1}=p11.y + 110$
    - label; p23; anchor; mr; w; 80; h; 20; ||| turnAt
    - line; p22.x - 80, p22.y; p23.x + 20, p23.y |||  stroke,1, red; dash; 3,3;
  blkVHVHV_1:
    - blkVHVHV_1a;
    - blkVHVHV_1b; move; 360, 0;
jobs:
  p2pHQueue:
    - blkHNet_Queue
  p2pHFacing:
    - blkHNet_FacingG
  p2pHBack:
    - blkHNet_Back
  p2pVQueue:
    - blkVNet_Queue
  p2pVFacing:
    - blkVNet_FacingG
  p2pVBack:
    - blkVNet_Back
  p2pFaceSameLine:
    - blkFaceSameLineG
  p2pPerpHVMeet:
    - blkPerp_Meet
  p2pPerpHVChase:
    - blkPerp_Chase
  pt2pESWN:
    - blkPt2Port
  pt2pNoSpace:
    - blkPt2PortNoSpace
  pt2ptTrunk:
    - blkPt2PtTrunk
  pt2ptTrunkDirect:
    - blkpt2ptDirect
[wtht]


主干的确定规则如下：

1. 两个端口的方向是平行的，同为水平或竖直：

- 如果两个端口的方向是相对的（面对面的）则得到的主干是垂直两者方向的线，位置在两者中间

[wtht] # Port face H
noTitle: yes
use: port2portSource
figure: blkVNet_FacingG
[wtht]

[wtht] # Port face V
noTitle: yes
use: port2portSource
figure: blkHNet_FacingG
[wtht]

但是如果两个端口之间的距离过近，则会变成另一个方向的主干。


[wtht] # Port face V
noTitle: yes
use: port2portSource
figure:
  - blkHNet_Facing3; move; 250, 300;
[wtht]

- 如果两个端口的方向是同向的，则会在那个方向最前面的端口外侧得到一个垂直于两者方向的主干

[wtht] # Port Same Dir H
noTitle: yes
use: port2portSource
figure: blkHNet_Queue
[wtht]

[wtht] # Port Same Dir V
noTitle: yes
use: port2portSource
figure: blkVNet_Queue
[wtht]

- 如果两个端口的方向是相反的（背靠背的）则得到的主干是与二者的方向**平行**的线，距离在出发点向右转弯的一定距离处

[i] 但请注意，这里的主干位置与起点和终点的相对位置有关系，原则是沿着起点开始，“**出门右拐**”到达终点。

[wtht] # Port Back H
noTitle: yes
use: port2portSource
figure: blkHNet_Back
[wtht]

[wtht] # Port Back V
noTitle: yes
use: port2portSource
figure: blkVNet_Back
[wtht]

- 如果两个端口的方向是相对的，面对面，且是在同一条线上，那么两者将会直接连起来，且得到一个位于连线中点的，垂直于其方向的主干

[wtht] # Port Back V
noTitle: yes
use: port2portSource
figure: blkFaceSameLineG
[wtht]


2.两个端口的方向是垂直的，一个水平，一个竖直：

- 如果两个端口的发射线会“相遇”，或者“不相遇”但反向延长线会相撞，则创建两个主干

[wtht] # Port HV meet
noTitle: yes
use: port2portSource
figure: p2pPerpHVMeet
[wtht]

- 如果一个端口的发射线会“撞上”另一个发射线的反向延长线，则在他们“中间”的位置创建一个主干，如果中间的空间过小，则往“发射线”前进的方向放置这个主干


[wtht] # Port HV meet
noTitle: yes
use: port2portSource
figure: p2pPerpHVChase
[wtht]


### 点到端口确定主干

点到端口确定主干采用“**就近**”原则，自由点总是试图往端口的发射线作垂线以连接，垂点与端口坐标点之间的部分即为主干。如果垂点不在端口的发射线上，而是在反向延长线上，则创建垂直方向的主干，以实现“回头”来连接自由点。

[wtht] # Point to Port Trunk
noTitle: yes
use: port2portSource
figure: pt2pESWN
[wtht]

如果自由点到端口的发射线的距离过短（空间不足），且自由点到发射线的垂点在反向延长线上，则会创建两个主干，通过“拐弯”来与自由点相遇。

[wtht] # Point to Port Trunk， no space
noTitle: yes
use: port2portSource
figure: pt2pNoSpace
[wtht]

### 点到点确定主干

点到点创建主干非常简单，比较两个端点X和Y坐标的插值$\Delta x$ 和$\Delta y$, 如果 $\Delta x$ 更大，则创建水平的主干，如果$\Delta y$更大，则创建竖直的主干，原则是主干尽可能长，主干都是在两个端点之间的位置。

[wtht] # Point to Point Trunk
noTitle: yes
use: port2portSource
figure: pt2ptTrunk
[wtht]

如果两个点在同一条水平或竖直线上，则两点间的竖直或水平线段即为主干。


[wtht] # Point to Point Trunk
noTitle: yes
use: port2portSource
figure: pt2ptTrunkDirect
[wtht]

[i] 请注意，点到点的连线**并不会**优先确定主干，只有在没有其他方式确定主干的时候才会被采用来确定主干。另外，两个点在同一水平或竖直线上时会**优先**确定主干，所以如果我们要绘制一幅图，其中有一条横平或竖直的干线的时候，我们可以先列出来，以确定主干，然后其他的端点都将连接到这条主干。

### 网点示例:

1. 如下的例子将绘制一条水平的主干，然后将需要连接的网点都就近连接到主干上

```wtht
draw:
  - b21
  - b22
  - b23
  - circ; p21.x -  60, p21.y - 80; 8; ||| fill, red;
  - circ; p23.x + 100, p23.y - 80; 8; ||| fill, red;
  # 水平线上的两个端点确定主干
  - wire; p21.x - 60, p21.y - 80; p23.x + 100, p21.y - 80; net; NET_ME_Pt2Pt1;
  # 其他的端口都连接到这条主干上
  - wire; b21.e;  net; NET_ME_Pt2Pt1;
  - wire; b21.n;  net; NET_ME_Pt2Pt1;
  - wire; b22.sw; net; NET_ME_Pt2Pt1;
  - wire; b22.s;  net; NET_ME_Pt2Pt1;
  - wire; b23.nw; net; NET_ME_Pt2Pt1;
  - wire; b23.se; net; NET_ME_Pt2Pt1;
```

[wtht] # 两点共水平线确定网点主干
noTitle: yes
use: port2portSource
figure:
  - b21
  - b22
  - b23
  - circ; p21.x -  60, p21.y - 80; 8; ||| fill, red;
  - circ; p23.x + 100, p23.y - 80; 8; ||| fill, red;
  # 水平线上的两个端点确定主干
  - wire; p21.x - 60, p21.y - 80; p23.x + 100, p21.y - 80; net; NET_ME_Pt2Pt1;
  # 其他的端口都连接到这条主干上
  - wire; b21.e;  net; NET_ME_Pt2Pt1;
  - wire; b21.n;  net; NET_ME_Pt2Pt1;
  - wire; b22.sw; net; NET_ME_Pt2Pt1;
  - wire; b22.s;  net; NET_ME_Pt2Pt1;
  - wire; b23.nw; net; NET_ME_Pt2Pt1;
  - wire; b23.se; net; NET_ME_Pt2Pt1;
[wtht]

2. 如下的例子中，先出现的端口确定主干，自由点会分别连接到主干上。如果一开始确定（初始生成）的主干长度不足，新加入的连线会对主干进行扩展，直到计算出的连接点的位置

```wtht
draw:
  - b21
  - b22
  - b23
  - circ; p21.x -  60, p21.y - 80; 8; ||| fill, red;
  - circ; p23.x + 100, p23.y - 80; 8; ||| fill, red;
  # 这条连线将确定主干
  - wire; b21.n; b23.se; net;NET_ME_Pt2Pt2; netcolor; grey; netlw; 6;lineWidth; 4; color; black;
  # 水平线上的两个端点,虽然在水平线上，但不会确定主干
  - wire; p21.x - 60, p21.y - 80; p23.x + 100, p21.y - 80; net; NET_ME_Pt2Pt2; lineWidth; 4; color; black;
  # 其他的端口都连接到这条主干上
  - wire; b21.e;  net; NET_ME_Pt2Pt2;lineWidth; 4; color; black;
  - wire; b22.sw; net; NET_ME_Pt2Pt2;lineWidth; 4; color; black;
  - wire; b22.s;  net; NET_ME_Pt2Pt2;lineWidth; 4; color; black;
  - wire; b23.nw; net; NET_ME_Pt2Pt2;lineWidth; 4; color; black;
```

[wtht] # 两点共水平线确定网点主干
noTitle: yes
use: port2portSource
figure:
  - b21
  - b22
  - b23
  - circ; p21.x -  60, p21.y - 80; 8; ||| fill, red;
  - circ; p23.x + 100, p23.y - 80; 8; ||| fill, red;
  # 这条连线将确定主干
  - wire; b21.n; b23.se; net;NET_ME_Pt2Pt2a; netcolor; blue; netlw; 12;lineWidth; 4; color; black;
  - wire; b21.n; b23.se; net;NET_ME_Pt2Pt2; netcolor; grey; netlw; 6;lineWidth; 4; color; black;
  # 水平线上的两个端点,虽然在水平线上，但不会确定主干，因为不是网点中的第一根连线
  - wire; p21.x - 60, p21.y - 80; p23.x + 100, p21.y - 80; net; NET_ME_Pt2Pt2; lineWidth; 4; color; black;
  # 其他的端口都连接到这条主干上
  - wire; b21.e;  net; NET_ME_Pt2Pt2;lineWidth; 4; color; black;
  - wire; b22.sw; net; NET_ME_Pt2Pt2;lineWidth; 4; color; black;
  - wire; b22.s;  net; NET_ME_Pt2Pt2;lineWidth; 4; color; black;
  - wire; b23.nw; net; NET_ME_Pt2Pt2;lineWidth; 4; color; black;
  - label; p22; offset; -10, -70; anchor; bm; w; 200; h; 60; font;songti ||| 蓝色是一开始确定的主干，灰色是所有连线连接好以后扩展到位的主干
[wtht]


3. 如下的例子中，两个点不在同一个水平或竖直线上时，即便第一个出现，也不会被采用来创建主干，对比上面第2个例子

```wtht
draw:
  - b21
  - b22
  - b23
  - circ; p21.x -  60, p21.y - 80; 8; ||| fill, red;
  - circ; p23.x + 100, p23.y - 100; 8; ||| fill, red;
  # 水平线上的两个端点,不在水平线上，首先出现也不会用来创建主干
  - wire; p21.x - 60, p21.y - 80; p23.x + 100, p21.y - 100; net; NET_ME_Pt2Pt3; lineWidth; 4; color; black;
  # 这条连线将确定主干
  - wire; b21.n; b23.se; net;NET_ME_Pt2Pt3; netcolor; grey; netlw; 6;lineWidth; 4; color; black;
  # 其他的端口都连接到这条主干上
  - wire; b21.e;  net; NET_ME_Pt2Pt3;lineWidth; 4; color; black;
  - wire; b22.sw; net; NET_ME_Pt2Pt3;lineWidth; 4; color; black;
  - wire; b22.s;  net; NET_ME_Pt2Pt3;lineWidth; 4; color; black;
  - wire; b23.nw; net; NET_ME_Pt2Pt3;lineWidth; 4; color; black;
```
[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - b21
  - b22
  - b23
  - circ; p21.x -  60, p21.y - 80; 8; ||| fill, red;
  - circ; p23.x + 100, p23.y - 100; 8; ||| fill, red;
  # 这条连线将确定主干
  - wire; b21.n; b23.se; net;NET_ME_Pt2Pt3a; netcolor; blue; netlw; 12;lineWidth; 4; color; black;
  # 水平线上的两个端点,不在水平线上，首先出现也不会用来创建主干
  - wire; p21.x - 60, p21.y - 80; p23.x + 100, p21.y - 100; net; NET_ME_Pt2Pt3; lineWidth; 4; color; black;
  - wire; b21.n; b23.se; net;NET_ME_Pt2Pt3; netcolor; grey; netlw; 6;lineWidth; 4; color; black;
  # 其他的端口都连接到这条主干上
  - wire; b21.e;  net; NET_ME_Pt2Pt3;lineWidth; 4; color; black;
  - wire; b22.sw; net; NET_ME_Pt2Pt3;lineWidth; 4; color; black;
  - wire; b22.s;  net; NET_ME_Pt2Pt3;lineWidth; 4; color; black;
  - wire; b23.nw; net; NET_ME_Pt2Pt3;lineWidth; 4; color; black;
  - label; p22; offset; -10, -70; anchor; bm; w; 200; h; 60; font;songti ||| 蓝色是一开始确定的主干，灰色是所有连线连接好以后扩展到位的主干
  - label; p23.x + 100, p23.y - 130; w; 140; h; 40; anchor; bm; font; songti;||| 自由点会“就近”连接到主干
[wtht]

4. 如下的例子中，我们需要将四个端口连接起来，第一个出现的连线确定主干

```wtht
draw:
  - b21
  - b22
  - b23
  - b32
  # 这条连线确定主干
  - wire; b21.s; b32.n; net; NET_ME_p2p4;
  - wire; b22.s; net; NET_ME_p2p4;
  - wire; b23.s; net; NET_ME_p2p4;
```
[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - b21
  - b22
  - b23
  - b32
  - wire; b21.s; b32.n; net; NET_ME_p2p4a; netcolor; indianred; netlw; 12;color; none;
  - wire; b21.s; b32.n; net; NET_ME_p2p4;
  - wire; b22.s; net; NET_ME_p2p4;
  - wire; b23.s; net; NET_ME_p2p4;
[wtht]

如果我们换一个顺序确定主干，结果会有所不同

```wtht
draw:
  - b21
  - b22
  - b23
  - b32
  # 换两个端口确定主干
  - wire; b21.s; b23.s; net; NET_ME_p2p5;
  - wire; b22.s;  net; NET_ME_p2p5;
  - wire; b32.n;  net; NET_ME_p2p5;
```
[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - b21
  - b22
  - b23
  - b32
  - wire; b21.s; b23.s; net; NET_ME_p2p5a;netcolor; indianred; netlw; 12; color; none;
  - wire; b21.s; b23.s; net; NET_ME_p2p5;
  - wire; b22.s;  net; NET_ME_p2p5;
  - wire; b32.n;  net; NET_ME_p2p5;
[wtht]

当网络中有不止一个“端点”时，我们可以只给出起点，然后加上`net`名字即可，比如这段代码

```wtht
draw:
  - wire; b21.s; b23.s; net; NET_ME_p2p5;
  - wire; b22.s; net; NET_ME_p2p5;
  - wire; b32.n; net; NET_ME_p2p5;
```
可以写成

```wtht
draw:
  # 最先出现的两个端点 b21.s 和 b23.s 将组成一对确定主干
  - wire; b21.s; net; NET_ME_p2p5;
  - wire; b23.s; net; NET_ME_p2p5;
  - wire; b22.s; net; NET_ME_p2p5;
  - wire; b32.n; net; NET_ME_p2p5;
```

5. 如下的例子中，两个相对的端口将直连，第一个出现的一对，中点位置将确定一个主干，另一对连线的中点位置将添加一条线连接到主干。

```wtht
draw:
  - b21
  - b22
  - b23
  - B33
  - wire; b21.e; b22.w; net; NET_ME_p2p6;
  - wire; b23.s; b33.n; net; NET_ME_p2p6;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - b21
  - b22
  - b23
  - b33
  - wire; b21.e; b22.w; net; NET_ME_p2p6a; netcolor; indianred; netlw; 12; color; none;
  - wire; b21.e; b22.w; net; NET_ME_p2p6;netcolor; blue; netlw; 6; color;black; lineWidth; 2;
  - wire; b23.s; b33.n; net; NET_ME_p2p6; color; black; lineWidth; 2;
  - label; (p21.x + p22.x) / 2, p22.y - 10; anchor; bm; w; 100; h; 20; font; songti; ||| 端口直连
  - label; p22.x - 90, p22.y + 90; ml; w; 100; h; 40; font; songti; ||| 从连线中点出发确定主干
  - label; p23.x + 10, (p23.y  + p33.y) / 2; ml; w; 80; h; 40; font; songti; ||| 端口直连
[wtht]

6. 如下的例子跟前面的例子相同，添加了一些关于连接点的参数设定。

```wtht
draw:
  - b21
  - b22
  - b23
  - B33
  - wire; b21.e; b22.w; net; NET_ME_p2p7; joint; red;  jointsize; 20
  - wire; b23.s; b33.n; net; NET_ME_p2p7; joint; aqua; jointsize; 30; squareJoint;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - b21
  - b22
  - b23
  - b33
  - wire; b21.e; b22.w; net; NET_ME_p2p7a; netcolor; indianred; netlw; 12; color; none;
  - wire; b21.e; b22.w; net; NET_ME_p2p7;netcolor; blue; netlw; 6; color;black; lineWidth; 2; joint; red; jointsize; 20
  - wire; b23.s; b33.n; net; NET_ME_p2p7; color; black; lineWidth; 2;joint; aqua; jointsize; 30; squareJoint;
  - label; (p21.x + p22.x) / 2, p22.y - 10; anchor; bm; w; 100; h; 20; font; songti; ||| 端口直连
  - label; p22.x - 90, p22.y + 90; ml; w; 100; h; 40; font; songti; ||| 从连线中点出发确定主干
  - label; p23.x + 10, (p23.y  + p33.y) / 2; ml; w; 80; h; 40; font; songti; ||| 端口直连
[wtht]



[i] 关于主干和连接点的一些要点
- 主干长度为0时将不会被绘制
- 确定的主干在后续连线连接到它时长度会进行扩展，直到计算出的连接点的位置
- 主干的颜色可以重复指定，会采用最新设置的那个颜色，同一网点的主干都会是一种颜色
- 如指定了网点颜色和线宽，网点中所有的连线都将使用这个颜色和线宽，如果需要使用不同的，需要每条连线再单独指定`lineWidth` 和 `color`, 没指定的都将使用网点的线宽和颜色
- 连接点位于主干端点且此连接点并未重复（没有其他端点以此为连接点连接到主干）时，将不会画出
- 连接点默认画出，如需取消，请添加`joint;none;`来取消
- 网点的名字在**整个画图范围**内共享，如果你打算在不同的`block`或其他层级中绘制单独的网络连接关系，建议你使用单独的网点名字
- 网点名字遵守**普通变量名**的命名限制，即字母开头，可接受数字字母下划线的组合


## 普通连线

如果定义连线时**没有**给出网点名，它就是一条**普通连线**。普通连线需同时指定起点和终点参数，即`to`参数不可省略，否则会报错，并无法绘制连线。普通连线除了使用公共参数`from`, `to`, `lineWidth`, `color`之外，还使用如下的参数：

- `type`: 连线的类型(见下文)，如未指定，则为`auto`
- `turn`: 一个介于[0.1]之间的数，大概表示“在从起点到终点的路径上在那个位置开始转弯”的意思
- `turnX`: 一个**数值**，表示连线将在`x == turnX`的位置开始转弯，如水平转垂直等
- `turnY`: 一个**数值**，表示连线将在`y == turnY`的位置开始转弯，如竖直转水平等
- `turnAt`: 一个**点**，表示连线将在这个位置转弯，比如我们指定连线类型为`line`时，可以指定一个中间点`turnAt`
- `turnLen`: 一个**数值**，表示连线在**行走多远**后开始转弯，只在某些特定情况下被使用
- `radius`: 指定连线转弯的圆角半径
- `startArrow`: 连线的**起始端**箭头，取值可以是**预定义的箭头的名字**(比如`arrow1`)，也可以是现场定义的**标记**(marker)的名字（当然，最好这个标记是一个箭头）
- `endArrow`: 连线的**终点端**箭头（可能更常用），取值可以是**预定义的箭头的名字**(比如`arrow1`)，也可以是现场定义的**标记**(marker)的名字（当然，最好这个标记是一个箭头）

## 连线类型

连线类型参数`type`,可取的值为`auto`, `line`, `toX`, `toV`, `toY`, `toH`, `hv`, `vh`, `hvh`, `vhv`, `hvhv`, `vhvh`, `hvhvh`, `vhvhv`。不同类型的连线所使用的参数略有不同，下面将依次说明：

- `auto`

当指定`type; auto;`或者不给出`type`参数时，连线类型即为`auto`，意为自动。在此模式下，如果`turnAt`参数指定，或是`turnX` 和`turnY`同时指定，则连线简化为从“起点”经由“**中间点**”到“终点”的**折线**。中间点在`turnAt`给定时使用`turnAt`，没给定时使用`(turnX, turnY)`。

```wtht
draw:
  - b11
  - b22
  - wire; b11.ne; b22.se; turnAt; p11.x + 300, p22.y - 190;
  # 或使用 turnX 和 turnY
  - wire; b11.ne; b22.se; turnX; p11.x + 300; turnY; p22.y - 190;
```


[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_TurnAt1
  - blkAuto_TurnAt2; move; 350, 0;
[wtht]

如果没有指定`turnAt`，或者`turnX`和`turnY`没有同时指定，则会根据起点（端口或自由点）和终点（端口或自由点）的特点来做一些判别。

**端口到端口**

如果起点和终点都是组件放置的端口时，我们按一下的顺序来判断。

1. 两个端口是否在同一条线上

如果两个端口在同一条线上时，若他们“面对面”时，将直接连接。若是“同向”或“背对背”，则会从出发点“向右”绕一下再连接到终点端口。比如如果我们只简单连接一下这些端口时：

```wtht
draw:
  - b12
  - b22
  - wire; b12.s; b22.n; # 面对面
  - wire; b12.n; b22.n; # 同向
  - wire; b12.n; b22.s; # 背对背
  - wire; b22.s; b12.n; # 背对背，起点终点互换
```
会是如下的结果
[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_SameLineG1
[wtht]

当然，在需要“绕道”的时候我们可以指定`turnX`,`turnY`,`turnLen`，以调整第一个“转弯点”的位置。

```wtht
draw:
  - b12
  - b22
  - wire; b12.n; b22.n; turnx;300;
  - wire; b12.n; b22.n; turnLen;160; turnY; -100;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_SameLineG2
[wtht]

```wtht
draw:
  - b11
  - b12
  - wire; b11.e; b12.e; turny;  120;
  - wire; b11.w; b12.e; turnX; -100; turnLen; 100;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_SameLineG3
[wtht]

2. 两个端口是否平行

当两个端口不在同一直线上，但平行时（在同一直线上必然平行，但已优先处理），如果未指定其他参数，将在平行方向的中间位置创建一条平行的直线，然后两个端口各自“垂直连接”到这条中间线，两个连接点之间的部分将作为最终连线的一部分。如果两个端口平行且“面对面”，则会延长各自的发射线，在二者的中间位置绘制一条垂直于发射线方向的线段连接彼此。

```wtht
draw:
  - b11
  - b22
  - wire; b11.sw; b22.sw;  # 相同方向
  - wire; b11.ne; b22.ne;  # 相同方向
  - wire; b11.se; b22.nw; color; red;  # 面对面
  - wire; b11.nw; b22.se;  # 背靠背
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_Port_Parallel_SD
[wtht]

当两个端口平行且相对时，我们可以指定`turn`参数, 它表示端口间的这条折线在哪个位置开始转弯，`turn`是一个0到1之间的小数，表示比例，具体示例如下，请注意，两个端口之间的距离是按平行线的方向计算的，且各自的`minHoldLen`不包含在长度距离范围内。

```wtht
draw:
  - b11
  - b22
  - wire; b11.se; b22.nw; color; red;  # 没有指定turn, 等价于0.5
  - wire; b11.se; b22.nw; color; red; turn; 0.2; # 指定turn = 0.2
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_Port_Parallel_Turn1
[wtht]

当`turnX`或`turnY`指定时，转折点会在起点发射线上`x=turnX`或`y=turnY`的位置，具体可参考下方示意图。

```wtht
draw:
  - b11
  - b22
  - wire; b11.sw; b22.sw; turnx; p11.x - 120
  - wire; b11.ne; b22.ne; turnY; p11.y - 60;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_Port_Parallel_Turn2
[wtht]

当两个端口“背靠背”时，指定`turnX` 或 `turnY`的同时，我们还可以指定`turn`参数，它将确定中间的那根平行于两个端口发射线，连接两个端口折线的中间线的位置，如下图所示：

```wtht
draw:
  - b11
  - b22
  - wire; b11.nw; b22.se; turn; 0.33; turnx; p11.x - 120
  - wire; b11.nw; b22.se; turn; 0.88; turny; p11.y - 70
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_Port_Parallel_Turn3
[wtht]

3. `turnY`有没有指定

如果两个端口不平行，则会检查是否有`turnY`参数，如果`turnY`指定，则起点端口会沿着发射线行走，一直到`y=turnY`的地方，然后画一条`y=turnY`的水平线，水平线一直行走到与终点端口发射线相交的位置，如果起点发射角是水平方向，则在保持`minHoldLen`长度之后会立即转到中间的水平线。

```wtht
draw:
  - b11
  - b22
  - wire; b11.se; b22.ne; turny; -70;color; red;
  - wire; b11.se; b22.ne; turny; 100; color; blue;
  - wire; b11.se; b22.ne; turny; 400; color; magenta;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_turnY_1
[wtht]

```wtht
draw:
  - b11
  - b22
  - wire; b11.e; b22.ne; turny; 100; color; red;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_turnY_2
[wtht]

4. `turnX`有没有指定

如果`turnY`没有被指定，而`turnX`被指定，则同前面的情况类似，起点端口会沿着发射线行走，一直到`x=turnX`的地方，然后画一条`x=turnX`的竖直线，竖直线一直行走到与终点端口发射线相交的位置，如果起点发射角是竖直方向，则在保持`minHoldLen`长度之后会立即转到中间的竖直线。

```wtht
draw:
  - b11
  - b22
  - wire; b11.se; b22.ne; turnx;  100; color; red;
  - wire; b11.se; b22.ne; turnx; -100; color; blue;
  - wire; b11.s;  b22.ne; turnx;  120; color; magenta;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_turnX_1
[wtht]


5. 两个端口的发射反向是否垂直

如果两个端口以上的参数或者条件都不具备，则会检查其两个端口的发射线是否垂直，如果是，则会计算出垂直相交的交点，然后两个端口都连接到这个交点。

```wtht
draw:
  - b11
  - b22
  - wire; b11.e; b22.n; red;
  - wire; b11.e; b22.s; green;
  - wire; b11.w; b22.s; magenta;
  - wire; b11.n; b22.w; maroon;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_port_perp1
[wtht]

6. 以上条件都不满足

如果以上条件都未满足，端口到端口将**直连**，即起点端口在沿发射角保持`minHoldLen`长度之后通过**直线段**连到终点的端口（终点端口沿其发射角保持`minHoldLen`长度之后的点）

```wtht
draw:
  - b11
  - b22
  - wire; b11.ne; b22.n;
  - wire; b11.sw; b22.w;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_port_direct
[wtht]

**端口到自由点**

在连线类型为`auto`时，端口到自由点的连线不接受任何参数，如果端口时水平或者竖直方向，则自由点会通过向端口的发射线做垂线的方式与其连接，如果不是，则它们直接连接，如下图所示：

```wtht
points:
  ...
  p11Offset: p11.x + 110, p11.y + 120
draw:
  - b11
  - wire; b11.ne; p11Offset
  - wire; b11.e;  p11Offset
  - wire; b11.w;  p11Offset
  - wire; b11.sw; p11Offset
  - wire; b11.n;  p11Offset
  - wire; b11.s;  p11Offset
```
[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkAuto_pt2P
[wtht]

**自由点到自由点**

在连线类型为`auto`时，自由点到自由点不接受任何参数，只是通过**线段直连**。


[i] 考虑到`auto`类型连线容易受算法限制，可能无法解析出期望的连线，这样我们可以具体指定一种连线类型，然后给定相应参数，这样会得到更加精准的连线。

- `line`

`line`类型连线，意为“线段直连”，但他接受一个中间点参数，`turnAt`或者`(turnX, turnY)`的组合。


```wtht
draw:
  - b11
  - b22
  - wire; type; line; b11.e; b22.n; red;
  # 当首先出现的时连线类型的取值时，type关键字可省略
  - wire; line; b11.s; b22.s; turnAt; p11.x - 20, p22.y + 5; blue;
  - wire; line; b11.s; b22.nw; turnX; p22.x - 70; turnY; p22.y; magenta
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - b11
  - b22
  - wire; type; line; b11.e; b22.n; red;
  - wire; line; b11.s; b22.s; turnAt; p11.x - 20, p22.y + 5; blue;
  - wire; line; b11.s; b22.nw; turnX; p22.x - 70; turnY; p22.y; magenta
[wtht]

- `toX` 或 `toV`

`toX`(或`toV`)含义为“**连接到竖直线**”，指定连线类型为此，会将起点的端口或连接线连接到由`to`指定的竖直线。绘制此种连线时，终点`to`可以是一个点，或端口，也可以是一个**数值**，当它是一个点或者端口时，竖直线的`x`坐标将是点的`x`分量（即`p.x`），当它是一个数值时，这个数值即为竖直线的`x`坐标。

此种类型的连线，接受一个参数`turnY`，意为在`y=turnY`时转弯，转弯之后再连接到竖直线，但如果端口本身就是水平方向的，则`turnY`无效。

```wtht
draw:
  - b11
  - b22
  # 以下连线都连接到竖直线 x = p11.x + 148 位置
  - wire; type; tov; from; b11.n;  to; p11.x + 148; red;
  - wire; tov;       from; b11.n;  to; p11.x + 148; turny; p11.y - 100; aqua;  # 给定turnY
  - wire; tov; b11.nw;                 p11.x + 148; turny; p11.y - 80;  blue;
  - wire; tov; b22.ne;                 p11.x + 148; turny; p22.y - 140; magenta;
  - wire; tov; p22.x + 50, p11.y + 40; p11.x + 148; turny; p11.y + 60;
  # 终点是点或端口，将取x分量作为竖直线的位置
  - wire; tov; b11.e; p22; color; pink
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - b11
  - b22
  - line; p11.x + 148, p11.y - 200; p11.x + 148, p11.y + 200; ||| stroke, 8, grey;
  - wire; type; tov; from; b11.n;  to; p11.x + 148; red;
  # 给定turnY
  - wire; tov;       from; b11.n;  to; p11.x + 148; turny; p11.y - 100; aqua;
  - wire; tov; b11.nw; p11.x + 148; turny; p11.y - 80; blue;
  - wire; tov; b22.ne; p11.x + 148; turny; p22.y - 140; magenta;
  - circ; p22.x + 50, p11.y + 40; 5; ||| fill, red;
  - wire; tov; p22.x + 50, p11.y + 40; p11.x + 148; turny; p11.y + 60;
  - line; p22.x, p11.y-30; p22; ||| stroke,1,black; dash; 3,3;
  # 终点是点或端口，将取x分量作为竖直线的位置
  - wire; tov; b11.e; p22; color; pink
[wtht]

- `toY` 或 `toH`

`toY`(或`toH`)含义为“**连接到水平线**”，指定连线类型为此，会将起点的端口或连接线连接到由`to`指定的水平线。绘制此种连线时，终点`to`可以是一个点，或端口，也可以是一个**数值**，当它是一个点或者端口时，水平线的`y`坐标将是点的`y`分量（即`p.y`），当它是一个数值时，这个数值即为水平线的`y`坐标。

此种类型的连线，接受一个参数`turnX`，意为在`x=turnX`时转弯，转弯之后再连接到水平线，但如果端口本身就是竖直方向的，则`turnX`无效。

```wtht
draw:
  - b11
  - b22
  # 以下连线都连接到水平线 y = p11.y - 68 位置
  - line; p11.x - 100,p11.y - 68 ;  p11.x + 300 , p11.y - 68 ; ||| stroke, 8, grey;
  - wire; type; toh; from; b11.e;  to; p11.y - 68; red;
  - wire; toh;       from; b11.e;  to; p11.y - 68; turnx; p11.x + 130; aqua;  # 给定turnX
  - wire; toh; b11.nw;                 p11.y - 68; turnx; p11.x - 10; blue;
  - wire; toh; b22.ne;                 p11.y - 68; turnx; p22.x + 80; magenta;
  - wire; toh; p22.x + 10, p11.y + 40; p11.y - 68; turnx; p22.x + 30;
  # 终点是点或端口，将取y分量作为水平线的位置
  - wire; toh; b11.s; p22; color; pink
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - b11
  - b22
  - line; p11.x - 100,p11.y - 68 ;  p11.x + 300 , p11.y - 68 ; ||| stroke, 8, grey;
  - wire; type; toh; from; b11.e;  to; p11.y - 68; red;  
  - wire; toh;       from; b11.e;  to; p11.y - 68; turnx; p11.x + 130; aqua; # 给定turnX
  - wire; toh; b11.nw;                 p11.y - 68; turnx; p11.x - 10; blue;
  - wire; toh; b22.ne;                 p11.y - 68; turnx; p22.x + 80; magenta;
  - circ; p22.x + 10, p11.y + 40; 5; ||| fill, red;
  - wire; toh; p22.x + 10, p11.y + 40; p11.y - 68; turnx; p22.x + 30;
  - line; p22.x -300, p22.y; p22; ||| stroke,1,black; dash; 3,3;
  # 终点是点或端口，将取x分量作为竖直线的位置
  - wire; toh; b11.s; p22; color; pink
[wtht]

- `hv`

`hv`类型的连线意为**先水平行走再拐向竖直到达终点**，它不接受参数，会在起点和终点间画一个水平线和竖直线，水平线以**起点的y**为坐标，竖直线以**终点的x**为坐标。请注意，此时起点和终点的顺序会起作用，起点和终点互换，会是不同的路径。

```wtht
draw:
  - b11
  - b22
  - wire; type; hv; b11.ne; b22.se;
  - wire; type; hv; b11.sw; b22.w;
  # 互换起点和终点
  - wire; type; hv; b22.w; b11.sw; red; lineWidth; 1;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - b11
  - b22
  - wire; type; hv; b11.ne; b22.se;
  - wire; type; hv; b11.sw; b22.w;
  # 互换起点和终点
  - wire; type; hv; b22.w; b11.sw; red; lineWidth; 1;
[wtht]

- `vh`

`vh`类型的连线意为**先竖直行走再拐向水平到达终点**，它不接受参数，会在起点和终点间画一个竖直线和水平线，竖直线以**起点的x**为坐标，水平线以**终点的y**为坐标。请注意，此时起点和终点的顺序会起作用，起点和终点互换，会是不同的路径，你也注意到，起点终点互换之后，等价于起点终点不变，但是更换连线类型为`hv`的结果。

```wtht
draw:
  - b11
  - b22
  - wire; type; vh; b11.ne; b22.se;
  - wire; type; vh; b11.sw; b22.w;
  # 互换起点和终点
  - wire; type; vh; b22.w; b11.sw; red; lineWidth; 1;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - b11
  - b22
  - wire; type; vh; b11.ne; b22.se;
  - wire; type; vh; b11.sw; b22.w;
  # 互换起点和终点
  - wire; type; vh; b22.w; b11.sw; red; lineWidth; 1;
[wtht]

- `hvh`

`hvh`类型为沿着起点**先水平再竖直再水平**以到达终点。它使用一个中间点$X_m$来进行转折，如果`turnX`提供则它取值`turnX`, 其次如果`turnAt`提供，则取值`turnAt.x`, 再其次如果`turn`提供，则它在**起点的x**到**终点的x**之间，按照与`turn`成比例的那个`x`来取值。


```wtht
draw:
  - b11
  - b22
  - wire; hvh; b11.e; b22.e; red;
  - wire; hvh; b11.e; b22.e; turnX; p22.x + 80; blue;
  - wire; hvh; b11.e; b22.e; turnAt; p22; pink
  - wire; hvh; b11.e; b22.e; turn; 0.2; magenta
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkHVH_1
[wtht]


- `vhv`

`hvh`类型为沿着起点**先竖直再水平再竖直**以到达终点。它使用一个中间点$Y_m$来进行转折，如果`TurnY`提供则它取值`turnY`, 其次如果`turnAt`提供，则取值`turnAt.y`, 再其次如果`turn`提供，则它在**起点的x**到**终点的x**之间，按照与`turn`成比例的那个`y`来取值。


```wtht
draw:
  - b11
  - b22
  - wire; vhv; b11.e; b22.e; red;
  - wire; vhv; b11.e; b22.e; turny; p11.y + 100; blue;
  - wire; vhv; b11.e; b22.e; turnAt; p11Offset; pink
  - wire; vhv; b11.e; b22.e; turn; 0.2; magenta
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkHVH_1
[wtht]


- `hvhv`

`hvhv`类型为沿着起点**先水平再竖直再水平再竖直**以到达终点。它使用一个中间点$X_m$和$Y_m$来进行转折。对于$X_m$，如果`turnX`提供则它取值`turnX`, 其次如果`turnAt`提供，则取值`turnAt.x`, 再其次如果`turn`提供，则它在**起点的x**到**终点的x**之间，按照与`turn`成比例的那个`x`来取值。对于$Y_m$，如果`TurnY`提供则它取值`turnY`, 其次如果`turnAt`提供，则取值`turnAt.y`, 再其次如果`turn`提供，则它在**起点的x**到**终点的x**之间，按照与`turn`成比例的那个`y`来取值。


```wtht
draw:
  - b11
  - b22
  - wire; hvhv; b11.e; b22.s; turnx; p22.x + 100; turny; p22.y + 150; red
  - wire; hvhv; b11.nw; b22.sw; turnAt; p21; magenta;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkHVHV_1
[wtht]

- `vhvh`

`vhvh`类型为沿着起点**先竖直再水平再竖直再水平**以到达终点。它使用一个中间点$Y_m$和$X_m$来进行转折。对于$Y_m$，如果`TurnY`提供则它取值`turnY`, 其次如果`turnAt`提供，则取值`turnAt.y`, 再其次如果`turn`提供，则它在**起点的x**到**终点的x**之间，按照与`turn`成比例的那个`y`来取值。对于$X_m$，如果`turnX`提供则它取值`turnX`, 其次如果`turnAt`提供，则取值`turnAt.x`, 再其次如果`turn`提供，则它在**起点的x**到**终点的x**之间，按照与`turn`成比例的那个`x`来取值。

```wtht
draw:
  - b11
  - b22
  - wire; vhvh; b11.e; b22.s; turnx; p22.x + 100; turny; p22.y + 150; red
  - wire; vhvh; b11.nw; b22.sw; turnAt; p21; magenta;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkVHVH_1
[wtht]

- `hvhvh`

`hvhvh`类型为沿着起点**先水平再竖直再水平再竖直再水平**，同前面的类型类似，只是这里将使用三个数值$X_{m1}$，$Y_m$，$X_{m2}$来决定实际连线的走向。

$X_{m2}$将优先使用`turnAt.x`,如果`turnAt`未提供，则使用起点x到终点x之间**2/3**的位置。

$$
X_{m2} = \begin{cases}
  \text{turnAt.x} & \text{if turnAt exists} \\
  \frac{X_{start} + 2X_{end}}{3} & \text{if turnAt is not given}
\end{cases}
$$

$X_{m1}$优先使用`turnX`, 如果`turnX`未提供，则使用起点x到$X_{m2}$之间终点的位置。

$$
X_{m1} = \begin{cases}
  \text{turnX} & \text{if turnX exists} \\
  \frac{X_{start} + X_{m2}}{2} & \text{if turnX is not given}
\end{cases}
$$

$Y_m$优先使用`turnY`，如果`turnY`未提供，则使用起点y到终点y之间终点的位置


$$
Y_{m} = \begin{cases}
  \text{turnY} & \text{if turnY exists} \\
  \frac{Y_{start} + Y_{end}}{2} & \text{if turnY is not given}
\end{cases}
$$

下面是示例：

```wtht
draw:
  - b11
  - b22
  - wire; hvhvh; b11.ne; b22.se;
  - wire; hvhvh; b11.ne; b22.se; turnAt; p23; turnx; p11.x + 150; turny; p11.y + 110;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkHVHVH_1
[wtht]


- `vhvhv`

`hvhvh`类型为沿着起点**先竖直再水平再竖直再水平再竖直**，同前面的类型类似，只是这里将使用三个数值$Y_{m1}$，$X_m$，$Y_{m2}$来决定实际连线的走向。

$Y_{m2}$将优先使用`turnAt.y`,如果`turnAt`未提供，则使用起点y到终点y之间**2/3**的位置。

$$
Y_{m2} = \begin{cases}
  \text{turnAt.y} & \text{if turnAt exists} \\
  \frac{Y_{start} + 2Y_{end}}{3} & \text{if turnAt is not given}
\end{cases}
$$

$Y_{m1}$优先使用`turnY`, 如果`turnY`未提供，则使用起点y到$Y_{m2}$之间终点的位置。

$$
Y_{m1} = \begin{cases}
  \text{turnY} & \text{if turnY exists} \\
  \frac{Y_{start} + Y_{m2}}{2} & \text{if turnY is not given}
\end{cases}
$$

$X_m$优先使用`turnX`，如果`turnX`未提供，则使用起点x到终点x之间终点的位置


$$
X_{m} = \begin{cases}
  \text{turnX} & \text{if turnX exists} \\
  \frac{X_{start} + X_{end}}{2} & \text{if turnX is not given}
\end{cases}
$$


下面是示例：

```wtht
draw:
  - b11
  - b22
  # 与前面例子相比，仅改变连线类型: hvhvhv --> vhvhv
  - wire; vhvhv; b11.ne; b22.se;
  - wire; vhvhv; b11.ne; b22.se; turnAt; p23; turnx; p11.x + 150; turny; p11.y + 110;
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - blkVHVHV_1
[wtht]


[i] 请注意，绘制`h`,`v`交替类型的连线时，从出发点会按照H或V的顺序依次进行转弯，请根据你的实际需要选择相应类型并给定相应参数，同时，起点和终点的顺序也会有影响，起点和终点互换可能得到不同的连线。

## 添加箭头

下面是一个添加箭头的示例：

```wtht
draw: 
  # 添加终点箭头
  - wire; b11.s; b31.n; red;  endArrow;   arrow11
  # 添加起点箭头
  - wire; b11.e; b13.w; blue; startArrow; arrow1     
  # 起点和终点都添加箭头
  - wire; b11.se; b33.nw;     startArrow; arrow1; endArrow; arrow21
  - b11
  - b13
  - b31
  - b33
```

[wtht] # Fig Description...
noTitle: yes
use: port2portSource
figure:
  - wire; b11.s; b31.n; red;  endArrow;   arrow11     # 添加终点箭头
  - wire; b11.e; b13.w; blue; startArrow; arrow1      # 添加起点箭头
  # 起点和终点都添加箭头
  - wire; b11.se; b33.nw;  startArrow; arrow1; endArrow; arrow21
  - b11
  - b13
  - b31
  - b33
[wtht]

[i] 关于箭头的定义和使用，请参照[**线条标记**](./marker.md)章节。

## 流程图示例

下面是一个实用的示例，我们可以定义一个端点组件，和一个菱形分支组件，放置若干组件实例，并用合适的线将他们连接起来，即可得到一幅如下的流程图(代码中出现的`label`系在画图中添加文本的命令，请阅读[**文本标签**](./label.md)以获得详细说明和使用指引):

```wtht
# 定义组件
components:
  # 条件判断组件
  flowChart:
    inputVars:
      question: Am I Good?
      bodyWidth: 160
      bodyHeight: 80
      lineWidth: 4
      fill: white
    vars:
      rW: $bodyWidth
      rH: $bodyHeight
      labelW: rW / 2
      labelH: rH / 2
    points:
      pE:  rW / 2, 0
      pW: -rW / 2, 0
      pN: 0, -rH / 2
      pS: 0,  rH / 2

    shapes:
      rhombus: plg; pW; pS; pE; pN;

    draw:
      - rhombus; stroke ,$lineWidth, black; fill, $fill;
      - label; 0,0; anchor;center; w; labelW; h; labelH; ||| $question
      - label; pE; anchor; bl;  w; 40; h; 25; left;  ||| Yes
      - label; pW; anchor; br;  w; 40; h; 25; right; ||| No

    ports:
      - qin; pN; 270; $lineWidth * 8
      - yes; pE;   0; $lineWidth * 8
      - no;  pW; 180; $lineWidth * 8

  # 端点组件
  dotTerm:
    inputVars:
      id: A
      dSize: 40
      lineWidth: 4
      fill: yellow
      textColor: black
    vars:
      rCircle: $dSize / 2
      labelW: $dSize * 0.85
    points:
      p0: 0,  0

    draw:
      - circ; p0; rCircle ||| stroke, $lineWidth, black; fill, $fill  
      - label; 0,0; anchor;center; w; labelW; h; labelW;color; $textColor ||| $id

    ports:
      - term; -rCircle, 0; 180; $lineWidth * 8

# 画图主体
vars:
  xExit: 200
points:
  p0: 0,0
  pEntry: p0.x, p0.y - 80
  pCond1: p0.x, p0.y + 100
  pExitA: xExit, pCond1.y
  pCond2: pCond1.x - 150, pCond1.y + 150
  pExitA2: xExit, pCond2.y
  pCondBig: pCond2.x - 150, pCond2.y + 150
  pExitA3: xExit, pCondBig.y
  pDieEarly: pCondBig.x - 160, pCondBig.y + 120
  pExitLegend: pExitA3.x - 200, pDieEarly.y

# 组件放置
placements:
  cond1:
    comp: flowChart
    at: pCond1
    $question: Eat today? 
  cond2:
    comp: flowChart
    location: pCond2
    $question: Eat tomorrow?
  condBig:
    comp: flowChart
    location: pCondBig
    $question: Eat Everyday?
    $bodyWidth: 200
    $bodyHeight: 100
    $fill: #eee
  entry: dotTerm; pEntry; rotdeg; -90; $id; Born; $dSize; 80; $fill; darkgreen; $textColor; white;
  exitA:  dotTerm; pExitA;  $id; A
  exitA2: dotTerm; pExitA2; $id; A
  exitA3: dotTerm; pExitA3; $id; A
  exitLegend: dotTerm; pExitLegend; $id; A
  exitDieEarly: dotTerm; pDieEarly; rotdeg; 90; $id; Die Early; $dSize; 80; $fill; darkgrey;
# 绘制内容
draw:
  - wire; entry.term;  cond1.qin;   endarrow; arrow1;
  - wire; cond1.yes;   exitA.term;  endarrow; arrow1;
  - wire; cond1.no;    cond2.qin;   endarrow; arrow1;
  - wire; cond2.yes;   exitA2.term; endarrow; arrow1;
  - wire; cond2.no;    condBig.qin; endarrow; arrow1;
  - wire; condBig.yes; exitA3.term; endarrow; arrow1;
  - exitLegend
  - label; pExitLegend; offset; 40, 0; anchor; ml; w; 100; h; 20;  align; left; ||| Live happily
  - wire; condBig.no; exitDieEarly.term;endarrow; arrow1;
  - entry
  - cond1
  - exitA
  - cond2
  - exitA2
  - condBig
  - exitA3
  - exitDieEarly
  - rect; pDieEarly.x - 18, pEntry.y - 15; pDieEarly.x -10, pEntry.y + 25; ||| fill, darkgreen;
  - label; pDieEarly.x + 8, pEntry.y + 5; anchor; ml; align; left; w; 360; h; 40; font; times; bold; fs; 36 ||| Flow Chart Example
```

[wtht] # Fig Description...
noTitle: yes

# 定义组件
components:
  # 条件判断组件
  flowChart:
    inputVars:
      question: Am I Good?
      bodyWidth: 160
      bodyHeight: 80
      lineWidth: 4
      fill: white
    vars:
      rW: $bodyWidth
      rH: $bodyHeight
      labelW: rW / 2
      labelH: rH / 2
    points:
      pE:  rW / 2, 0
      pW: -rW / 2, 0
      pN: 0, -rH / 2
      pS: 0,  rH / 2

    shapes:
      rhombus: plg; pW; pS; pE; pN;

    draw:
      - rhombus; stroke ,$lineWidth, black; fill, $fill;
      - label; 0,0; anchor;center; w; labelW; h; labelH; ||| $question
      - label; pE; anchor; bl;  w; 40; h; 25; left;  ||| Yes
      - label; pW; anchor; br;  w; 40; h; 25; right; ||| No

    ports:
      - qin; pN; 270; $lineWidth * 8
      - yes; pE;   0; $lineWidth * 8
      - no;  pW; 180; $lineWidth * 8
  # 端点组件
  dotTerm:
    inputVars:
      id: A
      dSize: 40
      lineWidth: 4
      fill: yellow
      textColor: black
    vars:
      rCircle: $dSize / 2
      labelW: $dSize * 0.85
    points:
      p0: 0,  0

    draw:
      - circ; p0; rCircle ||| stroke, $lineWidth, black; fill, $fill  
      - label; 0,0; anchor;center; w; labelW; h; labelW;color; $textColor ||| $id

    ports:
      - term; -rCircle, 0; 180; $lineWidth * 8

# 画图主体
vars:
  xExit: 200
points:
  p0: 0,0
  pEntry: p0.x, p0.y - 80
  pCond1: p0.x, p0.y + 100
  pExitA: xExit, pCond1.y
  pCond2: pCond1.x - 150, pCond1.y + 150
  pExitA2: xExit, pCond2.y
  pCondBig: pCond2.x - 150, pCond2.y + 150
  pExitA3: xExit, pCondBig.y
  pDieEarly: pCondBig.x - 160, pCondBig.y + 120
  pExitLegend: pExitA3.x - 200, pDieEarly.y

# 组件放置
placements:
  cond1:
    comp: flowChart
    at: pCond1
    $question: Eat today? 
  cond2:
    comp: flowChart
    location: pCond2
    $question: Eat tomorrow?
  condBig:
    comp: flowChart
    location: pCondBig
    $question: Eat Everyday?
    $bodyWidth: 200
    $bodyHeight: 100
    $fill: #eee
  entry: dotTerm; pEntry; rotdeg; -90; $id; Born; $dSize; 80; $fill; darkgreen; $textColor; white;
  exitA:  dotTerm; pExitA;  $id; A
  exitA2: dotTerm; pExitA2; $id; A
  exitA3: dotTerm; pExitA3; $id; A
  exitLegend: dotTerm; pExitLegend; $id; A
  exitDieEarly: dotTerm; pDieEarly; rotdeg; 90; $id; Die Early; $dSize; 80; $fill; darkgrey;
# 绘制内容
draw:
  - wire; entry.term;  cond1.qin;   endarrow; arrow1;
  - wire; cond1.yes;   exitA.term;  endarrow; arrow1;
  - wire; cond1.no;    cond2.qin;   endarrow; arrow1;
  - wire; cond2.yes;   exitA2.term; endarrow; arrow1;
  - wire; cond2.no;    condBig.qin; endarrow; arrow1;
  - wire; condBig.yes; exitA3.term; endarrow; arrow1;
  - exitLegend
  - label; pExitLegend; offset; 40, 0; anchor; ml; w; 100; h; 20;  align; left; ||| Live happily
  - wire; condBig.no; exitDieEarly.term;endarrow; arrow1;
  - entry
  - cond1
  - exitA
  - cond2
  - exitA2
  - condBig
  - exitA3
  - exitDieEarly
  - rect; pDieEarly.x - 18, pEntry.y - 15; pDieEarly.x -10, pEntry.y + 25; ||| fill, darkgreen;
  - label; pDieEarly.x + 8, pEntry.y + 5; anchor; ml; align; left; w; 360; h; 40; font; times; bold; fs; 36 ||| Flow Chart Example
[wtht]
