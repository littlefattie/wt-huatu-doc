# 快速上手

因为此`wt-huatu`是一种“图形描述语言”，所以我们首先要做的就是按照这种语言的格式和语法来进行“诉说”或“描述”，以完成我们对图形的设计。
为了及时反馈和更新我们所添加的设计输入是否已经正确地被解析并绘制，我们需要一个预览器。所以在你进行任何尝试之前，请打开我们的编辑器，在其中编写关于你的设计的描述，并打开预览以查看最新的渲染结果。

## 编辑器

我们基于微软出品的`Monaco Editor`开发了专门的编辑器，您可以在其中编写您自己的设计文件，保存在本地或者服务器（需要您注册帐号）上，地址为：[https://wt-huaut.org/editor/](https://wt-huaut.org/editor/)

## 设计输入/源文件

考虑到使用此`wt-huatu`进行绘图设计类似于一种编程行为，我们可以把你的设计称之为源文件。同时你的这份设计会输入到一个程序中来进行解析和渲染，所以可以称之为设计输入，或者设计文档。至于该如何称呼，可以全随你的心意。在下文的叙述中，我们更多地采用设计文档的叫法。

## 设计文档

### 文档结构

设计文档采用yaml文件格式（如果你是编程人员，并对此感兴趣，可参考官方网站[https://yaml.org](https://yaml.org)），并使用简化的语法子集来作为语法。一个基本的设计文档大概呈现为如下的样子。

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

我们只用两种形式的语法，即`key:value`键值对和短线开头的列表，以及将键值对或列表作为`value`赋值给`key`的嵌套形式。这里需要注意的是，如果定义列表，短线后面一定至少保留一个空格再接其他内容，否则将会被视为上一行未结束的部分，或者会引起解析错误，即我们写`-list item 1`通常不会被解析为列表项，而要写成`- list item 1`。

当然，我们再进行设计文档的编写时需要使用预先定义的关键字，也即前面样例中的各种`TAG`需要写成特定含义的一些字符。目前我们设计了两种类型的设计文档，一种是**普通画图**，一种是**组件** (component).

### 普通画图

普通画图的全要素模板示例如下:
```wtht
# 全要素画图模板

# 你可以写一个标题，是可选的，并不必须
title: optional title, if you would like to set

# 可以进一步写点描述
desc: Example Design file

# 你可以指定画图的尺寸大小，请注意这是可选的，而且并不会在预览视图中显式
size: 400, 600

# 你可以指定一个画图的外边界，这样超过这个范围的内容将不会显式
# 请注意，在最终的输出任务(job)中，如果没有指定任务自己的viewbox，那么这里的viewbox设置
# 将应用于全局，也即对每个任务都有效
#viewBox: -20, -100; 100; 100

# 变量
vars:
  a: 60
  b: 80

# 点
points:
  pC1: (0, 0)
  pC2: (b, 0)

# 基本图形定义
shapes:
  c1: circle; pC1; a
  c2: circle; pC2; b
  line1: line; (0,0); 30;
  lineseg1: line; line1; -20; 100
  c2moved1: movex; c1; 50

# 从前面定义的基本图形所计算/派生出来的点
cpoints:
  pcC1: c1; 45
  pcC2: c2; 90

# 基本图形间彼此的交点
xpoints:
  pxC1C2_N: c1; c2; n
  pxC1C2_S: c1; c2; s

# 点序列
pointSeries:
  psPC: pcC1; pcC2;
  psL1: lineseg1; 0: 0.1: 1
  # 这里特殊参数 x 表示求解全部c1和c2的交点，然后添加到点序列中
  psC1xC2: c1; x; c2;

# 图形序列
shapeSeries:
  ss1: circle; psL1; 2: 0.3: 120; cross

# 在前面第一组的点/图形/交点/计算点/序列等之后，我们可以指定第二/三/四组，
# 这样在第一组中定义的交点就可以在第二组中定义基本图形时被引用
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

# 你可以现场定义一个组件，或者指定一个URL通过读取来引用一个组件
components:
  comp1:
    vars:
      a:3
    draw:
      - circ; (0,0); a ||| stroke, 1, aqua
  comp2: https://wt-huatu.org/component/some-component-defined.yml

# 定义好组件之后，你可以“放置”组件到画图中，请注意这里放置只是实例化，要在最终画图中呈现
# 你还需要在画图任务中调用
placements:
  plcComp1: comp1; 100, 50; 
  plcComp2: comp2; 200, 100;

# 如果基本图形不能满足需求，你可以在这里设计复杂的路径，通过设计每一个小片段，
# 你可以自由地设计出非常复杂的形状，比如扇形等。你还可以使用'close'关键字，
# 将当前片段的终点直线连接到它前面最近的没有结束的那个片段的起点，来现一个封闭的图形
paths:
  complexPath1:
    - line; (0, 0); (50, 50)
    - arc; (50, 50); 30; 40; 0; cw; large; (60, 72)
    - line; (60,72); (-10, 90)
    - close

# 如果你想对图形进行标记，你可以在这里设计这些标记，比如为线段添加箭头等
markers:
  arrow1: arrow; len; 8; color; cc;
  arrow2: arrow-double; len; 12; color; cc; k;0.4
  arrow3: arrow-simple; len;10; cc; smooth

# 你也可以在这里设计一些图案，来填充你所涉及的图形，比如斜线，点，方格子等
patterns:
  ptn1: type;lines; density, 12; color; yellow; linewidth, 4; deg; -45;
  ptnGrid1:
    type: grid
    size: 4
    color: maroon
    linewidth: 1
    sparsek: 0.3

# 蒙版/遮罩，你可以设计一些蒙版来过滤你想要绘制的画图，模块，图层等
clips:
  clip1:
    - rect; -50, -50; 250, 50

# 你可以预先定义一些图形绘制格式，比如描边，填充等，这样当你为不同的图形设置格式时
# 就可以指定为这些预先定义好的格式，而不用再每一项都重新设置
styles:
  sty1Fillred: fill, red, 0.6;
  sty2Stroke2black: stroke,2, black;
  sty3complicated: fill, pink, 0.2; stroke, 1, aqua; pattern; ptnGrid1

# 你也可以指定一些用于标签的格式
labelStyles:
  lblSty1: 
    color: red
    font:  times
    align: center;
    size: 8

# 你可以组装一些符号，以便于绘制更复杂的复兴，比如设计一个符号的阵列
symbols:
  triangle1: 
    - triangle; 0, 2; 0, -2; 4, 0 ||| fill, green;
    - circ; 2, 0; 0.5 ||| fill, white;

# 有了符号以后，你就可以设计一个阵列了
arrays:
  array1: triangle1; polar; center; c2.c; count; 8; 
          startdeg; 0; deltaDeg; 30; r; c2.r ;scale;3.5; aligncenter

# 你可以设计一个模块，来包含各种你想要绘制的元素，即把它们“组合”到一起。
# 在组合时，你可以指定风格，也可以不指定，在最终将这些模块绘制出来的时候你还可以继续指定。
blocks:
  blk1:
    - c1;
    - c2;

# 你还可以设计图层，图层和模块几乎是相同的，但有一点不同，它在绘制时不可以指定缩放旋转，
# 以及风格等。它只是一个容器，且不可以再被其他的图层包含。
layers:
  layerBackground:
    - rect; -100, -100; 220, 150 ||| stroke, 1, aqua; fill, #eee

# 如果你设计此文件最终只需要输出一幅画图，那么你可以把所有要绘制的元素都列在“draw”下面。
# 这是默认的一个“输出任务”
draw:
  - c1; stroke; 1, red; fill, red, 0.2; opaque; 0.3
  - c2; stroke; 0.5, black; fill, none;
  - lineBetweenXpoints; stroke, 2 blue; marker; arrow1
  - circ; psPC(1); 3; ||| fill, yellow;

# 你还可以设计很多个输出任务，把它们放到“jobs”下面，这样你就可以通过一份设计文件来输出
# 不同的图形来，比如相同形状，但填色不同，或者一幅带参考线，而另一幅不带，或者一幅是全图，
# 另一幅是部分细节放大等
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
  labelTest:
    - plcComp1;
    - label; lblSty1; w; 90; h;20; at; 25,0;anchor; middleleft;
      background; lightgrey;left ||| This some label style test
    - circ; 25,0; 2 ||| fill, blue
  blockTest:
    - blk1; stroke, 2, magenta; fill, none;
```

### 组件

组件事实上也是一个画图，我们将它封装成一个单独的模块，放置在一个普通画图中。它除了不可以定义以下的内容外，和普通画图几乎一样。

- **jobs**, 组件不可以定义输出任务，它将有且只有一个任务，必须都写在`draw`下面
- **clips**, 组件不可以定义蒙版/遮罩
- **components** 和 **placements**, 组件不可以再包含另一个组件，即组件不支持嵌套，因此，组件中也不可以定义组件的放置/实例
- **symbols** 和 **arrays**, 组件不支持定义及使用符号，因此也不支持符号阵列
- **blocks**, 组件不支持模块
- **layers**, 组件也不支持使用图层

除此之外，组件还支持两个额外的属性：

1. **Input Var**, (**输入变量**)，通过添加`inputVars`，你可以定义一些输入参数，比如你可以定义一个组件，它只包含一个矩形，然后你可以把矩形的长和宽定义为输入变量，这样当你在画图中引用并放置这个组件时可以指定不同的长/宽参数，以便得到不同的矩形。
2. **Port**,(**端口**)，你可以为组件定义一些端口，然后在将多个组件放置到画图中以后，你可以添加一些连线(wire)将端口连接起来。比如你可以将电阻定义为一个组件，它有两个端口，然后你可以放许多个电阻，然后把他们的端口用线连起来，这样你就得到了一个电阻网络。

当我们使用**组件**的时候，我们可以把它当作一个黑盒子，而我们能看到的就只有输入变量 `inputVars` 和 端口 `ports`.

组件的全要素模板大概是这样子:

```wtht
# 你可以显式地定义一个 type(值为component)，来标明此设计文档为组件。
type: component

# 定义输入变量
# 所有在这里给出的值将是默认值，在放置该组件时如果没有给定新的值，那么将使用这里定义的默认值。
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

# 定义变量，像普通画图那样
vars:
  bw: $width        # 如果你要引用输入变量，在它的名字前添加美元符号$
  bh: $height       # 输入变量在解析时将被“替换”成它的实际值
                    #（前面的默认值，或者放置时给定的新值）
  lportW: bw/10
  lportH: bh/10
  lportDiag: sqrt(lportW * lportW + lportH * lportH) * 1.1

  cornerShrink: $corner *(1 - sqrt(0.5))

# 定义点，像普通画图那样
points:
  pCenter: (0, 0)
  pne: pCenter.x + bw/2 - cornerShrink, pCenter.y - bh/2 + cornerShrink
  pse: pCenter.x + bw/2 - cornerShrink, pCenter.y + bh/2 - cornerShrink
  pnw: pCenter.x - bw/2 + cornerShrink, pCenter.y - bh/2 + cornerShrink
  psw: pCenter.x - bw/2 + cornerShrink, pCenter.y + bh/2 - cornerShrink
  
# 定义图形
shapes:
  container: rect; bycenter; pCenter; bw; bh;
  lne: line; pCenter; pne
  lse: line; pCenter; pse
  lnw: line; pCenter; pnw
  lsw: line; pCenter; psw

# 所有需要被画出来的元素都需要列在draw下面
draw:
  - container; fill; $fill; stroke; $border, $borderWidth; radius; $corner
  - label; at; pCenter; anchor; center; w; bh; h; bh ||| $content

# 这这里，我们定义矩形的八个端口，即东/南/西/北/东北/东南/西南/西北
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

### 最小设计

如果不考虑复杂的设计，最简单的情况，我们可以使用如下两行来绘制一条线段。

```wtht
draw:
  - line; 0, 0; 100, 100; ||| stroke, 1, cc;
```
