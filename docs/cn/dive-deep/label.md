# 文本标签

## 背景
在设计图中，文字通常不可或缺，比如用来标记一些关键信息，或者用来解释说明一些复杂信息，再或者单个的字符也是设计图的一部分。于是在画图中添加文字就是一件必须要完成的事情。

在SVG图片中，我们可以直接添加文本，比如使用如下的代码就可以:

```html
<svg width="250" height="60" xmlns="http://www.w3.org/2000/svg">
  <text x="20" y="25" fill="purple" font-size="20">
  Hello SVG!
  </text>
</svg>
```
但为了更方便更友好地添加文本，我们没有直接使用SVG的文本添加方式，我们额外使用一个`foreignObject`对文本内容包了一层，在SVG层级，文本是一个`foreignObject`对象，而在`foreignObject`内部，它是HTML。这样以来，对于文本的摆放和坐标关系，我们使用`foreignObject`(类似一个方盒子)，会很便捷，对于文本内容的呈现，我们可以充分利用html的排版优势来实现更多可能性。

## 如何使用

文本标签作为一个画图元素和其他的待绘制条目一样，列在对应画图层级(通常是`block`,`layer`,`job`等)下面即可。它只能现场添加，不能预先定义。添加时，使用`label`关键字开头，后面跟用来分隔内容的分号(`;`)，再后面是关于此文本标签的**参数**，参数罗列完成后，添加一个文本分隔符`|||`（三根竖线）,在这个分隔符后面就是需要添加的文本内容了。

```wtht
draw:
  - label; key1; value1; ... keyN; valueN;  ||| 这是一个文本
```

[i] 定义文本的时候一定要添加三根竖线的分隔符`|||`，如果没有添加，可能会报如下的错误：
- `[e]Block [someBlockName] resolving failed: [label] is not found in defined modules!`

## 参数

格式参数包括两部分，一部分是文本的**风格参数**，重点是**如何呈现**这个文本，一种是文本**属性参数**，重点是文本的位置，大小，锚点等。另外，关于风格参数，我们可以单独定义，并重复使用。

**风格参数**的关键字和可能取值如下：

- `textAlign`, `align`: 定义文本在表示大小的方盒子内如何对齐，取值为`left`，表示左对齐, `right`，表示右对齐,`center`，表示居中对齐，默认值（即未特别指定时）是居中对齐`center`。请注意，这里的对其是水平对齐，垂直对齐无法设置，因为它**总是垂直居中对齐的**，即如果文本盒子的高度改变以后，实际文字的显示位置可能发生变化。
- `bold`: 是否**粗体**显示，无需取值，此关键字罗列即可
- `italic`: 是否**斜体**显示，无需取值，此关键字罗列即可
- `addLine` (或`line`): 是否为文字添加修饰线，有上划线(`over`, `overline`)，下划线(`under`, `underline`)，或居中线(`middle`, `middleline`, `through`, `strike-through`, `strikethrough`)。
- `fontName`, (或`font`): 字体名称，有一些预置的简写的字体名字，但也可以指定系统其他的字体，需要注意的是，这里的字体依赖于系统安装的字体，即便你指定了字体，如果你的电脑上没有安装这个字体，它依然不会显示出来，同样地，如果你电脑上安装了某个字体，但是另外一台电脑上没有，那么相同的代码可能显示的字体也不相同。
- `color`, (或`fontColor`): 文本的颜色，可以使用颜色名字，比如`red`，或者颜色代码`#33445566`等
- `fontSize`, (或`size`, `fs`): 文字的字体大小，默认大小是浏览器的默认值，通常为16，或者12等，你可以自己指定为你想要的其他整数数值。

