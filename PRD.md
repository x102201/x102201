# x102201 GitHub 个人主页（Profile README）项目文档（PRD）

## 1. 背景与目标

### 1.1 背景

`x102201/x102201` 是 GitHub **Profile README** 专用仓库：仓库根目录的 `README.md` 会渲染在 https://github.com/x102201 的 Overview 页，作为个人在 GitHub 上的「首屏名片」。

### 1.2 目标

- 用 **左文右图** 的横幅形式推广重点项目，点击文字或图片均可进入对应仓库。
- 支持 **多条横幅** 纵向排列，便于后续上架更多项目。
- 文案与配图 **自包含**（图片存于本仓库 `assets/`，不依赖其他仓库的相对路径）。
- 纯 Markdown + 受限 HTML，**零构建、零脚本**，push 到 `main` 即生效。

### 1.3 设计原则

**短、清晰、可点击。**

- 每条横幅：短标题、一句话、3–4 条要点、一张主截图、仓库链接。
- 每条横幅的版式与文案按项目单独设计，复制 HTML 表格模板后替换内容即可。
- 简介区可放个人标签与外链（如作品站点），但不承担项目详细文档职责。

---

## 2. 用户路径

```text
访问 https://github.com/x102201
  → 阅读 Profile README（简介 + 作品横幅列表）
  → 点击某一横幅的文字或截图
  → 进入对应 GitHub 仓库
```

核心转化目标：GitHub 访客理解项目价值 → 进入目标仓库。

---

## 3. 平台能力与约束

GitHub 对 Profile README 中的 HTML 经 **sanitizer** 过滤，实现横幅时必须遵守以下约定。

### 3.1 可用能力

| 能力 | 说明 |
|------|------|
| 标准 Markdown | 标题、列表、链接、分隔线、行内代码等 |
| HTML 表格 | `<table>` / `<tr>` / `<td>`，可用 `width`、`align`、`valign` |
| 链接与图片 | `<a href>`、`<img src width alt>` |
| 换行 | `<br/>` |
| 折叠块 | `<details>` / `<summary>`（可选，用于次要说明） |
| Markdown 嵌套链接图 | `[![alt](img)](url)` |

### 3.2 不可用 / 不可靠

| 限制 | 影响 |
|------|------|
| **无 JavaScript** | 不能读取 `navigator.language`，不能按浏览器语言切换文案 |
| **`style` / `class` 被剥离** | 不能用 flex、grid、自定义颜色与边距 |
| **不能把 `<table>` 包进单个 `<a>`** | 块级嵌套会被打散；需左右两格分别链到同一 URL |
| **无服务端** | 不能动态渲染；所有内容写死在 `README.md` |

### 3.3 语言策略

| 项 | 约定 |
|----|------|
| 主文案语言 | **中文**（与当前首批项目一致） |
| 双语 | 不在 Profile README 做动态切换；可在横幅内附一行英文副标题（非必做） |

---

## 4. 信息架构

### 4.1 页面结构（单文件 `README.md`）

```text
┌─────────────────────────────────────┐
│ 简介区：Hi there、个人标签、可选外链   │
├─────────────────────────────────────┤
│ 作品 / Projects                      │
│  ┌──────────────┬──────────────────┐ │
│  │ 左：标题+简介 │ 右：截图（可点击） │ │  ← 横幅 1（如 dsh-helper）
│  │ + 功能要点    │                  │ │
│  └──────────────┴──────────────────┘ │
│  ---                                 │
│  ┌──────────────┬──────────────────┐ │
│  │ …            │ …                │ │  ← 横幅 2（后续项目）
│  └──────────────┴──────────────────┘ │
└─────────────────────────────────────┘
```

多条横幅之间用 Markdown 分隔线 `---` 隔开。

---

## 5. 目录与命名约定

### 5.1 目录结构

```text
/
├── README.md              # Profile 主页内容（GitHub 自动展示）
├── PRD.md                 # 本规格文档
└── assets/
    └── {id}/              # 按项目 id 分目录存放横幅配图
        └── *.png          # 建议主界面截图，文件名语义化
```

示例（已上架）：

```text
assets/
└── dshhelper/
    └── screenshot-main-light.png
```

### 5.2 `{id}` 命名

- 小写、短横线或小写连写，稳定且语义清晰，例如：`dshhelper`。
- 图片路径在 `README.md` 中使用相对路径：`./assets/{id}/文件名.png`。
- `{id}` 一经上线尽量不改，避免 README 内路径失效。

### 5.3 图片规范

