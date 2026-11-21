# baby-steps-report

一个跨端 agent skill（支持 Antigravity、Claude Code、Codex）：撰写与重构**细致且真正易读**的科学推导报告（notes / 讲解体）——self-contained、step by step without handwaving、问题链组织、默认中文 XeLaTeX 编译为 PDF（按需双语）、附落盘验证脚本。

## 动机：同时对抗两种“不可读”

物理与数学推导报告常见两种死法，本 skill（v2）把两者一起堵住：

1. **过简（技术报告体）**：摘要一句塞五个结论；公式全塞在内联长句里；推导用“直接展开得到……”“容易验证……”跳过；符号不定义；推导外包给脚本引用。每条公式都对，但读者跟不上。
2. **过重（审稿防御体 / 版本补丁体）**：3 行无指标几何分解就能讲透却写半页四重下标；正文塞满下文用不上的防杠构造或代码级实现细节；每两段穿插“对比前稿式 (x.y) 区别于本稿”；行内插满“但须注意……不证明……”免责括号把节奏切碎；几十页纯白文字墙没有路标框。（尤其常见于长报告续写或吸收外部严格审计意见之后。）

一句话总结 v2 核心原则：**写作单位是“一步推导”，不是“一段结论”；细致服务于理解，不服务于防杠。**

## 适用范围与边界

**适用四类任务：**
- **从零写讲解/推导报告**：研究笔记、补全推导、专题讲解、总结讨论。
- **长报告可读性重构**：对已有难读报告直接动手重排故事线、直觉化证明、执行主线/附录分流并恢复视觉路标（交付干净最终版黑字 PDF + 简明改动说明，不先列清单让用户勾）。
- **已有讲解报告的追问补写**：对话追问先在对话里答，确认写入后再就地补写，延续同一讲解风格。
- **吸收外部严格性审核意见（如 Codex 审计）**：修真错、守文风——边界免责进节末 `notebox`，长技术补证沉附录，绝不因审计退化成防御体。

**两类明确不适用：**
- **正式论文**（paper / manuscript / PRL / PRB / SM）：受期刊篇幅与文体约束，不应写成讲解体。
- **用户自己写的严肃科研 notes**（通常为英文、偏论文手稿）：不套用本 skill 的展开讲解体，应照原逻辑和记号只补缺的、宁短勿啰嗦、能引直接引。

## 核心原则

1. **结论先行 + 问题链**：开头编号结论清单（长报告配首页导览 + 单页目录）；节标题本身是问题或判断，每节先用 2–3 句白话说动机再出公式。
2. **一步不跳，但走最短严格直觉路径**：禁用“直接展开得/显然”等跳步词，动机 → 行间公式 → 意义三拍推进；同时拒绝无意义的四重指标展开——直觉即严格。
3. **四分流纪律**：主线推导留正文；适用边界与防误读免责进节末 `notebox`；代码级实现与重复性泛函补证沉附录；全文用不上的防杠构造直接删。
4. **独立故事线，严禁版本补丁体**：锚定权威 notes 时就地抄出所需公式并用 PDF 式号标注；不拿被替代的旧草稿在正文里逐句对照编号（完整对比放文末独立节）。
5. **保住三类具象抓手**：极简玩具例的一行心算、贯穿全篇的显式手算锚点、相似对象的跨层辨析；反例与极限检验放在困惑发生处。
6. **吸收审计不丢文风**：真错原位修净，边界下沉 `notebox`，长补证入附录，改后回检节奏。
7. **清晰排版与视觉抽检**：一行一式、不 `\resizebox` 缩字号；长报告稳定使用 `lemmabox` / `keybox` / `notebox` 路标框；默认中文 XeLaTeX 编译零报错、零 Overfull `\hbox`，并对关键渲染页做视觉抽检。
8. **诚实验证**：Python / Mathematica 脚本落盘 `scripts/`，固定种子打印 `[PASS]` / `[FAIL]`；未运行则明说，证据标签放脚注或附录。

## 仓库结构

```text
baby-steps-report/
├── SKILL.md                     # 主控：双向总纲、四类模式工作流、适用范围硬分流
├── references/
│   ├── style-guide.md           # 风格细则：防过简 + 防过重、四分流、审计文风守护、
│   │                            #   数值与图注规范、七组真实对照实例（A–G）
│   ├── latex-template.md        # XeLaTeX 模板（三色路标框）、声明式标色防坑、编译与视觉抽检
│   └── checklist.md             # 交付前 24 项双向自审清单
└── README.md
```

## 安装（一套仓库多端复用）

先 clone 到本地固定目录，再按使用的 agent 软链接挂载：

```bash
git clone https://github.com/jxzhang97/baby-steps-report.git ~/Documents/AI/baby-steps-report

# Antigravity（全局自动发现）
mkdir -p ~/.gemini/config/skills
ln -s ~/Documents/AI/baby-steps-report ~/.gemini/config/skills/baby-steps-report

# Claude Code
mkdir -p ~/.claude/skills
ln -s ~/Documents/AI/baby-steps-report ~/.claude/skills/baby-steps-report

# Codex
mkdir -p ~/.codex/skills
ln -s ~/Documents/AI/baby-steps-report ~/.codex/skills/baby-steps-report
```

装好后新开会话即可自动发现并按需激活。

## 依赖

- **XeLaTeX**（TeX Live / MacTeX）；中文版默认用 `xeCJK` + macOS 系统字体（`Songti SC` / `Heiti SC` / `STFangsong`），其他系统按 `references/latex-template.md` 替换字体。
- **视觉抽检工具**（可选，推荐）：`pdftoppm`（Poppler）用于将关键 PDF 页渲染成 PNG 核查排版。
- **验证脚本**（可选）：Python 3（`sympy` / `numpy`）或 Mathematica（`wolframscript`）。