以上的风格参数，我们可以专门定义成为一个**预定义的文本格式**，然后在放置文本的现场定义中引用它。定义方式是在画图的根级添加`labelStyles`标签，然后在他下面添加我们定义的格式。在定义格式的时候，我们可以引用已定义的格式，这样就可以在它的基础上只改我们要改的部分。比如下面的例子中，我们就定义了三个文本格式，`lbSty1`, `lbSty2`和`lbSty3`，其中，`lbSty2`在`lbSty1`的基础上，关闭加粗和斜体，调整字体到16，改变颜色为蓝色，`lbSty3`则在`lbSty2`基础上仅调整字体大小到12，改颜色为洋红。

```wtht
labelStyles:
  lbSty1:
    align: left
    bold: yes
    italic: yes
    addLine: over
    font: lishu
    fontsize: 30
  lbSty2:
    ref: lbSty1
    italic: no
    bold: no
    fontsize: 16
  lbSty3: ref; lbSty2; fontsize; 12; color; magenta;

# 绘制这三个文本
draw:
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; lbSty1; ||| 文本 lbSty1
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; refs; lbSty2; ||| 文本 lbSty2
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; refs; lbSty3; ||| 文本 lbSty3
```

所以，在某一个已定义的格式上重新定义，只需要使用`refs`(或`styles`, `style`, `ref`)关键字引用其**名字**即可。

**属性参数**有以下一些:

- `refs` (或`styles`, `style`, `ref`):需要引用的已定义文本格式，取值为文本格式的名字。跟上面的解释含义相同，在现场定义文本的时候，我们可以引用已定义的**文本格式**，这样，我们就可以复用那个格式了，也可以快速实现许多许多个文本都是相同的格式。
- `width` (或`w`): 文本盒子的宽度，是一个数值，可以给数字，或者变量计算的组合
- `height` (或`h`): 文本盒子的高度，是一个数值，可以给数字，或者变量计算的组合
- `bgColor` (或`background`, `bg`): 我们可以指定一个颜色作为文本的背景
- `borderColor` (或`bdrColor`, `bdr`): 给文本盒子的边框上色
- `location` (或`at`, `position`, `pos`, `loc`): 文本盒子放置的位置，是一个点，可以是现场定义的点，也可以是前面已定义的点，交点，获取的点等。此参数是必须的。
- `offset`: 和前面的`location`共同决定文本的实际摆放位置, 是一个**类点**(`(x,y)`的形式)的参数。此参数不是必须的，可以不指定，仅在我们需要将文本位置相对于`location`做微调时才会考虑使用，如果给定这个参数，文本的实际的位置为$P_{real} = P_{loc} + P_{offset}$。
- `rotDeg` (或`deg`): 文本的旋转角，在位置定下来以后，我们可以定义文本盒子绕着这个点旋转的角度。
- `anchor`: 文本盒子上某个**固定在坐标点的位置点**，取值会决定文本盒子的实际位置，可取的值为：`tl`, `topleft`, `tm`, `topmiddle`, `tr`, `topright`, `ml`, `middleleft`, `mm`, `center`, `c`, `middlemiddle`, `mr`, `middleright`, `bl`, `bottomleft`, `bm`, `bottommiddle`, `br`, `bottomright`，下文将详细讲述。

[wtht] # Fig Description...
# wt-huatu default empty file
name: labelExplain
asSource: yes

styles:
  styBox: fill,none; stroke; 1, grey; dash; 3,3;
  styRealPos: fill, blue, 0.6
labelStyles:
  lbSty1:
    align: left
    bold: yes
    italic: yes
    addLine: over
    font: lishu
    fontsize: 30
  lbSty2:
    ref: lbSty1
    italic: no
    bold: no
    fontsize: 16
  lbSty3: ref; lbSty2; fontsize; 12; color; magenta;
vars:
  dx: 110
  dy: -70
  lblW: 200
  lblH: 100

