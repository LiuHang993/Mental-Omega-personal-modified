# Phobos b48 引擎扩展参考手册

> 基于 Phobos CHM 说明书的综合结构化摘要。Build #48。所有参数均以 rulesmd.ini 为主，除非特别标注为 artmd.ini。

---

## AttachEffect 系统

Phobos 的杀手级功能。需先在 `[AttachEffectTypes]` 列表中注册 AE 类型。

### [AttachEffectTypes] -- AE 类型定义

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `Duration` | integer | 0 | AE持续时间(帧)。0=无限 |
| `Duration.ApplyFirepowerMult` | boolean | false | AE持续时间是否受施放者火力系数影响 |
| `Duration.ApplyArmorMultOnTarget` | boolean | false | AE持续时间是否受目标装甲系数影响 |
| `Cumulative` | boolean | false | 同名AE是否可以叠加刷新持续时间 |
| `Cumulative.MaxCount` | integer | -1 | 可叠加AE的最大叠加层数。-1=无限 |
| `Powered` | boolean | false | AE是否在目标断电/EMP/低电力时失效 |
| `DiscardOn` | enum: entry\|move\|stationary\|drain\|inrange\|outofrange\|firing\|none | none | AE在何种条件下自动移除 |
| `DiscardOn.RangeOverride` | float (距离) | - | 覆盖 inrange/outofrange 的距离判定值 |
| `PenetratesIronCurtain` | boolean | false | AE是否可以附加到铁幕单位上 |
| `PenetratesForceShield` | boolean | 继承自PenetratesIronCurtain | AE是否可以附加到力场护盾单位上 |
| `Animation` | list of Animation | - | AE激活时播放的动画 |
| `Animation.ResetOnReapply` | boolean | - | 非叠加AE每次刷新时是否重播动画 |
| `Animation.OfflineAction` | enum: None\|Hides\|Temporal\|Paused\|PausedTemporal | - | 断电时动画行为 |
| `Animation.TemporalAction` | enum: None\|Hides\|Temporal\|Paused\|PausedTemporal | - | 被超时空时动画行为 |
| `Animation.UseInvokerAsOwner` | boolean | false | 动画所属方为施放者还是目标 |
| `Animation.HideIfAttachedWith` | list of AttachEffectTypes | - | 若与这些AE共存则隐藏此动画 |
| `CumulativeAnimations` | list of Animation | - | 叠加层数对应的动画(每层依次播放) |
| `CumulativeAnimations.RestartOnChange` | boolean | true | 叠加层数变化时是否重播累积动画 |
| `ExpireWeapon` | WeaponType | - | AE结束时发射的武器 |
| `ExpireWeapon.TriggerOn` | enum: none\|expire\|remove\|death\|all | expire | 触发ExpireWeapon的条件 |
| `ExpireWeapon.CumulativeOnlyOnce` | boolean | - | 叠加AE在整个生效期间只发射一次ExpireWeapon |
| `ExpireWeapon.UseInvokerAsOwner` | boolean | false | ExpireWeapon使用施放者还是目标所属方 |
| `Tint.Color` | integer (R,G,B) | - | 目标单位染色颜色 |
| `Tint.Intensity` | float | - | 染色强度 |
| `Tint.VisibleToHouses` | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | - | 哪些玩家可以看到染色 |
| `FirepowerMultiplier` | float | 1.0 | 火力倍率 |
| `ArmorMultiplier` | float | 1.0 | 装甲倍率 |
| `ArmorMultiplier.AllowWarheads` | list of WarheadTypes | - | 装甲倍率仅对这些弹头生效 |
| `ArmorMultiplier.DisallowWarheads` | list of WarheadTypes | - | 装甲倍率对这些弹头不生效 |
| `SpeedMultiplier` | float | 1.0 | 速度倍率 |
| `ROFMultiplier` | float | 1.0 | 射速倍率 |
| `ROFMultiplier.ApplyOnCurrentTimer` | boolean | - | 射速倍率是否直接作用在当前攻击冷却上 |
| `Cloakable` | boolean | - | AE生效期间是否获得隐形 |
| `ForceDecloak` | boolean | - | AE生效期间是否强制显形 |
| `WeaponRange.Multiplier` | float | - | 武器射程倍率(乘法) |
| `WeaponRange.ExtraRange` | float | - | 武器射程增量(加法,在乘法后) |
| `WeaponRange.AllowWeapons` | list of WeaponTypes | - | 射程加成仅对这些武器生效 |
| `WeaponRange.DisallowWeapons` | list of WeaponTypes | - | 射程加成对这些武器不生效 |
| `Crit.Multiplier` | float | - | 暴击倍率(乘法) |
| `Crit.ExtraChance` | float | - | 暴击几率增量(加法) |
| `Crit.AllowWarheads` | list of WarheadTypes | - | 暴击加成仅对这些弹头生效 |
| `Crit.DisallowWarheads` | list of WarheadTypes | - | 暴击加成对这些弹头不生效 |
| `RevengeWeapon` | WeaponType | - | AE期间受到攻击时反击的武器 |
| `RevengeWeapon.AffectsHouses` | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | - | 反击对哪些玩家生效 |
| `RevengeWeapon.UseInvokerAsOwner` | boolean | false | 反击武器使用施放者还是目标所属方 |
| `ReflectDamage` | boolean | - | AE期间是否反弹伤害 |
| `ReflectDamage.Warhead` | WarheadType | [General] -> C4Warhead | 反弹伤害使用的弹头 |
| `ReflectDamage.Warhead.Detonate` | boolean | false | 反弹伤害弹头是否引爆(范围伤害)还是只造成伤害 |
| `ReflectDamage.Multiplier` | float | - | 反弹伤害倍率 |
| `ReflectDamage.AffectsHouses` | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | - | 反弹伤害对哪些玩家生效 |
| `ReflectDamage.Chance` | float | 1.0 | 反弹伤害触发几率 |
| `ReflectDamage.Override` | integer | - | 覆盖反弹伤害值为固定值 |
| `ReflectDamage.UseInvokerAsOwner` | boolean | false | 反弹伤害使用施放者还是目标所属方 |
| `DisableWeapons` | boolean | - | AE期间是否禁用所有武器 |
| `Unkillable` | boolean | - | AE期间目标血量降至1时不死亡 |
| `LaserTrail.Type` | lasertrail type | - | AE期间在目标上绘制的激光尾迹类型 |
| `Groups` | string | - | AE分组标签,可批量移除同组AE |

### [TechnoType] -- 在单位上附加 AE

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `AttachEffect.AttachTypes` | list of AttachEffectTypes | - | 该单位出生时自带的AE类型列表 |
| `AttachEffect.DurationOverrides` | list of integers | - | 覆盖对应AE的持续时间(需一一对应) |
| `AttachEffect.Delays` | integer (帧) | 0 | AE的初始延迟时间(需一一对应) |

---

## 护盾系统 (Shield System)

需先在 `[ShieldTypes]` 列表中注册护盾类型，然后在 `[TechnoType]` 的 `ShieldType=` 中引用。

### [ShieldType] -- 护盾类型参数

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `Strength` | integer | - | 护盾耐久值(HP) |
| `InitialStrength` | integer | - | 护盾初始耐久值(可小于Strength) |
| `ConditionYellow` | float (百分比) | 继承全局 | 护盾黄血阈值百分比(如0.5=50%) |
| `ConditionRed` | float (百分比) | 继承全局 | 护盾红血阈值百分比 |
| `Armor` | ArmorType | none | 护盾的装甲类型(独立弹头计算) |
| `InheritArmorFromTechno` | boolean | no | 护盾装甲是否继承自单位本身的Armor |
| `InheritArmor.Allowed` | list of TechnoTypes | - | 允许继承装甲的单位白名单 |
| `InheritArmor.Disallowed` | list of TechnoTypes | - | 不允许继承装甲的单位黑名单 |
| `Powered` | boolean | no | 护盾是否在断电/EMP时失效 |
| `AbsorbOverDamage` | boolean | no | 护盾破裂时是否吸收最后一击的溢出伤害 |
| `SelfHealing` | double (百分比) | 0.0 | 护盾每秒恢复量。0.01-1.0=百分比，>1=固定值 |
| `SelfHealing.Rate` | double (分钟) | 0.0 | 护盾恢复间隔(游戏内分钟) |
| `SelfHealing.RestartInCombat` | boolean | yes | 受到攻击后是否重新计时再恢复 |
| `SelfHealing.RestartInCombatDelay` | integer (帧) | 0 | 脱离战斗后延迟多久开始恢复 |
| `SelfHealing.EnabledBy` | list of BuildingTypes | - | 需要拥有这些建筑之一才能启用恢复 |
| `Respawn` | double (百分比) | 0.0 | 护盾重生恢复量 |
| `Respawn.Rate` | double (分钟) | 0.0 | 护盾重生间隔 |
| `Respawn.RestartInCombat` | boolean | yes | 受到攻击后是否重新计时重生 |
| `Respawn.RestartInCombatDelay` | integer (帧) | 0 | 脱离战斗后延迟多久开始重生 |
| `Respawn.Anim` | list of AnimationTypes | - | 护盾重生时播放的动画 |
| `Respawn.Weapon` | WeaponType | - | 护盾重生时发射的武器 |
| `BracketDelta` | integer | 0 | 护盾血条高度偏移(类似PixelSelectionBracketDelta) |
| `Pips` | integer | - | 护盾pip帧索引(微调用) |
| `Pips.Building` | integer | - | 建筑护盾pip帧索引 |
| `Pips.Background` | filename (含.pcx/.shp扩展名) | - | 护盾pip背景文件 |
| `Pips.Building.Empty` | integer | - | 建筑护盾空pip帧索引 |
| `Pips.HideIfNoStrength` | boolean | false | 护盾耐久为0时是否隐藏pip显示 |
| `IdleAnim` | animation ID | - | 护盾空闲时播放的动画 |
| `IdleAnim.ConditionYellow` | animation ID | - | 护盾黄血时空闲动画 |
| `IdleAnim.ConditionRed` | animation ID | - | 护盾红血时空闲动画 |
| `IdleAnimDamaged` | animation ID | - | 护盾在单位受损时的空闲动画 |
| `IdleAnimDamaged.ConditionYellow` | animation ID | - | 护盾黄血+单位受损时空闲动画 |
| `IdleAnimDamaged.ConditionRed` | animation ID | - | 护盾红血+单位受损时空闲动画 |
| `IdleAnim.OfflineAction` | enum: None\|Hides\|Temporal\|Paused\|PausedTemporal | Hides | 断电时空闲动画行为 |
| `IdleAnim.TemporalAction` | enum: None\|Hides\|Temporal\|Paused\|PausedTemporal | Hides | 超时空时空闲动画行为 |
| `BreakAnim` | animation ID | - | 护盾破裂时播放的动画 |
| `HitAnim` | animation ID | - | 护盾受击时播放的动画 |
| `HitFlash` | boolean | false | 护盾受击时是否闪烁 |
| `HitFlash.FixedSize` | integer (81-252) | - | 固定闪烁大小 |
| `HitFlash.Red` | boolean | true | 受击时是否闪烁红色 |
| `HitFlash.Green` | boolean | true | 受击时是否闪烁绿色 |
| `HitFlash.Blue` | boolean | true | 受击时是否闪烁蓝色 |
| `HitFlash.Black` | boolean | false | 受击时是否闪烁黑色(屏幕变暗) |
| `BreakWeapon` | Weapon | - | 护盾破裂时发射的武器 |
| `AbsorbPercent` | double (百分比) | 1.0 | 护盾吸收伤害的比例 |
| `PassPercent` | double (百分比) | 0.0 | 穿透护盾直接伤害单位的伤害比例 |
| `ReceivedDamage.Minimum` | integer | - | 护盾单次承受的最小伤害(在Armor和AbsorbPercent后) |
| `ReceivedDamage.Maximum` | integer | - | 护盾单次承受的最大伤害 |
| `AllowTransfer` | boolean | 由AttachTypes添加=yes, 自带=no | 单位变形/部署时是否传递护盾 |
| `ImmuneToBerserk` | boolean | - | 护盾是否免疫狂暴效果 |
| `ImmuneToCrit` | boolean | - | 护盾是否免疫暴击 |
| `Tint.Color` | integer (R,G,B) | - | 护盾激活时单位染色颜色 |
| `Tint.Intensity` | float | - | 护盾染色强度 |
| `Tint.VisibleToHouses` | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | - | 哪些玩家可以看到护盾染色 |

