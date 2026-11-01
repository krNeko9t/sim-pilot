# 仿真候选总览与按场景推荐

固体方向后续更新（2026-11-01）：用户要求按需求重新研究，此前兴趣候选及优先考察理由均已过时。当前方案见[从完整物体需求重做固体正向仿真选型](07-demand-driven-solid-selection.md)，下文保留首轮全局总览，候选兴趣与排序仅作历史记录。

更新：2026-10-30。交付对应[候选总览与按场景推荐](../issues/06-candidate-overview-and-recommendations.md)，范围沿用[探索规格](../spec.md)。候选取舍采用用户本轮观看反馈，原研究保留详细参数、来源与历史推荐。

**AI 建议先从 FreeForm 的玩具或水果效果中选一个 E 展示场景，同时把 Newton、libuipc 作为直接接收材料参数的框架重点。** PhysTwin／Boba 保留用户的互动效果兴趣，AnisoMPM 保留肉与水果的材料行为兴趣；两组分别补齐连续体参数映射、各向异性与损伤输入后，再确定承担的参数展示。ν 可单独选 MiNNIE 或具备相应本构的 FEM／薄壳场景。颗粒集中看 Mixed MPM／Newton；蜂蜜保留 Newton 与 Blender／Mantaflow；ρ 沿用户提出的软体入液场景继续明确耦合条件。

## 观看入口与当前取舍

“感兴趣”记录用户偏好；“建议用途”是 AI 按正向仿真任务作出的判断。具体复跑组合尚由用户选择。

