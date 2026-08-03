# Ares 3.0 引擎扩展 完整参考

> 基于 Ares 3.0 中文说明书 (2026-02-12) 整理。所有参数适用于 `rulesmd.ini` / `artmd.ini` 等，除非另有标注。

---

## 新增超武类型

Ares 在 YR 原有超武基础上新增/恢复/增强了以下类型：

### 全新超武类型

- **Type=GenericWarhead** — 在指定地点引爆一个弹头。使用 `SW.Damage` + `SW.Warhead`，弹头目标是单元格。默认 AI 瞄准：`Offensive`。
- **Type=UnitDelivery** — 在指定地点投放单位（可投建筑、载具、步兵）。`Deliver.Types` 定义投放列表，`Deliver.Owner` 控制归属，`Deliver.Buildups` 控制建筑是否播放建造动画。默认延迟 20 帧。
- **Type=DropPod** — 从 TS 恢复的空降仓。套子从天而降对地面射击后跑出步兵。`DropPod.Types`/`DropPod.Minimum`/`DropPod.Maximum`/`DropPod.Veterancy`。必须空地着陆。
- **Type=EMPulse** — 从 TS 恢复的 EMP 炮。从建筑发射武器命中目标点。`EMPulse.Cannons` 指定发射建筑，`EMPulse.TargetSelf` 是否自爆，`EMPulse.PulseBall` 炮口准备动画。
- **Type=SonarPulse** — 声呐脉冲，可揭露隐形单位。`SonarPulse.Delay` 控制强制解除隐形的帧数。
- **Type=Battery** — TS 早期预览中的电池超武。长期生效，`Battery.Power` 提供电力，`Battery.Overpower` 过载指定建筑，`Battery.KeepOnline` 保持建筑在线。
- **Type=Firestorm** — 从 TS 恢复的火风暴。激活火风暴围墙摧毁穿越的单位/抛射体。`IgnoresFirestorm=yes` 的单位可安全穿越。长期生效超武设定。
- **Type=HunterSeeker** — 从 TS 恢复的狩猎者。从建筑飞出载具随机撞击敌人。`HunterSeeker.Buildings` 发射建筑列表，`HunterSeeker.Type` 使用的载具，`HunterSeeker.RandomOnly` 是否完全随机。

### 旧超武的重大增强

- **Type=LightningStorm** — 新增 `Lightning.Duration`（持续时间）、`Lightning.RadarOutage`（雷达关闭）、`Lightning.RadarOutageAffects`（影响对象）、`Lightning.HitDelay`/`ScatterDelay`/`ScatterCount`/`Separation`（精细控制雷击）、`Lightning.IgnoreLightningRod`（无视避雷针）、`Lightning.DebrisMin/Max`（碎片数量）、`Lightning.CloudHeight`、`Lightning.BoltExplosion`（雷击动画）、`Lightning.Sounds`/`Clouds`/`Bolts`/`Debris`（音效/云/电/碎片列表）。
- **Type=MultiMissile (Nuke)** — 新增 `Nuke.Payload`（核弹武器）、`Nuke.TakeOff`（起飞动画）、`Nuke.PsiWarning`（心灵探测器警告动画）、`Nuke.SiloLaunch`（是否从核弹井发射）。
- **Type=PsychicDominator** — 新增 `Dominator.FirstAnim/SecondAnim`（大头/生效动画）、`Dominator.FirstAnimHeight/SecondAnimHeight`、`Dominator.FireAtPercentage`（何时生效）、`Dominator.Capture`/`CaptureMindControlled`/`CapturePermaMindControlled`/`CaptureImmuneToPsionics`（捕获范围控制）、`Dominator.PermanentCapture`（是否永久控制）、`Dominator.Ripple`（波纹效果）。
- **Type=ChronoSphere** — 新增 `Chronosphere.BlastSrc/BLastDest`（传送点动画）、`Chronosphere.ReconsiderBuildings`（是否移动建筑）、`Chronosphere.KillOrganic`/`KillTeleporters`/`KillCargo`、`Chronosphere.AffectsIronCurtain`/`AffectsUnwarpable`/`AffectsUndeployable`/`BlowUnplaceable`。建筑视为载具传送：`Chronoshift.IsVehicle=yes`。
- **Type=IronCurtain / ForceShield** — 新增 `Protect.Duration`、`Protect.PowerOutage`（停电时间）、`Protect.PlayFadeSoundTime`。
- **Type=GeneticConverter** — 新增 `Mutate.Explosion`（使用弹头爆炸方式）、`Mutate.IgnoreCyborg`/`IgnoreNotHuman`/`KillNatural`。
- **Type=ParaDrop / AmerParaDrop** — 新增 `ParaDrop.Types`（支持载具！）、`ParaDrop.Num`、`ParaDrop.Aircraft`、`ParaDrop.Count`（飞机数量），以及按国家/阵营/独立飞机的精细化设置（`ParaDrop.Country.PlaneX.*`）。
- **Type=SpyPlane** — 新增 `SpyPlane.Type`、`SpyPlane.Count`、`SpyPlane.Mission`。

### 超武通用新增语句

