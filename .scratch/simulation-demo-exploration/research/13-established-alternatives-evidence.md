# 成熟产品扩搜：PolyFEM、SOFA、Chrono、MuJoCo 与行业参照

核查日期：2026-11-01。承接《知名仿真产品的工程适配与完整物体案例》，范围为正向仿真、完整物体及视频。Newton／Genesis 由同票另一份研究负责；PhysX 按用户选择排除。本文只读取官方文档、作者页面、代码、公开包元数据；视频标注的是页面／HTTP 可访问性，未播放影片，也未安装或复跑。`main`、`stable` 链接反映核查时状态，具体执行版本留给选定后的复跑工单。

**建议：在现有三个起点之外，优先增加 SOFA，按场景增加 Chrono 和 MuJoCo。PolyFEM 保留为弹性连续体与接触精度较易解释的候选，重新定位为有针对性的数值路线。** SOFA 的新增价值是完整结构和现成 Blender 制作示例；Chrono 的价值是机械、杆壳、轮胎及工程验证；MuJoCo 的价值是硬物件、机构、日常场景与质量／惯性演示。这些增益各自对应下文具体实现，尚未构成几个框架对同一软物体的精度或速度排名。

## 1. 广搜后的筛选

| 产品类别 | 产品／实际路线 | 与当前目标直接相关的价值 | 本轮取舍 |
| --- | --- | --- | --- |
| 连续体／研究工程库 | **PolyFEM＋内置 IPC** | E／ν／ρ、体网格、多材料、非线性接触、高阶位移与曲面；有 Armadillo、螺栓、球等配置。维护来源主要为 NYU／UVic 研究团队。[功能与组织](https://polyfem.github.io/)、[接触集成](https://polyfem.github.io/details/contact/) | 保留重点比较。其说服力来自可查材料方程、离散对照及公开场景；通用产品式资产、成片便利和工业采用证据较少。 |
| 交互力学／软机器人／医疗 | **SOFA＋SoftRobots／BlenderToolbox** | 现成气腔、加硬层、压力驱动、软硬接触；已有 .blend、贴图和动画导入导出脚本。[SoftRobots](https://www.sofa-framework.org/applications/plugins/softrobots/)、[Blender 插件](https://www.sofa-framework.org/applications/plugins/export-in-blender/) | **最值得新增的结构化软体候选**。按具体力场确定本构；漂亮示例的物理近似见 §3。 |
| 多体／车辆／工程机械 | **Project Chrono：FEA＋SMC／NSC；Vehicle** | 有体单元、ANCF／Reissner 壳、梁／绳、柔性轮胎与关节；现成 Python 和工程验证资料。[FEA](https://api.projectchrono.org/group__chrono__fea.html)、[轮胎分类](https://api.projectchrono.org/wheeled_tire.html) | **条件新增**：完整物体涉及薄壁结构、轮胎、杆或机械装配时收益明显；普通水果／玩具软体暂不优先。 |
| 机器人／硬物件交互 | **MuJoCo＋robosuite／RoboCasa** | pip 安装；大量公开硬物件、家具、关节模型、贴图、动作和视频脚本；ρ 可通过质量／惯性进入运动。[Python](https://mujoco.readthedocs.io/en/stable/python.html)、[RoboCasa](https://github.com/robocasa/robocasa) | **条件新增**：硬物件、机构与 ρ 展示。flex 另按其真实材料与降阶条件评价，机器人生态成熟度不自动转移到大应变软体。 |
| 生物力学连续体 | **FEBio／FEBio Studio** | 有超弹性、纤维、粘弹性、双相、损伤等现成实现；开放膝关节模型含材料与载荷。源码 MIT，免费二进制另有自定义许可；可用 Studio 或 `.feb` 输入。[许可](https://febio.org/)、[solid 模块](https://febiosoftware.github.io/febio-docs/features/modules/module_solid/)、[膝模型](https://repo.febio.org/permalink/project/118) | 专项保留：物件确需纤维／组织模型时增加价值。本次优先级低于通用弹性案例；多出的本构参数与组织结构需要已知依据。 |
| 工程多物理应用框架 | **MOOSE／Solid Mechanics** | INL 的核能工具 BISON、SAM、Pronghorn 等有实际集成，含热力耦合与材料能力；验证文档覆盖解析解、制造解及收敛分析。[NEAMS 集成清单](https://neams.inl.gov/code-descriptions/)、[Solid Mechanics 要求及验证](https://mooseframework.inl.gov/bison/modules/solid_mechanics/sqa/solid_mechanics_srs.html) | 已搜，暂不深入：其优势对应工程多物理问题与应用开发，当前缺少能缩短“完整日常物件→材质视频”路径的案例证据。 |
| 机器人系统仿真 | **Gazebo** | 成熟的机器人系统和传感器／环境组织层，实际动力学经 DART、Bullet 等插件提供。[官方引擎插件说明](https://gazebosim.org/api/physics/8/physicsplugin.html) | 已搜，暂不深入：追加系统平台对本轮 E／ν／ρ 体材料场景的收益有限，仍需选择具体引擎和材料实现。 |
| 通用实时物理库 | **Bullet／PyBullet** | 开源刚体、软体与锚定例；官方例中可分别选 mass-spring 和 Neo-Hookean 路径，参数应逐例追踪。[软体锚定代码](https://github.com/bulletphysics/bullet3/blob/master/examples/pybullet/examples/deformable_anchor.py) | 已搜，暂不深入：有可用软体能力；此次未找到相对 SOFA／现有候选更强的完整成片、E／ν／ρ 量值参照组合。 |
| 影视／内容制作 | **Blender** | 材质、动画、渲染与导出成熟；内置 soft body、cloth、Mantaflow 液体有不同求解与调节语义。[soft body 定位](https://docs.blender.org/manual/en/latest/physics/soft_body/introduction.html)、[液体黏度](https://docs.blender.org/manual/en/latest/physics/fluid/type/domain/liquid/diffusion.html) | **主要作为后渲染与资产制作工具**。传统 soft body 侧重次级运动，现成字段到体材料 E／ν 的对应证据不足。开发者文档另有 XPBD 布／杆能力，本轮未核其稳定发布状态，不据此推荐新体软体路线。[XPBD 开发文档](https://developer.blender.org/docs/features/nodes/xpbd_solver/) |

商业软件按工单范围只作行业参照：Ansys／Abaqus／COMSOL 对应工程连续体与多物理产品类别，Houdini 对应特效制作与成片工作流。其品牌、案例与材料库可帮助理解产品分工；本票实际复跑选择使用上述开放路线。

## 2. PolyFEM：保留材料与数值依据，降低“现成产品套装”的期待

**维护与采用。** 官网明确由 NYU Geometric Computing Lab 和 University of Victoria 开发，MIT 许可；2026-10-30 主分支仍有合并活动。[官网](https://polyfem.github.io/)、[当前提交](https://github.com/polyfem/polyfem/commit/ddadb5398f0fae2e825dedcf754aa7dd00bc3bb0)。公开采用包括高阶 IPC、DiffIPC 及 Cellogram 等科研项目；这些证明其被真实用于方法和应用研究，所核来源未给出与 Chrono 的商业结构产品、SOFA 的医疗产品同等具体的量产工程集成证据。[高阶 IPC](https://zferg.us/research/high-order-ipc/)、[DiffIPC](https://huangzizhou.github.io/research/diffipc.html)、[Cellogram](https://cellogram.github.io/)。官网的 Adobe／nTopology 出现在资助说明中，应按资助证据理解。

**目标物理量与说服力。** LinearElasticity、StVK、NeoHookean 都有 E／ν 入口，动态配置另有 ρ；Mooney–Rivlin／Ogden 则使用额外系数。高阶 IPC 的梁、垫子和 Armadillo 对照针对粗网格假硬、折叠细节及碰撞表示，属于同模型下的离散质量证据；可帮助解释“为什么这个形变值得信任”，并不证明任意现实材料只凭 E／ν／ρ 就能匹配。[材料方程](https://polyfem.github.io/details/formulations/)、[高阶对照](https://zferg.us/research/high-order-ipc/)。

现实参照还有原始 IPC 的泡沫练习球撞板：直径 0.04 m、E=10⁷ Pa、ν=.45、ρ=1150 kg/m³、初速 67 m/s，并给高速摄影对应帧。它是 **IPC 原实现／特定积分器与步长**的实验参照；PolyFEM 的[球场景](https://github.com/polyfem/polyfem-data/blob/main/contact/examples/3D/golf-ball.json)可作为复用起点，当前版本与原实验条件一致性留到选定后确认。[原作者结果](https://ipc-sim.github.io/)

**上手与成片。** CMake 构建后以 JSON 运行，VTU／PVD 可看时序结果，逐帧表面到 Blender 的材质与动画链仍需准备。[构建](https://polyfem.github.io/cxx_index/)、[运行与输出](https://polyfem.github.io/tutorials/getting_started/)。官网 Python 包名是 `polyfempy`；conda 元数据最高版本为 0.5.2，查询到的包文件发布于 2023 年，不能据首页一条安装命令推断其覆盖 2026 年主分支功能。[官方包元数据](https://api.anaconda.org/package/conda-forge/polyfempy)。PyPI 的同名 `polyfem` 是另一个作者的网格项目，后续安装应使用官方链接。[同名包信息](https://pypi.org/project/polyfem/)

**入围的具体理由：** 对完整弹性物体，现有公开体网格、材料定义、接触和精度改进路径较连贯。高阶 Armadillo 是优先观看项，E 的受力形变、ν 的横向应变／体积变化、ρ 的惯性响应可据此组织。其余完整结构应另确认空腔、材料分区和连接，PBR 外观需另准备。

## 3. SOFA：新增价值最大，但要读清每个漂亮案例的物理表示

**维护与真实集成。** SOFA 自 2006 年持续开发；Inria 承载的 consortium 明确负责发行、维护和贡献审查。当前 v26.06.00 于 2026-07-15 发布，带平台二进制，SofaPython3 需匹配所下载包的 Python 版本。[治理](https://www.sofa-framework.org/consortium/presentation/)、[发行](https://github.com/sofa-framework/sofa/releases/tag/v26.06.00)。核心 LGPL；SoftRobots 也提供开放组件。[SOFA](https://www.sofa-framework.org/)、[SoftRobots](https://www.sofa-framework.org/applications/plugins/softrobots/)

实际企业采用可具体到 InSimo：其官方访谈说明以 SOFA 为医疗／手术仿真基础，使用 FEM 和直接求解器，并把自有的多线程及矩阵装配优化回馈核心；HelpMeSee 是其明确提到的应用。该证据支持交互生物力学工程积累，产品内部模型与商用案例资产另有边界。[InSimo 一手说明](https://www.insimo.com/32077/crossed-interview-sofa-2/)。近期 InSimo 仍展示 Sim&Care 和 ROBOTiS，属于持续产品活动，不把产品宣传中的“高保真”自动赋予开源示例。[2024 SOFA Week 厂商说明](https://www.insimo.com/40339/insimo-sofa-week-2024/)

**完整结构案例：PneuNet 三指夹爪。** [完整 step7 场景](https://github.com/SofaDefrost/SoftRobots/blob/master/examples/tutorials/PneunetGripper/details/step7-grabTheCube.py)含软指、加硬层、气腔、压力驱动、刚性方块和摩擦接触；[网格目录](https://github.com/SofaDefrost/SoftRobots/tree/master/examples/tutorials/PneunetGripper/details/data/mesh)含体 VTK、气腔与外皮 STL。`TetrahedronFEMForceField(method='large')` 是弹性材料＋大旋转的实现，示例直接设 `youngModulus`／`poissonRatio`，质量为 `UniformMass(totalMass=...)`。ρ 可由选定物理体积换算总质量；若需要按非均匀体网格体积分配节点质量，应选密度质量组件，而非仅给每节点相同质量。单位体系应按示例的毫米、重力和质量一起解释。[作者逐步说明](https://github.com/SofaDefrost/SoftRobots/blob/master/examples/tutorials/PneunetGripper/pneunetgripper-tuto.md)

这个场景的价值是 **结构完整且现有实现可复用**：固定压力时观察弯曲与抓取，改变 E 时可同时记录位移、反力或抓持状态。教程提供真实弯曲照片；本轮所核资料未定位该 step7 全模型的误差曲线或已录 E／ν／ρ 参数对比，故将其标为“代码支持调参，效果待复跑”。大压缩或强非线性材料应采用 SOFA 已有合适力场与对应案例，避免从 `method='large'` 推导任意大应变的材料准确性。[力场文档](https://sofa-framework.github.io/doc/components/solidmechanics/fem/elastic/tetrahedronfemforcefield/)

**带材质成片案例：Dice Salad。** 官方收录的 [Sofa.BlenderToolbox](https://www.sofa-framework.org/applications/plugins/export-in-blender/) 提供动画导出和 Blender 导入。作者仓库确有 [scene.py](https://github.com/SofaComplianceRobotics/Sofa.BlenderToolbox/blob/main/example/scene.py)、[diceSalad.blend](https://github.com/SofaComplianceRobotics/Sofa.BlenderToolbox/blob/main/example/diceSalad.blend)（约 16.8 MB）、碗／骰子／圆柱 OBJ、`d20.png` 贴图及[结果 MP4](https://github.com/user-attachments/assets/3aa80034-c865-4510-b93b-979e02fead4d)。工具 MIT，README 写明导出依赖 SofaValidation、Python `toml`，导入示例基于 Blender 3.6，并有 Blender 4+ 导入开关。[README](https://github.com/SofaComplianceRobotics/Sofa.BlenderToolbox)

这里有一个影响材料判断的具体近似：软骰子 `JellyD20` 用 **6×6×6 节点、[-20,20]³ 的规则立方体网格**作物理体，`HexahedronFEMForceField(E=5, ν=.49)`、`totalMass=.05`；骰子显示面和粗碰撞面通过 BarycentricMapping 跟随它。因而这份现成数据直接证明的是软硬物体互动与完整外观制作链，**骰子外形与承载质量／刚度的体积并未一致建模**。它适合先观看制作效果；若选为目标量值场景，需使物理几何符合该物体，并确定单位、本构和接触，不能把演示中的 E=5 直接当现实果冻骰子的材料值。[场景源码](https://github.com/SofaComplianceRobotics/Sofa.BlenderToolbox/blob/main/example/scene.py)

**近期取舍：** SOFA 是“现成功能足以表达多区域、气腔、驱动和接触”的强新增候选。PneuNet 优先承接结构与材料演示；Dice Salad 优先承接外观和视频工作流。二者的证据用途分开后，SOFA 的新增价值更具体。

## 4. Chrono：工程采用和可解释结构响应突出，近期按物体类型启用

**维护、许可与使用。** UW–Madison／Parma 共同牵引，BSD-3；10.0.0 于 2026-04-07 发布。官方有 Conda PyChrono 二进制、Python FEA 示例和 C++ 源码。[项目](https://www.projectchrono.org/)、[发行](https://github.com/projectchrono/chrono/releases/tag/10.0.0)、[安装](https://api.projectchrono.org/pychrono_installation.html)。预编译 Python 包的模块覆盖有限，文档明确 CASCADE、VSG3D、ROS 等仍可能需源码构建，GPU 也应按 DEM／FSI 等具体模块核实，不能把所有 FEA 当作 GPU 求解。

工程采用有超越用户 logo 的证据：**QBlade 官方说明其完整结构模型基于 Project Chrono**，用于风机的气动／伺服／水动／弹性系统分析。此处 QBlade 只作为 Chrono 的真实集成证据；复跑仍直接使用开放 Chrono。[QBlade 官方说明，Acknowledgements](https://qblade.org/)。Chrono 用户页还列 DualSPHysics／GPUSPH 多体集成，以及政府实验室和企业；对仅列名称、未写具体能力的项保持原证据粒度。[用户与集成](https://www.projectchrono.org/testimonials/)

**E／ν／ρ 的实际入口。** `ChContinuumElastic` 明确是各向同性 Hooke 弹性，提供 `SetYoungModulus`、`SetPoissonRatio`、`SetDensity`，并可输出应力；其工程梁／壳另有截面、厚度和层材料输入。[材料 API](https://api.projectchrono.org/classchrono_1_1fea_1_1_ch_continuum_elastic.html)。官方[Python 接触例](https://github.com/projectchrono/chrono/blob/main/src/demos/python/fea/demo_FEA_contacts.py)同时建体 FEM、柔性绳和刚体，并分别设置**体材料 E**与 **SMC 接触材料 E**；后者用于接触罚力，参数对比时需要固定并单独记录。

**完整轮胎例。** [demo_FEA_abaqus_wheel.cpp](https://github.com/projectchrono/chrono/blob/main/src/demos/fea/demo_FEA_abaqus_wheel.cpp)加载公开拖拉机轮胎四面体 `.INP`，设置 E=16 MPa、ν=.4、ρ=1000；连接刚性轮辋、地面与障碍物，使用节点接触表面。其[数据目录](https://github.com/projectchrono/chrono/tree/main/data/models/tractor_wheel)有粗／细体网格、轮胎与轮辋 OBJ。这里 `.INP` 是读取的公开网格格式，运行使用 Chrono；该例当前走 C++，并涉及 PardisoMKL／可视化组件，准备成本高于直接运行 PyChrono 小例。运行视图以 FEM 量值着色为主，未查到同例可直接使用的 PBR 材质／Blender 成片包。

不要混用不同轮胎模型：Chrono 同时提供刚性、半经验 handling 和 FEA 轮胎，只有相应 FEA 实现才模拟体／壳形变；ANCF、Reissner、实体轮胎及土壤之间也有具体兼容范围。[官方轮胎方法表](https://api.projectchrono.org/wheeled_tire.html)

**质量依据与成本判断。** 官方汇集核心多体与 ADAMS 对照、颗粒实验、ANCF 梁／壳和 brick 验证。这种记录对梁挠度、振动、薄壳／轮胎的材料响应有实际帮助；每份验证限于其模型与条件。[验证入口](https://www.projectchrono.org/validation/)。本轮建议它进入 **机械／杆壳／轮胎条件候选**，而非仅因工程采用广就成为水果、肉、枕头的默认方案。

## 5. MuJoCo：日常完整资产与上手便利有明确增益

Google DeepMind 维护，Apache-2.0，当前 3.17.0 于 2026-10-22 发布；`pip install mujoco` 包含原生库与 Python viewer。[仓库](https://github.com/google-deepmind/mujoco)、[发行](https://github.com/google-deepmind/mujoco/releases/tag/3.17.0)、[安装](https://mujoco.readthedocs.io/en/stable/python.html)。公开实际集成包括 DeepMind 的 dm_control／MJX Playground，以及 UT Austin／Stanford 等的 robosuite。Playground 有训练后实机迁移视频；该证据对应机器人控制与接触任务。[Playground](https://playground.mujoco.org/)、[robosuite](https://robosuite.ai/)

**比机器人模型库更贴近本任务的现成资产是 RoboCasa。** 它基于 robosuite／MuJoCo，覆盖厨房硬物件、可开合家具和器具；当前 RoboCasa365 页面提供大量纹理、家具、食物与容器外观，公开代码、演示回放和视频输出脚本。代码 MIT，资产与数据为 CC BY 4.0；README 提示资产下载约 10 GB。[官方案例](https://robocasa.ai/)、[安装／回放／许可](https://github.com/robocasa/robocasa)。这些食物模型在该路线中主要承担可抓取硬物件的角色，食品外观本身不构成软体材料依据。

**参数适配。** 刚体的密度／质量／惯性决定受力与碰撞响应，适合固定冲量、同一外力下推移或与其他物体动量交换；ρ 的入口还需核现成 MJCF 是否写死 `inertial`／`mass`，保持质量与惯性一致。E／ν 要使用 flex 等变形实现：当前默认 flex 弹性为 StVK，官方限定有限旋转、小应变；`elasticity young/poisson` 是直接物理量，`mass` 则需要按物理体积换算 ρ。[XML 材料与质量说明](https://mujoco.readthedocs.io/en/stable/XMLreference.html)、[flex 建模](https://mujoco.readthedocs.io/en/stable/modeling.html#deformable-objects)

当前有[带 UV 和海绵贴图的 bunny](https://github.com/google-deepmind/mujoco/blob/3.17.0/model/flex/bunny_with_uv.xml)，但它使用 `dof='trilinear'`：以包围盒角点的少量自由度驱动形变，不能因为兔子网格很细就认为局部体形变同样精细。官方[full／trilinear 夹爪垫对照视频](https://www.youtube.com/watch?v=qJFbx-FR7Bc)与[3.3.0 说明](https://mujoco.readthedocs.io/en/stable/changelog.html#version-3-3-0-feb-26-2025)清楚展示此取舍。3.15 另加入实验 simplified Stable Neo-Hookean，限定非插值 3D flex、`mjsFlex.elastic3d=1` 和 discrete integrator，发布说明写明尚无 MJCF 设置。[发行说明](https://github.com/google-deepmind/mujoco/releases/tag/3.15.0)

**成片成本。** MuJoCo 自带离屏渲染可直接录视频；RoboCasa 已有回放和 `video_path` 使用例。robosuite 当前“photorealistic rendering”文档另接 Isaac Sim，默认 MuJoCo 贴图画面与这条附加渲染链应分别理解，本票近期入口采用默认渲染；Blender 品质目标仍需核实独立导出与材质准备。[RoboCasa README](https://github.com/robocasa/robocasa)、[robosuite 渲染文档](https://robosuite.ai/docs/modules/renderers.html)

## 6. 框架 × 效果 × 已有实现

下表是广度盘点，保持固体主线。`已有` 指官方源码／配置可定位；`插件` 指需另外组件；`待核` 表示本轮证据范围，不是断言框架永远不支持。多个模块出现在同一框架中，只有列明的耦合路径才视为已有耦合。

| 框架 | 体变形／刚软接触 | 布／薄壳 | 杆／植物 | 颗粒 | 黏性流体 | 耦合与 ρ 互动 |
| --- | --- | --- | --- | --- | --- | --- |
| **PolyFEM** | 已有：体 FEM＋IPC；Armadillo、螺栓、球 | 教程把受模拟薄壳列为当前缺口；薄障碍物接触另算 | 三维实体可做细长体；专门杆／植物案例待核 | 多个独立弹性体接触；工程颗粒材料库待核 | Stokes／Navier–Stokes 已实现；蜂蜜现成场景待核 | 多弹性体／障碍物接触已有；上述流体与软体双向 FSI 待核 |
| **SOFA** | 已有多种 FEM 力场、刚体、映射和约束；PneuNet／Dice Salad | 有三角形膜／弯曲组件及 ElasticShell 插件路线；完整布料参数场景待核 | BeamAdapter／Cosserat 插件；Dice Salad 带梁，导管／线缆案例；完整植物资产待核 | 本轮未核高密度颗粒成熟配置 | 本轮未核蜂蜜等现成流体路径 | 软指气腔压力＋刚性物体、刚柔映射已有；通用软体入液双向耦合待核 |
| **Chrono** | 已有体 FEA、SMC／NSC 接触及刚体机构；拖拉机轮胎／Python contact 例 | 已有 ANCF／Reissner 壳及柔性轮胎；有 shell demos | 已有 Euler／ANCF／IGA 梁与 cable demos；植物材料和资产待核 | DEM 模块；FSI 中另有 CRM 连续颗粒路径；角堆／铲挖例 | FSI::SPH：黏性流体、Couette／Poiseuille 等例 | FSI 已有 object drop、flexible cable／plate／toroidal tire；具体模块与兼容类型需随例选定 |
| **MuJoCo** | 刚体／关节原生；flex 1D／2D／3D 与接触；full／trilinear 分开 | 2D flex、cloth／bag／bunny_shell 等模型 | 1D flex 的离散杆弯曲，以及 elasticity cable 插件；完整植物资产待核 | 可复制刚体颗粒；本轮未核工程级大规模颗粒场景 | 有环境流体阻力／升力模型；自由表面液体求解待核 | 刚软接触／柔性夹爪已有；流体力模型不等于解析软体入液的自由表面 FSI |
| **FEBio** | 原生有限应变 FEM、接触、复杂组织材料；开放膝关节 | shell／membrane 与相关材料组件 | 有 beam／truss 材料；植物模型待核 | 本轮未核颗粒材料演示链 | 有 fluid／FSI／biphasic 等模块和材料 | 双相组织、流固与多相能力已列官方模块；当前重点为组织案例，视频/PBR路径另核 |
| **Blender** | Bullet 刚体；传统 soft body；外接 SOFA 等结果可渲染 | 原生 cloth；XPBD 开发文档另有布约束 | hair 动力学、XPBD 杆文档；具体发布与植物参数映射待核 | 多刚体／粒子系统可做视觉颗粒；材料标定依据另查 | Mantaflow：运动黏度、表面张力、高黏度选项 | 刚体、布／软体碰撞及液体障碍物已有；通用双向软固液耦合待核 |

矩阵主要依据：[PolyFEM 方程](https://polyfem.github.io/details/formulations/)、[IPC 教程限制](https://polyfem.github.io/tutorials/ipc_quick_start_guide/ipc_quick_start_guide/)；[SOFA 插件目录](https://www.sofa-framework.org/applications/plugins/)；[Chrono 模块](https://api.projectchrono.org/modules.html)、[FSI::SPH](https://api.projectchrono.org/group__fsisph.html)、[FSI 示例目录](https://github.com/projectchrono/chrono/tree/main/src/demos/fsi/sph)、[FEA 示例目录](https://github.com/projectchrono/chrono/tree/main/src/demos/fea)；[MuJoCo flex](https://github.com/google-deepmind/mujoco/tree/3.17.0/model/flex)、[流体力说明](https://mujoco.readthedocs.io/en/stable/computation/fluid.html)；[FEBio 功能](https://febiosoftware.github.io/febio-docs/features/modules/module_solid/)；[Blender 物理手册](https://docs.blender.org/manual/en/latest/physics/index.html)、[液体扩散／黏度](https://docs.blender.org/manual/en/latest/physics/fluid/type/domain/liquid/diffusion.html)。

## 7. 直接观看与公开资产对应

以下均为**作者已有演示**。本轮核查的是链接、文字说明、代码及资产目录；运动描述按作者说明或场景配置，未把未播放影片写成观看结论。

| 观看入口与定位 | 实际求解／动作／证据用途 | 公开配套与缺口 | 本轮访问状态 |
| --- | --- | --- | --- |
| **PolyFEM：高阶 Armadillo**。[官方影片](https://www.youtube.com/watch?v=YpXX6lPs0gM)；[作者页](https://zferg.us/research/high-order-ipc/)的 `Video`，并参看 `Armadillo-rollers` 对照 | PolyFEM 高阶体 FEM＋IPC；滚轮挤压完整 Armadillo，对比离散与碰撞表示。已有**数值方法对照**，E／ν／ρ 对比待复跑 | [数据仓库](https://github.com/zfergus/high-order-ipc-data)含 MSH、碰撞 OBJ、HDF5 映射及 JSON。论文场景字段有版本依赖；未核原片 .blend／PBR 材质 | 作者 HTML 200，核到嵌入 ID；未播放 YouTube |
| **PolyFEM：螺栓／螺母**。[直达 GIF](https://polyfem.github.io/tutorials/screw/full_sim.gif)，[教程](https://polyfem.github.io/tutorials/screw/screw/) | 体 NeoHookean 螺栓＋刚性螺母障碍物＋IPC；有全规定运动及轴向自由等不同配置。适合装配接触演示，E／ν 可见性需另选加载 | [完整配置／网格仓库](https://github.com/DuoZhangRobotics/Screw_Polyfem_Tutorial)公开；钢参数 E=2×10¹¹、ν=.3、ρ=8050。材质外观包未核 | GIF HEAD 200，`image/gif`，约 3 MB |
| **SOFA：Dice Salad**。[直达 MP4](https://github.com/user-attachments/assets/3aa80034-c865-4510-b93b-979e02fead4d)，[官方位置](https://www.sofa-framework.org/applications/plugins/export-in-blender/) | SOFA HexahedronFEM＋刚体／梁与接触，碗中硬／软骰子互动；Blender 后渲染。**仅有视觉／制作链参考**，网格近似详见 §3 | [example](https://github.com/SofaComplianceRobotics/Sofa.BlenderToolbox/tree/main/example)含场景代码、OBJ、纹理、成片 .blend；导出／导入脚本公开。物理体由代码生成，量值依据需补 | HEAD 200，`video/mp4`；未播放 |
| **SOFA：SoftRobots 综述演示**。[官方视频](https://www.youtube.com/watch?v=PbfvtGmtxZQ)，[作者插件页](https://www.sofa-framework.org/applications/plugins/softrobots/)的内嵌视频 | 实时直接／逆 FEM 的软机器人控制；用于看完整软机器人互动。该综合影片与 PneuNet step7 的逐镜头对应未核 | PneuNet 完整体／气腔／外皮网格和 Python 配置公开；综合片中其他模型不能一概视为同样公开 | 作者 HTML 200，核到嵌入 ID；未播放 YouTube |
| **Chrono：Tire**。[直达官方嵌入影片](https://www.youtube.com/watch?v=P-1VZoBnD_c)，[Gallery](https://www.projectchrono.org/gallery/)中 `Tire` | 作者说明为轮胎与不同粒径颗粒互动。页面未明示该历史影片的具体轮胎本构／求解模块，故只作为**效果参考**，不作为 FEA 参数响应证据 | 当前 `demo_FEA_abaqus_wheel` 的代码／体网格公开，但它与这段旧影片并未建立同一配置对应；影片渲染／配置待核 | 官方 HTML 200，核到标题和 ID；未播放 YouTube |
| **Chrono：VIPER 铲挖**。[官方视频](https://www.youtube.com/watch?v=rDK4ZNB3yKg)，[Gallery](https://www.projectchrono.org/gallery/)中 `low V-shaped blade` | 完整月球车＋颗粒地形＋低置 V 形铲。页面确定 Chrono 场景，具体 DEM／CRM 版本及参数未披露；用于看机械与颗粒广度 | Chrono 有 rover／颗粒／FSI 公开实现；本片独立完整配置、纹理和渲染脚本待核，故不作为近期精确复跑承诺 | 官方 HTML 200，核到标题和 ID；未播放 YouTube |
| **MuJoCo：柔性夹爪垫**。[官方对照视频](https://www.youtube.com/watch?v=qJFbx-FR7Bc)，[3.3.0 发布页位置](https://mujoco.readthedocs.io/en/stable/changelog.html#version-3-3-0-feb-26-2025) | MuJoCo flex full／trilinear；夹爪垫形变对照，说明保留自由度的影响。属于**模型表示对照**，E／ν 对比待复跑 | [full 场景](https://github.com/google-deepmind/mujoco/blob/3.17.0/model/flex/gripper.xml)、[trilinear 场景](https://github.com/google-deepmind/mujoco/blob/3.17.0/model/flex/gripper_trilinear.xml)公开；并有带贴图 bunny 供迁移。该对照片 PBR 脚本待核 | 官方 HTML 200，核到嵌入 ID；未播放 YouTube |
| **MuJoCo／RoboCasa：日常物件与家具**。[抓取放置 MP4](https://robocasa.ai/assets/videos/basic_skills/pnp.mp4)、[开门 MP4](https://robocasa.ai/assets/videos/basic_skills/door.mp4)；[作者页](https://robocasa.ai/)相应标题 | robosuite／MuJoCo 刚体、关节和接触；带外观的完整物件与家具动作。**完整资产／视觉参考**，ρ 对比需另固定施力／控制条件 | 代码、网格／贴图资产下载、示例回放与视频输出公开；现成机器人轨迹可能通过闭环控制补偿质量变化，需选择合适受力动作 | 从作者 HTML 提取准确 `source` 地址；未单独请求／播放这两段 MP4 |
| **MuJoCo／Playground：手内重定向**。[MP4](https://playground.mujoco.org/assets/inhand_reorientation.mp4)，[作者页](https://playground.mujoco.org/) `In-Hand Reorientation` | MJX 机器人控制及真实硬件迁移演示，用作实际接入与动作证据；画面中的实机与仿真片段需用户播放判断，本文未作镜头解读 | 官方开源框架、模型、环境和训练代码配套。并非完整软体或 E／ν／ρ 参数对比视频 | HEAD 200，`video/mp4`，约 19.9 MB；未播放 |

Chrono 的工程证据较强，而本轮找到的公开视频与当前可复跑 FEA 代码的对应仍弱；因此推荐级别为按物体类型启用，保留这个缺口。SOFA 的 Dice Salad 外观制作配套最完整，但物理体积近似限制其直接承担材料量值演示。两者都应按各自强项进入观看，而不是合并成“成熟所以每项都现成”。

## 8. 可交给主报告的取舍

1. **先比较现有主候选，再新增 SOFA 的完整结构选项。** 需要气腔、驱动、部件连接、软硬交互时，PneuNet 这类开源配置比新增自研材料／耦合更直接；BlenderToolbox 降低后渲染准备成本。先观看 Dice Salad 的外观质量，同时把它的物理笼近似展示清楚。
2. **PolyFEM 保留作体弹性＋接触质量较易解释的候选。** 推荐依据应写成材料方程、体网格、具体离散对照和可用完整模型；行业采用和端到端美观资产的优势交由证据更强的产品承担。
3. **Chrono／MuJoCo 按覆盖缺口启用。** 机械／梁壳／轮胎选 Chrono；硬家具、器具、玩具整体运动和 ρ 的惯性场景选 MuJoCo／RoboCasa。它们各自的现成场景能扩展展示范围，保持 E／ν 的变形问题落在明确材料实现上。
4. **参数说服力沿既定工单口径落实到场景。** 已知载荷下的形变／反力、横向应变与体积变化、固定冲量／释放后的惯性响应，都能把画面与量值关联；现成脚本中的接触、阻尼、单位、质量分布和控制方式决定解释边界。上表多数现成影片属于视觉、方法或结构证据，目标 E／ν／ρ 的具体动态对比由选定后的复跑产生。

