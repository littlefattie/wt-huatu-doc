# 介绍

吾题画图 `wt-huatu`，是一个通过编程的方式来进行画图的软件库。或者说我们发明了一种描述图形元素间相互关系的语言，名字叫做 `wt-huatu`，然后我们使用Typescript/Javascript对这门新的语言进行了代码实现，因为目前只有此一种实现，所以我们可以使用`wt-huatu` 来指代此新的“图形描述语言”或者这里的Typescript/Javascript实现。

## 轮子为什么而造

关于画图，我们有非常非常多的工具，给我一张纸一支笔，我大概也可以画出所有我需要用图形表达的任何内容，然后通过照相机或者扫描仪，我的图纸可以很容易地被转换成数字格式然后存储在计算机系统中。而在原生的计算系统中，我们也有很多的软件可以用来画图，比如直接输出位图(Bitmap)的mspaint， Photoshop等，可以输出矢量图形(Vector Graphics)的 InkScape, CoralDraw，Adobe Illustrator，Word/PPT, Asymptote 等等。然后，灵魂之问到来，**你搞这个东西有啥用呢？**

答案很简单，**它并没有什么大的用处，它只是提供了关于画图的另一种可能**。

发明这个工具的出发点来自于一个很简单的需求，即如何让图形中的一些元素能相互关联起来。当我使用Markdown写一篇技术文档的时候，我需要一张图片，比如我要解释如何求解两个圆的交点，那么我需要画两个能够相交的圆，很自然地，以这两个交点为端点我可能要画一条线段(弦)， 然后过一会儿，我要描述两个圆相切，那么我需要改变条件重新画这两个圆，并且很有可能我要画一条在切点的切线，然后继续下去，我可能需要画某个圆的一个内接三角形，再然后是内接三角形的的内切圆，以及从内切圆的圆心出发到达某一点的线段，等等。这些都是基础的几何学问题，相关参数的求解并不难，只是几行代码的问题，但如果当给定两个圆的基本参数(圆心位置，半径等)之后，其他的参数都能自动地求解计算出来，(所派生出来的)图形元素也能自动地确定位置尺寸等，是不是能省很多事呢？无论两个圆大小怎么变，那根弦总是连接两个交点，无论那个内接三角形的顶点是什么，那条线段的一个端点永远都在内接三角形的内切圆的圆心位置，等等等等。

因为通常技术文档的书写跟图形的交互比较密切，比如需要反复地去引用图形中的某一个元素，更改变量，然后描述变化等，如果能把图形这样一个具象化的二维事物变成一些具有特定结构及语义的文本直接嵌入到文档的叙述文本中，是一件很友好很方便的事情，我们可以像更改文章某句话那样去更改图形中的一些元素，比如一条线段的长度和颜色，某个矩形的旋转角等。

在此之前，也曾学习过`PGF/TikZ`, 也是很好用的工具，也是通过类似于编程的方式来定义然后绘制出图形。但通常情况下，使用`PGF/TikZ`来绘制图形元素的时候，我们需要给出确切的点的坐标，然后几何关系的计算等，编程的味道太过浓烈，然后因为它使用$\LaTeX$的语法，而$\LaTeX$的学习曲线相对来说比较陡峭，在这些动机的驱动之下，一个新的轮子被搓出来了。

下面是一个简单的例子，我们画两个圆，然后交点和圆心构成一个等边三角形，像下面这样:

[wtht]
name: circlesAndTriangle
# Example WT-HUATU design file
# --- Vars ------
noTitle: yes
vars:
  r: 100
  degAB: datan(0.25/1.25)

# --- Points ------
points:
  pA: (0, 0)

# --- Shapes ------
shapes:
  lnAB: line; pA; -degAB
cpoints:
  pB: lnAB; r

shapes2:
  cA: circle; pA; r
  cB: circle; pB; r

xpoints2:
  pC: cA; cB; n;
  pD: lnAB; cA; w;
  pE: lnAB; cB; e;

shapes3:
  triABC: triangle; pA; pB; pC;