- `SW.Range` — 生效范围（支持矩形：两个数=长宽）。
- `SW.CreateRadarEvent` — 是否建立雷达事件。
- `SW.AffectsHouse/AffectsTarget` — 影响所属方/目标类型。
- `SW.ShowCameo` — 防御栏是否显示（需配合 `SW.AutoFire=yes`）。
- `SW.Deferment` — 生效前延迟。
- `SW.TimerVisibility` — 计时器可见性。
- `SW.InitialReady` — 是否初始充能完毕。
- `SW.VirtualCharge` — 虚拟充能（失去建筑后台继续计时）。
- `SW.Shots` — 可用次数（-1=无限）。
- `SW.FireIntoShroud` — 能否丢黑幕。
- `SW.UseAITargeting` — 点击图标自动发射（按 AITargeting 约束）。
- `SW.AutoFire` — 无需点击自动发射。
- `SW.ManualFire` — 能否人工指定目标。
- `SW.RequiresTarget/RequiresHouse` — 可点击的目标类型/所属方限制。
- `SW.Group` — 超武组号，用于 AI 脚本关联。
- `SW.AITargeting` — AI 瞄准方式（None/LightningStorm/Nuke/PsychicDominator/GeneticMutator/ParaDrop/DropPod/ForceShield/NoTarget/Offensive/Stealth/Base/EnemyBase/HunterSeeker/IronCurtain/Self/LightningRandom/Attack/LowPower/LowPowerAttack）。
- `SW.AITargeting.Constraints/Preference` — AI 发射约束条件与偏向。
- `SW.RangeMaximum/RangeMinimum` — 射程限制（以超武建筑为中心）。
- `SW.Designators/AnyDesignator` — 指示者单位（超武只能在其范围内发射）。
- `SW.Inhibitors/AnyInhibitor` — 抑制者单位（超武不能在其范围内发射）。建筑断电极/未占领时失效。
- `SW.RequiredHouses/ForbiddenHouses` — 阵营国家限制。
- `SW.AllowPlayer/AllowAI` — 人类/AI 使用权限。
- `SW.AuxBuildings/NegBuildings` — 辅助建筑（需要/禁止存在才能使用）。
- `SW.AlwaysGranted` — 无源超武（不需要建筑）。
- `SW.ChargeToDrainRatio` — 长期超武持续时间倍率。
- `SW.Unstoppable` — 禁止手工停止（火风暴/电池专用）。
- `Money.Amount/DrainAmount/DrainDelay` — 超武花费/持续扣费。
- `SW.Animation/AnimationHeight/AnimationVisibility` — 超武动画。
- `SW.Sound/ActivationSound` — 音效。
- `EVA.Detected/Ready/Activated/Impatient/InsufficientFunds/SelectTarget` — EVA 事件（均可填 `none` 屏蔽）。
- `Message.*` — CSF 信息提示系列。
- `Text.Hold/Ready/Charging/Active/Preparing` — 图标文本。
- `Light.Enabled/Ambient/Red/Green/Blue` — 全局光效。
- `Cursor/NoCursor` — 超武专用鼠标指针。
- `SuperWeapons=` — 建筑可挂载多个超武（列表），不限2个。
- `SW.PostDependent=` — 超时空主/副超武链接。

---

## 弹头增强 (Warhead)

### 通用特殊效果

- `AffectsEnemies/AffectsOwner` — 决定是否影响敌人/自己。
- `EffectsRequireDamage` — 是否只有造成至少1点伤害才赋予效果。默认 no。
- `EffectsRequireVerses` — 是否仅对 Versus>0 的目标赋予效果。默认 yes。
- `AllowZeroDamage` — 伤害为0是否影响范围内所有目标。
- `Malicious` — 是否引起 EVA 警告（高度针对矿车遇袭）。
- `UnitLost.Suppress` — 抑制单位死亡 EVA 警报。
- `DieSound.Override/VoiceDie.Override` — 覆盖死亡音效/语音（`<none>` 禁用）。
- `PreventScatter` — 是否防止目标乱跑。
- `Sonar.Duration` — 强制现身帧数。
- `DisableWeapons.Duration` — 禁用武器帧数（对 C4 等无效）。
- `Flash.Duration` — 闪烁效果帧数。
- `NukeFlash.Duration` — 核弹全屏闪光帧数（NUKE 默认 30）。
- `RelativeDamage` + `RelativeDamage.Buildings/Aircraft/Infantry/Vehicles/Terrain` — 真实百分比伤害（支持 -100 到 100，正=最大生命%，负=当前生命%）。
- `CellSpread.MaxAffect` — 多格弹头对建筑伤害次数上限（防止大范围弹头秒建筑）。
- `DamageAirThreshold` — 立体爆炸高度阈值，低于此值不伤空中单位（-1=永远伤害）。
- `Damage.Deployed` — 部署步兵独立受伤倍率（与匍匐 ProneDamage 分离）。

### EMP 体系

- `EMP.Duration` — EMP 增加量（正负）。
- `EMP.Cap` — EMP 最大累积上限（<0 不叠加，0 无限叠加，>0 有上限）。
- `EMP.Sparkles` — EMP 动画（微观覆盖单位自身设置）。

### 铁幕体系

- `IronCurtain.Duration` — 铁幕增加量（正加负减）。
- `IronCurtain.Cap` — 铁幕最大累积上限。
- `IronCurtain.Flash` — 局部覆盖铁幕闪光效果。

### 心灵控制 / 基因突变 / 时空

- `MindControl.Permanent` — 永久心控（不占心控位，可用于 AOE 永久心控）。
- `InfDeathAnim` — 自定义步兵死亡动画（覆盖旧有限制）。
- `Temporal.WarpAway` — 超空间抹除动画。
- `Temporal.HealthFactor` — 抹除目标所需时间考虑剩余生命值权重。

### 击杀驾驶员

- `KillDriver` — 击杀载具驾驶员。
- `KillDriver.Owner` — 驾驶员被杀后载具归属（civilian/special/neutral）。
- `KillDriver.KillBelowPercent` — 血量低于百分比才能杀。
- `KillDriver.Chance` — 击杀概率。
- `KillDriver.RemoveVeterancy` — 是否移除等级。
- 对 Organic=yes 的单位强制无效。

