# 战锤3原版能力强度（Intensity）整理

> 基于原版 `源码/db/db/special_ability_intensity_*_tables` 与中英本地化整理。  
> 用于「随击杀 / 近战时间 / 受伤 / 魔风 / 范围内单位数… 提升能力效果」类被动与法术。  
> **`intensity_source` 属于引擎固定枚举，禁止新增自定义源。**  
> 文档日期：2026-07-27。

---

## 1. 总览

能力强度系统由三张表构成：

| 表 | 作用 |
|----|------|
| `special_ability_intensity_settings_tables` | 某能力如何积攒强度（源、类型、上下限、衰减） |
| `special_ability_intensity_sources_tables` | 全部合法 `intensity_source` 枚举 |
| `special_ability_intensity_types_tables` | 全部合法 `intensity_type` 枚举 |

强度改变的是该能力挂载 **phase** 的数值效果（`special_ability_phase_stat_effects` 的 add/mult、以及 `heal_amount` 等），不是战役 effect bundle。

典型链路：

```
unit_abilities / unit_special_abilities
        │
        ├── special_ability_to_special_ability_phase_junctions → phases（效果本体）
        └── special_ability_intensity_settings（如何随战斗状态放大/缩小上述效果）
```

UI 补充说明常挂在：

`unit_abilities_to_additional_ui_effects_juncs` → `unit_abilities_additional_ui_effects`

例如击杀类：`wh3_dlc26_ability_intensity_kills_50/100/150/200/300/500`。

---

## 2. `special_ability_intensity_settings` 字段

| 字段 | 含义 |
|------|------|
| `ability` | → 能力 key（通常与 `unit_special_abilities.key` 同名） |
| `default_amount` | 默认强度（开战时 / 无源数据时的基准） |
| `max_amount` | 强度上限（`linear_multiplier` 下满强度对应 phase 表里的完整数值） |
| `intensity_source` | → `special_ability_intensity_sources.source_type` |
| `intensity_type` | → `special_ability_intensity_types.type` |
| `intensity_decay_delay` | 停止积攒后，多久开始衰减；`-1` = 通常不衰减 |
| `intensity_decay_duration` | 衰减到 0 所需时间；`0` + delay`-1` = 常见「不衰减」组合 |
| `reset_intensity_in_reinforcement_pool` | 在增援池中是否重置强度 |

### `intensity_type`（仅 3 种）

| type | 作用 | 原版用量（约） |
|------|------|----------------|
| `set` | 直接采用强度源给出的档位/数值（如精气之风 1/2） | 最多（约 415） |
| `linear_multiplier` | 从 0 → `max_amount` **线性**放大 phase 效果 | 约 97 |
| `inverse_linear_multiplier` | **反向**线性（源越大，效果越弱） | 约 3 |

**`linear_multiplier` 经验公式（设计用）：**

- phase 表填写的是「满强度」效果  
- 当前效果 ≈ `满强度效果 × (当前源值 / max_amount)`（封顶于 `max_amount`）  
- 例：`max_amount=200`，phase 中 `stat_melee_damage_base = 200 add` → 约等于每击杀 +1 普伤（到 200 封顶）

---

## 3. 全部 `intensity_source`（26）

来源：`special_ability_intensity_sources_tables`。  
「仅激活时更新」= 字段 `updates_only_when_ability_active`。  
「原版用量」= 在 `special_ability_intensity_settings` 中出现的次数（0 表示枚举存在但当前未挂能力）。

### 3.1 击杀 / 死亡

| source | 含义 | 仅激活时更新 | 原版用量 | 典型例子 |
|--------|------|--------------|----------|----------|
| `kills_made` | 本单位击杀数（**兵模**，非整队） | 是 | 26 | 嗜血欲、恐虐斧、屠戮与屠杀、链锯剑 |
| `nearby_all_deaths` | 附近任意实体死亡 | 是 | 13 | 赐予狂怒荣光、杀戮典范 |
| `nearby_enemy_deaths` | 附近敌军实体死亡 | 是 | 3 | 黑镰等 |
| `nearby_allied_deaths` | 附近友军实体死亡 | 是 | 1 | 战场补偿等 |

