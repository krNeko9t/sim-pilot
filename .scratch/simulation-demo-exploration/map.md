# 仿真方案探索地图

## Notes

范围与阶段边界见[规格](spec.md)；每张工单的状态以其 `Status:` 为准。

- [01 固体形变](issues/01-solid-deformation-candidates.md)
- [02 布料、薄壳与植物物件](issues/02-cloth-shell-plant-candidates.md)
- [03 颗粒材料](issues/03-granular-material-candidates.md)
- [04 黏性流体](issues/04-viscous-fluid-candidates.md)
- [05 密度展示](issues/05-density-demonstration-candidates.md)
- [06 候选总览与推荐](issues/06-candidate-overview-and-recommendations.md)

**当前接续：**[重点固体候选与框架的正向仿真取舍](issues/07-solid-forward-simulation-selection.md#comments)因用户反馈重开，继续研究完整物体覆盖、运动／形变质量与数字资产后渲染。当前采用各项目自带数据，示例优先推荐待重新评估；[候选总览与按场景推荐](issues/06-candidate-overview-and-recommendations.md)继续提供全局入口。其他补充问题按 `issues/` 中的状态与依赖接续。

## Decisions-so-far

- 2026-10-30，[候选总览与按场景推荐](issues/06-candidate-overview-and-recommendations.md#answer)：已交付统一观看、实现归并、参数证据、覆盖缺口与场景推荐，并把已明确的补充问题交给独立工单。
- 2026-10-30，用户确认本轮评价和筛选建议已表达完整，调研方式方面沿用已有意见。06 的人工输入已齐备；
- 2026-10-30，用户完成首轮观看评价，详见 01–05 各工单的 Comments。**当前优先级与候选取舍以这轮反馈为准**；此前研究记录中的 AI 推荐保留为首轮判断。
- 2026-10-30，用户补充明确需求重心为**已有几何和材料参数的正向仿真，按需加后渲染**。后续按论文贡献、实际仿真实现与任务用途辨认候选价值；效率、接触、耦合等贡献按场景需要取舍，神经方法按其实际角色判断。共同筛选依据见[规格](spec.md)，用户原意见 [06 Comments](issues/06-candidate-overview-and-recommendations.md#comments)。
- **01 为后续重点**：感兴趣的是 FreeForm、PhysTwin、Boba、AnisoMPM；次一级为 SoftMAC、MiNNIE。PhysGaussian 降为仿真与外观结合参考，Breaking Good 移出当前仿真候选；Newton、PhysX、MuJoCo、libuipc 为用户补充的框架线索。组织持续性、维护与实际采用成为筛选重点。详见 [01 用户反馈](issues/01-solid-deformation-candidates.md#comments)。
- **02 后续分开布料与植物**，塑料瓶单列覆盖缺口；比较 IPC 各实现与材料模型／框架的配合。后续主候选排除老论文，关注在用实现；植物的参数预测、正向仿真和外观表示分别核实。详见 [02 用户反馈](issues/02-cloth-shell-plant-candidates.md#comments)。
- **03、04 收窄投入**，优先近期、用户多、知名度高的代表项目。04 聚焦 Mixed MPM / Newton、Blender / Mantaflow。详见 [03](issues/03-granular-material-candidates.md#comments)、[04](issues/04-viscous-fluid-candidates.md#comments)。
- **05 暂无候选偏好**，已提出的线索是软体入液，在丰富的形变和流体互动中体现密度差异；后续可按该需求另建场景工单。详见 [05 用户反馈](issues/05-density-demonstration-candidates.md#comments)。

- 2026-10-30，02 的候选研究见[研究记录](research/02-cloth-shell-plant-candidates.md)。观看入口、参数依据和 AI 推荐集中于该文件；用户选择候选后，再建立对应复跑工单。
- 2026-10-30，04 已完成[黏性流体候选研究](research/04-viscous-fluid-candidates.md)。优先观看 Mixed MPM 的 50/500 Pa·s 盘卷对照与 SPlisHSPlasH；ρ入口与黏度类型分别记录，E/ν按材料模型判断。共 7 项重点和 5 项备选，交给 05/06 接续密度展示与候选汇总。
- 2026-10-30，05 的[密度展示研究](research/05-density-demonstration-candidates.md)收录球体浮沉、分层流体翻转、液滴回球、解析浮力与梁振动。已抽帧核对 Akinci 2012、Primal SPH 2026、MultiLBM 2024 的密度标签和动态；优先观看浮沉，复跑候选优先考虑 SPlisHSPlasH，理论参照保留 Ferrite。代码发布状态与控制条件分别记录，交给 06 汇总。

## Fog

- 02：完整花束的相互接触与捆扎、盆土内部力学，以及免商业仿真软件的完整塑料瓶复跑材料，仍需补充证据。具体覆盖和待核实项见研究记录。
- 04：ρ的独立视觉差异、选定蜂蜜场景的真实尺度与固定黏度类型，以及部分候选的单位映射和渲染资产，留待补充研究或所选候选复跑。详见[研究记录](research/04-viscous-fluid-candidates.md)。
- 05：浮沉视频采用连续调密度，独立复跑需统一初态；部分最新方法的完整求解器/示例资产、液滴对照的表面张力及单位映射仍待确认。具体边界见[研究记录](research/05-density-demonstration-candidates.md)，按用户选中的候选与场景建立复跑工单。