# --- Draw --------
draw:
  - cA; fill, none; stroke; 1, cc
  - cB; fill, none; stroke; 1, cc
  - triABC; fill, yellow; stroke; 1, cc
  - circle; pA; 4 ||| fill; grey
  - circle; pB; 4 ||| fill; grey
  - circle; pC; 4 ||| fill; grey
  - circle; pD; 4 ||| fill; grey
  - circle; pE; 4 ||| fill; grey
  - label; at; pA; w; 20; h; 22; anchor; mr ||| $A$
  - label; at; pB; w; 20; h; 22; anchor; ml ||| $B$
  - label; at; pC; w; 20; h; 22; anchor; bm ||| $C$
  - label; at; pD; w; 20; h; 22; anchor; tr ||| $D$
  - label; at; pE; w; 20; h; 22; anchor; bl ||| $E$
[wtht]


`PGF/TikZ`($\LaTeX$)的语法如下:


```tex
\usetikzlibrary {backgrounds,calc,intersections,through}
\begin{tikzpicture}[thick,help lines/.style={thin,draw=black!50}]
  \def\A{\textcolor{input}{$A$}}
  \def\B{\textcolor{input}{$B$}}
  \def\C{\textcolor{output}{$C$}}
  \def\D{$D$}
  \def\E{$E$}

  \colorlet{input}{blue!80!black}
  \colorlet{output}{red!70!black}
  \colorlet{triangle}{orange}

  \coordinate [label=left:\A]  (A) at ($ (0,0) + .1*(rand,rand) $);
  \coordinate [label=right:\B] (B) at ($ (1.25,0.25) + .1*(rand,rand) $);

  \draw [input] (A) -- (B);

  \node [name path=D,help lines,draw,label=left:\D]   
        (D) at (A) [circle through=(B)] {};
        
  \node [name path=E,help lines,draw,label=right:\E]  
        (E) at (B) [circle through=(A)] {};

  \path [name intersections={of=D and E,by={[label=above:\C]C}}];

  \draw [output] (A) -- (C) -- (B);

  \foreach \point in {A,B,C}
    \fill [black,opacity=.5] (\point) circle (2pt);

  \begin{pgfonlayer}{background}
    \fill[triangle!80] (A) -- (C) -- (B) -- cycle;
  \end{pgfonlayer}

\end{tikzpicture}
```

`wt-huatu`的语法如下:

```wtht
# Example WT-HUATU design file
# --- Vars ------
vars:
  r: 100
  degAB: datan(0.25/1.25)

# --- Points ------
points:
  pA: (0, 0)

# --- Shapes ------
shapes:
  lnAB: line; pA; -degAB
cpoints:
  pB: lnAB; r

shapes2:
  cA: circle; pA; r
  cB: circle; pB; r

xpoints2:
  pC: cA; cB; n;
  pD: lnAB; cA; w;
  pE: lnAB; cB; e;

shapes3:
  triABC: triangle; pA; pB; pC;

# --- Draw --------
draw:
  - cA; fill, none; stroke; 1, cc
  - cB; fill, none; stroke; 1, cc
  - triABC; fill, yellow; stroke; 1, cc
  - circle; pA; 2 ||| fill; grey
  - circle; pB; 2 ||| fill; grey
  - circle; pC; 2 ||| fill; grey
  - circle; pD; 2 ||| fill; grey
  - circle; pE; 2 ||| fill; grey
  - label; at; pA; w; 20; h; 22; anchor; mr ||| $A$
  - label; at; pB; w; 20; h; 22; anchor; ml ||| $B$
  - label; at; pC; w; 20; h; 22; anchor; bm ||| $C$
  - label; at; pD; w; 20; h; 22; anchor; tr ||| $D$
  - label; at; pE; w; 20; h; 22; anchor; bl ||| $E$
```

## 目标应用

- 设计Logo，图标等
- 绘制示意图，原理图，流程图等
- 绘图文档化及自动化

## 技术栈

- 输入
  - 格式： yaml文件格式的子集，只接受`key: value` 键值对形式和 `- list item` 列表项形式
  - 内容： 描述图形的相关定义和结构
- 输出
  - SVG格式图片： 本程序依赖于SVG (Scalable Vector Graphics, https://www.w3.org/Graphics/SVG/)，图形的渲染和绘制依赖于浏览器及相关可以解析SVG的软件或程序.




