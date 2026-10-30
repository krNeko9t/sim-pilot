# 05：密度影响的展示场景与候选

研究截止：2026-10-30。对应[工单 05](../issues/05-density-demonstration-candidates.md)与[探索规格](../spec.md)，术语沿用 [GLOSSARY](../../../GLOSSARY.md)。本轮查阅作者论文、动态演示、官方文档与源码，覆盖 2024–2026 成果及经典可用项目；记录第一阶段候选，选定后再建立复跑工单。

**AI 推荐先看同球浮沉、两相翻转、方滴回球三组已有密度对照；公开复跑优先考虑 SPlisHSPlasH，明确理论参照优先考虑 Ferrite 梁振动。**浮沉最容易用日常经验理解；两相翻转和方滴回球补充最新多流体方法。论文视频、当前代码能力与后续预期分别记录。

## 观看导航

| 重点候选与时间 | 动态入口与观看说明 | 参数控制方式 | 密度演示证据 | 肉眼直观 / 理论参照价值 |
| --- | --- | --- | --- | --- |
| **1. Akinci 等刚液耦合，SIGGRAPH 2012；SPlisHSPlasH 当前公开路线** | [跳到 00:42][A-jump]、[完整 MP4][A-video]；**00:42–01:09**：同一球依次标出 1、10、100、500、50000 kg/m³；先观察浸入深度 | **直接输入物理量**：刚体ρ、流体ρ；质量由几何体积换算 | 原视频为**视频已展示参数对比**；当前公开例为**代码支持调参，效果待复跑** | **高 / 高**：浮沉容易辨认，静态排水量有解析参照 |
| **2. Primal SPH，2026-08-12** | [跳到 01:31][P-jump]、[完整 MP4][P-video]；**01:31–01:43**：看左、中四格 Ours 的 1:5、1:10、1:100、1:1000；**01:57–02:09** 看四相重排 | **直接输入物理量**：各相质量密度；程序入口待核 | **视频已展示参数对比** | **高 / 高**：翻转节奏明显；Rayleigh–Taylor（RT）有明确密度差驱动 |
| **3. Kinetic Simulation of Turbulent Multifluid Flows / MultiLBM，SIGGRAPH 2024** | [跳到 02:23][L-jump]、[完整 MP4][L-video]；**02:23–02:35**：同尺寸绿、红、蓝方滴回球；标签为液体与空气密度比 1000:100:25:1 | **直接输入物理量**：相密度；现有证据给比值，SI 缩放待核 | **视频已展示参数对比**；完整独立控制条件待补 | **高 / 中高**：轻滴更快回球；需同时说明表面张力及黏度条件 |
| **4. Analytical Buoyancy Integrator，SIGGRAPH Asia 2025 TC** | [作者视频][H-video]：找解析波面上的漂浮兔子，以及方块浮力稳定性比较；片段时间待定位 | **直接输入物理量**：算法中的流体ρ；刚体质量另给 | **仅有视觉效果参考**；论文有固定密度比的理论验证 | **中高 / 高**：漂浮生活感强，解析浮力便于解释 |
| **5. Ferrite.jl 梁振动，持续维护的官方教程** | [动态 WebP][F-video]、[教程][F-home]：看悬臂梁卸载后的往复振动；**位移放大 75 倍** | **直接输入物理量**：ρ=7850 kg/m³，进入质量矩阵 | **代码支持调参，效果待复跑**；现有动画为单组ρ | **中 / 高**：统一时间轴后可看频率差，具有精确比例参照 |

**动态核查深度。**本轮下载并抽帧实看 Akinci、Primal SPH、MultiLBM 的上述密度片段，核对标签与运动；Ferrite 动画已实看单梁振动。Hirae 仅核对作者视频入口及论文场景说明。备选 Newton XPBD 短片已抽帧看浮沉，其余备选以作者说明为准。视频播放时刻用于定位；计算耗时、播放速度与物理时间分别解释。

