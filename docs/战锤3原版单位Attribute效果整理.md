# 战锤3原版单位 Attribute 效果整理

> 基于原版 `源码/db/db/unit_attributes_tables/data__.tsv`（共 **84** 条）及中英本地化 `unit_attributes_bullet_text_*` / `ui_text_replacements` / `can_siege_tooltip` 整理。  
> 属性键属于引擎固定枚举，**禁止在 MOD 中新增自定义 attribute**；只能引用本表已有键。  
> 文档日期：2026-07-27。

---

## 1. 使用方式速查（MOD）

| 用途 | 表 / 手段 |
|------|-----------|
| 单位自带属性 | `land_units.attribute_group` → `unit_attributes_groups` → `unit_attributes_to_groups_junctions` |
| 技能相位施加/移除 | `special_ability_phase_attribute_effects`（`positive`=施加，`negative`=移除） |
| 效果永久启用 | `effect_bonus_value_unit_attribute_junctions` / `…_unit_set_unit_attribute_junctions` |
| 战斗上下文条件启用 | `battle_context_unit_attribute_junctions` |
| 脚本检测 | `has_attribute("key")`（战斗单位接口） |

说明文案来源：游戏内兵牌/技能 tooltip 的 `unit_attributes_bullet_text_<key>`（部分键走 `{{tr:…}}` 引用）。

---

## 2. 按效果分类总览

| 分类 | 属性键 |
|------|--------|
| 心理 / 士气 | `causes_fear` `causes_terror` `immune_to_psychology` `unbreakable` `encourages` `expendable` `contempt` `force_rally` `disciplined` `rampage` |
| 不稳定实体（溃而不逃、士气伤血） | `undead` `daemonic` `construct` `elemental` `bound_fire_daemon` |
| 冲锋 / 侧袭 | `charge_defense` `charge_defense_vs_large` `charge_reflection` `devastating_flanker` `flanking_immune` `ogre_charge` `ogre_charge_upgraded` `glorious_charge` |
| 机动 / 隐蔽 / 部署 | `flying` `always_flying` `hide_forest` `stalk` `snipe` `unspottable` `revealed` `guerrilla_deploy` `strider` `ignore_trees` `cant_run` `underground` |
| 体力 | `fatigue_immune` `fatigue_res` |
| 防御 / 远程格挡 / 建筑 | `armoured_vehicle` `can_block_missiles_360` `ballistic_plating` `wallbreaker` `can_siege` |
| 战斗限制 / 无敌 | `melee_disabled` `shoot_disabled` `silenced` `cannot_die` `invulnerable` `invulnerable_to_effects_ally` `invulnerable_to_effects_enemy` `ignore_imbue_contact_effects_ally` `ignore_imbue_contact_effects_enemy` |
| 射击 / 阵型 / 空中平台 | `mounted_fire_move` `formed_attack` `gunship` |
| 混沌印记 / 震旦阴阳 / 虎人 | `mark_khorne` `mark_nurgle` `mark_slaanesh` `mark_tzeentch` `yang` `yin` `tiger_warrior` |
| 协同标签（邻近加成） | `skink` `kroxigor` `peasant` `knight` |
| 特殊战斗规则 | `executor` `slayer` `unyielding_assault` `hellforged` `moulder_monster` `spell_mastery` `randomises_spells_on_cast` |
| 仅技能目标标签 | `boar_cavalry` `fanatic` `goblin_infantry` `gorger` `nasty_skulker` `night_goblin_archer` `orc_infantry` `spider` `squig` `squig_herd` `troll` |

---

## 3. 详细效果（按 key 字母序）

格式：**中文名**（英文名）— 效果摘要。括号内为引擎 key。

### always_flying — 永远飞行（Always Flying）

此部队永远处于飞行状态，无法落地。

### armoured_vehicle — 定向格挡（Directional Shield）

远程格挡概率随受击方向变化：

| 方向 | 倍率 |
|------|------|
| 前方 | 1.0× |
| 侧方 | 0.5× |
| 后方 | 0.1× |

### ballistic_plating — 防弹板甲（Ballistic Plating）

重型防弹装甲，可偏转包括直射火炮在内的弹道攻击。

### boar_cavalry — 野猪骑兵（Boar Cavalry）

**技能目标标签**：指定目标时视为野猪骑兵。无独立战斗数值效果。

### bound_fire_daemon — 绑定火焰恶魔（Bound Fire Daemon）

不稳定实体变体：

