# LaTeX 模板与编译流程

两份报告都用 XeLaTeX。中文版依赖 xeCJK 与 macOS 系统中文字体；若编译环境没有这些字体，先用 `fc-list :lang=zh | head` 查可用字体再替换。

## 中文版序言（report_CN.tex）

```latex
% !TEX program = xelatex
\documentclass[11pt]{article}

\usepackage{amsmath,amssymb,bm,mathtools}
\usepackage{newpxtext,newpxmath}          % 西文与数学字体
\usepackage[margin=2.5cm]{geometry}
\usepackage{xeCJK}
\setCJKmainfont{Songti SC}[BoldFont=Heiti SC]
\setCJKsansfont{PingFang SC}
\setCJKmonofont{STFangsong}
\usepackage{graphicx}
\usepackage{booktabs,array}
\usepackage[table]{xcolor}
\usepackage[most]{tcolorbox}
\usepackage{fancyhdr}
\usepackage[colorlinks=true,linkcolor=blue!55!black,
            citecolor=teal!60!black,urlcolor=blue!55!black]{hyperref}
\usepackage{enumitem}

\allowdisplaybreaks                        % 长推导允许跨页
\numberwithin{equation}{section}           % 公式按节编号

% 结论盒与警告盒（不要滥用；正文叙事要能独立立住）
\definecolor{verd}{RGB}{0,90,80}
\definecolor{warn}{RGB}{150,60,0}
\definecolor{keybg}{RGB}{245,248,246}
\newtcolorbox{conclusionbox}[1]{enhanced,breakable,colback=keybg,
  colframe=verd,fonttitle=\bfseries,title={#1},
  left=6pt,right=6pt,top=4pt,bottom=4pt,boxrule=0.9pt,arc=2pt}
\newtcolorbox{pitfallbox}[1]{enhanced,breakable,colback=orange!4,
  colframe=warn,fonttitle=\bfseries,title={#1},
  left=6pt,right=6pt,top=4pt,bottom=4pt,boxrule=0.8pt,arc=2pt}

% 中文界面名
\renewcommand{\contentsname}{目\quad 录}
\renewcommand{\figurename}{图}
\renewcommand{\tablename}{表}
\renewcommand{\appendixname}{附录}
\renewcommand{\refname}{参考文献}

\pagestyle{fancy}\fancyhf{}
\fancyhead[L]{\small\itshape 〈报告短标题〉}
\fancyhead[R]{\small\thepage}

\title{\bfseries 〈标题：直接说明报告回答什么问题〉}
\author{〈作者行〉}
\date{〈日期〉}

\begin{document}
\maketitle
\tableofcontents

\section{我先把结论写清楚}
% 编号结论清单……

\end{document}
```

## 英文版序言（report_EN.tex）

在中文版序言基础上删去 `xeCJK` 四行与中文界面名重定义，其余相同（仍用 XeLaTeX 编译，保证两版排版一致）。

## 正文格式约定

- 行间公式默认 `equation`/`align`；多步演算 `align` 且 `&` 对齐，一行一个操作。
- 关键结果 `\boxed{…}`；结论性文字可写 `\boxed{\text{…}}`。
- 引用公式一律 `式~\eqref{eq:xxx}` / `Eq.~\eqref{eq:xxx}`。
- 脚注承载证据标签：`\footnote{式~\eqref{eq:xxx} 已由 scripts/check\_xxx.py 数值验证至 $10^{-12}$。}`
- 图放 `figs/`，`\includegraphics[width=…]{figs/…}`；图注要能独立读懂（说清坐标轴、参数、结论）。

## 编译与清理

```bash
xelatex -interaction=nonstopmode report_CN.tex
xelatex -interaction=nonstopmode report_CN.tex   # 第二遍：目录与交叉引用
xelatex -interaction=nonstopmode report_EN.tex
xelatex -interaction=nonstopmode report_EN.tex
```

编译后检查 `.log` 里没有 `undefined references`／`Undefined control sequence`；有则修复重编。

交付前清理中间文件：

```bash
rm -f *.aux *.log *.out *.toc *.fls *.fdb_latexmk *.synctex.gz *.bbl *.blg *.nav *.snm *.vrb
```

最终目录只留 `report_CN.tex/pdf`、`report_EN.tex/pdf`、`figs/`、`scripts/`。