points:
  p0: 0,0
  pOffset: dx, dy;
  pReal: p0.x + dx, p0.y + dy
  pBcTL: pReal.x + lblW / 2, pReal.y + lblH / 2
  pBcTM: pReal.x, pReal.y + lblH / 2
  pBcTR: pReal.x - lblW / 2, pReal.y + lblH / 2
  pBcML: pReal.x + lblW / 2, pReal.y
  pBcMM: pReal.x, pReal.y
  pBcMR: pReal.x - lblW / 2, pReal.y
  pBcBL: pReal.x + lblW / 2, pReal.y - lblH / 2
  pBcBM: pReal.x, pReal.y - lblH / 2
  pBcBR: pReal.x - lblW / 2, pReal.y - lblH / 2

shapes:
  cLoc: circ; p0; 5;
  cReal: circ; pReal; 5;
  boxDemo: rect; bycenter; p0; 2*lblW; 2 *lblH;
  boxTL: rect; bycenter; pBcTL; lblW; lblH;
  boxTM: rect; bycenter; pBcTM; lblW; lblH;
  boxTR: rect; bycenter; pBcTR; lblW; lblH;
  boxML: rect; bycenter; pBcML; lblW; lblH;
  boxMM: rect; bycenter; pBcMM; lblW; lblH;
  boxMR: rect; bycenter; pBcMR; lblW; lblH;
  boxBL: rect; bycenter; pBcBL; lblW; lblH;
  boxBM: rect; bycenter; pBcBM; lblW; lblH;
  boxBR: rect; bycenter; pBcBR; lblW; lblH;
  boxTLrot36: rotate; boxTL; 36; boxTL.nw;
  boxTMrotm30: rotate; boxTM; -30; boxTM.n;