| 材料或现象 | 候选与动态入口 | 看什么与证据范围 | 当前取舍 |
| --- | --- | --- | --- |
| 玩具与水果接触形变 | **FreeForm，CVPR 2026**：[狗玩具 10 秒](https://research.nvidia.com/labs/sil/projects/freeform/assets/videos/splats/dog.mp4)、[葡萄 10 秒](https://research.nvidia.com/labs/sil/projects/freeform/assets/videos/splats/grape.mp4) | 约 2 秒落地后的压缩、回弹与稳定形状；首轮已抽帧，E／ν／ρ 对比待复跑 | 用户感兴趣；AI 优先建议承担日常 E 展示 |
| 毛绒玩具拉伸与摆动 | **PhysTwin，ICCV 2025**：[作者动态页](https://jianghanxiao.github.io/phystwin-web/)，Applications → Keyboard Control → sloth；Results → Double stretch sloth | 树懒受夹持后的身体形变；首轮已抽帧。视频支持互动效果，连续体 E／ν／ρ 映射待核 | 用户感兴趣，保留毛绒互动路线 |
| 同一物件的多实例互动 | **Boba，ECCV 2026**：[作者页](https://jianxiapyh.github.io/Boba-project-page/)，Boba in action 的 1 分 51 秒概览及 Demos | 看复用一个物理 twin 的独立运动和交互；本次按作者页面定位。批量性能与 E／ν／ρ 控制分别评估 | 用户感兴趣；与 PhysTwin 同组比较，保留各自实现 |
| 肉与水果的方向性撕裂 | **AnisoMPM，SIGGRAPH 2020**：[作者视频](https://www.youtube.com/watch?v=eXJi7pkUZn0)、[场景图注](https://joshuahwolper.com/anisompm) | Anisotropic Feast、Dongpo Pork；生肉纹理、橙瓣与分层食物。首轮按作者图注定位 | 用户明确感兴趣，继续保留；纤维和损伤另有输入 |
| 夹爪与塑形材料 | **SoftMAC，IROS 2024**：[挤压 GIF](https://raw.githubusercontent.com/minliu01/SoftMAC/main/media/grip_merged.gif)、[全片](https://www.youtube.com/watch?v=3n_PlXzD_Sc) | 首轮抽帧核对三列夹持运动；三列比较优化轨迹 | 用户比较感兴趣；软刚互动的次级候选 |
| 近不可压缩软体 | **MiNNIE，SIGGRAPH Asia 2024**：[作者视频](https://www.bilibili.com/video/BV1xZS4YXE65)、[论文](https://lwruan.com/assets/pub/minnie.pdf) | 论文 Fig.14 软字母落箱，ν=0.49999 是该场景值；视频抓取曾受限，按论文定位 | 用户比较感兴趣；AI 建议单独承担 ν 展示 |
| 布料与软硬接触 | **libuipc**：[官方演示合集](https://spirimirror.github.io/libuipc-web/)、[视频一](https://www.bilibili.com/video/BV1Yzp8zmEDE/)、[视频二](https://www.bilibili.com/video/BV1FMU6Y6Eu2/) | 对照 bunny + cloth、cloth stack 等样例观察折叠与接触；参数影响待复跑 | 框架重点；布料后续单独比较材料模型与接触实现 |
| 花茎与花头受扰 | **M-PhyGs，CVPR 2026**：[康乃馨 9 秒](https://vision.ist.i.kyoto-u.ac.jp/assets/videos/m-phygs_carnation.mp4)、[跨动作片段](https://vision.ist.i.kyoto-u.ac.jp/assets/videos/m-phygs_cross_carnation.mp4) | 左实拍、中仿真、右 E 伪彩；首轮已抽帧。观察接触后花头回摆 | 植物线索；追踪其 MLS-MPM 正向路径及参考状态 |
| 盆栽与机械臂 | **VoMP，ICLR 2026**：[厨房盆栽 50 秒](https://research.nvidia.com/labs/sil/projects/vomp/assets/videos/nurec_compressed/kitchen_02_4k_spp128_rgb.mp4) | 白盆植物的枝叶响应；首轮已抽帧。盆土、根系与盆体的力学处理待核 | 保留植物效果与下游仿真器入口 |
| 塑料瓶压缩 | **Radioss 官方案例**：[动态视频](https://fast.wistia.net/embed/iframe/d8hugtye24)、[模型说明](https://community.altair.com/discussion/62588/simulation-of-plastic-bottle-compression-with-radioss) | 同瓶形在空瓶、空气、水等条件下屈曲；比较包含内容物与加载变化 | 观看参考；开放复跑路线仍是覆盖缺口 |
| 雪与沙的弹塑性 | **Mixed MPM／Newton，SIGGRAPH 2026**：[参数视频](https://research.nvidia.com/labs/prl/mixed_mpm/files/mixed_mpm_params_supplemental.mp4#t=3)、[大场景](https://research.nvidia.com/labs/prl/mixed_mpm/) | 00:03–00:08、00:30–00:50；同片段左右 E=∞／10 MPa，跨片段塑性设置也变化。首轮已抽帧 | 颗粒主代表；投入收窄到具体所选场景 |
| 蜂蜜状盘卷 | **同一 Mixed MPM／Newton 实现**：[参数视频 01:04 起](https://research.nvidia.com/labs/prl/mixed_mpm/files/mixed_mpm_params_supplemental.mp4#t=64) | 约 01:08–01:11 为 50 Pa·s，01:13–01:19 为 500 Pa·s；首轮已抽帧 | 用户点名路线；已有对照量为黏度，ρ 效果待复跑 |
| 高黏度液体成片 | **Blender／Mantaflow**：[开发者演示](https://www.youtube.com/watch?v=iVjA8I_Qnlw) | 液体坍塌、铺展与堆积；按开发者入口定位 | 用户点名路线；适合仿真与渲染一体化，目标参数映射见下文 |

以下入口供补看参数证据与覆盖缺口，当前优先级承接用户反馈。

| 用途 | 动态入口与观看说明 | 如何使用 |
| --- | --- | --- |
| 枕头与外观结合 | PhysGaussian：[枕头落沙发](https://xpandora.github.io/PhysGaussian/static/videos/pillow2sofa.mp4)、[材料球](https://xpandora.github.io/PhysGaussian/static/videos/materials_cropped.mp4) | 首轮已抽帧；材料球有 E／ν 变化方向，数值与完整固定条件待补。保留仿真加外观参考 |
| 帽子与布料历史例子 | DiffCloth：[戴帽动图](https://people.csail.mit.edu/liyifei/publication/diffcloth/hat.gif)；C-IPC：[作者视频](https://www.youtube.com/watch?v=jvBSDXxsgRA) | 分别看戴帽、抽桌布／纸牌接触。按用户意见，老论文留作来源和具体物件参考 |
| 新薄壳弯曲方法 | Better Bending，SIGGRAPH 2026：[作者视频](https://zhenchen-jay.github.io/uploads/videos/Better_bending.mp4) | 弯曲、锐折与屈曲；主要比较离散模型，按布料补充需求展开 |
| 刚体密度与浮沉 | Akinci：[00:42–01:09](https://cgl.ethz.ch/Downloads/Publications/PaperVideos/2012/Sol12-Siggraph2012-VersatileRigidFluidCouplingForIncompressibleSPH.mp4#t=42) | 同球依次标 1／10／100／500／50000 kg/m³，属于连续调密度；首轮已抽帧 |
| 多流体密度比 | Primal SPH：[01:31–01:43](https://diglib.eg.org/bitstreams/8f8e680b-0eee-472e-a30f-403df48208a6/download#t=91) | 比较 Ours 左、中四格 1:5／1:10／1:100／1:1000；右列是算法比较。首轮已抽帧 |
| 密度与毛细时间尺度 | MultiLBM：[02:23–02:35](https://geometry-1.cms.caltech.edu/Movies/KineticSimMultiFluids.mp4#t=143) | 方滴回球，液滴与空气密度比 1000:100:25:1；液滴运动黏度相同，表面张力等完整条件待补。首轮已抽帧 |
| 密度的理论参照 | Ferrite：[梁振动动画](https://ferrite-fem.github.io/Ferrite.jl/stable/tutorials/elastodynamics-light.webp) | 现有动画为单组密度，位移放大 75 倍；不同密度下的周期对比待复跑 |

密度类当前保留观看资料，用户提出的**软体入液**才是下一步场景线索。刚球浮沉、多流体翻转与软体形变分别有不同的实现要求。

## 正向仿真实现与参数能力

术语沿用 [GLOSSARY](../../../GLOSSARY.md)。**已核实**指作者资料、文档或源码支持相应能力；**待核**指影响选型的具体缺口；场景推荐属于 **AI 推断**。“代码支持调参，效果待复跑”按表中列出的物理量成立，外观视频与参数对比各自记录。

### 固体形变重点

| 候选与贡献 | 实际仿真实现及本任务用途 | 参数控制方式与目标物理量 | 演示证据及关键条件 |
| --- | --- | --- | --- |
| **FreeForm**：构造降阶变形基，减少每物件优化成本 | RKPM 离散与 skinning eigenmodes 的降阶超弹性仿真；可接收已有材料属性，用于玩具、水果及受控梁 | **直接输入** E、ν、ρ，字段 `young_modulus / poisson_ratio / density`，支持体素属性 | **代码支持调参，效果待复跑**。玩具／葡萄视频的数值、真实尺度和对应资产待核；梁配置为另一独立示例 |
| **PhysTwin**：由互动视频重建动力学与 Gaussian 外观 | Warp 弹簧质量模型，现成 twin 可直接前向交互；适合毛绒效果与后渲染参考 | 当前按**视觉调节**记录弹簧刚度；连续体 E、独立 ν、体密度 ρ 到离散质量的映射待核 | 对 E／ν／ρ 属**仅有视觉效果参考**。初始化 `spring_Y` 与拟合后加载值有别 |
| **Boba**：提升 PhysTwin 类物理 twin 的单实例与批量执行效率 | 延续弹簧质量仿真、skinning 与 Gaussian 渲染；公开 Local／Batched 路径，适合复用已重建物件 | 当前按**视觉调节**理解原生弹簧／质量参数；到 E、ν、ρ 的物理映射**待核** | 对目标物理量属**仅有视觉效果参考**；批量能力可服务多组实验，但材料映射是前提 |
| **AnisoMPM／Ziran 2020**：各向异性弹性与损伤断裂 | C++ MPM 实现，覆盖生肉、橙瓣、奶酪与分层食物；按材料结构效果保留 | **直接输入基础 E、ν、ρ**，另需纤维方向、强化与损伤参数 | **代码支持调参，效果待复跑**；已有 E 扫值脚本，视频对应段待核。不同各向异性模型的画面差异应保留模型说明 |
| **SoftMAC**：软体与刚体／布料的可微耦合及控制优化 | Taichi MPM 与 Jade 刚体；可从公开 soft-rigid 挤压场景复用正向计算 | E／ν **字段输入与公式换算**；ρ 是源码常量 `p_rho=1`。elastic 模式另把 Lamé 系数乘 0.3，SI 尺度待核 | **代码支持调参，效果待复跑**；已看三列属于轨迹优化。屈服参数和夹爪轨迹须固定 |
| **MiNNIE**：近不可压缩弹性求解 | CUDA mixed FEM；适合 ν 对横向鼓胀与体积保持的展示 | **直接输入** `young / poisson / density`；E、ν、ρ 均可控制 | **代码支持调参，效果待复跑**。`bulging` 为准静态；ρ 的动态展示需用动态场景 |

参数细节与源码分别见[固体形变研究](01-solid-deformation-candidates.md)。FreeForm 的贡献及实现见[作者页](https://research.nvidia.com/labs/sil/projects/freeform/)；Boba 本次补核的公开路径见[官方仓库](https://github.com/jianxiapyh/Boba-Public)。

### 框架与重复候选的归并

| 实际实现 | 跨类别归并方式 | 参数与接触能力 | 当前用于选择的结论 |
| --- | --- | --- | --- |
| **Newton 的 Mixed MPM** | 雪、沙、黏性流体和密度研究中重复出现的同一实现归并到这里；按材料场景保留记录 | 弹性分支直接 E／ν；ρ 按粒子体积赋质量。黏流体例直接输入动力黏度，论文采用 E=∞ 的不可压设置 | 颗粒与蜂蜜主代表；E 视频对照和黏度视频对照各自成立，ρ 动态差异待复跑。[项目](https://research.nvidia.com/labs/prl/mixed_mpm/)、[示例](https://github.com/newton-physics/newton/tree/main/newton/examples/mpm) |
| **Newton 的软体求解路径** | 与同框架 MPM 分开，具体示例固定对应 solver | 已查软梁例按 E／ν 公式换算 `k_mu/k_lambda`，ρ 为 `density`；原软梁对比的是阻尼 | 列为固体框架重点；所选玩具几何、接触和视频输出继续按具体示例核实。[软体来源](01-solid-deformation-candidates.md)、[当前示例目录](https://github.com/newton-physics/newton#examples) |
| **libuipc** | 固体、布料和 VoMP 下游条目共用框架入口，体 FEM 与壳／布料分别选本构 | 体弹性可直接 E／ν／ρ；布料见下一表。采用 IPC 接触与 StiffGIPC 衍生 GPU 求解技术 | 固体接触和布料重点；官方示例已有 tetrahedron、bunny + cloth、cloth stack。[官方代码与样例](https://github.com/spiriMirror/libuipc) |
| **PhysX GPU soft bodies** | 用户新增框架线索，本次补核到具体 FEM 软体路径 | 5.4.1 文档明确四面体 FEM、`PxFEMMaterial` 的 E／ν、`updateMass` 的 density；仿真与碰撞网格可分开 | **文档支持物理量输入，效果待复跑**；目标物件动态入口、当前版本示例及输出条件待核。[官方软体文档](https://nvidia-omniverse.github.io/PhysX/physx/5.4.1/docs/SoftBodies.html) |
| **MuJoCo flex** | 用户新增框架线索，保留 deformable 路径 | `flexcomp/elasticity` 传递 `young / poisson`；ρ 经体积与质量如何配置按所选模型核实 | E／ν 为**文档支持，效果待复跑**；目标对象、材料范围和视频入口待核。[XML 文档](https://mujoco.readthedocs.io/en/stable/XMLreference.html#flexcomp-elasticity) |
| **PhysGaussian 内置 Warp MPM** | 枕头、沙状物和材料球合为一个项目，保留弹性与颗粒分支区别 | E／ν／density 有明确字段，几何归一化后的物理尺度待核；Gaussian 表示承担外观 | 按用户反馈保留仿真加外观参考；材料球的 E／ν 方向对照、沙和枕头的代码能力分别记录。[源码入口](https://github.com/XPandora/PhysGaussian) |
| **SPlisHSPlasH** | 高黏度、刚液浮沉与刚液接触方法共用项目入口，分别选择求解器和场景 | 流体 `density0`、刚体 `density`；物理运动黏度入口；已有双向刚液例 | 保留密度参考与流体备选；软体入液要核实可变形体与流体的双向耦合。[流体研究](04-viscous-fluid-candidates.md)、[密度研究](05-density-demonstration-candidates.md) |

MPM、FEM 是离散方法，IPC 描述接触处理，Warp 提供计算基础。共用其中一项的项目各自保留本构、求解器和代码来源。VoMP 的材料预测结果可以交给多个求解器；选型继续落到实际运行的那个实现。

### 布料与薄壳

| 路线与角色 | E／ν／ρ 及附加条件 | 当前用途 |
| --- | --- | --- |
| **libuipc 的 NeoHookeanShell 与 DiscreteShellBending**：材料模型与弯曲模型配合 IPC 接触 | **直接输入 E／ν／ρ＋派生弯曲系数**；固定全厚度，统一改 E 时同步更新膜与弯曲。API `thickness` 为半厚度 r，物理全厚度 h=2r | 当前框架重点；代码支持调参，参数效果待复跑 |
| **libuipc 的 Baraff–Witkin 壳**：方向刚度与应变限制 | E／ν 字段用于系数换算；该模型的 ν 不规定横向收缩比，ρ 仍为直接输入 | 可选布料效果；展示物理泊松比时选具有对应本构的路线 |
| **StiffGIPC**：高刚度及刚软混合的 GPU 求解技术与原型实现 | 具体材料输入和资产按该仓库核实；libuipc 官方说明了其技术继承关系 | 与 libuipc 关联阅读，独立保留原型和框架的运行条件 |
| **C-IPC**：薄结构接触、厚度和应变限制 | 各向同性壳接受 E、ν、ρ、厚度；膜与弯曲倍率分别设定 | 历史方法与参数依据，保留抽桌布、纸牌等参考 |
| **Better Bending／libshell，2026**：弯曲离散模型 | 已查静态例由 E／ν 换算二维 Lamé 参数；动态质量与场景配置待核 | 新方法备选；选择需求为弯曲／屈曲时展开 |

具体公式、材料预设、视频和代码来源见[布料薄壳研究](02-cloth-shell-plant-candidates.md)。按用户反馈，下一步比较以在用实现为主体，历史论文提供方法和物件来源。帽子可继续看 DiffCloth 与 Progressive Dynamics++ 的现成动态，E／ν 映射分别核实。

### 植物与塑料瓶

| 候选 | 贡献与实际正向仿真 | 参数证据与待核条件 |
| --- | --- | --- |
| **M-PhyGs** | 从视频估计材料与运动，使用 MLS-MPM 推进粒子，以 Gaussian viewer 呈现花材 | 粒子 E、ρ、ν 有模型依据；估计阶段优化 E／ρ、固定 ν。现有三栏是实拍／仿真／材料场。前向参数效果、参考状态、固定区域和初始内应力处理待核 |
| **VoMP** | 预测体积 E／ν／ρ 场；公开连接 Simplicits、warp.fem、libuipc、Newton | 已给参数时可复用其下游仿真路径。libuipc 橙子与 Newton 高／低 E 方块有入口，盆栽视频的具体 solver、资产、支撑与渲染条件待核 |
| **塑料瓶压缩** | Radioss 演示提供内容物与瓶壁屈曲参考；开放薄壳框架提供建模线索 | 现成开放瓶子示例仍缺。瓶壁厚度、内压／密封影响结果；永久压瘪另需塑性输入。E／ν／ρ 材料卡与 OpenRadioss 兼容性待核 |

来源：[植物与瓶体研究](02-cloth-shell-plant-candidates.md)、[VoMP 官方仿真路径](https://github.com/nv-tlabs/VoMP#-simulation)。根茎内应力的作用是待核问题；目前资料支持的是具体花材／植株运动，整束捆扎与盆土内部力学继续列为覆盖缺口。

### 蜂蜜与密度

**Newton 黏流体例**有 `density` 与动力黏度入口，漏斗和粒子程序化生成。已有视频比较 50／500 Pa·s，ρ 固定为 1000 kg/m³；ρ 的独立动态差异待复跑。E／ν 的有限值展示应选弹性或有明确对应关系的黏弹材料场景。来源：[黏性流体研究](04-viscous-fluid-candidates.md)。

**Blender／Mantaflow**保留为用户点名的仿真渲染路线。按已记录的 Blender 4.4 文档，Diffusion 用运动黏度 `Base×10^(−Exponent)`，高黏度 Strength 的 SI 映射待核，当前归为视觉调节；液体质量密度的独立入口及 E／ν 映射仍待确认。蜂蜜的黏度变化作为附加物理量展示，单独标注。来源：[控制方式与复跑条件](04-viscous-fluid-candidates.md)。

**软体入液**可把密度差异与形变、推移、摆动和液体响应放在同一场景。当前资料中的 SPlisHSPlasH 刚球、Newton XPBD 浮沉、多流体论文分别提供线索；完整的 MPM／FEM 软体与液体双向耦合、公开示例和参数控制需由场景工单选定。密度重点观察入液后的行为；相同初态、忽略空气阻力时，入液前自由落体加速度相同。依据与控制条件见[密度研究](05-density-demonstration-candidates.md)及[用户场景反馈](../issues/05-density-demonstration-candidates.md#comments)。

## 代码与示例以及维护证据

维护、后续继承、第三方采用分别记录。公开仓库与组织归属支持继续考察；用户规模和目标材料的实际采用需要具体证据。以下运行条件用于候选选择，选中后按具体版本核实。

| 路线 | 代码与现成数据 | 查看与渲染及运行条件 | 已知持续性或采用证据 |
| --- | --- | --- | --- |
| **FreeForm** | [evaluation 代码](https://github.com/nv-tlabs/freeform-eval)含梁与 19 个 SimReady 网格／材料例 | Kaolin／Warp／PyTorch、Polyscope；玩具和葡萄完整 3DGS 场景待核。SimReady 资产另有非商业研究／评估许可 | [Kaolin 官方 News](https://github.com/NVIDIAGameWorks/kaolin/tree/master#news)把 FreeForm／RKPM 集成列在未发布的 master／staging 变化中；这是明确的主库集成证据 |
| **PhysTwin** | [代码、预处理数据及 checkpoint](https://github.com/jianghanxiao/phystwin)；可从预训练交互入口开始 | Warp／CUDA／PyTorch 与 Gaussian 渲染；具体物件资产和环境待选后核 | 官方记录 2026-03 的 MPC 示例与 2026-08 的 Boba，另有社区贡献的 Windows／Docker 路线；支持后续发展判断 |
| **Boba** | [公开仓库](https://github.com/jianxiapyh/Boba-Public)已提供 Local／Batched、数据与预训练资源入口 | 当前分支要求 Linux、CUDA 13.2 或更新的 PyTorch 构建；有 headless 与视频保存路径。Distributed 分支仍为后续计划 | 官方 News 有 2026-09-12／14 的渲染优化记录；广泛采用程度待补具体证据 |
| **AnisoMPM** | [Ziran 2020](https://github.com/penn-graphics-research/ziran2020)有各向异性 demo、批扫与 Data.zip 入口 | C++／Eigen／OpenVDB／TBB；数据下载、许可及完整成片流程待核 | 当前材料能确认论文实现与示例；近期维护、后续采用继续核实，用户效果兴趣保留 |
| **SoftMAC** | [仓库](https://github.com/minliu01/SoftMAC)含 soft-rigid 场景与资源 | renderer／GIF 示例；soft-cloth 路线的 DiffClothAI 底层公开状态待核 | 近期维护与外部采用证据待补 |
| **MiNNIE** | [仓库](https://github.com/LwRuan/MiNNIE)含 assets、configs、CUDA 求解 | Vulkan 查看器；作者视频用 Houdini 渲染，独立开源求解与后渲染导出分别核实；复用许可待核 | 近期维护与外部采用证据待补 |
| **Newton** | [官方库](https://github.com/newton-physics/newton)有软体、布料、MPM、机器人及耦合示例；雪／沙／漏斗可程序化生成 | Warp、查看器及输出路径；选定 solver 后核实 A800／4090 环境及表面渲染 | Linux Foundation 项目，由 Disney Research、Google DeepMind、NVIDIA 发起；[VoMP](https://github.com/nv-tlabs/VoMP#-simulation)提供具体下游软体示例 |
| **libuipc** | [主库与样例](https://github.com/spiriMirror/libuipc)提供 Python／C++ 与 GUI／headless 场景 | CUDA；Blender 集成及所选场景视频导出继续核实 | 官方 News 记录 2026-03 Genesis AI 贡献、2026-08 CUDA 优化；VoMP 的橙子 FEM 路径是具体采用入口 |
| **PhysX 与 MuJoCo** | [PhysX](https://github.com/NVIDIA-Omniverse/PhysX)、[MuJoCo](https://github.com/google-deepmind/mujoco)公开库；目标软体示例按版本核实 | PhysX 此次核对的 5.4.1 软体为 GPU 路线；MuJoCo viewer 与物件视频输出待选定 | 当前补核限于软体参数文档，针对本任务的采用与维护比较交后续固体工单 |
| **M-PhyGs 与 VoMP** | 前者有 [Phlowers、预处理数据和前向命令](https://github.com/kyotovision-public/m-phygs)；后者有模型、材料场与下游例子 | 前者 Gaussian viewer；后者有逐帧表面输出及不同外观路径。具体花材 checkpoint、盆栽资产待核 | 2026 项目及已公开路线可查；植物场景的长期维护与采用证据待补 |
| **Blender／Mantaflow** | 公开 Blender 流体工作流；开发者演示对应 .blend 下载待核 | 液面网格与 Cycles／Eevee 可在同软件完成；所选版本参数约定见[流体研究](04-viscous-fluid-candidates.md) | 以现行工程项目保留；本次按已有 4.4 文档记录能力，具体场景使用证据待补 |
| **SPlisHSPlasH** | [仓库](https://github.com/InteractiveComputerGraphics/SPlisHSPlasH)有高黏度、刚球与多相 JSON／资产 | 独立公开求解，粒子输出及 splashsurf／BlenderSequenceLoader 配套 | [Consistent SPH 作者实现说明](https://github.com/InteractiveComputerGraphics/SPlisHSPlasH/discussions/328)提供算法进入项目的线索；Primal SPH 完整公开分支仍待核 |

商业依赖按具体路线判断：Radioss 案例、Variational Stokes 和 PolyStokes 的现有商业求解流程保留观看参考；MiNNIE 的作者成片使用 Houdini，公开求解器与可替换后渲染可继续评估。代码和场景均在选定后检查运行条件。

## 物件与材料覆盖

| 规格目标 | 当前有证据的覆盖 | 对后续选择有影响的缺口 |
| --- | --- | --- |
| 家具 | PhysGaussian 沙发与坐垫接触 | 木／金属家具结构弯曲，以及沙发各部分的独立力学处理 |
| 玩具 | FreeForm 玩偶；PhysTwin／Boba 毛绒互动 | 硬塑料、充气或复合结构玩具需分别确认材料和内部结构 |
| 枕头 | PhysGaussian pillow2sofa 视频与配置 | 外皮／填充物的独立参数与等效实体模型对应关系 |
| 塑料瓶 | Radioss 瓶体屈曲视频 | 现成开放瓶体场景、壁厚与内压条件；永久形变另需塑性输入 |
| 帽子 | DiffCloth 戴帽；Progressive Dynamics++ 抛帽 | 在用实现中的对应资产、上游 E／ν／ρ 映射、帽檐／衬层结构 |
| 花束 | M-PhyGs 单枝与多花头枝条 | 整束多茎接触、捆扎、包装和容器互动 |
| 盆栽 | VoMP 白盆植株互动；Gazebo Plants 枝叶参考 | 植株参考状态、盆体自由度、根土力学与连接 |
| 玻璃 | SoliDualSPHysics 脆性基准、Fracture-GS 线索 | 玻璃本体 E／ν／ρ 的动态对比与开放物件示例；Breaking Good 按用户取舍移出当前候选 |
| 金属 | SoliDualSPHysics Taylor bar；PhysGaussian 罐体压皱 | 塑性以外的 E／ν 单独效果；前者动态观看入口仍待补 |
| 塑料 | 弹塑性块、塑形材料互动可看 | 普通硬塑料的模型与场景；plasticine 画面只覆盖塑形材料 |
| 果冻 | 弹性软块和近不可压缩求解器提供候选基础 | 真实果冻物件／材料值与现成晃动、挤压对照；目前为拟议场景 |
| 肉 | AnisoMPM 生肉、东坡肉及分层撕裂 | 只给各向同性 E／ν／ρ 时的弹性按压路线；纤维／损伤输入如何沿用 |
| 水果 | FreeForm 葡萄；AnisoMPM 橙瓣；VoMP 的 libuipc 橙子入口 | 果皮／果肉分层与受控挤压；外形和材质标签的真实对应 |
| 布料 | libuipc、StiffGIPC、C-IPC 与 DiffCloth 等 | 在用实现比较、膜／弯曲／厚度统一解释；日常 ν 对比视频 |
| 雪与沙堆 | Mixed MPM／Newton 的公开场景 | ν／ρ 独立动态效果；连续体密度与上游颗粒实体密度的对应 |
| 蜂蜜 | Newton 盘卷；Blender 高黏度；其他成片参考 | 日常尺度、黏度类型、ρ 展示；渲染与真实流变条件 |
| 密度的丰富互动 | 刚液浮沉、多相翻转和液滴回球资料 | 软体入液的完整双向耦合实现与现成资产 |

E 的已有动态证据以 Mixed MPM 左右块及 PhysGaussian 材料球为代表；ν 的现有定性对比集中于 PhysGaussian 材料球，其他候选主要提供参数入口；ρ 有刚球和多流体视频，软体入液仍待定义。覆盖表示有可查资料，具体物件的参数对比按场景选择。

## AI 按场景推荐

这些组合以视觉合理性和参数变化的可辨识程度排序，允许分别采用不同方案。除注明的作者对比外，建议效果都待所选方案复跑。

| 想展示的内容 | 推荐组合及理由 | 参数与固定条件 | 证据依托 |
| --- | --- | --- | --- |
| **玩具或水果太软与太硬** | 优先 FreeForm；Newton／libuipc 可作直接材料输入的框架选择。落地接触和释放后的形变容易按日常经验讨论 | E 可从参考值的 0.1×／1×／10×选适用组；固定 ν、ρ、几何、落高／外力、接触及观察时间 | FreeForm 日常视频＋E 代码入口；框架参数与样例 |
| **体积保持与横向鼓胀** | MiNNIE 的软块／bulging，或 libuipc 相应体弹性场景；适合把 ν 单独解释 | 固定 E、ρ，选模型适用的 ν，如 0.3／0.45／0.49；同轴向变形观察横向尺寸。准静态例只承担形变展示 | MiNNIE 参数与近不可压缩模型；libuipc 本构依据 |
| **喜爱的毛绒互动** | PhysTwin／Boba，保留树懒等既有资产和外观；适合探索观感与交互 | 先确定弹簧、离散质量与上游物理量的对应；确定前沿用原生量标注 | 用户兴趣、原作者动态、公开 twin 路线；E／ν／ρ 对照待核 |
| **肉或水果的材料结构** | AnisoMPM；纤维方向与层间响应的差异醒目 | 若比较 E，固定纤维、损伤与拉伸条件；基础 E／ν／ρ 加附加材料输入共同定义场景 | 食物动态与现成 E 扫值代码；脚本与视频对应待核 |
| **布片软硬与回弹** | libuipc 的 NeoHookeanShell＋弯曲模型，固定厚度做同外力下垂／释放；接触展示另选布堆 | 统一改 E 时同步改膜与弯曲系数；ν 对照另做小应变拉伸，固定厚度与自由侧边 | 已有参数模型；日常参数效果待复跑。布料研究进一步比较实现 |
| **花材与植株受扰** | M-PhyGs 花茎或 VoMP 线索中的具体下游 solver；花头回摆比材料伪彩更贴合任务 | 固定支撑、动作、初始受重力状态处理；E 场按倍率变，ν／ρ 和分区固定 | 花材／盆栽动态；正向资产、参考状态与内应力处理待植物工单核实 |
| **雪与沙的明显异常** | Mixed MPM／Newton，同塑性设置下块体受撞或落在圆柱上；已有大差异 E 对照 | 视频 E=∞／10 MPa 作观看依据；复跑有限数值组按模型选择，固定塑性／摩擦／初态 | 作者参数视频＋公开雪／沙例 |
| **蜂蜜盘卷和铺展** | Newton 优先展示有单位的黏度变化；Blender 优先选择仿真渲染一体流程 | 黏度是附加输入。改 ρ 时写清固定动力黏度 η 还是运动黏度 ν_kin，二者满足 ν_kin=η／ρ | Newton 50／500 Pa·s 视频；Blender 控制与渲染路线 |
| **密度差异与软体丰富互动** | 先把用户设想收束为一个软体入液场景，再定 solver；刚球浮沉可作辅助参照 | 同几何、初态、E／ν 和液体条件，只改软体 ρ；观察入液后浮沉、惯性、摆动、形变和水的响应 | 用户场景意图＋已有密度资料；软液双向耦合实现待核 |
| **密度的明确理论参照** | 若需要辅助解释，可选 Ferrite 梁振动或 SPlisHSPlasH 刚球浮沉 | 梁固定刚度、初形变、阻尼策略与物理时间窗；球固定水密度与初态，保留浅槽接触条件 | 梁方程、刚球视频与公开例；均作为可选辅助场景 |

其他输入沿用所选同一场景的默认配置；壳厚度、纤维、屈服或流变参数若为必填且缺默认值，在该场景工单中明确。比较大跨度参数时，数值求解需保持适用；改变步长等必要设置要记录。视觉合理性用于讨论参数，理论参照帮助解释已控制的条件。

## 同类备选与原始资料

下表保留首轮候选全貌。重复实现已在正文归并，旧论文和专项方法可按具体需求回查；各研究文件记录它们的原始来源、已找到的代码与动态入口，以及待核项。

| 原研究 | 正文以外仍可查的候选 | 当前用途 |
| --- | --- | --- |
| [固体形变候选与动态演示](01-solid-deformation-candidates.md) | IPC、DiffPD、Genesis、VR-Doh、SoliDualSPHysics、Fracture-GS、Ziran 2019；Breaking Good 的历史条目 | 受控弹性、不同工程实现、塑性与断裂补充。Breaking Good 的用户排除意见继续有效；SoliDualSPHysics 保留观看入口缺口 |
| [布料薄壳与植物候选](02-cloth-shell-plant-candidates.md) | DiffCloth、Gazebo Plants、Progressive Dynamics++、Unlocking Thickness Modeling、2025 CPU 布料、ARCSim、Botanical Materials、Beyond Kirchhoff–Love | 帽子与具体物件、植物杆模型、厚度和弯曲依据；老论文留作历史来源。Beyond Kirchhoff–Love 按作者公开且会议尚未举行处理 |
| [颗粒材料候选](03-granular-material-candidates.md) | 经典雪 2013、经典沙 2016、Taichi Elements／MLS-MPM、Homogenized Sand 2025、Matter、LBM-MPM 水气沙耦合、粉雪雪崩 | 保留方法、参数与特殊现象依据，主推荐集中 Mixed MPM／Newton |
| [黏性流体候选](04-viscous-fluid-candidates.md) | SPlisHSPlasH、Variational Stokes、Jet／CubbyFlow、SCA 2024 多相黏弹、PeriDyno NUV、TVCG 2026 多相黏弹、libWetHair、CreamyStrand、2008 黏性自由表面、PolyStokes | 供特殊流变与涂覆需求回查；商业依赖逐路线标注 |
| [密度展示候选](05-density-demonstration-candidates.md) | Akinci／SPlisHSPlasH、Primal SPH、MultiLBM、解析浮力积分、Ferrite、Adaptive Phase-Field-FLIP、Newton XPBD、AnisoMPM 密度批扫、LBM-MPM 耦合、Consistent SPH | 保留浮力、毛细、惯性和振动来源；AnisoMPM 环断裂中的数值失稳单独解释 |

LBM-MPM 水气沙耦合在两份研究中的公开状态记录深度不同：[颗粒研究](03-granular-material-candidates.md)已找到[作者代码入口](https://github.com/DacianShaw/LBM-MPM-Air-Water-Sand)，密度研究仍保留“代码待核”。总览采用“已有代码入口，具体密度控制、场景资产与复跑条件待核”，相关资料归并为同一候选。

## 审阅与接续

本轮汇总在候选、视频入口、参数证据和场景建议处收束。用户选中“候选＋展示场景＋要观察的物理量”后，建立相应复跑工单，核实那组公开代码、现成数据、默认设置和后渲染路径，再生成视频。补充调研由具体缺口或新反馈触发。

既有反馈已明确的后续问题分别由[重点固体候选与框架的正向仿真取舍](../issues/07-solid-forward-simulation-selection.md)、[布料材料模型与接触实现的组合](../issues/08-cloth-material-contact-combinations.md)、[植物正向仿真的参考状态与支撑条件](../issues/09-plant-forward-simulation-conditions.md)、[软体入液的密度差异展示](../issues/10-softbody-liquid-density-scene.md)承接。各票保留具体问题与完成边界，本轮关闭汇总票，后续票保持未认领。
