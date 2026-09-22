# dsh-mcp-manager

> **Upstream source**: this project is a locally maintained fork of [`Js2Hou/dsh-mcp-manager`](https://github.com/Js2Hou/dsh-mcp-manager), kept by mill413. Upstream code, license, and repository history are retained.
>
> **Package identity**: this branch is published as `@mill413/dsh-mcp-manager`, which is **not published to npm** (npm and the plugin marketplaces carry the upstream package). Install it locally from this repository as described under [Install](#-install).

<!-- Hero -->
<div align="center">
  <b style="font-size: 1.15em;">A visual MCP manager — installed or not, connected or not, at a glance</b><br /><br />
  <code>Server list</code> <code>Add / Remove</code> <code>Enable / Disable</code> <code>Connection status</code> <code>Connectivity test</code> <code>zh / en</code><br />
  <code>DeepSeek Harness 0.1.6-alpha.2</code> <code>web profile</code><br /><br />
  Manage every MCP server in DeepSeek Harness from <b>Settings → MCP</b>,<br />
  no more hand-editing <code>cordis.patch.yml</code> — every change applies live (HMR hot reload).
</div>

<div align="center">
  <img src="./assets/market-screenshot.png" alt="Settings → MCP: server list, status pills, and actions" width="760" />
</div>

<div align="center">

[![License](https://img.shields.io/github/license/mill413/dsh-mcp-manager)](LICENSE)
[![DeepSeek Harness](https://img.shields.io/badge/DeepSeek%20Harness-0.1.6--alpha.2-4d94ff)](#-requirements)

</div>

<div align="center">
  🌏 <a href="./README.md">中文</a> · <a href="./README_EN.md"><b>English</b></a>
</div>

## ✨ Features

- **📋 Server list** — every installed/enabled MCP server (`@deepseek-ai/dsh-mcp-client` instance): `serverName`, transport (`stdio` / `streamable-http`), URL / command, enabled state, loader phase, registered tool count
- **➕ Add / ➖ Remove** — validated form for stdio and streamable-http servers (env / headers / args / cwd / timeout / failOnStartupError); duplicate id/serverName rejected; one-click removal
- **🔌 Enable / Disable** — toggle anytime; tools hot-connect / hot-disconnect
- **📶 Connection status** — live status pill per server (Connected · N tools / Failed / Loading / Disabled) plus an independent **Test** probe (`initialize` + `tools/list`) reporting latency and tool count
- **✏️ Edit** — the edit form opens in place of the card being edited; save applies immediately
- **🌏 Localized** — UI copy follows the DSH language (zh / en) in real time
- **💾 Persistent** — every mutation is written to the profile's `cordis.patch.yml` and survives restarts; the footer shows the file path

## 📦 Requirements

| Item | Requirement |
|---|---|
| DeepSeek Harness | **`0.1.6-alpha.2`** — the host version declared by `dsh.compatibility.dshReleases` in `package.json` |
| Node.js | ≥ 22.19 |
| pnpm | 11 (used to build the `lib/` bundles) |
| Profile | `web` (run `dsh web` once to initialize `~/.dsh/profiles/web`) |

## 🚀 Install

This branch is **not distributed through npm or the plugin marketplaces** (those carry the upstream `@js2hou/dsh-mcp-manager`); install it locally from this repository.

### Option 1 · Local checkout (recommended)

The built `lib/` bundles are committed, so a clone can build and install directly; installing via `link:` means a rebuild is picked up without reinstalling.

```sh
git clone https://github.com/mill413/dsh-mcp-manager.git
cd dsh-mcp-manager
pnpm install
pnpm build

# Install into the web profile (when DSH is managed by the Launcher, use the runtime CLI
# pointed to by DSH_LAUNCHER_DSH_COMMAND)
dsh plugin --profile web add "$PWD"
```

Or run the bundled script (it detects a local checkout and installs with `link:`):

```sh
bash scripts/install.sh                                          # macOS / Linux
powershell -ExecutionPolicy Bypass -File .\scripts\install.ps1   # Windows
```

### Option 2 · Local tarball

```sh
pnpm install
pnpm pack     # produces mill413-dsh-mcp-manager-<version>.tgz

dsh plugin --profile web add /absolute/path/mill413-dsh-mcp-manager-0.1.5.tgz
```

<details>
<summary><b>Option 3 · GitHub source install (no local build needed, <code>lib/</code> is committed)</b></summary>

```sh
dsh plugin --profile web add github:mill413/dsh-mcp-manager
```

</details>

Then **hard-refresh the browser** (Cmd/Ctrl+Shift+R) and open **Settings → MCP**. If the MCP tab does not appear, restart DSH once (first-time host mounting).

> `dsh plugin --profile web add` registers the dependency, detects the package's `dsh.bundle.patch`, and adds it to `dsh.profile.bundles` — no manual `cordis.patch.yml` edits needed.

<details>
<summary><b>Update</b></summary>

Local-checkout mode: `git pull`, then `pnpm install && pnpm build`, then re-run `dsh plugin --profile web add "$PWD"`. Client changes need only a hard refresh; host changes need a DSH restart.

</details>

<details>
<summary><b>FAQ</b></summary>

| Symptom | Cause / fix |
|---|---|
| No MCP tab after install | Hard refresh (Cmd/Ctrl+Shift+R); if still missing, restart DSH once (first-time host mounting). |
| **Two MCP tabs** | Double-mount: a stale manual `- insert: ... mcp-manager ...` row still lives in `~/.dsh/profiles/web/cordis.patch.yml` — delete it. |
| "Profile not found" | Run `dsh web` once to initialize `~/.dsh/profiles/web`. |
| `minimum release age` error | Only applies to npm-published packages; this branch installs from a local `link:` / tarball and should not trigger it. |
| Obsidian MCP returns 401 | Check the header format: `Authorization: Bearer <api-key>` without surrounding quotes (the form strips quotes from pasted `"Key": "value"` lines). |
| Config change not applied | All mutations hot-apply via HMR within 1–2s; use the manual refresh button in the page header if needed. |

</details>

## 📖 Usage

Open **Settings → MCP**:

- **Add server** — fill in the entry id, `serverName`, transport, and transport-specific fields (`streamable-http`: URL; `stdio`: command / args / env / cwd). The panel validates format and rejects duplicate ids / serverNames.
- Each card shows the live status, target, and tool count, with **Enable / Disable**, **Test** (connectivity probe), **Edit** (inline form), and **Remove**.
- The footer shows the patch file being edited.

## ⚙️ Configuration

The plugin's loader row accepts one optional field:

| Field | Description |
|---|---|
| `patchFile` | Absolute path of the user patch layer to edit. Defaults to `$DSH_HOME/profiles/web/cordis.patch.yml`. |

## 🏗️ Architecture

- **Host half** (`src/index.ts`) registers authenticated exact Fetch routes `/api/mcp-manager/*` on the shared Connection channel: `list` (enumerates `@deepseek-ai/dsh-mcp-client` entries via `ctx.loader` + tool counts via `ctx.tools`), `add` / `remove` / `setEnabled` / `update` (edits the profile patch layer, persisted and HMR-applied), `probe` (independent MCP SDK connectivity probe), `patchInfo`. Zero runtime `@deepseek-ai` imports (the js-yaml `!!js` dialect and `isJsExpr` are inlined), so it can be installed from any path.
- **Browser half** (`src/client`) registers the Settings → MCP section (`settings.section` slot, order 18), provides zh/en copy via `ctx.locale`, and talks to the host exclusively over the RPC routes — it never touches the filesystem.
- **Test fixture** — `test/fixtures/mcp-test-server.mjs` is a minimal MCP stdio server for end-to-end verification.

## 🛠️ Development

```bash
pnpm install
pnpm typecheck   # tsc --noEmit; SDK types come from the pinned @deepseek-ai/* devDependencies
pnpm build       # esbuild: lib/index.js (host) + lib/client.js (ModuleLoader browser bundle)
```

- After editing `src/`, re-run `pnpm build` and commit `lib/` (GitHub source installs use it directly).
- Client changes need only a hard refresh; host changes need a DSH restart.
- Building and installing this repo **never depends on a harness source checkout**: the SDK comes from pinned npm versions, reproducible with `pnpm install --frozen-lockfile`.

## 📄 Source & License

- **Upstream source**: [`Js2Hou/dsh-mcp-manager`](https://github.com/Js2Hou/dsh-mcp-manager)
- **This fork**: [`mill413/dsh-mcp-manager`](https://github.com/mill413/dsh-mcp-manager)
- **License**: [MIT](./LICENSE), Copyright (c) 2026 Js2Hou — upstream license and attribution are retained with the source
