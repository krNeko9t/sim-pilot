# 04：黏性流体候选与动态演示

研究截止：2026-10-30。对应[工单 04](../issues/04-viscous-fluid-candidates.md)与[探索规格](../spec.md)。本轮查阅作者论文、作者动态演示、官方文档与公开代码，覆盖 2024–2026 公开成果并保留经典项目；术语沿用 [GLOSSARY](../../../GLOSSARY.md)。记录原作者结果与后续候选，复跑原型在选定后开展。

**AI 推荐先看 Mixed MPM 的黏度对照，再看 SPlisHSPlasH 的卷曲演示。**前者有清楚的 50/500 Pa·s 标签、固定条件和公开漏斗示例；后者有现成高黏度场景及明确的运动黏度入口。需要蜂蜜浇甜甜圈的成片观感时看 SCA 2024；需要 Blender 内完成仿真和渲染时保留 Mantaflow。目标物理量中，ρ有适合继续核实的代码入口；普通牛顿蜂蜜的主要外观控制量是附加输入黏度，E、ν应按材料模型分别判断。

## 观看导航

| 候选与时间 | 动态入口 | 值得观看的场景或片段 | 参数控制方式 | 演示证据与用途 |
| --- | --- | --- | --- | --- |
| **1. Mixed MPM / Newton，SIGGRAPH 2026** | [参数视频 MP4][M-video]、[作者项目页][M-home] | **01:04–01:20**：黏性流落地盘卷；01:08–01:11 为 50 Pa·s，01:13–01:19 为 500 Pa·s | **直接输入物理量**：动力黏度、ρ；E/ν另属弹性分支 | 黏度为**视频已展示参数对比**；ρ为**代码支持调参，效果待复跑**。优先看、优先复跑候选 |
| **2. SPlisHSPlasH / Weiler 等，EG 2018，项目持续更新** | [官方 Gallery][S-gallery] 中 “A Physically Consistent Implicit Viscosity Solver for SPH Fluids” | 看流柱落地卷曲、薄片折叠、搅拌与黏性模型变形；卷曲对应论文 Fig.6 | **直接输入物理量**：ρ、运动黏度；动力黏度需**公式换算** | **代码支持调参，效果待复跑**；现有视频用于看运动，独立目标参数动态对照待核实 |
| **3. Variational Stokes，SIGGRAPH 2017** | [作者链接的完整视频][V-video]、[补充 MP4][V-extra] | 看蜂蜜/糖浆状细流盘卷，以及分裂求解和耦合求解的自由表面对照 | 黏度为**直接输入物理量**；ρ具体设置入口待核实 | **仅有视觉效果参考**；公开实现依赖 Houdini，保留算法与外观参考 |
| **4. Blender / Mantaflow，2.92 引入高黏度求解器，2021 至今** | [开发者 Sebastian Barschkis 演示][B-video] | 看高黏度流体坍塌、铺展与堆积；结合手册区分 Diffusion 和 High Viscosity Solver | Diffusion 为物理运动黏度；高黏度 Strength 暂按**视觉调节** | **代码支持调参，效果待复跑**；适合渲染一体化候选 |
| **5. Jet / CubbyFlow，2017 方法及后续项目** | [作者 Examples 嵌入视频页][J-examples]、[视频 1][J-video1]、[视频 2][J-video2] | 从液体形状坍塌判断流动速度与形状保持；高/零黏度兔子见同页图片，具体动态片段待定位 | 黏度系数可直接设；SI 尺度需核实，暂按**视觉调节**记录 | **代码支持调参，效果待复跑**；轻量网格流体备选 |
| **6. Multiphase Viscoelastic Non-Newtonian Fluid Simulation，SCA 2024** | [作者 B 站视频][N-video]、[EG 记录中的 paper1038.mp4][N-eg] | 找蜂蜜浇甜甜圈、三块流体落斜板、菱形/球黏弹对照与叉子划过 “SCA2024” | 论文直接给流变参数；ρ为各相输入，代码入口待核实 | 当前为**仅有视觉效果参考**；论文另有黏度/松弛时间对照。适合先看黏弹与非牛顿范围 |
| **7. Nonlocal Unified Variational Framework / PeriDyno，SIGGRAPH 2026** | [ACM 论文页][U-doi] → Supplemental Material → Demo（MP4，167.84 MB）；[作者页][U-home] | 找 molten sugar 与 pitch 的盘卷，以及黏度和表面张力共同作用 | 模型黏性系数直接可调；SI 映射待核实，暂按**视觉调节**记录 | **代码支持调参，效果待复跑**；最新公开实现候选 |