blocks:
  base:
    - cLoc; fill, red
    - cReal; styRealPos
  blkGeneric:
    - boxTLrot36; styBox
    - base
    - dimmark; p0; pReal; h; dir; left; gap; 10; barlenEqual; label; offset.x
    - dimmark; p0; pReal; v; dir; left; gap; 10; barlenEqual; label; offset.y
    - label; p0; offset; pOffset; w; lblW; h; lblH; rotdeg; 36 ||| label text (anchor = ml)
    - line; p0.x - 20, p0.y + 50; p0.x - 2, p0.y + 5;  ||| stroke,1, black; marker; arrow2;
    - label; p0.x - 20, p0.y + 50; w; 80, h; 20; anchor; tm; ||| location
    - line; pReal.x + 43, pReal.y -42; pReal.x + 6, pReal.y -6;  ||| stroke,1, black; marker; arrow2;
    - label; pReal.x + 45, pReal.y -42; w; 160, h; 20; anchor; ml; ||| 真实位置 real location
    - line; pReal.x - 20, pReal.y; pReal.x + 200, pReal.y; ||| stroke,0.5,maroon;
    - anglemark; pReal.x + 200, pReal.y; pReal; boxTLrot36[2]; label; rotDeg; size; 80; type; arrow-solid; linewidth; 2; anchor;tl; labelpos; 0.35; offset; 5,0;
    - dimmark; boxTLrot36[4]; boxTLrot36[3]; gap; 6; label;  width
    - dimmark; boxTLrot36[3]; boxTLrot36[2]; gap; 6; label;  height
  blkAnchorPoints:
    - boxDemo; styBox
    - circ; boxDemo.nw; 5; ||| styRealPos
    - label; boxDemo.nw; offset; -10,0; w; 100; h; 20; anchor; br; align;right; ||| topleft, tl
    - circ; boxDemo.n;  5; ||| styRealPos
    - label; boxDemo.n; offset; 0,-10; w; 110; h; 20; anchor; bm; ||| topmiddle, tm
    - circ; boxDemo.ne; 5; ||| styRealPos
    - label; boxDemo.ne; offset; 10,0; w; 90; h; 20; anchor; bl; align;left; ||| topright, tr
    - circ; boxDemo.w;  5; ||| styRealPos
    - label; boxDemo.w; offset; -10,0; w; 120; h; 20; anchor; mr; align;right; ||| middleleft, ml
    - circ; boxDemo.c;  5; ||| styRealPos
    - label; boxDemo.c; offset; 0,-10; w; 240; h; 20; anchor; bm; ||| middlemiddle, mm, center, c
    - circ; boxDemo.e;  5; ||| styRealPos
    - label; boxDemo.e; offset; 10,0; w; 120; h; 20; anchor; ml; align;left; ||| middleright, mr
    - circ; boxDemo.sw; 5; ||| styRealPos
    - label; boxDemo.sw; offset; -10,0; w; 120; h; 20; anchor; tr; align;right; ||| bottomleft, bl
    - circ; boxDemo.s;  5; ||| styRealPos
    - label; boxDemo.s; offset; 0,10; w; 140; h; 20; anchor; tm; ||| bottommiddle, bm
    - circ; boxDemo.se; 5; ||| styRealPos
    - label; boxDemo.se; offset; 10,0; w; 120; h; 20; anchor; tl; align;left; ||| bottomright, br
  blkShowTL:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; ||| label text \\n (anchor = tl)
  blkShowTM:
    - boxTM; styBox
    - base
    - label; p0; offset; pOffset; anchor; tm; w; lblW; h; lblH; ||| label text \\n (anchor = tm)
  blkShowTR:
    - boxTR; styBox
    - base
    - label; p0; offset; pOffset; anchor; tr; w; lblW; h; lblH; ||| label text \\n (anchor = tr)
  blkShowML:
    - boxML; styBox
    - base
    - label; p0; offset; pOffset; anchor; ml; w; lblW; h; lblH; ||| label text \\n (anchor = ml)
  blkShowMM:
    - boxMM; styBox
    - base
    - label; p0; offset; pOffset; anchor; mm; w; lblW; h; lblH; ||| label text \\n (anchor = mm)
  blkShowMR:
    - boxMR; styBox
    - base
    - label; p0; offset; pOffset; anchor; mr; w; lblW; h; lblH; ||| label text \\n (anchor = mr)
  blkShowBL:
    - boxBL; styBox
    - base
    - label; p0; offset; pOffset; anchor; bl; w; lblW; h; lblH; ||| label text \\n (anchor = bl)
  blkShowBM:
    - boxBM; styBox
    - base
    - label; p0; offset; pOffset; anchor; bm; w; lblW; h; lblH; ||| label text \\n (anchor = bm)
  blkShowBR:
    - boxBR; styBox
    - base
    - label; p0; offset; pOffset; anchor; br; w; lblW; h; lblH; ||| label text \\n (anchor = br)
  blkLblOverLine:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH;  addline;  over; ||| 上划线 overline
  blkLblUnderLine:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH;  addline;  under; ||| 下划线 underline
  blkLblMiddleLine:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH;  addline;  middle; ||| 删除线 middleline
  blkLblLines:
    - blkLblOverLine
    - blkLblUnderLine; move; 0, 150
    - blkLblMiddleLine; move; 0, 300
  blkLblAlignCenter:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; ||| 中间对齐, align center \\n(默认取值)
  blkLblAlignLeft:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH;  align; left ||| 左对齐, align left
  blkLblAlignRight:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH;  align; right ||| 右对齐, align right
  blkLblAlign:
    - blkLblAlignCenter;
    - blkLblAlignLeft; move; 0, 150
    - blkLblAlignRight; move; 0, 300
  blkLblBold:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; bold; color; red;font;lishu ||| 粗体 bold
  blkLblItalic:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; italic; color; magenta; font; fangsong ||| 斜体 italic
  blkLblBoldItalic:
    - blkLblBold
    - blkLblItalic; move; 0, 150;
  blkLineHeight1:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; bold; lineheight; 1.0 ||| 这是一段特别长的文本，我们需要用三行来显示，于是 lineheight 会显得有用
  blkLineHeight1d5:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; bold; lineheight; 1.8 ||| 这是一段特别长的文本，我们需要用三行来显示，于是 lineheight 会显得有用
  blkLineHeight:
    - blkLineHeight1;
    - blkLineHeight1d5; move; 0, 150
  blkLbStyle1:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; lbSty1; ||| 文本 lbSty1
  blkLbStyle2:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; refs; lbSty2; ||| 文本 lbSty2
  blkLbStyle3:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; refs; lbSty3; ||| 文本 lbSty3
  blkLbStyes:
    - blkLbStyle1
    - blkLbStyle2; move; 0, 150;
    - blkLbStyle3; move; 0, 300;
  blkBgBdr:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; bdr; magenta; bg; #66778899 ||| 背景色 #66778899 \\n 边框色 洋红(magenta)
  blkFont1:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; lishu ||| 隶书字体
  blkFont2:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; kaiti ||| 楷体字体
  blkFont3:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; times ||| Font Times New Roman
  blkFont4:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; 仿宋 ||| 仿宋字体（你可以使用中文字体名称）
  blkFont5:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; yahei ||| 微软雅黑字体
  blkFont6:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; Arial ||| Font Arial
  blkFonts:
    - blkFont1
    - blkFont2; move; 0, 140
    - blkFont3; move; 0, 280
    - blkFont4; move; 450, 0
    - blkFont5; move; 450, 140
    - blkFont6; move; 450, 280
  blkRotate:
    - base
    - boxTM; stroke,1, grey; dash; 3,3; fill, none;
    - label; p0; offset; pOffset; anchor; tm; w; lblW; h; lblH; align;left;||| 我没旋转
    - boxTMrotm30; stroke,1, red; dash; 3,3; fill, none;
    - label; p0; offset; pOffset; anchor; tm; w; lblW; h; lblH; rotdeg; -30; color; red; ||| 我旋转了$-30\degree$
  blkKatex1:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; ||| $x_{1,2} = \frac{-b\pm\sqrt{b^2-4ac}}{2a}$
  blkKatex2:
    - boxTL; styBox
    - base
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; align; left; font; songti; ||| 拉普拉斯变换: \\n $F(s)=\int_{-\infty}^{+\infty}f(t)e^{-st}\text{d}t$
  blkKatexs:
    - blkKatex1
    - blkKatex2; move; 0, 150;
