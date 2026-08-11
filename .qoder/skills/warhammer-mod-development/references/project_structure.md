# 项目结构说明

## 顶层目录

| 目录 | 用途 |
|------|------|
| `/skills` | 4 个技能包（`SKILL.md` + references）。Qoder 通过 `~/.qoder/skills/`（用户级）与工作区 `.qoder/skills/`（项目级）目录链接加载本目录，改动仓库即全局生效 |
| `/docs` | 7 份专题文档（法术DB、战斗脚本函数、接触效果、Attribute、Intensity、法术类角色技能、领主与英雄动作），由 development skill 引用 |
| `/.qoder` | Qoder 项目级配置目录（`skills/` 为指向 `skills/` 的目录链接） |
| `/多语言` | 原版本地化文件（`local_cn` 简体中文、`local_zh` 繁体中文、`local_en` 英文）+ 术语库（`术语库.md`，随翻译增长） |
| `/源码` | 战锤3原版脚本、UI、DB文件（RPFM 解包结构：`db/db/`、`data_script/script/`、`data/documentation/script`、`ui/ui/`）。所有官方数据在此，用于参考原始表结构和API |
| `/selfMods` | 战锤3 MOD文件夹（文档中简称 `mod/`），包含所有MOD的子目录 |

## MOD内部目录结构

每个MOD文件夹（如 `Better Khorne Technology`）的标准结构：

```
selfMods/<MOD名称>/
├── db/                              # 数据库表覆盖
│   ├── <table_name>/               # 表名目录（与源码db目录同名）
│   │   ├── !!!wyccc_xxx.tsv        # 三级叹号 = 主表/核心表
│   │   ├── !!wyccc_xxx.tsv         # 二级叹号 = 关联表
│   │   ├── wyccc_xxx.tsv           # 无叹号 = 普通数据表
│   │   └── ...
│   └── ...
├── script/
│   └── campaign/
│       ├── mod/                     # 自定义脚本（新建MOD功能放这里）
│       │   └── wyccc_xxx.lua
│       └── <原版脚本名>.lua        # 直接覆盖原版脚本（少用）
├── text/
│   └── db/
│       ├── wyccc_xxx_CN.loc.tsv     # 中文本地化
│       └── en/
│           └── wyccc_xxx_EN.loc.tsv # 英文本地化
└── <备注文件>                       # 如 备注.txt, xlsx等
```

## 已有MOD一览

| MOD | 路径 | 用途 |
|-----|------|------|
| Better Khorne Technology | `selfMods/Better Khorne Technology/` | 恐虐科技大修（新建，内容待填充） |

## 关键路径约定

- Lua自定义脚本：`script/campaign/mod/wyccc_<功能>.lua`
- DB表文件：`db/<table_name>/!!wyccc_<功能>.tsv`
- 本地化文本：`text/db/wyccc_<功能>_CN.loc.tsv` 和 `en/wyccc_<功能>_EN.loc.tsv`
- 所有文件使用 `wyccc_` 前缀