## 什么条件下ρ会产生可辨认的变化

ρ在本报告表示**质量密度**。ν始终表示**泊松比**；动力黏度记作 η（Pa·s），运动黏度记作 ν_kin（m²/s），满足 `ν_kin=η/ρ`。粒子采样密度、烟雾着色 density、密度约束误差需要按各自定义读取；例如 Primal SPH 的 κ属于体积约束系数。[SPlisHSPlasH 参数文档][S-format]、[Primal SPH 论文][P-paper]

| 展示场景 | 原始方程与 AI 推导 | 对照时固定什么、观察什么 |
| --- | --- | --- |
| **同形物体浮沉** | 原论文浮力为 `F_b=−ρ_f V_sub g`。**AI 推导**：均匀实心物体自由漂浮、达到静力平衡且未触底，明确排水体积并忽略毛细/流动载荷时，`V_sub/V=ρ_s/ρ_f` | 固定球体几何、流体ρ、重力及其余条件；观察浸入比例、上浮/下沉。中空或吸水物体用总质量与排水体积处理。[解析浮力论文][H-paper] |
| **固定外力或冲量下的惯性** | NASA 给出 `F=ma`；固定体积 `m=ρV`。**AI 推导**：相同净外力下 `a=F/(ρV)`；相同冲量下 `Δv=J/(ρV)` | 固定几何、力的大小/方向/持续时间、初态；优先观察无接触阻碍的短时速度变化。施加同一加速度或同一初速度属于另一种条件。[NASA][T-nasa] |
| **固定 E/ν的固体振动** | Ferrite 给出 `M ü+C u̇+K u=F` 与 `Kφ=ω²Mφ`。**AI 推导**：均匀ρ按比例缩放、几何/E/ν固定时，无阻尼自然频率 `f∝ρ^(−1/2)` | 固定初始形变、边界、阻尼策略、物理时间轴和播放倍率；观察振动周期。[Ferrite 教程][F-home] |
| **自重下垂与固定外载静态形变** | 原方程 `div σ+b=ρü`。**AI 推导**：静态固定外载、体力忽略时ρ退出方程；自重 `b=ρg` 则随ρ改变 | 要观察自重形变，应明确重力载荷并固定 E/ν、几何与约束；原 Ferrite 卸载例的体力忽略，需另设自重场景。[Ferrite 教程][F-home] |
| **纯重力自由落体** | NASA 的 `W=mg` 与 `F=ma` 给出 `a=g` | 相同初态、忽略阻力时下落轨迹重合。密度展示可转向受控外力、浮力或接触后的响应。[NASA][T-nasa] |
| **单相均匀密度的重力流动** | 从不可压牛顿动量方程作 **AI 推导**：`Du/Dt=−∇(p/ρ)+ν_kin∇²u+g`。均匀ρ、固定 ν_kin、相同初态及运动学边界，并排除表面张力、独立压差、多相和动态刚体反馈时，压力随ρ缩放可对应相同速度场 | 先声明固定 η还是 ν_kin；固定 η再改ρ会同时改变 ν_kin。多相、浮力和毛细场景提供额外力尺度，适合密度展示。[Consistent SPH §3.1][C-paper] |

以上比例关系是依据一手方程作出的推导，实际离散误差、接触、阻尼及大变形影响留给所选场景复跑。液体/刚体浮沉无需用有限 E/ν表达软体形变；Ferrite 则必须固定 E/ν。颜色、透明度和表面材质可帮助读图，参数控制方式归为**视觉调节**，与质量密度的输入分别记录。

## 1. 同球浮沉：Akinci 2012 与 SPlisHSPlasH

