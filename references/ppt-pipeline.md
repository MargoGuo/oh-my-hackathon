# PPT 生成管线(借鉴 ppt-master + 用户行为偏好)

> 本 skill 的 PPT 部分按此管线生成。核心思想:**SVG 中间格式 + live preview 精修 + 导出可编辑 DrawingML PPTX**。

## 管线

```
源(比赛 brief + 项目画像)
  → Strategist(信息架构 + 视觉风格 spec_lock)
  → Executor(逐页 SVG,只画精简版支持的元素:rect/text/circle/line/image/g)
  → live preview 精修(用户反馈驱动,多轮)
  → 全片扫描(超界/灰字/叠字/对齐)
  → svg2pptx(skill 内置精简转换器,SVG 直转可编辑 PPTX)
  → 可编辑 PPTX(原生形状)
```

## SVG 规范

> **页设计模板**(配色/字体/字号体系/叠字硬阴影标题/导航标签/角标/底部金句)见 [svg-page-template.md](svg-page-template.md) —— 保证全片视觉一致、接近获奖路演风格。生成 SVG 前先读它。

- `viewBox="0 0 1280 720"`(16:9),`width="1280" height="720"`
- 每页一个 `<svg>`,文件名 `NN_页名.svg`(两位序号 + 短名);按文件名排序决定页序
- **图标**:精简版**不内联图标库**(`<use data-icon>` 会被跳过)。要图标就直接画简单几何(圆/三角/小 rect 组合),或嵌预设小 SVG `<image>`
- **图表**:精简版无原生图表对象。用 `<rect>` 画柱状/`<circle>` 分解饼图,或嵌图表截图 `<image>`
- 文字用 `<text>`/`<tspan>`,字号 ≥14 投影清晰
- 箭头 marker 加 `markerUnits="userSpaceOnUse"`(防被 stroke 放大成畸形)

## 用户行为偏好(务必遵守)

> 从做 SuperPmAgent 路演 PPT 的实战中提炼,通用 skill 要保留:

1. **可编辑 PPTX**:导出 Native DrawingML(真实形状),不要整页图片——交付后还要在 PowerPoint 里改
2. **SVG 中间格式**:生成阶段在 SVG 上改,直观;live preview 实时看效果
3. **强对比 + 大字**:投影友好;正文 ≥14px,标题 ≥26px,金句可 40+
4. **多色不单调**:主色 + 辅色,多卡片用不同主题色区分(参考 visual-style-guide.md 预设)
5. **图标用简单几何**:精简版不内联图标库——画圆/三角/小 rect 组合,或嵌预设小图;别依赖 `<use data-icon>`(会被跳过)
6. **导航章节标签**:顶部 `▶ 章节` 药丸,章节词和大标题前缀呼应
7. **不造假数据**:数字/能力从 README/源拉,拿不到就标"待补",绝不发明
8. **导出前全片扫描**:派 agent 找超界 / 灰字模糊 / 叠字 / 对齐问题,逐个修
9. **箭头 `markerUnits="userSpaceOnUse"`**:默认 `strokeWidth` 会把箭头放大成畸形大三角
10. **灰字改黑**:`#44403C` 在浅底对比不足(约5:1)、≤13px 投影模糊 → 改 `#0A0A0A`(约12:1)
11. **叠字投影模糊**:紫影+黑主双层叠字边缘发虚;关键大字(结语/金句)改单层黑;封面/演示页大字(≥96px)叠字可保留(够大不糊)
12. **SVG 是最终源**:精简版 `svg2pptx` 直接把 SVG 转成 PPTX,SVG 文件就是最终版——要继续改就改 SVG 再重转,不会被中间步骤覆盖
13. **改完重转**:精修 SVG 后重跑 `svg2pptx.py` 覆盖 PPTX 即可,无需 finalize/backup 等中间步骤
14. **逐页精修是常态**:生成后预期会有多轮用户反馈精修(live preview + 浏览器 `Ctrl+F5` 强刷),别指望一次成