视频核查深度：Mixed MPM 参数视频已抽帧核对标签、时间与盘卷形态，并与论文参数表核对。其余已核对作者动态入口及论文/页面场景说明，尚未实看全部视频；表中按场景给观看指引，时间与动态参数标签留待确认。Jet README 的高/低黏度兔子是 **PNG 静态图**。ACM 与 B 站抓取存在访问限制，可从上列作者背书入口打开；入口可用性和完整片段观看情况分别记录。

## 目标物理量与黏度的关系

本报告的 **ν始终表示泊松比**。动力黏度写作 η，单位 Pa·s；运动黏度写作 ν_kin，单位 m²/s，满足 `ν_kin = η / ρ`。原论文常用 μ、ν等字母表示黏度，读取数值时以该文定义为准。特别是 SPlisHSPlasH 的物理黏度入口和 Blender Diffusion 接收运动黏度；Newton 漏斗例接收 Pa·s。[SPlisHSPlasH 参数文档][S-format]、[Blender 手册][B-manual]、[Newton API][M-api]

蜂蜜需先固定物态与温度：实验研究中的液化、均匀蜂蜜可表现为牛顿流体，其黏度随温度显著变化；结晶或膏状（creamed）蜂蜜可出现剪切变稀和黏弹性。因此，**高黏度、剪切相关黏度、黏弹性是不同建模范围**。本轮以固定温度的牛顿高黏度蜂蜜作为简单候选，另保留黏弹/非牛顿场景；盘卷或拉丝外观本身不足以判定需要弹性模型。[蜂蜜流变实验][H-rheology]、[结晶蜂蜜实验，2025][H-crystal]

E、ν描述弹性响应。牛顿不可压液体的候选通常没有有限 E/ν输入；框架支持弹性材料，并不自动意味着该蜂蜜场景用 E/ν控制。黏弹模型还需要松弛时间、黏性分支等信息，具体论文的松弛参数应按自身模型解释。[Mixed MPM §8][M-paper]、[SCA 2024 模型][N-paper]

ρ具有物理意义，但外观可辨识性取决于实验。对单相、不可压流体，固定运动黏度时改变均匀ρ，部分重力流动的速度场可能保持相似；固定动力黏度再改ρ，则运动黏度同步变化。这是依据候选方程作出的判断。后续密度对照应声明固定的是哪种黏度，观察整体铺展、受力响应或有独立力尺度的场景，避免仅凭自由落体快慢推断ρ。[Weiler 等论文][S-paper]、[Newton 黏流体示例][M-example]

| 候选 | E 的适用性与入口 | ν（泊松比）的适用性与入口 | ρ的适用性与入口 |
| --- | --- | --- | --- |
| Mixed MPM / Newton | API 有 `mpm:young_modulus`；黏流体论文采用 E=∞的不可压模式，有限 E 对照需另设模型条件 | API 有 `mpm:poisson_ratio`，用于弹性；黏流体参数表该栏空白 | 示例 `density`，按粒子体积乘密度赋质量，kg/m³ |
| SPlisHSPlasH / Weiler | 所选流体黏性模型无 E；项目其他弹性模块另计 | 所选流体黏性模型无泊松比输入 | `density0` 为静止密度；当前卷曲例 1000 |
| Variational Stokes | 不可压牛顿液体，无 E 输入 | 无泊松比输入 | 模型包含密度；公开 Houdini 节点的独立入口待核实 |
| Blender / Mantaflow 液体 | 所查液体面板/API无 E 入口 | 所查液体面板/API无泊松比入口 | 所查液体 API 未确认独立质量密度入口；烟雾 density 字段另有用途 |
| Jet / CubbyFlow 网格液体 | 所选 GridFluidSolver 无 E 入口 | 无泊松比入口 | 所查网格求解器未确认独立质量密度入口；其他 SPH 求解器另计 |
| SCA 2024 多相黏弹 | 模型以构形张量及松弛时间表达弹性；E 映射待核实 | 原模型未给泊松比输入或映射 | 各相静止密度 ρₖ⁰；混合密度按体积分数加权；程序入口待核实 |
| NUV / PeriDyno | 当前液体例调体积/剪切黏性，E 入口待核实 | 当前液体例无已确认泊松比入口 | 当前例未找到独立质量密度设置；`Rho_Lambda` 变量实际传入 kappa，不作为ρ证据 |