中文 UI 参考：

- 击杀：`效果强度随此部队的每次击杀而增加`（可附「N 击杀时达到最大强度」）
- 附近死亡：`效果强度随着附近兵模的死亡而增加`

### 3.2 近战 / 时间 / 受伤

| source | 含义 | 仅激活时更新 | 原版用量 | 典型例子 |
|--------|------|--------------|----------|----------|
| `time_in_melee` | 近战交战累计时间 | 是 | 15 | 血怒、原始狂怒 |
| `time_active_relative_to_duration` | 相对本能力持续时间的已激活比例/时间 | 是 | 1 | — |
| `combined_time_in_melee_and_self_wom_spent` | 近战时间 + 自身已耗魔风 | 是 | 2 | — |
| `damage_taken` | 本单位已承受伤害 | 是 | 14 | 狂热决心、天象辐射 |

中文 UI 参考：

- 近战：`效果强度随近战时间的增加而增加`
- 受伤：`部队受到伤害时，效果强度增加`  
  （另有反向文案：受伤时强度下降 → 多配合 `inverse_linear_multiplier`）

### 3.3 魔风 / 精气之风

| source | 含义 | 仅激活时更新 | 原版用量 | 典型例子 |
|--------|------|--------------|----------|----------|
| `mastery_of_elemental_winds` | 精气之风精通层数 | **否** | 412 | 绝大多数学派法术（强度档） |
| `all_wom_spent` | 全场已消耗魔风 | 是 | 4 | — |
| `allied_wom_spent` | 友军已消耗魔风 | 是 | 1 | — |
| `allied_and_self_wom_spent` | 友军 + 自身已消耗魔风 | 是 | 3 | — |
| `enemy_wom_spent` | 敌军已消耗魔风 | 是 | 2 | — |

中文 UI 参考：`效果强度随消耗的魔法之风而攀升（消耗30点魔法之风时达到最强效果）`（具体上限以 `max_amount` 为准）。

> `mastery_of_elemental_winds` + `set` 是原版用量最大的组合：法术强度 1/2 档，与「击杀叠层」玩法不同。

### 3.4 范围内单位 / 角色 / 士气

| source | 含义 | 仅激活时更新 | 原版用量 | 典型例子 |
|--------|------|--------------|----------|----------|
| `enemies_in_range` | 范围内敌军**实体**数 | **否** | 3 | 无尽残暴、渴求挑战 |
| `enemy_characters_in_range` | 范围内敌军**角色**数 | 是 | 0 | （枚举可用，当前无挂载） |
| `enemy_in_range_low_morale` | 范围内领导力动摇及以下的敌军 | 是 | 9 | — |
| `allies_in_range` | 范围内友军数 | **否** | 0 | （枚举可用） |
| `units_in_range` | 范围内单位数 | 是 | 1 | — |

中文 UI 参考：

- 敌军实体：`效果强度随范围内每个敌方兵模而增加`
- 动摇敌军：`效果强度随范围内领导力处于动摇或更低的敌方部队数量而增加`
- 范围内单位：`效果强度随范围内部队数量而增加`

范围半径通常由该能力 `unit_special_abilities` 的 AOE / effect_range 相关字段决定。

### 3.5 震旦宁和 / 夺点

| source | 含义 | 仅激活时更新 | 原版用量 | 典型例子 |
|--------|------|--------------|----------|----------|
| `harmony` | 附近部队最高武道宁和倍率 | **否** | 2 | 武道宁和·阴 / 阳 |
| `capture_points_controlled_percentage` | 已控制夺点百分比 | **否** | 1 | — |

中文 UI 参考：`效果强度随附近一支部队附带的最高宁和化生加成而增加`。

