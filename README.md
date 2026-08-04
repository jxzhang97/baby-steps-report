# baby-steps-report

一个 [Claude Code](https://claude.com/claude-code) skill：撰写"宝宝都能读"的科学推导报告——self-contained、step by step without handwaving、以问题链组织、中英双语 LaTeX 编译为 PDF、附落盘验证脚本。

## 动机

同一件事有两种写法。

**技术报告体**：摘要一句话塞五个结论；公式全部内联在长句里；推导用"直接展开得到……""容易验证……"跳过；名词与符号默认读者认识；证据以"[C7] 已由脚本验证"的形式外包。每条公式都对，但读者跟不上。

**讲解体**：开头先把结论按条写清楚；每一节回答一个问题，标题本身推动推理；每个推导步骤是"动机句 → 行间公式 → 意义句"三拍；所有符号和名词在首次出现处定义；反例和极限检验放在困惑发生的那一步旁边；每个结论旁边写明用了什么假设。相邻结论之间没有断桥——读者不动笔就能跟上。

这个 skill 把第二种写法固定成可执行的工作流程与自审清单。风格规则提炼自一批作者认可的逐步推导记录，并针对四份被否决的技术报告体旧文逐条总结失败模式（skill 内附四组真实的"旧写法 → 新写法"对照实例）。

## 核心原则

1. **结论先行**：全文以编号结论清单开头（"我先把结论写清楚"），先给终点再走路。
2. **问题驱动**：报告的组织单位是"一个需要回答的问题"，不是"一个知识主题"；节标题形如"为什么单纯的 $K\neq1$ 不会打开 gap？"，不是"模型与方法"。
3. **无断桥**：禁语清单（"直接展开得到""容易验证""显然"……）配拆解配方；每步操作有名有姓（"两式相乘""把第一项做指标替换 $j\to j-1$"）。
4. **意义句**：每条关键公式后立即说明它意味着什么、排除了什么、与原问题什么关系——意义句是推导的一部分，不是科普点缀。
5. **Self-contained**：目标读者是不了解该 framework 的新人；用户未声明"我已知道"的内容一律现场推导；每个符号、名词首次出现即定义。
6. **文字论断马上跟公式**：说"weight 均匀分布"，立即跟"$W(k)$ 与 $k$ 无关"。
7. **反例放在困惑处**：检验不是文末审计项目，而是放在误解会发生的那一步旁边（"同样 $K_\rho<1$，可以有 gap，也可以没有 gap"）。
8. **假设记账在现场**："到这里严格得到的是 X；要声称 Y 还必须加假设 A。"证据标签（脚本验证）只进脚注或附录。
9. **Notation 以用户 notes 为最高权威**：冲突处明确指出该怎么改；与 notes 的对应用节号/公式号锚定，绝不用 LaTeX 行号。
10. **双语交付**：`report_CN.tex` 与 `report_EN.tex` 内容严格对应，XeLaTeX 编译成 PDF，验证脚本落盘，中间文件清理干净。

## 仓库结构

```
baby-steps-report/
├── SKILL.md                     # 主控：触发条件 + 六阶段工作流
├── references/
│   ├── style-guide.md           # 风格细则：骨架、节奏、禁语清单与拆解配方、
│   │                            #   意义句措辞库、四组旧写法→新写法对照实例
│   ├── latex-template.md        # 中英文 XeLaTeX 模板、编译与清理流程
│   └── checklist.md             # 交付前 25 项定性自审清单
└── README.md
```

## 安装

前提：已安装 [Claude Code](https://claude.com/claude-code)。

**方式一：直接 clone 到 skills 目录**

```bash
git clone https://github.com/jxzhang97/baby-steps-report.git ~/.claude/skills/baby-steps-report
```

**方式二：clone 到工作目录再软链接**（便于改动与同步）

```bash
git clone https://github.com/jxzhang97/baby-steps-report.git ~/Documents/AI/baby-steps-report
ln -s ~/Documents/AI/baby-steps-report ~/.claude/skills/baby-steps-report
```

也可以装到某个项目的 `.claude/skills/` 下作为项目级 skill。装好后**新开一个 Claude Code 会话**即生效（skill 在会话启动时加载）。

## 使用

在 Claude Code 里通过斜杠命令或自然语言触发：

```
/baby-steps-report 用玻色化推导 t2=0.75 时口袋间过程的结果，notes 在 notation.tex
```

或者直接说："帮我写一份 step by step 的推导报告，讲清楚……，我的 notes 在……"。

给足三样输入，效果最好：

1. **notes 路径**——notation 与 convention 以它为准；
2. **知识边界**——明确说"我知道 X"（例如"我知道 sine-Gordon RG"），X 的标准结论就可以直接引用，其余一律现场推导；
3. **问题清单**——你想让报告回答的具体问题，每个问题都会得到直接的、显著标出的回答。

产出目录：

```
<report_dir>/
├── report_CN.tex   report_CN.pdf    # 中文版
├── report_EN.tex   report_EN.pdf    # 英文版（内容严格对应）
├── figs/                            # 图（如有）
└── scripts/                         # 验证脚本，头部注明验证的公式号
```

## 工作流程（六阶段）

| 阶段 | 内容 |
|---|---|
| 1 输入确认 | 确认 notes、知识边界、问题清单；缺了就问 |
| 2 notation 提取 | 通读 notes 建符号词典；冲突处明确指出并给修改建议 |
| 3 推导规划 | 列问题链（每节一个问题）；列易混淆概念簇；标注对应 notes 的节/公式号 |
| 4 写作 | 按 style-guide 十条核心规则写正文 |
| 5 辅助验证 | Python/Mathematica 复核关键代数，脚本落盘，结果进脚注/附录 |
| 6 交付与自审 | 双语编译出 PDF；过 25 项 checklist；清理中间文件 |

## 依赖

- **XeLaTeX**（TeX Live / MacTeX）；中文版用 xeCJK + macOS 系统字体（Songti SC 等），其他系统按 `references/latex-template.md` 的说明替换字体。
- 验证脚本（可选）：Python 3（sympy / numpy）或 Mathematica（wolframscript）。
