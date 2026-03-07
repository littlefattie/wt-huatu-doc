# 长度标记

为了在画示意图时方便地标记长度，我们设计了一个简便功能，可以给定一些参数后，直接画一个长度标记出来。比如下图的样子，我们为两个点之间的长度做一个标记。标记由平行于起点终点连线的一根**中间横线**和两端的两根**竖线**组成。

[wtht] # Source of the DIM MARK
asSource: true
name: dimmarkSource

vars:
  dX: 400
  dY: 200
  gapForDemo: 100
  deg1: datan(dY/dX)
  deg2: 90 - deg1
  barLenForDemo: 80
  lwForMark: 200
  lhForMark: 50
points:
  pFrom: 0,0
  pTo: pFrom.x + dX, pFrom.y - dY
shapes:
  lnFromTo: line; pFrom; pTo
  lnpFrom: line; pFrom; lnFromTo.deg + 90
  lnpTo: line; pTo; lnFromTo.deg + 90;
cpoints:
  cpLnpFromRight: lnpFrom;  gapForDemo
  cpLnpFromLeft:  lnpFrom; -gapForDemo
  cpLnpToRight:   lnpTo;    gapForDemo
  cpLnpToRight2:  lnpTo;    gapForDemo + barLenForDemo
  cpLnpToRight3:  lnpTo;    gapForDemo + barLenForDemo * 0.76

  cpLnpToLeft:    lnpTo;   -gapForDemo
  cpLnMiddle: lnFromTo; 0.5
  cpLsMarkFrom:   lnpFrom; 6 + (barLenForDemo/2) * 0.5
  cpLsMarkTo:     lnpTo;   6 + (barLenForDemo/2) * 0.5
shapes2:
  lsMark: line; cpLsMarkFrom; cpLsMarkTo
cpoints2:
  cpLbl1: lsMark; 0.5 - lwForMark / lsMark.len / 2
  cpLbl2: lsMark; 0.5 + lwForMark / lsMark.len / 2
shapes3:
  lnLbl1: line; cpLbl1; lnFromTo.deg + 90
  lnLbl2: line; cpLbl2; lnFromTo.deg + 90
cpoints3:
  cpLbl1a: lnLbl1; lhForMark
  cpLbl2a: lnLbl2; lhForMark