**已有动态对照。**Akinci 等的 *Versatile Rigid-Fluid Coupling for Incompressible SPH*（TOG / SIGGRAPH 2012）视频从约 00:39 显示 “Handling of different densities”。同一黄色球的标签依次为 **1、10、100、500、50000 kg/m³**；500 时约半浸。论文 §4 / Fig.9 确认该数列、半浸结论及 90K 流体粒子。展示采用**连续改参**，前一段状态会进入下一段；重球还受到浅容器底部限制。各次重置同一初态的独立实验需后续生成。[作者论文与项目][A-home]、[论文][A-paper]、[视频][A-video]

| 证据组 | ρ、固定条件与待核项 | 控制方式 / 演示证据 |
| --- | --- | --- |
| 2012 原片同球 | 上述五个 kg/m³ 数值，同一球体与水槽；流体粒子数固定。完整切换时间表、流体黏度与数值设置待核 | **直接输入物理量 / 视频已展示参数对比** |
| 当前 `DoubleDamBreakWithSphere.json` | 流体 `density0=1000`；球 `density=20`、缩放 (0.5,0.5,0.5)、初位置 (0,1,0)、初速度 (1,0,0)；g=(0,−9.81,0)，粒子半径 .025，Standard 运动黏度 .01，DFSPH、体积边界处理；其余沿用完整 JSON | **直接输入物理量 / 代码支持调参，效果待复跑**。该例包含溃坝冲击，宜观察同一初态后的完整浮沉过程。[场景][S-sphere] |
| 当前 `DoubleDamBreakMultiPhase.json` | 三相 `density0=1000/500/250`，Standard 运动黏度均 .01；不同流体块和发射条件 | **直接输入物理量 / 代码支持调参，效果待复跑**。用于多相能力证明；现有几何/初态另有差异。[场景][S-multi] |

**ρ语义与单位。**`FluidModel` 按代表体积乘 `density0` 赋粒子质量；刚体场景的 `density` 经 PositionBasedDynamics 的 `initBody` / 体积积分生成质量与惯量。两者均有质量密度的源码依据。2012 视频明确标 kg/m³；当前 JSON 的数值需与模型长度、质量、时间单位共同解释，采用 m/kg/s 时密度对应 kg/m³。[流体源码][S-fluid]、[场景解析][S-loader]、[刚体源码][S-rigid]、[参数文档][S-format]

**复跑条件。**代码：SPlisHSPlasH 为公开 C++/Python 项目，刚体耦合使用 PositionBasedDynamics；数据：仓库含 JSON、球体网格/粒子文件；商业仿真依赖：当前公开路线可独立求解；渲染：有粒子输出及 splashsurf、BlenderSequenceLoader 配套。2012 原实现使用 Bullet，论文成片使用 mental ray 3.9.4，该项属于渲染工具；原片完整场景资产的公开状态仍待核实。当前场景与原片各自保留来源和默认设置。[当前仓库][S-code]、[原论文][A-paper]

**AI 推荐：肉眼直观高，理论参照高，优先复跑。**可在选定后用同一球、相同初态，对球ρ作大跨度变化，例如 **200/500/1200** 对流体 1000（AI 提议数值，尚无对应结果）。固定流体ρ、几何、黏度、重力、边界、采样与渲染；观察浮/沉及最终浸入比例，瞬态另记录溃坝冲击和阻尼影响。原片约 06:00–06:20 的六球、两种流体也值得看；论文给两流体密度比 1:3，各球数值待核，作为补充视觉参考。[原论文][A-paper]

## 2. Primal SPH：高密度比两相翻转与四相重排

*Primal SPH Solver for Strongly Coupled Multiphase Simulations with High Density Ratios* 于 **2026-08-12** 正式发表。视频 **01:31–01:43** 左、中四格同为作者方法，标出轻:重 **1:5、1:10、1:100、1:1000**，展示重流体在上、轻流体在下的 RT 翻转。抽帧可见 1:5 的翻转较慢，1:100 与 1:1000 均较快；现有画面支持节奏差异，精确倍率需按物理时间量测。右列 DFSPH+DC 的 1:2 / 1:5 用于算法比较，爆散应归于数值失稳。[论文][P-paper]、[正式记录][P-home]、[视频][P-video]

