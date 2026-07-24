# SVG 页设计模板(对齐获奖路演风格)

> 生成 SVG 幻灯片时严格按本规范,保证全片视觉一致、接近专业路演效果。源自对一个获奖黑客松路演 PPT(ProudCat/SuperPmAgent,NeoBrutalism 风格)的逆向分析。配合 [ppt-pipeline.md](ppt-pipeline.md) 的精简 svg2pptx 用。

## 核心原则:NeoBrutalism(强对比 + 硬阴影 + 大字)

投影友好、远看清晰、有冲击力。每个元素都要"看得见、看得清"。

## 配色(固定 palette)

| 用途 | hex |
|---|---|
| 主背景 | `#FDE047` 黄(绝大多数页) / `#7C3AED` 紫(结语页变化) |
| 主文字 | `#0A0A0A` 黑 |
| 主强调 | `#7C3AED` 紫 |
| 辅色(卡片区分) | `#06B6D4` 青 / `#22C55E` 绿 |
| 卡片内高亮字 | `#FDE047` 黄(紫底卡) / `#FFFFFF` 白(深底卡) |

## 字体

| 场景 | font-family |
|---|---|
| 大标题(英文/数字) | `Arial Black, Microsoft YaHei` |
| 中文标题/正文 | `Microsoft YaHei` |
| 导航标签/代码 | `Consolas, Microsoft YaHei` |

> svg2pptx 取 `font-family` 逗号前第一个字体名,**不要给字体名加内层单引号**(会触发 shell/heredoc 问题,且无需)。中文由 PowerPoint 自动回退到 YaHei。

## 字号体系(全 bold,px 值,svg2pptx 按 ×0.75 转 pt)

| 元素 | font-size(px) | 用途 |
|---|---|---|
| 封面大标题 | 132 | 品牌名(英文) |
| 内页主标题 | 56–64 | 每页大标题(叠字) |
| 副标题 | 26–30 | 标题下的一句补充 |
| 卡片标题 | 28 | 卡片顶部 |
| 正文 | 20–22 | 卡片内说明 |
| 大数字(数据) | 88–108 | benchmark 关键数 |
| 导航标签 | 18 | 顶部 ▶ 章节 |
| 小注/金句 | 20–22 | 底部一句 |

> 全部加 `font-weight="bold"`(参考风格就是全粗,有力量感)。正文最低 17px,投影再小就糊。

## 叠字硬阴影标题(标志性效果,必须有)

两个 `<text>` 叠放:阴影色在右下小偏移,主体色在原位。**中文标题位移用 (+2,+2)**——中文笔画密,+4 会重影模糊看不清(踩过坑);英文大字(≥96px)才用 (+4,+4)。

```xml
<!-- 紫影 + 黑主(黄底页中文标题,位移 +2) -->
<text x="62" y="132" font-family="Microsoft YaHei" font-size="56" font-weight="bold" fill="#7C3AED">标题文字</text>
<text x="60" y="130" font-family="Microsoft YaHei" font-size="56" font-weight="bold" fill="#0A0A0A">标题文字</text>
```

- 阴影 text 先画(z 序在下),主体 text 后画(在上)
- 紫底页用"黑影 + 黄主":黑 `fill="#0A0A0A"` 偏 +2,+2,黄 `fill="#FDE047"` 原位
- `text-anchor="middle"` 的叠字:阴影 text 的 x/y 比主体大 2

## 每页固定框架(6 要素)

每页都画这些,保证一致:

1. **背景**: `<rect width="1280" height="720" fill="#FDE047"/>`(结语页改紫)
2. **左上角标**: `<rect x="40" y="40" width="32" height="32" fill="#0A0A0A"/>`
3. **右下角标**: `<rect x="1208" y="648" width="32" height="32" fill="#7C3AED"/>`(结语页改黄)
4. **导航标签**: `<text x="90" y="62" font-family="Consolas, Microsoft YaHei" font-size="18" font-weight="bold" fill="#0A0A0A">▶ 章节名</text>`
5. **叠字标题**: 上文格式
6. **底部金句栏**: 页底部一句总结,`font-size="22" font-weight="bold"`,含紫色强调词