### [AudioVisual] -- 全局护盾 Pip 设置

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `Shield.ConditionYellow` | float (百分比) | 继承ConditionYellow | 全局护盾黄血阈值 |
| `Shield.ConditionRed` | float (百分比) | 继承ConditionRed | 全局护盾红血阈值 |
| `Pips.Shield` | integer | -1,-1,-1 (17帧) | 护盾pip帧(绿/黄/红) |
| `Pips.Shield.Building` | integer | -1,-1,-1 (6帧) | 建筑护盾pip帧 |
| `Pips.Shield.Background` | filename | pipbrd.shp | 护盾pip背景文件 |
| `Pips.Shield.Building.Empty` | integer | 1 | 建筑护盾空pip帧 |

### [Warhead] -- 弹头与护盾交互

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `Shield.Penetrate` | boolean | no | 弹头是否穿透护盾直接伤害本体 |
| `Shield.Break` | boolean | no | 弹头是否在伤害计算前先击碎护盾 |
| `Shield.BreakAnim` | animation ID | - | 覆盖护盾的BreakAnim |
| `Shield.HitAnim` | animation ID | - | 覆盖护盾的HitAnim |
| `Shield.BreakWeapon` | Weapon | - | 覆盖护盾的BreakWeapon |
| `Shield.AbsorbPercent` | double (百分比) | - | 覆盖护盾的AbsorbPercent |
| `Shield.PassPercent` | double (百分比) | - | 覆盖护盾的PassPercent |
| `Shield.ReceivedDamage.Minimum` | integer | - | 覆盖护盾的ReceivedDamage.Minimum |
| `Shield.ReceivedDamage.Maximum` | integer | - | 覆盖护盾的ReceivedDamage.Maximum |
| `Shield.ReceivedDamage.MinMultiplier` | float | 1.0 | 与Minimum相乘的系数 |
| `Shield.ReceivedDamage.MaxMultiplier` | float | 1.0 | 与Maximum相乘的系数 |
| `Shield.Respawn.Duration` | integer (帧) | 0 | 覆盖护盾重生参数的持续时间 |

### [TechnoType] -- 单位护盾引用

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `ShieldType` | ShieldType | - | 该单位使用的护盾类型 |

---

## 新增/增强武器系统

### 多武器系统 (MultiWeapon)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `MultiWeapon` | [TechnoType] | boolean | false | 是否启用多武器系统 |
| `MultiWeapon.IsSecondary` | [TechnoType] | list of integer | Weapon1为副武器 | 哪些武器槽为副武器模式 |
| `MultiWeapon.SelectCount` | [TechnoType] | list of integer | - | 主武器模式可选的武器槽数量 |

### 自动开火 (AutoFire)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `AutoFire` | [TechnoType] | boolean | no | 单位是否自动开火(不需要目标) |
| `AutoFire.TargetSelf` | [TechnoType] | boolean | no | 自动开火时是否以自身为目标 |

### 武器弹头增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `ROF.RandomDelay` | [Weapon] | integer | 0 | ROF随机延迟范围(帧) |
| `FireOnce` | [Weapon] | boolean | no | 该武器槽仅开火一次(适用于DeployFireWeapon) |
| `Burst.Delays` | [Weapon] | list of integers | - | 自定义每次Burst之间的间隔帧数 |
| `Burst.Count` | [Weapon] | integer | 1 | 超过2的Burst计数支持 |
| `AreaFire.Target` | [Weapon] | enum: base\|self\|random\|target\|cell | base | AreaFire时的目标定位方式 |
| `DelayedFire.Trigger` | [Weapon] | enum: none\|land\|impact\|detonate | - | 延迟开火的触发条件 |
| `DelayedFire.Delay` | [Weapon] | integer (帧) | - | 延迟开火的延迟时间 |
| `Crit.Chance` | [Weapon] | float | 0.0 | 暴击几率 |
| `Crit.Multiplier` | [Weapon] | float | 1.0 | 暴击伤害倍率 |
| `Crit.AllowWarheads` | [Weapon] | list of WarheadTypes | - | 暴击仅对这些弹头生效 |
| `Crit.DisallowWarheads` | [Weapon] | list of WarheadTypes | - | 暴击对这些弹头不生效 |
| `Crit.ExtraDamage` | [Weapon] | integer | 0 | 暴击附加固定伤害 |
| `Crit.Affects` | [Weapon] | enum: all\|infantry\|units\|buildings\|aircraft | all | 暴击仅对这类目标生效 |
| `Laser.Duration` | [Weapon] | integer | 15 | 激光持续时间(帧) |
| `Laser.InnerColor` | [Weapon] | (R,G,B) | - | 激光内层颜色 |
| `Laser.OuterColor` | [Weapon] | (R,G,B) | - | 激光外层颜色 |
| `Laser.OuterSpread` | [Weapon] | integer | - | 激光外层扩散距离 |
| `IsRadBeam` | [Weapon] | boolean | no | 是否是光束武器(连续伤害光束) |
| `IsRadBeam.Duration` | [Weapon] | integer | - | 光束武器持续时间 |
| `IsRadBeam.Amplitude` | [Weapon] | integer | - | 光束波动幅度 |
| `Wave.IsLaser` | [Weapon] | boolean | no | 是否是波动激光 |
| `Wave.Amplitude` | [Weapon] | integer | - | 波动幅度 |
| `Wave.Frequency` | [Weapon] | integer | - | 波动频率 |
| `Bolt.Color` | [Weapon] | (R,G,B) | - | 电弧颜色 |
| `Bolt.Duration` | [Weapon] | integer | - | 电弧持续时间 |
| `Bolt.Arcs` | [Weapon] | integer | - | 电弧分支数量 |
| `Beam.Color` | [Weapon] | (R,G,B) | - | 光束颜色 |
| `Beam.Duration` | [Weapon] | integer | - | 光束持续时间 |
| `Beam.Amplitude` | [Weapon] | integer | - | 光束波动幅度 |
| `Arc.Color` | [Weapon] | (R,G,B) | - | 弧线颜色 |
| `Arc.Duration` | [Weapon] | integer | - | 弧线持续时间 |

---

## 弹头增强 (Warhead Enhancements)

### 伤害与效果

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Crit.Chance` | [Warhead] | float | 0.0 | 弹头暴击几率(全局,可被AE加成) |
| `Crit.Multiplier` | [Warhead] | float | 1.0 | 弹头暴击倍率 |
| `ExtraDamage` | [Warhead] | integer | 0 | 弹头额外固定伤害 |
| `ExtraDamage.ForNumberOfTargets` | [Warhead] | integer | 0 | 额外伤害平分给目标数 |
| `ExtraDamage.DamageCalculationType` | [Warhead] | enum: normal\|armor\|verses\|all\|versesandarmor | - | 额外伤害的计算方式 |
| `DamageMultiplier` | [Warhead] | float | 1.0 | 伤害总倍率(在Verses后) |
| `ApplyModifiersOnNegativeDamage` | [Warhead] | boolean | - | 负伤害时是否应用Verses和PercentAtMax |
| `WallDamage` | [Warhead] | integer | 0 | 对围墙的伤害值 |

### 护盾交互 (见护盾系统章节)

### 弹头特殊效果

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Shroud.Reveal` | [Warhead] | boolean | no | 弹头爆炸时是否揭示黑幕 |
| `Shroud.Reveal.Radius` | [Warhead] | float (cells) | - | 揭示黑幕的半径 |
| `Shroud.Reveal.Duration` | [Warhead] | integer (帧) | - | 揭示黑幕的持续时间 |
| `Reveal` | [Warhead] | boolean | - | 是否揭示目标区域 |
| `CreateGap` | [Warhead] | boolean | - | 是否创建/恢复黑幕 |
| `TotallyFriendly` | [Warhead] | boolean | no | 是否完全不伤害友军 |
| `RemoveDisguise` | [Warhead] | boolean | no | 是否移除目标伪装 |
| `RemoveMines` | [Warhead] | boolean | no | 是否移除地雷 |
| `OreDamage` | [Warhead] | integer | 0 | 破坏矿石量 |
| `ElectricAssaultLevel` | [Warhead] | integer | 1 | 对建筑的过载充能等级 |

### 弹头特殊逻辑

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `LimboKill` | [Warhead] | boolean | no | 是否将目标置入Limbo而非杀死(InfDeath,可复活) |
| `LimboKill.Affects` | [Warhead] | enum: all\|infantry\|units\|buildings\|aircraft | all | LimboKill影响的目标类型 |
| `LimboKill.ID` | [Warhead] | integer | - | LimboKill的InfDeath ID |
| `MindControl` | [Warhead] | boolean | no | 是否触发心灵控制效果 |
| `MindControl.Duration` | [Warhead] | integer (帧) | - | 心灵控制持续时间 |
| `MindControl.Affects` | [Warhead] | enum: all\|infantry\|units\|buildings\|aircraft | - | 心灵控制影响的目标类型 |
| `AnimList.Random` | [Warhead] | boolean | true | AnimList动画是否随机播放 |
| `SplashList.Random` | [Warhead] | boolean | true | SplashList动画是否随机播放 |
| `NotHuman.Death` | [Warhead] | enum: kill\|skip\|ignore | - | 对NotHuman步兵的击杀方式 |
| `GenerateMutations` | [Warhead] | boolean | no | 是否触发基因突变 |
| `GenerateMutations.Affects` | [Warhead] | enum: all\|infantry\|units | - | 基因突变影响的目标类型 |
| `GenerateMutations.Animation` | [Warhead] | Animation | - | 基因突变时播放的动画 |
| `GenerateMutations.InfantryType` | [Warhead] | InfantryType | - | 基因突变生成的步兵类型 |
| `GenerateMutations.KillWhenLimit` | [Warhead] | boolean | yes | 超出突变上限时是否杀死目标 |
| `Suicide` | [Warhead] | boolean | no | 是否使目标自毁 |
| `Suicide.Damage` | [Warhead] | integer | - | 目标自毁伤害值 |
| `Suicide.Warhead` | [Warhead] | WarheadType | - | 目标自毁使用的弹头 |
| `Displace` | [Warhead] | boolean | no | 是否随机传送目标 |
| `Displace.Radius` | [Warhead] | integer | - | 传送后距离原位置的最大偏移(cells) |
| `Displace.Height` | [Warhead] | integer | - | 传送后高度偏移(leptons) |
| `Convert` | [Warhead] | boolean | no | 是否转换目标单位类型 |
| `Convert.From` | [Warhead] | list of TechnoTypes | - | 可被转换的源单位白名单 |
| `Convert.To` | [Warhead] | TechnoType | - | 转换成的目标单位类型 |
| `Convert.Affects` | [Warhead] | enum: all\|infantry\|units\|buildings\|aircraft | - | 转换影响的单位类型 |
| `Grant` | [Warhead] | boolean | no | 是否授予目标经验/升级 |
| `Grant.Exp` | [Warhead] | integer | - | 授予经验值 |
| `Grant.Veterancy` | [Warhead] | enum: none\|rookie\|veteran\|elite | - | 设定/升级老兵等级 |
| `RemoveShield` | [Warhead] | boolean | no | 是否移除目标的护盾 |
| `RemoveShield.Groups` | [Warhead] | list of strings | - | 按组移除护盾 |
| `TransactMoney` | [Warhead] | integer | - | 弹头命中时转移金钱(正=给予,负=扣除) |
| `TransactMoney.Display` | [Warhead] | boolean | false | 是否显示金钱转移提示 |
| `TransactMoney.Display.Offset` | [Warhead] | X,Y | 0,0 | 金钱提示偏移 |
| `TransactMoney.Display.Houses` | [Warhead] | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | - | 哪些玩家可看到提示 |
| `ApplyModifiersOnNegativeDamage` | [Warhead] | boolean | - | 负伤害时是否应用Verses和PercentAtMax |