### 离子炮

- `IonCannon` + `IonCannon.Blast/Beam/Warhead/Damage/Rock` — 独立离子炮逻辑。
- `IonCannon.Ripple` / `Ripple.Radius` — 波纹视觉效果（0-79）。
- `BridgeAbsoluteDestroyer` — 瞬间摧毁桥（需要 `Wall=yes`）。

### 预爆炸/动画相关

- `PreImpactAnim` — 弹头爆炸前播放动画（核弹=NUKEBALL）。
- `PreImpactAnim.Moves` — 爆点跟随动画移动结束位置。
- `DeathWeapon.Suppress` — 不触发列表单位的死亡武器。
- `DeathWeapon.SuppressVehicles/SuppressInfantry` — 整体抑制载具/步兵死亡武器。

### 斩杀 (Culling)

- `Culling=yes` + `Culling.BelowHealth` / `Culling.RookieBelowHealth` / `Culling.VeteranBelowHealth` / `Culling.EliteBelowHealth` — 斩杀血量阈值（%: 1-100，特殊值 0=红血，-1=黄血，-2=绿血）。
- `Culling.Chance` / `Culling.RookieChance` / `Culling.VeteranChance` / `Culling.EliteChance` — 斩杀概率。（注意：概率在弹头释放时结算，范围内所有可斩杀目标一并斩杀。）

### 护甲/免疫系统

- `[ArmorTypes]` 新增护甲注册表。`paper=steel` 格式让 paper 继承 steel 的值，`magic=11%` 为全部 11。
- `Versus.<armor>` — 对新护甲的伤害比例。
- 百分比特殊值：`0%` = 不主动攻击/不还击/不响应手动攻击；`1%` = 不主动攻击/不还击；`2%` = 不主动攻击。仅对尾部 `%` 且恰好 2 位的有效（`00%` 不是特殊值）。
- `Versus.<armor>.ForceFire` / `Retaliate` / `PassiveAcquire` — 手动覆盖攻击/反击/主动攻击行为（仅对 0 比率生效）。

---

## 武器增强 (Weapon)

### 自定义伊文炸弹 (IvanBomb)

- `IvanBomb.Delay` / `Warhead` / `Damage` — 爆炸延迟/弹头/伤害。
- `IvanBomb.AttachSound` / `TickingSound` — 安装音效/滴答音效（需 `Control=loop`）。
- `IvanBomb.DeathBomb` / `DeathBombOnAllies` — 只在死亡时爆炸（而非倒计时）。
- `IvanBomb.CanDetonateDeathBomb` / `CanDetonateTimeBomb` / `DetonateOnSell` — 引爆行为控制。
- `IvanBomb.Detachable` — 能否拆弹。
- `IvanBomb.DestroysBridges` — 能否炸桥。
- `IvanBomb.Image` — 炸弹图像（不含 .shp 扩展名）。
- `IvanBomb.FlickerRate` — 闪烁速率。

### 声波/火焰武器

- `ApplyDamage` — 使 `IsSonic=yes` 或 `UseFireParticles=yes` 的武器赋予正常 Damage。

### 辐射波 (RadBeam)

- `Beam.Color` / `Beam.IsHouseColor` / `Beam.Duration` / `Beam.Amplitude`。

### 特斯拉 (EBolt)

- `Bolt.Color1` / `Bolt.Color2` / `Bolt.Color3` — 颜色（EBolt 为纯色线段）。
- `Bolt.ParticleSystem` — 目标位置生成的粒子系统（`<none>` 禁用）。

### 波逻辑 (Wave - IsSonic/IsMagBeam)

- `Wave.IsLaser` / `Wave.IsBigLaser` — 波类型（反的：IsLaser=大波，IsBigLaser=小波）。
- `Wave.Color` / `Wave.IsHouseColor` / `Wave.Intensity`（支持负值）。
- `Wave.ReverseAgainstVehicles/Buildings/Infantry/Aircraft/Others` — 绘制方向（从目标向发射者）。
- 所有波类型均可使用穿透伤害：`AmbientDamage` + `Warhead`。

### 粗激光 (Laser)

- `LaserThickness` — 激光宽度（最大 2^3=8，强制所属色）。

### 弹药逻辑

- `Weapon►Ammo` — 一次消耗多少弹药（0=不消耗）。
- `NoAmmoWeapon` — 弹药耗尽切换武器（0=主武，1=副武，-1=不切换）。
- `NoAmmoAmount` — 弹药低于等于此值触发切换。

### 武器注册表

- `[WeaponTypes]` — 独立武器注册表，可用于 `ShrapnelWeapon` 等子武器。

### 其他

- `IsDetachedRailgun` — 分离轨道炮（不受 MaxEC 限制不可发射的问题）。
- `Abductor` — 超空间监狱吸入逻辑。`Abductor.Temporal`/`Anim`/`ChangeOwner`/`AbductBelowPercent`/`MaxHealth`。（已知有硬编码问题，例如影响其他武器等。）
- `DiscLaser` 武器现在可以正常播放弹头动画（`DiskLaserAnimEnabled=yes`）。
- `[Weapon]►Cursor.Attack` / `Cursor.AttackOutOfRange` — 武器攻击指针定制。
- `Range` / `ProjectileRange` — 有效追击范围（防止无限追击）。
- 32面开火动画自动支持。

---

## 抛射体增强 (Projectile)

