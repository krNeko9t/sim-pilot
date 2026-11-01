# 仿真方案探索地图

## Destination

为利用已有几何及 E、ν、ρ 开展正向仿真、输出供下游模型评分的参数影响视频，确定适用的仿真路线与展示场景。范围与阶段边界见[规格](spec.md)。

## Notes

范围与阶段边界见[规格](spec.md)；工单状态、依赖与领取方式见[本地跟踪规则](../../docs/agents/issue-tracker.md#wayfinding-operations)。暂缓工单保留在地图范围内，以 `Status: deferred` 排除出可领取前沿。

- 决策讨论使用 grilling 与 domain-modeling，术语沿用 [GLOSSARY.md](../../GLOSSARY.md)。
- 使用各项目自带资产及成熟公开场景；数据格式与建模条件由研究解释。论文效果、物理模型、实际求解器和后渲染分别判断。
- 首轮候选研究与人工观看已完成；当前推进安排以[从最终用途收束仿真选型主线](issues/11-simulation-selection-direction.md#answer)为准，早期推荐与反馈作为比较依据保留。

## Decisions so far

- 2026-10-30，[固体形变候选与动态演示](issues/01-solid-deformation-candidates.md#comments)：固体为重点，保留 FreeForm、PhysTwin、Boba、AnisoMPM 等效果兴趣及四个框架线索，补查持续维护与实际采用。
- 2026-10-30，[布料、薄壳与植物物件候选与动态演示](issues/02-cloth-shell-plant-candidates.md#comments)：布料与植物的后续问题分开，分别核实材料／接触组合及植物正向模型，塑料瓶保留覆盖缺口。
- 2026-10-30，[颗粒材料候选与动态演示](issues/03-granular-material-candidates.md#comments)：收窄后续投入，优先近期且采用较多的代表项目。
- 2026-10-30，[黏性流体候选与动态演示](issues/04-viscous-fluid-candidates.md#comments)：完成动态入口与参数依据整理，后续关注 Mixed MPM / Newton、Blender / Mantaflow 等代表路线。
- 2026-10-30，[密度影响的展示场景与候选](issues/05-density-demonstration-candidates.md#comments)：整理浮沉等密度展示依据，用户提出软体入液的丰富互动线索，具体场景另行讨论。
- 2026-10-30，[候选总览与按场景推荐](issues/06-candidate-overview-and-recommendations.md#answer)：完成统一观看与候选归并，明确围绕已有参数的正向仿真取舍，并将补充问题交给独立工单。
- 2026-11-01，[从最终用途收束仿真选型主线](issues/11-simulation-selection-direction.md#answer)：近期由 AI 收束可指导复跑的固体方案，给出覆盖、质量依据与参数对比场景；暂缓布料、植物和软体入液，ρ 留在主线。

## Not yet specified

- 完整花束的相互接触与捆扎、盆土内部力学等进一步覆盖需求，随固体方案及植物分支接续后的发现细化；已有线索见[布料、薄壳与植物候选研究](research/02-cloth-shell-plant-candidates.md)。
- 蜂蜜等流体场景的密度独立视觉差异、真实尺度、黏度类型与渲染资产，待主线提出具体覆盖需求或选定场景后细化，见[黏性流体候选研究](research/04-viscous-fluid-candidates.md)。
- 密度场景选定后，进一步明确独立对比所需的初态、单位映射、表面张力及完整示例资产；已有边界见[密度展示研究](research/05-density-demonstration-candidates.md)。

## Out of scope

- 上下游接口、统一仿真平台、真实物件材料标定与下游视频评分方法，范围边界见[规格](spec.md#out-of-scope)。
- 安装与复跑由用户选择方案后的独立工单承接。