---

## 抛射体增强 (Projectile Enhancements)

### 自定义抛射体

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Inviso.Radius` | [Projectile] | integer | - | Inviso抛射体的碰撞检测半径 |
| `Inviso.Radius.Override` | [Projectile] | integer | - | 强制覆盖碰撞半径 |
| `Shrapnel.Affects` | [Projectile] | enum: all\|infantry\|units\|buildings\|aircraft | all | Shrapnel影响的目标类型 |
| `Shrapnel.AffectsHouses` | [Projectile] | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | - | Shrapnel影响哪些阵营 |
| `Shrapnel.UseWeaponTargeting` | [Projectile] | boolean | no | Shrapnel是否使用武器系统的目标筛选 |
| `Shrapnel.UseMuzzleFlash` | [Projectile] | boolean | no | Shrapnel是否从MuzzleFlash位置发射 |
| `Cluster.Count` | [Projectile] | integer | - | Cluster抛射体散射数量 |
| `Cluster.Distance` | [Projectile] | integer | - | Cluster散射距离 |
| `Cluster.Angle` | [Projectile] | integer | - | Cluster散射角度范围 |
| `Cluster.Weapon` | [Projectile] | WeaponType | - | Cluster使用的武器(覆盖原设定) |
| `Airburst` | [Projectile] | boolean | - | 是否启用在空中爆炸 |
| `Airburst.Distance` | [Projectile] | integer | - | 空炸距离(leptons) |
| `Airburst.Weapon` | [Projectile] | WeaponType | - | 空炸使用的武器 |
| `Airburst.Spread` | [Projectile] | integer | - | 空炸散布范围 |
| `Airburst.Height` | [Projectile] | integer | - | 空炸高度 |

### 抛射体弹道

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Trajectory` | [Projectile] | enum: Straight\|Arcing\|Parabolic\|Guided | - | 自定义抛射体弹道类型 |
| `Trajectory.Straight.DetonationDistance` | [Projectile] | float | - | 直线弹道引爆距离 |
| `Trajectory.Arcing.Angle` | [Projectile] | float | 45.0 | 弧形弹道角度 |
| `Trajectory.Arcing.AllowElevationInaccuracy` | [Projectile] | boolean | true | 弧形弹道是否受高度差精度影响 |
| `Trajectory.Parabolic.OpenAngle` | [Projectile] | float | 45.0 | 抛物线弹道开放角度 |
| `Trajectory.Parabolic.TargetSnapDistance` | [Projectile] | float | 0.5 | 抛物线弹道目标吸附距离 |
| `SubjectToLand` | [Projectile] | boolean | - | 抛射体是否会被陆地阻挡 |
| `SubjectToWater` | [Projectile] | boolean | - | 抛射体是否会被水面阻挡 |
| `SubjectToCliffs` | [Projectile] | boolean | - | 抛射体是否会被悬崖阻挡 |
| `SubjectToElevation` | [Projectile] | boolean | - | 抛射体是否受高度影响 |
| `SubjectToWalls` | [Projectile] | boolean | - | 抛射体是否会被围墙阻挡 |
| `Splits` | [Projectile] | boolean | - | 抛射体是否分裂 |
| `Splits.Count` | [Projectile] | integer | 2 | 分裂的子抛射体数量 |
| `Splits.Weapon` | [Projectile] | WeaponType | - | 子抛射体使用的武器 |
| `ReturnWeapon` | [Projectile] | WeaponType | - | 抛射体返回时使用的武器(如回旋镖) |
| `BombClass` | [Projectile] | boolean | - | 是否使用轰炸弹道 |
| `BombClass.Range` | [Projectile] | integer | - | 轰炸范围 |
| `BombClass.AttachToSource` | [Projectile] | boolean | - | 炸弹是否跟随源单位 |
| `BombClass.DetonationDistance` | [Projectile] | integer | - | 轰炸引爆距离 |

---

## 新增超武类型 (SuperWeapon Types)

Phobos 新增了大量超武类型，可通过 `Type=` 指定。

### 新增超武类型列表

| Type值 | 说明 |
|---|---|
| `GenericWarhead` | 通用弹头超武——在目标点引爆一个Warhead |
| `UnitDelivery` | 单位投送超武——在目标地点创建单位 |
| `LightningStorm` | 闪电风暴超武——自定义闪电风暴效果 |
| `DetonateWeapon` | 引爆武器超武——引爆目标位置预设的弹头 |
| `Firestorm` | 火风暴超武——创建/移除火风暴效果 |
| `DropPod` | 空降仓超武——空降步兵到目标位置 |
| `EMP` | EMP超武——电磁脉冲效果 |
| `Reveal` | 地图揭示超武——揭示地图区域 |
| `HunterSeeker` | 猎杀无人机超武——发射猎人无人机 |
| `PsychicReveal` | 心灵揭示超武——揭示区域并显示敌方单位 |
| `SpyPlane` | 侦察机超武——派出侦察机 |
| `Chronosphere` | 超时空超武——传送单位 |
| `GeneticMutator` | 基因突变超武——突变步兵 |
| `IronCurtain` | 铁幕超武——无敌效果 |
| `ForceShield` | 力场护盾超武——护盾无敌效果 |
| `Nuke` | 核弹超武 |

### [SuperWeapon] 通用增强参数

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `Type` | enum | - | 超武类型(上述列表之一) |
| `ShowTimer.Priority` | integer | 0 | 超武计时器显示优先级 |
| `ShowTimer.SortValue` | integer | 0 | 超武计时器排序值 |
| `ShowTimer` | enum: none\|ready\|charging\|all | - | 超武计时器显示方式 |
| `SW.InitialReady` | boolean | no | 超武是否开局即就绪 |
| `SW.RequiresTarget` | enum: none\|land\|water\|any\|empty | any | 超武需要的目标类型 |
| `SW.AITargeting` | enum: none\|nuke\|lightningstorm\|... | - | AI使用此超武时的目标选择方式 |
| `SW.AIFireDelay` | integer (帧) | 0 | AI触发超武后的延迟开火时间 |
| `SW.Range` | float (cells) | - | 超武使用范围 |
| `SW.Cursor` | CursorType | - | 超武光标 |
| `SW.Cursor.Visible` | boolean | yes | 超武光标是否可见 |
| `SW.NoCursor` | enum: none\|land\|water\|all | none | 禁用光标的区域 |
| `SW.NoEVA` | boolean | no | 是否不播放EVA通知 |
| `SW.NoEVA.Detected` | boolean | no | 敌方检测到此超武时是否不播放EVA |
| `SW.NoEVA.Ready` | boolean | no | 超武就绪时是否不播放EVA |
| `SW.NoEVA.Activated` | boolean | no | 超武激活时是否不播放EVA |
| `EVA.Detected` | EVA entry | - | 自定义检测EVA |
| `EVA.Ready` | EVA entry | - | 自定义就绪EVA |
| `EVA.Activated` | EVA entry | - | 自定义激活EVA |
| `Message.Detected` | CSF entry | - | 自定义检测文字提示 |
| `Message.Ready` | CSF entry | - | 自定义就绪文字提示 |
| `Message.Activated` | CSF entry | - | 自定义激活文字提示 |
| `SW.Deferment` | integer (帧) | 0 | 超武激活后的延迟执行时间 |
| `SW.Damage` | integer | -1 | 超武对目标的伤害值(-1=使用弹头Damage) |
| `SW.Warhead` | WarheadType | - | 超武使用的弹头 |
| `SW.Animation` | Animation | - | 超武播放的动画 |
| `SW.Animation.Visibility` | enum: none\|owner\|allies\|team\|enemies\|all | - | 超武动画对哪些玩家可见 |
| `SW.Sound` | Sound | - | 超武音效 |
| `SW.ActivationSound` | Sound | - | 激活瞬间音效 |
| `SW.SidebarPCX` | filename | - | 自定义超武侧栏图标 |
| `SW.AlwaysGranted` | boolean | no | 超武是否总是可用(不受建筑限制) |
| `SW.Shots` | integer | -1 | 超武可用次数(-1=无限) |
| `SW.AllowPlayer` | boolean | yes | 是否允许人类玩家使用 |
| `SW.AllowAI` | boolean | yes | 是否允许AI使用 |
| `SW.AutoFire` | boolean | no | 超武是否自动释放 |
| `SW.ManualFire` | boolean | yes | 超武是否可手动释放 |
| `SW.ResetTimer` | boolean | no | 超武释放后是否重置计时器 |
| `SW.CreateUnit` | TechnoType | - | 超武释放时创建的单位 |
| `SW.CreateUnit.Facing` | integer (0-255) | - | 创建单位的朝向 |
| `SW.CreateUnit.RandomFacing` | boolean | yes | 创建单位是否随机朝向 |
| `SW.CreateUnit.Mission` | Mission | - | 创建单位的初始任务 |
| `Detonate.Warhead` | WarheadType | - | DetonateWeapon类型使用的弹头 |
| `Detonate.Weapon` | WeaponType | - | DetonateWeapon类型使用的武器(可选) |

---

## 科技类型增强 (TechnoType Enhancements)