表中依据分别见各节源码/文档。后续只改变选定的目标物理量或附加输入，其余沿用**所选同一场景**默认值；至少记录长度尺度、重力、发射条件、边界、表面张力、离散分辨率、时间步与渲染。原论文与当前代码各自成组，避免把不同来源拼成“原作者默认值”。

## 1. Mixed Material Point Methods for Stiff Elastoplasticity / Newton

**已核实。**作者将工作列为 SIGGRAPH 2026，并公开 Newton 实现。参数视频中，50 Pa·s 的流体落地后形成较低、较宽的盘卷和铺展；500 Pa·s 形成较高、较窄的盘卷。它是本轮最明确的黏度动态对照。[项目与视频][M-home]、[参数 MP4][M-video]

| 来源 | 实际改变 | 已列固定条件 |
| --- | --- | --- |
| 论文 Fig.18、Table 5；视频约 01:04–01:20 | 塑性黏度 ηₚ=50 / 500 Pa·s | ρ=1000 kg/m³；g=9.8；E=∞；摩擦系数=0；p_c=∞；β=1；τ_c=0；θ=0；dx=0.005 m；Q1-dP1-S2；每格 3³粒子。ν和弹性黏度栏空白 |
| 当前 `example_mpm_viscous.py`，单组默认例 | 默认黏度 50 Pa·s，density=1000 | 漏斗孔径 0.02 m、高 0.2 m、底高 0.2 m；dx=0.005；重力 (0,0,−10)；240 fps、每帧 1 子步；地面摩擦 0.5、漏斗摩擦 0；strain basis=P0 |

论文对照与当前默认例的基函数、重力等设定不同，分别用于理解已有证据与后续复跑起点。论文 §8 明确用 E=∞去除弹性柔度；有限 E/ν的框架能力适用于另一组弹性条件。官方 API 分别标注 Young 模量、弹性泊松比及 Pa·s 黏度，支持把其物理语义分开。[论文][M-paper]、[API][M-api]、[示例源码][M-example]

**参数控制方式：直接输入物理量。演示证据：黏度为视频已展示参数对比，ρ为代码支持调参，效果待复跑。**ρ经 `mass=cell_volume*density` 进入质量；改变ρ时固定黏度输入即可保持动力黏度，适合后续检验目标物理量的展示价值。[示例源码][M-example]

**复跑条件。**代码：Newton/Warp 公开；示例数据：漏斗和粒子程序化生成；商业仿真依赖：该例走公开求解流程；渲染：当前例调用查看器 `log_state` 显示粒子，Newton 支持可视化/输出，蜂蜜表面重建和最终材质需后续配套。论文成片资产的完整公开程度待核实。[Newton 仓库][M-code]、[示例源码][M-example]

**AI 推荐。**首先观看已有黏度对照；若进入复跑，先采用同一漏斗的默认参数核实ρ效果。有限 E/ν的流体展示需要先选定黏弹模型，另列弹性与黏性条件。

## 2. SPlisHSPlasH / A Physically Consistent Implicit Viscosity Solver for SPH Fluids

**已核实。**Weiler、Koschier、Brand、Bender 的 EG 2018 方法提供物理黏度求解，作者演示包括卷曲和折叠。SPlisHSPlasH 当前公开场景含 `CoilingModel_Weiler2018.json` 与 `ViscousBunny.json`，适合从简单细流或黏性兔子开始。[论文][S-paper]、[官方 Gallery][S-gallery]、[卷曲场景][S-coil]、[兔子场景][S-bunny]

当前源码文档将 Weiler 方法列为 `viscosityMethod=6`，`viscosity` 为运动黏度；Standard 与改进 Takahashi 亦采用运动黏度。Bender/Peer 若干方法使用 [0,1] 系数，XSPH 平滑又有独立参数，选择方法时应同时记录名称与版本编号。项目早期版本的编号不同。[参数文档][S-format]