**参数控制方式：直接输入物理量；演示证据：视频已展示参数对比。**论文 §4.3 的容器为 **6 m×4 m×2 m**，该节 κ=1500、步长 .1 ms；默认粒子半径 .025 m。两相绝对ρ、界面扰动、黏度与表面张力的逐组完整值待核。四相例的密度为 **1、10、100、1000 kg/m³**，视频约 01:47–01:55 给出每层密度相差十倍的说明，01:57–02:09 可看翻转与重排。[论文 §4][P-paper]

**复跑条件。**代码：论文表示计划集成到 SPlisHSPlasH，本轮未定位公开 PSPH 求解器分支；数据：演示视频已公开，完整场景文件待核；商业仿真依赖：作者完整执行环境待核；渲染：论文使用 splashsurf 重建表面，最终渲染工程待核。上述待核项与已有视频证据分别记录。[论文与补充材料][P-home]、[当前 SPlisHSPlasH][S-code]

**AI 推荐：肉眼直观高，理论参照高，优先观看的最新候选。**同一求解器四格对照直接体现密度比的作用，适合展示轻重流体交换位置。公开求解器与场景齐备后再评估复跑；洗衣机等复杂片段可作外观补充。

## 3. MultiLBM 2024：不同密度方滴回球

Li、Wu、Desbrun 的 *Kinetic Simulation of Turbulent Multifluid Flows* 为 **SIGGRAPH 2024 / TOG 43(4), 55**，机构记录发表于 **2024-07-19**。作者视频 **02:23–02:35** 的 “Cubic droplet deformation” 显示三种同尺寸方滴：**绿:红:蓝:空气=1000:100:25:1**。抽帧可见蓝滴最先接近球形，红滴次之，绿滴较慢；论文 Fig.22 同样解释了较轻液滴更快变圆。[作者项目][L-home]、[论文][L-paper]、[视频][L-video]、[发表记录][L-date]

**参数控制方式：直接输入物理量；演示证据：视频已展示参数对比。**ρ是各相质量密度，混合密度随相体积分数组合。现有场景使用密度比，绝对 kg/m³ 与物理尺度待核。Table 3 的三个液滴黏度值均 **.012**、空气 **.032**，依论文定义属于**运动黏度 ν_kin**；同一 ν_kin 下各滴动力黏度 η随ρ变化。流场/相场网格为 320×160×160 / 640×320×320。图中 “32.5 s/frame” 是计算耗时。[论文 §5、Fig.22、Table 3][L-paper]

视频显示相同立方初形，表中给出相同液滴 ν_kin；各相表面张力系数 **β_i** 的完整数值、初速度、重力处理与 SI 缩放仍待核。论文的 **σ_ij 表示相间可混溶性**，读取它时需与表面张力区分。故本项保留明确的ρ动态对照，同时把“完整条件下只改ρ”的复跑确认列为后续工作。[论文模型与参数表][L-paper]

**复跑条件。**代码：作者该项目页提供论文/视频，完整多流体求解器公开入口待核；数据：方滴及其他场景文件待核；商业仿真依赖、最终渲染工程：待核。作者另公开的 [OpenHOMELBM][L-openhome] 说明基于 2023 HOME-LBM 并加入 MuJoCo 耦合，范围与本篇多流体实现分别核对。

**AI 推荐：肉眼直观高，理论参照中高。**它提供浮沉之外的密度展示：毛细作用驱动回球，质量密度影响动态时间尺度。作为定量参照时，先补齐 β_i、单位及初态，再说明固定 ν_kin 的条件。视频中的瓶口咕噜流和混合液分层也有观看价值，但部分对照改变初始排列，宜作为视觉补充。[作者视频][L-video]、[论文][L-paper]