blocks:
  base:
    - circ; pFrom; 5; ||| fill, 0.5, maroon
    - circ; pTo;   5; ||| fill, 0.5, maroon
  blkSimple:
    - base;
    - line; pFrom; pTo; ||| stroke,2, black;
    - dimmark; pFrom; pTo; label; $L$; gap; 8; 
  blkGeneric:
    - base
    - circ; pFrom; 1; ||| fill, black
    - circ; pTo;   1; ||| fill, black
    - dimmark; pFrom; pTo; label; $\text{label text}$; lw; 200; lh; 30; linecolor; red; linewidth; 2; barwidth; 1; barcolor; blue; textcolor; magenta;
    - line; pFrom.x + 30, pFrom.y - 50; pFrom.x + 2, pFrom.y - 5;  ||| stroke,1, black; marker; arrow1;
    - label; pFrom.x + 10, pFrom.y - 53; anchor; bl; w; 100; h; 20; align;left; |||  Start Point
    - line; pTo.x - 60, pTo.y; pTo.x - 6, pTo.y; ||| stroke,1, black; marker; arrow1
    - label; pTo.x - 66, pTo.y; anchor; mr; w; 100; h; 20; align;right; ||| End Point
    - line; cpLnMiddle.x - 40, cpLnMiddle.y - 20; cpLnMiddle.x, cpLnMiddle.y + 4; ||| stroke,1, black; marker; arrow1;
    - label; cpLnMiddle.x - 65, cpLnMiddle.y - 24; anchor; bl; w; 60; h; 20; ||| Line
    - label; lnpFrom.30; anchor;tl; align;left; w; 70; ||| Start Bar
    - label; lnpTo.30; anchor;tl; align;left; w; 70; h; 20; ||| End Bar
    - label; pTo.x + 40, pFrom.y - 45; anchor; mr; align; right; ||| lineColor =  red
    - label; pTo.x + 40, pFrom.y - 20; anchor; mr; align; right; ||| barColor =  blue
  blkShowGap:
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5
    - circ; pFrom; 1; ||| fill, red;
    - circ; pTo;   1; ||| fill, red;
    - circ; cpLnpFromRight; 1; ||| fill, red;
    - circ; cpLnpToRight;   1; ||| fill, red;
    - dimmark; pFrom; pTo; label; $\text{label text}$; lw; 200; lh; 30; gap; gapForDemo;
    - base
    - dimmark; pFrom; cpLnpFromRight; label; $\text{gap}$; gap; 6;
  blkShowBarLen:
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5
    - circ; pFrom; 1; ||| fill, red;
    - circ; pTo;   1; ||| fill, red;
    - circ; cpLnpFromRight; 1; ||| fill, red;
    - circ; cpLnpToRight;   1; ||| fill, red;
    - dimmark; pFrom; pTo; label; $\text{label text}$; lw; 200; lh; 30; gap; gapForDemo;barlen; barLenForDemo; linepos; 0.76; linewidth; 2;linecolor; red;
    - base
    - dimmark; pFrom; cpLnpFromRight; label; $\text{gap}$; gap; 6;
    - dimmark; cpLnpToRight2; cpLnpToRight; label; $\text{barlen}$; gap; 6; barlen; 60; linepos; 0.9
    - dimmark;cpLnpToRight3; cpLnpToRight;  label; $\text{barlen * linePos}$; gap; 6;  lw; 120; remotepos; pTo.x + 90, pTo.y + 30;noEndbar;
  blkShowLength:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - circ; pFrom; 1; ||| fill, red;
    - circ; pTo;   1; ||| fill, red;
    - dimmark; pFrom; pTo; label; $\text{label text has some}$; lw; 200; lh; 30; barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; arrow; simple; rotatelabel;
    - dimmark; pFrom; pTo; lw; 200; lh; 30; gap; gapForDemo/2; barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; arrow; simple; rotatelabel; showlength;
    - dimmark; pFrom; pTo; lw; 200; lh; 30; gap; gapForDemo; barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; arrow; simple; rotatelabel; showlength; lendigits; 3;
  blkArrows:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - circ; pFrom; 1; ||| fill, red;
    - circ; pTo;   1; ||| fill, red;
    - dimmark; pFrom; pTo; label; $\text{arrow = simple}$; lw; 200; lh; 30; barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; arrow; simple; rotatelabel;
    - dimmark; pFrom; pTo; label; $\text{arrow = solid}$; lw; 200; lh; 30; gap; gapForDemo / 2;barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; arrow; solid; rotatelabel;
    - dimmark; pFrom; pTo; label; $\text{arrow = empty}$; lw; 200; lh; 30; gap; gapForDemo;barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; arrow; empty; rotatelabel;
    - dimmark; pFrom; pTo; label; $\text{arrow = none}$; lw; 200; lh; 30; gap; gapForDemo * 1.5;barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; arrow; none; rotatelabel;
  blkBars:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - circ; pFrom; 1; ||| fill, red;
    - circ; pTo;   1; ||| fill, red;
    - dimmark; pFrom; pTo; label; Both bars; lw; 200; lh; 30; barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; rotatelabel;
    - dimmark; pFrom; pTo; label; $\text{noStartBar}$; lw; 200; lh; 30; gap; gapForDemo / 2;barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; rotatelabel; nostartbar
    - dimmark; pFrom; pTo; label; $\text{noEndBar}$; lw; 200; lh; 30; gap; gapForDemo;barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; rotatelabel; noendbar;
    - dimmark; pFrom; pTo; label; $\text{noStartBar and noEndBar}$; lw; 200; lh; 30; gap; gapForDemo * 1.5;barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; rotatelabel; noStartBar; noendbar;
  blkLblWh:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - circ; pFrom; 1; ||| fill, red;
    - circ; pTo;   1; ||| fill, red;
    - dimmark; pFrom; pTo; label; $\text{label Text}$; lw; 200; lh; 50; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; arrow; simple; rotatelabel;labelfill; aqua; labelborder; magenta;
    - dimmark; cpLbl1a; cpLbl2a; label; $\text{labelWidth}$; lw; 100; lh; 20; rotatelabel;
    - dimmark; cpLbl1; cpLbl1a; label; $\text{labelHeight}$; lw; 100; remotepos; cpLbl1a.x - 40, cpLbl1a.y + 50;
    - label; pFrom.x, pTo.y + 10; anchor; ml; w; 140; h; 20; align; left; ||| labelFill = aqua
    - label; pFrom.x, pTo.y + 40; anchor; ml; w; 180; h; 20; align; left; ||| labelBorder = magenta
  blkLblText:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - circ; pFrom; 1; ||| fill, red;
    - circ; pTo;   1; ||| fill, red;
    - dimmark; pFrom; pTo; label; text size = 30 \\n $\text{textColor = orange}$; textcolor; orange; lw; 320; lh; 80; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; arrow; simple; rotatelabel;labelfill; aqua; labelborder; magenta; textsize; 30;
  blkLblNoRotate:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - circ; pFrom; 1; ||| fill, red;
    - circ; pTo;   1; ||| fill, red;
    - circ; lsMark.m; 5; ||| fill, black;
    - dimmark; pFrom; pTo; label; text size = 30 \\n $\text{textColor = orange}$; textcolor; orange; lw; 320; lh; 80; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; arrow; simple; labelfill; aqua; labelborder; magenta; textsize; 30;
  blkLblOnLine:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - circ; pFrom; 1; ||| fill, red;
    - circ; pTo;   1; ||| fill, red;
    - circ; lsMark.m; 5; ||| fill, black;
    - dimmark; pFrom; pTo; label; $\text{labelOnLine}$ \\n and rotate; textsize; 30; textcolor; orange; lw; 320; lh; 80; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; arrow; simple; labelonline; rotatelabel; labelfill; #eeeeee77; labelborder; magenta;
  blkLblOnLineNoRotate:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - circ; pFrom; 1; ||| fill, red;
    - circ; pTo;   1; ||| fill, red;
    - circ; lsMark.m; 5; ||| fill, black;
    - dimmark; pFrom; pTo; label; $\text{labelOnLine}$ \\n and NO rotate; textsize; 30; textcolor; orange; lw; 320; lh; 80; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; arrow; simple; labelonline; labelfill; #eeeeee77; labelborder; magenta;
  blkLabels:
    - blkLblWh
    - blkLblText; move; 0, 240;
    - blkLblNoRotate; move; 0, 480;
    - blkLblOnLine; move; 0, 700
    - blkLblOnLineNoRotate; move; 0, 940;
  blkRemotePos:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - dimmark; pFrom; pTo; label; remotePos.x < lsLineMid.x; lw; 200; lh; 30; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; arrow; simple; labelonline; labelfill; #99999933;  remotepos; pFrom.x + 150, pFrom.y + 60;
    - dimmark; pFrom; pTo; label; remotePos.x > lsLineMid.x \\n no $\text{labelFill}$; lw; 200; lh; 44; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; arrow; simple; labelonline;  remotepos; pTo.x - 150, pTo.y + 160;
    - circ; lsMark.m; 5; ||| fill, black;
    - label; lsMark.m; offset; 30, 0; anchor; ml; w; 70; ||| lsLineMid
  blkRightLeft:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - dimmark; pFrom; pTo; label;$\text{direction}$ = right; lw; 180; lh; 44; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
    - dimmark; pFrom; pTo; label;$\text{direction}$ = left; direction; left; lw; 180; lh; 44; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
  blkHori:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - dimmark; pFrom; pTo; type; h; label;$\text{type}$ = h \\n  $\text{direction}$ = right; lw; 180; lh; 40; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
    - dimmark; pFrom; pTo; type; h; label;$\text{type}$ = h \\n  $\text{direction}$ = left; left; lw; 180; lh; 40; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
  blkHoriBarLenEqual:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - dimmark; pFrom; pTo; type; h; label;$\text{type}$ = h \\n  $\text{direction}$ = right \\n $\text{barLenEqual}$; lw; 180; lh; 60; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;barLenEqual
    - dimmark; pFrom; pTo; type; h; label;$\text{type}$ = h \\n  $\text{direction}$ = left \\n $\text{barLenEqual}$; left; lw; 180; lh; 60; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel; barLenEqual
  blkVert:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - dimmark; pFrom; pTo; type; v; label;$\text{type}$ = v \\n  $\text{direction}$ = right; lw; 140; lh; 40; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
    - dimmark; pFrom; pTo; type; v; label;$\text{type}$ = v \\n  $\text{direction}$ = left; left; lw; 140; lh; 40; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
  blkVertBarLenEqual:
    - base
    - lnFromTo; stroke,1, grey; dash; 3,3; marker; arrow5;
    - dimmark; pFrom; pTo; type; v; label;$\text{type}$ = v \\n  $\text{direction}$ = right \\n $\text{barLenEqual}$; lw; 140; lh; 60; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;barLenEqual
    - dimmark; pFrom; pTo; type; v; label;$\text{type}$ = v \\n  $\text{direction}$ = left \\n $\text{barLenEqual}$; left; lw; 140; lh; 60; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel; barLenEqual
  