### 通用 [TechnoType] 参数

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `Insignia` | filename | - | 单位徽章图标SHP文件 |
| `Insignia.Rookie/Veteran/Elite` | filename | - | 不同等级的徽章图标 |
| `InsigniaFrame` | integer | -1 | 徽章帧索引 |
| `InsigniaFrames` | three integers | -1,-1,-1 | 快捷设置菜鸟/老兵/精英徽章帧 |
| `Insignia.WeaponN` | filename | - | 使用第N个武器时的徽章 |
| `Insignia.PassengersN` | filename | - | 载有N个乘客时的徽章 |
| `Insignia.ShowEnemy` | boolean | 继承全局 | 是否对敌人显示徽章 |
| `InsigniaType` | InsigniaType | - | 引用的徽章模板 |
| `Image` | filename (不含扩展名) | - | artmd.ini中自定义模型文件(SHP/VXL) |
| `SelfHealGainType` | enum: noheal\|infantry\|units | 步兵=infantry,载具=units,建筑=noheal | 从建筑升级获得的自愈类型 |
| `AutoFire` | boolean | no | 单位是否自动开火(不需要目标) |
| `AutoFire.TargetSelf` | boolean | no | 自动开火目标是否为自身 |
| `KeepTargetOnMove` | boolean | false | 载具移动时是否保持攻击目标 |
| `KeepTargetOnMove.NoMorePursuit` | boolean | true | 移动到目的地后是否停止追击 |
| `KeepTargetOnMove.ExtraDistance` | float (cells) | 0 | 超出武器范围额外允许的追踪距离 |
| `NoTurret.TrackTarget` | boolean | 继承全局 | 无炮塔载具在攻击冷却时是否转向目标 |
| `FallingDownDamage` | integer/percentage | - | 坠地伤害(绝对值或百分比) |
| `FallingDownDamage.Water` | integer/percentage | 继承FallingDownDamage | 坠入水中伤害 |
| `DamagedSpeed` | float | 继承全局0.75 | 受伤时(黄血)速度倍率 |
| `Explodes.KillPassengers` | boolean | true | 爆炸死亡时是否杀死乘员 |
| `Wake` | Animation | 继承全局 | 水上移动尾迹动画 |
| `Wake.Grapple` | Animation | 继承全局 | 被拖拽时水花动画 |
| `Wake.Sinking` | Animation | 继承全局 | 沉没时水花动画 |
| `WarpOut/WarpIn` | animation ID | 继承全局 | 超时空传送进出动画 |
| `Chronoshift.WarpOut/Chronoshift.WarpIn` | animation ID | - | 超时空超武专用动画 |
| `ChronoTrigger` | boolean | - | 超时空冷却是否随距离变化 |
| `ChronoDistanceFactor` | integer | - | 超时空距离系数 |
| `ChronoMinimumDelay` | integer | - | 超时空最小冷却 |
| `ChronoRangeMinimum` | integer | - | 超时空计算冷却的最短距离 |
| `IronCurtain.Effect` | enum: kill\|invulnerable\|ignore | kill | 铁幕对有机体的效果 |
| `IronCurtain.KillWarhead` | warheadtype | C4Warhead | 铁幕杀死有机体使用的弹头 |
| `ForceShield.Effect` | enum: kill\|invulnerable\|ignore | kill | 力场护盾对有机体的效果 |
| `ForceShield.KillWarhead` | warheadtype | C4Warhead | 力场护盾杀死有机体使用的弹头 |
| `TargetZoneScanType` | enum: same\|any\|inrange | same | 小队脚本目标类型0的瞄准方式 |
| `Power` | integer | 0 | 单位电力产出/消耗(正=产出,负=消耗) |
| `RadarInvisibleToHouse` | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | enemies | 雷达上对哪些玩家不可见 |
| `SubterraneanHeight` | integer (leptons) | 继承全局-256 | 潜地单位高度 |
| `SubterraneanSpeed` | float | 继承全局7.5 | 潜地单位水平速度 |
| `OreGathering.Anims` | list of Animation | - | 自定义采矿动画(每种矿石对应一个) |
| `OreGathering.FramesPerDir` | list of integers | 15 | 每个采矿动画每方向帧数 |
| `OreGathering.Tiberiums` | list of integers | 0 | 采矿动画对应的矿石类型ID |

### 语音增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `VoicePickup` | [AircraftTypes] | Sound | - | Carryall=yes飞机的拾取语音 |
| `Voice[Primary/Secondary]EliteWeaponAttack` | [TechnoType] | Sound | - | 精英武器攻击语音 |
| `Voice[Primary/Secondary]WeaponAttack` | [TechnoType] | Sound | - | 武器攻击语音 |
| `VoiceEliteWeaponNAttack` | [TechnoType] | Sound | - | 精英第N武器攻击语音 |

---

## 建筑增强 (Building Enhancements)

### 建筑通用参数

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `PowersUpToLevel` | [BuildingType] | integer | -1 | 累计升级等级(-1需配合PowerUpNAnim) |
| `PowerUpNDamagedAnim` | [BuildingType] (artmd.ini) | animation | - | 建筑受损+N级动画 |
| `PowerUpNAnim` | [BuildingType] (artmd.ini) | animation | - | 建筑N级升级动画 |
| `AircraftDockingDir` | [BuildingType] | integer (0-255) | [AudioVisual]PoseDir | 飞机停靠方向(全局) |
| `AircraftDockingDir(N)` | [BuildingType] | integer (0-255) | 继承AircraftDockingDir | 第N个停靠位方向 |
| `BarracksExitCell` | [BuildingTypes] | (X,Y) | 按阵营变化 | 兵营出口格坐标 |
| `ConsideredVehicle` | [BuildingType] | boolean | 根据UndeploysInto和Foundation自动判定 | 建筑是否被视为载具 |
| `SellBuildupLength` | [BuildingType] | integer (帧) | 23 | 出售/收起时播放建造动画的长度 |
| `DisableDamageSound` | [BuildingType] | boolean | false | 是否禁用受伤音效 |
| `ExcludeFromMultipleFactoryBonus` | [BuildingType] | boolean | false | 是否不享受多工厂建造加速 |
| `IsAnimDelayedBurst` | [BuildingType] (artmd.ini) | boolean | true | 建筑攻击前是否播放蓄力动画(RA2风格) |
| `ZShapePointMove.OnBuildup` | [BuildingType] (artmd.ini) | boolean | false | 建造动画期间是否应用ZShapePointMove |
| `Refinery.UseNormalActiveAnim` | [BuildingType] (artmd.ini) | boolean | false | 精炼厂是否允许多个ActiveAnim同时播放 |
| `Explodes.DuringBuildup` | [BuildingType] | boolean | true | 建造期间被摧毁是否爆炸 |

### 电站增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `PowerPlant.DamageFactor` | [BuildingType] | float | 1.0 | 受伤时电力衰减系数。0=不受伤害影响 |

### 维修厂增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Units.RepairRate` | [BuildingTypes] | float (分钟) | 继承全局ReloadRate/URepairRate | 维修速度 |
| `Units.RepairStep` | [BuildingTypes] | integer | 继承全局RepairStep | 每次维修恢复HP |
| `Units.RepairPercent` | [BuildingTypes] | float (百分比) | 继承全局RepairPercent | 维修费用倍率 |
| `Units.UseRepairCost` | [BuildingTypes] | boolean | - | 是否收取维修费用 |

### 工厂增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `FactoryPlant.AllowTypes` | [BuildingType] | list of TechnoTypes | (全部) | 工厂折扣仅对这些单位生效 |
| `FactoryPlant.DisallowTypes` | [BuildingType] | list of TechnoTypes | (无) | 工厂折扣对这些单位不生效 |

### 磨碎机增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Grinding.AllowAllies` | [BuildingType] | boolean | no | 是否允许友军进入 |
| `Grinding.AllowOwner` | [BuildingType] | boolean | yes | 是否允许己方单位进入 |
| `Grinding.AllowTypes` | [BuildingType] | list | - | 可被吸收的单位白名单 |
| `Grinding.DisallowTypes` | [BuildingType] | list | - | 不可被吸收的单位黑名单 |
| `Grinding.PlayDieSound` | [BuildingType] | boolean | yes | 是否播放死亡音效 |
| `Grinding.Sound` | [BuildingType] | Sound | EnterGrinderSound | 磨碎时音效 |
| `Grinding.Weapon` | [BuildingType] | Weapon | - | 磨碎时发射的武器 |
| `Grinding.Weapon.RequiredCredits` | [BuildingType] | integer | 0 | 触发研磨武器所需的累积资金阈值 |

### 建筑进驻/碉堡增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `OccupyDamageMultiplier` | [BuildingType] | float | 继承CombatDamage全局值 | 驻军伤害倍率 |
| `OccupyROFMultiplier` | [BuildingType] | float | 继承CombatDamage全局值 | 驻军射速倍率 |
| `BunkerDamageMultiplier` | [BuildingType] | float | 继承全局 | 坦克碉堡内伤害倍率 |
| `BunkerROFMultMultiplier` | [BuildingType] | float | 继承全局 | 坦克碉堡内射速倍率 |
| `BunkerWallsUpSound` | [BuildingType] | Sound | 继承全局 | 碉堡升起音效 |
| `BunkerWallsDownSound` | [BuildingType] | Sound | 继承全局 | 碉堡降下音效 |

### 电力超载系统

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Overpower.KeepOnline` | [BuildingType] | integer | 2 | 低电力时能承受的过载等级数 |
| `Overpower.ChargeWeapon` | [BuildingType] | integer | 1 | 累积过载时切换到的武器槽 |

---

## 载具增强 (Vehicle Enhancements)

### 部署相关

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `DeployDir` | [VehicleType] | integer (0-7或-1) | 继承[AudioVisual]DeployDir | 部署朝向,-1=全方向 |
| `DeployingAnims` | [VehicleType] | list of Animations | - | 各方向部署动画列表 |
| `DeployingAnim.KeepUnitVisible` | [VehicleType] | boolean | no | 部署动画期间是否保持单位可见 |
| `DeployingAnim.ReverseForUndeploy` | [VehicleType] | boolean | yes | 收起时是否反向播放部署动画 |
| `DeployingAnim.UseUnitDrawer` | [VehicleType] | boolean | yes | 部署动画是否使用单位调色板 |
| `Ammo.AddOnDeploy` | [VehicleType] | integer | 0 | IsSimpleDeployer部署时增加的弹药 |
| `DeployToFire` | [VehicleType] | boolean | no | 部署后开火 |

### 碾压增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `TiltsWhenCrushes.Vehicles` | [VehicleType] | boolean | - | 碾压载具时是否倾斜 |
| `TiltsWhenCrushes.Overlays` | [VehicleType] | boolean | - | 碾压覆盖物时是否倾斜 |
| `CrushForwardTiltPerFrame` | [VehicleType] | float | -0.02(载具)/-0.05(覆盖物) | 每帧前倾角度 |
| `CrushOverlayExtraForwardTilt` | [VehicleType] | float | 0.02 | 碾压覆盖物额外前倾 |
| `CrushSlowdownMultiplier` | [VehicleType] | float | 0.2 | 碾压时速度减速倍率 |
| `SkipCrushSlowdown` | [VehicleType] | boolean | false | 是否跳过碾压减速 |

### 炮塔/炮管后座

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `TurretRecoil` | [VehicleTypes] | boolean | false | 是否有炮塔后座动画 |
| `TurretTravel` | [VehicleTypes] | integer (pixels) | 2 | 炮塔后座距离 |
| `TurretCompressFrames` | [VehicleTypes] | integer (帧) | 1 | 炮塔压缩帧数 |
| `TurretHoldFrames` | [VehicleTypes] | integer (帧) | 1 | 炮塔保持帧数 |
| `TurretRecoverFrames` | [VehicleTypes] | integer (帧) | 1 | 炮塔恢复帧数 |
| `BarrelTravel/CompressFrames/HoldFrames/RecoverFrames` | [VehicleTypes] | integer | 2/1/1/1 | 炮管后座参数(同炮塔) |
| `TurretRecoil.Suppress` | [Weapon] | boolean | false | 该武器是否抑制炮塔后座 |

### 沉没增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Sinkable` | [VehicleType] | boolean | 根据目标类型判定 | 是否可沉没 |
| `SinkSpeed` | [VehicleType] | integer (leptons/帧) | 5 | 沉没速度 |
| `Sinkable.SquidGrab` | [VehicleType] | boolean | true | 被乌贼抓住时是否沉没 |

### 采矿车增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Harvester.CanGuardArea` | [VehicleTypes] | boolean | false | 采矿车是否可警戒区域 |
| `HarvesterDumpAmount` | [VehicleTypes] | float | 0 | 每次卸矿量上限(0=无限制) |
| `HarvesterScanAfterUnload` | [VehicleTypes] | boolean | false | 卸矿后在精炼厂附近寻矿而非返回原位 |
| `Storage.TiberiumIndex` | [General] | integer | -1 | 精炼厂存储的矿石类型ID |

---

