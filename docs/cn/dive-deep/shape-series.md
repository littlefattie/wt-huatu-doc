# 图形序列

按照定义[基本图形](./shapes.md)的方法，我们把相应位置的参数换成**点序列**，或者**数列**（离散列，或者等差列），那么我们将得到图形序列。我们可以为这些图形序列设定样式并绘制。

[i] 请注意，我们可以把所需要的每个参数都替换成序列，然后最终图形序列中图形的个数取决于参数序列中包含元素最少的那个。当某个参数序列只含有一个元素时，它将被复制扩展至与其他参数序列等长。

## 线段序列

[i] 请注意，因为直线是无限长度的，我们无法绘制出来，所以在图形序列里，我们放弃定义直线序列，请定义线段序列，并在最终的绘制中引用它。

同定义基本图形[线段](./shapes.md#线段)时类似，我们用关键字`line`(或简写为`ln`)，或`lineseg`(或简写为`ls`)来定义线段序列，这里仍然提供类似的关键字，只是为了方便，但请注意，你将不能使用`line`,`ln`,`lineseg`,`ls`等关键字来定义多线段，定义多线段序列请使用`polyline`(或简写为`pln`)关键字，详情请查看后文叙述。

定义线段序列有两种方式：

1. 先定义出“直线序列”。第一个参数是点序列，或者一个点，为锚点的集合，第二个参数为一个数列，或者一个数，为角度的集合，如果只提供了一个点和一个数字，则只得到一条直线，也就是这个直线序列中仅有一条直线。在直线序列之后，我们添加第三个参数来从直线序列上截取长度，我们也可以提供第四个参数，这样线段的两端都可以通过参数来确定。

```wtht
shapeSeries:
  # ps1 是一个点序列，degSeries1是一个表示角度的数列，lSeries是一个表示长度的数列
  ssLn1: line; ps1; degSeries1; lSeries
  # ps2 是一个点序列，degSeries1是一个表示角度的序列
  # l1Series和l2Series是表示长度的两个数列
  ssLn2: line; ps2; degSeries1; l1Series; l2Series
```

下面是一些实例：

```wtht
points:
  p0: 0,0

shapes:
  c1: circ; p0; 60;
pointSeries:
  psFromC1: c1; 0: 60: 350

shapeSeries:
  # 固定锚点，角度，两端长度L1，L2都是数列
  ssLs1:  line; p0; 20:15:170; -80:10:0; 20:18:300
  # 固定锚点，角度是数列，L1固定不变，L2都是数列
  ssLs2:  line; p0; 20:15:170; -60; 30:22:340;
  # 锚点使用点序列，角度，两端长度L1，L2都是固定值
  ssLsC1: line; psFromC1; -30; -30; 30;
  # 锚点使用点序列，角度是数列，只有一个长度L，是数列
  ssLsC2: line; psFromC1; -30: 10: 30; -30:5:0;
  # 锚点使用点序列，角度和长度L1是数列，L2是固定值
  ssLsC3: line; psFromC1; -30: 10: 30; -30:5:0; 30;
  # 锚点使用点序列，角度是固定值，两端长度L1，L2都是数列
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
    - label; p0.x, p0.y - 65; anchor; bm; w; 140; h; 26; bdrcolor; black; ||| $L_1$减小，$L_2$增大
  blkL2:
    - ssLs2; stroke, 1, blue;
    - label; p0.x-32, p0.y - 65; anchor; bm; w; 140; h; 26; bdrcolor; black; ||| $L_1$不变，$L_2$增大
  refCircle:
    - c1; stroke, 1, grey; fill, none;
  blkC1:
    - refCircle
    - psFromC1; 4; red; 
    - ssLsC1; stroke,1, blue;
    - label; p0.x, p0.y - 75; anchor; bm; w; 200; h;48; bdrcolor; black; ||| 多锚点，单一角度，两端长度相同且不变
  blkC2:    
    - refCircle
    - psFromC1; 4; red; 
    - ssLsC2; stroke,1, blue;
    - label; p0.x, p0.y - 75; anchor; bm; w; 200; h; 48; bdrcolor; black; ||| 多锚点，角度变，只有一个长度$L$，且变化
  blkC3:
    - refCircle
    - psFromC1; 4; red; 
    - ssLsC3; stroke,1, blue;
    - label; p0.x, p0.y - 80; anchor; bm; w; 200; h; 48; bdrcolor; black; ||| 多锚点，角度变，$L_1$减小，$L_2$不变
  blkC4:
    - refCircle
    - psFromC1; 4; red; 
    - ssLsC4; stroke,1, blue;
    - label; p0.x, p0.y - 80; anchor; bm; w; 200; h; 48; bdrcolor; black; ||| 多锚点，角度不变，$L_1$减小，$L_2$增大

draw:  
  - blkL1;
  - blkL2; move; 340, 0;
  - blkC1; move;0, 290
  - blkC2; move;300, 290
  - blkC3; move;0, 500
  - blkC4; move;300, 500
[wtht]



2. 我们可以提供两个点序列作为线段的两个端点，然后提供一个关键字`zip`（或`pair`），`cross`(或`interleave`)来表示将这两个点序列如何连成线段，`zip`表示起点点序列中的第一个点对终点点序列中的第一个点，依此类推；`cross`表示起点点序列中的每一个点都和终点点序列中的每个点都构成一条线段，是相乘的关系。如果起点点序列中的个数为$M$，终点点序列中的点的个数为$N$，那么`zip`方式将得到一个$\min(M,N)$条线段构成的序列，而`cross`方式将得到$M \times N$条线段构成的序列。


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
  # 从ln2上取点，t 从小到大
  ps21: ln2; 0.2 : 0.1: 1
  # 从ln2上取点，t 从大到小，与ps21不同
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


## 圆序列

定义圆序列时，使用与定义[圆](./shapes.md#圆)相同的关键字`circle`(或简写为`circ`)，只需要把圆心和半径都换成序列即可。

```wtht
shapes:
  c1: circ; 0,0; 120;
pointSeries:
  psC1: c1; 0: 25: 280;
  psRandom: c1.25; c1.140; c1.230; c1.c; -50, c1.r / 2;
shapeSeries:
  ssC1: circ; psC1; 2: 1: 10; # 图形总数取决于数量最少的那个序列
  ssC2: circ; psC1; 4;        # 图形总数取决于psC1的点的个数，
                              # 如果序列中只有一个数，将复制扩展为
                              # 与其他参数序列中个数最少的那个一样多
  # 圆心不变，半径是序列
  ssC3: circ; c1.c; c1.r / 2 : c1.r/8 : c1.r
  # 圆心是点列，半径是数列
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
  ssC1: circ; psC1; 2: 1: 10; # 图形总数取决于数量最少的那个序列
  ssC2: circ; psC1; 4;        # 图形总数取决于psC1的点的个数，
                              # 如果序列中只有一个数，将复制扩展为
                              # 与其他参数序列中个数最少的那个一样多
  ssC3: circ; c1.c; c1.r / 2: c1.r/8: c1.r
  ssC4: circ; psRandom; 4, 15, 7, 12, 6

blocks:
  blkC1:
    - c1; stroke, 1, black; dash, 3,3; fill, none;
    - ssC1; fill, red;
    - label; c1.c; w; 150; h; 40; anchor; center; ||| 圆心是序列，半径也是序列，不断变大
  blkC2:
    - c1; stroke, 1, black; dash, 3,3; fill, none;
    - ssC2; fill, red;
    - label; c1.c; w; 150; h; 40; anchor; center; ||| 圆心是序列，半径不变，是恒定值
  blkC3:
    - ssC3; fill, none; stroke, 1, red;
    - label; c1.c; w; 70; h;50; anchor; center; ||| 圆心不变，半径是序列
  blkC4:
    - c1; stroke,1, black; dash, 3,3; fill, none;
    - ssC4; fill, royalblue, 0.6;
    - label; 0, -c1.r/2.2; w; 140; h;50; anchor; center; ||| 圆心是离散的点序列，半径是离散的数列
draw:
  - blkC1
  - blkC2; move; 260, 0;
  - blkC3; move; 0, 260;
  - blkC4; move; 260, 260;
[wtht]


## 椭圆序列

定义椭圆序列时，使用与定义[椭圆](./shapes.md#椭圆)相同的关键字`ellipse`(或简写为`ell`)，只需要把圆心，半径和旋转角都换成序列即可。

```wtht
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

## 矩形序列

定义矩形序列时，使用与定义[矩形](./shapes.md#矩形)相同的关键字`rectangle`(或简写为`rect`)。一共有三种定义方式。
1. 给定对角点，都可以是点列
2. 给定中心点和宽高，中心点可以是点列，宽高可以是数列
3. 给定左上角定位点和宽高，定位点可以是点列，宽高可以是数列

```wtht
shapeSeries:
  # 对角点
  ssRect1: rect; psNW; psSE;
  # 中心点 + 宽高
  ssRect2: rect; bycenter; wSeries; hSeries;
  # 左上角 + 宽高
  ssRect3: rect; topLeftCornerSeries; wSeries; hSeries;
```
下面是一些具体的例子：

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
  # 通过对角来定义矩形序列
  ssLs1: rect; psLs1; psLs2;
  # 通过左上角和宽高来定义矩形序列
  ssLs3: rect; psLs3; 100:10:10; 60: 5: 20;
  # 通过矩形中心点和宽高来定义序列
  ssLsC: rect; bycenter; p0; 160:20:80; 80:20:160;
  # 中心点和宽高都是序列
  ssRectOnArc: rect; bycenter; psArc1; 160:20:80; 80:20:160;
  # 左上角固定，宽高是数列
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
  # 通过对角来定义矩形序列
  ssLs1: rect; psLs1; psLs2;
  # 通过左上角和宽高来定义矩形序列
  ssLs3: rect; psLs3; 100:10:10; 60: 5: 20;
  # 通过矩形中心点和宽高来定义序列
  ssLsC: rect; bycenter; p0; 160:20:80; 80:20:160;
  # 中心点和宽高都是序列
  ssRectOnArc: rect; bycenter; psArc1; 160:20:80; 80:20:160;
  # 左上角固定，宽高是数列
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
    - label; psLs1[5]; w; 180; h; 40; anchor; ml; offset; -20, -80; left;  ||| 通过对角点来定义矩形序列，对角点都是点列
    - label; psLs3[1]; w; 160; h; 52; anchor; bl; offset; 30, -10; left;  ||| 通过左上角和宽高来定义矩形序列，左上角是点列，宽高是数列
  blkRect2:
    - ssLsC;fill, magenta; 0.4; stroke, 1, black;
    - circ; p0; 3; ||| fill, black;
    - arc1; stroke, 1, black; dash, 3,3; fill, none;
    - psArc1; 3; black;
    - ssRectOnArc; fill, magenta, 0.4; stroke, 1, black;
    - label; p0.x + 100, p0.y; w; 180; h; 40; anchor; ml; ||| bycenter, 中心点固定，宽高都是数列
    - label; psArc1[3]; w; 180; h; 40; anchor; tm; offset; 0, 80; ||| bycenter, 中心点是点列，宽高都是数列
draw:
  - blkRect1
  - blkRect2;  move; -50, 420
  - ssRect1; fill, magenta, 0.4; stroke, 1, black; move; -350, 380
  - label; p0.x -350 - 10, p0.y + 380 - 50; w; 120; h; 40; ||| 左上角固定，宽高是数列
  
[wtht]

## 三角形序列

定义三角形序列时，使用与定义[三角形](./shapes.md#三角形)相同的关键字`triangle`(或简写为`tri`)。定义三角形序列只使用一种方式，即给定三个点的序列即可。

```wtht
shapeSeries:
  ssTri: triangle; psPointA; psPointB; psPointC
```
下面是一个具体的例子：

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


## 多线段序列

定义多线段序列时，使用与定义[多线段](./shapes.md#多线段)相同的关键字`polyline`(或简写为`pline`或`pln`)。定义多线段序列只是用一种方式，依次给出多线段的各个点即可，单个点或者点序列。当提供的是单个点，则序列中所有的多线段都将经过这个点，如果每个点都是点序列（点的个数$\geqslant 2$），则序列中多线段的个数取决于点序列中个数最少的那个。

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
    - label; psLs4[1]; offset; -10, -h/4; w; 200; h; 50; align; right; anchor; mr; ||| 当某个点是单个点时，序列中所有多线段都经过这个点
  blkSS2:
    - blkCoordinates
    - blkLines;
    - ssPolyLines2; stroke, 1, green; fill, none;  
    - label; psLs4[1]; offset; -10, -h/4; w; 200; h; 50; align; right; anchor; mr; ||| 都是点序列时，多线段的个数取决于点序列中点个数最少的那个
draw:
  - blkSS1
  - blkSS2;move; 0, h;
[wtht]


## 多边形序列

定义多边形序列时，使用与定义[多边形](./shapes.md#多边形)相同的关键字`polygon`(或简写为`plg`)。定义多边形序列只是用一种方式，依次给出多边形的各个顶点即可，单个点或者点序列。其构成方式同[多线段](#多线段序列)类似。

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
    - label; 0,0; w; 80; h; 40; anchor; center ||| 所有顶点都是序列
  polygons2:
    - refLines;
    - ssPlg2; fill, none; stroke, 1, black;
    - label; 0,0; w; 80; h; 40; anchor; center ||| 有一个顶点是固定点
  polygons3:
    - refLines;
    - ssPlg3; fill, none; stroke, 1, black;
    - label; 0,0; w; 80; h; 40; anchor; center ||| 顶点个数不相等

draw:
  - polygons;
  - polygons2; move; R * 3, 0;
  - polygons3; move; R * 3 / 2, R * 3;
[wtht]


## 正多边形序列

定义正多边形序列时，使用与定义[正多边形](./shapes.md#正多边形)相同的关键字`npolygon`(或简写为`plgn`)。定义多边形序列只是用一种方式，一个给出各个参数，可以是单个数或点，也可以是数列或点列。

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
    - label; 0, -w/7; anchor; bm; w; 160; h; 40; ||| 所有参数都是序列
  blkNplg2:
    - ssNplg2; fill, royalblue, 0.3; stroke, 1, black;
    - label; 0, -w/6; anchor; bm; w; 160; h; 40;  ||| 中心点固定，其他参数都是序列
  blkNplg3:
    - ssNplg3; fill, silver, 0.3; stroke, 1, black;
    - label; 0, -w/5; anchor; bm; w; 160; h; 40; ||| 中心点固定，边数固定(正七边形)
draw:
  - blkNplg1;
  - blkNplg2; move; -w/4, w/2.2;
  - blkNplg3; move;  w/4, w/2.2;
[wtht]

## Bezier曲线序列

定义Bezier曲线序列时，使用与定义[二次Bezier曲线](./shapes.md#二次Bezier曲线)使用的关键字`bezier2`(或简写为`b2`)或定义[三次Bezier曲线](./shapes.md#三次Bezier曲线)使用的关键字`bezier3`(或简写为`b3`)。定义时只需将相应的点替换成点序列即可。

- 二次Bezier曲线序列

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
    - label; 0, w/2; anchor; tm; w; 160; h; 50; ||| 起点和终点固定，控制点是点列
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
    - label; 0, w/2; anchor; tm; w; 160; h; 50; ||| 起点固定，控制点和终点是点列

draw:
  - blkB2;
  - blkB2a; move; w*1.1, 0;

[wtht]


- 三次Bezier曲线序列

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
    - label; 0, w/1.7; anchor; tm; w; 160; h; 50; ||| 起点和终点固定，两个控制点是点列
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
    - label; 0, w/1.7; anchor; tm; w; 160; h; 50; ||| 起点，终点和控制点都是是点列

draw:
  - blkB3;
  - blkB3a; move; w*1.4, 0;

[wtht]


## 弧线序列

定义弧线形序列时，使用与定义[弧线](./shapes.md#弧线)相同的关键字`arc`。请注意，我们只能从[圆序列](#圆序列)或者[椭圆序列](#椭圆序列)定义圆弧序列。我们先定义圆序列，或者椭圆序列，然后指定起始角和终到角，以及沿(椭)圆周的行走方向，顺时针(`cw`)或逆时针(`ccw`)，即:

```wtht
shapeSeries:
  ssCircle: circle; psCenter; Rmin | Rstep | Rmax;
  ssEllipse: ellipse; psCenter; 
    RxMin | RxStep | RxMax; 
    RyMin | RyStep | RyMax; 
    rotDegMin | rotDegStep | rotDegMax;
  ssArc1: arc; ssCircle; 
    degStart | degStartStep | degStartMax;
    degEnd | degEndStep | degEndMax;
    cw;  # cw and ccw are rotating directions
  ssArc2: arc; ssEllipse;
    degStart | degStartStep | degStartMax;
    degEnd | degEndStep | degEndMax;
    cw;  # cw and ccw are rotating directions
```

下面是一些具体的例子：

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