- **弹道散布分离** — `BallisticScatter.Min` / `BallisticScatter.Max`（需要 `Inaccurate=yes` + `Arcing=yes`）。
- **有效追击范围** — `Ranged=yes` + `ProjectileRange`（防止无限追击）。
- **实体建筑阻挡** — `SubjectToBuildings=yes` + `SolidLevel`。
- **战壕穿透** — `SubjectToTrenches=yes/no`。
- **多方向抛射体动画** — `AnimLength`（32面每面动画，需 `Rotates=yes`）。
- **粒子型抛射体轨迹** — `AttachedSystem`（挂载 BehavesLike=Smoke 的粒子系统）。
- **火风暴** — `IgnoresFirestorm=yes` 可穿越火风暴墙。

### 分裂逻辑

- `Splits=yes` + `AirburstWeapon` + `Cluster`。
- 可和 `Airburst=yes` 连用（母弹在目标头上引爆）。
- 可和追击范围连用。
- `RetargetAccuracy` — 子武器攻击原目标概率。
- `RetargetSelf` — 子武器是否可能攻击发射者。
- 受子武器弹头 `AffectsAllies/AffectsEnemies` 影响寻敌。

### 空爆逻辑

- `Airburst=yes` + `AirburstWeapon`。
- `AirburstSpread` — 空爆覆盖半径（默认 1.5=3x3，不能和分裂混用）。
- `AroundTarget` — 子武器散布原点（yes=目标中心，no=抛体爆炸处）。

---

## 单位/建筑增强 (TechnoType)

此处按功能分类罗列 Ares 在 TechnoType（InfantryType/VehicleType/AircraftType/BuildingType）上新增/增强的参数。

### 核心战斗属性

- `AttachEffect.*` — 属性赋予系统（AE），见下方独立章节。
- `EMP.Threshold` — EMP 摧毁阈值（yes=1帧, no=0, inair=-1, 正整数=达到该量摧毁, 负整数=空中达到该量摧毁）。
- `ImmuneToEMP` — 是否免疫 EMP。
- `EMP.Modifier` — EMP 抗性乘数。
- `ImmuneToAbduction` — 是否免疫超空间监狱吸入。
- `ImmuneToBerserk` — 是否免疫狂暴（可覆盖 `ImmuneToPsionics`）。
- `ImmuneToSaboteurs` — 建筑是否免疫破坏者。
- `CivilianEnemy` — 是否视为主动攻击目标（平民敌对）。
- `CanPassiveAquire.Guard` — Guard 模式下是否主动攻击。
- `CanPassiveAquire.Cloak` — 隐形时 Guard 模式下是否主动攻击。
- `NoManualFire` — 禁止手动选择目标攻击。
- `NoManualUnload` — 禁止手动卸载乘员。
- `NoManualEnter` — 禁止手动装载乘员。
- `NoSelfGuardArea` — 禁止手动区域防御。

### 变形/转换/部署

- `Convert.Deploy` — `IsSimpleDeployer=yes` 单位部署后变换单位。支持 `DeployToLand` 和 `DeployDir`。
- `Convert.Script` — 执行脚本时变换单位。
- `Convert.Water` — 移动到水格变换单位（增强版 WaterImage）。
- `Convert.Land` — 移动到陆地格变换单位。
- 以上全部支持更换 Locomotor。
- `WaterImage` — 水中素材（VehicleType，支持 Shape 载具）。
- `DeployDir` — 部署朝向（0-7）微观定义。

### 乘员/载荷

- `InitialPayload.Types/Nums` — 初始载荷单位/数量（支持套娃，建筑需 `CanBeOccupied=yes` 或 `InfantryAbsorb=yes`）。
- `Passengers.Allowed/Disallowed` — 允许/禁止进入的特定单位。
- `Passengers.BySize` — 装载时是否检查 Size（no=每人占1格）。
- `PassengerTurret` — 超空间监狱炮塔跟随乘客数切换。
- `Operator` — 指定载具需要的驾驶员类型（`_ANY_`=任意，建筑需 `InfantryAbsorb` 或 `UnitAbsorb`）。
- `PoweredBy` — 遥控坦克多单位控制（建筑列表）。

### 隐形体系

- `Cloakable.Stages` — 隐形层级（越小越快）。
- `Cloakable.Deployed` — 步兵部署后才隐形。
- `Cloakable.Powered` — 建筑有电时才隐形。
- `Cloakable.Allowed` — 是否允许隐形（no=隐形发生器也无效）。
- `CloakSound` / `DecloakSound` — 微观隐/现声音。
- 空中单位可被隐形发生器隐形（`CloakHeight` 全局阈值）。
- `SensorArray.Warn` — 传感器侦测到是否警告。
- `ForceDecloak` (AE标签) — 弹头赋予 AE 时强制解除隐形。

### 升级/经验/军衔

- `Experience.FromPassengers/PromotePassengers/PassengerModifier` — 乘客击杀经验分配。
- `Experience.FromAirstrike/AirstrikeModifier` — 空袭击杀经验分配。
- `Experience.MindControlSelfModifier/MindControlVictimModifier` — 心控击杀经验分配。
- `Experience.SpawnOwnerModifier/SpawnModifier` — 子机击杀经验分配。
- `Promote.IncludePassengers` — 载具升级时乘客同步等级。
- `Promote.VeteranType/EliteType` — 升级转换单位（同类互换）。
- `Promote.VeteranExperience/EliteExperience` — 升级转换后经验倍率（-1.0 降一级）。
- 新增老兵/精英能力：`EMPIMMUNE` / `RADIMMUNE` / `PROTECTED_DRIVER` / `UNWARPABLE` / `POISONIMMUNE` / `PSIONICWEAPONIMMUNE` / `PSIONICSIMMUNE`。
- `Promote.VeteranSound/EliteSound` — 升级音效微观。
- `Promote.VeteranFlash/EliteFlash` — 升级闪光时长。
- `EVA.VeteranPromoted/ElitePromoted` — 升级 EVA。
- `Insignia.Rookie/Veteran/Elite` — 自定义军衔 SHP。
- `InsigniaFrame.*` — 军衔帧设定。
- `Insignia.ShowEnemy` — 是否向敌人显示等级。