## 步兵增强 (Infantry Enhancements)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `InfantryAutoDeploy` | [General]/[InfantryType] | boolean | false | 步兵遇敌是否自动部署 |
| `ProneSpeed` | [InfantryType] | float | 继承全局 | 匍匐移动速度 |
| `ProneSpeed.Crawls` | [General] | float | 0.67 | 有爬行帧的步兵匍匐速度 |
| `ProneSpeed.NoCrawls` | [General] | float | 1.5 | 无爬行帧的步兵匍匐速度 |
| `NotHuman` | [InfantryType] | boolean | no | 是否不是人类(影响部分死亡逻辑) |

---

## 飞机增强 (Aircraft Enhancements)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `ExtendedAircraftMissions` | [General] | boolean | false | 是否启用扩展飞机任务(航点/巡逻/水上着陆) |
| `ExtendedAircraftMissions.SmoothMoving` | [AircraftTypes] | boolean | 继承全局 | 接近着陆点时是否直接返回 |
| `ExtendedAircraftMissions.EarlyDescend` | [AircraftTypes] | boolean | 继承全局 | 是否提前下降高度 |
| `ExtendedAircraftMissions.RearApproach` | [AircraftTypes] | boolean | 继承全局 | 是否从着陆方向的反方向进场 |
| `SpawnDistanceFromTarget` | [AircraftType] | double | 地图边界 | 生成时距离目标的距离 |
| `SpawnHeight` | [AircraftType] | integer | FlightLevel | 生成高度(leptons) |
| `LandingDir` | [AircraftTypes] | integer (0-255) | PoseDir | 着陆方向 |
| `FiringForceScatter` | [AircraftTypes] | boolean | true | 被摧毁时是否强制散开周围单位 |
| `VoicePickup` | [AircraftTypes] | Sound | - | Carryall=yes飞机拾取单位时的语音 |

---

## Jumpjet 增强

### [JumpjetControls] 全局默认值

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `TurnRate` | integer | 原版值 | 全局Jumpjet转向速率 |
| `Speed` | integer | 原版值 | 全局Jumpjet速度 |
| `Climb` | float | 原版值 | 全局Jumpjet爬升率 |
| `CruiseHeight` | integer | 原版值 | 全局Jumpjet巡航高度 |
| `Acceleration` | float | 原版值 | 全局Jumpjet加速度 |
| `WobblesPerSecond` | float | 原版值 | 全局Jumpjet摆动频率 |
| `WobbleDeviation` | integer | 原版值 | 全局Jumpjet摆动幅度 |
| `Crash` | float | 5.0 | 全局Jumpjet坠毁速度(新增) |
| `NoWobbles` | boolean | false | 全局Jumpjet是否无摆动(新增) |
| `AllowLayerDeviation` | boolean | yes | 是否根据高度切换绘制层 |
| `TurnToTarget` | boolean | no | 是否转向目标再开火 |

### [General] Jumpjet 参数

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `JumpjetClimbPredictHeight` | boolean | false | 是否提前5格预判爬升 |
| `JumpjetClimbWithoutCutOut` | boolean | false | 爬升时是否不停止水平移动 |

### [TechnoType] Jumpjet 参数

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `JumpjetAllowLayerDeviation` | boolean | 继承全局 | 每单位图层偏差设置 |
| `JumpjetFacingTarget` | boolean | 继承全局 | 每单位是否转向目标 |
| `JumpjetRotateOnCrash` | boolean | true | 坠毁时是否旋转 |

---

## AI 增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `AIAutoDeployMCV` | [AI] | boolean | true | AI是否自动部署MCV |
| `AISetBaseCenter` | [AI] | boolean | true | AI是否将新建的MCV设为中心基地 |
| `AIBiasSpawnCell` | [AI] | boolean | false | AI是否偏向出生点最近的MCV(战役) |
| `AIForbidConYard` | [AI] | boolean | false | 是否禁止AI使用ConstructionYard=true的建筑 |
| `AINodeWallsOnly` | [AI] | boolean | false | 是否只在ProtectWithWall=yes的建筑周围造墙 |
| `AICleanWallNode` | [AI] | boolean | false | 建筑被摧毁后是否停止造墙 |
| `AIFireSale` | [General] | boolean | true | AI是否执行大甩卖 |
| `AIFireSaleDelay` | [General] | integer | - | 大甩卖延迟 |
| `AIAllToHunt` | [General] | boolean | - | AI是否全体出击 |
| `GatherWhenMCVDeploy` | [General] | integer | - | MCV部署时是否聚兵 |
| `EnablePowerSurplus` | [AI] | boolean | true | 启用AI电力盈余逻辑 |
| `ForbidParallelAIQueues.Infantry/Vehicle/Navy/Aircraft/Building` | [GlobalControls] | boolean | no | 禁止AI并行克隆各类单位 |
| `ForbidParallelAIQueues` | [TechnoType] | boolean | false | 禁止AI并行克隆此单位 |

---

## 地形/矿石/围墙增强

### 地形

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `IsAnimated` | [TerrainType] | boolean | - | 地形是否播放动画 |
| `AnimationRate` | [TerrainType] | integer | - | 动画速率 |
| `AnimationLength` | [TerrainType] | integer | 总帧数一半 | 动画帧长度 |
| `HasDamagedFrames` | [TerrainType] | boolean | - | 是否有受损帧 |
| `HasCrumblingFrames` | [TerrainType] | boolean | - | 是否有崩塌帧 |
| `CrumblingSound` | [TerrainType] | Sound | - | 崩塌音效 |
| `DestroyAnim` | [TerrainType] | Animation | - | 被摧毁动画 |
| `DestroySound` | [TerrainType] | Sound | - | 被摧毁音效 |
| `IsPassable` | [TerrainType] | boolean | no | 单位是否可通过 |
| `CanBeBuiltOn` | [TerrainType] | boolean | no | 是否可以在地形上建造(自动清除地形) |
| `MinimapColor` | [TerrainType] | (R,G,B) | - | 小地图颜色 |
| `Palette` | [TerrainType] (artmd.ini) | filename | - | 自定义调色板 |
| `ConditionYellow.Terrain` | [AudioVisual] | float | - | 地形受损帧显示阈值 |

### 矿石

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `SpawnsTiberium.Type` | [TerrainType] | integer | 0 | 矿石类型ID |
| `SpawnsTiberium.Range` | [TerrainType] | integer | 1 | 矿石生成范围 |
| `SpawnsTiberium.GrowthStage` | [TerrainType] | integer | 3 | 矿石生长阶段 |
| `SpawnsTiberium.CellsPerAnim` | [TerrainType] | integer | 1 | 每次动画生成的矿石格子数 |
| `HideIfNoOre.Threshold` | [Animation] (artmd.ini) | integer | 0 | 矿石生长阶段不够时隐藏动画的阈值 |

### 围墙

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `AdjacentWallDamage` | [CombatDamage] | integer | 200 | 伤害围墙时相邻墙段的溅射伤害 |

---

## 铁幕/力场护盾增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `IronCurtain.EffectOnOrganics` | [CombatDamage] | enum: kill\|invulnerable\|ignore | kill | 铁幕对有机单位效果 |
| `KillOrganicsWarhead` | [CombatDamage] | warheadtype | C4Warhead | 铁幕杀死有机单位弹头 |
| `ForceShield.EffectOnOrganics` | [CombatDamage] | enum: kill\|invulnerable\|ignore | kill | 力场护盾对有机单位效果 |
| `ForceShield.KillOrganicsWarhead` | [CombatDamage] | warheadtype | C4Warhead | 力场护盾杀死有机单位弹头 |
| `IronCurtain.ExtraTintIntensity` | [AudioVisual] | float | 0.0 | 铁幕染色强度加成 |
| `ForceShield.ExtraTintIntensity` | [AudioVisual] | float | 0.0 | 力场护盾染色强度加成 |
| `IronCurtain.KeptOnDeploy` | [CombatDamage]/[TechnoType] | boolean | yes | 部署/收起后是否保留铁幕效果 |
| `ForceShield.KeptOnDeploy` | [CombatDamage]/[TechnoType] | boolean | yes | 部署/收起后是否保留力场护盾效果 |

---

## 超时空增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `ChronoSparkleDisplayDelay` | [General] | integer (帧) | 24 | 超时空闪光延迟 |
| `ChronoSparkleBuildingDisplayPositions` | [General] | enum: building\|occupants\|occupantslots\|all | occupantslots | 建筑超时空闪光显示位置 |
| `ChronoSphereDelay` | [General]/[TechnoType] | integer (帧) | 60 | 超时空传送后硬直时间 |
| `ChronoSpherePreDelay` | [General]/[TechnoType] | integer (帧) | 0 | 超时空传送前硬直时间 |

---

## 动画系统增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Damage.DealtByInvoker` | [Animation] | boolean | - | 动画伤害是否由调用者造成 |
| `CreateUnit` | [Animation] | TechnoType | - | 动画创建的单位 |
| `CreateUnit.Mission` | [Animation] | Mission | - | 创建单位的初始任务 |
| `CreateUnit.Facing` | [Animation] | integer (0-255) | - | 创建单位的朝向 |
| `CreateUnit.RandomFacing` | [Animation] | boolean | yes | 是否随机朝向 |
| `CreateUnit.SpawnAnim` | [Animation] | Animation | - | 创建单位时播放的生成动画 |
| `CreateUnit.Owner` | [Animation] | enum: invoker\|neutral\|random\|specific | - | 创建单位的所有者 |
| `CreateUnit.SpawnDelay` | [Animation] | integer (帧) | 0 | 创建延迟 |
| `CreateUnit.ConsiderPathfinding` | [Animation] | boolean | yes | 是否考虑寻路(防止生成在障碍物上) |
| `VisibleTo` | [Animation] | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | - | 动画对哪些玩家可见 |
| `AltPalette.ApplyLighting` | [Animation] | boolean | - | AltPalette动画是否应用光照 |
| `AnimRemapDefaultColorScheme` | [AudioVisual] | integer | [Colors]第一色 | AltPalette使用的默认颜色方案 |
| `UseNormalLight` | [Animation] | boolean | - | MakeInfantry动画是否使用单位光照 |

---

## 粒子系统增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `AdjustTargetCoordsOnRotation` | [ParticleSystemType] | boolean | - | BehavesLike=Fire粒子系统是否随旋转改变目标坐标 |
| `Gas.MaxDriftSpeed` | [ParticleSystemType] | integer | 2 | BehavesLike=Gas粒子系统最大漂移速度 |

---

## 电力和基础建设增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `UnitPowerDrain` | [General] | boolean | false | 启用单位电力消耗/产出系统 |
| `Power` | [TechnoType] | integer | 0 | 单位电力(正=产出,负=消耗) |
| `MCVRedeploys` | [MultiplayerDialogSettings] | boolean | - | MCV在多人游戏中是否可重新部署 |

---

