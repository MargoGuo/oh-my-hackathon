---
name: autpilot-oh-my-hackathon
description: 黑客松/比赛参赛文档全流程自动化。用户要参赛(看到比赛/题目)或要产出参赛交付文档(路演 PPT、PRD、产品规划、项目说明书、架构图等)时触发——产出全套,端到端。已有项目或已有 idea 可中途切入。
argument-hint: "比赛题目要求/brief(+ 可选已有项目 repo / 团队偏好 / 风格)"
---

# Oh-My-Hackathon

黑客松参赛**全流程自动化**:从比赛题目,一路推进到 **idea → 计划 → PPT → 项目说明书(飞书文档 + 架构文档)**。

不是只生成某一类文档,而是**一条龙**:看到题目 → 想清楚 idea → 落地计划 → 做出路演 PPT → 写完项目说明书。每个阶段产出文档,关键节点(idea / 计划)和用户确认。

## Use When

- 用户给比赛题目/brief,要从头参赛(还没 idea)
- 或已有 idea/项目,要补齐计划/PPT/说明书
- 要端到端产出:idea 文档 + 计划 + PPT + 飞书产品文档 + 架构文档

## Inputs

- **比赛题目要求 / brief**(必需)—— 题目主题/约束/评审标准/交付要求
- 可选:已有项目 repo(有项目则跳过 idea、从计划切入)
- 可选:团队技术偏好、视觉风格(预设名/品牌色/参考站)、飞书文件夹 token

## Preconditions(环境依赖)

按所用阶段检查,缺哪个标"待装"并提示用户:

- **Stage 1 fetch**:`fetch_url_content`(web runtime) / `WebFetch`(standalone Claude Code) / `git clone` —— 至少一个可用
- **Stage 4 PPT**:skill 内置 `scripts/svg2pptx.py`(python-pptx 精简自实现,缺则提示安装)+ Python 3.10+
- **Stage 5 飞书**:`lark-cli`(已认证)+ 飞书文件夹 token
- **Stage 5 架构图**:`whiteboard-cli`(SVG 校验)

## Source Of Truth(不造假)

1. **比赛题目官方页**(规则/评审标准/rubric/交付物)—— 真相
2. **已有项目 repo**(若有)—— 真相
3. session `notes.md`/`decisions.md` —— 补充
4. **不发明数据/能力**;idea 基于题目真实约束,计划/PPT/说明书的数据从真实来源拉,拿不到标"待补"

## 流水线(5 阶段,关键节点和用户确认)

### Stage 1 — 题目解析
读题目 → 提取:主题、约束、**评审标准/rubric**(驱动后续重点)、交付物要求、格式/截止。
产出:`题目解析.md`。
工具:`fetch_url_content`(web) / `WebFetch`(standalone) / `git clone --depth 1` / 让用户粘贴 brief。记录用了哪条路径。

### Stage 2 — idea 生成
根据题目 + 团队/技术偏好 → 生成 **1-3 个候选 idea**(问题切入 / 解决思路 / 差异化 / 可行性 / 和评审标准的契合度)。
**和用户确认选哪个 idea**(或合并/调整)再继续。
产出:`idea.md`(选定 idea 完整陈述:问题/方案/亮点/可行性)。

### Stage 3 — 计划 + PRD
选定 idea(或已有项目)→ 落地**两份互补文档**:
- `计划.md`:产品规划(定位 / **架构** / 里程碑 / 分工 / 风险)
- `PRD.md`:功能规格(背景 / 目标 / 用户场景 / **功能清单+验收标准** / 非目标 / 评审契合)——给开发/评委看"具体做什么、怎么验"

结构见 [deliverable-templates.md](references/deliverable-templates.md)。已有项目时,功能清单从 repo 真实能力拉,不发明。

### Stage 4 — PPT(路演)
按 idea + 计划 + 评审标准 → 路演 PPT。

**风格快速决策**(选定后读对应模板,细节见 [references/visual-style-guide.md](references/visual-style-guide.md)):
- **年轻/创意/极客/Agent 主题** → NeoBrutalism(黄黑紫,叠字硬阴影)→ [references/svg-page-template.md](references/svg-page-template.md)
- **论文/答辩/严谨学术** → 极简学术(白底深蓝,章节导航+红强调)→ [references/academic-style-template.md](references/academic-style-template.md)
- **企业/行业赛/正式** → 商务蓝 → visual-style-guide 配色 + 参考 NeoBrutalism 页框架
- 用户指定品牌色/参考站 → 自定义,提取色后套页框架
**读 [ppt-pipeline.md](references/ppt-pipeline.md)**(,SVG→PPTX,含用户行为偏好)。
**视觉风格读 [visual-style-guide.md](references/visual-style-guide.md)**(不锁定,按比赛调性/项目品牌/用户偏好选,选定写 `spec_lock.md` 锁全片)。
产出:可编辑 PPTX(Native DrawingML)。