[wtht]

### 坐标关系

下图展示`location`和`offset`如何确定文本盒子的位置关系，以及宽，高，旋转角等。

[wtht] # Fig Description...
noTitle: yes
use: labelExplain
figure:
  - blkGeneric
[wtht]

### 锚点

锚点共有9种取值情况，见下图。这里的取值含义是文本盒子上相对位置的点。锚点取这个值的意思是，**将这个点跟计算出的文本放置位置（坐标）对齐**，同时，如果需要旋转，旋转亦是将文本盒子绕着这个点进行旋转。

[wtht] # Fig Description...
noTitle: yes
use: labelExplain
figure:
  - blkAnchorPoints
[wtht]

锚点位置与文本盒子实际放置位置的关系如下面各图所示：

[wtht] # Fig Description...
noTitle: yes
use: labelExplain
figure:
  - blkShowTL;
  - blkShowTM; move; 400, 0
  - blkShowTR; move; 800, 0
  - blkShowML; move; 0, 250
  - blkShowMM; move; 400, 250
  - blkShowMR; move; 800, 250
  - blkShowBL; move; 0, 500
  - blkShowBM; move; 400, 500
  - blkShowBR; move; 800, 500
[wtht]

## 使用预定义文本格式

如前所述，下面的代码将绘制出如下的三个文本。

