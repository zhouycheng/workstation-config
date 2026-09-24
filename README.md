# Workstation configuration

这个仓库管理 macOS 开发工作站的**期望配置**。Codex 是其中一个子项；Wiki 记录设计、迁移和故障过程。本机已安装版本、CLI 路径、探测错误和对账结果写入 `~/.local/state/env/inventory.json`，不进 Git。

## 目录

| 路径 | 职责 |
| --- | --- |
| `macos/manifest/` | Brewfile、受管能力探测目标、zsh、Android/Gradle、GUI 环境声明 |
| `codex/` | 部署到 `~/.codex` 的全局规则和稳定能力导航 |
| `.agents/skills/` | 工作站审计、供给、Android/Flutter、shell 四个项目技能 |
| `scripts/` | 唯一的 Brewfile 探测/声明/验证脚本及本机状态生成器 |
| `bin/workstation` | 只读检查、状态读取、按组件部署和落位验证 |

以本仓库为 Codex 当前项目时，项目技能可以从 `.agents/skills/` 发现。独立 `skills` 仓库继续管理其他个人技能。

## 新机和日常使用

```sh
git clone https://github.com/zhouycheng/workstation-config.git ~/workstation-config
cd ~/workstation-config
bin/workstation check
bin/workstation inventory refresh
bin/workstation inventory status
```

`check` 只读；无参数仅显示用法。先读差异，再按实际决定逐项安装或部署。组件落位命令为 `bin/workstation apply codex|shell|android|gui`，随后运行 `bin/workstation verify`。首次 `apply gui` 安装本机 LaunchAgent 并立即刷新状态；以后在本地时间 00:00、06:00、12:00、18:00 只读刷新。普通终端启动不下载分发或安装包。

清单超过 12 小时或配置摘要变化标为 stale；探测失败记为 unknown，并保留上一份成功分区供排障。执行依赖某项能力的操作前仍需定向核验它。`codex/environment/development.md` 是带日期的历史核验记录，不作为实时状态。

Android/Flutter 工具升级后，先核对 `macos/manifest/gradle/distributions.json` 与真实模板及官方 SHA-256，再执行 `android_env check → prepare → verify`。`prepare` 从镜像准备标准 Wrapper 缓存，不修改新项目的 Wrapper URL。`proxy`、`flutter_source`、`gradle_mirror` 和 `flutter_new` 的旧调用方式保留。SDK、NDK、模拟器映像与 IDE Marketplace 单独检查。

不把密钥、认证、完整环境变量、Gradle 缓存或测试项目放进仓库。对系统代理和 TUN 的切换需要单独处理；本仓库的只读审计与定时任务不会切换它们。