## 4. 解析浮力积分：波面上的漂浮物

Hirae、Morishima、Ando 的 *An Analytical Integrator for Solid-Fluid Coupled Buoyancy Forces* 为 **SIGGRAPH Asia 2025 Technical Communications 短文，2025-12-14 发表**。方法把近静水压力在浸水三角形上解析积分，得到浮力和力矩，可接受高度场及艺术控制的水面；作者 Fig.1 为解析波面上的兔子漂浮。适用范围主要是水面驱动刚体的浮力响应，完整双向水花动力学需另配流体求解。[正式记录][H-date]、[作者入口][H-home]、[论文][H-paper]

**参数控制方式：直接输入物理量；演示证据：仅有视觉效果参考。**论文算法明确输入流体ρ，刚体质量/惯量另给。方块验证固定流体:刚体密度比 **1:.75**，平静水面、时间步 1/60 s，理论平衡角 **26.565°**；对比关注三角网格积分、分辨率和阻尼策略。作者视频尚未实看密度标签，独立ρ动态对照待补。绝对单位与程序配置入口待核。[论文验证与算法][H-paper]、[视频][H-video]

**复跑条件。**代码：论文有算法描述，作者入口所列公开实现待核；数据：兔子/方块完整场景待核；商业仿真依赖、渲染工程：待核。**AI 推荐：肉眼直观中高，理论参照高。**适合作为漂浮物/简化波面候选及浮力公式参照；公开复跑起点优先采用第 1 组，获得实现后再比较该解析方法。

## 5. Ferrite：固定 E/ν的悬臂梁振动

官方 *Elastodynamics and modal analysis* 教程给出完整 Julia 程序和动画；所查稳定文档构建于 **2026-09-04**，这是文档时间，教程初始发表时间待核。模型为 **1 m×.05 m×.01 m** 的钢梁、左端固定，**E=210×10⁹ Pa、ν=.3、ρ=7850 kg/m³**；先以端面 −10 kPa 载荷求静态形变，再卸载释放，初速度为零且忽略体力。动画位移放大 **75 倍**。[教程与公式][F-home]、[完整脚本][F-code]、[动态 WebP][F-video]

**参数控制方式：直接输入物理量；演示证据：代码支持调参，效果待复跑。**源码以 `ρ(N_i·N_j)` 积分质量矩阵，E/ν决定弹性刚度，SI 单位明确。现有动画只显示一组ρ。**AI 提议**后续采用 ρ的 **.25×、1×、4×**，同几何和刚度下无阻尼频率预期 **2×、1×、.5×**；这些是人为参数变化，物料名称相应标作“同刚度、不同密度的梁”。[脚本][F-code]

**比较条件。**原例阻尼比 ζ=.02，Rayleigh 系数 α、β根据第 1、3 模态频率计算，ρ变化时系数也随之变化。后续可明确沿用该阻尼策略，或固定参考 α、β，分别解释结果。原例 `Δt=T₁/100`、300 步，覆盖各自三周期；密度对照应统一**物理时间窗和播放倍率**，才能显出频率差。初形变、放大倍率、网格与边界保持一致。[教程][F-home]

**复跑条件。**代码：Ferrite/Julia 完整脚本，配套 KrylovKit、WriteVTK、Plots；数据：梁网格程序化生成；商业仿真依赖：公开数值求解流程；渲染：可导出 VTK/PVD 给 ParaView，已有教程动画。**AI 推荐：肉眼直观中，理论参照高。**它最适合明确解释 E固定时ρ改变振动周期；自重下垂另需启用相应体力。动画标注放大倍率和真实物理时间，可降低误读。[官方教程][F-home]、[项目][F-repo]

## 同类候选与搜索边界