[wtht]

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkSimple
[wtht]

长度标记的添加方法和[文本标记](./label.md)类似，列在对应画图层级(通常是`block`,`layer`,`job`等)下面即可。它也只能现场添加，不能预先定义。添加时，使用`dimMark`关键字开头，后面跟用来分隔内容的分号(`;`)，再后面是关于此长度标记的**参数**，

定义长度标记的参数有以下这些：

- `from`(或`start`): 长度标记的起点。
- `to`(或`end`): 长度标记的终点。

[i] 请注意，起点和终点的相对性对长度标记的位置有决定性，与下面的`direction`参数共同决定标记的位置。

- `type`: 长度标记的类型，共有三种取值，`line`(默认值，表示沿着起点终点连线的方向）,`h`(或`hori`, `horizontal`, 表示标记为水平方向，长度为水平的距离），`v`(或`vert`,`vertical`，表示标记为竖直方向，长度为竖直方向的距离)
- `dir`(或`direction`): 长度标记的位置参数，取值为`left`或`right`，`right`为默认值，长度标记将被放置在起点终点连线的右侧，`left`将会把长度标记放在起点与终点连线的左侧。
- `gap`: 长度标记与其所标记的点（线）之间的距离，为一个长度,, 默认值为`2`
- `barWidth`: 长度标记两端竖线的线宽, 默认值为`0.5`
- `lineWidth`: 长度标记中间横线的线宽, 默认值为`0.5`
- `barLen`: 长度标记两端竖线的长度, 默认值为`24`
- `linePos`: 长度标记中中间横线在两端竖线上的相对位置，为[0,1]之间的数，默认值为`0.45`
- `noStartBar`: 一个开关参数，给出后，将不再绘制起点端的竖线
- `noEndBar`: 一个开关参数，给出后，将不再绘制终点端的竖线
- `noStartArrow`: 一个开关参数，给出后，将不再绘制中间横线起点端的箭头
- `noEndArrow`: 一个开关参数，给出后，将不再绘制中间横线终点端的箭头
- `barLenEqual`: 一个开关参数，是否将两端的竖线画成相同长度，这个参数只在标记类型为`h`或`v`时才生效，标记类型为`line`时，两根竖线本来就是相同长度的。
- `barColor`: 两端竖线的颜色
- `lineColor`: 中间横线的颜色
- `arrow`: 中间横线两端箭头的类型，取值为`simple`,`solid`,`empty`或`none`，`non`表示不画箭头（不常用）
- `showLength`: 是否显示两个点之间的长度（距离），此参数只有在`label`参数为给出情况下才启用
- `lenDigits`: 如果显示长度，此参数将控制显示小数点的为主，默认情况下，是0，即标记的长度是整数
- `label`: 长度标记的**文字标签**的内容，`label`如果没有指定，请打开`showlength`的开关，以便成功绘制长度标记。
- `labelWidth`(或`lw`): 文字标签的宽度，默认值为`60`
- `labelHeight`(或`lh`): 文字标签的高度，默认值为`20`
- `textSize`: 文字标签中文字的字体大小，默认值为`16`
- `textColor`: 文字标签中文字的颜色
- `labelOnLine`: 是否将文字标签放在中间横线上
- `labelFill`: 文字标签的背景色，默认没有背景色
- `labelBorder`: 文字标签的边框颜色，请参考
- `rotateLabel`: 一个开关，是否**旋转**文字标签，使其方向总是平行于中间横线，默认不旋转，即文字标签总是水平放置
- `remotePos`: 在长度很小，空间狭小时，可能无法放下文字标签，此时你可以给定此参数来将文字标签移到另外开阔的地方，这个参数就是**远处**你想放置文字标签的地方，它时一个位置参数，按照给定点的方式给定即可。

[i] 请注意，因为这里使用`label`关键字定义需要显示的文本说明，故我们无法将本文一开始列出的那些参数的关键字作为文本显示出来。这有一点局限性，但设计长度标记的初衷是标记这个长度，相应标签只是辅助信息，应当尽可能简短，简明扼要，所以最好不要拼写太过复杂的内容，如果的确需要标记那些关键字的名字，请想办法绕过，比如我们可有使用`label_Height`来替换`labelHeight`，或者我们把他们放在数学公式的表达式里`$\text{labelHeight}$`。

## 参数使用示例

1. 下面的示意图展示起点，终点，中间横线，两端竖线的含义，并同时给出中间横线的颜色，两端竖线的颜色，和文字标签的颜色。

```wtht
draw:
  - dimMark; pFrom; pTo; label; $\text{label text}$; lw; 200; lh; 30; 
    linecolor; red; linewidth; 2; barwidth; 1;
    barcolor; blue; textcolor; magenta;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkGeneric
[wtht]

2. 下面的示例展示`gap`的含义和使用。

```wtht
draw:
  - dimMark; pFrom; pTo; label; $\text{label text}$; lw; 200; lh; 30; gap; gapForDemo;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkShowGap
[wtht]

3. 下面的示例展示`barLen`,`linePos`的含义。

```wtht
draw:
  - dimmark; pFrom; pTo; label; $\text{label text}$; lw; 200; lh; 30; gap; gapForDemo;
    barlen; barLenForDemo; linepos; 0.76; linewidth; 2;linecolor; red;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkShowBarLen
[wtht]

4. 下面的示例展示各种不同的箭头的使用。

```wtht
draw:
  - dimmark; pFrom; pTo; label; Both bars; lw; 200; lh; 30; 
    barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; rotatelabel;
  - dimmark; pFrom; pTo; label; $\text{noStartBar}$; lw; 200; lh; 30; gap; gapForDemo / 2;
    barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; rotatelabel; nostartbar
  - dimmark; pFrom; pTo; label; $\text{noEndBar}$; lw; 200; lh; 30; gap; gapForDemo;
    barlen; barLenForDemo / 2; linewidth; 2;linecolor; red; rotatelabel; noendbar;
  - dimmark; pFrom; pTo; label; $\text{noStartBar and noEndBar}$; lw; 200; lh; 30; gap; 
    gapForDemo * 1.5;barlen; barLenForDemo / 2; linewidth; 2;linecolor; red;
    rotatelabel; noStartBar; noendbar;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkArrows
[wtht]


[i] 请注意，箭头大小和中间横线的线宽成正比，线宽越粗，箭头越大。

5. 下面的示例展示文字标签的宽高示意。

```wtht
draw:
  - dimmark; pFrom; pTo; label; $\text{label Text}$; lw; 200; lh; 50; gap; 6; 
    barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; arrow; simple;
    rotatelabel;labelfill; aqua; labelborder; magenta;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkLblWh
[wtht]

6. 下面的示例展示文字标签的字体大小和颜色。


```wtht
draw:
  - dimmark; pFrom; pTo; label; text size = 30 \\n $\text{textColor = orange}$;
    textcolor; orange; lw; 320; lh; 80; gap; 6;  barlen;barLenForDemo / 2; 
    linepos; 0.5 linewidth; 2;linecolor; red; arrow; simple; rotatelabel;
    labelfill; aqua; labelborder; magenta; textsize; 30;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkLblText
[wtht]

7. 下面的示例展示不旋转文字标签的样子。


```wtht
draw:
  - dimmark; pFrom; pTo; label; text size = 30 \\n $\text{textColor = orange}$; 
    textcolor; orange; lw; 320; lh; 80; gap; 6;  barlen;barLenForDemo / 2; 
    linepos; 0.5 linewidth; 2;linecolor; red; arrow; simple; 
    labelfill; aqua; labelborder; magenta; textsize; 30;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkLblNoRotate
[wtht]

8. 下面的示例展示文字标签放在中间横线上的样子。

```wtht
draw:
  - dimmark; pFrom; pTo; label; $\text{labelOnLine}$ \\n and rotate; 
    textsize; 30; textcolor; orange; lw; 320; lh; 80; gap; 6;  
    barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; 
    arrow; simple; labelonline; rotatelabel; 
    labelfill; #eeeeee77; labelborder; magenta;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkLblOnLine
[wtht]

9. 下面的示例展示文字标签放在中间横线上但不旋转的样子。

```wtht
draw:
  - dimmark; pFrom; pTo; label; $\text{labelOnLine}$ \\n and rotate; 
    textsize; 30; textcolor; orange; lw; 320; lh; 80; gap; 6;  
    barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; 
    arrow; simple; labelonline; 
    labelfill; #eeeeee77; labelborder; magenta;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkLblOnLineNoRotate
[wtht]

10. 下面的的示例展示将文字标签拉远的样子。请注意，远处的文字标签“向右偏”，还是“向左偏”取决于`remotePos`的`x`坐标是否比中间横线的**中点**的`x`坐标大或者小。


```wtht
draw:
  - dimmark; pFrom; pTo; label; remotePos.x < lsLineMid.x; lw; 200; lh; 30; gap; 6;
    barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; arrow; simple;
    labelonline; labelfill; #99999933;  remotepos; pFrom.x + 150, pFrom.y + 60;
  - dimmark; pFrom; pTo; label; remotePos.x > lsLineMid.x \\n no $\text{labelFill}$;
    lw; 200; lh; 44; gap; 6;  barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;
    linecolor; red; arrow; simple; labelonline;  remotepos; pTo.x - 150, pTo.y + 160;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkRemotePos
[wtht]

11. 下面的示例展示长度标记的方位`direction`的作用。

```wtht
draw:
  - dimmark; pFrom; pTo; label;$\text{direction}$ = right; lw; 180; lh; 44; gap; 6;
    barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
  - dimmark; pFrom; pTo; label;$\text{direction}$ = left; direction; left; lw; 180; lh; 44; gap; 6; 
    barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkRightLeft
[wtht]

12. 下面的示例展示长度标记为`h`类型时, 有`barLenEqual`和没有的样子。

```wtht
draw:
  - dimmark; pFrom; pTo; label;$\text{direction}$ = right; lw; 180; lh; 44; gap; 6;
    barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
  - dimmark; pFrom; pTo; label;$\text{direction}$ = left; direction; left; lw; 180; lh; 44; gap; 6; 
    barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkHori
  - blkHoriBarLenEqual; move; 0, 380;
[wtht]


13. 下面的示例展示长度标记为`v`类型时, 有`barLenEqual`和没有的样子。

```wtht
draw:
  - dimmark; pFrom; pTo; label;$\text{direction}$ = right; lw; 180; lh; 44; gap; 6;
    barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
  - dimmark; pFrom; pTo; label;$\text{direction}$ = left; direction; left; lw; 180; lh; 44; gap; 6; 
    barlen;barLenForDemo / 2; linepos; 0.5 linewidth; 2;linecolor; red; rotatelabel;
```

[wtht] # Simple demo
noTitle: yes
use: dimmarkSource
figure:
  - blkVert
  - blkVertBarLenEqual; move; 0, 280;
[wtht]
