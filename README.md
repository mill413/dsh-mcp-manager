# dsh-mcp-manager

> **参考来源**：本项目是 [`Js2Hou/dsh-mcp-manager`](https://github.com/Js2Hou/dsh-mcp-manager) 的本地衍生分支（fork），由 mill413 维护；上游代码、许可证与仓库历史均予保留。
>
> **包标识**：本分支包名为 `@mill413/dsh-mcp-manager`，**未发布到 npm**（npm 与插件市场里收录的是上游包）。请按下方「安装」从本仓库本地安装。

<!-- Hero -->
<div align="center">
  <b style="font-size: 1.15em;">MCP 可视化管理器：装没装、连没连，一目了然</b><br /><br />
  <code>查看列表</code> <code>新增删除</code> <code>启用停用</code> <code>连接状态</code> <code>连接测试</code> <code>中英双语</code><br />
  <code>DeepSeek Harness 0.1.6-alpha.2</code> <code>web profile</code><br /><br />
  <b>设置 → MCP</b> 一站管理 DeepSeek Harness 里的所有 MCP 服务器，<br />
  无需再手改 <code>cordis.patch.yml</code> —— 所有修改即改即生效（HMR 热应用）。
</div>

<div align="center">
  <img src="./assets/market-screenshot.png" alt="设置 → MCP：服务器列表、状态胶囊与操作按钮" width="760" />
</div>

<div align="center">

[![License](https://img.shields.io/github/license/mill413/dsh-mcp-manager)](LICENSE)
[![DeepSeek Harness](https://img.shields.io/badge/DeepSeek%20Harness-0.1.6--alpha.2-4d94ff)](#-环境要求)

</div>

<div align="center">
  🌏 <a href="./README.md"><b>中文</b></a> · <a href="./README_EN.md">English</a>
</div>

## ✨ 功能一览

- **📋 服务器列表**：列出所有已安装/启用的 MCP 服务器（`@deepseek-ai/dsh-mcp-client` 实例）——`serverName`、传输方式（`stdio` / `streamable-http`）、URL / 命令、启用状态、加载阶段、已注册工具数
- **➕ 新增 / ➖ 删除**：表单添加 MCP 服务器（stdio 与 streamable-http，支持 env / headers / args / cwd / 超时 / failOnStartupError），带格式与重名校验；一键删除
- **🔌 启用 / 停用**：随时切换，工具随之热连接 / 热断开
- **📶 连接状态**：每台服务器实时状态胶囊（Connected · N tools / Failed / Loading / Disabled）+ 独立 **测试** 探测（`initialize` + `tools/list`，报告延迟与工具数）
- **✏️ 编辑**：在被编辑卡片原位展开表单，保存即应用
- **🌏 多语言**：界面文案跟随 DSH 语言（zh / en）实时切换
- **💾 持久化**：所有修改写入 profile 的 `cordis.patch.yml`，重启后保留；页面底部显示文件路径

## 📦 环境要求

| 项 | 要求 |
|---|---|
| DeepSeek Harness | **`0.1.6-alpha.2`** —— 即 `package.json` 中 `dsh.compatibility.dshReleases` 声明并对齐的宿主版本 |
| Node.js | ≥ 22.19 |
| pnpm | 11（构建 `lib/` 产物用） |
| Profile | `web`（先跑过一次 `dsh web`，初始化 `~/.dsh/profiles/web`） |

## 🚀 安装

本分支**不通过 npm / 插件市场分发**（市场里收录的是上游 `@js2hou/dsh-mcp-manager`），从本仓库本地安装。

### 方式一 · 本地 checkout（推荐）

`lib/` 构建产物已入库，clone 后可直接构建安装；用 `link:` 方式挂载，改完源码重新 `pnpm build` 即生效。

```sh
git clone https://github.com/mill413/dsh-mcp-manager.git
cd dsh-mcp-manager
pnpm install
pnpm build

# 装进正式 web profile（DSH 由 Launcher 托管时，用 DSH_LAUNCHER_DSH_COMMAND 指向的 runtime CLI）
dsh plugin --profile web add "$PWD"
```

也可直接跑仓库脚本（检测到本地 checkout 会自动用 `link:` 安装）：

```sh
bash scripts/install.sh                                          # macOS / Linux
powershell -ExecutionPolicy Bypass -File .\scripts\install.ps1   # Windows
```

### 方式二 · 本地 tarball

```sh
pnpm install
pnpm pack     # 产出 mill413-dsh-mcp-manager-<version>.tgz

dsh plugin --profile web add /绝对路径/mill413-dsh-mcp-manager-0.1.5.tgz
```

<details>
<summary><b>方式三 · GitHub 源安装（无需本地构建，<code>lib/</code> 已入库）</b></summary>

```sh
dsh plugin --profile web add github:mill413/dsh-mcp-manager
```

</details>

装完后**硬刷新浏览器**（Cmd/Ctrl+Shift+R），打开 **设置 → MCP**。若没出现 MCP 页签，重启一次 DSH（host 半首次挂载需要）。

> `dsh plugin --profile web add` 会自动登记依赖、识别包内 `dsh.bundle.patch` 并加入 `dsh.profile.bundles`，无需手改 `cordis.patch.yml`。

<details>
<summary><b>更新</b></summary>

本地 checkout 模式：`git pull` 后 `pnpm install && pnpm build`，再 `dsh plugin --profile web add "$PWD"` 重挂一次。client 改动硬刷新浏览器即可；host 改动需重启 DSH。

</details>

<details>
<summary><b>常见问题</b></summary>

| 现象 | 原因与解决 |
|---|---|
| 装完没看到 MCP 页签 | 硬刷新（Cmd/Ctrl+Shift+R）；仍没有就重启 DSH 一次（host 半首次挂载需要）。 |
| 出现**两个 MCP 页签** | 双挂载：`~/.dsh/profiles/web/cordis.patch.yml` 里还留着旧的手动挂载行，删掉那段 `- insert: ... mcp-manager ...`。 |
| 报「找不到 profile 目录」 | 先跑一次 `dsh web`，让它初始化 `~/.dsh/profiles/web`。 |
| 报 `minimum release age` | 只在安装 npm 已发布包时出现；本分支走本地 `link:` / tarball，不应触发。 |
| Obsidian MCP 报 401 | 检查 headers 格式：应为 `Authorization: Bearer <api-key>`，不要带引号（表单已支持直接粘贴 `"Key": "value"` 自动去引号）。 |
| 修改配置后未生效 | 本插件所有修改走 HMR 热应用，等 1–2 秒自动刷新；页面右上角可手动刷新。 |

</details>

## 📖 使用说明

打开 **设置 → MCP**：

- **添加服务器**：填写 条目 ID、`serverName`、传输方式及对应字段（`streamable-http` 填 URL；`stdio` 填 command / args / env / cwd）。面板做格式与重名校验，重复的 id / serverName 会被拒绝。
- 每张卡片显示实时状态、连接目标与工具数；可执行 **启用 / 停用**、**测试**（连接探测）、**编辑**（原位表单）、**删除**。
- 页面底部显示正在编辑的补丁文件路径。

## ⚙️ 配置

插件自身在 loader 中的行配置支持一个可选字段：

| 字段 | 说明 |
|---|---|
| `patchFile` | 要编辑的用户补丁层绝对路径。默认 `$DSH_HOME/profiles/web/cordis.patch.yml`。 |

## 🏗️ 架构

- **宿主端**（`src/index.ts`）在共享 Connection 通道上注册经认证的精确 Fetch 路由 `/api/mcp-manager/*`：`list`（遍历 `ctx.loader` 中的 `@deepseek-ai/dsh-mcp-client` 条目 + `ctx.tools` 统计工具数）、`add` / `remove` / `setEnabled` / `update`（编辑 profile 补丁层，持久化并经 HMR 应用）、`probe`（独立 MCP SDK 连接探测）、`patchInfo`。运行时零 `@deepseek-ai` 依赖（js-yaml 方言、`isJsExpr` 均内联），可放在任意路径安装。
- **浏览器端**（`src/client`）注册 设置 → MCP 页（`settings.section` 槽位，order 18），经 `ctx.locale` 提供中英双语，与宿主端仅通过 RPC 路由通信——浏览器端不直接访问文件系统。
- **测试 fixture**：`test/fixtures/mcp-test-server.mjs` 是一个最小 MCP stdio 服务器，用于端到端验证。

## 🛠️ 开发

```bash
pnpm install
pnpm typecheck   # tsc --noEmit；SDK 类型来自 devDependencies 中固定版本的 @deepseek-ai/* 包
pnpm build       # esbuild：lib/index.js（宿主端）+ lib/client.js（ModuleLoader 浏览器 bundle）
```

- 改 `src/` 后请重新 `pnpm build` 并提交 `lib/`（GitHub 源安装直接用它，无需本地构建）。
- client 改动：硬刷新浏览器即可；host 改动：需重启 DSH。
- 本仓库构建与安装**不依赖任何 harness 源码 checkout**：SDK 从 npm 取固定版本，`pnpm install --frozen-lockfile` 即可独立复现。

## 📄 来源与许可

- **参考来源（上游）**：[`Js2Hou/dsh-mcp-manager`](https://github.com/Js2Hou/dsh-mcp-manager)
- **本分支维护**：[`mill413/dsh-mcp-manager`](https://github.com/mill413/dsh-mcp-manager)
- **许可证**：[MIT](./LICENSE)，Copyright (c) 2026 Js2Hou —— 上游许可证与署名随源码保留
