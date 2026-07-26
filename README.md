<div align="center">

# Oh My Hackathon

</div>

> 一个 Skill-first 的黑客松参赛引擎——从比赛题目,一路推进到 idea、计划、路演 PPT、项目说明书,端到端一条龙。

![中文](https://img.shields.io/badge/中文-red)
![License](https://img.shields.io/badge/license-MIT-green)
![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey)
![Stages](https://img.shields.io/badge/stages-5-blueviolet)

<!-- TODO: 替换为产出示例图 <img src="assets/example.png" width="800"> -->

请将下方提示词复制给你的 Agent,实现一键安装与配置:

> Clone and set up https://github.com/MargoGuo/oh-my-hackathon, then help me prepare full hackathon deliverables from my competition brief.

---

一场黑客松真正耗时的,往往不是写代码,而是把 idea 讲清楚、把价值说明白。题目一发布,团队常常要在有限时间内同时产出:idea 陈述、产品规划、PRD、路演 PPT、项目说明书、架构图。

这些交付物不是孤立的文档,而是一条连贯的叙事:从“题目要什么”到“我们做什么”再到“为什么能赢”。把这条链路跑通,需要三个核心能力:

- **端到端(Pipeline)** —— 不只生成某一类文档,而是从题目到交付物的完整链路。
  idea 不是凭空想,计划不是脱缰跑,PPT 不是花架子——每一份产出都对齐评审标准,前后衔接。
- **求真(Truthfulness)** —— 数据与能力可追溯,不发明。
  数字、技术栈、评审覆盖度都从题目官方页 / 项目 repo / 真实来源拉取,拿不到就标“待补”,绝不编造。
- **可控(Ownership)** —— 关键节点人工拍板,交付后仍可改。
  idea 和计划必须用户确认才推进;PPT 导出为可编辑的原生形状(非整页图),赛后还能在 PowerPoint 里继续改。

Oh My Hackathon 围绕这三个理念构建:

**Pipeline(端到端) · Truthfulness(求真) · Ownership(可控)**

在此基础上,Agent 作为智能协作者,参与参赛文档构建的全过程——从题目解析、idea 发散、计划落地,到 PPT 精修与飞书文档发布。

<!-- TODO: 补工作流演示 GIF <img src="assets/workflow-demo.gif" width="800"> -->

---

## 为什么选择 Oh My Hackathon?

这个领域已有不少工具,它们解决的问题不同。Oh My Hackathon 不试图复刻一个 PPT 生成器或文档模板站,而是选择另一条路线:**全流程、Agent 原生、求真、可改。**

| 能力 | 通用 AI 对话 | 在线 PPT 工具 | LaTeX / Beamer | 手搓 Office | __Oh My Hackathon__ |
| --- | --- | --- | --- | --- | --- |
| 题目 → 说明书端到端 | ❌ | ❌ | ❌ | ❌ | ✅ |
| idea / 计划 / PRD 全覆盖 | △ | ❌ | ❌ | ❌ | ✅ |
| 可编辑 PPTX(原生形状) | ❌ | △ | ❌ | ✅ | ✅ |
| 对齐评审标准(rubric) | △ | ❌ | ❌ | ❌ | ✅ |
| 数据不造假 / 可追溯 | △ | ❌ | ✅ | ✅ | ✅ |
| 关键节点人工确认 | ❌ | ❌ | ❌ | ✅ | ✅ |
| 视觉风格可选不锁定 | ❌ | △ | ❌ | △ | ✅ |
| 飞书文档 + 架构图 | ❌ | ❌ | ❌ | ❌ | ✅ |
| Agent 原生协作 | △ | ❌ | ❌ | ❌ | ✅ |

## 快速开始

将本仓库安装为 Codex 或 Claude Code Skill:

```
oh-my-hackathon/
  SKILL.md          # 主编排:5 阶段流水线
  references/       # 各阶段深度参考(模板 / 管线 / 检查清单)
  scripts/          # svg2pptx.py · preview.py · validate_deck.py
```

如果你的工具需要重启 Agent,安装后重启一次。

### Agent 工作流

Oh My Hackathon 不是一个在线编辑器,它让 Agent 直接参与参赛文档构建的全流程:

```
Use $oh-my-hackathon with this competition brief: <贴题目 / 链接 / 要求>
```

Agent 会自动完成:

- 解析题目,提取主题 / 约束 / **评审标准** / 交付物要求;
- 给出 1-3 个候选 idea,等你拍板;
- 落地 `计划.md` + `PRD.md`(功能清单 + 验收标准);
- 生成路演 PPT(SVG 精修 → 可编辑 PPTX);
- 发布飞书产品文档 + 架构图。

也支持中途切入:已有 idea 从计划开始;**已有项目 + 题目 → 直接产出文档**(最常见的实战场景)。

---

## 五阶段流水线

```
比赛题目
  → Stage 1  题目解析      (主题 / 约束 / 评审标准 / 交付物)
  → Stage 2  idea 生成     (1-3 候选,用户确认)           ← 确认门
  → Stage 3  计划 + PRD    (架构 / 里程碑 / 功能清单 / 验收标准)
  → Stage 4  路演 PPT      (SVG → 可编辑 PPTX)
  → Stage 5  项目说明书    (飞书文档 + 架构 SVG)
```

| 阶段 | 产出 | 关键约束 |
| --- | --- | --- |
| Stage 1 题目解析 | `题目解析.md` | 评审标准是后续重点的驱动 |
| Stage 2 idea | `idea.md` | **必须用户确认**才继续 |
| Stage 3 计划 + PRD | `计划.md` / `PRD.md` | 已有项目则从 repo 真实能力拉,不发明 |
| Stage 4 PPT | 可编辑 PPTX | 导出前全片扫描(超界 / 灰字 / 叠字) |
| Stage 5 说明书 | 飞书文档 URL + 架构 SVG | 架构图过 `whiteboard-cli --check` |

> 拿不到题目 / brief 时,Agent 会停下来问你要,不会凭空编——这是硬门控,不是建议。

---

## PPT 管线:SVG 中间格式 → 可编辑 PPTX

PPT 是黑客松交付的重头戏。本 skill 不生成整页图片,而是走一条“可继续改”的链路:

```
brief + 项目画像
  → 信息架构 + 视觉风格 spec_lock
  → 逐页 SVG  (rect / text / circle / line / image / g)
  → preview.py 渲染 png 精修  (改 SVG → 秒看效果,多轮)
  → 全片扫描  (超界 / 灰字 / 叠字 / 对齐)
  → svg2pptx.py 直转可编辑 PPTX  (原生形状)
```

**先预览(改 SVG 后秒看,不开 PowerPoint):**

```bash
S=${CLAUDE_PLUGIN_ROOT}/skills/oh-my-hackathon/scripts
python "$S/preview.py" --input <svg目录> --output preview/
```

**导出 PPTX:**

```bash
python "$S/svg2pptx.py" --input <svg目录或单文件> --output exports/<name>.pptx
```

`svg2pptx.py` 是借鉴 ppt-master 思路、用 python-pptx 重新实现的精简转换器(~400 行),不依赖外部引擎,体积可控。产出为原生形状(矩形 / 圆角矩形 / 椭圆 / 文本框 / 连接线 / 图片),赛后还能在 PowerPoint 里继续编辑。

**导出后自检(查文字超界 / 字号红线 / 灰字模糊):**

```bash
python "$S/validate_deck.py" exports/<name>.pptx
```

---

## 视觉风格(不锁定)

不强制任何单一风格。按【比赛调性 + 项目品牌 + 用户偏好】选择:

- **年轻 / 创意 / 极客 / Agent 主题** → NeoBrutalism(黄黑紫,叠字硬阴影)
- **论文 / 答辩 / 严谨学术** → 极简学术(白底深蓝,章节导航 + 红强调)
- **企业 / 行业赛 / 正式** → 商务蓝
- **用户指定品牌色 / 参考站** → 自定义,提取主色后套页框架

选定后写 `spec_lock.md` 锁全片,保证视觉一致。

---

## 环境要求

Skill 会按所用阶段检查,缺哪个提示安装——不必一次装齐:

| 阶段 | 依赖 |
| --- | --- |
| Stage 1 题目抓取 | `fetch_url_content`(web runtime)/ `WebFetch`(standalone)/ `git clone`,至少一个可用 |
| Stage 4 PPT | **Python 3.10+** + `python-pptx` |
| Stage 5 飞书文档 | `lark-cli`(已认证)+ 飞书文件夹 token |
| Stage 5 架构图 | `whiteboard-cli`(SVG 校验) |

> 只要 PPT?有 Python + python-pptx 即可跑通 Stage 4。

```bash
pip install python-pptx
```

---

## 项目结构

| 路径 | 职责 |
| --- | --- |
| `SKILL.md` | 主编排:5 阶段流水线 + 风格决策 + IO 协议 |
| `references/deliverable-templates.md` | 各阶段产出文档的标准结构 |
| `references/ppt-pipeline.md` | PPT 生成管线 + svg2pptx 约束(单一来源) |
| `references/checklist.md` | PPT 质量检查清单(P0 / P1 / P2 + grep 命令) |
| `references/svg-page-template.md` | NeoBrutalism 页设计模板 |
| `references/academic-style-template.md` | 极简学术页设计模板 |
| `references/visual-style-guide.md` | 风格选型决策(多预设 + 自定义品牌) |
| `references/feishu-publish.md` | 飞书发布(lark-cli + 嵌架构 SVG) |
| `scripts/svg2pptx.py` | SVG → 可编辑 PPTX 转换器 |
| `scripts/preview.py` | SVG → png 快速预览 |
| `scripts/validate_deck.py` | PPTX 质量验证(超界 / 字号 / 灰字) |

---

## Star History

<!-- TODO: 补 star-history.com 图,仓库 MargoGuo/oh-my-hackathon -->

---

## 开源协议

本项目采用 [MIT License](LICENSE) 开源许可证。

---

Made with ❤️ by the open-agent-power community.