### 自愈/维修

- `SelfHealing.Rate/Max/Amount` — 自愈速率/上限/每次回血量。
- `SelfHealing.RookieMax/VeteranMax/EliteMax` — 分级自愈上限。
- `SelfHealing.RookieAmount/VeteranAmount/EliteAmount` — 分级回血量。
- `SelfHealing.CombatDelay` — 受击后延迟自愈帧数。
- `EngineerRepairable` — 建筑是否允许工程师修理。

### 弹药/武器相关

- `ReloadAmount` / `EmptyReloadAmount` — 弹药恢复量/空仓恢复量。
- `NoAmmoWeapon` / `NoAmmoAmount` — 弹药耗尽切武器。
- `AircraftType►ReloadRate` — 飞机弹药恢复速率。
- `TurretROT` — 炮台独立转向速度。

### 物理/运动

- `AirRate` — 空中单位专用帧速（覆盖 WalkRate/IdleRate）。
- `FallRate.Parachute/NoParachute/ParachuteMax/NoParachuteMax` — 降落速度分离控制。
- `Parachute.Anim` — 自定义降落伞动画。
- `CrushDamage` + `CrushDamage.Warhead` — 反碾压伤害（对方压自己时对方受伤）。
- `OmniCrusher.Aggressive` — 万能碾压单位是否主动追压。
- `DigIn/DigOut` / `DigInSound/DigOutSound` — 钻地/钻出动效分设。
- `Carryall.SizeLimit` — 吊运尺寸限制。
- `Carryall.Allowed` — 载具是否可被吊运。

### 生产/建造

- `BuildTime.Speed` — 微观建造速度（分钟/1000块）。
- `BuildTime.Cost` — 建造时间结算用独立 Cost。
- `BuildTime.LowPowerPenalty/MinLowPower/MaxLowPower` — 低电建造惩罚。
- `BuildTime.MultipleFactory` — 多工厂加速因数。
- `FactoryPlant.Multiplier` — 工业工厂效果倍率（支持 -1.0 到 1.0）。
- `Prerequisite.RequiredTheaters` — 需要特定气候。
- `Prerequisite.Negative` — 有其中任一建筑禁止建造。
- `Prerequisite.Lists` / `Prerequisite.List#` — 多重独立建造前提。
- `Prerequisite.StolenTechs` — 偷窃科技前提。
- `FactoryOwners` / `FactoryOwners.Forbidden` — 特定国家工厂可/不可建造。
- `FactoryOwners.Permanent` / `FactoryOwners.HasAllPlans` — 占领工厂永久/临时科技。
- `BuiltAt` — 指定在哪个建筑建造（狗屋逻辑）。
- `CloningFacility` — 建筑是否可克隆载具。
- `Cloneable` / `ClonedAt` / `ClonedAs` — 克隆相关微观控制。
- `Academy.*` — 训练所系列：`InfantryVeterancy` / `AircraftVeterancy` / `VehicleVeterancy` / `BuildingVeterancy` 以及 `Types` / `Ignore`。
- `Groups` — 类别选择组（如 `GroupAs=ENGINEER` 全选所有工程师）。

### 侦察/探测

- `RadarJamRadius` — 雷达干扰范围。
- `HasSpotlight` + `Spotlight.StartHeight/Distance/AttachedTo/DisableRed/Green/Blue/DisableColor` — 探照灯系统（车只能向前，建筑扫描）。
- `RadialIndicatorRadius` — 视觉指示器范围（HasRadialIndicator 扩展到非建筑单位）。

### 赏金/间谍/逆向工程

- `Bounty` + `Bounty.Value/RookieValue/VeteranValue/EliteValue` — 赏金猎人/猎物设定。
- `Bounty.Display` — 是否显示赏金获得效果。
- `SpyEffect.Custom` — 启用自定义渗透效果。
- `SpyEffect.ResetRadar/RevealRadar/KeepRadar` — 雷达渗透。
- `SpyEffect.PowerOutageDuration` — 停电时间。
- `SpyEffect.StolenMoneyAmount/StolenMoneyPercentage` — 偷钱（可同时设上限+比例）。
- `SpyEffect.ResetSuperweapons` — 重置所有超武。
- `SpyEffect.SuperWeapon/SuperWeaponPermanent` — 渗透给予超武（可用一次或永久）。
- `SpyEffect.StolenTechIndex` — 偷窃项（0-31列表）。
- `SpyEffect.InfantryVeterancy/VehicleVeterancy/NavalVeterancy/AircraftVeterancy/BuildingVeterancy` — 渗透后初始老兵分类独立控制。
- `SpyEffect.RevealProduction` — 查看对方生产。
- `SpyEffect.UndoReverseEngineer` — 取消逆向工程。
- `SpyEffect.SabotageDelay` — 建筑定时自爆。
- `ReverseEngineersVictims` — 建筑启用逆向工程（需搭配 `Grinding=yes`）。
- `CanBeReversed` / `ReversedAs` — 单位逆向工程控制。
- `SecretLab.PossibleBoons` / `SecretLab.GenerateOnCapture` — 秘密科技实验室。
- `SecretLab.RequiredHouses/ForbiddenHouses` — 获得限制（写在 Boon 单位上）。

