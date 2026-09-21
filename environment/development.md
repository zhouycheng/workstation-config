# 开发与工具能力

以下内容来自 2026 年 9 月 20 日的本机实测（系统重装后的恢复基线）。CLI 统计以 `command -v` 能找到入口为准；MCP 统计来自 `~/.codex/config.toml`。

## 概览

| 类型 | 数量 | 当前状态 |
| --- | ---: | --- |
| CLI 命令 | 50 | 均可在当前 `PATH` 中找到（含 `codegraph`） |
| Codex MCP | 6 | 5 个启用、`computer-use` 停用，见下表 |
| Skills | 72 | `~/.agents/skills/` 全部就位 |

## CLI 清单

| 类别 | 数量 | 命令 |
| --- | ---: | --- |
| Shell、源码和终端 | 22 | `rg`、`git`、`gh`、`git-filter-repo`、`jq`、`bat`、`lazygit`、`brew`、`curl`、`rsync`、`ssh`、`openssl`、`zip`、`unzip`、`starship`、`tmux`、`yazi`、`zoxide`、`eza`、`btop`、`htop`、`codegraph` |
| 运行时和包管理 | 17 | `uv`、`uvx`、`python3`、`pip3`、`node`、`npm`、`npx`、`ruby`、`bundle`、`gem`、`rustc`、`cargo`、`rustup`、`java`、`javac`、`dart`、`flutter` |
| 构建（Xcode CLT 提供） | 8 | `make`、`cmake`、`ninja`、`xcodebuild`、`swift`、`clang`、`clang++`、`lldb` |
| 数据 | 1 | `sqlite3` |
| 文档提取 | 2 | `pdftotext`、`pdftoppm` |

## 关键运行时

- Rust：由 rustup 官方安装管理（`~/.cargo`、`~/.rustup`），stable 1.98.1，支持 `rust-toolchain.toml` 钉版本。
- Flutter：3.47.5 stable，brew cask 管理（`/opt/homebrew/share/flutter`），内置 Dart 3.13.4 与 DevTools。升级用 `flutter upgrade`，不走 `brew upgrade`。
- Java：Homebrew OpenJDK 26.0.1。未接入 `/Library/Java/JavaVirtualMachines`，依赖 `java_home` 的 GUI 工具找不到它；终端 PATH 使用正常。
- Node：系统级 node/npm/npx 已恢复。

## Codex MCP（当前配置）

| 名称 | 入口 | 状态和用途 |
| --- | --- | --- |
| `node_repl` | Codex 内置 Node.js REPL | 启用；执行受信任的本机 JavaScript 工具调用 |
| `computer-use` | Codex Computer Use 客户端 | 停用（`enabled = false`） |
| `context7` | `npx @upstash/context7-mcp` | 启用；查询库和框架文档 |
| `lark_mcp` | `npx @larksuiteoapi/lark-mcp` | 启用；飞书能力，待用户 Access Token 授权后可调用 |
| `codegraph` | `codegraph serve --mcp` | 启用；预索引代码知识图谱，token 高效读项目；每个项目首次使用前跑 `codegraph init` 建图 |
| `freecad` | `uvx freecad-mcp` | 启用；需 FreeCAD 应用打开且 RPC Server 运行中 |

MCP 显示为已加载只说明配置会被 Codex 读取。真正调用前还要确认相应应用、网络、账号和后台服务已经就绪。

## 已知边界

重装后有明确决策或暂缓的项，任务涉及时应先与用户确认而不是假定存在：

- **Xcode 完整版未装**：只有 Command Line Tools。iOS/macOS 桌面构建、CocoaPods（`pod`）不可用；需要时用户会自行安装。
- **Android 工具链未装**：`adb`、`scrcpy`、`apktool`、`jadx` 缺失；需要时用户会自行安装。
- **ffmpeg 按决策不装**：framelean 项目自包含 ffmpeg，系统级安装没有必要。
- **容器与数据客户端未装**：`docker`、`kubectl`、`psql`、`redis-cli`、`mysql`、`mongosh`、`duckdb` 等；需要时 `brew install` 即可。
- **文档转换链未装**：`pandoc`、`qpdf`、`soffice`、`magick`、`tesseract`、`d2`、`glow` 等；需要时 `brew install`。
- **Agent CLI 不在 PATH**：`codex`、`claude`、`firecrawl`、`playwright` 均缺失；Codex 桌面版内置 CLI 仍可用。
- **SSH 密钥未重建**：`ssh` 命令可用，但 `~/.ssh` 为空，SSH 远端登录和 git SSH 协议暂不可用（gh 走 HTTPS 正常）。
- FreeCAD 及其 MCP 未安装，`uvx freecad-mcp` 调用前需先恢复该链路。

## 当前运行条件

- 终端为 Ghostty；`~/.zshrc` 加载 uv、cargo、starship、zoxide，提示符带时间戳，`history -i` 可查历史时间。
- `zoxide` 已初始化，`z 关键词` 智能跳转可用。
- `~/.codex/AGENTS.md` 与本 environment 目录软链自 `~/codex-config` 仓库，修改清单后需提交到该仓库保持同步。

项目自己的运行时、包管理、构建、测试和格式规则优先。每次调用会改写文件、外部系统或账号数据的工具前，都要重新确认作用域和当前状态。