## 游戏 UI/体验增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `ArtImageSwap` | [General] | boolean | no | 全局启用Image交换功能 |
| `EnemyInsignia` | [General] | boolean | true | 是否对敌人显示徽章 |
| `DrawInsignia.OnlyOnSelected` | [AudioVisual] | boolean | false | 是否仅选中时显示徽章 |
| `DrawInsignia.UsePixelSelectionBracketDelta` | [AudioVisual] | boolean | - | 徽章是否使用PixelSelectionBracketDelta |
| `DrawInsignia.AdjustPos.Infantry/Units/Buildings` | [AudioVisual] | (X,Y) | 5,2 / 10,6 / 10,6 | 徽章位置调整 |
| `DrawInsignia.AdjustPos.BuildingsAnchor` | [AudioVisual] | enum: top\|lefttop\|leftbottom\|bottom\|rightbottom\|righttop | 居中 | 建筑徽章锚点 |
| `ColorAddUse8BitRGB` | [AudioVisual] | boolean | - | ColorAdd中的值是否使用8位RGB(0-255) |
| `ConditionYellow/ConditionRed` | [AudioVisual] | float (百分比) | - | 全局黄血/红血阈值 |
| `AircraftLevelLightMultiplier` | [AudioVisual] | float | 0.05 | 飞机高度光照倍率 |
| `JumpjetLevelLightMultiplier` | [AudioVisual] | float | 0.0 | Jumpjet高度光照倍率 |
| `FixTransparencyBlitters` | [General] | boolean | - | 修复RLE SHP透明绘制 |
| `DisguiseBlinkingVisibility` | [General] | boolean | - | 伪装闪烁可见性控制 |
| `AllowWeaponSelectAgainstWalls` | [CombatDamage] | boolean | - | 对围墙时是否选择副武器 |
| `AirstrikeLineColor` | [AudioVisual]/[TechnoType] | (R,G,B) | 255,0,0 | 空袭线颜色 |
| `LaserTargetColor` | [TechnoType] | integer (ColorAdd索引) | 继承全局 | 激光目标线颜色 |
| `ShowTimer.Priority/SortValue` | [SuperWeapon] | integer | 0 | 超武计时器优先级/排序 |

---

## 武器目标筛选 (Weapon Targeting)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `CanTarget` | [Weapon] | enum: none\|land\|water\|empty\|infantry\|units\|buildings\|all | all | 武器可瞄准的目标类型 |
| `CanTargetHouses` | [Weapon] | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | all | 武器可瞄准的阵营 |
| `CanTarget.MaxHealth` | [Weapon] | float (百分比) | 1.0 | 目标血量百分比上限(高于此值不可瞄准) |
| `CanTarget.MinHealth` | [Weapon] | float (百分比) | 0.0 | 目标血量百分比下限(低于此值不可瞄准) |

## 弹头特殊效果 (Warhead Special Effects)

### 暴击系统 (Critical Hits)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Crit.Chance` | [Warhead] | float (0.0-1.0) | 0.0 | 暴击触发几率 |
| `Crit.ApplyChancePerTarget` | [Warhead] | boolean | no | 是否对每个目标单独计算暴击几率 |
| `Crit.ExtraDamage` | [Warhead] | integer | 0 | 暴击附加伤害(受FirepowerMult影响用ApplyFirepowerMult) |
| `Crit.ExtraDamage.ApplyFirepowerMult` | [Warhead] | boolean | no | 暴击额外伤害是否受火力系数影响 |
| `Crit.Warhead` | [Warhead] | WarheadType | - | 暴击时使用的弹头(替换原弹头) |
| `Crit.Warhead.FullDetonation` | [Warhead] | boolean | yes | 暴击弹头是否应用全部弹头效果 |
| `Crit.Affects` | [Warhead] | enum: none\|land\|water\|infantry\|units\|buildings\|all | all | 暴击影响的目标类型 |
| `Crit.AffectsHouses` | [Warhead] | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | all | 暴击对哪些阵营生效 |
| `Crit.AffectBelowPercent` | [Warhead] | float | 1.0 | 仅对血量低于此百分比的目标暴击 |
| `Crit.AffectsAbovePercent` | [Warhead] | float | 0.0 | 仅对血量高于此百分比的目标暴击 |
| `Crit.AnimList` | [Warhead] | list of Animation | - | 暴击时播放的动画列表 |
| `Crit.AnimOnAffectedTargets` | [Warhead] | boolean | no | 暴击动画是否在每个受影响目标上播放 |
| `Crit.SuppressWhenIntercepted` | [Warhead] | boolean | no | 抛射体被拦截时是否抑制暴击 |
| `ImmuneToCrit` | [TechnoType]/[ShieldType] | boolean | no | 是否免疫暴击 |

### 弹头资金转移 (TransactMoney)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `TransactMoney` | [Warhead] | integer | 0 | 弹头命中时的金钱变化(正=给予,负=扣除) |
| `TransactMoney.Display` | [Warhead] | boolean | no | 是否显示金钱浮动提示 |
| `TransactMoney.Display.AtFirer` | [Warhead] | boolean | no | 是否在开火者位置显示(否则在目标位置) |
| `TransactMoney.Display.Houses` | [Warhead] | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | all | 哪些玩家可看到金钱提示 |
| `TransactMoney.Display.Offset` | [Warhead] | (X,Y) | 0,0 | 金钱提示偏移像素 |

### 弹头单位转换 (Convert)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `ConvertN.From` | [Warhead] | list of TechnoTypes | - | 可被转换的源单位(N为组号,从0开始) |
| `ConvertN.To` | [Warhead] | TechnoType | - | 转换成的目标单位(必须同类型) |
| `ConvertN.AffectedHouses` | [Warhead] | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | team | 哪些阵营被转换 |

### 弹头自杀/击杀 (Suicide / KillWeapon)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `CanKill` | [Warhead] | boolean | yes | 弹头是否可以杀死目标(no=最低1血) |
| `KillWeapon` | [Warhead] | WeaponType | - | 杀死目标后在目标位置创建的武器 |
| `KillWeapon.OnFirer` | [Warhead] | WeaponType | - | 同上但以击杀者为视角 |
| `KillWeapon.AffectsHouses` | [Warhead] | enum | all | KillWeapon的阵营过滤 |
| `KillWeapon.Affects` | [Warhead] | enum: none\|aircraft\|buildings\|infantry\|units\|all | all | KillWeapon的目标类型过滤 |
| `SuppressKillWeapons` | [TechnoType] | boolean | - | 该单位被杀死时不触发KillWeapon |
| `SuppressKillWeapons.Types` | [TechnoType] | list of WeaponTypes | all | 需抑制的KillWeapon类型 |

### 弹头位移/反位移 (Displace / UnlimboDetonate)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `UnlimboDetonate` | [Warhead] | boolean | no | 是否让LimboLaunch=yes的单位在引爆点重新出现 |
| `UnlimboDetonate.ForceLocation` | [Warhead] | boolean | no | 是否强制目标格位置(摧毁地形物体) |
| `UnlimboDetonate.KeepTarget` | [Warhead] | boolean | no | 重新出现的单位是否保持原攻击目标 |
| `UnlimboDetonate.KeepSelected` | [Warhead] | boolean | no | 重新出现的单位是否保持选中状态 |

### 弹头发射超武 (LaunchSW)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `LaunchSW` | [Warhead] | list of SuperWeaponTypes | - | 弹头引爆时发射的超武列表 |
| `LaunchSW.RealLaunch` | [Warhead] | boolean | yes | 是否真实发射(消耗充能,需拥有) |
| `LaunchSW.IgnoreInhibitors` | [Warhead] | boolean | no | 是否忽略抑制器 |
| `LaunchSW.IgnoreDesignators` | [Warhead] | boolean | yes | 是否忽略指定器 |

### 弹头生成箱子 (SpawnsCrate)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `SpawnsCrateN.Type` | [Warhead] | enum: money\|unit\|healbase\|cloak\|explosion\|napalm\|squad\|reveal\|armor\|speed\|firepower\|icbm\|invulnerability\|veteran\|ionstorm\|gas\|tiberium\|pod | - | 弹头生成的箱子类型(N从0开始) |
| `SpawnsCrateN.Weight` | [Warhead] | integer | 1 | 该箱子类型的权重 |

### 弹头地图全目标引爆 (DetonateOnAllMapObjects)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `DetonateOnAllMapObjects` | [Warhead] | boolean | no | 是否在地图上所有对象上引爆 |
| `DetonateOnAllMapObjects.Full` | [Warhead] | boolean | yes | 是否应用全部弹头效果 |
| `DetonateOnAllMapObjects.AffectTargets` | [Warhead] | enum | all (b33+默认none) | 影响的目标类型 |
| `DetonateOnAllMapObjects.AffectHouses` | [Warhead] | enum | all (b33+默认none) | 影响的阵营 |
| `DetonateOnAllMapObjects.AffectTypes` | [Warhead] | list of TechnoTypes | (全部) | 具体影响的白名单 |
| `DetonateOnAllMapObjects.IgnoreTypes` | [Warhead] | list of TechnoTypes | (无) | 忽略的黑名单 |
| `DetonateOnAllMapObjects.RequireVerses` | [Warhead] | boolean | no | 是否需要Verses非0才影响 |

### 弹头建筑出售/收起 (BuildingSell/Undeploy)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `BuildingSell` | [Warhead] | boolean | false | 是否出售建筑(含建造动画) |
| `BuildingSell.IgnoreUnsellable` | [Warhead] | boolean | false | 是否忽略Unsellable限制强制出售 |
| `BuildingUndeploy` | [Warhead] | boolean | false | 是否收起有UndeploysInto的建筑 |
| `BuildingUndeploy.Leave` | [Warhead] | boolean | false | 收起后的单位是否试图离开 |

### 弹头伤害倍率系统

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `DamageOwnerMultiplier` | [CombatDamage]/[Warhead] | float | 1.0 | 对自己伤害倍率 |
| `DamageAlliesMultiplier` | [CombatDamage]/[Warhead] | float | 1.0 | 对友军伤害倍率 |
| `DamageEnemiesMultiplier` | [CombatDamage]/[Warhead] | float | 1.0 | 对敌人伤害倍率 |
| `DamageSourceHealthMultiplier` | [Warhead] | float | 0.0 | 基于攻击者血量百分比的额外伤害系数 |
| `DamageTargetHealthMultiplier` | [Warhead] | float | 0.0 | 基于目标血量百分比的额外伤害系数 |

---

## 弹道系统 (Projectile Trajectories)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Trajectory` | [Projectile] | enum: Straight\|Bombard\|Parabola | - | 弹道类型(b39+) |
| `Trajectory.Speed` | [Projectile] | double | - | 弹道初速度 |

### 直线弹道 (Trajectory.Straight)

| 参数 | 默认值 | 说明 |
|---|---|---|
| `Trajectory.Straight.DetonationDistance` | 0.4 | 引爆距离(0=可穿透目标) |
| `Trajectory.Straight.TargetSnapDistance` | 0.5 | 目标吸附距离 |
| `Trajectory.Straight.PassThrough` | false | 是否穿透目标后爆炸 |
| `Trajectory.Straight.PassDetonate` | false | 穿透时是否对每个经过的单位造成伤害 |
| `Trajectory.Straight.PassDetonateWarhead/Damage/Delay` | - | 穿透伤害的弹头/伤害/延迟 |
| `Trajectory.Straight.OffsetCoord` | 0,0,0 | 目标偏移坐标(FLH格式) |
| `Trajectory.Straight.RotateCoord` | 0 | Burst之间旋转角度 |
| `Trajectory.Straight.ProximityImpact` | 0 | 近接引爆(0=禁用,非0=最大触发次数) |
| `Trajectory.Straight.ProximityWarhead/Damage/Radius` | - | 近接引爆的弹头/伤害/检测半径 |
| `Trajectory.Straight.SubjectToGround` | false | 是否受地面高度影响 |
| `Trajectory.Straight.EdgeAttenuation` | 1.0 | 射程边缘伤害衰减系数 |
| `Trajectory.Straight.CountAttenuation` | 1.0 | 每次伤害计数衰减系数 |

### 轰炸弹道 (Trajectory.Bombard)