| 备选与一手动态入口 | 控制方式 / 密度证据、已知条件 | 代码、数据、商业仿真与渲染状态；AI 判断 |
| --- | --- | --- |
| **Adaptive Phase-Field-FLIP，SIGGRAPH 2025**：[作者页][Q-home]、[视频][Q-video]、[论文][Q-paper] | **论文直接输入物理量 / 仅有视觉效果参考**。RT 验证 Atwood 数 .9，对应重:轻=19:1（AI 代数换算）；两相 ν_kin=4×10⁻⁵ m²/s、g=9.81、L=.05 m、忽略表面张力；独立ρ扫值待核，视频未实看 | 公开 [MSBG][Q-code] 是数据结构、表面重建及 PDE 例；完整两相求解器、场景和商业依赖待核。论文自述重建/喷雾渲染路线。海浪观感高、RT 理论高；复跑顺位低于已知完整代码 |
| **Newton XPBD Fluid Support，开发者报告 2026-09-07/08**：[报告][N-home]、[10 秒浮沉片][N-video] | **直接输入物理量 / 仅有视觉效果参考**。`fluid_rest_density` 以 kg/m³计，例值 1000；物体ρ数列待核。实看不同尺寸/初态方块浮沉，画面未标ρ。`fluid_viscosity` 是每子步速度混合系数 [0,1]，SI 黏度映射待核 | 报告列 Newton/Warp 与实验分支，当前分支/完整场景公开状态待核；代码路线、数据和商业依赖随分支确认。原生 ViewerGL 表面预览已展示。观感高、受控理论参照待补 |
| **AnisoMPM / Ziran，SIGGRAPH 2020**：[作者页/视频][Z-home]、[密度批扫][Z-batch]、[torus 源码][Z-code] | **直接输入物理量 / 代码支持调参，效果待复跑**。环ρ=.75/1/2/3/5/10/20/25/30，E=100、ν=.3、fiberScale=100；绳 E=10、ν=.3、ρ=2，g=(0,−3,0)，max_dt=.001；单位缩放待核 | 公开代码、分项目 Data.zip 入口和 torus/rope 网格名；开放库求解，渲染工程待核。[论文 Fig.21][Z-paper] 的断裂涉及固定时间步设置下各向异性弹性的**数值失稳**，适合作数值压力测试；密度物理展示优先采用前述浮沉/振动 |
| **Mixed MPM / Newton，SIGGRAPH 2026**：[项目与视频][M-home]、[黏流体源码][M-code] | **直接输入物理量 / 代码支持调参，效果待复跑**。`mass=cell_volume*density`，默认ρ=1000 kg/m³、动力黏度 50 Pa·s；原视频 50/500 Pa·s 变化属于黏度对照 | Newton/Warp 公开，漏斗/粒子程序化生成，公开求解与查看器；表面/蜂蜜材质工程待配。观感高、ρ独立归因中：固定 η改ρ也会改变 ν_kin。可复用 [04 研究](04-viscous-fluid-candidates.md)，浮沉更易解释 |
| **Volume-Preserving LBM-MPM Coupling for Air-Water-Sand Mixtures，SIGGRAPH 2026**：[作者论文目录][W-home]、[论文][W-paper]、[MP4][W-video] | **目标控制方式待核 / 仅有视觉效果参考**。作者范围含空气、水、沙的侵蚀、输运与沉降；具体ρ数列、砂粒材料ρ与整体体积平均ρ的定义、固定条件和单位待核；视频未实看 | 代码、场景、商业仿真依赖与渲染工程待核。肉眼丰富、理论条件较多；保留湿沙/沉降方向，后续有明确ρ条件再提升顺位 |
| **Consistent SPH Rigid-Fluid Coupling，VMV 2023**：[作者页与视频][C-home]、[论文][C-paper] | **直接输入物理量 / 代码支持调参，效果待复跑**。支持轻、重刚体；作者在项目讨论确认方法进入 DFSPH。独立ρ视频片段、完整数列待核 | SPlisHSPlasH 公开路线；具体论文场景资产/成片渲染待核。作为第 1 组同类算法备选，不重复计作独立密度证据。[作者实现说明][C-implementation] |