- 不会溃逃；领导力崩溃时受伤
- 物理抗性、火焰抗性
- 恐惧与惊骇免疫
- 大部分生命值仍在时额外视为**永不战败**

### can_block_missiles_360 — 全方位远程防御（360 Missile Block）

无论朝向如何，均可格挡来自任意方向的弹丸。

### can_siege — 攻城者（Siege Attacker）

> 本地化不走 `unit_attributes_bullet_text_*`，而用 `can_siege_tooltip`。

可直接攻击城门/城墙；无需建造攻城塔或攻城锤即可发动攻城战。

### cannot_die — 不灭（Cannot Die）

兵模可受伤，但不会死亡。

### cant_run — 徐行（Cannot Run）

无法奔跑，只能行走移动。

### causes_fear — 引发恐惧（Causes Fear）

- 附近敌军领导力下降（恐惧惩罚**不叠加**）
- 自身免疫恐惧

### causes_terror — 惊骇敌军（Causes Terror）

- 可使近战目标短时溃逃
- 自身免疫恐惧与惊骇

### charge_defense — 专擅抵御冲锋（Expert Charge Defence）

阵列齐整（bracing）时，无视**任意**攻击者的冲锋加成。

### charge_defense_vs_large — 抵御冲锋（大型部队）（Charge Defence vs. Large）

阵列齐整时，仅无视**大型**攻击者的冲锋加成。

### charge_reflection — 冲锋反制（Charge Reflection）

阵列齐整时，对正在冲锋的敌军造成额外伤害。

### construct — 构装体（Construct）

- 不会溃逃；领导力崩溃时受伤
- 恐惧与惊骇免疫
- 可由古墓技师等角色恢复生命并提升属性

### contempt — 蔑视异类（Contempt）

仅当溃逃友军也带有「蔑视异类」时，才会因此降低本部队领导力。

### daemonic — 恶魔（Daemonic）

- 不会溃逃；领导力**低下**时受伤（文案为 low，与亡灵等「崩溃」表述略有不同）
- 物理抗性
- 恐惧与惊骇免疫

### devastating_flanker — 致命迂回者（Devastating Flanker）

从敌方侧面或背后攻击时，冲锋加成**翻倍**。

### disciplined — 训练有素（Disciplined）

- 领主阵亡不掉领导力
- 溃逃后更容易重整  
> 英文原文带 `- NOT WH` 标注，战锤规则下实际生效情况需以游戏实测为准。

### elemental — 元素生物（Elemental）

- 不会溃逃；领导力崩溃时受伤
- 物理抗性
- 恐惧与惊骇免疫

### encourages — 激励友军（Encourage）

提升附近友军领导力。若同时处于领主光环与激励范围内，取二者中较大加成。

### executor — 刽子手（Executioner）

近战中处决生命值低于 **20%** 的目标单位。

### expendable — 炮灰（Expendable）

友军目睹炮灰溃逃时，非炮灰部队领导力不受影响；自身也是炮灰则仍会受影响。

### fanatic — 狂热者（Fanatic）

**技能目标标签**：指定目标时视为狂热者。

### fatigue_immune — 体力充沛（Perfect Vigour）

执行再费力的行动也不会损失体力。

### fatigue_res — 体力旺盛（Strong Vigour）

战斗等耗力行为对体力的影响更小。

### flanking_immune — 免疫侧袭（Immune to Flanking）

被侧袭时不受惩罚。

### flying — 可以飞行（Can Fly）

可以飞行（可升降，与 `always_flying` 不同）。

### force_rally — 重整（Rally）

立即激励部队进行重整。

### formed_attack — 保持阵型（Formation Attack）

近战时尽量保持阵型，少量提高近战防御。

### glorious_charge — 荣光冲锋（Glorious Charge）

冲入敌军时：

- 冲锋加成持续时间翻倍
- 敌军承受等同于被侧袭的领导力惩罚

### goblin_infantry — 地精步兵（Goblin Infantry）

**技能目标标签**。

### gorger — 猎食者（Gorger）

**技能目标标签**。

### guerrilla_deploy — 先锋部署（Vanguard Deployment）

可在部署区外部署。

### gunship — 炮艇机（Gunship）

- 纯远程输出，无近战能力
- 从正上方攻击地面目标
- 远程攻击飞行目标
- 部分乘员弹药无限

### hellforged — 地狱铸造（Hell-Forged）

可在战斗中通过恶魔铁匠的「重铸」技能恢复。

### hide_forest — 于森林中隐蔽（Hide (forest)）