```wtht
labelStyles:
  lbSty1:
    align: left
    bold: yes
    italic: yes
    addLine: over
    font: lishu
    fontsize: 30
  lbSty2:
    ref: lbSty1   # 以 lbSty1 为基础
    italic: no    # 取消斜体
    bold: no      # 取消粗体
    fontsize: 16  # 字体调整为 16
  lbSty3: ref; lbSty2; fontsize; 12; color; magenta;  # 以lbSty2为基础，更改字体为12， 改颜色为洋红

# 绘制这三个文本
draw:
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; lbSty1; ||| 文本 lbSty1
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; refs; lbSty2; ||| 文本 lbSty2
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; refs; lbSty3; ||| 文本 lbSty3
```

[wtht] # Fig Description...
noTitle: yes
use: labelExplain
figure:
  - blkLbStyes
[wtht]

## 背景和边框线

我们可以为文本标签添加背景颜色，并对边框进行描边。背景色可以任意指定颜色代码，边框为固定1px的实线，（本文当中的虚线文本边框仅为示意文本边界，并非这里的文本边框），如果你想要更复杂的边框，请**额外添加一个矩形来补充**。

```wtht
draw:
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; bdr; magenta; bg; #66778899 ||| 背景色 #66778899 \\n 边框色 洋红(magenta)
```

[wtht] # Fig Description...
noTitle: yes
use: labelExplain
figure:
  - blkBgBdr
[wtht]

## 加粗和倾斜

以下的代码示例文本如何加粗和倾斜：

```wtht

draw:
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; bold; color; red;font;lishu ||| 粗体 bold
    - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; italic; color; magenta; font; fangsong ||| 斜体 italic
```

[wtht] # Fig Description...
noTitle: yes
use: labelExplain
figure:
  - blkLblBoldItalic
[wtht]

## 对齐

以下的代码示例文本如何对齐：

```wtht
draw:
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; ||| 中间对齐, align center \\n(默认取值)
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH;  align; left ||| 左对齐, align left
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH;  align; right ||| 右对齐, align right
```

[wtht] # Fig Description...
noTitle: yes
use: labelExplain
figure:
  - blkLblAlign
[wtht]

## 字体

我们可以使用`font`关键字来指定字体，我们提供了一些预置的字体，你也可以使用你喜欢的字体，但请注意，这里的字体并不严格等同于**Word**中的字体，这里是浏览器中`font-family`的概念，你能否使用这个字体取决于你的电脑上是否安装了该字体，且浏览器能够使用改字体进行渲染。相同的代码在不同的电脑上很容易出现字体不一致的情况，大体上是因为安装的字体不同所致。如果你特别在意字体，请使用能够正确渲染出你所需要的字体的电脑，预览出想要的结果之后进行截图以获得图片形式的结果。在线打印出的图片很可能无法渲染出正确的字体，这取决于服务器有没有安装对应字体，但绝大多数情况没有。

预置的字体名字和简写有：
- 隶书：取值`lishu`, 或`lisu`,
- 楷体：取值`kai`, `kaishu`, 或`kaiti`
- 仿宋：`fangsong`
- 宋体：`song`, `songti`, `simsun`, `xinsongti`, 或`xst`
- 英文字体Times New Roman: `times`
- 黑体：`heiti`
- 微软雅黑：`yahei`,`msyahei`,`weiruanyahei`
- 文泉驿黑体：`wenquanyi`,`wqy`
- 幼圆： `youyuan`
- 新细明体：`mingti`, `ximingti`, `xinximingti`, `xxmt`
- 英文字体Helvetica: `helvetica`
- 英文字体Consolas: `consolas`

下面是几个示例。

```wtht
draw:
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; lishu ||| 隶书字体
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; 仿宋   ||| 仿宋字体（你可以使用中文字体名称）
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; kaiti ||| 楷体字体
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; yahei ||| 微软雅黑字体
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; times ||| Font Times New Romanblk
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; font; Arial ||| Font Arial
```
[wtht] # Fig Description...
noTitle: yes
use: labelExplain
figure:
  - blkFonts
[wtht]

