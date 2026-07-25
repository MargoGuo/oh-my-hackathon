# PPT 质量检查清单(Checklist)

> 借鉴 guizang-ppt-skill 的检查表结构(P0 现象/根因/做法/检查命令),适配本 skill 的 SVG→PPTX 流程。
> 生成 PPT 前通读;生成后逐项自检。每条都有可执行的检查命令。

---

## 🔴 P0 · 一定不能犯(几何确定,必改)

### P0-1. 文字溢出容器

**现象**:文字跑出卡片/画布右边界,叠到相邻元素。
**根因**:SVG `<text>` 估算宽度 < 实际渲染宽度(中英混排、Consolas 窄、CJK 宽)。
**做法**:超界时砍文案、拆行或换版式,不要硬塞小字。preview.png 看实际效果。
**检查**:
```bash
python scripts/preview.py --input svg/ --output preview/
# 逐页看 preview_NN.png,文字不超卡片右边界
```

### P0-2. 灰字对比不足(投影看不清)

**现象**:`#44403C` 中灰在浅底上投影模糊(对比 ~5:1,WCAG AA 需 4.5:1);≤13px 灰字更糊。
**根因**:投影仪低亮度下灰字对比进一步下降。
**做法**:浅底正文改 `#0A0A0A`(对比 ~12:1)或 `#1F2937`。
**检查**:
```bash
grep -nE 'fill="#44403C"|fill="#6B7280"' svg/*.svg | grep -i 'font-size="1[0-3]"'
# 命中 = 灰字 + 小字号,要改黑
```

### P0-3. 叠字位移过大(中文重影)

**现象**:叠字标题(紫影+黑主)位移 +4 时,中文笔画密导致重影模糊看不清。
**根因**:+4 适合英文大字(≥96px),中文中等字号(50-60px)+4 太大。
**做法**:中文标题叠字位移 **+2**(英文 ≥96px 可 +4)。
**检查**:
```bash
# 找叠字对(同内容、坐标差),看位移是否 ≤2
grep -nE 'fill="#7C3AED"|fill="#0A0A0A"' svg/*.svg
```

### P0-4. 字号低于投影下限

**现象**:正文 ≤13px 投影看不清。
**根因**:SVG 按屏幕设计,投影仪分辨率低。
**做法**:正文 ≥17px,标题 ≥26px,金句 ≥40px(见 svg-page-template 字号体系)。
**检查**:
```bash
grep -oE 'font-size="[0-9]+"' svg/*.svg | grep -oE '[0-9]+' | sort -n | head -3
# 最小字号应 ≥14(导航/标签允许 14-16,正文 ≥17)
```

### P0-5. path/polygon 被 svg2pptx 跳过(图示缺失)

**现象**:复杂图示(架构图/流程图)用 `<path>`,转 pptx 后消失(svg2pptx 精简版不支持)。
**根因**:svg2pptx 只认 rect/text/circle/ellipse/line/image/g,不认 path/polygon/use。
**做法**:用 rect/circle/line 组合画,或 `<image>` 嵌真实图。
**检查**:
```bash
grep -cE '<path|<polygon|<use' svg/*.svg
# 应为 0(或确认是 <image> 嵌图,不是 path 矢量)
```

---

## 🟡 P1 · 强烈建议

### P1-1. 导出前全片扫描

**现象**:单页看着 OK,全片有超界/叠字/对齐问题。
**做法**:preview.py 渲染全片 png,逐页肉眼扫一遍再 svg2pptx。
**检查**:
```bash
python scripts/preview.py --input svg/ --output preview/ && ls preview/
```

### P1-2. 架构图过 whiteboard-cli --check

**现象**:架构 SVG 嵌飞书后渲染坏(渐变/mask 等不支持)。
**做法**:`whiteboard-cli -i architecture/xxx.svg -f svg --check` 0 错才嵌入。
**检查**:--check 输出 error 数 = 0。

### P1-3. 不造假数据

**现象**:benchmark/能力数字编造。
**做法**:数字从真实来源(repo README/题目/源)拉,拿不到标"待补"。
**检查**:PPT 里每个数字能在源里找到出处;找不到的标"待补"。

### P1-4. 颜色块顶到顶(导航/标题色块)

**现象**:导航高亮色块悬空在中间(没顶到 PPT 顶),或标题色块没顶住线。
**做法**:导航高亮块 `y=0`(顶到 PPT 顶),高度满导航条(70px)。
**检查**:preview.png 看色块是否顶到画布顶。

---

## 🟢 P2 · 锦上添花

### P2-1. 风格全片一致

**做法**:选定风格写 `spec_lock.md`,Executor 每页重读保证配色/字号/元素一致。

### P2-2. 导航章节标签呼应

**做法**:顶部导航 `▶ 章节`(NeoBrutalism)或章节导航条(学术),章节词和大标题前缀呼应。

### P2-3. 嵌图用分栏(左图右文/左文右图)

**做法**:嵌真实图的页(架构/闭环),图占一半 + 文字解释另一半,中间竖分隔线(见 academic-style-template 嵌图分栏)。
