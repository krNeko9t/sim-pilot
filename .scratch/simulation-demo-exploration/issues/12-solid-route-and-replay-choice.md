# 12: 审阅固体主力路线与首组参数场景

Type: grilling
Status: claimed
Blocked by: 07

## Question

依据[从完整物体需求重做固体正向仿真选型](../research/07-demand-driven-solid-selection.md)，是否采用其主推荐作为近期固体路线，以及选哪一组公开资产和参数场景进入复跑？本票承接规格中已约定的用户审阅与选择。

研究建议以 PolyFEM＋IPC 承担离线体弹性与复杂接触，首组使用完整软摆件的 E 对比；ν 压缩、ρ 固定冲量分别有具体方案。PhysX 的完整玩具／USD 路线、libuipc 的 GPU／Blender 路线作为有明确条件的替代。审阅需结合所选物体的材料与结构假设、运动证据、出片条件和版本适配成本。

2026-11-01 审阅补充：PolyFEM 已集成 IPC；Genesis MPM.Elastic 保留为普通弹性完整物体的并行候选。FEM／MPM 的当前比较结论及证据强度见[主报告补充](../research/07-demand-driven-solid-selection.md#mpm-比较的实际结果与结论强度)，审阅沿用这一更新后的候选范围。

本票通过与用户的实际讨论记录选定路线、资产、目标参数及必要的效果要求。若用户指出现有比较尚有影响选择的关键缺口，回到[重点固体候选与框架的正向仿真取舍](07-solid-forward-simulation-selection.md)补齐相应证据；选择形成后另建安装与复跑工单。其他暂缓分支沿用原恢复条件。

## Comments

### 2026-11-01：开始方案审阅

本会话认领本票，按 grilling 与 domain-modeling 讨论。首轮待用户选择：是否采用 PolyFEM（已集成 IPC）＋ Blender 作为近期先复跑路线，以及是否以 E 的明显差异为首组目标。AI 建议两项均采用；具体资产、材料／结构假设与效果要求随选择继续收束。

推荐依据沿用主报告：完整软体接触的公开结果与高阶形变精度路径，对应成本为离线计算、场景版本适配和后渲染衔接。Genesis MPM.Elastic 保留为普通弹性完整物体的并行候选；当前证据支持优先尝试的建议，PolyFEM 与 Genesis 在同一任务上的质量和耗时比较仍待对应证据。用户的路线与首组场景选择待本票实际讨论形成。

审阅所用原始入口：[PolyFEM 的 IPC 集成](https://polyfem.github.io/details/contact/)、[高阶 IPC 的完整 Armadillo 动态结果](https://zferg.us/research/high-order-ipc/)、[Genesis 弹性材料](https://genesis-world.readthedocs.io/en/latest/api_reference/engine/material/mpm/elastic.html)、[Genesis 外观网格](https://genesis-world.readthedocs.io/en/latest/user_guide/physics/beyond_rigid_bodies.html)。PolyFEM 官方 README 另列有[社区 Blender 接口](https://github.com/polyfem/polyfem#community-projects)，具体适配方式随选定版本落实。

### 2026-11-01：补充开源性与环境／使用成本

用户追问“这些方案开源性和环境配置难度如何”，并指出 Newton 配环境后即可通过 Python 直接运行，已知其上手方便。此项作为当前选型的审阅维度补齐，路线选择继续待讨论。

官方许可、发布包与源码核查已补入[开源与环境配置成本](../research/07-demand-driven-solid-selection.md#开源与环境配置成本)。Newton 与 Genesis 本体均有低门槛 Python 入口；libuipc 也有 wheel，但 0.0.29 尚未包含 E／ν 映射修复，本任务采用修复后的入口需源码构建。PolyFEM 当前 IPC 常规路径为编译一次后改 JSON 运行，旧 Python 包与所选场景的版本适配另计；PhysX 区分裸 SDK、独立 ovphysx 与 Isaac 环境。核心开源许可与可选渲染／资产的许可边界已分别记录。本轮为只读资料核查。
