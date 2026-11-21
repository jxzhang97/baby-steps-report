# LaTeX 模板、编译与视觉抽检流程

默认交付中文版 `report_CN.tex/pdf`（或用户指定文件名）；仅在用户要求双语时增出 `report_EN.tex/pdf`。均使用 XeLaTeX 编译。中文版依赖 `xeCJK` 与 macOS 系统中文字体；其他系统先用 `fc-list :lang=zh | head` 查可用字体替换。

## 中文版序言（`report_CN.tex`）

```latex
% !TEX program = xelatex
\documentclass[11pt,a4paper]{article}

\usepackage{amsmath,amssymb,bm,mathtools}
\usepackage{newpxtext,newpxmath}
\usepackage[top=2.2cm,bottom=2.2cm,left=2.3cm,right=2.3cm]{geometry}
\usepackage{xeCJK}
\setCJKmainfont{Songti SC}[BoldFont=Heiti SC,ItalicFont=STFangsong]
\setCJKsansfont{Heiti SC}
\setCJKmonofont{STFangsong}

\usepackage{graphicx}
\usepackage{booktabs,array,multirow}
\usepackage[table]{xcolor}
\usepackage[most]{tcolorbox}
\usepackage{fancyhdr}
\usepackage{caption}
\usepackage{subcaption}
\usepackage{titlesec}
\usepackage{enumitem}
\usepackage[section]{placeins}
\usepackage[colorlinks=true,linkcolor=navy,citecolor=tealc,urlcolor=navy]{hyperref}

% Palette
\definecolor{navy}{HTML}{1B3B5F}
\definecolor{terra}{HTML}{C85A2B}
\definecolor{tealc}{HTML}{1B7A6E}
\definecolor{ink}{HTML}{1F2937}
\definecolor{muted}{HTML}{64748B}
\definecolor{bgnavy}{HTML}{F3F6FA}
\definecolor{bgteal}{HTML}{F1F8F6}
\definecolor{bgwarn}{HTML}{FDF6F0}

% Spacing & equation numbering
\linespread{1.26}
\setlength{\parindent}{2em}
\setlength{\parskip}{0.26em}
\allowdisplaybreaks
\setlength{\emergencystretch}{1.8em}
\renewcommand{\arraystretch}{1.18}
\numberwithin{equation}{section}
\AtBeginDocument{\normalsize\color{ink}}

% Section headings (do not force \clearpage between regular sections)
\titleformat{\section}
  {\Large\bfseries\color{navy}}
  {\thesection}{0.7em}{}
  [\vspace{2pt}{\color{navy!28}\titlerule[0.8pt]}]
\titleformat{\subsection}
  {\large\bfseries\color{navy!90!black}}
  {\thesubsection}{0.55em}{}

% Chinese labels
\renewcommand{\contentsname}{目\quad 录}
\renewcommand{\figurename}{图}
\renewcommand{\tablename}{表}
\renewcommand{\appendixname}{附录}
\renewcommand{\refname}{参考文献}
\captionsetup{font=small,labelfont={bf,color=navy},skip=5pt}

% Header & footer
\pagestyle{fancy}\fancyhf{}
\fancyhead[L]{\small\color{muted}\sffamily 〈报告短标题〉}
\fancyhead[R]{\small\color{muted}\thepage}
\renewcommand{\headrulewidth}{0.4pt}
\renewcommand{\headrule}{\hbox to\headwidth{\color{navy!25}\leaders\hrule height \headrulewidth\hfill}}
\setlength{\headheight}{22pt}

% Semantic visual landmark boxes
% keybox: milestones & core physical intuition
\newtcolorbox{keybox}[1]{
  enhanced,breakable,
  colback=bgteal,colframe=tealc,
  fonttitle=\bfseries\sffamily,
  title={{\hypersetup{linkcolor=white,citecolor=white,urlcolor=white}#1}},
  left=9pt,right=9pt,top=6pt,bottom=6pt,
  before skip=8pt,after skip=8pt,
  boxrule=0.85pt,arc=2.5pt
}
% lemmabox: key criteria, theorems & no-go statements
\newtcolorbox{lemmabox}[1]{
  enhanced,breakable,
  colback=bgnavy,colframe=navy,
  fonttitle=\bfseries\sffamily,
  title={{\hypersetup{linkcolor=white,citecolor=white,urlcolor=white}#1}},
  left=9pt,right=9pt,top=6pt,bottom=6pt,
  before skip=8pt,after skip=8pt,
  boxrule=0.85pt,arc=2.5pt
}
% notebox: scope boundaries, disclaimers & brief cross-route notes
\newtcolorbox{notebox}[1]{
  enhanced,breakable,
  colback=bgwarn,colframe=terra,
  fonttitle=\bfseries\sffamily,
  title={{\hypersetup{linkcolor=white,citecolor=white,urlcolor=white}#1}},
  left=9pt,right=9pt,top=6pt,bottom=6pt,
  before skip=8pt,after skip=8pt,
  boxrule=0.85pt,arc=2.5pt
}

\title{\bfseries\color{navy} 〈标题：直接说明报告回答什么问题〉}
\author{〈作者行〉}
\date{〈日期〉}

\begin{document}
\maketitle

\section{我先把结论写清楚}
% Short reports: numbered conclusions followed directly by TOC or Section 2.
% Long reports (20+ pages): fit Section 1 cleanly on Page 1, then \clearpage + single-page \tableofcontents + \clearpage.

\tableofcontents

\end{document}
```