## 导出命令(skill 内置精简转换器)

**先预览(改 SVG 后秒看效果,不开 PowerPoint)**:

```bash
S=${CLAUDE_PLUGIN_ROOT}/skills/oh-my-hackathon/scripts
python "$S/preview.py" --input <svg目录> --output preview/
```

渲染每页 png(模拟 pptx 实际效果,含 margin=0 / 文本定位等转换逻辑),浏览器开 `preview/preview_NN.png`。改 SVG 重跑覆盖——精修闭环:改 SVG → preview → 满意 → svg2pptx 导出。

**导出 PPTX**:

本 skill 自带 `scripts/svg2pptx.py`——**借鉴 ppt-master 思路、用 python-pptx 重新实现的精简 SVG→PPTX(~400 行)**,不依赖外部 ppt-master 安装,体积可控。

```bash
S=${CLAUDE_PLUGIN_ROOT}/skills/oh-my-hackathon/scripts
python "$S/svg2pptx.py" --input <svg目录或单文件> --output exports/<name>.pptx
```

- 输入: SVG 目录(按 `NN_页名.svg` 文件名排序决定页序)或单个 SVG
- 输出: 可编辑 PPTX(原生形状:矩形/圆角矩形/椭圆/文本框/连接线/图片),非整页图片
- 依赖: `python-pptx`(缺失则脚本打印提示、退出码 3)
- 坐标: `viewBox="0 0 1280 720"`,1px = 9525 EMU 恒定映射

### 精简版的已知边界(MVP,控制体积不 vendor 3 万行引擎)

- ✅ 支持: `<rect>`/`<rect rx>`(圆角保留为 roundRect,可继续调)、`<text>`/`<tspan>`、`<circle>`/`<ellipse>`、`<line>`、`<image href>`(含 `data:base64`)、`<g transform translate/scale>`
- ⚠️ 跳过(记 warning,不中断): `<path>`(自定义几何)、`<use data-icon>`(图标库内联)、渐变/mask/`<foreignObject>`
- text 垂直定位是近似(基线 → 框顶偏移一个字号),精细对齐在 live preview 阶段手调

> 需 `<path>`/原生图表/动画等高级能力时,若环境另装了完整 ppt-master,可改调其 `finalize_svg.py`+`svg_to_pptx.py`——SVG 规范一致,产物可互换。默认走 skill 内置精简版即可满足黑客松路演 PPT。

## 全片扫描清单(导出前)

派 agent 扫所有页,找:
1. **文字超界**:`<text>` x + 估算宽度 > 所在 `<rect>` 右边界(中文≈fontsize×1.0,Consolas≈×0.6)
2. **灰字模糊**:`fill="#44403C"` 且 `font-size` ≤ 13
3. **叠字模糊**:相邻两 text 内容相同、一紫(`#7C3AED`)一黑、坐标偏移 +4
4. **对齐/越界**:同行元素 y/x 差 >5;rect 越出画布(1280×720)

逐个修完再导出。

## 踩坑速查

| 现象 | 原因 | 解法 |
|---|---|---|
| 箭头成畸形大三角 | marker 默认 `markerUnits=strokeWidth` | 加 `markerUnits="userSpaceOnUse"` |
| 公式/文字看不到 | 图片白底白字 | 改用 SVG `<text>` 写,颜色可控 |
| 灰字发虚 | `#44403C` 在浅底对比不足 | 改 `#0A0A0A` 黑 |
| 大字投影糊 | 紫影+黑叠字 | 关键大字改单层 |
| 改了 SVG 没生效 | 没重跑 svg2pptx | 改 SVG 后重跑 `svg2pptx.py` 覆盖 PPTX |
| 元素跑到画布外 | linter/拖动坐标错 | 导出前扫画外元素(负坐标/超1280×720) |
| 预览"改了没生效" | 浏览器缓存 | `Ctrl+F5` 强刷 |
