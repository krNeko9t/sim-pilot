# 从完整物体需求重做固体正向仿真选型

研究日期：2026-11-01。对应[重点固体候选与框架的正向仿真取舍](../issues/07-solid-forward-simulation-selection.md)。本文承接[规格](../spec.md)，取代此前候选兴趣、优先级及推荐理由；[前轮记录](07-solid-forward-simulation-selection.md)保留材料映射和源码线索。结论是本轮研究建议，公开结果、源码能力与拟议复跑分别标明。

## 当前建议

**阅读说明（2026-11-01 审阅补充）：**本文的“PolyFEM＋IPC”指 PolyFEM 已集成的 IPC 接触功能，使用者通过现有场景配置使用它；后续另需衔接的是 Blender 输出。[官方集成说明](https://polyfem.github.io/details/contact/)。PolyFEM 是本轮扩搜通用连续体求解器时新增的候选，随后由高阶接触论文、系统比较和公开资产支撑其优先位置。该优先级属于现有证据下的建议，FEM 与 MPM 的同任务质量／成本优劣仍有待对应证据。

**主推荐：PolyFEM＋IPC，用于保持完整、以弹性形变为主的体积物体；后渲染采用 Blender。** 理由是它同时提供明确的 E／ν／ρ、多材料体网格、非线性接触、可选时间积分与空间离散精度，并有完整软物体和部件接触的公开资产。当前是离线视频任务，选择依据侧重可解释的材料响应与求解质量。其高阶离散给出了针对粗网格假硬、局部形变质量的实质改进路径。[PolyFEM 功能](https://polyfem.github.io/)、[高阶 IPC 的对照与数据](https://zferg.us/research/high-order-ipc/)

**libuipc 是 GPU 体弹性、复杂接触和 Blender 工作流的条件性替代。** 当选定场景的规模或出片成本使 CPU 路线代价过高，或者需要它已实现的刚软混合结构时，采用该路线有明确收益。两者的跨项目误差与速度排名尚缺同一场景、同一模型的直接对照；本轮排序依据公开精度选项、场景证据及任务匹配程度。详细核查见[连续体路线证据](07-continuum-route-evidence.md)。

**PhysX GPU FEM 是应一起审阅的强竞争项。** 它有完整 teddy USD 与操作示例，也有使用 PhysX 生成多种完整外形物体下落数据的公开研究。若近期最重视已有玩具资产与 USD／RTX 制作条件，可选择这条路线。其求解迭代、材料版本和接触限制需按实际场景解释；静摩擦握持尤其需要区别判断。详细来源见[GPU 路线证据](07-gpu-route-evidence.md)。

这套主线的覆盖单位是**材料行为＋物体结构＋作用条件**。实体硅胶摆件可以采用体弹性；空心橡皮鸭需要壁与空腔；枕头需要外皮与填充或明确的等效模型；家具需要部件与连接。把这些外形都填成同一种实心材料，会改变物体所表达的行为。当前资料尚不足以给出“覆盖数据集中大多数物体”的比例，研究因此交付结构条件与迁移范围。

## 从需求推导物理路线

上游三量在选定材料模型内有明确作用。各向同性线弹性的弹性关系由 E、ν 决定，ρ 决定质量分布；线弹性适用于小弹性应变。大形变软体可选 Neo-Hookean 等超弹性模型，但相同 E、ν 的不同模型仍可在大应变下给出不同响应。材料分区、厚度、空腔、连接和接触条件同样影响视频。这是仿真建模范围的判断；下游评分方法沿用原职责边界。[线弹性的适用条件](https://docs.software.vt.edu/abaqusv2025/English/SIMACAEMATRefMap/simamat-c-linearelastic.htm)、[PolyFEM 材料形式](https://polyfem.github.io/details/formulations/)

| 物体与作用 | 应表达的物理内容 | E／ν／ρ 的展示价值与覆盖条件 |
| --- | --- | --- |
| 实心软玩具、橡胶摆件、可视为弹性体的食物，按压／掉落／弯曲 | 体弹性、质量、接触；复杂材料先声明采用的等效模型 | 当前主推荐范围；表皮、果肉、果核需要分区才有相应内部结构 |
| 金属／玻璃／硬塑料物件的平移、转动、堆叠 | 刚体、质量与转动惯量、摩擦与碰撞 | ρ 可通过固定外力下的响应体现；E、ν 的弹性效应需转入可解析形变的模型和加载条件 |
| 多部件玩具、家具、带软垫物件 | 分件、各部件材料、连接／关节／接触 | 体 FEM 可承担形变部件；各部件的连接条件决定整物响应，外壳外形本身不足以确定它 |
| 空心瓶、杯、壳体、枕头 | 壁厚、壳弯曲，按需要加入内压／填充／外皮 | 属条件性扩展；用实心软体替代只支持相应等效物体，覆盖结论需随结构限定 |
| 永久压瘪、肉撕裂、玻璃破碎、明显黏弹性 | 塑性、损伤／断裂、纤维或时间相关材料模型 | 需新增对应材料输入；目前三量主线先覆盖其适用的弹性作用范围，需求触发后再选专门路线 |

上述划分用于解释选择；布料、植物、软体入液保持暂缓。物体资产与上游接口的统一设计留在既定范围之外。

## 连续体与专门路线的取舍

| 路线 | 何时优先采用 | 本轮取舍依据 |
| --- | --- | --- |
| **PolyFEM＋IPC** | 完整体积弹性物体、多材料区域、复杂接触，重视离线精度可控 | 当前主推荐；高阶位移、曲面／碰撞表示和求解容差提供精度路径，接受 CPU 成本和后渲染适配。[证据与资产](07-continuum-route-evidence.md) |
| **libuipc** | CUDA 求解、较多软硬互动、直接在 Blender 制作 | 原生 E／ν／ρ 与缓存链清楚；运行库需包含2026-10-19材料映射修复。ABD 场景按硬部件看待，局部弹性另选体或壳。[体材料](https://spirimirror.github.io/libuipc-doc/specification/constitutions/stable_neo_hookean/)、[Blender](https://spirimirror.github.io/libuipc-doc/build_install/blender/) |
| **SOFA＋SoftRobots 等组件** | 有气腔、驱动、连接和刚软混合关系的完整机构 | PneuNet 完整例子有软指、加硬层、气腔与抓取；收益来自结构表达。具体力场、接触算法、单位和质量入口逐场景确定。[完整夹爪](https://github.com/SofaDefrost/SoftRobots/blob/master/examples/tutorials/PneunetGripper/details/step7-grabTheCube.py) |
| **FEBio** | 已知物体需纤维、粘弹性、近不可压缩或复杂组织材料 | 材料库与完整模型更合适；附加参数取自公开场景，接触性能按场景判断。近不可压缩也需选择相应本构与单元。[材料范围](https://febiosoftware.github.io/febio-docs/features/modules/module_solid/)、[具体条件](07-continuum-route-evidence.md) |
| **FreeForm／降阶 RKPM** | 复杂外形或点表示使体网格准备成为主要成本，且关注平滑的整体形变／快速批量运行 | 当这个条件成立时有实质价值；当前离线精度主线更看重局部接触和自接触。作者明确限制包括高频皱纹、尖锐接触、显式自碰撞和基质量。[方法与限制](https://research.nvidia.com/labs/sil/projects/freeform/) |
| **PhysTwin 类弹簧质量重建** | 输入来自互动视频，目标为重建并复现该 twin 的互动与外观 | 当前输入为几何与 E／ν／ρ，需先建立连续体量到弹簧网络及质量的对应关系；视频重建能力本身不解决这项映射。[作者的表示与任务](https://jianghanxiao.github.io/phystwin-web/) |
| **AnisoMPM 等各向异性／损伤 MPM** | 目标动作明确包含纤维方向撕裂、分层破坏或永久材料变化 | 其强项需要相应结构与损伤输入；当前完整弹性物体的主线先采用接触与材料语义直接的路线。[材料与损伤机制](https://joshuahwolper.com/anisompm) |

这里按新需求重新评价模型能力，旧兴趣名单对这些判断不再赋予额外权重。维护与实际使用同样按具体实现核实：PolyFEM 有2026-10-30代码活动及 DiffIPC 下游研究，SOFA 有26.06发行，FEBio 有4.13发行；版本、许可和具体资产来源见[连续体核查](07-continuum-route-evidence.md#7-维护使用与执行条件)。

## GPU 框架的正面比较

| 实现 | 当前适配价值 | 影响取舍的具体边界 |
| --- | --- | --- |
| **PhysX GPU FEM** | 完整 teddy 资产、体／碰撞／外观网格分离；E、ν 与ρ输入明确。可选裸 SDK、独立 ovphysx 或 Isaac 环境 | 三种交付环境的成本分别计算；ovphysx 0.7 是预发布，当前限制表列出 deformable 静摩擦限制。高刚度的形变质量取决于离散、材料与迭代预算。[形变设置](https://nvidia-omniverse.github.io/PhysX/ovphysx/latest/simulation_setup/deformables.html)、[限制](https://nvidia-omniverse.github.io/PhysX/ovphysx/latest/guides/limitations.html) |
| **Genesis FEM＋具体 coupler＋Nyx** | 当前已有隐式 FEM；SAP、IPC 路径及公开 Nyx 插件，支持 FEM 固定拓扑顶点更新。希望场景与渲染在同一环境时有价值 | IPC 路径复用 libuipc；完整 dragon 例是 PBD、worm 例是主动 MPM，分别按实际求解器计证据。Nyx 的透明材质和 USD 导入有边界。[耦合路线](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/theory/coupling/index.html)、[Nyx 支持表](https://genesis-embodied-ai.github.io/genesis-nyx/latest/user_guide/supported_features.html) |
| **Newton VBD** | 完整 tet 鸭子、程序化抓取／释放、可改的 Warp／Python 物理实现 | VBD 仍标 experimental；有限迭代、接触配置和外观网格更新均需场景化处理。完整实体鸭子的外形与空心玩具的结构区分。[求解器](https://newton-physics.github.io/newton/latest/solvers/vbd.html)、[鸭子资产](https://github.com/newton-physics/newton-assets/tree/main/manipulation_objects/rubber_duck) |
| **MuJoCo 的刚体／flex** | 硬物件、关节机构和ρ场景有价值；flex 有 E／ν 入口，当前 MuJoCo Warp 也已有实验性 flex | 刚体分支用于质量、惯性与整体运动；常见 flex 默认模型和降阶模式各有形变范围。使用大应变模型时须落实到具体实现。[建模](https://mujoco.readthedocs.io/en/stable/modeling.html#deformable-objects)、[Warp](https://mujoco.readthedocs.io/en/stable/mjwarp/index.html) |
| **Genesis／Newton／Warp 的 MPM 实现** | 弹性完整物体的并行候选，并按实现扩展到极大形变、屈服及既有 Gaussian 资产 | Genesis 弹性 MPM 可直接 E／ν／ρ，并有外观网格输出；Newton ImplicitMPM 的材料侧重和 PhysGaussian 的外观表示分别评价。完整弹性物体本身不足以排除 MPM。[具体实现与资产核查](07-gpu-route-evidence.md#7-mpm只保留有额外用途的具体实现) |

PhysX 的证据要分开阅读：**teddy 完整 USD 与抓放**见[官方任务入口](https://isaac-sim.github.io/IsaacLab/v2.3.0/source/overview/simple_agents.html)及[操作动图](https://developer-blogs.nvidia.com/wp-content/uploads/2025/08/Deformableobjectsinsim.gif)；**E／ν 动态对比**是当前 Isaac Lab 文档里的软球落桌面，E=1／10／100 kPa，ν 文本明确端点 .10 与 .49。当前多后端文档未标明该动画由哪个后端生成，因此将其记录为官方参数对比资料，teddy 的参数对比仍是待复跑方案。[E 动图](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/volume_youngs_1e3_1e4_1e5.gif)、[ν 动图](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/volume_poisson.gif)、[图注与材料说明](https://isaac-sim.github.io/IsaacLab/v3.0.0/source/concepts/deformables.html)

维护证据中，Newton 1.7.0、Genesis 1.4.5、MuJoCo 3.17.0 和 PhysX／ovphysx 均有2026年10月的具体发行；版本与功能要配套。Genesis 主站仍有 Nyx 内部发布的旧文字，当前插件官方安装页已公开 wheel，采用后者判断安装可得性。A800 可考虑承担 CUDA 物理计算，4090 更适合已核的 RTX／Nyx 渲染路径；实际显存与运行成本随所选场景确定。[发布与硬件核查](07-gpu-route-evidence.md)、[Nyx 当前安装要求](https://genesis-embodied-ai.github.io/genesis-nyx/latest/user_guide/installation.html)

## 质量依据与边界

### MPM 比较的实际结果与结论强度

本轮对 MPM 完成的是材料入口、示例、渲染及适用条件比较；FEM 另有较深入的高阶与接触质量证据。两侧证据深度不同，因此 PolyFEM 的优先建议不能解读为已证明 MPM 在本任务中更差。

| 具体 MPM 路线 | 本轮已得到的结果 | 当前位置 |
| --- | --- | --- |
| **Genesis MPM.Elastic** | 直接接收 E／ν／ρ，支持共旋和 Neo-Hookean；通过蒙皮使原外观网格跟随粒子，Nyx 可渲染该网格。已查完整 worm 是主动肌肉示例，纯被动日常物体的现成配置证据仍需补足 | **普通弹性完整物体的并行候选**。在已查 MPM 路线中，它的物理量与数字资产／PBR 路径最贴近本任务。[材料](https://genesis-world.readthedocs.io/en/latest/user_guide/theory/soft_solvers.html)、[外观网格](https://genesis-world.readthedocs.io/en/latest/user_guide/physics/beyond_rigid_bodies.html)、[Nyx](https://genesis-embodied-ai.github.io/genesis-nyx/latest/user_guide/supported_features.html) |
| **Newton ImplicitMPM** | 官方定位为颗粒与弹塑性，特别适合很硬材料和完全非弹性极限，通用性与传统显式 MPM 不同；已有弹性梁例 | 按具体材料与作用条件选用；其稳定性声明只支持相应数值性质。[官方定位](https://newton-physics.github.io/newton/stable/api/_generated/newton.solvers.SolverImplicitMPM.html) |
| **Warp-MPM／PhysGaussian** | 已有枕头等完整外观、粒子材料与视频输出；复用价值集中在已有 Gaussian 资产，物理尺度、内部填充与参数语义需配套解释 | 已有该类资产时的候选；普通 PBR 网格还可直接考虑 Genesis 的蒙皮路径。[项目与资产](https://github.com/XPandora/PhysGaussian)、[参数和尺度核查](07-gpu-route-evidence.md) |
| **AnisoMPM** | 纤维方向弹性与损伤支持肉／水果撕裂；作者示例可提供额外结构与损伤参数 | 需要该类作用时有明确收益；额外参数可沿用作者示例固定，迁移和解释需保留相应模型条件。[作者材料机制](https://joshuahwolper.com/anisompm) |

两条离散路线的实际取舍是：体 FEM 需要体网格，局部形变与表面接触可在明确的网格和离散精度下处理；MPM 用粒子承载材料状态、背景网格求解，需要选择粒子／网格分辨率、时间步及具体接触实现。MPM 固体也可保留并驱动外观网格，PBR 输出成本应按项目核算。现有证据支持“PolyFEM 优先、Genesis 弹性 MPM 保留并行比较”，而将 MPM 限为破坏／流动专项会遗漏其普通弹性能力。[Genesis 对两类表示和输出的说明](https://genesis-world.readthedocs.io/en/latest/user_guide/physics/beyond_rigid_bodies.html)

| 关注点 | 已有依据 | 对本任务的含义 |
| --- | --- | --- |
| 复杂接触与严重形变 | IPC 原论文公开挤压多物体、缠绕软球等场景，并提供接触和动态求解精度控制 | 适合研究完整物体之间的挤压、滑动和自接触；不穿透的数值性质与真实物体的材料准确性分别评价。[IPC 作者页](https://ipc-sim.github.io/) |
| 网格造成的假硬与形变细节 | High-Order IPC 给出 P1／P2／P3 与细网格参考对照，包含梁弯曲、垫子扭转与 Armadillo 滚轮挤压 | 选择 PolyFEM 的实质理由是具备提高空间精度的路线。结论限于论文的模型和试验；具体场景仍需匹配网格、阶次与版本。[公开对照](https://zferg.us/research/high-order-ipc/) |
| 真实运动对照 | 原 IPC 有高速泡沫球撞板的实验影像与模拟比较 | 提供一个受控材料／撞击条件下的实拍参照；它支持该试验，其他物体的真实性仍依赖各自材料与结构。[实验说明](https://ipc-sim.github.io/) |
| 时间离散与回弹 | PolyFEM 提供隐式 Euler、Newmark 和 BDF | 稳定的大步长可以抹平振动；回弹和ρ的动态展示需要时间分辨率。后渲染插帧只增加画面帧数。[时间积分](https://polyfem.github.io/details/time_integrators/) |
| 近不可压缩 | 普通位移 FEM 在 ν 接近 .5 时可能锁死；混合方法或更合适离散有对应研究 | ν 主对比先使用 .10／.30／.45；实际需要 .49 或更接近 .5 时，选取相应离散／混合路线。增加求解迭代主要减少求解残差，不能代替材料与离散的选择。[锁死研究](https://erleben.github.io/pubs/2021/francu.ea21/francu.ea21.pdf) |

**计算取舍：**高阶 IPC 作者展示的复杂 Armadillo 例耗时为小时量级；这是原论文配置的报告值，本机性能仍待所选方案复跑。它说明此选择主动接受离线成本。GPU 路线在大批量场景中的价值保留，现阶段按质量路径和资产条件给出推荐。[原场景耗时](https://zferg.us/research/high-order-ipc/)

## 公开场景怎样支撑完整物体覆盖

| 可查场景 | 它支撑的能力 | 迁移到目标物体的准备 |
| --- | --- | --- |
| **PolyFEM／高阶 IPC：完整 Armadillo 经滚轮挤压** | 非凸完整软体、局部形变和多处接触；作者页提供动态结果及代码、数据 | 迁移到同类实心软摆件需体网格、尺度、材料和接触。真实动物、毛绒填充或空心玩具另需对应结构。[动态结果与数据](https://zferg.us/research/high-order-ipc/) |
| **PolyFEM：螺栓旋入螺母** | 曲面接触、旋转驱动与自由进给；螺栓为可变形体，教程把螺母作为刚性障碍 | 可借鉴装配和部件接触设置；动态刚体、关节、粘结和家具连接仍需各自定义。全约束版本的轨迹按边界条件指定。[分场景教程](https://polyfem.github.io/tutorials/screw/screw/) |
| **PolyFEM：多材料和多组织公开数据** | 多区域、不同刚度及多部件体积结构；完整数据入口见专项研究 | 支持选择带分区的物理模型；迁移到水果或软垫需要目标物体自己的分区与连接。[多材料教程](https://polyfem.github.io/tutorials/multi-material/multi-material/)、[数据核查](07-continuum-route-evidence.md) |

这些场景共同支撑“具有可迁移的模型与接触能力”，各自的演示仍有确定的物体和边界条件。现阶段现成数字资产的模型文件、材质和求解场景须分别核对，完整物体视频的数量不直接构成材料正确性的证明。

复跑的具体数据入口已经定位：[`armadillo-roller.json`](https://github.com/polyfem/polyfem-data/blob/main/contact/examples/3D/friction/armadillo-roller.json)引用[`armadillo_verschoor.msh`](https://github.com/polyfem/polyfem-data/tree/main/contact/meshes/3D/creatures/armadillo)，原材料为 E=100 kPa、ν=.2、ρ=1000 kg/m³；[`golf-ball.json`](https://github.com/polyfem/polyfem-data/blob/main/contact/examples/3D/golf-ball.json)引用[`sphere19K.msh`](https://github.com/polyfem/polyfem-data/blob/main/contact/meshes/3D/simple/sphere/sphere19K.msh)。它们与高阶论文的配置是不同资产组合，原始取值和版本分别保留。

## 可选择复跑的参数场景

以下是 **AI 提出的场景改造方案**，使用项目公开网格。原作者演示支持求解与动作能力，本表的参数视频属于“代码支持调参，效果待复跑”。给出的尺度与取值是演示设定，真实物件的标定值由后续任务另定。

拟采用 Neo-Hookean 体材料、P2 位移与 IPC 接触，动态积分优先使用隐式 Newmark；P2 配置参考[当前弹跳球示例](https://github.com/polyfem/polyfem-data/blob/main/contact/examples/3D/higher-order/ball-bounce/P2.json)。此处 P2 指体内位移插值阶数，外观网格的面数另行决定。时间步须对应所选动作的回弹／冲击时标，固定后用于该组全部参数；空间阶次与时间积分的选择均属于拟议配置。

| 目标 | 资产、尺度与动作 | 对比取值 | 固定条件及观察量 |
| --- | --- | --- | --- |
| **E：完整软摆件掉落后的压缩与恢复（建议首组）** | 采用 PolyFEM 的 Armadillo 体网格，等比设为最长边 0.20 m；最低点离水平地面 0.10 m，零初速释放，保留完整四肢／躯干 | E=10／100／1000 kPa；ν=.30、ρ=1000 kg/m³ | 同一姿态、g=9.81 m/s²、摩擦、阻尼、网格和外观；比较触地压缩、自由部位下垂、接触面积与恢复过程。适用解释为实心弹性摆件 |
| **ν：球形软体受控压缩** | 采用公开 sphere 体网格，设直径 0.10 m；两平板平滑压缩到原高度的80%，约1 s加载、0.5 s保持，随后释放；接触设为无摩擦以显露横向响应 | ν=.10／.30／.45；E=100 kPa、ρ=1000 kg/m³ | 固定平板动作与其余条件；比较侧向鼓胀、体积比和自由表面。有限形变响应由所选超弹性模型决定，ν 的定义对应小应变关系 |
| **ρ：完整软摆件受同一水平短脉冲** | 同一0.20 m Armadillo，放在无摩擦水平支撑上；对同一表面区域施加世界坐标固定方向的牵引，持续0.05 s，总水平冲量固定为 J=0.2·m₀ N·s，其中 m₀=1000·V₀ kg | ρ=250／1000／4000 kg/m³；E=100 kPa、ν=.30 | 同一初始形状、零初速、重力、脉冲和外观；以质心位移／速度为主，形变传播为辅。纯水平动量估算给出脉冲后速度0.8／0.2／0.05 m/s，实际加载须保持三组积分总冲量一致 |

密度场景的 J 使用数值系数0.2 m/s乘基准质量 m₀；V₀ 为统一尺度后的静止体积。实现时固定合力／冲量，按所选表面力的积分约定配置，避免以“相同加速度”代替“相同力”。PolyFEM 当前 `RhsAssembler` 会把体力 `rhs` 乘以ρ，而 Neumann 面力单独按边界积分。因此ρ对比采用面力入口；自由落体阶段的重力加速度相同，密度差异应通过其他受力或动态过程体现。[源码依据](https://github.com/polyfem/polyfem/blob/main/src/polyfem/assembler/RhsAssembler.cpp)

E 场景选择掉落与自由响应，是为了让运动由材料与受力共同决定。仅比较相同的强制压缩轨迹时，加载端的位移已被指定，E 差异可能主要体现在反力中。ν 场景则有意固定压缩高度，用侧向与体积响应区分材料。

上述三组分别生成独立视频；人工审阅可另排并列画面。时间尺度、相机、光照和渲染材质固定；原始动态按真实播放速度输出。既有滚轮场景继续用于审阅复杂接触质量，单独标明它与新参数对比场景的区别。

## 数字资产与 PBR 的实际路径

PolyFEM 的公开工作流输出 PVD／VTU 等体网格与位移数据，ParaView 需要应用位移才能显示形变。制作 PBR 视频时，需要从结果取得形变表面，或将体位移插值到原有高分辨率外观网格，再写几何缓存；所用高阶基的位移应按其形函数计算。该环节是所选场景的导出适配工作。[输出说明](https://polyfem.github.io/tutorials/ipc_quick_start_guide/ipc_quick_start_guide/)

Blender 的 MDD／PC2 路径要求顶点数与顺序保持一致；USD／Alembic 的 Mesh Sequence Cache 还可处理变拓扑序列。保持完整的弹性体适合固定拓扑路径。UV、PBR 贴图与材质属于外观资产；单独导出仿真顶点不会生成它们。[Mesh Cache](https://docs.blender.org/manual/id/3.2/modeling/modifiers/modify/mesh_cache.html)、[Mesh Sequence Cache](https://docs.blender.org/manual/en/4.5/modeling/modifiers/modify/mesh_sequence_cache.html)

第一组 Armadillo 可使用统一的橡胶类程序材质展示该等效物体；若目标是保留照片级数字资产外观，需要已有纹理／材质及外观网格绑定。PBR 负责反射、透射等外观，体网格和求解负责运动；两者都对最终观看效果重要。[Blender Principled BSDF](https://docs.blender.org/manual/en/4.0/render/shader_nodes/shader/principled.html)

## 工程显式动力学的新增比较

OpenRadioss 的历史公开例子包含多部件手机跌落：有部件连接、弹塑性外壳与弹性内部件。这种路线适合今后出现硬物件冲击、塑性与薄壁结构需求时比较，材料输入会超出 E／ν／ρ。原示例页面的搜索索引仍有模型说明，但本次打开 Confluence 显示空间锁定，旧代码仓库返回404；该资产本轮记为历史公开、当前获取待恢复。[手机例子历史入口](https://openradioss.atlassian.net/wiki/spaces/OPENRADIOSS/pages/11141156/Cell%2BPhone%2BDrop%2BTest)

截至本次研究，维护状态已经改变：RESF 于2026-10-19公告，OpenRadioss 在10月1日终止，OpenCourant 从最后公开代码延续，并进入 RESF 孵化。OpenCourant 已发布2026-10-11构建，包内有 `anim_to_vtk` 和 `th_to_csv`。本轮将其列为按需补充的工程路线，原 OpenRadioss 的持续维护与资产可用性结论应按此更新。[RESF 公告](https://rockylinux.org/news/opencourant)、[当前构建与输出工具](https://opencourant.org/downloads/)

这种显式路线的步长受材料与网格影响；常用质量缩放会改变质量和动态行为，ρ对比应保留真实设定质量。其后渲染还需处理几何与材质，现阶段的覆盖需求更适合先从弹性主线收束。[显式步长与质量缩放](https://help.altair.com/2024/hwsolvers/rad/topics/solvers/rad/theory_dynamic_analysis_time_step_control_r.htm)

## 开源与环境配置成本

2026-11-01 审阅补充：用户追问各方案的开源性、环境配置及使用难度，并以熟悉的 Newton Python 工作流作比较。下表依据官方许可、安装文档、发布包及源码判断；本轮只读核查，未安装或运行。难度以有兼容 NVIDIA 驱动的 Linux 机器、使用现成示例为背景，区分物理核心与可选渲染／资产的条件。

| 路线 | 核心源码许可 | 安装与日常使用 | 当前任务的成本判断 |
| --- | --- | --- | --- |
| **Newton VBD** | Apache-2.0；官方 rubber duck 资产同许可 | `pip install "newton[examples]"` 后可运行 `python -m newton.examples softbody_franka`。场景与求解器采用 Python／Warp；ρ 直接输入，E／ν 换算为 Lamé 参数 | **低**。现成完整鸭子和脚本入口直接；VBD 仍为 experimental，运动质量与参数配置沿用此前边界。[仓库与安装](https://github.com/newton-physics/newton)、[VBD](https://newton-physics.github.io/newton/stable/solvers/vbd.html) |
| **Genesis MPM.Elastic** | Genesis 核心 Apache-2.0 | 匹配 GPU 的 PyTorch 加 `pip install genesis-world`；Python 中直接配置 `Elastic(E=..., nu=..., rho=...)`。标准安装使用预编译依赖 | **基础低，加入 Nyx 后低至中**。与 Newton 同属 Python 起步方便的路线；已查完整 worm 是主动肌肉例，改为被动物体仍需对应场景配置。[安装](https://genesis-world.readthedocs.io/en/latest/user_guide/overview/installation.html)、[材料](https://genesis-world.readthedocs.io/en/latest/api_reference/engine/material/mpm/elastic.html) |
| **libuipc／pyuipc** | 核心 Apache-2.0；Blender 适配层 GPL-3.0-or-later | `pip install pyuipc` 有现成 wheel，Python 场景可调 E／ν／ρ。当前 0.0.29 支持 CPython 3.10–3.14、Windows x64／Linux x64（glibc ≥2.34），wheel 不要求本机 CUDA Toolkit | **现成例低；本次采用已修复 E／ν 入口为中至高**。已发布 wheel 与修复的关系见下文；源码构建需 C++20、CMake、CUDA ≥12.4 和 vcpkg。[项目](https://github.com/spiriMirror/libuipc)、[发布](https://github.com/spiriMirror/libuipc/releases/tag/v0.0.29)、[源码构建](https://spirimirror.github.io/libuipc-doc/build_install/linux/) |
| **PolyFEM＋IPC** | MIT | 当前官方 IPC 教程采用 CMake／C++17 构建，随后 `PolyFEM_bin -j scene.json -o output`，修改 JSON 中 E／ν／ρ 即可运行；标准路径使用 CPU | **安装中，目标场景加后渲染中至高**。日常调参可通过 JSON 完成；高阶旧场景还需版本适配。Python 绑定存在，但当前预编译包与新 IPC 场景的兼容性仍待落实。[仓库](https://github.com/polyfem/polyfem)、[IPC 运行教程](https://polyfem.github.io/tutorials/ipc_quick_start_guide/ipc_quick_start_guide/) |
| **PhysX：独立 ovphysx** | PhysX SDK 为 BSD-3-Clause；ovphysx 源码为 Apache-2.0，官方预编译 SDK／wheel 采用 NVIDIA Omniverse License | 已有 pip wheel 和 Python API，可脱离 Kit 使用，也不要求本机 CUDA Toolkit。当前 quickstart 安装 `ovphysx physx-usd-schemas newton-usd-schemas`；参数配置在 USD 材料属性上 | **安装低至中，构造参数演示中**。要处理 stage、网格绑定、材料和渲染交接；当前仍 pre-release。[许可与说明](https://github.com/NVIDIA-Omniverse/PhysX/blob/main/ovphysx/README.md)、[quickstart](https://nvidia-omniverse.github.io/PhysX/ovphysx/latest/tutorials/quickstart.html) |
| **PhysX：裸 SDK 或 Isaac Sim／Lab** | SDK 为 BSD-3-Clause；Isaac Sim 仓库 Apache-2.0，Isaac Lab 主框架 BSD-3。Kit 与配套资产另有条款 | 裸 SDK 通常自行构建 C++ 应用；Isaac 环境可 pip 安装并用 Python 操作，但运行时、GPU 和磁盘要求较重，换取现成 USD 场景及 RTX 渲染集成 | **环境高，现成 Isaac 场景的日常调参低至中**。选择 PhysX 时应明确采用哪条交付路径。[SDK 许可](https://github.com/NVIDIA-Omniverse/PhysX/blob/main/LICENSE.md)、[Sim 许可](https://github.com/isaac-sim/IsaacSim/blob/main/LICENSE)、[Lab 许可](https://github.com/isaac-sim/IsaacLab#license)、[Isaac 安装](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_python.html) |

直接影响本次选型的具体条件：

- **libuipc 的 E／ν 版本成本已定位。** PyPI／GitHub 最新 0.0.29 于 2026-10-18 发布，其标签源码仍使用修正前的 SNK 系数映射，确实未含 10-19 的修复。若采用已修复的官方参数入口，当前应从包含修复的源码构建。0.0.29 已加入原生四面体生成，因此旧 Blender 文档中因生成体网格而要求源码的条件已变化；本任务当前的源码需求来自材料映射。[发布包](https://pypi.org/project/pyuipc/)、[0.0.29 材料源码](https://github.com/spiriMirror/libuipc/blob/v0.0.29/src/constitution/stable_neo_hookean.cpp)、[官方修复说明](https://spirimirror.github.io/libuipc-doc/specification/constitutions/stable_neo_hookean/)
- **PolyFEM 有 Python 绑定，但应保留版本区别。** `polyfempy` 的 PyPI 包为 0.2.3（2019，仅源码包）；conda-forge 的 0.5.2 于 2023-09-29 有 Linux／Windows／macOS x86-64、Python 3.8–3.12 预编译构建。当前 IPC 主线采用官方构建与 JSON 教程更明确。社区 Blender 插件另有 Docker 路径，可减少本机编译工作，镜像与所选场景仍需匹配。[PyPI](https://pypi.org/project/polyfempy/)、[conda 文件](https://anaconda.org/conda-forge/polyfempy/files)、[社区插件](https://github.com/ETSim/PolyFEMBlenderPlugin)
- **Genesis 物理核心与 Nyx 分开说明开源性。** Nyx 已公开插件、文档、示例与编译 wheel；公开仓库的 Apache-2.0 范围与整个渲染核心源码开放性分别判断，本轮未核到足以将全部核心称为开源的证据。Nyx 还要求匹配 GPU PyTorch 与 Vulkan 硬件光追条件。[仓库](https://github.com/Genesis-Embodied-AI/genesis-nyx)、[安装](https://genesis-embodied-ai.github.io/genesis-nyx/latest/user_guide/installation.html)
- **安装便利与视频交付分别计成本。** Newton 的脚本、预览及 USD 路径明确，变形外观与 PBR 绑定仍按选定场景落实；Genesis＋Nyx 已有 MPM 外观网格路径；libuipc Blender 扩展有缓存烘焙；PolyFEM 标准输出还要衔接表面／位移与后渲染。现成 Python 包主要降低仿真入口成本，最终出片沿用各路线已查的输出条件。[Newton 可视化](https://newton-physics.github.io/newton/latest/guide/visualization.html)、[Nyx 支持](https://genesis-embodied-ai.github.io/genesis-nyx/latest/user_guide/supported_features.html)、[libuipc Blender](https://spirimirror.github.io/libuipc-doc/build_install/blender/)

**对推荐的影响（AI 判断）：**Newton 与 Genesis 应处于 Python 上手便利性的第一档；libuipc 的现成入口同样轻，但本次材料参数版本条件增加投入。PolyFEM 的优先理由仍是已有高阶与接触质量证据，其配置／导出成本应与质量价值一起交给用户审阅。本次补充尚未形成用户的新路线选择。

## 交接时保留的具体条件

- **版本与资产：**PolyFEM 主分支在2026-10-30移除了 legacy 配置路径；高阶 Armadillo 论文配置仍含旧字段。使用匹配的论文实现或迁移配置，当前 P2 示例可作参考。常规 polyfem-data 与选定版本的运行兼容性在复跑时落实。[核查详情](07-continuum-route-evidence.md)
- **外观：**PolyFEM 已有的机械网格和几何结果需要后渲染适配；PhysX teddy 是完整 USD 的入口，其下载包与贴图在取用时检查。主方案的橡胶类程序材质可直接表达所声明的弹性摆件，照片级资产外观需要对应的纹理与绑定。
- **证据层级：**本次核查了官方文档、源码、资产目录与作者论文；新动态入口按作者图注定位。推荐、参数趋势和迁移范围为 AI 判断，参数场景的本地视频及耗时由所选方案复跑产生。

本票在路线比较与可选参数方案处完成；用户审阅由[审阅固体主力路线与首组参数场景](../issues/12-solid-route-and-replay-choice.md)承接。该票形成选择后，再建立具体安装与复跑工单。
