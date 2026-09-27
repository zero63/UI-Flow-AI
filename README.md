<div align="center">

# 🎨 UI-Flow AI

**用 AI 把一张设计参考图，变成整套可直接落地的前端素材**

上传参考图 → AI 生成初稿 → 智能拆解素材清单 → 批量产出透明底素材 → 一键导出/直接改写你的代码

[官网体验](https://ui.xiaozhusho.top) · [领取试用积分](#-领取试用积分) · [编辑器 Skill 安装](#-编辑器-skill-一句话安装推荐) · [Open API](#-open-api)

</div>

---

## 😩 为什么做这个

每个开发者都遇到过：

- 产品只给一张设计稿**截图**，没有源文件，图标插画全靠自己抠；
- 复刻好看的 UI，光切图就花掉半天；
- AI 生成的图文字烙死在图片里，改个文案就要重新生成。

**UI-Flow 一条流水线全部解决**：素材自动切好、全部透明底、不含任何文字（文字永远在代码里排版，改文案不用重新生图）。

> 💡 新用户可免费领取试用积分，拉到文末 [领取试用积分](#-领取试用积分) 扫码即可。

## ⚙️ 四步流水线

```
  参考图(可选) + 提示词
        │
        ▼
  ① 生成初稿 Image A ── 5 积分     目标设计的完整效果图
        │
        ▼
  ② AI 智能拆解 ────── 2 积分     视觉模型产出结构化素材清单
        │
        ▼
  ③ 批量生成素材 ──── 3 积分/个    每项一张透明底 PNG，逐个独立命名
        │
        ▼
  ④ 导出 ZIP ───────── 免费        1x/2x/3x · PNG/WebP
```

<!-- 【配图 1：流水线界面截图，放 docs/images/pipeline.png】
![UI-Flow 四步流水线](docs/images/pipeline.png)
-->

**几个较真的细节：**

- **图标逐个拆分**：成组图标自动拆成 `sidebar_icon_dashboard.png`、`sidebar_icon_setting.png`……绝不生成没法用的"图标合集"；
- **失败可单独重试**：每个素材是独立子任务，一个失败不影响整批；
- **画布实时渲染**：批量生成时进入画布页，素材完成一个上屏一个。

<!-- 【配图 2：透明底素材效果对比（左：手动抠图，右：AI 直出），放 docs/images/assets-compare.png】
![素材对比](docs/images/assets-compare.png)
-->

## 🚀 快速开始（网页版）

1. 打开 [https://ui.xiaozhusho.top](https://ui.xiaozhusho.top) 注册账号（新用户可 [领试用积分](#-领取试用积分)）；
2. 新建项目 → 上传参考图（可选）→ 写一句提示词；
3. 点生成，坐等画布出图 → 一键导出 ZIP。

## 🧩 编辑器 Skill 一句话安装（推荐）

UI-Flow 提供编辑器 Skill，让 **Trae / Claude Code / Codex / Cursor** 里的 AI 直接调用整条流水线，并把素材落进你的本地项目、**直接改写页面代码**。

在项目根目录让编辑器 AI 执行一句：

```bash
# macOS / Linux（适用于项目级；其他工具只需替换目录即可，详见下表）
mkdir -p .trae/skills/ui-flow && curl -fsSL https://ui.xiaozhusho.top/open/skill.md -o .trae/skills/ui-flow/SKILL.md
```

```powershell
# Windows PowerShell
New-Item -ItemType Directory -Force ".trae\skills\ui-flow" | Out-Null; iwr "https://ui.xiaozhusho.top/open/skill.md" -OutFile ".trae\skills\ui-flow\SKILL.md"
```

各工具的项目级 Skill 目录对照（装到用户目录 `~/` 下的同名路径则全局生效）：

| 工具 | 项目级目录 |
|---|---|
| Trae | `.trae/skills/ui-flow/` |
| Claude Code | `.claude/skills/ui-flow/` |
| **Codex** | `.codex/skills/ui-flow/` |
| Cursor 等兼容 Agent Skills 的工具 | `.claude/skills/ui-flow/` |

装好后，在编辑器里对 AI 说一句：

> **"这是设计参考图，帮我还原成页面"**

AI 会自动完成：调流水线生成初稿和素材 → 下载到本地 → 以初稿为基准改写你的页面代码。

<!-- 【配图 3：编辑器里触发 Skill → 本地项目改造前后对比，放 docs/images/skill-demo.png】
![技能演示](docs/images/skill-demo.png)
-->

##开放API

标准REST接口，Bearer认证，适合集成进自己的系统：

```bash
curl -X POST https://ui.xiaozhusho.top/open/v1/pipeline/run \
  -H "Authorization: Bearer $UIFLOW_API_KEY" \
  -F "prompt=深色科技风的仪表盘页面，左侧导航" \
  -F "image=@/path/to/reference.png"
```

一次返回 `project_id`、三个任务状态、每个素材的 `label` + 下载 URL、剩余积分。也支持分步调用（建项目 / 上传参考图 / 初稿 / 拆解 / 生成 / 轮询 / 导出 ZIP），限流 60 次/分钟。

密钥在官网「我的密钥」页自助创建，明文仅显示一次，支持随时吊销。

### 分步接口一览

| 步骤 | 接口 |
|---|---|
| 建项目（自带 UI 页面 1） | `POST /open/v1/projects` |
| 项目列表 | `GET /open/v1/projects` |
| 加 UI 页面 | `POST /open/v1/projects/{projectId}/pages` |
| 上传参考图 | `POST /open/v1/ref-image`（multipart） |
| ① 初稿 | `POST /open/v1/generate` |
| ② 拆解 | `POST /open/v1/decompose` |
| ③ 批量素材 | `POST /open/v1/generate/refine` |
| 任务轮询 | `GET /open/v1/tasks/{uuid}` |
| 页面详情（素材清单） | `GET /open/v1/pages/{page_id}` |
| 导出 ZIP | `POST /open/v1/export` → `GET /open/v1/exports/{id}/download` |
## 🚀 效果对比

## 🎁 领取试用积分

新用户可免费领取试用积分，体验完整流水线：

1. 打开 [https://ui.xiaozhusho.top](https://ui.xiaozhusho.top) 注册账号；
2. **微信扫码添加小助手**，备注 **「UI-Flow 试用」**；
3. 小助手人工发放试用积分，登录后在充值页即可看到。

<div align="center">
  
<!-- 【配图 4：微信二维码图片，建议命名 docs/images/wechat-assistant.jpg】
<img src="docs/images/wechat-assistant.jpg" width="260" alt="扫码添加小助手，备注 UI-Flow 试用">
-->

**扫码添加小助手 · 备注「UI-Flow 试用」**

</div>

> 也可以在官网登录页点击底部「🎁 新用户福利 · 领取试用积分」查看二维码。

## 💰 计费

买断制积分包，无订阅、积分永不过期：

| 积分包 | 价格 | 说明 |
|---|---|---|
| 500 积分 | ¥19.9 | 约可完成 10+ 套页面全流程 |
| 2000 积分 | ¥69 | 高频使用者更划算 |

消耗明细：初稿 5 积分 + 拆解 2 积分 + 每个素材 3 积分。以一套 10 个素材的页面为例，全流程约 **37 积分（不到 2 元）**。

## ❓ FAQ

<details>
<摘要><b>生成的素材里有文字吗？</b></摘要>
没有。所有素材均为无文字、透明底独立元素，文字请在代码中用 HTML/CSS 排版，方便随时修改。
</details>

<details>
<summary><b>没有参考图，只有一句想法可以吗？</b></summary>
可以。<code>image</code> 参数是可选的，纯提示词同样能走完整流水线。
</details>

<details>
<summary><b>个别素材生成失败了怎么办？</b></summary>
每个素材都是独立子任务，失败可单独重试，不影响其他素材，也不重复扣费（失败自动退积分）。
</details>

<details>
<summary><b>支持什么格式导出？</b></summary>
ZIP 打包，支持 1x/2x/3x 缩放与 PNG/WebP 格式，均不限量开放。
</details>

<details>
<summary><b>技术栈是什么？</b></summary>
后端 ThinkPHP 8 + MySQL + database 队列 + 阿里云 OSS；视觉模型采用阿里云百炼多模态模型（qwen-vl 系）。
</details>

---

<div align="center">

**[🌐 立即体验 →](https://ui.xiaozhusho.top)**  觉得有用的话，给个 Star ⭐ 支持一下吧！

</div>