## 英文版序言（按需双语时使用 `report_EN.tex`）

在中文版序言基础上删去 `xeCJK` 四行与中文标签重定义，其余完全一致（仍用 XeLaTeX 编译，确保两版排版与公式编号严格对应）。

## 正文排版细则

- **行间公式**：默认 `equation` / `align`；一行一式，不用逗号或 `\qquad` 把无关式子硬挤在同一行；写不下就用 `align` 换行对齐，**严禁用 `\resizebox` 或缩字号硬塞**。
- **加框克制**：`\boxed{…}` 只给回答核心问题的关键结果；章节级视觉路标用 `lemmabox` / `keybox` / `notebox`。
- **公式引用**：一律 `式~\eqref{eq:xxx}` / `Eq.~\eqref{eq:xxx}`，避免模糊的“上式”。
- **证据脚注**：`\footnote{式~\eqref{eq:xxx} 已由 scripts/check\_xxx.py 数值验证至 $10^{-12}$。}`
- **源码注释**：LaTeX 源码内注释一律使用英文。
- **局部补写标色防坑**（仅当用户要求标出本轮新增内容时）：使用声明式写法 `{\color{orange}\bfseries …}` 或自定义环境；**切勿**用 `\textcolor{orange}{\textbf{…}}` 包裹含空行或 `align` 的段落，否则会触发 `Runaway argument` 报错。

## 编译、版面抽检与清理

1. **编译 2–3 遍**（确保目录与交叉引用完全收敛）：

```bash
xelatex -interaction=nonstopmode report_CN.tex
xelatex -interaction=nonstopmode report_CN.tex
xelatex -interaction=nonstopmode report_CN.tex
```

2. **日志核查**：检查 `.log` 确保：
   - 0 编译错误（无 `Undefined control sequence` / `Runaway argument`）；
   - 0 未定义引用（无 `undefined references` / `There were undefined references`）；
   - 0 严重 `Overfull \hbox`（超出版面的公式或长行必须折行修复）。

3. **关键页视觉抽检（长报告或含复杂框/图时必做）**：将首页、目录、核心推导页、图表页与附录首页渲染成 PNG 抽看（例如用 `pdftoppm -png -r 140 -f <p> -l <p> report_CN.pdf /tmp/page`），核查：
   - 首页与目录是否整洁，无奇怪留白；
   - 提示框、跨页公式、图注、附录标题无孤立标题（widow/orphan）或重叠溢出；
   - 确认无误后删掉临时 PNG。

4. **清理编译中间文件**：

```bash
rm -f *.aux *.log *.out *.toc *.fls *.fdb_latexmk *.synctex.gz *.bbl *.blg *.nav *.snm *.vrb
```

最终目录只保留 `.tex` / `.pdf`、`figs/`、`scripts/`（以及重构时可选的简短 `改写说明.md`）。
