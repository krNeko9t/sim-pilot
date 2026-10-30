# 07: 重点固体候选与框架的正向仿真取舍

Type: research
Status: open
Blocked by: 01

## Question

在上游提供几何与 E、ν、ρ 的条件下，用户感兴趣的 FreeForm、PhysTwin、Boba、AnisoMPM，次级兴趣 SoftMAC、MiNNIE，以及 Newton、PhysX、MuJoCo、libuipc 框架线索，哪些适合继续选择具体正向仿真场景？分别有什么维护、后续继承和实际采用证据支持这一取舍？

依据[固体形变的用户反馈](01-solid-deformation-candidates.md#comments)、[探索规格](../spec.md)与[候选总览](../research/06-candidate-overview-and-recommendations.md)。本票承接已明确的候选范围，给出场景选择依据；安装与复跑在用户选择后另立工单。

- 对每条重点路线定位具体 solver、本构、E／ν／ρ 输入或换算，以及必要附加材料输入；复用总览已有证据，集中补影响取舍的缺口。
- PhysTwin／Boba 重点查弹簧与质量参数如何接收上游物理量；AnisoMPM 说明纤维、损伤输入与当前任务的关系。保留用户效果兴趣，分别判断直接正向仿真与视觉参考价值。
- 框架落实到软体示例、几何要求、接触能力和输出路径；共享 Warp、MPM 或 FEM 名称的实现分别说明。固液耦合场景由独立场景票承接。
- 维护与采用列具体发布日期、代码更新、外部集成或公开使用案例；组织声誉、作者维护承诺和用户规模分别记录。总览已核的 Kaolin 集成、Boba 更新、VoMP 下游例子可作为起点。
- 给出适合玩具、水果、软体等用户兴趣场景的继续选择建议。SoliDualSPHysics 的动态入口作为已有缺口记录，若进入具体材料选择再补。

完成时形成可供选择的比较结论，保留未证实项及其对选择的影响；新研究结果链接本票和原固体研究票，下一步由用户选定候选与场景。
