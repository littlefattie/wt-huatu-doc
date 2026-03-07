# Introduction

`wt-huatu` here what we define could be a software library aiming to draw some graph or chart, or some language describing how to draw a graph/chart, like the C programming language describing how the program is logically organized and executed, we can use this "language" to describe what we are going to draw and how to place them all together.

Upon this "language", we could make implementations using different languages, Javascript/Typescript, Python, Go, etc. Because we now have this only implementation using Typescript/Javascript, we could also use the name `wt-huatu` to refer the implementation.

## How it comes

When drawing something, we have a lot of tools. Giving me a pen and a paper, I can draw anything I could image, and after scanning or photographing, it is in the cyber world. Even in the native cyber world, we have a lot of software tools to draw, like `mspaint`, `Photoshop` which could draw directly bitmap images, and `InkScape`, `CoralDraw`, `Adobe Illustrator`, `Word/PPT`, `Asmpotote` etc. which could also be used to draw vector graphics. There seems no reason to re-invent the wheel.

An ordinary round rubber-tyred-wheel is not needed to be invented anymore, but a rubber-tyred-wheel with teeth or texture on the tyre surface is maybe a little worthy to be invented. Here is the reason.

When I am writing some tech passage, say, describing how and when two circles would intersect or touch each other, I need draw a graph with two intersecting circles, upon the intersection, I probably need to draw a line segment between the two intersection points, and then I might draw a triangle with the two points and some other point; with the triangle, I might need to draw some inner circle of it. All these circles, line segments, triangles are connected to each other, geometrically dependent and constraint. These geometry stuff is not difficult, we can solve all the shapes using **several lines of code** per the inputs, which are the circle radius and center locations. But it would be very good idea and convenient if I can get all the shapes **automatically** derived and resolved when I update the inputs. So I define some inputs and shapes, the final drawing is composed of the shapes defined, when some adjustment required, what I need to do is modify the values of the inputs, then I get a new graph.

This idea is not new, like `PGF/TikZ`, or `Asymptote` both could serve the purpose of inputs/variables and geometry constraints, but they have more taste of programming, and even for programmers, they have steep learning curves. So this new language `wt-huatu` is invented. when I want a circle, I just write `c1: circle; pC1Center; rC1`, or `c2: circle; pC2Center; rC2`, to get intersection point we can just define a new `xpoint` and present as `pxC1C2n: c1; c2; n` (n means the norther one of the two points). It is intuitive and quite readable.

To make a more fruitful impression, we can read some example here, we build a regular triangle with centers and one intersection point of the two same-radius circles, like this:

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


With `PGF/TikZ`($\LaTeX$), we present like this:


```tex
\usetikzlibrary {backgrounds,calc,intersections,through}
\begin{tikzpicture}[thick,help lines/.style={thin,draw=black!50}]
  \def\A{\textcolor{input}{$A$}}     \def\B{\textcolor{input}{$B$}}
  \def\C{\textcolor{output}{$C$}}    \def\D{$D$}
  \def\E{$E$}

  \colorlet{input}{blue!80!black}    \colorlet{output}{red!70!black}
  \colorlet{triangle}{orange}

  \coordinate [label=left:\A]  (A) at ($ (0,0) + .1*(rand,rand) $);
  \coordinate [label=right:\B] (B) at ($ (1.25,0.25) + .1*(rand,rand) $);

  \draw [input] (A) -- (B);

  \node [name path=D,help lines,draw,label=left:\D]   (D) at (A) [circle through=(B)] {};
  \node [name path=E,help lines,draw,label=right:\E]  (E) at (B) [circle through=(A)] {};

  \path [name intersections={of=D and E,by={[label=above:\C]C}}];

  \draw [output] (A) -- (C) -- (B);

  \foreach \point in {A,B,C}
    \fill [black,opacity=.5] (\point) circle (2pt);

  \begin{pgfonlayer}{background}
    \fill[triangle!80] (A) -- (C) -- (B) -- cycle;
  \end{pgfonlayer}

  \node [below right, text width=10cm,align=justify] at (4,3) {
    \small\textbf{Proposition I}\par
    \emph{To construct an \textcolor{triangle}{equilateral triangle}
      on a given \textcolor{input}{finite straight line}.}
    \par\vskip1em
    Let \A\B\ be the given \textcolor{input}{finite straight line}.  \dots
  };
\end{tikzpicture}
```

with `wt-huatu` we write like this:

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

## Tech stack

- Design input
  - Format: we use the file format of **yaml** but only limit features, only accept `key: value` form and item list like `- list item` 
  - Content: the parameters and hierarchical defines of shapes and drawings
- Output
  - Image in SVG format: The library depends on SVG (Scalable Vector Graphics, https://www.w3.org/Graphics/SVG/). The visual renderer of SVG image/graph is Web Browser or some other equivalent SVG viewers etc.