[i] 如果我们没有指定字体，预览显示的字体大概是浏览器的默认字体，比如为`Inter, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif`，但当我们提供了`font`关键字，却没有提供正确的字体名称（或者你电脑中没有相应字体）时，会显示**宋体**及**Times**字体。字体可能是所有画图软件/工具都难以完美解决的问题，我们只能相对有限地显示和切换字体。

## 旋转

下面是一个文本旋转的示例，请注意，文本盒子的旋转中心点是它的锚点位置。

```wtht
draw:
  - label; p0; offset; pOffset; anchor; tm; w; lblW; h; lblH; align;left;||| 我没旋转
  - label; p0; offset; pOffset; anchor; tm; w; lblW; h; lblH;
      rotdeg; -30; color; red; ||| 我旋转了$-30\degree$
```
[wtht] # Fig Description...
noTitle: yes
use: labelExplain
figure:
  - blkRotate
[wtht]

## 文本内容

在三根竖线分隔符`|||`之后的部分就是我们的文本内容，我们可以在其中添加数学公式，但只能添加行内嵌入式的，不能是大块儿的。数学公式的渲染和显示使用的是$\KaTeX$模块([KaTeX: https://katex.org](https://katex.org))，请参考具体的网页内容来获取如何书写数学公式。([KaTeX指令支持列表](https://katex.org/docs/supported))

下面是一个使用数学公式的例子:

```wtht
draw:
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; ||| $x_{1,2} = \frac{-b\pm\sqrt{b^2-4ac}}{2}$
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; align; left; font; songti; ||| 拉普拉斯变换: \\n $F(s)=\int_{-\infty}^{+\infty}f(t)e^{-st}\text{d}t$
```

[wtht] # Fig Description...
noTitle: yes
use: labelExplain
figure:
  - blkKatexs
[wtht]

文本的输入有时候可能比较长，我们可以换行，但新行必须比原行缩进更多，否则可能解析出错。上面的第二个关于拉普拉斯变换的例子，我们可以把它写成三行。如下：

```wtht
draw:
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; align; left; font; songti; ||| 拉普拉斯变换: \\n $F(s)=\int_{-\infty}^{+\infty}f(t)e^{-st}\text{d}t$
  # 可以写成下面三行
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH;
    align; left; font; songti; ||| 拉普拉斯变换: \\n
    $F(s)=\int_{-\infty}^{+\infty}f(t)e^{-st}\text{d}t$
```
在文本很长时，我们可能需要手动地把我们想显示的文字进行分行显示，为此我们添加了一个**换行符**，`\\n`，请注意，不是`\n`，这里有一点现实考虑，在某种情况下，我们需要解释并显示`\n`，但`\\n`的情况则少很多，考虑到程序执行的情况，如果把`\n`定义成换行符，我们将无法在画图中显示`\n`，所以我们在前面多加了一个反斜杠。上面的代码示例中我们就使用了一个换行符。另外`\\r`，`\\r\\n`,`\\n\\r`也是换行符。


## 需要注意的点

1. 你不能添加一个**空白文本**，即分隔符`|||`后面不能是空白，如果是空白，将不会绘制任何内容。
```wtht
draw:
  - label; p0; offset; pOffset; anchor; tl; w; lblW; h; lblH; ||| # 没有任何文本内容，将不会绘制
```

2. 文本盒子的宽和高，使用`w`,和`h`作为参数的关键字名称，因而你如果定义了名为`w`或`h`的变量，在这里将无法使用，解决方法是换一个变量名字，通常情况下变量名应表征它的具体含义，因而一个字母的名字通常是不太够的，所以，建议你取一个长一点的名字，比如本文的例子中，文本盒子的宽用的是`lblW`,高用的是`lblH`。

```wtht
vars:
  w: 188
  h: 122
draw:
  # 这样将无法使用，文本框的宽和高将使用默认值(宽320，高度根据文本长度计算)
  - label; 0,0; anchor; tl; w;w;h;h; ||| label example
```

