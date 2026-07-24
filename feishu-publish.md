# 飞书发布(Stage 5)

把项目说明书发布到飞书,嵌入架构 SVG。**本文件自包含**,不读其他 skill。

用官方 `@larksuite/cli`(`lark-cli`);`lark-openapi-mcp` 不能编辑云文档,不用。

> ⚠️ **lark-cli 迭代快**。任何命令以 CLI 版本匹配的参考为准(CLI 为权威,本文件是快照):
> ```bash
> lark-cli skills read lark-doc references/lark-doc-create.md
> lark-cli skills read lark-doc references/lark-doc-update.md
> lark-cli skills read lark-doc references/lark-doc-xml.md
> lark-cli skills read lark-whiteboard routes/svg.md
> ```

## 准备

```bash
npx @larksuite/cli@latest install
lark-cli auth login --recommend
lark-cli auth status
```

## 目标位置

- `--parent-token "<folder token>"` —— 指定文件夹(需写权限;只读/共享文件夹返回 `code 3380004 Permission denied`)。问 PM 要 token。
- `--parent-position my_library` —— 个人库,始终可写,**缺 token 时的兜底**。

## 创建文档

```bash
lark-cli docs +create --api-version v2 \
  --parent-token "<folder token>" \
  --content '<title><项目名> 项目说明书</title>
<h1>1. 一句话定位</h1><p>...</p>'
```

捕获 `data.document.url` + `data.document.document_id`。

## 写长文档(可靠模式)

`append` 的 `-1` 锚点在 `create` 后不稳(`degrade_code=1002 target block not found`)。优先:

- **模式 A — 一次性 `overwrite`**(首选):整篇一个 `overwrite`。单次 `--content` 的**纯文本**控制在 ~4KB 以内(SVG 体积单独容忍,但总量过大仍可能失败 → 拆分)。
- **模式 B — overwrite 前半,再 `append` 后半**:overwrite 让大多数文档状态稳定后,append 才可靠。
- **模式 C — `block_insert_after --block-id "<id>"`**:中途插入。先 `docs +fetch --scope outline --detail with-ids` 取锚点 block id,再 insert after。**别用 `-1`**。

## 嵌入架构 SVG(whiteboard,必做)

架构图用**自包含 SVG** 包在 `<whiteboard type="svg">` 里——飞书解析成可编辑节点并渲染成真图(比 Mermaid/ASCII 强)。

### 1. 先验证(强制)

```bash
npx -y @larksuite/whiteboard-cli@^0.2.11 -i architecture/xxx.svg -f svg --check
```

每个 `text-overflow` 错都要修(加宽容器或缩短文字)。`node-overlap` 警告(层背景 + 内卡片)是预期的。

### 2. 嵌入(heredoc + SVG 文件,因为 SVG 大)

SVG 太大不宜手内联。写到本地文件,再拼 `--content` = 文本头 + SVG 文件 + 文本尾:

```bash
DOC="<doc id>"
SVG=$(cat architecture/overall.svg)
HEAD=$(cat <<'HEAD'
<h1>4. 架构</h1>
<p>说明...</p>
<whiteboard type="svg">
HEAD
)
TAIL=$(cat <<'TAIL'
</whiteboard>
<p>横切说明...</p>
TAIL
)
lark-cli docs +update --api-version v2 --doc "$DOC" --command overwrite \
  --content "$HEAD$SVG$TAIL"
```

SVG 规则:
- **自包含**:`<svg>` 根 + `viewBox`,无外部图片/脚本/远程引用。
- 只用受支持元素(rect/circle/ellipse/polygon/line/polyline/path/text/tspan/g/a/use/symbol)。**避免** radialGradient/filter/pattern/clipPath/mask——会渲染坏。
- 文字用 `<text>`;CJK 容器宽≈1em / Latin≈0.6em。
- `&` 转义成 `&amp;`。
- 响应里 `data.document.new_blocks[]` 出现 `block_type: "whiteboard"` = 嵌入成功。

## 嵌入其他图片(产品截图/演示)

```xml
<img href="https://example.com/public-image.png" caption="可选说明"/>
```

- `href` 必须是**公开** URL。飞书内部 URL(`internal-api-drive-stream.feishu.cn/...`)**不渲染**——别复用。
- 没有真实公开 URL 就别嵌,绝不放占位图。
- 数据图表(benchmark/token 节省)优先用 `<whiteboard type="svg">`(保持可编辑),而非静态图。

## XML 标签速查(v2)

| 标签 | 飞书元素 |
|---|---|
| `<title>` | 文档标题(每篇仅一个) |
| `<h1>`~`<h9>` | 标题层级 |
| `<p>` | 段落 |
| `<ul><li>` / `<ol><li>` | 无序/有序列表 |
| `<table><thead><tr><th>` / `<tbody><tr><td>` | 表格 |
| `<callout emoji="🚀">` | 高亮提示框 |
| `<whiteboard type="svg"><svg>...</svg></whiteboard>` | **SVG 图(架构/图表——主用)** |
| `<img href="URL" caption="..."/>` | 图片(仅公开 URL) |
| `<a href="URL">text</a>` | 链接 |
| `<b>` `<em>` `<code>` | 行内样式(嵌套序:a→b→em→del→u→code→span) |
| `<hr/>` | 分割线 |

## 去 AI 味(所有正文)

黑客松说明书评委一眼能看出 AI 腔。书面正式、具体、有真实数字和逻辑链,不堆网络用语、不模板化。数据不准标"待补",不发明。详见 `deliverable-templates.md` 飞书说明书节的写作要求。

## 踩坑(均已验证)

- **`--folder-token` 没了** → 用 `--parent-token` / `--parent-position my_library`。
- **`code 3380004 Permission denied`** → 无写权限,改 `my_library`。
- **`degrade_code=1002 target block not found`(append)** → 用 `overwrite`(模式 A)或 `block_insert_after`(模式 C)。
- **SVG 文字溢出** → `--check` 会标;加宽 rect 或缩短文字。
- **SVG 渲染坏** → 用了不支持的装饰器(gradient/filter/pattern/clipPath/mask),删掉。
- **`&` 让解析崩** → 转义 `&amp;`。
- **PowerPoint 里中文变 `?????`**(Windows web runtime):PowerShell 5.1 把 stdin 按 ASCII 编码 pipe 给 lark-cli,中文全变 `?`(拉丁文存活)。写之前先:
  `chcp 65001; $OutputEncoding=[System.Text.Encoding]::UTF8; [Console]::OutputEncoding=[System.Text.Encoding]::UTF8`。
  **优先用 Git Bash 跑 lark-cli**(绕开 PowerShell 管道),或从 UTF-8 文件传 `--content` 而非 stdin pipe。
- **认证过期** → `lark-cli auth login --recommend`。

## 产出

收集飞书文档 URL。确认响应 `new_blocks` 里有 `whiteboard`。该 URL 是 Stage 5 的主交付物,记进 IO 记录。
