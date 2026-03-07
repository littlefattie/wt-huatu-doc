# 图形变换

当我们设计或定义好一个图形以后，我们会很自然地产生这样的需求，即把它移到另外一个地方(**平移**)或者绕着某个点进行一个角度的旋转(**旋转**)。比如我想设计一幅图案，其中有6个五角星，均匀分布在一个圆周上，然后他们的顶角都是指向这个圆周的圆心，那么我们可以只定义出一个五角星，然后通过平移和旋转来复制创建出其他的五角星。

[i] 在这里，我们讨论的平移和旋转是通过平移和旋转操作对目标图形进行**复制**从而产生新的图形。它和我们通常定义图形的方法类似，区别是我们不指定图形的类型，而是使用特定的关键字来说明**如何操作**。

## 平移

关于平移，我们定义了三种平移方式，即**沿X轴平移**，**沿Y轴平移**，**给定XY偏移的平移**，区别在于定义平移时所使用的关键字和给定的参数不同。具体的使用方式参考如下的示例代码。

```wtht
shapes:
  shape1: polygon; ... # 其他你定义图形需要的参数
  # 使用 moveX 关键字来定义 沿X轴平移， 给出你要平移的目标图形和偏移量
  shape1MoveX:   moveX; shape1; xOffset
  # 使用 moveY 关键字来定义 沿Y轴平移， 给出你要平移的目标图形和偏移量
  shape1MoveY:   moveY; shape1; yOffset
  # 使用 moveby 关键字(或 move) 来定义 给定XY偏移的平移，给出你要平移的目标
  # 和x，y方向的偏移量，他们可以按照通常定义点的方式给出。
  shape1MoveBy:  move;   shape1; offsetX, offsetY
  shape1MoveBy2: moveby; shape1; offsetX, offsetY
```

## 旋转

当通过旋转的方式产生新图形时，我们使用`rotateby`(或`rotate`)关键字来标示，需要给定被旋转的目标图形，旋转的角度，以及一个可选的旋转中心，如果旋转中心没有指定的话将默认绕着坐标系原点`(0, 0)`来旋转。

```wtht
points:
  pRotateCenter: ...   # 旋转中心
shapes:
  shape1: polygon; ... # 其他你定义图形需要的参数
  # 使用 rotateby 关键字来定义 旋转， 给出你要旋转的角度，和旋转中心
  shape1rotate:  rotateby; shape1; degOfRotate; pRotateCenter
  # 使用 rotate 关键字也是一样的， 若未指定旋转中心，将围绕(0,0)旋转
  shape1rotate2: rotate; shape1; degOfRotate
```

[i] 请注意，旋转操作会改变图形的类别，比如矩形经过旋转以后，虽然还是矩形的样子，但它会变成一个多边形，当我们使用`rectXXX.nw`等来进行快捷取点时就会失效，我们需要使用`rectXXXrotated[1]`等来进行取点。
```wtht
shapes:
  rect1: rect; (0,0); (100,100);
  rect1rotated: rotate; rect1; 45; (30,30)
  circ1: circ; rect1.nw; 5
  # 这样写不行
  circ2: circ; rect1rotated.nw; 5
  # 这样写可以
  circ2: circ; rect1rotated[1]; 5
```

## 实例

下面是一个例子，我们可以通过平移和旋转来实现开头所描述的6个五角星。

