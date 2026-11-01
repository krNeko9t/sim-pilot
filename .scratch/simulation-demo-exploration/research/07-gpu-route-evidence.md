# 完整物体正向仿真的 GPU 路线证据

核查日期：2026-11-01。范围为实心软体、硬物件及多部件，薄壁只记录覆盖边界；布料、植物、固液分支暂缓。研究对象为 Newton、PhysX、Genesis、MuJoCo，以及能增加实际能力的开放 MPM 实现。本文重新按当前需求判断，未沿用旧候选兴趣或优先级。

**2026-11-01 审阅补充：**下文将 MPM 统一列为“有目标现象时启用”的排序表述收得过窄，更新结论以[主报告的 MPM 比较](07-demand-driven-solid-selection.md#mpm-比较的实际结果与结论强度)为准。Genesis MPM.Elastic 保留为普通弹性完整物体的并行候选，Newton ImplicitMPM、PhysGaussian 和 AnisoMPM 仍按具体实现分别判断。本文已核参数、资产和输出事实继续有效；本轮比较尚未建立 FEM 对 MPM 的同任务质量／成本优势。

标记：**事实**为官方文档、源码或作者论文明确内容；**推断**为据此作出的选型判断或建议；**未核**为当前尚无直接证据的环节。已核查公开资料与源码，未安装、执行、计时或观看完整动画过程，因此“有动态入口”与“已亲自确认视觉质量”分开。

## 1. 结论：哪些值得进入主推荐

**推断：PhysX GPU FEM 应成为完整物体路线的主推荐竞争项；Genesis FEM 是值得保留的端到端备选；Newton VBD 是开放可改求解器与完整软体操作的条件性备选；MuJoCo 主用于硬物件、多部件及密度演示。** MPM 应在目标需要极大形变、屈服或已有 MPM/3DGS 资产时加入，不能仅因粒子法覆盖材料种类多而占据普通弹性完整物体的主线。此排序针对“已有资产 → 有意义的 E/ν/ρ 变化 → 可交付视频”的证据与成本，不是未经测试的求解精度排行榜。

| 本次排序与位置 | 最有价值的场景 | 进入该位置的依据 | 改选条件 |
| --- | --- | --- | --- |
| **1. PhysX GPU FEM：主推荐竞争项** | 均质软体完整外形的跌落、挤压、与刚体互动；同一环境补充刚体/关节物体 | 有 teddy 完整 USD 与操作示例、独立论文使用 PhysX 生成多种完整物体下落数据；仿真网格、碰撞网格、外观网格可分离。另有 Isaac Lab 软球 E/ν 动图，但其生成后端未定位 | 若主要关注复杂持续静摩擦、极硬材料的大步长精度、复杂本构，需与离线 FEM/IPC 方案比较；不能从实时引擎稳定性推出精度最优 |
| **2. Genesis FEM：端到端条件性备选** | 刚体抓持/挤压软体，使用现成 GLB/OBJ 外观；希望 Python 仿真与 PBR 摄像机在一处 | 当前已有隐式 FEM、SAP、基于 libuipc 的 IPC；公开 Nyx 插件支持 FEM 顶点更新与 MPM 蒙皮 | 已核完整软体 E/ν 场景以简单球/块为主；dragon 与 worm 不能直接填补均质弹性日常物体覆盖。看重复杂接触时选 IPC；适度形变持续接触可看 SAP |
| **3. Newton VBD：开放实现条件性备选** | 完整 tet 物体被夹起、释放、落地；希望直接使用 Warp/Python 实现 | 官方 rubber duck 的 surface/tet 数据与 Franka 操作源码可定位；VBD 能处理体积软体 | 官方仍标 experimental，刚软接触保护有具体限制；附带 USD 场景保留 PBR 的 RTX 路径还需核实可变形更新，不宜把刚体渲染演示直接套给软体 |
| **4. MuJoCo：硬物件/多部件与 ρ 的补充主力** | 家具部件、关节机构、硬玩具的推撞、固定冲量、跌落与支撑 | 刚体/关节资产生态可用；flex 确实提供 E/ν，当前 Warp 也开始支持 flex | 刚体无应变，不能用来演示材料 E/ν；默认 flex 的小应变本构及降阶模式有限定，实验 SNH 需另设，不作为果冻/食品/复杂软物件的通用替代 |
| **5. MPM：有目标现象时启用** | 极大形变、屈服；已有 Genesis MPM 或 PhysGaussian 数据 | Genesis 弹性 MPM、Newton ImplicitMPM、Warp-MPM/PhysGaussian 各有可定位实现 | 对普通保持拓扑的完整弹性物体，粒子/网格分辨率与表面输出增加成本；不同实现的本构与接触不能互相代替 |

这些排序对应下文事实。**推断：若主报告选择 PolyFEM+IPC 等离线 FEM 为精度优先主线，PhysX 应明确作为“现成完整资产和渲染链更直接”的竞争方案审阅，而不是仅列为机器人刚体框架。Genesis 的现有公开渲染链也构成实际竞争力。** 本研究未作 PolyFEM 与这些实现的同场景误差或耗时对比。

## 2. E、ν、ρ 在完整物体上的含义

**事实：**E 是杨氏模量，单位 Pa；ν 是泊松比，无量纲；ρ 为密度，单位 kg/m³。各向同性弹性模型可换为 Lamé 参数：`μ = E / [2(1+ν)]`，`λ = Eν / [(1+ν)(1−2ν)]`。Isaac Lab 文档直接示范 PhysX 的 E/ν 与 Newton 的 μ/λ 换算；Genesis 软体理论页明确三者单位。[Isaac Lab 材料参数](https://isaac-sim.github.io/IsaacLab/v3.0.0/source/concepts/deformables.html)、[Genesis 软体理论](https://genesis-world.readthedocs.io/en/latest/user_guide/theory/soft_solvers.html)

**推断：**E 控制抵抗拉压/剪切的尺度，ν 影响受压后的横向鼓出和体积变化，ρ 通过质量与惯性改变固定力、冲量和接触下的响应。`ν→0.5` 时上式 λ 急剧增大，数值求解更困难；应按各模型范围取值，不把 `0.5` 当跨项目通用设置。相同重力下的真空自由落体加速度与密度无关；用“哪一个先落地”展示 ρ 没有区分力，固定冲量、与参照物碰撞或承载下垂更合适。

**推断：**同一外形填满一种材料，只代表均质实体假设。rubber duck 的完整外形 tet 不是日常空心橡胶鸭的壳；teddy 的均质软体不是布面加填充物；枕头、塑料瓶、肉与水果分别可能需要壳/填充物、壁厚/空腔、各向异性或分层组织。E/ν/ρ 本身不提供这些结构，也不提供阻尼、屈服、摩擦、粘附和断裂参数。PBR 材质只解决外观表达，不能补全内部物理结构。

**推断：**“画面稳定”“碰撞不明显穿透”和“本构及材料量可信”应分别判断。可信程度同时受本构、体积离散、网格/粒子分辨率、时间步、迭代收敛、接触近似影响；GPU 支持或跑得快只说明执行能力。

## 3. PhysX：完整物体主推荐的证据与边界

### 3.1 区分求解器与交付环境

| 层次 | 已核事实 | 本任务含义（推断） |
| --- | --- | --- |
| **开源 PhysX SDK** | 仓库提供开源 SDK；deformable volume 使用 GPU FEM，提供网格 cooking、材料、刚体/软体接触。2026-10-27 发布 Omniverse PhysX 110.3 / SDK 5.9.1。[仓库](https://github.com/NVIDIA-Omniverse/PhysX)、[发布记录](https://github.com/NVIDIA-Omniverse/PhysX/releases) | 可脱离 Isaac/Omniverse 搭建仿真；SDK 本身不等于现成资产浏览器、PBR 视频生产应用 |
| **ovphysx** | 2026-10-15 的 0.7.0 为完整开源发布，独立 C/Python 库，可载入 USD、模拟并以 DLPack 读取/写入状态；官方标为 pre-release，API 尚在完善。[发布记录](https://github.com/NVIDIA-Omniverse/PhysX/releases)、[库文档](https://nvidia-omniverse.github.io/PhysX/ovphysx/latest/index.html) | 是“开源求解器 + USD/Python”的现实中间路线，能减少直接写 C++ SDK 的工作；其预发布状态要单独记录 |
| **Isaac Sim / Omniverse 环境** | 提供 USD 场景、资产、物理属性编辑及 RTX 渲染。Isaac Lab 3.0 的 PhysX、Newton、OvPhysX 可处理 volume/surface deformable，后端能力不同。[Isaac Lab deformables](https://isaac-sim.github.io/IsaacLab/v3.0.0/source/concepts/deformables.html) | 同场景资产和高质量输出方便，但安装、运行与渲染硬件成本高于裸库；不能把该环境便利性归成 SDK 自带功能 |

**事实：**Isaac Lab 的 OvPhysX 后端不依赖 Kit；当前集成锁定 `ovphysx 0.6.4.72118723`，并非自动使用最新 0.7。该后端与 Kit visualizer 不可在同一进程使用，提供 headless/GL/Rerun/Viser/OVRTX 选择。完整 USD stage 物化有启动开销，当前不适合数千环境的快速克隆；本任务的少量离线视频受此影响较小。[Isaac Lab OvPhysX 后端](https://isaac-sim.github.io/IsaacLab/v3.0.0/source/concepts/physics-backends/ovphysx.html)

### 3.2 物理模型与限制

- **事实：**SDK 5.8 deformable volume 文档介绍 fixed-corotated 弹性能量，以 E/ν 定义 μ/λ；当前主分支头文件已明确提供默认 `eCO_ROTATIONAL` 与 `eNEO_HOOKEAN` 选择，分别说明偏高刚度/低刚度的适用侧重，不能用旧文档把当前 PhysX 限定为单一本构。simulation tet、collision tet 和 render surface 可不同精度；高刚度一般需要更多迭代，降低网格分辨率可改善收敛但牺牲细节。[PhysX 5.8 Deformable Volume](https://nvidia-omniverse.github.io/PhysX/physx/5.8.0/docs/DeformableVolume.html)、[当前材料模型头文件](https://github.com/NVIDIA-Omniverse/PhysX/blob/main/physx/include/PxDeformableVolumeMaterial.h)
- **事实：**当前 ovphysx 支持 volume TetMesh 与 surface triangle mesh，材料有 density、Young's modulus、Poisson ratio，surface 还涉及厚度和弯曲。可由外观网格生成模拟/碰撞网格，也能绑定多个外观网格。[ovphysx deformables](https://nvidia-omniverse.github.io/PhysX/ovphysx/latest/simulation_setup/deformables.html)
- **事实：**当前限制表明确：deformable/particle 不支持静摩擦，只支持动态摩擦；没有 deformable 接触报告，也有摩擦组合等限制。GPU convex 有顶点/面数限制，复杂硬物件需要分解或其他碰撞表示。[ovphysx limitations](https://nvidia-omniverse.github.io/PhysX/ovphysx/latest/guides/limitations.html)
- **推断：**跌落、短时挤压与夹持演示可以入选；长时间依赖静摩擦的握持稳定性与高刚度小形变精度，不应仅凭现成 demo 推定可靠。网格生成仍需处理开口、自交和坏单元，不是任意数字资产导入即成为准确物理物体。

### 3.3 完整物体、运动和采用证据

| 证据 | 核实内容 | 可以支持什么；尚缺什么 |
| --- | --- | --- |
| Teddy 完整资产 | 官方教程引用 `ISAACLAB_NUCLEUS_DIR/Objects/Teddy_Bear/teddy_bear.usd`，有材质与 deformable 配置。[当前文档](https://isaac-sim.github.io/IsaacLab/v3.0.0/source/concepts/deformables.html) | 完整外形和现成 USD 的明确入口；资产端点可用性、许可与具体贴图包未下载检查 |
| Teddy 夹起操作 | Isaac Lab 官方示例提供 `lift_teddy_bear.py` 状态机与软体任务；NVIDIA 官方文章另有机器人操作 teddy 的 GIF。[示例](https://isaac-sim.github.io/IsaacLab/v2.3.0/source/overview/simple_agents.html)、[teddy 动图](https://developer-blogs.nvidia.com/wp-content/uploads/2025/08/Deformableobjectsinsim.gif)、[图注来源](https://developer.nvidia.com/blog/developers-build-fast-and-reliable-robot-simulations-with-nvidia-omniverse-libraries/) | 完整物体/机器人互动的实现与视觉参考；GIF 未展示参数扫描，也未核与脚本逐帧对应关系 |
| E 对比动画 | 图注明确为**软球落到桌面**，E=`1e3 / 1e4 / 1e5 Pa`，同场景只改该量。[E 动图](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/volume_youngs_1e3_1e4_1e5.gif) | **已有参数对比动态资料**；它不是 teddy，具体生成后端未从多后端文档定位，本研究未完整播放评审 |
| ν 对比动画 | 图注为**软球落到桌面**，同场景三组 ν；正文明确端点 `0.10 / 0.49`，中值未在正文列出，描述侧向鼓出差异。[ν 动图](https://download.isaacsim.omniverse.nvidia.com/isaaclab/images/volume_poisson.gif)、[文档源码](https://raw.githubusercontent.com/isaac-sim/IsaacLab/main/docs/source/concepts/deformables.rst) | E/ν 可见效果的独立资料入口；不能将其写成 PhysX teddy 参数视频或未经确认归给某个具体后端 |
| 独立数据集采用 | TMLR 2026 论文 *Context-aware Learned Mesh-based Simulation via Trajectory-Level Meta-Learning* 报告用 Isaac Sim/PhysX 5.0 生成 FallingTeddyBear、MixedObjectsFall，后者含 teddy、bunny、gummy bear、两种 gummy worm、traffic cone；变化 E/ν。[作者论文](https://arxiv.org/abs/2511.05234)、[机构保存的论文](https://publikationen.bibliothek.kit.edu/1000190300/174459365) | 具体完整物体与材料变化的数据生产采用，强于品牌名气；完整生成脚本/资产下载尚未核成可立即复跑的包，也不是与真实物体的精度验证 |

**推断：**目前这组证据足以把 PhysX 放入主推荐竞争，而不把它缩成“仅刚体适用”。同时，它覆盖的是多个完整外形的物体，不足以证明 teddy/交通锥等全部内部分层、空腔和接触都已真实建模。

## 4. Genesis：当前 FEM 与公开 PBR 链路值得重新评价

### 4.1 不能用一个 Genesis 标签混合所有求解器

**事实：**Genesis FEM、MPM 直接输入 E/ν/ρ。FEM 有 linear、linear-corotated、stable-Neo-Hookean 等选择；默认 linear 的适用范围与大旋转表现不能等同于后两者。MPM 默认 fixed-corotated，另有 Neo-Hookean 选项。PBD 的“elastic”名称不自动代表同样的物理量定义。[软体理论](https://genesis-world.readthedocs.io/en/latest/user_guide/theory/soft_solvers.html)、[MPM Elastic API](https://genesis-world.readthedocs.io/en/v1.4.0/api_reference/engine/material/mpm/elastic.html)

| 接触/耦合路线 | 已核事实 | 本任务判断（推断） |
| --- | --- | --- |
| Legacy | 各子求解器分别推进，通过接触交换动量；有 influence softness、restitution、friction 等参数。[耦合说明](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/theory/coupling/index.html) | 适用于现成多物理 demo；不能自动视为精确的刚软强耦合，默认参数须固定并披露 |
| SAP + implicit FEM | 面向刚体/FEM 持续接触；要求 64 位精度与隐式 FEM，当前不接 MPM/SPH/PBD，部分关节约束有限制。[SAP 文档](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/theory/coupling/sap_coupler.html) | 适度形变的挤压/夹持值得进入备选；A800/4090 的实际耗时要以后续选中场景为准 |
| IPC | 接入 libuipc 的接触路线，支持较复杂软体接触，刚体以 ABD 表示；有 NVIDIA GPU/pyuipc 依赖。[IPC 文档](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/theory/coupling/ipc_coupler.html)、[例子目录](https://github.com/Genesis-Embodied-AI/genesis-world/tree/main/examples/ipc) | 与裸 libuipc 的差别主要是 Genesis 场景/资产/渲染工作流；不能把同一个底层求解器重复算成两份独立物理质量证据 |

### 4.2 现成完整资产的真实证据强度

- **事实：**官方 SAP 示例为 Franka 抓球：球半径 0.02，`E=1e5`、`ν=0.4`，linear-corotated、implicit FEM、64 位，施加夹持力后抬起。[示例源码](https://raw.githubusercontent.com/Genesis-Embodied-AI/genesis-world/main/examples/sap_coupling/franka_grasp_fem_sphere.py)。**推断：**这是受控参数/耦合能力证据，尚不足以支持丰富完整物体覆盖。
- **事实：**`elastic_dragon.py` 使用 `PBD.Elastic` 和 dragon OBJ。[示例源码](https://raw.githubusercontent.com/Genesis-Embodied-AI/genesis-world/main/examples/deformable/elastic_dragon.py)。**推断：**它是完整外形运动参考，不能当成现成 E/ν 参数演示。
- **事实：**官方 worm 有网格和 `bdy_Base_Color.png` 贴图，使用 MPM.Muscle、`E=5e5`、`ν=0.45`、`ρ=10000`，多个肌肉组主动驱动。[教程示例](https://raw.githubusercontent.com/Genesis-Embodied-AI/genesis-world/main/examples/tutorials/advanced_worm.py)。**推断：**它证明有带纹理的完整 MPM 物体与渲染；主动驱动和示例密度不能直接解释为被动食物或生物组织的可信材料模型。替换成被动弹性体属于后续改场景。
- **事实：**仓库网格目录包含 duck、dragon、worm、bunny、bathtub、boat、bolt_nut 等，支持常见 mesh/robot 导入格式。[资产目录](https://github.com/Genesis-Embodied-AI/genesis-world/tree/main/genesis/assets/meshes)、[载入资产](https://genesis-world.readthedocs.io/en/latest/user_guide/assets/loading_assets.html)。**推断：**网格文件数量是几何资产证据，不能当成每个物件都有 tet/材料/接触/PBR 预设的证据。
- **事实：**HybridEntity 可用刚性骨架配 MPM 软皮，暂不支持 FEM/PBD 皮层。[Hybrid 文档](https://genesis-world.readthedocs.io/en/latest/user_guide/physics/hybrid_entity.html)。**推断：**有机会覆盖硬软复合玩具或物件，仍需给出实际结构和连接；自动生成骨架不是内部真实结构的观测。

### 4.3 Nyx 已公开：安装可得与完整材质映射仍需分别看

**事实：**Genesis 主文档的 Nyx 页面仍写内部发布，而官方插件独立文档现在明确提供 `pip install gs-nyx-plugin`；插件拉取编译 renderer wheel `gs-nyx`。本研究按更新的插件官方安装页认定公开可安装。公开 wheel 不等于已确认渲染核心源码开放。[旧主文档](https://genesis-world.readthedocs.io/en/latest/user_guide/rendering/nyx_renderer.html)、[当前插件安装文档](https://genesis-embodied-ai.github.io/genesis-nyx/latest/user_guide/installation.html)、[官方仓库](https://github.com/Genesis-Embodied-AI/genesis-nyx)

**事实：**Nyx 插件明确支持 FEM 实时顶点、固定拓扑，以及 MPM 实体的蒙皮网格。GLB/GLTF 可保留内嵌材料；当前插件不支持 USD/DAE mesh 导入。它映射 Disney PBR，支持 diffuse/normal/emissive 贴图和 roughness/metallic 标量，但 Genesis Glass/Water 的透射、IOR、厚度等会丢失，输出目前主要为 RGB。[Nyx 支持表](https://genesis-embodied-ai.github.io/genesis-nyx/latest/user_guide/supported_features.html)

**事实：**Nyx 当前要求支持 Vulkan ray tracing 扩展的 GPU；NVIDIA 列出的对象为 RTX 系列。插件面向 Genesis 1.4.x，支持 Linux/Windows 与 Python 3.10–3.13，没有 CPU 渲染回退。[安装要求](https://genesis-embodied-ai.github.io/genesis-nyx/latest/user_guide/installation.html)

**推断：**对实体塑料、橡胶、玩具等不透明材质，Genesis FEM/MPM→实时变形网格→Nyx RGB 的公开链路已成立，适合 4090 做成片；玻璃/透明瓶的外观不宜据“PBR”一词宣称完整覆盖。当前缺口主要是本任务所需的多种完整物体物理资产与独立质量证据，而不是“高质量渲染尚未公开”。

**事实：**Genesis 1.4.5 于 2026-10-22 发布，具体维护包括非凸/凸分解碰撞、轨迹记录、缓存与场景重建修复。[发布记录](https://github.com/Genesis-Embodied-AI/genesis-world/releases)。**未核：**本次未找到足以支撑广泛日常软物件材料准确性的外部采用/测量结果；仓库活跃度不能替代该证据。

## 5. Newton：完整鸭子可用，需区分 VBD、MuJoCo 与 MPM

**事实：**Newton 是多求解器框架。VBD 支持 cloth/softbody，处于 experimental；其 SolverMuJoCo 的能力表不包含软体；ImplicitMPM 是单独的粒子路线。不能把某个子求解器的优点推广到所有对象。[求解器能力表](https://newton-physics.github.io/newton/stable/solvers/index.html)

### 5.1 VBD 与完整资产

**事实：**`ModelBuilder.add_soft_mesh` 接受 tet 网格，density 单位 kg/m³，`k_mu/k_lambda` 单位 Pa，支持单值或逐单元材料；阻尼另设。体积弹性能量有 Neo-Hookean 实现，表面/边的弹性默认另行控制。[Newton 1.7 ModelBuilder](https://newton-physics.github.io/newton/1.7.0/api/_generated/newton.ModelBuilder.html)。E/ν 通过前述 Lamé 公式输入，ρ 直接输入。

**事实：**官方 rubber duck 资产包含 `model.usda`、surface 与 tet；README 记录 surface 1002 vertices/2000 faces、tet 2734 vertices/11615 tets，surface 从更高分辨率网格简化，tet 经 fTetWild 生成。资产是开发者制作的 Apache-2.0 完整外形数据。[rubber duck 资产](https://github.com/newton-physics/newton-assets/tree/main/manipulation_objects/rubber_duck)

**事实：**`example_softbody_franka.py` 用 Franka 抓起/释放鸭子，VBD 处理软体；Featherstone 在此作为机器人运动学积分器。示例设 `density=100`、`k_mu=k_lambda=1e6`、10 substeps、5 iterations，自碰撞关闭。[示例源码与动态入口](https://github.com/newton-physics/newton/blob/main/newton/examples/softbody/example_softbody_franka.py)

**推断：**这是可定位数据和动作的完整 tet 物体示例，比“仅能跑方块”强；但该机器人行为不是一般双向动力学耦合的完整验证，鸭子均质体也不是空心壳。可作为 E/ν 对比的起点，现有 demo 本身没有展示这些参数的扫描。

### 5.2 接触与输出边界

- **事实：**VBD 是隐式 Vertex Block Descent，部分接触/约束采用 ALM，官方明确 experimental。[VBD 文档](https://newton-physics.github.io/newton/latest/solvers/vbd.html)
- **事实：**self-contact 与 rigid-soft DAT 默认关闭。rigid-soft DAT 的保护针对报告出来的接触，不是对所有非凸表面提供全局无穿透保证；接触溢出会丢约束，运动学刚体不按同样规则截断。[SolverVBD API](https://newton-physics.github.io/newton/stable/api/_generated/newton.solvers.SolverVBD.html)。**推断：**快压、薄部件或复杂夹持不宜直接沿默认值宣称稳定完整覆盖。
- **事实：**可输出 USD 时间采样场景供 DCC 使用，有 GL 捕帧及实验性 RTX viewer。对于加载的既有 ovstage，保留已写材质/灯光的路径更新刚体 transform，但不更新原有 deformable prim；另外提供可选的 deformable visual-mesh 显示路径。[可视化文档](https://newton-physics.github.io/newton/latest/guide/visualization.html)。**推断：**不能从“USD + RTX”直接推断现有带贴图鸭子每帧都正确更新；若用 USD→Blender，仍需确认变形网格、UV、法线与材质绑定。
- **事实：**Newton 1.7.0 于 2026-10-14 发布，有接触、资产加载、RTX 捕帧等更新；发布说明还修正了 MPM 材料缩放，要求旧调参场景相应调整 E/屈服/粘度值。[发布记录](https://github.com/newton-physics/newton/releases)。**推断：**这是持续维护的具体证据，也意味着材料比较要记录版本，不能混用旧 demo 参数与新版本轨迹。

## 6. MuJoCo：完整硬物体有价值，flex 要按模型范围使用

**事实：**当前 XML 所述默认 MuJoCo flex 使用 St. Venant–Kirchhoff 弹性有限元，允许有限旋转但面向小应变；直接配置 Young's modulus 与 Poisson ratio。2D 弹性还需要厚度；接触的 `solref/solimp` 与物体 E/ν 是不同设置。3.15 起另有实验 simplified Stable Neo-Hookean：仅针对非插值 3D flex，通过 `mjsFlex.elastic3d=1` 开启并要求 discrete integrator，发布说明明确尚未提供 MJCF 设置；不能用默认 StVK 概括所有新功能。[XML elasticity 参考](https://mujoco.readthedocs.io/en/latest/XMLreference.html)、[3.15 发布说明](https://github.com/google-deepmind/mujoco/releases/tag/3.15.0)

**事实：**flex 可以逐顶点运动，也可以 trilinear/quadratic 降阶表示，后两者减少自由度；flexcomp 的质量需按对象体积/总质量正确设定，不能把一个任意总质量直接读成材料密度。[deformable 建模说明](https://mujoco.readthedocs.io/en/stable/modeling.html#deformable-objects)

**事实：**仓库提供 bunny、bunny_shell、bag、basket 等 flex 模型。已核 `bunny.xml` 为 bunny surface mesh 加 trilinear 运动表示、`E=1e3`、`ν=0.1`、总质量 0.05，自碰撞关闭，有压板控制。[模型目录](https://github.com/google-deepmind/mujoco/tree/main/model/flex)、[bunny 源码](https://raw.githubusercontent.com/google-deepmind/mujoco/main/model/flex/bunny.xml)。**推断：**这是完整外形参数能力的起点，但降阶的局部细节不能等同于充分分辨的体积网格。

**事实：**当前 MuJoCo Warp 已有 flex 支持，官方标为 experimental、功能/性能未全部实现；“MuJoCo 的 GPU 完全不能软体”是过时判断。Newton 的 SolverMuJoCo 能力表与独立 MuJoCo Warp 的功能进度也应分开。[MuJoCo Warp 文档](https://mujoco.readthedocs.io/en/stable/mjwarp/index.html)、[项目仓库](https://github.com/google-deepmind/mujoco_warp)

**事实：**MuJoCo Menagerie 提供大量完整机器人/关节模型；MuJoCo 3.17.0 于 2026-10-22 发布，包括 flex 弯曲、自碰撞等具体更新。[Menagerie](https://github.com/google-deepmind/mujoco_menagerie)、[发布记录](https://github.com/google-deepmind/mujoco/releases)

**推断：**硬家具/硬玩具的整体推撞、带关节部件和固定冲量的 ρ 演示适合 MuJoCo，也适合 PhysX/Newton/Genesis 的刚体分支。若刚体模型下只改变 E/ν，几何运动不会因此产生材料应变；这不能用来判断上游 E/ν 是否合理。flex 可做小应变/有限旋转的补充，果冻般大压缩和食品复杂材料需另选本构。高质量 PBR 交付还需独立确认渲染/导出链，本次未把一般可视化等同于已完成后渲染。

## 7. MPM：只保留有额外用途的具体实现

| 实现 | 已核能力与现成数据 | 本任务位置（推断） |
| --- | --- | --- |
| **Genesis MPM** | 直接 E/ν/ρ；有完整 worm 蒙皮/纹理实例，MPM 固体可通过 visual mesh 交给 Nyx；域边界会约束粒子，时间步/子步需与材料匹配。[软机器人](https://genesis-world.readthedocs.io/en/latest/user_guide/physics/soft_robots.html)、[软体可视化](https://genesis-world.readthedocs.io/en/latest/user_guide/physics/beyond_rigid_bodies.html) | 大形变实体、现成纹理 MPM 资产的首个备选；主动肌肉示例需改成被动场景后才适合当前输入语义 |
| **Newton ImplicitMPM** | 隐式、稀疏网格，面向高刚度/完全非弹性行为，含 Drucker–Prager、屈服与粘度等；官方说明比传统显式 MPM 的材料范围更窄。E/ν 为物理参数，ρ 由质量/体积体现。[API](https://newton-physics.github.io/newton/stable/api/_generated/newton.solvers.SolverImplicitMPM.html) | 屈服/颗粒/粘塑性目标出现时有明显价值；不能仅凭“大时间步稳定”推导弹性完整物体最准确 |
| **Newton MPM 场景** | 现成 beam twist 用 E=`5e6`、ν=`0.45`、ρ=`1000`，为生成的梁；还有 snowball、granular、two-way coupling 等例子。[梁源码](https://github.com/newton-physics/newton/blob/main/newton/examples/mpm/example_mpm_beam_twist.py)、[目录](https://github.com/newton-physics/newton/tree/main/newton/examples/mpm) | 梁可作受控参数演示，场景目录不能当成多种日常完整物件资产库 |
| **Warp-MPM / PhysGaussian** | Warp-MPM 被 Neural Stress Fields/PhysGaussian 使用，基础示例偏沙柱/流柱。PhysGaussian 公开 Gaussian 数据入口、配置及视频生成命令，物理基于其 Warp-MPM。[Warp-MPM](https://github.com/zeshunzong/warp-mpm)、[PhysGaussian](https://github.com/XPandora/PhysGaussian) | 仅当需要复用已有 3DGS 完整外观时加入；其物理并不因为套了 Gaussian 外观而变成另一种已验证材料模型 |

**事实：**PhysGaussian 的 pillow2sofa 配置为 jelly、`E=1e4`、`ν=0.3`、`ρ=2000`、子步 `1e-4`，有内部填充设置。其 wolf 配置为 sand，因此“狼/枕头等完整外形”不等于那些真实物件的材料和内部结构已经建立。[pillow 配置](https://raw.githubusercontent.com/XPandora/PhysGaussian/main/config/pillow2sofa_config.json)、[wolf 配置](https://raw.githubusercontent.com/XPandora/PhysGaussian/main/config/wolf_config.json)

**事实：**PhysGaussian 预处理包含旋转、缩放到单位域、透明度裁剪和可选内部填充；实现中密度乘粒子体积形成质量，E/ν进入本构。[仓库流程](https://github.com/XPandora/PhysGaussian)、[求解器源码](https://raw.githubusercontent.com/XPandora/PhysGaussian/main/mpm_solver_warp/mpm_solver_warp.py)

**推断：**复用这些数据的价值是已有外观与视频脚本；成本在于物理尺度、填充和表面细节随粒子运动的合理性。已有 PBR 数字资产并不需要先转换成 3DGS 才能仿真。对于普通完整弹性物体，Genesis/PhysX/FEM 的网格路径更直接。未核这些旧配置与当前 CUDA/Warp 的兼容性及近期维护，不能给出安装即用结论。

## 8. 资产、成片与离线硬件成本

| 路线 | 物理数据准备 | 数字资产/PBR 与视频 | 相对成本判断（推断） |
| --- | --- | --- | --- |
| PhysX + Isaac 环境 | 从完整 teddy USD 起步最低；换物件仍需体积/碰撞网格与材料 | USD 渲染网格及材质环境现成；4090 做 RTX 有合理路径 | 现有示例到视频较直接，环境安装/资产下载较重 |
| PhysX SDK / ovphysx | SDK 自建场景；ovphysx 复用 USD；可读取 deformable 节点状态。[状态读取文档](https://nvidia-omniverse.github.io/PhysX/ovphysx/latest/read_write/readable.html) | 有渲染交接机制，但单独节点输出不等于渲染变形/材质已完成。[render handoff](https://nvidia-omniverse.github.io/PhysX/ovphysx/latest/tutorials/render_handoff.html) | 库更轻，视频链需明确选择及补齐 |
| Genesis FEM/MPM + Nyx | 自带资产可起步；新 mesh 的体积离散/颗粒填充仍重要 | 已核固定拓扑 FEM 与 MPM skin 更新；GLB/GLTF 材质路径直接，USD/透明材质受限 | 不透明物件端到端成本有优势；不能从公开 wheel 推断所有材质零损失 |
| Newton VBD + viewer/USD | 已有鸭子 surface/tet；其他资产需各自 tetrahedralize | USD导出可供 Blender；RTX 既有场景的 soft prim 更新需要另处理 | 物理起点明确，高质量外观输出需检查具体路径 |
| MuJoCo | 刚体/关节模型成熟；flex 模型与质量分布需设置 | 普通可视化直接；完整 deformable→PBR 导出本次未核 | 硬物件 ρ 演示便利；不因主物理方便而假定电影级外观已解决 |
| MPM/PhysGaussian | 内部粒子填充、域/网格分辨率、尺度 | 现有 Gaussian 可直接走项目渲染；普通 PBR mesh 需要蒙皮或表面重建 | 大形变与既有 3DGS 有价值；普通弹性物件增加数据与输出成本 |

**事实：**Isaac Sim 官方要求 RTX 能力，明确列出缺少 RT core 的 A100/H100 不受支持；Nyx 也要求 Vulkan 硬件光追能力。[Isaac Sim requirements](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/requirements.html)、[Nyx requirements](https://genesis-embodied-ai.github.io/genesis-nyx/latest/user_guide/installation.html)

**推断：**A800 可作为 CUDA 物理计算资源考虑，4090 更适合这些 RTX/Nyx 成片路径；不能因 A800 可运行 CUDA 就预设它可运行上述渲染器。实际 A800 型号、驱动、可用显存及两种机器的运行时间均未核。离线许可了更多子步、迭代和后渲染时间，因此不以机器人 RL 吞吐排名代替本任务选型。

**推断：**若改走 Blender，实际工作包括输出逐帧顶点/变换、保持 UV 与材质、正确驱动高分辨率外观网格、统一尺度/坐标以及编码帧。固定拓扑 FEM 比不断重建表面的粒子结果更容易保持已有贴图关系；这只是数据链复杂度判断，不是成片质量上限判断。

## 9. 可直接交给后续选片/复跑的场景建议

以下均为**推断/建议**；官方已有动画单列，代码参数可改与已有目标参数视频分开。

| 目标 | 优先资产/场景 | 参数与固定项 | 预期可见差异 | 当前证据状态 |
| --- | --- | --- | --- | --- |
| E | PhysX/Isaac teddy 下落或挤压；Newton duck 作为替代 | E=`0.1× / 1× / 10×`；ν、ρ、几何、初始速度、摩擦、阻尼、动作固定。可参考官方软球 `1e3/1e4/1e5 Pa` 示例 | 落地压扁、回弹形变、接触面积；仅“跳得高”还受阻尼影响 | Isaac 有官方软球 E 对比 GIF；teddy/duck 的这组参数视频待复跑 |
| ν | teddy 或完整均质软体受压，镜头同时看正侧面；简单球作为受控补充 | `0.10 / 0.30 / 0.45`，若实现收敛可加入 `0.49`；E/ρ及压板位移曲线固定 | 侧向鼓出及体积变化 | Isaac 有官方软球 ν 对比 GIF；Genesis SAP 球有接触和物理量代码，完整物体的这组参数视频待复跑 |
| ρ | 同外形完整硬物件受到相同冲量，撞击相同参照物；或软体悬挂/承载 | `0.25× / 1× / 4×`；固定几何、重力、施力/冲量、E/ν、摩擦与阻尼 | 固定冲量后的速度、撞后参照物运动；软体自重下垂 | 框架有质量/密度输入；本次未找到已核同条件 ρ 对比视频 |
| 硬软混合 | Genesis FEM 与刚性夹爪；PhysX teddy 抓放 | 使用真实体积材料与明确附件/接触，目标参数逐项改变 | 软部件压缩、夹持滑移、释放恢复 | 有现成示例；复杂物件的结构迁移仍需建模 |

**推断：**其他非目标物理参数采用示例默认值并在比较中保持固定。高 E/高 ν 的组若需要足够子步或迭代，应使用能覆盖该组的统一数值配置；不能为每组另调阻尼/接触来“做漂亮”。本次研究不增加额外安装或全量验收环节，具体执行由选中的场景决定。

## 10. 主要缺口与条件性替代

1. **完整外形与完整结构之间仍有差距。** 当前最强现成数据为 PhysX/Isaac teddy、Newton tet duck、Genesis dragon/worm、MuJoCo flex bunny；它们各自只能支持明示的均质、主动、PBD或降阶假设。多层肉/水果、真实填充枕头、空心玩具不能凭同类外形自动纳入。
2. **薄壁覆盖成立，塑料瓶可信行为待定。** PhysX/ovphysx 已有 deformable surface，MuJoCo 有 2D flex；Genesis IPC 与 Newton 也有 surface 路线。但完整瓶需要曲面初始状态、厚度、封闭空腔/压力及可能的塑性，E/ν/ρ 与一张表面不足以确定。当前只记录覆盖，保持该分支边界。
3. **实测质量排名仍缺同场景证据。** 未发现足以把这四个框架按完整日常物体材料精度统一排序的公开比较；因此推荐按已有对象、物理模型、接触限制和输出链，而不是品牌或 FPS。
4. **PhysX 是强竞争项，接触限制有具体触发条件。** 若目标是持续静摩擦夹持或精细接触压力，应优先比较 IPC/离线 FEM；若主要是完整软体跌落/受压和直接成片，PhysX 的资产与视频链更有现成证据。
5. **Genesis 是真实可行的输出备选。** 当前公开 Nyx 已支持变形数据更新；下一步若选它，应从 FEM/具体 coupler 定位一个完整网格对象，而不是用 dragon=PBD、worm=muscle 的视觉展示代替 E/ν 完整物体证据。
6. **MPM 保持条件性。** 屈服、极大形变或已有 Gaussian/MPM 数据使其收益明确；普通弹性完整物体优先保留直接网格路径。Newton ImplicitMPM 的稳定性声明不等于大时间步仍有高动态精度。

交付边界：本文件为公开资料与源码研究；未改工单、地图或规格，未安装复跑，也未将“未核”环节写成已验证能力。