### 建筑特性

- `SuperWeapons` — 建筑可挂载多个（>2）超武。
- `IsPassable` — 建筑格可通行。
- `GateDownSound/GateUpSound` — 闸门声音自定义。
- `LightningRod` + `LightningRod.Modifier` — 避雷针。
- `SolidHeight/SolidLevel` — 实体建筑阻挡高度/坚固度。
- `Firestorm.Wall` — 火风暴围墙节点。
- `Tunnel` — 隧道网络入口（需注册 `[TunnelTypes]`）。
- `Bunker.Raidable` — 敌方步兵可占据战斗碉堡。
- `CanBeOccupiedBy` — 指定哪些步兵可驻兵此建筑。
- `IsTrench` — 战壕类型 ID（同类型可连接移动）。
- `UC.PassThrough/UC.FatalRate/UC.DamageMultiplier` — 掩体穿透概率/一击必杀率/伤害倍率。
- `UnitSell` — 建筑可变卖内部载具。
- `Rubble.Destroyed/Intact` — 废墟逻辑（被摧毁/修复转换）。
- `Rubble.Destroyed.Remove/Owner/Strength/Anim` — 废墟转换精细控制。
- `Rubble.Intact.Remove/Owner/Strength/Anim` — 工程师修复转换控制。
- `Fake` — 假建筑（显示 TXT_FAKE）。
- `EnemyUIName` — 敌方看到建筑显示的名称。
- `FakeOf` — 假单位所属原单位（价值计算用）。
- `MassSelectable` — 1x1 建筑是否可框选。
- `Degrade.Percentage/Amount` — 建筑微损伤（断电掉血）。
- `BuildupTime/SellTime` — 建筑拔起/变卖时间独立控制。
- `ProduceCashDisplay` / `DisplayCreditsDelay` — 资金显示。
- `DockUnloadCell/DockUnloadFacing` (artmd.ini) — 矿厂卸矿格/朝向。

### 矿车/建筑特定

- `Harvester.LongScan/ShortScan/ScanCorrection/TooFarDistance/KickDelay` — 矿车寻矿范围全面自定义。
- `Refinery.UseStorage` — 启用储矿逻辑（矿不直接变钱）。
- `TiberiumSpill` — 携带矿石单位被摧毁时泄露矿石。
- `TiberiumHeal` — 单位可在泰矿上回血。
- `TiberiumRemains` — 被摧毁时留下泰矿（独立于 TiberiumHeal）。
- `TiberiumProof` — 免疫泰矿伤害。
- `TiberiumTransmogrify` — 泰矿死亡转变器官兽概率。
- `Doggie` — 步兵设为泰伯利亚恶魔（趴矿/攻击/受伤回矿治疗，需 `Crawls=yes`）。

### 粒子/受损系统

- `DamageSparks` — 是否飙受损火花。
- `DamageSmokeParticleSystems/DamageSparksParticleSystems` — 自定义受损烟雾/火花粒子系统列表。
- 粒子系统在 BehavesLike=Spark/Railgun/Smoke 且 HoldsWhat 一致时自动优化。

### 其他

- `AlternateTheaterArt` — 根据气候自动换装（JUNK→JUNKA/JUNKD 等）。
- `VoiceAirstrikeAttack/VoiceAirstrikeAbort` — 空袭语音。
- `Smoke.Anim/ChanceRed/ChanceDead` — 飞机尾烟。
- `Crashable/CrashSpin` — 飞机坠毁控制。
- `Missile.Custom` + 全套导弹运动参数 + `Missile.Weapon/EliteWeapon`。
- `VehicleThief.*` — 劫车犯系列。
- `Saboteur` — 破坏者（进入并摧毁建筑）。
- `Assaulter.Level` — 突击等级。
- `KeepAlive` — 是否保持存活（防止快速游戏判负），已与 DontScore/Insignificant 分离。
- `Unsellable` — 扩展到非建筑单位。
- `EVA.Lost` — 单位被毁独立 EVA 语音（`none` 不播报）。
- `ProtectedDriver` / `CanDrive` / `CanBeDriven` — 驾驶员保护/驾驶/可被驾驶。
- `Crew.TechnicianChance` — 建筑摧毁出现技师概率。
- `Survivor.Side#` — 阵营专属逃生步兵。
- `Survivor.Pilots` + 分级逃生概率 — 驾驶员/乘客逃生概率。
- `Crew.EngineerChance` — 建筑被占后逃出工程师概率。
- `DesignatorRange/InhibitorRange` — 指示者/抑制者范围。
- `RadialIndicatorRadius` — HasRadialIndicator 扩展到步兵/载具/飞机。
- `Cursor.Move/NoMove/Deploy/NoDeploy/Enter/NoEnter/Spy` — 鼠标指针微观定制。

### 升级/经验系统

- `EnemyInsignia` (全局) / `Insignia.ShowEnemy` (微观) — 对敌隐藏等级。
- `VeteranBuildings` (国家) — 生产即为精英的建筑列表。
- `GivesBounty` (国家) — 国家是否获得赏金。
- `Trainable=yes` 单位的乘客/空袭飞机/受控单位/子机经验分配全面自定义。

---

## 属性赋予系统 (AttachEffect / AE)

AE 是 Ares 最强大的特性之一，可为单位赋予属性。

### 通用标签（单位和弹头通用）