### 3.6 地精 / 史奎格（标签部队）

依赖单位 attribute / 部队类型标签（如 `goblin_infantry`、`squig_herd`）：

| source | 含义 | 仅激活时更新 | 原版用量 |
|--------|------|--------------|----------|
| `goblin_infantry_in_range` | 范围内地精步兵 | 是 | 0 |
| `allied_goblin_infantry_in_range` | 范围内友军地精步兵 | 是 | 1 |
| `enemy_goblin_infantry_in_range` | 范围内敌军地精步兵 | 是 | 0 |
| `squig_herds_in_range` | 范围内史奎格兽群 | 是 | 0 |
| `allied_squig_herds_in_range` | 范围内友军史奎格兽群 | 是 | 1 |
| `enemy_squig_herds_in_range` | 范围内敌军史奎格兽群 | 是 | 0 |

中文 UI 参考：`效果强度随范围内地精部队数量而增加` / `…史奎格兽群…`。

---

## 4. 完整 key 清单（26）

```
all_wom_spent
allied_and_self_wom_spent
allied_goblin_infantry_in_range
allied_squig_herds_in_range
allied_wom_spent
allies_in_range
capture_points_controlled_percentage
combined_time_in_melee_and_self_wom_spent
damage_taken
enemies_in_range
enemy_characters_in_range
enemy_goblin_infantry_in_range
enemy_in_range_low_morale
enemy_squig_herds_in_range
enemy_wom_spent
goblin_infantry_in_range
harmony
kills_made
mastery_of_elemental_winds
nearby_all_deaths
nearby_allied_deaths
nearby_enemy_deaths
squig_herds_in_range
time_active_relative_to_duration
time_in_melee
units_in_range
```

---

## 5. MOD 设计速查

### 5.1 每击杀叠属性（本场战斗）

推荐：

| 项 | 建议 |
|----|------|
| `intensity_source` | `kills_made` |
| `intensity_type` | `linear_multiplier` |
| `max_amount` | 50 / 100 / 150 / 200 / 300 / 500（对齐原版 UI 文案档） |
| `default_amount` | 通常 `0` |
| 衰减 | `decay_delay=-1`，`decay_duration=0` |
| phase 数值 | 填**满强度**值；要「每杀 +1 武威」则 `add` 值 = `max_amount` |

击杀统计的是**兵模**，不是整支部队卡。

### 5.2 与「瞬回生命」的区别

| 需求 | 强度系统能否精确做到 |
|------|----------------------|
| 击杀越多，持续回血越快 | 能（`heal_amount` + `kills_made`，如饥饿如斯） |
| **每次击杀瞬间**回固定 % 生命 | **不能精确**；需战斗脚本轮询 `number_of_enemies_killed()` + `heal_hitpoints_unary` |

### 5.3 禁止事项

- 不要向 `special_ability_intensity_sources_tables` 新增自定义 source  
- 不要假设 UI 文案里的「附近施法」等描述等于新的 source key——以本表 26 个枚举为准  
- `set` 与 `linear_multiplier` 语义不同：法术精气之风用 `set`；击杀/受伤叠层多用 `linear_multiplier`

---

## 6. 资料来源

| 文件 | 内容 |
|------|------|
| `源码/db/db/special_ability_intensity_sources_tables/data__.tsv` | 全部 source 枚举 + `updates_only_when_ability_active` |
| `源码/db/db/special_ability_intensity_types_tables/data__.tsv` | `set` / `linear_multiplier` / `inverse_linear_multiplier` |
| `源码/db/db/special_ability_intensity_settings_tables/data__.tsv` | 各能力挂载与用量 |
| `多语言/local_en/text/db/unit_abilities_additional_ui_effects__.loc.tsv` | 强度相关 UI 英文说明 |
| `多语言/local_cn/text/localisation__.loc.tsv` | 对应中文说明 |
| `战锤3法术相关DB字段说明.md` §10.2 | 字段速查（本节的专题展开） |