搜索沿浮沉/刚液双向耦合、高密度比多相与 RT、毛细惯性、固定外力惯性、固体振动/自重、颗粒和黏性场景展开；论文与源码记录截止日均为本报告日期。旧候选按当前可用资产与理论价值保留，最新候选按已公开证据确定顺位。

固定外力/冲量的惯性有清楚的物理依据，本轮尚未定位比上述三组更完整的作者ρ动态对照，作为**待复跑的场景设计方向**记录。干沙坍塌与单相重力流动需要先确认质量密度确实进入所观察的独立力尺度；烟雾渲染密度、粒子数量与颜色变化按各自控制含义处理。对 Mantaflow、Jet 等常见液体路线，本轮沿用 [04 研究](04-viscous-fluid-candidates.md) 已查的接口边界，独立质量密度入口与合适动态对照仍待核。

## 面向选择的 AI 推荐

| 选择目的 | 推荐与理由 | 交给复跑工单的关键条件 |
| --- | --- | --- |
| **最直观的ρ演示** | Akinci 同球浮沉；用 SPlisHSPlasH 公开例承接 | 同几何、同初态、同一流体ρ；记录完整 JSON、浸入比例及浅槽接触影响 |
| **看最新的已有ρ对照** | Primal SPH 2026 与 MultiLBM 2024 | 前者只比较 Ours 四格；后者明确固定 ν_kin，并补 β_i 与单位。代码可用性逐项确认 |
| **明确理论参照** | Ferrite 梁振动；静态浮力作为另一组 | 固定 E/ν，统一物理时间、阻尼策略和位移放大倍率；检查预期频率比例 |
| **日常漂浮物与波面** | 解析浮力积分 | 获取实现/资产，明确ρ_f、刚体质量/排水体积及水面运动条件 |
| **扩展到惯性、自重、颗粒、蜂蜜** | 先采用上述方程选择可辨认观测量，再复用公开固体/MPM 代码 | 外力与加速度分清；自重写出ρg；明确 η或 ν_kin；颗粒材料ρ与堆积平均ρ分清 |

本轮交付为候选、观看依据和控制条件；用户审阅选中后，再补齐所选场景的待核项并复跑。现阶段推荐不依赖安装、运行模拟或生成新的对照视频。

## 一手来源

