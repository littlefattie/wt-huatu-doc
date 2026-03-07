# 角度标记

为了方便地标记一些角度，我们设计了一个**角度标记**的功能，通过给定一些参数，我们就可以自动标出那个角。角度标记作为一个画图元素和其他的待绘制条目一样，列在对应画图层级(通常是`block`,`layer`,`job`等)下面即可。它只能现场添加，不能预先定义。添加时，使用`anglemark`关键字开头，后面跟用来分隔内容的分号(`;`)，再后面是关于此角度标记的**参数**。

```wtht
draw:
  - anglemark; key1; value1; ... keyN; valueN;
```

角度的概念很简单，大概就是标记一个角$\angle ABC$, 这个角由三个点$A$，$B$，$C$确定。定义角度标记的参数如下：

- `pA`: $\angle ABC$构成中的点A
- `pB`: $\angle ABC$构成中的点B
- `pC`: $\angle ABC$构成中的点C

- `type`: 定义此标记的类型，可取值为`line1` （三根线）, `line2`（双线）, `line3`（三线）, `line4`（四线）, `arrow-solid`（实心箭头）, `arrow-simple`（简单箭头）, `arrow-empty`（空心箭头）
- `lineWidth`(或`strokeWidth`): 标记的线宽
- `size`: 角度标记实际上是一段弧线，对应$\angle ABC$角度的圆弧，这里的`size`是画出这个圆弧的半径，`size`越大，角度标记距离中间B点就越远
- `large`: 是一个开关，默认情况下，我们绘制$\lt 180\degree$的角，如果我们要绘制反向的$\gt 180\degree$的角，我们可以打开这个开关
- `fill`: 角度标记对应弧线和圆心包含的“小扇形”的填充效果，可以同时定义填充颜色，和透明度
- `fillOpacity`: “小扇形”的填充透明度
- `fillColor`: “小扇形”的填充色
- `strokeColor`: 角度标记对应线性的描边颜色
- `showDeg`: 是否显示角度标记对应的角度
- `degDigits`: 如果显示角度标记，显示几位小数，如果未提供，默认显示整数
- `showPerp`: 是否显示垂直标记，如果显示垂直标记，将不会显示角度和文本说明
- `label`: 要显示的文本说明（它是一个文本标签，请参考[文本标签](./label.md)），请注意，如果显示角度值，这里即便给定了文本也不会显示，显示角度具更优先
- `labelWidth`(或`lw`): 文本说明的宽度，参考[文本标签](./label.md)
- `labelHeight`(或`lh`): 文本说明的高度，参考[文本标签](./label.md)
- `textSize`: 文本说明的字体大小
- `textColor`: 文本说明的颜色
- `anchor`: 文本说明的锚点，请参考[文本标签->锚点](./label.md#锚点)
- `startArrow`: 是一个开关，是否显示起始箭头，这个只有在标记类型为箭头时才起效，无箭头时无效
- `labelPos`: 是一个百分比（0到1之间的数），表示文本说明的放置位置在角度标记弧线上的相对位置，方向为从出发射线到结束射线
- `labelOffset`(或`offset`): 文本说明相对于`labelPos`的偏移位置，对应于[文本标签](./label.md)的偏移offset

下面的图示显示角度标记的一些基本参数及其含义：

[wtht] # Fig Description...
asSource: yes
name: angleMarkSource

vars:
  wAng: 600
  hAng: 300
  sAng: 450
  wLbl: 90
  hLbl: 45
points:
  ptA: wAng, 0
  ptB: 0,0
  ptC: wAng, -hAng
  ptS: sAng, ptA.y
  ptLblOffset: 100, 80
shapes:
  outline: line; ptA; ptB; ptC;
  tri1: tri; ptC; ptA; ptB;
  lineBA: line; ptB; ptA
  lineBC: line; ptB; ptC
  circS: circ; ptB; sAng
  arcAng: arc; circS; lineBA.deg; lineBC.deg; ccw;
  circS2: circ; ptB; sAng + 3 * 4
  circS3: circ; ptB; sAng + 3 * 8
  circS4: circ; ptB; sAng + 3 * 12
  arcAng2: arc; circS2; lineBA.deg; lineBC.deg; ccw;
  arcAng3: arc; circS3; lineBA.deg; lineBC.deg; ccw;
  arcAng4: arc; circS4; lineBA.deg; lineBC.deg; ccw;
  arcAngLarge: arc; circS; lineBA.deg; lineBC.deg; cw;
cpoints:
  cpLabelPos: arcAng; 0.7
  cpLabelPos2: arcAng2; 0.7
  cpLabelPos3: arcAng3; 0.7
  cpLabelPos4: arcAng4; 0.7
  cpLabelPosLarge: arcAngLarge; 0.7
shapes2:
  lbBox: rect; cpLabelPos.x + ptLblOffset.x, cpLabelPos.y + ptLblOffset.y; wLbl; hLbl;
  lbBox2: rect; cpLabelPos2.x + ptLblOffset.x, cpLabelPos2.y + ptLblOffset.y; wLbl; hLbl;
  lbBox3: rect; cpLabelPos3.x + ptLblOffset.x, cpLabelPos3.y + ptLblOffset.y; wLbl; hLbl;
  lbBox4: rect; cpLabelPos4.x + ptLblOffset.x, cpLabelPos4.y + ptLblOffset.y; wLbl; hLbl;
  lbBoxLarge: rect; cpLabelPosLarge.x + ptLblOffset.x, cpLabelPosLarge.y + ptLblOffset.y; wLbl; hLbl;
blocks:
  blkPoints:
    - circ; ptA; 5; ||| fill, red, 0.6
    - circ; ptB; 5; ||| fill, red, 0.6
    - circ; ptC; 5; ||| fill, red, 0.6
    - label; ptA; anchor; ml; w; 35; ||| $A$
    - label; ptB; anchor; mr; w; 35; ||| $B$
    - label; ptC; anchor; bl; w; 35; ||| $C$
  blkGeneric:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox; stroke,1, red; dash; 3,3; fill, #eee;
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-solid; anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
    - dimmark; ptB; ptS; label; size;
    - circ; cpLabelPos; 5; ||| fill, aqua, 0.6
    - dimmark; lbBox.se; lbBox.ne; label; label_Height; textsize; 12; lw; 100;
    - dimmark; lbBox.ne; lbBox.nw; label; label_Width; textsize; 12; lw; 100;
    - dimmark; cpLabelPos; lbBox.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPos; lbBox.nw; v; right; label; offset.y; textsize; 12;
    - label; cpLabelPos; w; 120; h; 40; fs;12 ||| labelPos
    - line; cpLabelPos.x + 33, cpLabelPos.y + 22; cpLabelPos.x + 6, cpLabelPos.y + 2; ||| stroke,1, blue;; marker; arrow1;
    - line; ptB; cpLabelPos; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPos; size; sAng/2; linewidth; 2; arrow-solid; ml; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
  blkTypeLine1:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox; stroke,1, red; dash; 3,3; fill, #eee;
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line1; anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
    - dimmark; ptB; ptS; label; size;
    - circ; cpLabelPos; 5; ||| fill, aqua, 0.6
    - dimmark; cpLabelPos; lbBox.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPos; lbBox.nw; v; right; label; offset.y; textsize; 12;
    - line; ptB; cpLabelPos; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPos; size; sAng/2; linewidth; 2; arrow-solid; ml; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 50; anchor; tm; ||| type == line1
  blkTypeLine2:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox2; stroke,1, red; dash; 3,3; fill, #eee;
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line2; anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
    - dimmark; ptB; ptS; label; size;
    - circ; cpLabelPos2; 5; ||| fill, aqua, 0.6
    - dimmark; cpLabelPos2; lbBox2.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPos2; lbBox2.nw; v; right; label; offset.y; textsize; 12;
    - line; ptB; cpLabelPos2; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPos2; size; sAng/2; linewidth; 2; arrow-solid; ml; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 50; anchor; tm; ||| type == line2
  blkTypeLine3:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox3; stroke,1, red; dash; 3,3; fill, #eee;
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line3; anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
    - dimmark; ptB; ptS; label; size;
    - circ; cpLabelPos3; 5; ||| fill, aqua, 0.6
    - dimmark; cpLabelPos3; lbBox3.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPos3; lbBox3.nw; v; right; label; offset.y; textsize; 12;
    - line; ptB; cpLabelPos3; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPos3; size; sAng/2; linewidth; 2; arrow-solid; ml; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 50; anchor; tm; ||| type == line3
  blkTypeLine4:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox4; stroke,1, red; dash; 3,3; fill, #eee;
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line4; anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
    - dimmark; ptB; ptS; label; size;
    - circ; cpLabelPos4; 5; ||| fill, aqua, 0.6
    - dimmark; cpLabelPos4; lbBox4.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPos4; lbBox4.nw; v; right; label; offset.y; textsize; 12;
    - line; ptB; cpLabelPos4; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPos4; size; sAng/2; linewidth; 2; arrow-solid; ml; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 50; anchor; tm; ||| type == line4
  blkTypeArrowSolid:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox; stroke,1, red; dash; 3,3; fill, #eee;
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-solid; anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
    - dimmark; ptB; ptS; label; size;
    - circ; cpLabelPos; 5; ||| fill, aqua, 0.6
    - dimmark; cpLabelPos; lbBox.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPos; lbBox.nw; v; right; label; offset.y; textsize; 12;
    - line; ptB; cpLabelPos; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPos; size; sAng/2; linewidth; 2; arrow-solid; ml; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 50; anchor; tm; ||| type == arrow-solid
  blkTypeArrowSimple:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox; stroke,1, red; dash; 3,3; fill, #eee;
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-simple; anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
    - dimmark; ptB; ptS; label; size;
    - circ; cpLabelPos; 5; ||| fill, aqua, 0.6
    - dimmark; cpLabelPos; lbBox.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPos; lbBox.nw; v; right; label; offset.y; textsize; 12;
    - line; ptB; cpLabelPos; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPos; size; sAng/2; linewidth; 2; arrow-solid; ml; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 50; anchor; tm; ||| type == arrow-simple
  blkTypeArrowEmpty:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox; stroke,1, red; dash; 3,3; fill, #eee;
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-empty; anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
    - dimmark; ptB; ptS; label; size;
    - circ; cpLabelPos; 5; ||| fill, aqua, 0.6
    - dimmark; cpLabelPos; lbBox.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPos; lbBox.nw; v; right; label; offset.y; textsize; 12;
    - line; ptB; cpLabelPos; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPos; size; sAng/2; linewidth; 2; arrow-solid; ml; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 50; anchor; tm; ||| type == arrow-empty
  blkTypesLine1234:
    - blkTypeLine1
    - blkTypeLine2; move; 0, 400;
    - blkTypeLine3; move; 0, 800;
    - blkTypeLine4; move; 0, 1200;
  blkTypesArrows:
    - blkTypeArrowSolid
    - blkTypeArrowSimple; move; 0, 400;
    - blkTypeArrowEmpty; move; 0, 800;
  blkLarge:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBoxLarge; stroke,1, red; dash; 3,3; fill, #eee;
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-solid; anchor; tl; large; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
    - dimmark; ptB; ptS; label; size; left;
    - circ; cpLabelPosLarge; 5; ||| fill, aqua, 0.6
    - dimmark; cpLabelPosLarge; lbBoxLarge.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPosLarge; lbBoxLarge.nw; v; right; label; offset.y; textsize; 12;
    - line; ptB; cpLabelPosLarge; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPosLarge; large; size; sAng/2; linewidth; 2; arrow-solid; tr; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
    - circ; ptB; sAng * 1.02; ||| fill, none;
  blkShowDeg:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox; stroke,1, red; dash; 3,3; fill, #eee;
    # showDeg 出现时， label值将会被忽略
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line1; anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$; showDeg;
    - dimmark; ptB; ptS; label; size;
    - circ; cpLabelPos; 5; ||| fill, aqua, 0.6
    - dimmark; cpLabelPos; lbBox.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPos; lbBox.nw; v; right; label; offset.y; textsize; 12;
    - line; ptB; cpLabelPos; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPos; size; sAng/2; linewidth; 2; arrow-solid; ml; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 50; anchor; tm; h;25 ||| 显示角度，showDeg
  blkShowDeg2:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox; stroke,1, red; dash; 3,3; fill, #eee;
    # showDeg 出现时， label值将会被忽略
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line1; anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; showDeg; degdigits; 5;
    - dimmark; ptB; ptS; label; size;
    - circ; cpLabelPos; 5; ||| fill, aqua, 0.6
    - dimmark; cpLabelPos; lbBox.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPos; lbBox.nw; v; right; label; offset.y; textsize; 12;
    - line; ptB; cpLabelPos; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPos; size; sAng/2; linewidth; 2; arrow-solid; ml; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 50; anchor; tm; h;25 ||| 显示角度，showDeg 和 degDigits
  blkShowDegs:
    - blkShowDeg;
    - blkShowDeg2; move; 0, 350
  blkShowStartArrow:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox; stroke,1, red; dash; 3,3; fill, #eee;
    # showDeg 出现时， label值将会被忽略
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-solid; anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; showDeg; degdigits; 5; startArrow;
    - dimmark; ptB; ptS; label; size;
    - circ; cpLabelPos; 5; ||| fill, aqua, 0.6
    - dimmark; cpLabelPos; lbBox.nw; h; left; label; offset.x; textsize; 12;
    - dimmark; cpLabelPos; lbBox.nw; v; right; label; offset.y; textsize; 12;
    - line; ptB; cpLabelPos; ||| stroke,1, aqua;
    - anglemark; ptA; ptB; cpLabelPos; size; sAng/2; linewidth; 2; arrow-solid; ml; fill, 1, yellow; label; $0.7\alpha=\text{labelPos}\times\alpha$; textsize; 12 ; lw; 160; lh; 20; offset; 10, 0;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 50; anchor; tm; h;25 ||| 两端都显示箭头
  blkShowStyles:
    - outline; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    - lbBox; stroke,1, red; dash; 3,3; fill, #eee;
    # strokeColor 可以更改弧线的颜色 
    # fill; 0.3, maroon == fillcolor; maroon; fillOpacity; 0.3
    - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-solid; anchor; tl; labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; showDeg; degdigits; 5; startArrow; strokeColor; red; fillcolor; maroon; fillOpacity; 0.3;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 30; anchor; tm; h;25 w; 350;||| 更改弧线的颜色，“小扇形”填充的颜色和透明度
  blkShowPerpMark:
    - tri1; stroke, 4, #ccc; fill,none; linejoin; round;
    - blkPoints
    #- lbBox; stroke,1, red; dash; 3,3; fill, #eee;
    # 角<CAB是直角，这样我们就可以画一个直角标记
    - anglemark; ptC; ptA; ptB; size; 50; linewidth; 4; arrow-solid; anchor; tl; labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; showDeg; degdigits; 5; showPerp; startArrow; strokeColor; red; fillcolor; maroon; fillOpacity; 0.3;
    - label; (ptA.x + ptB.x) / 2, ptA.y + 30; anchor; tm; h;25 w; 350;||| 直角标记
[wtht]

[wtht] # Fig Description...
noTitle: yes
use: angleMarkSource
figure: 
  - blkGeneric
[wtht]

[i] 请注意，在定义角度标记的时候，因为构成$\angle ABC$的三个点使用`pA`,`pB`,`pC`作为关键字，你将无法使用前面你以`pA`，`pB`，`pC`为名字定义的点（如果有的话）来定义角度标记。

[i] 请注意，因为这里使用`label`关键字定义需要显示的文本说明，故我们无法将本文一开始列出的那些参数的关键字作为文本显示出来。这有一点局限性，但设计角度标记的初衷是标记这个角度，文本说明是辅助信息，且应当尽可能简短，简明扼要，所以最好不要拼写太过复杂的内容，如果的确需要标记那些关键字的名字，请想办法绕过，比如一个下划线等。

## 标记类型

不同的标记类型，所绘制的那根弧线会有所不同，下面是各自展示：
```wtht
draw:
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line1;        anchor; tl; fill, 0.4, yellow;
    labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line2;        anchor; tl; fill, 0.4, yellow;
    labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line3;        anchor; tl; fill, 0.4, yellow;
    labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line4;        anchor; tl; fill, 0.4, yellow;
    labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-solid;  anchor; tl; fill, 0.4, yellow;
    labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-simple; anchor; tl; fill, 0.4, yellow;
    labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-empty;  anchor; tl; fill, 0.4, yellow;
    labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
```


[wtht] # Line 1
noTitle: yes
use: angleMarkSource
figure: 
  - blkTypeLine1
[wtht]

[wtht] # Line 2
noTitle: yes
use: angleMarkSource
figure: 
  - blkTypeLine2
[wtht]

[wtht] # Line 3
noTitle: yes
use: angleMarkSource
figure: 
  - blkTypeLine3
[wtht]

[wtht] # Line 4
noTitle: yes
use: angleMarkSource
figure: 
  - blkTypeLine4
[wtht]

[wtht] # Line Arrow solid
noTitle: yes
use: angleMarkSource
figure: 
  - blkTypeArrowSolid
[wtht]

[wtht] # Line Arrow Simple
noTitle: yes
use: angleMarkSource
figure: 
  - blkTypeArrowSimple
[wtht]

[wtht] # Line Arrow empty
noTitle: yes
use: angleMarkSource
figure: 
  - blkTypeArrowEmpty
[wtht]

## 优弧 large

我们可以打开`large`的开关，这样，我们就可以绘制另外半圈的圆弧标记，默认是$\lt 180\degree$的。

```wtht
draw:
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-solid; anchor; tl; 
    large; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; lw; wLbl; lh; hLbl; label; $\alpha$
```

[wtht] # Line Arrow empty
noTitle: yes
use: angleMarkSource
figure: 
  - blkLarge
[wtht]

## 显示角度

我们可以添加`showDeg`关键字，这样就可以显示角度，同时`label`给出的内容将被忽略，同时，你可以使用`degDigits`来显示角度数值的小数点位数。

```wtht
draw:
  # showDeg 出现时， label值将会被忽略
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line1; 
    anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; 
    lw; wLbl; lh; hLbl; label; $\alpha$; showDeg;
  # 使用 degDigits 显示角度值的多位小数
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; line1; 
    anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; 
    lw; wLbl; lh; hLbl; showDeg; degdigits; 5;
```

[wtht] # Line Arrow empty
noTitle: yes
use: angleMarkSource
figure: 
  - blkShowDegs
[wtht]


## 两端箭头

如果需要标记弧线的两端都显示箭头，我们需要将类型设置为**箭头类型**（`arrow-solid`, `arrow-simple`, 或者`arrow-empty`），然后添加`startArrow`关键字，这样，就会在两端都显示箭头。


```wtht
draw:
  # 使用 degDigits 显示角度值的多位小数
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-solid; 
    anchor; tl; fill, 0.4, yellow;labelPos; 0.7; offset; ptLblOffset; 
    lw; wLbl; lh; hLbl; showDeg; degdigits; 5; startArrow
```

[wtht] # Line Arrow empty
noTitle: yes
use: angleMarkSource
figure: 
  - blkShowStartArrow
[wtht]

## 颜色设置

下面的例子中，我们可以更改填充色，和弧线的颜色。

```wtht
draw:
  # strokeColor 可以更改弧线的颜色 
  # fill; 0.3, maroon == fillcolor; maroon; fillOpacity; 0.3
  - anglemark; ptA; ptB; ptC; size; sAng; linewidth; 4; arrow-solid; 
    anchor; tl; labelPos; 0.7; offset; ptLblOffset; 
    lw; wLbl; lh; hLbl; showDeg; degdigits; 5; startArrow; 
    strokeColor; red; fillcolor; maroon; fillOpacity; 0.3;
```

[wtht] # Line Arrow empty
noTitle: yes
use: angleMarkSource
figure: 
  - blkShowStyles
[wtht]

## 直角标记

我们可以使用`showPerp`标记来表示直角标记，前提是这个角的确是$90\degree$的，使用直角标记的时候，`label`, 和`showDeg`都将不起作用。直角标记时，`size`表示的是标记（正方形）的宽度。


```wtht
draw:
  # 角<CAB是直角，这样我们就可以画一个直角标记， showPerp
  - anglemark; ptC; ptA; ptB; size; 50; linewidth; 4; arrow-solid; 
    anchor; tl; labelPos; 0.7; offset; ptLblOffset; 
    lw; wLbl; lh; hLbl; showDeg; degdigits; 5; showPerp; startArrow; 
    strokeColor; red; fillcolor; maroon; fillOpacity; 0.3;
```

[wtht] # Line Arrow empty
noTitle: yes
use: angleMarkSource
figure: 
  - blkShowPerpMark
[wtht]