```wtht
vars:
  # 五角星外接圆的半径
  rStar: 80 
  # 计算出五角星内凹五个点所在圆的半径
  rStar2: rStar * dcos(72) / dcos(36)   
  rCenters: 4 * rStar
points:
  p0: 0,0

shapes:
  # 定义参考圆
  cStar:  circ; p0; rStar;
  cStar2: circ; p0; rStar2;
  cCenters: circ; p0; rCenters;

pointSeries:
  # 五角星外顶点
  psStar:  cStar; 270 : -72 : 270 - 4 * 72
  # 五角星内顶点
  psStar2: cStar2; 270 - 36 : -72 : 270 - 4 * 72 -36
  # 圆周上分布的五角星的中心点
  psCenters: cCenters; 0: 360 / 6 : 360

shapes2:
  # 制作五角星，是个多边形，外顶点和内顶点交替
  star: plg; psStar[1]; psStar2[1]; psStar[2]; psStar2[2];
        psStar[3]; psStar2[3]; psStar[4]; psStar2[4];
        psStar[5]; psStar2[5];
  # X方向平移
  starMoveX: movex; star; rCenters / 2
  # Y方向平移
  starMoveY: movey; star; -rCenters * 1.2
  # moveby: XY都有提供的平移
  # 请注意，这里的 psCenters[n] 是以点的形式标识，但它实际上应为偏移值，
  # 因为我们的中心参考点是(0,0), 位移就等于坐标值
  starMove1: move; star; psCenters[1]
  starMove2: move; star; psCenters[2]
  starMove3: move; star; psCenters[3]
  starMove4: move; star; psCenters[4]
  starMove5: move; star; psCenters[5]
  starMove6: move; star; psCenters[6]
  # rotate: 平移之后，以新的中心点为中心 旋转
  # 请注意这里的旋转中心，是各个五角星的中心点
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
  # 五角星外接圆的半径
  rStar: 80 
  # 计算出五角星内凹五个点所在圆的半径
  rStar2: rStar * dcos(72) / dcos(36)   
  rCenters: 4 * rStar
points:
  p0: 0,0

shapes:
  # 定义参考圆
  cStar:  circ; p0; rStar;
  cStar2: circ; p0; rStar2;
  cCenters: circ; p0; rCenters;

pointSeries:
  # 五角星外顶点
  psStar:  cStar; 270 : -72 : 270 - 4 * 72
  # 五角星内顶点
  psStar2: cStar2; 270 - 36 : -72 : 270 - 4 * 72 -36
  # 圆周上分布的五角星的中心点
  psCenters: cCenters; 0: 360 / 6 : 360

shapes2:
  # 制作五角星，是个多边形，外顶点和内顶点交替
  star: plg; psStar[1]; psStar2[1]; psStar[2]; psStar2[2];
        psStar[3]; psStar2[3]; psStar[4]; psStar2[4];
        psStar[5]; psStar2[5];
  # X方向平移
  starMoveX: movex; star; rCenters / 2
  # Y方向平移
  starMoveY: movey; star; -rCenters * 1.2
  # moveby: XY都有提供的平移
  # 请注意，这里的 psCenters[n] 是以点的形式标识，因为我们的中心参考点是(0,0), 
  #        位移就等于坐标值
  starMove1: move; star; psCenters[1]
  starMove2: move; star; psCenters[2]
  starMove3: move; star; psCenters[3]
  starMove4: move; star; psCenters[4]
  starMove5: move; star; psCenters[5]
  starMove6: move; star; psCenters[6]
  # rotate: 平移之后，以新的中心点为中心 旋转
  # 请注意这里的旋转中心，是各个五角星的中心点
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



画出来大概是如下的样子：

1. 单个五角星和分别沿X方向和Y方向平移的结果。外圆供参考。


[wtht] # Example of the 6-star
use: SixStarOnCircle
noTitle: yes

figure: draw
[wtht] 


2. XY方向都有偏移的平移，将五角星移动到圆周上均分的6个点。请注意这里没有平移。


[wtht] # Example of the 6-star
use: SixStarOnCircle
noTitle: yes

figure: moveby

[wtht] 



3. 在圆周上特定位置绕着各自中心旋转，使五角星的正北顶点指向圆心。


[wtht] # Example of the 6-star
use: SixStarOnCircle
noTitle: yes

figure: rotate

[wtht] 


[i] 你可能还会想这个五角星是不是可以放大或缩小。请别担心，我们有方法可以实现这个目标，但将会是其他章节或功能模块所讨论的问题，这里只介绍有关平移和旋转相关的部分。

[i] 关于正五角星如何计算及绘制的，可以参考如下的图示。

[wtht] # how to draw a 5-angle star

noTitle: yes

vars:
  # 五角星外接圆的半径
  rStar: 300 
  # 计算出五角星内凹五个点所在圆的半径
  rStar2: rStar * dcos(72) / dcos(36)   
  rCenters: 4 * rStar
points:
  p0: 0,0

shapes:
  # 定义参考圆
  cStar:  circ; p0; rStar;
  cStar2: circ; p0; rStar2;

pointSeries:
  # 五角星外顶点
  psStar:  cStar; 270 : -72 : 270 - 4 * 72
  # 五角星内顶点
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