| 参数 | 默认值 | 说明 |
|---|---|---|
| `Trajectory.Bombard.Height` | 0.0 | 转折点垂直高度 |
| `Trajectory.Bombard.FallPercent` | 1.0 | 转折点水平位置(0=发射者,1=目标) |
| `Trajectory.Bombard.FallPercentShift` | 0.0 | 转折点位置额外偏移 |
| `Trajectory.Bombard.FallScatter.Max/Min` | 0.0 | 转折点散布范围 |
| `Trajectory.Bombard.FreeFallOnTarget` | true | 是否从转折点自由下落 |
| `Trajectory.Bombard.NoLaunch` | false | 是否跳过上升段直接在转折点生成 |
| `Trajectory.Bombard.DetonationDistance` | 0.4 | 引爆距离 |
| `Trajectory.Bombard.TurningPointAnims` | - | 转折点动画 |

### 抛物弹道 (Trajectory.Parabola)

| 参数 | 默认值 | 说明 |
|---|---|---|
| `Trajectory.Parabola.OpenFireMode` | Speed | 发射模式(Speed/Height/Angle/SpeedAndHeight/HeightAndAngle/SpeedAndAngle) |
| `Trajectory.Parabola.ThrowHeight` | 600 | 最大飞行高度 |
| `Trajectory.Parabola.LaunchAngle` | 30 | 发射角度(度) |
| `Trajectory.Parabola.DetonationAngle` | -90.0 | 弹道角度低于此值时提前引爆 |
| `Trajectory.Parabola.DetonationHeight` | -1 | 降至该高度时提前引爆(-1=禁用) |
| `Trajectory.Parabola.BounceTimes` | 0 | 落地反弹次数 |
| `Trajectory.Parabola.BounceOnWater` | no | 是否可在水面反弹 |
| `Trajectory.Parabola.BounceDetonate` | no | 每次反弹是否引爆弹头 |
| `Trajectory.Parabola.BounceAttenuation` | 0.8 | 每次反弹伤害衰减 |
| `Trajectory.Parabola.BounceCoefficient` | 0.8 | 每次反弹速度衰减 |

---

## 抛射体拦截系统 (Interceptor System)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Interceptor` | [TechnoType] | boolean | - | 该单位是否可拦截抛射体 |
| `Interceptor.Weapon` | [TechnoType] | integer | 0 | 拦截使用的武器槽(0=Primary,1=Secondary) |
| `Interceptor.CanTargetHouses` | [TechnoType] | enum | enemies | 可拦截哪些阵营的抛射体 |
| `Interceptor.GuardRange` | [TechnoType] | float | 0.0 | 拦截最大范围 |
| `Interceptor.Veteran/EliteGuardRange` | [TechnoType] | float | - | 老兵/精英拦截范围 |
| `Interceptor.MinimumGuardRange` | [TechnoType] | float | 0.0 | 拦截最小范围 |
| `Interceptor.DeleteOnIntercept` | [TechnoType] | boolean | no | 拦截弹是否直接消失 |
| `Interceptor.WeaponOverride` | [TechnoType] | Weapon | - | 用哪个武器的弹头/伤害替代拦截 |
| `Interceptor.KeepIntact` | [TechnoType] | boolean | no | 被拦截抛射体是否继续运行 |
| `Interceptable` | [Projectile] | boolean | no | 该抛射体是否可被拦截 |
| `Strength` | [Projectile] | integer | 0 | 抛射体耐久值(耐久<=0时被摧毁) |
| `Armor` | [Projectile] | ArmorType | - | 抛射体装甲类型 |

---

## 自动死亡系统 (AutoDeath)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `AutoDeath.Behavior` | [TechnoType] | enum: kill\|vanish\|sell | - | 自动死亡行为(kill=正常死亡,vanish=无声消失,sell=仅建筑) |
| `AutoDeath.VanishAnimation` | [TechnoType] | Animation | - | vanish行为播放的动画 |
| `AutoDeath.OnAmmoDepletion` | [TechnoType] | boolean | no | 弹药归零时自动死亡 |
| `AutoDeath.AfterDelay` | [TechnoType] | integer | - | 延迟帧数后自动死亡 |
| `AutoDeath.TechnosDontExist` | [TechnoType] | list of TechnoTypes | - | 指定单位不存在时自动死亡 |
| `AutoDeath.TechnosDontExist.Any` | [TechnoType] | boolean | no | 是否任一不存在即触发(yes=任一,no=全部) |
| `AutoDeath.TechnosDontExist.Houses` | [TechnoType] | enum | owner | 检查哪些阵营的单位 |
| `AutoDeath.TechnosExist` | [TechnoType] | list of TechnoTypes | - | 指定单位存在时自动死亡 |
| `AutoDeath.TechnosExist.Any` | [TechnoType] | boolean | yes | yes=任一存在即触发 |

---

## 激光尾迹系统 (LaserTrails)

需在artmd.ini中注册`[LaserTrailTypes]`。

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `DrawType` | [LaserTrailType] | enum: laser\|ebolt\|radbeam | laser | 尾迹类型(激光/电弧/辐射光束) |
| `FadeDuration` | [LaserTrailType] | integer | - | 尾迹持续时间(帧) |
| `SegmentLength` | [LaserTrailType] | integer | - | 每段最短长度 |
| `IgnoreVertical` | [LaserTrailType] | boolean | no | 是否忽略垂直移动(不绘制) |
| `CloakVisible` | [LaserTrailType] | boolean | no | 隐形时是否仍可见 |
| `IsHouseColor` | [LaserTrailType] | boolean | - | 是否使用阵营颜色 |
| `Color` | [LaserTrailType] | (R,G,B) | - | 尾迹颜色 |
| `Thickness` | [LaserTrailType] | integer | - | 线宽 |
| `IsIntense` | [LaserTrailType] | boolean | no | 是否支持Alpha混合(发光效果) |
| `LaserTrail.Types` | [Projectile] | LaserTrailType | - | 抛射体上的激光尾迹 |
| `LaserTrailN.Type/FLH/IsOnTurret` | [TechnoType] | - | - | 单位上的第N个激光尾迹 |

---

## 自定义辐射系统 (Custom Radiation)

需注册`[RadiationTypes]`。

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `RadType` | [Weapon] | RadiationType | Radiation | 武器使用的辐射类型 |
| `RadDurationMultiple` | [RadiationType] | integer | - | 辐射持续时间倍率 |
| `RadApplicationDelay` | [RadiationType] | integer | - | 辐射伤害间隔(帧) |
| `RadLevelMax` | [RadiationType] | integer | - | 最大辐射等级 |
| `RadLevelDelay` | [RadiationType] | integer | - | 辐射等级衰减间隔 |
| `RadLevelFactor` | [RadiationType] | double | - | 每辐射等级伤害 |
| `RadColor` | [RadiationType] | (R,G,B) | - | 辐射染色颜色 |
| `RadSiteWarhead` | [RadiationType] | Warhead | - | 辐射伤害使用的弹头 |
| `RadSiteWarhead.Detonate` | [RadiationType] | boolean | no | 弹头是否引爆(应用弹头效果) |
| `RadHasOwner` | [RadiationType] | boolean | no | 辐射伤害是否计入原释放者 |
| `RadHasInvoker` | [RadiationType] | boolean | no | 释放者是否获得经验 |

---

## 强制武器系统 (ForceWeapon)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `ForceWeapon.Naval.Decloaked` | [TechnoType] | integer | -1 | 对付未隐形海军时强制使用的武器槽 |
| `ForceWeapon.Cloaked` | [TechnoType] | integer | -1 | 对付隐形单位时强制使用的武器槽 |
| `ForceWeapon.Disguised` | [TechnoType] | integer | -1 | 对付伪装单位时强制使用的武器槽 |
| `ForceWeapon.UnderEMP` | [TechnoType] | integer | -1 | 对付EMP单位时强制使用的武器槽 |
| `ForceWeapon.Buildings` | [TechnoType] | integer | -1 | 对付建筑时强制使用的武器槽 |
| `ForceWeapon.Defenses` | [TechnoType] | integer | -1 | 对付防御建筑时使用的武器槽(优先级高于Buildings) |
| `ForceWeapon.Infantry` | [TechnoType] | integer | -1 | 对付步兵时强制使用的武器槽 |
| `ForceWeapon.Units` | [TechnoType] | integer | -1 | 对付载具时强制使用的武器槽 |
| `ForceWeapon.Aircraft` | [TechnoType] | integer | -1 | 对付飞机时强制使用的武器槽 |
| `ForceWeapon.InRange` | [TechnoType] | list of integers | - | 优先武器列表(按距离顺序) |

---

## 子机系统增强 (Spawner Enhancements)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Spawner.LimitRange` | [TechnoType] | boolean | no | 是否限制子机追击范围 |
| `Spawner.ExtraLimitRange` | [TechnoType] | integer | 0 | 额外追击距离 |
| `Spawner.DelayFrames` | [TechnoType] | integer | 飞机9/其他20 | 子机发射间隔(帧) |
| `Spawner.AttackImmediately` | [TechnoType] | boolean | no | 子机是否立即攻击 |
| `Spawner.UseTurretFacing` | [TechnoType] | boolean | no | 子机发射方向是否继承炮塔朝向 |
| `Spawner.RecycleRange` | [TechnoType] | float | -1 | 子机回收范围(-1=禁用) |
| `Spawner.RecycleAnim/RecycleCoord/RecycleOnTurret` | [TechnoType] | - | - | 回收动画/位置/是否随炮塔 |
| `Spawns.Queue` | [TechnoType] | list of AircraftTypes | - | 子机类型队列 |
| `InitialSpawnsNumber` | [TechnoType] | integer | - | 初始子机数量 |
| `Promote.IncludeSpawns` | [TechnoType] | boolean | no | 子机是否共享父级老兵等级 |

---

## 谍报/升级/电站增强

### 建筑谍报效果

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `SpyEffect.Custom` | [BuildingType] | boolean | - | 是否应用Ares自定义谍报逻辑 |
| `SpyEffect.VictimSuperWeapon` | [BuildingType] | SuperWeaponType | - | 被渗透时向被渗透方发射的超武 |
| `SpyEffect.InfiltratorSuperWeapon` | [BuildingType] | SuperWeaponType | - | 被渗透时向渗透方发射的超武 |

### 建筑升级增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `PowersUp.Owner` | [BuildingType] | enum: none\|owner/self\|allies/ally\|team\|enemies/enemy\|all | self | 升级可应用于哪些玩家 |
| `PowersUp.Buildings` | [BuildingType] | list of BuildingTypes | - | 升级可放置的建筑白名单 |

### 电站增强器

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `PowerPlantEnhancer.PowerPlants` | [BuildingType] | list of BuildingTypes | - | 被增强的电站列表 |
| `PowerPlantEnhancer.Amount` | [BuildingType] | integer | 0 | 增强的固定电力值 |
| `PowerPlantEnhancer.Factor` | [BuildingType] | float | 1.0 | 增强的电力系数 |

---

## 建造限制组 (BuildLimit Groups)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `BuildLimitGroup.Types` | [TechnoType] | list of TechnoType | - | 共享建造限制的单位组 |
| `BuildLimitGroup.Nums` | [TechnoType] | integer | - | 共享限制的数量 |
| `BuildLimitGroup.Factor` | [TechnoType] | integer | - | 该单位占用几个限制名额 |
| `BuildLimitGroup.ContentIfAnyMatch` | [TechnoType] | boolean | false | 是否任一达到上限即停止全部 |
| `BuildLimitGroup.NotBuildableIfQueueMatch` | [TechnoType] | boolean | false | 建造队列中是否计入限制 |
| `BuildLimitGroup.ExtraLimit.Types/Nums` | [TechnoType] | - | - | 额外增加限制的触发单位/数量 |

---

## 战斗警报/过载/伪装增强

