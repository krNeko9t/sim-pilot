# 12: 审阅固体主力路线与首组参数场景

Type: grilling
Status: open
Blocked by: 07

## Question

依据[从完整物体需求重做固体正向仿真选型](../research/07-demand-driven-solid-selection.md)，是否采用其主推荐作为近期固体路线，以及选哪一组公开资产和参数场景进入复跑？本票承接规格中已约定的用户审阅与选择。

研究建议以 PolyFEM＋IPC 承担离线体弹性与复杂接触，首组使用完整软摆件的 E 对比；ν 压缩、ρ 固定冲量分别有具体方案。PhysX 的完整玩具／USD 路线、libuipc 的 GPU／Blender 路线作为有明确条件的替代。审阅需结合所选物体的材料与结构假设、运动证据、出片条件和版本适配成本。

2026-11-01 审阅补充：PolyFEM 已集成 IPC；Genesis MPM.Elastic 保留为普通弹性完整物体的并行候选。FEM／MPM 的当前比较结论及证据强度见[主报告补充](../research/07-demand-driven-solid-selection.md#mpm-比较的实际结果与结论强度)，审阅沿用这一更新后的候选范围。

本票通过与用户的实际讨论记录选定路线、资产、目标参数及必要的效果要求。若用户指出现有比较尚有影响选择的关键缺口，回到[重点固体候选与框架的正向仿真取舍](07-solid-forward-simulation-selection.md)补齐相应证据；选择形成后另建安装与复跑工单。其他暂缓分支沿用原恢复条件。