### Stage 5 — 项目说明书
- **飞书产品文档**:产品定位/功能/价值/使用/亮点(评委视角)
- **架构文档**:架构图(SVG,`whiteboard-cli --check` 验证 0 错)+ 技术说明
**读 [feishu-publish.md](references/feishu-publish.md)** —— lark-cli 创建文档 + 嵌入架构 SVG + 踩坑(自包含,不依赖其他 skill)。
产出:飞书文档 URL + 架构 SVG。

### 收尾 — 注册 IO 记录
每阶段产出注册 `derived_output` 回 session(`<session>/attachments/exports/hackathon-<stage>-<slug>.json`),`output_type` 用协议 allow-list(`feishu_prd` / `ppt` 等)。无 session 则 inline 返回。

## 产出路径(工作目录)

所有文档产出落到一个工作目录,默认:

```
<cwd>/hackathon-<比赛slug>/
├── 题目解析.md
├── idea.md
├── 计划.md
├── architecture/      # 架构 SVG
└── exports/           # PPT 等
```

- 用户指定目录 / session 目录优先;否则用上面默认(`<cwd>/hackathon-<比赛slug>/`)
- **IO 记录**(`derived_output` .json)仍回 `<session>/attachments/exports/`(协议要求,见 Stage 收尾)
- 飞书文档产出是 URL(记到 IO 记录,不本地存内容)
- 各 .md 的结构见 [deliverable-templates.md](references/deliverable-templates.md)

## 阶段跳转(灵活)

不一定从头:
- 只给题目 → Stage 1 开始(完整流水线)
- 已有 idea → 从 Stage 3(计划)开始
- 已有项目 + 题目 → 跳 Stage 2,Stage 3 直接基于项目产出 `计划.md` + `PRD.md`,再 Stage 4 PPT / Stage 5 说明书(**最常见场景:"项目+题目→文档"**)
- 只要 PPT / 说明书 → 直接 Stage 4 / 5

## 视觉风格(不锁定)

**不强制任何单一风格**。按【比赛调性 + 项目品牌 + 用户偏好】选(NeoBrutalism / dark-tech / 极简学术 / 商务蓝 / 科技未来,见 visual-style-guide.md),选定后 `spec_lock.md` 锁全片。

## PPT 原则(核心)

用户行为偏好,详见 [ppt-pipeline.md](references/ppt-pipeline.md):SVG 中间格式、可编辑 DrawingML、图标 `data-icon`、不造假、导出前全片扫描、`markerUnits=userSpaceOnUse`、灰字改黑、风格不锁定。

## Output

返回各阶段产出 URI + 总结:

```markdown
## Oh-My-Hackathon Result

Competition: <题目 + link>
Idea: <选定 idea 一句话>
Plan: <计划.md / 飞书>
PPT: <PPTX path>
项目说明书: 飞书<URL> + 架构<svg>
Style: <预设名 or 自定义>
Judging criteria aligned: <N/M>
Risks: <list or none>
Delivery Status: docs_generated
```

## 文件职责(划分明确)

| 文件 | 职责 | 何时读 |
|---|---|---|
| `SKILL.md`(本文件) | 主编排:5 阶段流水线 + 跳转 + 风格决策 + IO 协议 | 总是(skill 触发即载入) |
| `references/deliverable-templates.md` | 各阶段产出 .md 的标准结构(题目解析/idea/计划/PRD/飞书/架构) | Stage 1/2/3/5 产出文档时 |
| `references/ppt-pipeline.md` | PPT 生成管线 + svg2pptx 约束(单一来源) + 用户行为偏好 | Stage 4 生成 PPT 时 |
| `references/svg-page-template.md` | NeoBrutalism 页设计模板(配色/字体/叠字/导航/15页结构) | 选 NeoBrutalism 风格时 |
| `references/academic-style-template.md` | 极简学术页设计模板(亮蓝/Arial/章节导航/嵌图分栏) | 选学术风格时 |
| `references/visual-style-guide.md` | 风格选型决策(5 预设 + 自定义品牌) | 不确定选哪个风格时 |
| `references/feishu-publish.md` | 飞书发布(lark-cli + 嵌架构 SVG + 踩坑) | Stage 5 发布飞书文档时 |
| `scripts/svg2pptx.py` | SVG→可编辑 PPTX 转换器(精简,python-pptx) | Stage 4 导出 PPTX 时 |
| `scripts/preview.py` | SVG→png 快速预览(模拟 pptx,改完秒看) | Stage 4 改 SVG 后看效果 |
| `scripts/_selftest/svg/` | 最小样例 SVG(可复现 demo) | 验证 svg2pptx 时 |

> 职责不重叠:页元素的"怎么画"归两套风格模板;svg2pptx 的"支持什么/不支持什么"归 ppt-pipeline(单一来源,模板只引用);产出文档结构归 deliverable-templates。

## Scope (do not)

- 不写代码 / 不开 PR(IO skill,只做文档)
- 不造假数据 / 能力 / 评审覆盖
- **不锁定单一视觉风格**
- **idea / 计划阶段必须用户确认**,不擅自定方案
- 架构图必须过 `whiteboard-cli --check`
- 不替代 session 的 `notes.md`(`/goal` 仍消费 session)
- 不读/改其他 skill 的文件(本 skill 自包含)