## 布局网格

- 画布 `1280×720`,统一边距 `left≈60, top≈40`
- 标题区 y≈130,副标题 y≈172
- **双卡**: `translate(60,210)` + `translate(680,210)`,卡 `540×400`
- **三卡**: `translate(60/455/850, 200)`,卡 `370×420`(数据页)或 `350×360`(流程页)
- **三横条**(架构): `y=200/345/490`,高 130,宽 1160
- 底部金句 y≈665–680

## 卡片样式

```xml
<g transform="translate(60,210)">
  <rect width="540" height="400" fill="#7C3AED" stroke="#0A0A0A" stroke-width="3"/>
  <text x="270" y="62" font-family="Microsoft YaHei" font-size="28" font-weight="bold" fill="#FDE047" text-anchor="middle">卡片标题</text>
  <!-- 正文 x=40 起,行间距 40-42 -->
</g>
```

- 深底卡:标题用黄/白,正文用白
- 浅底卡(青/绿):标题正文用黑
- `stroke="#0A0A0A" stroke-width="3"` 粗黑边(NeoBrutalism 标志)
- 圆角卡加 `rx="18"`

## svg2pptx 适配约束(精简版)
> **完整 svg2pptx 约束(支持/不支持元素、坐标映射)见 [ppt-pipeline.md](ppt-pipeline.md)。** 本文件只列风格特有补充。


- ✅ 用:`<rect>`(含 `rx` 圆角)、`<text>`/`<tspan>`、`<circle>`/`<ellipse>`、`<line>`、`<image>`、`<g transform="translate(...)">`
- ❌ 不用(精简版跳过): `<path>`、`<polygon>`、`<use>`、`<style>`、渐变、mask、`<foreignObject>`、`transform="rotate(...)"`
- **箭头**用 `<line>` + 末端 `<circle r="13">`(不用 marker,精简版不渲染 marker 形状)
- **图标**直接画简单几何(圆/三角/小 rect),不依赖图标库
- 文字垂直定位是基线近似(`y` 是文字基线),精修时微调 `y` 即可,改完重跑 svg2pptx 覆盖

## 路演 PPT 页结构(参考 15 页完整叙事)

按路演节奏展开,每页都遵守上面的"6 要素框架 + 叠字标题 + 底部金句":

1. **01_cover** — 品牌(叠字 132px) + 副标 + 金句
2. **02_pain** — 叠字标题 + 双卡(两段成本)
3. **03_position** — 一句话定位(紫竖条引语) + 三部分概览卡
4. **04_loop** — 叠字标题 + 三卡(流程,卡间 line+circle 箭头) + 底部回灌线
5. **05_arch** — 叠字标题 + 三横条(架构层) + 底部数据流金句
6. **06_web** — 叠字标题 + 5 横条(序号圆 + 能力名 + 描述)
7. **07_plugins** — 叠字标题 + 2×2 卡(四插件)
8. **08_knowledge** — 叠字标题 + 2×2 卡(四类知识,含大数字)
9. **09_contract** — 叠字标题 + 八阶段横排小卡 + 两门控卡 + 结论金句
10. **10_scenarios** — 叠字标题 + 三卡(真实场景)
11. **11_benchmark** — 叠字标题 + 三数据卡(大数字 88-108px) + 结论金句
12. **12_failure** — 叠字标题 + 双栏(失败定位 + 修复方向) + 挽回金句
13. **13_demo** — 叠字标题 + 5 横条(序号 + 步骤 + Consolas 命令)
14. **14_increment** — 叠字标题 + 三方向卡(A/B/C) + 合规黑横条
15. **15_closing** — 紫底 + 叠字金句(黄主黑影) + 路线图三栏 + 署名

> 页数按内容增减(12-15 页均可),核心是每页遵守"6 要素 + 叠字标题 + 底部金句",保证全片一致。