[A-home]: https://cgl.ethz.ch/publications/papers/paperSol12.php
[A-paper]: https://cgl.ethz.ch/Downloads/Publications/Papers/2012/Sol12/Sol12.pdf
[A-video]: https://cgl.ethz.ch/Downloads/Publications/PaperVideos/2012/Sol12-Siggraph2012-VersatileRigidFluidCouplingForIncompressibleSPH.mp4
[A-jump]: https://cgl.ethz.ch/Downloads/Publications/PaperVideos/2012/Sol12-Siggraph2012-VersatileRigidFluidCouplingForIncompressibleSPH.mp4#t=42
[S-code]: https://github.com/InteractiveComputerGraphics/SPlisHSPlasH
[S-format]: https://github.com/InteractiveComputerGraphics/SPlisHSPlasH/blob/master/doc/file_format.md
[S-sphere]: https://github.com/InteractiveComputerGraphics/SPlisHSPlasH/blob/master/data/Scenes/DoubleDamBreakWithSphere.json
[S-multi]: https://github.com/InteractiveComputerGraphics/SPlisHSPlasH/blob/master/data/Scenes/DoubleDamBreakMultiPhase.json
[S-fluid]: https://github.com/InteractiveComputerGraphics/SPlisHSPlasH/blob/master/SPlisHSPlasH/FluidModel.cpp
[S-loader]: https://github.com/InteractiveComputerGraphics/PositionBasedDynamics/blob/master/Utils/SceneLoader.cpp
[S-rigid]: https://github.com/InteractiveComputerGraphics/PositionBasedDynamics/blob/master/Simulation/RigidBody.h
[P-home]: https://diglib.eg.org/items/48169c40-2d31-4fdb-95a0-613a825148f4
[P-paper]: https://onlinelibrary.wiley.com/doi/full/10.1111/cgf.70559
[P-video]: https://diglib.eg.org/bitstreams/8f8e680b-0eee-472e-a30f-403df48208a6/download
[P-jump]: https://diglib.eg.org/bitstreams/8f8e680b-0eee-472e-a30f-403df48208a6/download#t=91
[L-home]: https://kuiwuchn.github.io/multilbm.html
[L-paper]: https://geometry.caltech.edu/pubs/LWD24.pdf
[L-video]: https://geometry-1.cms.caltech.edu/Movies/KineticSimMultiFluids.mp4
[L-jump]: https://geometry-1.cms.caltech.edu/Movies/KineticSimMultiFluids.mp4#t=143
[L-date]: https://researchportal.ip-paris.fr/en/publications/kinetic-simulation-of-turbulent-multifluid-flows/
[L-openhome]: https://github.com/kuiwuchn/OpenHOMELBM
[H-home]: https://sites.google.com/view/harukahirae/
[H-paper]: https://drive.google.com/file/d/10sX-y4kx_lSc0-Ako-DjzHNw1RkbkTcN/view?usp=sharing
[H-video]: https://www.youtube.com/watch?v=wo86RbDOTHc
[H-date]: https://waseda.elsevierpure.com/en/publications/an-analytical-integrator-for-solid-fluid-coupled-buoyancy-forces/
[F-home]: https://ferrite-fem.github.io/Ferrite.jl/stable/tutorials/elastodynamics/
[F-video]: https://ferrite-fem.github.io/Ferrite.jl/stable/tutorials/elastodynamics-light.webp
[F-code]: https://ferrite-fem.github.io/Ferrite.jl/stable/tutorials/elastodynamics/#Plain-program
[F-repo]: https://github.com/Ferrite-FEM/Ferrite.jl
[T-nasa]: https://www1.grc.nasa.gov/beginners-guide-to-aeronautics/free-falling-objects/
[Q-home]: https://ge.in.tum.de/2025/06/04/adaptive-phase-field-flip-for-very-large-scale-two-phase-fluid-simulation/
[Q-paper]: https://ge.in.tum.de/download/Adaptive_Phase_Field_FLIP_preprint.pdf
[Q-video]: https://www.youtube.com/watch?v=nt9BohngvoE
[Q-code]: https://github.com/tum-pbs/MSBG
[N-home]: https://reports.eric-heiden.com/newton-fluid-support/
[N-video]: https://reports.eric-heiden.com/newton-fluid-support/assets/interactive-tank.mp4
[Z-home]: https://joshuahwolper.com/anisompm
[Z-paper]: https://dl.acm.org/doi/pdf/10.1145/3386569.3392428
[Z-batch]: https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/anisofractureBatch3D.py
[Z-code]: https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/examples/hangingTorus.h
[M-home]: https://research.nvidia.com/labs/prl/mixed_mpm/
[M-code]: https://github.com/newton-physics/newton/blob/main/newton/examples/mpm/example_mpm_viscous.py
[W-home]: https://geometry-1.cms.caltech.edu/pubs.html
[W-paper]: https://geometry-1.cms.caltech.edu/pubs/XWYDL26.pdf
[W-video]: https://geometry-1.cms.caltech.edu/Movies/XWYDL26.mp4
[C-home]: https://srjeske.de/publications/2023-vmv-sph-boundary-handling/
[C-paper]: https://srjeske.de/publications/2023-vmv-sph-boundary-handling/BWJ23.pdf
[C-implementation]: https://github.com/InteractiveComputerGraphics/SPlisHSPlasH/discussions/328