| 项 | 建议 |
|----|------|
| 格式 | PNG（界面截图）或 SVG（简单示意图） |
| 宽度 | README 中 `<img width="420–480">`，原图可宽屏，GitHub 会缩放 |
| 来源 | 从产品文档仓复制到本仓库，避免引用其他仓库相对路径 |
| 暗色主题 | 可选准备 `screenshot-main-dark.png`，配合 `<picture>` + `prefers-color-scheme`（非首期必做） |

---

## 6. 横幅模板（HTML 表格）

每条作品横幅使用 **两列表格**：左 55% 文案，右 45% 配图；标题、「查看仓库」与图片均指向同一仓库 URL。

```html
<table>
  <tr>
    <td width="55%" valign="top">
      <a href="https://github.com/x102201/{repo}"><strong>{产品名}</strong> — {一句话定位}</a>
      <br/><br/>
      {2–3 句简介，含核心卖点}
      <br/><br/>
      🖥️ <strong>{要点1标题}</strong> — {说明}<br/>
      🔀 <strong>{要点2标题}</strong> — {说明}<br/>
      …
      <br/><br/>
      <a href="https://github.com/x102201/{repo}">查看仓库 →</a>
    </td>
    <td width="45%" valign="middle" align="right">
      <a href="https://github.com/x102201/{repo}">
        <img src="./assets/{id}/screenshot-main-light.png" alt="{产品名} 主界面" width="460" />
      </a>
    </td>
  </tr>
</table>
```

---

## 7. 页面职责

### 7.1 简介区

- 简短问候与个人标签（如 coding by day / night）。
- 可选：作品站点等外链，链到 README 中写明的 URL 即可。

### 7.2 作品横幅区

- 每条横幅独立设计文案与配图，但共用 §6 表格骨架。
- 点击标题、底部「查看仓库」或右侧截图，均应进入 **同一 GitHub 仓库**。
- 功能要点建议 3–4 条，带 emoji 便于扫读；文案可与产品 README 对齐，适度压缩。

### 7.3 本仓库不承担

- 项目详细文档、安装教程、Release 说明（在各自产品仓库）。
- 语言切换、访问统计、评论。

---

## 8. 维护流程

### 8.1 上新项目流程

1. 选定主截图，复制到 `assets/{id}/`。
2. 在 `README.md` 的「作品 / Projects」下复制 §6 表格模板，填入产品名、简介、要点与仓库 URL。
3. push 到 `main`，打开 https://github.com/x102201 验收：布局正常、链接与图片可点可用。

### 8.2 更新已有横幅

- 文案变更：只改 `README.md` 对应 `<td>` 内容。
- 换图：替换 `assets/{id}/` 下文件，必要时改 `alt` 文本；文件名不变则无需改 `src`。

### 8.3 首批上架项目

| id | 产品名 | 仓库 | Profile 配图 | 状态 |
|----|--------|------|--------------|------|
| `dshhelper` | dsh-helper — DeepSeek Harness 桌面助手 | https://github.com/x102201/dsh-helper | `assets/dshhelper/screenshot-main-light.png` | 已上架 |

---

## 9. 首期范围与后续扩展

### 9.1 首期范围（做）

- `README.md` 简介区 + 至少一条左文右图可点击横幅（dsh-helper）。
- `assets/{id}/` 自托管配图。
- 本 `PRD.md` 说明维护方式与平台约束。

### 9.2 首期不做

- Profile README 内的浏览器语言判定或多语言动态切换。
- 自定义 CSS、动画、第三方统计脚本。
- 自动生成 README（保持手工维护，便于按项目微调版式）。

### 9.3 后续可扩展（非首期必做）

- 增加更多横幅（其他 GitHub 项目）。
- 使用 `<picture>` + `prefers-color-scheme` 在 GitHub 暗色主题下切换截图。
- 在简介区增加 GitHub 统计徽章（shields.io 等）。

---

## 10. 验收清单

每次修改 `README.md` 或 `assets/` 后检查：

- [ ] https://github.com/x102201 Overview 页渲染正常，无 HTML 被整段吃掉。
- [ ] 左栏标题、「查看仓库」、右栏图片均跳转到正确仓库。
- [ ] 图片相对路径 `./assets/...` 在 GitHub 上可加载。
- [ ] 窄屏（手机 GitHub App / 浏览器）上文字不严重挤压、截图仍可辨认。

---

## 修订记录

| 日期 | 说明 |
|------|------|
| 2026-08-27 | 初版：Profile README 定位、HTML 约束、横幅模板 |
| 2026-08-27 | 首批上架 `dshhelper` 横幅及 `assets/dshhelper/` 配图约定 |
| 2026-08-27 | 精简范围：仅描述 Profile README，移除外部站点实现说明 |