- `AttachEffect.Animation` — 伴随动画（动画需注册）。
- `AttachEffect.Duration` — 持续时间（-1=无限）。
- `AttachEffect.TemporalHidesAnim` — 超时空时是否停止动画。
- `AttachEffect.SpeedMultiplier` — 速度加成（对 Foot/Mech 运动模式）。
- `AttachEffect.ArmorMultiplier` — 装甲加成（所受杀伤除以该值，向下取整）。
- `AttachEffect.FirepowerMultiplier` — 伤害加成（正伤害可跨正负变治疗）。
- `AttachEffect.ROFMultiplier` — ROF 因数（仅在开火时赋予，非实时）。
- `AttachEffect.Cloakable` — 持续期间获得隐形。
- `AttachEffect.ForceDecloak` — 强制解除隐形（仅触发瞬间）。
- `AttachEffect.DiscardOnEntry` — 进入建筑/载具时效果是否消失。
- `AttachEffect.PenetratesIronCurtain` — 是否穿透铁幕/力盾附着。
- `AttachEffect.Cumulative` (Warhead only) — 是否叠加作用。
- `AttachEffect.AnimResetOnReapply` (Warhead only) — 再次赋予时是否重置动画。

### 单位专用

- `AttachEffect.Delay` — 获得AE后冷却帧数（负值不重置）。
- `AttachEffect.InitialDelay` — AE 附着后延迟生效帧数。

---

## 营地/国家增强 (Side / Country)

### 新增阵营

- `[Sides]` 注册表，可无限阵营（国家最多16）。
- `Side►DefaultDisguise` — 间谍默认伪装。
- `Side►Crew/Engineer/Technician` — 阵营驾驶员/工程师/技师。
- `Side►SurvivorDivisor` — 残兵数量。
- `Side►AI.BaseDefenses/BaseDefenseCounts` — AI 防御建筑/数量（困难/中等/简单）。
- `Side►ParaDrop.Types/Num/Aircraft/Parachute.Anim` — 阵营默认伞兵（Types 支持载具）。
- `Side►HunterSeeker` — 阵营狩猎者单位。
- `Side►Sidebar.MixFileIndex/Sidebar.YuriFileNames` — 侧边栏 UI 文件。
- `Side►ToolTipColor/MessageTextColor/DialogBackground.*/LoadingTheme/EVA.Tag` — UI 定制。
- `Side►CampaignScore.*/MultiplayerScore.*` — 分数画面。
- `Side►GraphicalText.Image/Palette` — 游戏结束图。

### 新增国家

- `[Countries]` 注册表。
- `Country►AI.PowerPlants` — AI 认为的电厂列表。
- `Country►ParaDrop.Types/Num/Aircraft/Parachute.Anim` — 国家伞兵（支持载具）。
- `Country►VeteranBuildings` — 生产即为精英的建筑列表。
- `Country►GivesBounty` — 是否能获得赏金。
- `Country►File.Flag/LoadScreen/LoadScreenPAL` — UI 国旗/载入图。
- `Country►LoadScreenText.*/MenuText.Status` — 载入文本。
- `Country►RandomSelectionWeight` — 随机选中权重。
- `Country►ListIndex` — 选择表排序（负值隐藏）。
- `Country►File.Taunt` — 对战嘲笑语音（仅16国，`~~` 代替 `%02i`）。
- `Country►LoadingTheme` — 国家载入音乐。
- `Country►StartInMultiplayer.Types` — 初始单位（支持 Infantry 和 Vehicle，`<none>` 无初始单位）。
- `Country►StartInMultiplayer.WithConst` — 初始直接获得建筑（而非 MCV）。
- `Country►Degrades` — 是否启用建筑微损伤。
- `Country►CanBeDriven` — 该国单位是否可被驾驶。

### EVA 语音

- `[EVATypes]` 注册表，可新增 EVA 语音阵营。
- `evamd.ini` 中按 `[EVA_xxx]` 设定各 EVA 条目对应不同阵营语音。

---

## INI 系统增强

### 继承

- `[NewUnit]:[BaseUnit]` 语法，继承所有基础单位属性后叠加覆盖。被继承项必须在继承者之前定义。

### +=快速注册

- `[Section] += Entry` 自动分配不重复序号，简免手动数数。地图内置 INI 不可用。

### #include 文件拆分

- `[#include] 1=rules_sw.ini` 等拆分 rules/art/ai/ui/mission INI。
- 子 INI 可继续 `#include` 嵌套（读取顺序有特定规则）。
- 子 INI 中可出现多个同名 `[Section]`（主 INI 不可）。

### CSF 增强

- 加载 stringtable00.csf 到 stringtable99.csf（优先级高于 ra2md.csf）。
- 直接使用 `NOSTR:前缀` 内联字符串（最多31字符）。
- CSF 条目最大 20000 条。
- 语言中立 CSF（Language 设为 -1）始终加载。

### MIX 增强

- expandmd##.mix 优先载入。
- 支持多 BAG 音频文件（audio01.idx/bag 到 audio99.idx/bag）。
- 音频可直接从根目录和 MIX 读取 .wav（无需打包进 BAG）。

---

## 粒子系统增强

- 对 Spark/Railgun/Smoke 三类粒子系统自动优化（需 HoldsWhat 一致）。
- Gas 类粒子：`DamageRange` — 自定义伤害范围。
- 有形粒子：`Palette` — 自定义色盘（在 RULES 中指定）。
- 动画产生粒子：`SpawnsParticle.RangeMinimum/RangeMaximum` — 粒子偏移距离。

---

## 全局控制 (Global)

