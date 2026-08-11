# 战锤3 MOD 开发 Skill 集合

面向 AI 编程助手（Qoder / Claude Code / Codex / Cursor 等）的《全面战争：战锤Ⅲ》MOD 开发技能包。包含 4 个独立 skill，覆盖从 DB 表编辑、Lua 脚本、本地化翻译到角色创建、美术资源的完整开发流程。

## 包含的 Skill

| Skill | 用途 |
|------|------|
| `warhammer-mod-development` | 主开发指南：TSV 表格式、Lua 脚本模式、DB 表用途、法术/技能树/动作等。含 15 份 references 子文档 |
| `warhammer-mod-translation` | 本地化文本（loc/TSV）翻译与校对规范、术语查证流程 |
| `warhammer-mod-character-creation` | 新建传奇领主/英雄角色的完整流程与 DB 清单 |
| `warhammer-mod-art` | 技能图标、角色兵牌、立绘、porthole 等美术资源绘制规范 |

## 目录结构

```
Warhammer3Mod/
├── .qoder/skills/   # Qoder 技能目录（4 个 skill 本体，随仓库提交）
│   ├── warhammer-mod-art/SKILL.md
│   ├── warhammer-mod-character-creation/SKILL.md
│   ├── warhammer-mod-development/
│   │   ├── SKILL.md
│   │   └── references/          # 15 份 DB 表分域文档
│   └── warhammer-mod-translation/SKILL.md
├── docs/                        # development skill 引用的 7 份专题文档
│   ├── 战锤3原版单位Attribute效果整理.md
│   ├── 战锤3原版战斗脚本函数整理.md
│   ├── 战锤3原版接触效果整理.md
│   ├── 战锤3原版法术类角色技能整理.md
│   ├── 战锤3原版能力强度Intensity整理.md
│   ├── 战锤3原版领主与英雄动作整理.md
│   └── 战锤3法术相关DB字段说明.md
├── 源码/           # 游戏原版脚本、UI、DB文件（RPFM 解包数据，本地参考，不提交）
├── 多语言/         # 原版本地化（local_cn 简体 / local_zh 繁体 / local_en 英文）+ 术语库.md（不提交）
└── selfMods/       # 所有MOD文件夹（文档中简称 mod/）
```

> 技能包本体位于 `.qoder/skills/`（Qoder 项目级技能发现目录，真实目录，随仓库提交）；`docs/` 位于仓库根，两者相对结构固定，clone 后即可在 Qoder 中直接使用。

## 安装方法

> **重要**：`warhammer-mod-development` 通过相对路径引用 `docs/` 下的专题文档（`../../docs/战锤3*.md`）。**请保持 `skills/` 与 `docs/` 的相对结构**，否则 development skill 内的专题文档链接会失效。

### 方式一：Qoder（本机已安装）

技能包本体位于仓库 `.qoder/skills/`（Qoder 项目级技能发现目录），clone 后在 Qoder 中打开本仓库即可自动发现；同时用户级 `~/.qoder/skills/` 通过**目录链接（Junction）**指向仓库 `.qoder/skills/` 下的同名 skill，无需复制，改动仓库即全局生效：

| 位置 | 说明 |
|------|------|
| `<仓库根>/.qoder/skills/`（项目级） | 4 个 skill 本体（真实目录，随仓库提交），Qoder 打开本仓库自动发现 |
| `~/.qoder/skills/`（用户级） | 4 个 Junction 分别链接到仓库 `.qoder/skills/` 下同名目录 |

重新创建用户级链接的命令（PowerShell）：

```powershell
$src = "D:\GitHubProject\Warhammer3Mod\.qoder\skills"
New-Item -ItemType Junction -Path "$env:USERPROFILE\.qoder\skills\warhammer-mod-development" -Target "$src\warhammer-mod-development"
# ... 其余 3 个 skill 同理
```

### 方式二：其他客户端（整体放置，链接完整）

将本仓库整个根目录放到任意位置，然后把 `skills/` 下的 4 个子目录**连同 `docs/` 一起**做软链接或复制到客户端 skills 目录。

各客户端默认 skills 目录：

| 客户端 | 路径 |
|--------|------|
| Qoder | `~/.qoder/skills/` |
| ZCode | `~/.zcode/skills/` |
| Claude Code | `~/.claude/skills/` |
| Codex CLI | `~/.codex/skills/` |
| Cursor | `~/.cursor/skills/` |

**最稳妥做法**：把整个 `Warhammer3Mod/` 文件夹放到客户端 skills 目录的上一级，再将 `skills/` 下 4 个子目录复制进去，`docs/` 保留在 `skills/` 的同级。

### 方式三：仅放置 skills（专题文档链接失效）

如果你只需要基础开发能力、不需要查阅法术/动作专题文档，可直接把 `skills/` 下 4 个子目录复制到客户端 skills 目录。`docs/` 相关链接会失效，但不影响 skill 主体功能。

## 依赖（本包不含，需自行准备）

本 skill 假定你已具备以下工作区环境（文档中多处引用）：

- **游戏本体源码**：解包后的原版脚本、UI、DB 文件（`源码/` 目录）
- **多语言本地化文件**：原版 `/CN`、`/EN` 本地化包（`多语言/` 目录）

这些资源涉及游戏版权，请通过 RPFM 等工具自行从游戏目录解包，本包不提供。

## 更新同步

本仓库 `.qoder/skills/` 与 Qoder 用户级技能目录（`~/.qoder/skills/`）通过目录链接保持同步——**只改仓库一处，Qoder 即自动生效**。如发现内容过时或有误，欢迎提 issue。