可在森林中隐蔽，直到敌军靠得太近。

### ignore_imbue_contact_effects_ally — 忽略友方接触效果

忽略来自**盟友**接触效果的伤害、治疗与属性修正。

### ignore_imbue_contact_effects_enemy — 免疫接触效果（Immune to Contact Effects）

忽略**敌方**接触效果（如淬毒等）。

### ignore_trees — 林地居民（Woodsman）

可穿越树林（不被树木阻挡）。

### immune_to_psychology — 心理免疫（Immune to Psychology）

免疫恐惧与惊骇。

### invulnerable — 无敌（Invulnerable）

不会受到伤害。

### invulnerable_to_effects_ally — 免疫友方伤害效果

免疫友方造成的伤害类效果。

### invulnerable_to_effects_enemy — 免疫敌方伤害效果

免疫敌方造成的伤害类效果。

### knight — 骑士（Knight）

目睹带「农民」属性的友军溃逃，不影响本部队领导力。  
（与 `peasant` 形成邻近协同，见下。）

### kroxigor — 巨蜥（Kroxigor）

附近有「灵蜥」属性部队时：提高武器威力。

### mark_khorne — 恐虐印记（Mark of Khorne）

- 狂暴被动
- 提高法术抗性、护甲
- 降低近战防御

### mark_nurgle — 纳垢印记（Mark of Nurgle）

- 淬毒接触效果
- 提高生命值、近战防御
- 降低近战攻击

### mark_slaanesh — 色孽印记（Mark of Slaanesh）

- 心理免疫、地形适性
- 提高物理抗性、移动速度

### mark_tzeentch — 奸奇印记（Mark of Tzeentch）

- 魔屏（Barrier）
- 魔法攻击

### melee_disabled — 无法近战（Cannot melee）

禁止近战。

### moulder_monster — 腐坏氏族怪兽（Moulder Monster）

腐坏氏族造物；捕兽师与斯洛特可激发其战斗潜力，并在战斗中恢复。

### mounted_fire_move — 移动射击（Fire Whilst Moving）

可在移动中射击。

### nasty_skulker — 卑鄙潜伏者（Nasty Skulker）

**技能目标标签**。

### night_goblin_archer — 夜地精弓箭手（Night Goblin Archer）

**技能目标标签**。

### ogre_charge — 食人魔冲锋（Ogre Charge）

攻击阵列齐整的抵御冲锋单位时，仅损失 **一半** 冲锋加成。

### ogre_charge_upgraded — 巨力食人魔冲锋（Immense Ogre Charge）

同上场景，仅损失 **四分之一** 冲锋加成。

### orc_infantry — 兽人步兵（Orc Infantry）

**技能目标标签**。

### peasant — 农民（Peasant）

附近有「骑士」属性部队时：提高领导力。

### rampage — 癫狂（Rampage）

- 失控，自动攻击最近敌军
- 附近无敌军时随机游荡

### randomises_spells_on_cast — 秘术卷轴（Scrolls of Sorcery）

每次施法后，该单位所有法术被随机替换为其他法术。

### revealed — 现身（Revealed）

对敌军永久可见（无视视野）。并使下列属性失效：

- `hide_forest`
- `stalk`
- `unspottable`

### shoot_disabled — 无法射击（Cannot Shoot）

禁止射击。

### silenced — 沉默（Silenced）

无法使用主动技能或施法。

### skink — 灵蜥（Skink）

附近有「巨蜥」属性部队时：获得「引发恐惧」。

### slayer — 屠夫（Slayer）

战斗中武器威力**永不被降低**。

### snipe — 狙击（Snipe）

射击时保持隐蔽。

### spell_mastery — 精气之风精通（Mastery of Elemental Winds）

同一军队中两支及以上部队拥有此属性时，提高所施法术威力（强度叠加相关）。

### spider — 蜘蛛（Spider）

**技能目标标签**。

### squig — 史奎格（Squig）

**技能目标标签**。

### squig_herd — 史奎格兽群（Squig Herd）

**技能目标标签**。

### stalk — 潜行（Stalk）

在任意地形移动时保持隐蔽。

### strider — 地形适性（Strider）

无视地形造成的速度与战斗惩罚。

### tiger_warrior — 虎人战士（Tiger Warrior）

- 提高速度、冲锋加成
- 获得「凶暴伏击」技能

### troll — 巨魔（Troll）

**技能目标标签**。

### unbreakable — 永不战败（Unbreakable）