### 战斗警报 (CombatAlert)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `CombatAlert` | [AudioVisual]/[TechnoType] | boolean | - | 是否启用战斗警报 |
| `CombatAlert.IgnoreBuilding` | [AudioVisual] | boolean | true | 是否忽略建筑被攻击 |
| `CombatAlert.SuppressIfInScreen` | [AudioVisual] | boolean | true | 屏幕内可见时是否抑制 |
| `CombatAlert.Interval` | [AudioVisual] | integer (帧) | 150 | 两次警报最小间隔 |

### 心灵控制过载

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Overload.Count/Damage/Frames` | [TechnoType] | list of integers | 继承CombatDamage | 过载等级对应的控制数/伤害/间隔 |
| `Overload.DeathSound` | [TechnoType] | Sound | - | 过载死亡音效 |
| `Overload.ParticleSys` | [TechnoType] | ParticleSystemType | DefaultSparkSystem | 过载粒子系统 |
| `MindControlRangeLimit` | [TechnoType] | double | -1.0 | 心控范围限制(-1=无限制) |
| `MultiMindControl.ReleaseVictim` | [TechnoType] | boolean | no | 是否在攻击新目标前释放被控单位 |

### 伪装增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `DefaultDisguise` | [InfantryType] | InfantryType | - | 默认伪装类型(优先级高于Ares Side设置) |
| `UseDisguiseMovementSpeed` | [InfantryType] | boolean | - | 伪装时是否使用伪装目标的速度 |

---

## 移动/攻击增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `AttackMove.Aggressive` | [General]/[TechnoType] | boolean | false | 攻击移动是否攻击中立建筑(ThreatPosed=0) |
| `AttackMove.UpdateTarget` | [General]/[TechnoType] | boolean | false | 攻击移动是否自动切换最优先目标 |
| `AttackMove.StopWhenTargetAcquired` | [General]/[TechnoType] | boolean | 根据OpportunityFire自动判定 | 发现敌人后是否停止移动 |
| `AttackMove.PursuitTarget` | [TechnoType] | boolean | - | 攻击移动时是否追击目标 |
| `AttackMove.Follow` | [TechnoType] | boolean | false | 攻击移动是否等效Ctrl+Alt跟随 |
| `AttackMove.IgnoreWeaponCheck` | [General] | boolean | false | 是否允许无武器单位接受攻击移动命令 |
| `NoManualMove` | [TechnoType] | boolean | no | 是否禁止玩家手动移动 |
| `NoRearm.UnderEMP/Temporal` | [General]/[TechnoType] | boolean | false | EMP/超时空下是否不重置ROF计时器 |
| `NoReload.UnderEMP/Temporal` | [General]/[TechnoType] | boolean | false | EMP/超时空下是否不装弹 |
| `Convert.HumanToComputer` | [TechnoType] | TechnoType | - | 从人类转AI时变成的单位 |
| `Convert.ComputerToHuman` | [TechnoType] | TechnoType | - | 从AI转人类时变成的单位 |

---

## 乘客/弹药/部署增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `PassengerDeletion.Rate` | [TechnoType] | integer (帧) | - | 乘客删除间隔 |
| `PassengerDeletion.Soylent` | [TechnoType] | boolean | no | 删除乘客是否获得退款 |
| `PassengerDeletion.SoylentMultiplier` | [TechnoType] | float | 1.0 | 退款倍率 |
| `Passengers.SyncOwner` | [TechnoType] | boolean | no | 乘客阵营是否与载具同步 |
| `Passengers.SyncOwner.RevertOnExit` | [TechnoType] | boolean | yes | 离开时乘客阵营是否恢复 |
| `Ammo.Shared` | [TechnoType] | boolean | no | 载具和乘客是否共享弹药 |
| `Ammo.Shared.Group` | [TechnoType] | integer | -1 | 弹药共享组ID |
| `OpenTopped.RangeBonus` | [TechnoType] | integer | - | 乘客攻击范围加成 |
| `OpenTopped.DamageMultiplier` | [TechnoType] | float | - | 乘客攻击伤害倍率 |
| `OpenTopped.ShareTransportTarget` | [TechnoType] | boolean | yes | 是否共享载具攻击目标 |
| `NoQueueUpToEnter/Unload` | [General]/[VehicleTypes] | boolean | false | 是否禁用排队进入/离开 |
| `AmphibiousEnter/Unload` | [General]/[VehicleTypes] | boolean | false | 是否允许在水上建筑进入/卸载 |

---

## 自定义选择框 (Selection Box)

需注册`[SelectBoxTypes]`。

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `Shape` | [SelectBoxType] | filename (.shp) | select.shp | 选择框SHP文件 |
| `Palette` | [SelectBoxType] | filename (.pal) | palette.pal | 调色板文件 |
| `Frames` | [SelectBoxType] | 整数列表 | 步兵1,1,1 / 载具0,0,0 | 满血/黄血/红血对应帧 |
| `Offset` | [SelectBoxType] | (X,Y) | 0,0 | 选择框像素偏移 |
| `Translucency` | [SelectBoxType] | 0/25/50/75 | 0 | 透明度 |
| `VisibleToHouses` | [SelectBoxType] | enum | all | 哪些玩家可见 |
| `DrawAboveTechno` | [SelectBoxType] | boolean | yes | 是否绘制在单位名称之上 |
| `GroundShape/GroundPalette/GroundFrames` | [SelectBoxType] | - | - | 地面阴影图像 |
| `GroundLine` | [SelectBoxType] | boolean | no | 是否显示地面指示线 |
| `GroundLineColor` | [SelectBoxType] | (R,G,B) | 0,255,0 | 地面线颜色 |
| `SelectBox` | [TechnoType] | SelectBox | - | 单位使用的选择框 |
| `HideSelectBox` | [TechnoType] | boolean | no | 是否隐藏选择框 |
| `EnableSelectBox` | [Phobos] (RA2MD.ini) | boolean | no | 全局开关 |

---

## 超武侧栏 (SuperWeapon Sidebar)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `SuperWeaponSidebar` | [Sidebar] (uimd.ini) | boolean | no | 是否显示超武侧栏 |
| `SuperWeaponSidebar.Max/MaxColumns` | [Sidebar] (uimd.ini) | integer | - | 每行最多显示数量/最大列数 |
| `SuperWeaponSidebar.Allow` | [SuperWeapon] | boolean | - | 该超武是否在侧栏显示 |
| `SuperWeaponSidebar.PriorityHouses` | [SuperWeapon] | list | - | 哪些阵营使此超武优先显示 |
| `CameoPriority` | [TechnoType]/[SuperWeapon] | integer | 0 | 侧栏排序优先级(越大越靠前) |

---

## 扩展提示框 (Extended ToolTips)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `ExtendedToolTips` | [ToolTips] (uimd.ini) | boolean | false | 是否启用扩展提示框 |
| `AnchoredToolTips` | [ToolTips] (uimd.ini) | boolean | false | 提示框是否固定于侧栏外部 |
| `CostLabel/PowerLabel/TimeLabel` | [ToolTips] (uimd.ini) | CSF label | $/闪电/手表符号 | 花费/电力/时间标签 |
| `MaxWidth` | [ToolTips] (uimd.ini) | integer | 0 | 提示框最大宽度(像素) |
| `UIDescription` | [TechnoType]/[SuperWeapon] | CSF label | - | 扩展提示框中的描述文本 |
| `ToolTip.Background.Color/Opacity/BlurSize` | [AudioVisual]/[Side] | (R,G,B)/整数/float | 0,0,0/100/0.0 | 提示框背景颜色/透明度/模糊 |
| `ToolTipDescriptions` | [Phobos] (RA2MD.ini) | boolean | yes | 全局描述功能开关 |

---

## 游戏速度/保存/版本

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `CustomGS` | [General] | boolean | - | 是否允许自定义遭遇战游戏速度 |
| `CustomGS.ChangeInterval/ChangeDelay/DefaultDelay` | [General] | integer | - | 速度帧间隔/延迟/默认延迟 |
| `CampaignDefaultGameSpeed` | [Phobos] (RA2MD.ini) | integer | 4 | 战役默认游戏速度 |
| `SaveGameOnScenarioStart` | [Phobos] (RA2MD.ini) | boolean | true | 战役开始时是否自动存档 |
| `RealTimeTimers` | [Phobos] (RA2MD.ini) | boolean | false | 超武计时器是否实时计时 |
| `RealTimeTimers.Adaptive` | [Phobos] (RA2MD.ini) | boolean | false | 是否考虑当前延迟(单人始终true) |

---

## INI文件系统增强

### [$Include] 文件引用系统
在rulesmd.ini/artmd.ini/soundmd.ini/地图INI中使用:
```ini
[$Include]
0=rules1.ini
1=rules2.ini
```
- 文件按顺序读取,后读覆盖先读
- 支持递归嵌套(防无限递归)
- 警告:使用后会禁用Ares的`[#include]`

### $Inherits= INI继承系统
```ini
[PARENT]
[CHILD]
$Inherits=PARENT
```
- 多个父级用逗号分隔(左侧优先级最高)
- 支持递归嵌套
- 警告:使用后会禁用Ares的`[A]:[B]`继承格式

---

## 自定义矿石吞噬 (TiberiumEater)

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `TiberiumEater.TransDelay` | [TechnoType] | integer (帧) | -1 | 吞噬间隔(-1=禁用) |
| `TiberiumEater.CellN` | [TechnoType] | (X,Y) | 单位脚下 | 第N个吞噬格偏移 |
| `TiberiumEater.CashMultiplier` | [TechnoType] | float | 1.0 | 矿石价值倍率 |
| `TiberiumEater.AmountPerCell` | [TechnoType] | integer | 0 (无限) | 每格每次吞噬量 |
| `TiberiumEater.Display` | [TechnoType] | boolean | true | 是否显示金额浮动提示 |
| `TiberiumEater.Anims` | [TechnoType] | list of Animation | - | 吞噬动画(支持8方向,不足补最后) |

---

## 触发/脚本/地图增强

- **新增触发事件 (500-606档)**: 变量比较、累计分数变化、特定单位建造、AttachEffect附加检测等
- **新增触发动作 (500-802档)**: 快速存档、变量编辑、随机数、发射超武、MCV部署控制、横幅系统等
- **新增脚本动作 (10000-18071档)**: 扩展攻击移动、目标类型值(1-36)、时间跳跃、变量编辑等
- **地图默认值自定义**: missionmd.ini `[Defaults]` 控制加载界面默认参数
- **MCV重新部署**: 地图/多人设置中控制
- **通关评级**: missionmd.ini中设置标准时间/评级消息
- **任务简报**: 自定义简报屏幕和音乐

---

## 杂项增强

| 参数 | 位置 | 类型 | 默认值 | 说明 |
|---|---|---|---|---|
| `ApplyLunarFixes` | [General] (lunarmd.ini) | boolean | - | 启用月球地图特殊修复 |
| `DebugPlanningPaths` | [GlobalControls] | boolean | - | 显示路径规划调试信息 |
| `ReloadInTransport` | [TechnoType] | boolean | - | 运输工具内也重新装弹 |
| `CanC4.AllowZeroDamage` | [BuildingType] | boolean | - | 允许C4=no的建筑受1点伤害(强制) |
| `NoSecondaryWeaponFallback` | [TechnoType] | boolean | - | 副武器不可用时不用主武器替代 |
| `Voice[Primary/Secondary]EliteWeaponAttack` | [TechnoType] | Sound | - | 老兵主/副武器攻击语音 |

---

*基于 Phobos CHM 说明书 Build #48 整理。部分参数为开发版特性，可能存在变动。*