- `AllowParallelAIQueues` — AI 是否并行生产队列。
- `AllowBypassBuildLimit` — AI 不同难度无视 BuildLimit（三值对应简/中/难）。
- `TeamRetaliate` — 团队复仇（TS 恢复）。
- `ReturnStructures` — 占有者败北/投降时建筑回归中立。
- `TogglePowerAllowed` — 是否允许手动切换电源。
- `TogglePowerDelay` — 切换电源延迟。
- `EnemyWrench` — 敌方是否看到维修扳手。
- `EnemyInsignia` — 敌方是否看到等级。
- `EngineerCaptureLevel/EngineerDamage/EngineerAlwaysCaptureTech` — 多位工程师系统。
- `AllowMultiEngineer` (UISettings) — 多位工程师勾选框。
- `RepairStopOnInsufficientFunds` — 资金匮乏时维修暂停/停止。
- `CloakHeight` — 隐形发生器可作用的高度阈值。
- `TiberiumHealEnabled` / `TiberiumDamageEnabled` — 泰矿治疗/伤害开关。
- `BountyEnablers` — 赏金逻辑启动所需建筑。
- `StartInMultiplayerUnitCost` — 固定平均单位成本。
- `TypeSelectUseDeploy` — T 选是否自动包含部署前/后单位。
- `DropPodTypes/Minimum/Maximum` — 全局空降仓设置。
- `Degrade.Enabled/Percentage/AmountNormal/AmountConsumer` — 全局建筑微损伤。
- `AIFriendlyDistance` — AI 友军基地聚集距离。
- `ChronoInfantryCrush` — 超时空步兵传坦克上是否摧毁坦克。
- `DamageToFirestormDamageCoefficient` — 火风暴摧毁单位对持续时间的影响。
- `DebugKeysEnabled` — 是否启用调试键盘命令。
- `IQ►TogglePower` — AI 电力切换等级。

---

## UI / 杂项增强

- 最高分辨率 4096x4096（强制 HiRes）。
- 截图输出 PCX 而非 BMP。
- 命令行参数：`-CD`（免CD）、`-NOLOGO`（不播放开场动画）、`-LOG`（生成 debug.log）、`-LOG-CSF`（CSF 缺失记录）、`-STRICT`（非必要错误抛 IE）、`-AI-CONTROL`（AI 代打模式）。
- 战役列表 TS 风格（`CampaignList=yes`）。
- 4个自定义战役按钮（`CampaignX`）。
- 16种可选颜色（`SlotX.DisplayColor/ColorScheme/Tooltip`）。
- UI 所有控件颜色可定制（`[UISettings]►Color.*`）。
- 主菜单按钮可隐藏/禁用（`SinglePlayerButton=hide` 等）。
- MOD 名称/版本信息（`[VersionInfo]`）。
- 发布说明文本（`TXT_RELEASE_NOTE` CSF）。
- 随机地图支持城市地形（`[URBAN]`）。
- 热键：地图快照、调试日志、类型数据转储、AI 基地计划转储、FPS 计数器。
- `一键跑路`：`QuickExit=yes` 跳过退出确认。
- `一次多订单`：Shift+左键一次下单5个。
- PCX 文件图标（`CameoPCX`/`AltCameoPCX`/`SidebarPCX`）。
- 标签长度从 128 提升到 512 字符。
- 注册名最大 24 字符（超出截断警告）。

---

## 地图编辑器 / 触发增强

### 新增触发条件 (Events)
- 62-65: EMP 事件系列（被EMP/被特定方EMP/解除/特定方解除）
- 66: 敌人被探照灯发现
- 67-68: 驾驶员被杀害/被特定方杀害
- 69-70: 载具被夺取/被特定方夺取
- 71-74: 劫持事件系列
- 75-76: 超武释放/停止
- 77: 超武在路径点释放
- 78-80: 逆向工程事件系列
- 81-82: 所属方拥有/不拥有指定数量某类型
- 83-85: 被任意方攻击或摧毁/被特定方/被特定方一切手段摧毁
- 86: 科技类型数量不多于
- 87-88: 存活条件全部被摧毁（KeepAlive）

### 新增触发行为 (Actions)
- 146: 获得额外电力（可负值）
- 147: 杀死驾驶员（可指定移交所属方）
- 148: 设定 EVA 语音
- 149: 编组
- 92: 激活火风暴（地图触发）
- 93: 停止火风暴

### 新增小队脚本
- 53: 敌方基地外集合
- 54: 己方基地外集合
- 55: 请求超武
- 65: 给予额外电力
- 66: 杀死载具驾驶员
- 67: 夺取就近空载具
- 68: 转换形态（Convert.Script）
- 69: 从隐形状态现身
- 70: 禁用武器

---

## BUG 修复要点（Ares 相比原版）

- 恢复存档功能。
- 动画伤害弹头可自定义。
- 载具可为磁暴线圈建筑充电。
- IvanBomb 可用于载具/飞机/建筑。
- 加载建筑可作为建造前提。
- 自定义色盘可用于单位/建筑。
- 建筑摧毁动画有所属色。
- 建筑/步兵弹药正常恢复。
- 注册单位数量上限取消（>100 无异常）。
- AlphaImage 可移动且支持朝向。
- 战机空对空直接允许（只需 `AA=yes`）。
- BaseUnit 数量不限 3 个。
- 墙正确映射所属色。
- 闸门可直接放墙上。
- 有炮台单位完全伪装成树。
- 飞机可使用轨道炮/可隐形。
- 建筑使用 Spawns 逻辑时子机可用 Rocket 类运动。
- 多 Burst 武器切换目标不重置计数器（卡攻速 Bug 修复）。
- Guard 下单位不追击飞行单位。
- 超时空机场飞机不再允许生产问题。
- 探照灯留在原地问题。

---

> 注：本摘要覆盖 Ares 3.0 全部主要功能性参数。部分标注为"已移除/已废弃"的语句已过滤。AI 行为相关细节请参考 Ares 官方文档。
