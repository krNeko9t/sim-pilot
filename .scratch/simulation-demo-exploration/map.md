# 仿真方案探索地图

## Notes

范围与阶段边界见[规格](spec.md)；每张工单的状态以其 `Status:` 为准。

- [01 固体形变](issues/01-solid-deformation-candidates.md)
- [02 布料、薄壳与植物物件](issues/02-cloth-shell-plant-candidates.md)
- [03 颗粒材料](issues/03-granular-material-candidates.md)
- [04 黏性流体](issues/04-viscous-fluid-candidates.md)
- [05 密度展示](issues/05-density-demonstration-candidates.md)
- [06 候选总览与推荐](issues/06-candidate-overview-and-recommendations.md)

## Decisions-so-far

- 2026-10-30，02 的候选研究见[研究记录](research/02-cloth-shell-plant-candidates.md)。观看入口、参数依据和 AI 推荐集中于该文件；用户选择候选后，再建立对应复跑工单。
- 2026-10-30，04 已完成[黏性流体候选研究](research/04-viscous-fluid-candidates.md)。优先观看 Mixed MPM 的 50/500 Pa·s 盘卷对照与 SPlisHSPlasH；ρ入口与黏度类型分别记录，E/ν按材料模型判断。共 7 项重点和 5 项备选，交给 05/06 接续密度展示与候选汇总。

## Fog

- 02：完整花束的相互接触与捆扎、盆土内部力学，以及免商业仿真软件的完整塑料瓶复跑材料，仍需补充证据。具体覆盖和待核实项见研究记录。
- 04：ρ的独立视觉差异、选定蜂蜜场景的真实尺度与固定黏度类型，以及部分候选的单位映射和渲染资产，留待补充研究或所选候选复跑。详见[研究记录](research/04-viscous-fluid-candidates.md)。