领导力永不下降，永不溃逃。

### undead — 亡灵（Undead）

- 不会溃逃；领导力崩溃时受伤
- 恐惧与惊骇免疫
- 可由亡灵法师等角色恢复生命  
> 吸血鬼伯爵子文化另有战役损耗相关描述（`wh2_dlc09_undead_description_wh_main_sc_vmp_vampire_counts`），战斗属性本身同上。

### underground — 地下（Underground）

处于地下：无法被攻击，也无法主动攻击（文案以恐惧之嘴为例）。

### unspottable — 隐蔽高手（Unspottable）

若当前位置可隐蔽，则敌军需非常接近才能发现。

### unyielding_assault — 不挠袭击（Unyielding Assault）

战斗中近战攻击**永不被降低**。

### wallbreaker — 破墙者（Wallbreaker）

- 可用近战摧毁城墙
- 对建筑伤害 **+50%**

### yang — 阳（Yang）

武道宁和判定中计为**阳**单位（邻近阴阳和谐）。

### yin — 阴（Yin）

武道宁和判定中计为**阴**单位。

---

## 4. 成对 / 组合关系速查

| 关系 | 属性 | 效果 |
|------|------|------|
| 邻近协同 | `skink` ↔ `kroxigor` | 灵蜥获恐惧；巨蜥获武器威力 |
| 邻近协同 | `peasant` ↔ `knight` | 农民获领导力；骑士无视农民溃逃士气冲击 |
| 阴阳和谐 | `yin` / `yang` | 邻近异性属性触发震旦武道宁和 |
| 冲锋对抗 | `charge_defense*` vs `ogre_charge*` | 齐整抵消冲锋加成；食人魔冲锋仅部分损失 |
| 隐藏对抗 | `revealed` vs `hide_forest`/`stalk`/`unspottable` | 现身使隐蔽类失效 |
| 不稳定家族 | `undead`/`daemonic`/`construct`/`elemental`/`bound_fire_daemon` | 不溃逃 + 士气伤血 +（多数）心理免疫；细节见上 |
| 永不降属性 | `slayer` / `unyielding_assault` | 分别锁定武器威力 / 近战攻击不被降低 |

---

## 5. 完整 key 清单（84）

```
always_flying
armoured_vehicle
ballistic_plating
boar_cavalry
bound_fire_daemon
can_block_missiles_360
can_siege
cannot_die
cant_run
causes_fear
causes_terror
charge_defense
charge_defense_vs_large
charge_reflection
construct
contempt
daemonic
devastating_flanker
disciplined
elemental
encourages
executor
expendable
fanatic
fatigue_immune
fatigue_res
flanking_immune
flying
force_rally
formed_attack
glorious_charge
goblin_infantry
gorger
guerrilla_deploy
gunship
hellforged
hide_forest
ignore_imbue_contact_effects_ally
ignore_imbue_contact_effects_enemy
ignore_trees
immune_to_psychology
invulnerable
invulnerable_to_effects_ally
invulnerable_to_effects_enemy
knight
kroxigor
mark_khorne
mark_nurgle
mark_slaanesh
mark_tzeentch
melee_disabled
moulder_monster
mounted_fire_move
nasty_skulker
night_goblin_archer
ogre_charge
ogre_charge_upgraded
orc_infantry
peasant
rampage
randomises_spells_on_cast
revealed
shoot_disabled
silenced
skink
slayer
snipe
spell_mastery
spider
squig
squig_herd
stalk
strider
tiger_warrior
troll
unbreakable
undead
underground
unspottable
unyielding_assault
wallbreaker
yang
yin
```

---

## 6. 资料来源

| 文件 | 内容 |
|------|------|
| `源码/db/db/unit_attributes_tables/data__.tsv` | 全部合法 attribute key |
| `多语言/local_en/text/db/unit_attributes__.loc.tsv` | 英文本地化 bullet / imbued / removed |
| `多语言/local_cn/text/localisation__.loc.tsv` | 中文 `unit_attributes_bullet_text_*` |
| `多语言/local_en/text/db/ui_text_replacements__.loc.tsv` | `guerrilla_deployment`、`wh2_dlc09_undead_description` |
| `多语言/local_en/text/db/random_localisation_strings__.loc.tsv` | `can_siege_tooltip` |

数值类细节（恐惧半径、不稳定伤害公式、印记具体百分比等）多数由引擎硬编码或其它表提供，本表仅整理 **attribute 本身在 UI/规则层声明的效果**。