| 公开场景 | ρ与黏度原始输入 | 主要固定条件 |
| --- | --- | --- |
| CoilingModel_Weiler2018 | `density0=1000`；`viscosity=5` m²/s；对应 η=5000 Pa·s | `viscosityBoundary=4`；粒子半径 0.025；初始步长 0.001；DFSPH；g=(0.1,−9.81,0.02)；流柱 y 从 0.01 到 12；黏性迭代上限 200、误差 0.05 |
| ViscousBunny | `density0=1000`；`viscosity=1` m²/s | `viscosityBoundary=1`；文件指定 bunny.bgeo、Dragon_50k.obj 与地面；以完整 JSON 作为该例条件 |

上述是两个独立默认场景。论文卷曲表另报动力黏度 4000 Pa·s，和当前 JSON 数值不同。作者在 honey 参数讨论中指出，卷曲例的流柱高达 12 m，视觉结果需要同时考虑几何尺度；把日常小勺蜂蜜的黏度直接填进该大场景，外观可能差异明显。[论文][S-paper]、[作者讨论 #240][S-scale]

**参数控制方式：ρ、运动黏度为直接输入物理量，η经公式换算。演示证据：代码支持调参，效果待复跑。**后续若只改ρ并固定动力黏度η，应按新ρ重算运动黏度；若固定 JSON 中的运动黏度，需明确动力黏度随之改变。官方视频的目标参数独立对照片段及完整固定条件待核实。[参数文档][S-format]

**复跑条件。**代码：公开 C++/Python 项目；示例数据：JSON 与相关模型位于仓库；商业仿真依赖：公开求解流程；渲染：支持粒子导出，官方列出 splashsurf、BlenderSequenceLoader 等配套。作者成片的完整渲染工程及部分特定场景仍需逐个核实。[仓库][S-code]

**AI 推荐。**和 Mixed MPM 并列优先复跑候选；卷曲场景便于看黏度影响，ρ的展示需要严守动力/运动黏度选择。标准 Weiler 场景按牛顿黏性解释，剪切变稀、黏弹性另查对应模型。

## 3. Variational Stokes: A Unified Pressure-Viscosity Solver for Accurate Viscous Liquids

**已核实。**Larionov、Batty、Bridson 的 SIGGRAPH 2017 工作把压力与黏度耦合求解，展示糖浆/蜂蜜状盘卷及自由表面行为。作者视频适合比较分裂求解造成的差异；这种算法对照应与材料参数对照分开。[作者项目页][V-home]、[作者视频][V-video]

**参数控制方式：黏度直接输入物理量；演示证据：仅有视觉效果参考。**公开插件 README 明确 `Min Viscosity` 接收动力黏度，和 Houdini `GasViscosity` 的运动黏度有别。模型是不可压牛顿液体，E/ν没有对应输入；ρ独立节点入口、视频中的黏度数列及其他固定条件待核实。[公开实现][V-code]

**复跑条件。**代码：公开 `stokes-houdini`；示例数据：有 examples 和构建/输出流程，README 提醒仅 piling armadillos 示例已知可用；商业仿真依赖：编译和运行需 Houdini SDK/环境；渲染：依托 Houdini 示例管线。按本探索的商业仿真约束，将它列为视觉和算法参考。[公开实现][V-code]

**AI 推荐。**看落地盘卷与自由表面是否自然，帮助评判其他候选；直接复跑优先采用前两项公开独立求解器。

## 4. Blender / Mantaflow

**已核实。**Blender 2.92 于 2021 年引入新的高黏度求解器，开发者提供动态演示。Blender 液体设置同时保留 Diffusion 与 High Viscosity Solver，两组参数需分别解释。[2.92 发布说明][B-release]、[开发者视频][B-video]

| 控制 | 官方含义与参数控制方式 | 证据边界 |
| --- | --- | --- |
| Diffusion：Base、Exponent | 运动黏度 `Base×10^(−Exponent)`，m²/s；默认 1×10⁻⁶。动力黏度经 `η/ρ` 换算 | 物理单位明确；场景长度尺度仍影响结果 |
| High Viscosity Solver：Strength | `viscosity_value` 默认 0.05；官方 API 范围 [0,10]。本轮按**视觉调节**记录 | 手册未提供明确 SI 映射；0 仍应用黏性，关闭该求解器才关闭此项 |

依据为[官方手册][B-manual]、[API][B-api]与[流体脚本][B-fluid-script]。代码将两条路径分别作为 `kinViscosity` 和高黏度 `applyViscosity` 输入；复跑选择其一后记录完整配置，避免仅凭同名 viscosity 交换数值。[液体脚本][B-liquid-script]

**演示证据：代码支持调参，效果待复跑。**官方手册有 Strength=0.2（frame 65）与 0.4（frame 200）示图，时间不同，适合作参数功能说明；同一时刻、仅改 Strength 的动态证据本轮待核实。E、ν及独立液体ρ入口见前表。[官方手册][B-manual]

**复跑条件。**代码：Blender/Mantaflow 公开；示例数据：开发者视频对应完整 .blend 工程的下载状态待核实，可用基础发射/容器场景；商业仿真依赖：公开 Blender 流程；渲染：液面网格、Cycles/Eevee 在同一软件内。当前版本下高黏度与 Diffusion 组合的具体行为留到选中后核对。[发布说明][B-release]、[官方液体文档][B-manual]

**AI 推荐。**适合优先产出容易观看的蜂蜜材质成片；目标ρ展示目前依据较弱，E/ν也应转向有明确本构入口的候选。

## 5. Jet / CubbyFlow

**已核实。**Doyub Kim 的 Fluid Engine Development（2017）配套 Jet 提供 C++/Python 流体求解，作者 Examples 页含嵌入视频与高/零黏度兔子图。该兔子图列出 200³网格、半拉格朗日平流与水平集处理条件，上排高黏度具体数值未给出，下排为零黏度。[作者 Examples][J-examples]、[作者仓库][J-code]

所选 `GridFluidSolver3` 有 `setViscosityCoefficient`，非正值截为 0，并把系数交给网格扩散求解器。由扩散方程的维度可推断其作用类似运动黏度，但所查 API 未给完整 SI/场景缩放约定，故本轮暂按**视觉调节**记录，后续确认单位后再归为直接输入物理量。E/ν及独立ρ入口见前表。[头文件][J-header]、[实现][J-source]

**演示证据：代码支持调参，效果待复跑。**作者视频入口可看运动；本轮尚未确认高/零黏度兔子在视频中的片段及固定条件。PNG 的形状差异提供静态参考，不升级为已验证的动态黏度对照。[作者 Examples][J-examples]

**复跑条件。**代码：Jet 公开，含示例及 Python API；示例数据：示例代码和作者 `fluid-engine-dev-demo` 仓库提供渲染相关材料，具体兔子例的文件齐备程度待核实；商业仿真依赖：公开求解流程；渲染：作者 demo 提供 Mitsuba 场景。CubbyFlow 是基于 Jet 的衍生项目，支持 C++/Python 及可选 CUDA，作为同组备选，版本兼容性需选中后核实。[Jet 仓库][J-code]、[渲染仓库][J-demo]、[CubbyFlow][J-cubby]

**AI 推荐。**适合把同一液体模型的坍塌、流动与形状保持作为简单视觉场景；若优先看ρ的物理控制，先选 Newton/SPlisHSPlasH。

## 6. Multiphase Viscoelastic Non-Newtonian Fluid Simulation

**已核实。**Zhang 等的 SCA 2024 / CGF 43(8) 工作以 SPH、多相混合及构形张量处理牛顿、剪切变稀、剪切增稠和黏弹响应。蜂蜜浇甜甜圈对应 Fig.9；作者论文目录直接链接 B 站视频。[EG 官方记录][N-eg]、[作者论文目录][N-author]、[作者视频][N-video]

| 论文已有对照 | 实际改变 | 明确固定与待核条件 |
| --- | --- | --- |
| Fig.6 上排菱形 | η₀、η_max 在 [1,15] 范围变化 | 松弛时间 λ相同；α₀=0、C_d⁰=1；除小球外步长 1.5 ms。逐个对象的完整数值、密度与几何条件待核实 |
| Fig.6 下排前三个菱形 | 松弛时间 λ在 [0.005,0.3] 范围变化 | 黏度相同；其余已列条件同上；λ按松弛时间解释 |

论文 Table 3 把黏度列为 m²/s，Fig.11 则以 Pa·s 标高黏度比混合；后续需核对不同段落的单位映射，表中范围保留原数值。ρ按相密度加权；E/ν与这些流变系数的对应关系未给出。Fig.7 三块流体落斜板有助于区分剪切变稀/增稠的运动。[论文 §3–6、Figs.6–11][N-paper]

**参数控制方式：论文直接设置流变物理量及模型系数，程序入口待核实。演示证据：仅有视觉效果参考，另有论文参数对照。**作者视频尚未实看参数标签；黏度、松弛时间、相比例、扩散和拖曳应各自记录，不能用一个“蜂蜜黏度”概括。[论文][N-paper]

**复跑条件。**代码：作者当前条目列论文和视频，公开实现待核实；示例数据：甜甜圈等场景下载待核实；商业仿真依赖：待核实；渲染配套：待核实。论文报告 CUDA 实现，这只能说明作者执行方式。[作者目录][N-author]、[论文][N-paper]

**AI 推荐。**优先作为蜂蜜成片及流变差异的观看参考；拿到代码与单位约定后再评估复跑。它补充普通高黏度牛顿流体的范围，有限 E/ν的展示仍需独立映射依据。

## 7. A Nonlocal Unified Variational Framework for Free Surface Flows / PeriDyno

**已核实。**SIGGRAPH 2026 作者最终题目如上。方法将不可压性、黏度、表面张力放进同一优化问题，分开处理体积黏性与剪切黏性；公开实现位于 PeriDyno `examples/Cuda/UnifiedFluid`。[作者页][U-home]、[论文及代码目录][U-publications]、[代码][U-code]

论文 Fig.12 比较 molten sugar（λ=400、μ=0.1）与 pitch（λ=300、μ=100），**两项系数同时改变**；适合看外观范围，独立黏度归因需另做对照。这里的λ、μ属于黏性模型，不能直接套用弹性 Lamé 系数与 E/ν的换算。文中将黏弹性列为未来工作，并讨论剪切项对旋转/角动量的影响。[论文][U-paper]

`Qt_Viscosity/main.cu` 提供程序化例：lambda=2000、mu=0、gamma=0、kappa=1；重力 (0,−9,0)、发射速度 2、采样距离 0.005、迭代次数 30。变量 `Rho_Lambda` 使用的是 kappa，按命名猜测ρ会误读该例。SI 单位和独立ρ入口仍待核实。[示例源码][U-example]

**参数控制方式：模型系数可直接调，暂按视觉调节记录。演示证据：代码支持调参，效果待复跑。**ACM 的 Supplemental Material 列出 Demo MP4；本轮受访问限制，尚未实看具体标签和时间。当前源码默认值与论文糖浆/沥青参数各自保留。[ACM 页面][U-doi]

**复跑条件。**代码：PeriDyno 公开 CUDA 实现；示例数据：程序化黏性例与表面张力例；商业仿真依赖：公开项目流程；渲染：Qt/OpenGL 预览，作者成片表面与渲染工程待核实。仓库说明 CUDA 12+，Windows 测试充分，Linux 兼容性需选中后核实。[仓库说明][U-repo]

**AI 推荐。**作为最新公开方法保留；先看已有盘卷，再核实物理单位及ρ入口，优先级排在已有 SI 参数与视频对照的前两项之后。

## 其他值得保留的候选

| 候选 | 原始来源、覆盖与动态入口 | 参数控制方式 / 演示证据 | 代码、数据、商业依赖与渲染判断 |
| --- | --- | --- | --- |
| **A Unified Viscoelastic Solver for Multiphase Fluid Simulation Based on a Mixture Model，TVCG 2026** | [作者目录][N-author]、[大学作者成果记录][A-2026]；2026-03 在线发表，使用多模态构形张量处理多相黏弹流体 | **控制方式待核实；动态证据待补**。E/ν映射、ρ入口及附加流变参数的具体控制均待核实 | 代码、场景数据、商业仿真依赖及渲染配套均待核实。作为最新论文线索保留 |
| **A Multi-Scale Model for Simulating Liquid-Hair Interactions / libWetHair，2017** | [作者仓库及演示][A-wethair]：液体在毛发间捕获、流动、滴落 | **目标参数控制方式待核实；仅有视觉效果参考**。毛发弹性与液体黏度分属两部分；E/ν/ρ各自的实际入口与对照待核实 | 公开代码与 XML 例子，含 OpenGL/PNG 输出；Houdini 文件用于表面重建/渲染，替代后处理路线待核实。适合扩展涂覆场景 |
| **A Multi-Scale Model for Coupling Strands with Shear-Dependent Liquid / CreamyStrand，SIGGRAPH 2019** | [作者项目页含 Video/MP4 与代码][A-creamy]；奶油、巧克力、涂料与束状物耦合 | **目标参数控制方式待核实；仅有视觉效果参考**。剪切相关黏弹塑性模型的 E/ν映射、ρ入口和流变系数对照待核实 | 有公开实现入口；所选场景资产、商业仿真依赖和渲染工程待核实。保留涂覆、拉丝，场景复杂度高于简单蜂蜜 |
| **Accurate Viscous Free Surfaces for Buckling, Coiling, and Rotating Liquids，2008** | [作者机构论文记录][A-batty]；经典自由表面黏流体方法 | **控制方式待核实；动态入口待补**。E/ν适用性、ρ及黏度的具体输入待核实 | 当前可运行代码、示例资产、商业依赖与渲染配套待核实。保留折叠、卷曲与旋转的算法线索 |
| **PolyStokes，2023** | [作者代码、场景及视频目录][A-poly]；分区降阶压力/黏度求解 | **目标参数控制方式待核实；仅有视觉效果参考**。E/ν适用性、ρ及黏度节点入口待核实 | 有代码和场景，要求 Houdini 18.5+；完整成片渲染配套待核实。按商业仿真约束保留视觉/算法参考 |

## 面向下一步选择的 AI 推荐

| 想观察的变化 | 优先候选与场景 | 已有证据 / 后续建议 |
| --- | --- | --- |
| 蜂蜜状流动随黏度变化 | Mixed MPM 落地盘卷 | 已有 50/500 Pa·s 对照。后续固定同一漏斗、ρ、时间步和渲染，观察盘卷高度、半径与铺展 |
| 目标ρ的展示价值 | Newton 公开漏斗；SPlisHSPlasH 作为备选 | Newton 可固定动力黏度、只改 density；SPlisHSPlasH 需同步按η/ρ换算运动黏度。效果待复跑，优先记录铺展和受力响应，避免只看落下速度 |
| E、ν在流体中的作用 | 先选定具有弹性分支的模型 | 目前普通牛顿蜂蜜无对应有限 E/ν对照；Mixed 的弹性分支与 SCA 黏弹模型可作后续依据，需先明确松弛、黏性及不可压条件 |
| 蜂蜜成片观感 | SCA 2024 甜甜圈；Blender 高黏度例 | 前者先看作者成片，后者可进一步核实仿真/渲染一体化流程；高光与透明材质属于外观辅助 |
| 简单公开复跑路线 | Mixed MPM、SPlisHSPlasH | 代码、参数入口和现成示例证据最完整。进入复跑时沿用同一例默认条件，仅改变声明的参数 |
| 最新方法补充 | PeriDyno NUV 2026；TVCG 2026 多相黏弹 | 前者已有程序化代码，后者先补代码/视频；当前用于候选选择与研究储备 |

交给后续工单的主要待核项是：ρ对照是否具有足够视觉差异；选定场景的物理尺度和固定黏度类型；蜂蜜表面重建/渲染配套；SCA/PeriDyno 的单位及资产入口。上述均属于候选选定后的复跑准备，本轮完成研究与记录。

## 一手来源

[M-home]: https://research.nvidia.com/labs/prl/mixed_mpm/
[M-paper]: https://research.nvidia.com/labs/prl/mixed_mpm/files/mixed_mpm.pdf
[M-video]: https://research.nvidia.com/labs/prl/mixed_mpm/files/mixed_mpm_params_supplemental.mp4
[M-api]: https://newton-physics.github.io/newton/latest/api/_generated/newton.solvers.SolverImplicitMPM.html
[M-example]: https://github.com/newton-physics/newton/blob/main/newton/examples/mpm/example_mpm_viscous.py
[M-code]: https://github.com/newton-physics/newton
[S-gallery]: https://splishsplash.physics-simulation.org/gallery/
[S-paper]: https://animation.rwth-aachen.de/media/papers/2018-EG-ImplicitVisco.pdf
[S-format]: https://github.com/InteractiveComputerGraphics/SPlisHSPlasH/blob/master/doc/file_format.md
[S-coil]: https://github.com/InteractiveComputerGraphics/SPlisHSPlasH/blob/master/data/Scenes/CoilingModel_Weiler2018.json
[S-bunny]: https://github.com/InteractiveComputerGraphics/SPlisHSPlasH/blob/master/data/Scenes/ViscousBunny.json
[S-scale]: https://github.com/InteractiveComputerGraphics/SPlisHSPlasH/discussions/240
[S-code]: https://github.com/InteractiveComputerGraphics/SPlisHSPlasH
[V-home]: https://elrnv.com/projects/variational-stokes-a-unified-pressure-viscosity-solver-for-accurate-viscous-liquids/
[V-video]: https://www.youtube.com/watch?v=QJePco0_os8
[V-extra]: https://cs.uwaterloo.ca/~c2batty/papers/Larionov2017/supp_video.mp4
[V-code]: https://github.com/elrnv/stokes-houdini
[B-video]: https://www.youtube.com/watch?v=iVjA8I_Qnlw
[B-release]: https://www.blender.org/download/releases/2-92/
[B-manual]: https://docs.blender.org/manual/zh-hans/4.4/physics/fluid/type/domain/liquid/diffusion.html
[B-api]: https://docs.blender.org/api/main/bpy.types.FluidDomainSettings.html
[B-fluid-script]: https://github.com/blender/blender/blob/main/intern/mantaflow/intern/strings/fluid_script.h
[B-liquid-script]: https://github.com/blender/blender/blob/main/intern/mantaflow/intern/strings/liquid_script.h
[J-examples]: https://fluidenginedevelopment.org/examples/
[J-video1]: https://www.youtube.com/watch?v=EKrqvBaLMyA
[J-video2]: https://www.youtube.com/watch?v=SqyrddEASVQ
[J-code]: https://github.com/doyubkim/fluid-engine-dev
[J-header]: https://github.com/doyubkim/fluid-engine-dev/blob/main/include/jet/grid_fluid_solver3.h
[J-source]: https://github.com/doyubkim/fluid-engine-dev/blob/main/src/jet/grid_fluid_solver3.cpp
[J-demo]: https://github.com/doyubkim/fluid-engine-dev-demo
[J-cubby]: https://github.com/utilForever/CubbyFlow
[N-eg]: https://diglib.eg.org/items/77b08e4d-17f0-451f-a481-9edafd398a1e
[N-paper]: https://www.cs.rug.nl/~jiri/papers/24ZhLoXuWaYaKoFrTeBa.pdf
[N-author]: https://xiaokun17.github.io/publications/
[N-video]: https://www.bilibili.com/video/BV14iJk67Er2/
[U-home]: https://dreliveam.github.io/publication/SIGGRAPH2026_Nonlocal
[U-publications]: https://peridynamics.com/publications.html
[U-paper]: https://peridynamics.com/publications/2026-Liu-NUV.pdf
[U-doi]: https://doi.org/10.1145/3799902.3811196
[U-code]: https://github.com/peridyno/peridyno/tree/master/examples/Cuda/UnifiedFluid
[U-example]: https://github.com/peridyno/peridyno/blob/master/examples/Cuda/UnifiedFluid/Qt_Viscosity/main.cu
[U-repo]: https://github.com/peridyno/peridyno
[H-rheology]: https://pmc.ncbi.nlm.nih.gov/articles/PMC4745488/
[H-crystal]: https://pmc.ncbi.nlm.nih.gov/articles/PMC12111295/
[A-2026]: https://research.rug.nl/nl/publications/a-unified-viscoelastic-solver-for-multiphase-fluid-simulation-bas/
[A-wethair]: https://github.com/raymondyfei/libWetHair
[A-creamy]: https://www.cs.columbia.edu/cg/creamystrand/
[A-batty]: https://uwspace.uwaterloo.ca/items/8e918fe0-bd66-4524-b448-e559e4cd67d4
[A-poly]: https://github.com/panuelosj/polystokes
