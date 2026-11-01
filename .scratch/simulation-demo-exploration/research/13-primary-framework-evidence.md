# Newton / Genesis：工程采用、完整物体与材料量证据

核查日期：2026-11-01。承接主工作区《知名仿真产品的工程适配与完整物体案例》研究票。正文使用截至本日可访问的官方文档、版本源码、作者论文与演示；Newton 源码以 **v1.7.0**、Genesis 以 **v1.4.5** 为主要依据，`latest` 文档另列。本文未安装、复跑或完整播放视频；“作者说明”“源码对应”“可访问”分别记录，视效优劣留给人工观看。

## 1. 对这两个产品的建议

- **Newton VBD 是工程主力候选。** 完整体积弹性起点是公开 tetrahedral rubber duck；同一产品已有布料、杆、刚体与颗粒的工程案例，外部使用可具体定位到求解器。VBD 的材料输入具有物理语义，求解仍属 experimental；鸭子案例尚缺独立实测 E／ν 量值对应。因此，推荐依据是现成资产、实现、工程案例和可检查机制，物理可信度按场景限定。
- **Genesis MPM.Elastic 是真实的并行候选。** 本轮补到了被动完整物体 `cut_dragon`，直接输入 E／ν／ρ，配套 OBJ、材料文件、纹理、脚本与动画均有公开入口。现成动作是下落切割、粒子显示；将动作改为保持拓扑的下落／受压并启用外观网格，是场景和输出改造。它的普通弹性物体能力应独立比较，不应仅因使用 MPM 而降为特殊现象备用。
- **Genesis implicit FEM + SAP 是抓持／受压的条件替代。** 现成的 Franka 抓球把隐式 FEM、`linear_corotated` 与 SAP 接触组合明确。丰富完整物体的现成 FEM 配置证据较少；选它意味着进一步复用已有网格并生成体网格。大形变、自接触可考虑已集成的 libuipc 路径，作为外部求解后端记录。

以上判断对应下文证据；几何外形、材料模型、物体结构、接触和后渲染分别决定迁移范围。空心橡胶鸭、填充玩具、分层水果、薄壁瓶仍需对应结构，外形相似的均质实体只支持该实体假设。

## 2. 工程与采用：组织、维护、实际使用分列

| 项目 | 组织与持续工程依据 | 安装／使用入口与成本 | 具体采用及证据范围 |
| --- | --- | --- | --- |
| **Newton** | 由 Disney Research、Google DeepMind、NVIDIA 发起，Linux Foundation 项目；当前稳定版 v1.7.0 发布于 2026-10-14，包含材料缩放、刚软接触、诊断与渲染更新。[仓库](https://github.com/newton-physics/newton)、[1.7 发布](https://github.com/newton-physics/newton/releases/tag/v1.7.0) | Python 包与统一示例入口：`pip install "newton[examples]"`，`python -m newton.examples softbody_franka`；基础安装沿用 wheel／Warp JIT，无需先自编整个引擎。当前文档提示新装 Warp 1.18 的 CUDA 路径需要 Turing/CC 7.5+ 与 R580+ 驱动；版本与驱动须按选定环境匹配。[安装](https://newton-physics.github.io/newton/latest/guide/installation.html)、[README](https://github.com/newton-physics/newton) | **Skild AI**：Isaac Lab + Newton 的 MuJoCo Warp 刚体，使用 Newton SDF／hydroelastic 接触做 GPU 机架装配训练。**Lightwheel／Samsung 案例**：MuJoCo Warp 与 VBD cable 双向耦合做冰箱线缆插接；原文 Samsung 为“will use”，属于已展示工作流与采用计划，部署成熟度须按此表述。[官方案例](https://developer.nvidia.com/blog/newton-adds-contact-rich-manipulation-and-locomotion-capabilities-for-industrial-robotics/) |
| **Genesis World** | 2024-12 起源于学术项目，现由 Genesis AI 支持；v1.4.5 发布于 2026-10-22。主体框架 Apache-2.0，当前文档涵盖材料、本构、耦合、传感器与渲染。[介绍](https://genesis-world.readthedocs.io/en/latest/user_guide/overview/what_is_genesis.html)、[发布](https://github.com/Genesis-Embodied-AI/genesis-world/releases/tag/v1.4.5)、[仓库](https://github.com/Genesis-Embodied-AI/genesis-world) | 先 PyTorch，再 `pip install genesis-world`；Python 3.10–3.13，Linux／Windows／macOS，CPU 和 CUDA／ROCm／Metal 后端。`gs.init()` → `gs.Scene()` → `add_entity()` → `scene.step()`；IPC 与 Nyx 分别添加依赖。Luisa 光追路径需源码构建，官方对新工作建议 Nyx。[安装](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/overview/installation.html) | **Genesis Forge**：外部开源 RL 框架，示例用 Genesis 原生刚体／URDF Go2 环境进行步态学习，属于可检查的应用集成。[作者仓库](https://github.com/jgillick/genesis-forge)。**GAUGE**：外部研究用 Genesis 原生刚体、PBD 布料、显式 MPM 体积软体做实测对照，见下文。上述证据支持研究使用和集成，当前查得资料尚不足以宣称完整软体已获广泛工业量值验证。 |

Newton 另有 ETH Zurich 用隐式 MPM 做工程机械与颗粒堆交互；它支持颗粒求解器的外部采用，不能移作 VBD 弹性物体精度证据。Taccel、Style3D 等外部后端的效果同样应归给对应实现。[官方生态案例及视频 4／6／7](https://developer.nvidia.com/blog/train-a-quadruped-locomotion-policy-and-simulate-cloth-manipulation-with-nvidia-isaac-lab-and-newton/)

## 3. 框架 × 效果 × 实际实现

“原生”表示框架提供该实现；“外接”表示框架集成独立依赖。现成案例与潜在物体覆盖分别说明。

| 效果 | Newton | Genesis |
| --- | --- | --- |
| 刚体／关节完整物体 | 原生 MuJoCo、Featherstone、XPBD、VBD 等后端；MuJoCo 面向关节系统，VBD 有自己的关节限制。已有机架装配、机器人和连接器等场景。[求解器表](https://newton-physics.github.io/newton/latest/solvers/index.html) | 原生 Rigid，已有 Franka 抓瓶、接触与多机器人示例。刚体的 ρ 进入质量／惯性，E／ν 不产生物体应变。[抓瓶源码](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/examples/rigid/grasp_bottle.py) |
| 体积软体 | 原生 VBD tetrahedral stable Neo-Hookean；已有鸭子抓放、softbody hanging。ImplicitMPM 为另一条颗粒／弹塑性实现，材料范围与 VBD 分开。[VBD 材料内核](https://github.com/newton-physics/newton/blob/v1.7.0/newton/_src/solvers/vbd/particle_vbd_kernels.py)、[鸭子](https://github.com/newton-physics/newton/blob/v1.7.0/newton/examples/softbody/example_softbody_franka.py) | 原生 FEM、MPM、PBD 三条路径。MPM 有被动切割龙；FEM 有 SAP 抓球；PBD 有 elastic dragon。方法和材料输入各异。[软体理论](https://genesis-world.readthedocs.io/en/latest/user_guide/theory/soft_solvers.html)、[示例目录](https://github.com/Genesis-Embodied-AI/genesis-world/tree/v1.4.5/examples) |
| 布料／薄壳 | 原生 VBD 三角膜＋弯曲，有 Franka 布料操作、gift 布带；Style3D 为另一个外部后端，不把其皮衣展示归为默认 VBD。[VBD 概览](https://newton-physics.github.io/newton/latest/solvers/vbd.html)、[Style3D 示例](https://github.com/newton-physics/newton/blob/v1.7.0/newton/examples/cloth/example_cloth_style3d.py) | 原生 PBD.Cloth；`FEM.Cloth` 经外接 libuipc 使用薄壳，额外输入厚度与弯曲刚度；已有 robot cloth teleop。[IPC 教程](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/theory/coupling/ipc_coupler.html)、[对应脚本](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/examples/ipc/ipc_robot_cloth_teleop.py) |
| 杆／植物 | 原生 VBD ROD／Cosserat cable，已有扭转、Y 接头、线缆抓放及工业插接；杆刚度、截面、连接仍需指定。植物属于可建模方向，线缆例子本身只证明杆系统。[cable 示例](https://github.com/newton-physics/newton/tree/v1.7.0/newton/examples/cable) | PBD Rope 提供绳索约束；soft robot／hybrid 能表达主动或混合结构。当前核实的资料未形成成熟被动植物整套模型，应保留为潜在能力。[软体理论](https://genesis-world.readthedocs.io/en/latest/user_guide/theory/soft_solvers.html)、[Hybrid](https://genesis-world.readthedocs.io/en/latest/user_guide/physics/hybrid_entity.html) |
| 颗粒／雪／塑性 | 原生 ImplicitMPM 的 Drucker–Prager、硬化／软化、屈服、剪胀；现成 granular、snowball、ANYmal 沙地。[API](https://newton-physics.github.io/newton/latest/api/_generated/newton.solvers.SolverImplicitMPM.html)、[示例](https://github.com/newton-physics/newton/tree/v1.7.0/newton/examples/mpm) | 原生 MPM Sand／Snow／ElastoPlastic；现成 sand wheel、MPM tutorial。E／ν／ρ 之外仍需要摩擦角、屈服或硬化等材料量。[材料模型表](https://genesis-world.readthedocs.io/en/latest/user_guide/theory/soft_solvers.html) |
| 黏性流体／水 | 原生 ImplicitMPM 有 viscosity、屈服等设置，现成 `mpm_viscous`、`mpm_water_dam_break`；此证据限于该实现。开发者 XPBD 流体实验不写入正式能力。[现成代码](https://github.com/newton-physics/newton/tree/v1.7.0/newton/examples/mpm) | 原生 MPM Liquid、SPH Liquid，另有 PBD Liquid；已核 MPM／SPH／刚体示例。牛顿黏性、塑性与弹性材料字段各有含义，流体不能仅凭 E／ν／ρ 定义。[软体理论](https://genesis-world.readthedocs.io/en/latest/user_guide/theory/soft_solvers.html)、[coupling 示例](https://github.com/Genesis-Embodied-AI/genesis-world/tree/v1.4.5/examples/coupling) |
| 真正耦合的范围 | 单个 VBD 已联立刚体、软体、布料和杆。外部求解器耦合还有 experimental `SolverCoupledProxy`／`SolverCoupledADMM`，已有 MuJoCo–VBD、MuJoCo–MPM、VBD–MPM 等示例；proxy 迭代、ADMM 行类型与接触有各自限制。**鸭子示例的机器人是运动学驱动，不能称为完整双向动力学验证。**[耦合文档源码](https://github.com/newton-physics/newton/blob/v1.7.0/docs/concepts/coupling.rst)、[鸭子源码](https://github.com/newton-physics/newton/blob/v1.7.0/newton/examples/softbody/example_softbody_franka.py) | Legacy 明确支持 rigid–MPM／SPH／PBD／FEM，以及 MPM–SPH、MPM–PBD、FEM–MPM、FEM–SPH。SAP 只支持 Rigid／FEM，要求 float64＋隐式 FEM。IPC 通过 libuipc 处理 FEM／壳与 ABD 刚体，有双向设置；MPM／SPH／PBD 使用 Legacy。**求解器同场景存在不等于任意两者耦合。**[耦合对表](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/theory/coupling/index.html)、[SAP](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/theory/coupling/sap_coupler.html)、[IPC](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/theory/coupling/ipc_coupler.html) |

## 4. E／ν／ρ 的含义与“值合理”的证据

| 具体实现 | 已核输入与材料映射 | 可用动作与解释范围 |
| --- | --- | --- |
| **Newton VBD 体积** | `add_soft_mesh(... density, k_mu, k_lambda)`；μ=`E/[2(1+ν)]`，λ=`Eν/[(1+ν)(1−2ν)]`。内核将 Lamé 系数转为 stable Neo-Hookean 参数以匹配小应变极限。鸭子代码 μ=λ=`1e6 Pa`、ρ=`100 kg/m³`，按公式为 **E=2.5 MPa、ν=0.25**。[材料内核](https://github.com/newton-physics/newton/blob/v1.7.0/newton/_src/solvers/vbd/particle_vbd_kernels.py)、[鸭子配置](https://github.com/newton-physics/newton/blob/v1.7.0/newton/examples/softbody/example_softbody_franka.py) | 抓紧、释放、跌落可显露局部压缩和恢复；控制载荷的挤压／拉伸更适合把位移与材料量联系起来。鸭子值是示例配置，源码未给该鸭子实物标定；实心体网格也不直接代表常见空心玩具。该完整物体目前属于**代码可调、参数对比待复跑**。 |
| **Genesis MPM.Elastic** | 直接 E／ν／ρ；默认 E=`3e5 Pa`、ν=`0.2`、ρ=`1000 kg/m³`，默认 fixed-corotated；另有 `neohooken`。基类按同一公式算 μ／λ。`cut_dragon` 继承这些默认材料量。[Elastic](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/genesis/engine/materials/MPM/elastic.py)、[基类](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/genesis/engine/materials/MPM/base.py)、[龙配置](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/examples/coupling/cut_dragon.py) | 现成下落切割证明被动完整外形与刚体工具互动；形状保持型的下落／受压可作为 E、ν 对比动作改造。切割现象还取决于 CPIC／刀具／分辨率，现成动画属于**视觉参考＋代码可调**，尚无该龙的 E／ν 实测参照。 |
| **Genesis FEM** | E／ν／ρ 直接输入，模型包括 linear、linear_corotated、stable_neohookean；须按实际积分路径选择。v1.4.5 内部 FEM 中，stable_neohookean 的隐式梯度／Hessian仍抛未实现；linear_corotated 的显式 stress 路径也未实现。现成 SAP 球使用 **隐式＋linear_corotated，E=`1e5 Pa`、ν=`0.4`**。IPC 是另行集成的 libuipc 路径。[材料源码](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/genesis/engine/materials/FEM/elastic.py)、[抓球源码](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/examples/sap_coupling/franka_grasp_fem_sphere.py) | 抓球是受控接触起点，适度形变下可以比较挤压量、横向鼓出和释放响应。它支持明确的材料量演示设置，完整日常物体及量值实拍对应仍需按对象增加证据。 |
| **Newton ImplicitMPM** | 直接 `mpm.young_modulus`、`mpm.poisson_ratio`，质量与粒子体积给出ρ；含弹塑性／流变参数。作者定位为偏高刚度及非弹性极限、较传统显式 MPM 更窄的材料范围。1.7 修正应力归一化，旧调参场景需将 E／屈服／黏度值减半以迁移。[API](https://newton-physics.github.io/newton/latest/api/_generated/newton.solvers.SolverImplicitMPM.html)、[1.7 迁移说明](https://github.com/newton-physics/newton/releases/tag/v1.7.0) | 已有扭梁、雪球和多材料演示；时间步稳定性是数值性质，受力位移的精度仍取决于本构、离散、积分与收敛。跨版本视频／参数不能直接拼接成量值证据。 |
| **Genesis PBD.Elastic** | ρ 为直接密度，软硬主要是 stretch／bending／volume compliance 与 relaxation；该类未提供 E／ν 字段或通用换算。[源码](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/genesis/engine/materials/PBD/elastic.py) | elastic dragon 可作为完整外形运动参考，E／ν 的量值论证需另补明确映射。它与被动 MPM dragon 分列。 |

**判断值合理性所需的证据是递进的。** 上表证明输入确实进入应力／质量方程；对应的小应变拉伸量、受压横向变形或已知冲量响应，可以给量值解释。完整物体在较大形变和复杂接触下还受到材料模型、结构与数值误差影响，单看“更硬／更软”只能说明趋势。以下已有参照可直接补到选型说明：

| 参照 | 已有证据能支持到哪里 |
| --- | --- |
| **Newton VBD／DAT 的数值机制** | 作者论文给出穿透／翻转控制及多物理接触案例，Newton 1.7 刚软 DAT 为 opt-in。论文也说明未收敛截断可能改变能量／动量。因此它加强接触鲁棒性的解释，不能独立标定橡胶鸭 E／ν。[作者页](https://ankachan.github.io/Projects/DAT/)、[原论文](https://arxiv.org/abs/2604.15513)、[发布说明](https://github.com/newton-physics/newton/releases/tag/v1.7.0) |
| **Newton 的理论对照测试** | 官方 `test_physics_verification.py` 有解析轨迹／守恒量的实现检查，并明确区分实现正确性与真实材料符合程度。本文只核查测试内容，未执行测试；不将刚体／接触测试扩展成体积材料全面验证。[测试源码](https://github.com/newton-physics/newton/blob/v1.7.0/newton/tests/test_physics_verification.py) |
| **GAUGE 的实测参照** | 作者实测材料量并采集动作轨迹，体积软体使用 **Genesis 显式 MPM、Newton 隐式 MPM**；测量参数之外保留默认设置。结果显示量值仍有场景偏差。该证据仅适用于已测实现／配置，不能给当前 VBD 或 Genesis 新 FEM 排名。正文 Genesis 版本写 `v1.12.0`，与官方发布序列有待核对。此处仅采用仿真器轨道。[原文 §3.2／§5.1](https://arxiv.org/html/2608.05948v1) |

GAUGE 的[泡沫压缩实拍](https://internrobotics.github.io/GAUGE/trials/foam-compressing.mp4)、[泡沫弯曲实拍](https://internrobotics.github.io/GAUGE/trials/foam-bending.mp4)可供团队理解可见响应；主研究已确认端点为 200 `video/mp4`。它们是作者页的 **Real-world trial**，属于现实参照片，不计作某框架的仿真展示或已公开一键复跑案例。[作者项目页](https://internrobotics.github.io/GAUGE/)

**场景建议（推断）：**完整物体可配受控片段：固定载荷看变形量支撑 E；在同一轴向应变下看横向应变／体积变化支撑 ν；固定冲量或承载下垂支撑ρ。实际比较可用 E 的 0.1×／1×／10×、ν=0.1／0.3／0.45、ρ的 0.25×／1×／4×作为待选示例范围，其他物理参数固定。高刚度／近不可压缩组需要足够分辨率和求解精度；具体配置留到选中后的复跑。

## 5. 可直接观看的案例与公开资产

### Newton

| 观看入口与定位 | 物体、动作、实际实现 | 脚本／物理资产／外观公开程度；证据类别 |
| --- | --- | --- |
| **[DAT 作者视频](https://www.youtube.com/watch?v=gdUB5NDl_E8)**；[作者页顶部唯一视频](https://ankachan.github.io/Projects/DAT/)，标题 *Divide and Truncate* | 作者说明覆盖体积软体、布壳、杆、刚体耦合；论文 Fig.15 为由软块和两条交叉布带组成的 **Gift Box Drop**，有变形、跌落及多重接触。作者明确 DAT 已进入 Newton VBD。 | Newton 有对应主题的 [`softbody_gift` 脚本](https://github.com/newton-physics/newton/blob/v1.7.0/newton/examples/multiphysics/example_softbody_gift.py)，参数及程序生成几何公开。这里记录为完整组合物体／体积软体视觉入口；论文视频的每个场景与当前脚本版本逐项对应关系、完整渲染包尚未核定。未伪造片内时间点。 |
| **[Franka 折／提布料 MP4](https://developer-blogs.nvidia.com/wp-content/uploads/2025/09/robot-arm-folding-cloth.mp4)**；官方文章 Video 3 | 机械臂操作完整布片；Newton VBD 布料与刚体机器人，文章展示由 Omniverse Kit 渲染。 | [`cloth_franka` 现成代码](https://github.com/newton-physics/newton/blob/v1.7.0/newton/examples/cloth/example_cloth_franka.py)可定位；物理场景与相机入口公开。文章成片的 Kit 光照／PBR 配置未核成同一公开复跑包。属于**视觉参考＋代码可调**。[出处](https://developer.nvidia.com/blog/train-a-quadruped-locomotion-policy-and-simulate-cloth-manipulation-with-nvidia-isaac-lab-and-newton/) |
| **[冰箱线缆插接 MP4](https://developer.download.nvidia.com/video/devblog/waterhose.mp4)**；官方文章 Video 5 | RB-Y1 操作完整线缆／软管，MuJoCo Warp 与 VBD cable **双向耦合**，外部工业工作流。 | 作者文章明确方法与用途；该冰箱、机器人、线缆配置及电影画质渲染包的公开入口尚未核定，属于**外部采用＋视觉参考**。框架自身另有公开 cable 示例，二者分开。[出处](https://developer.nvidia.com/blog/newton-adds-contact-rich-manipulation-and-locomotion-capabilities-for-industrial-robotics/) |
| **Rubber duck：当前为代码起点** | 完整四面体鸭子抓紧、提起、释放；VBD stable Neo-Hookean，机器人运动学驱动。[完整脚本](https://github.com/newton-physics/newton/blob/v1.7.0/newton/examples/softbody/example_softbody_franka.py) | [资产目录](https://github.com/newton-physics/newton-assets/tree/main/manipulation_objects/rubber_duck)有 `model.usda`、`mesh.usd`、README、LICENSE；tet 与 surface 入口可定位，示例有材料参数。目录未单列贴图，未据此断言纹理是否内嵌 USD。**本轮未定位与这份脚本确切对应的直达鸭子动画**，不能把仓库缩略图称为视频。 |

### Genesis

下列动画来自官方 README 的“动画 → 脚本”配对，直达媒体均已收到 HTTP 200，`image/webp` 文件头有 `ANIM` 标记，可作为短循环动画观看。[官方目录](https://github.com/Genesis-Embodied-AI/genesis-world)

| 观看入口 | 物体、动作、实际实现 | 脚本／物理资产／外观公开程度；证据类别 |
| --- | --- | --- |
| **[被动 MPM 龙下落切割](https://raw.githubusercontent.com/Genesis-Embodied-AI/genesis-world/readme-assets/videos/coupling_cut_dragon.webp)** | 完整龙外形下落到交叉刀具；**MPM.Elastic＋CPIC**，刀具为固定刚体，属于被动弹性体实例。 | [`cut_dragon.py`](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/examples/coupling/cut_dragon.py)与[dragon OBJ／MTL／PNG](https://github.com/Genesis-Embodied-AI/genesis-world/tree/v1.4.5/genesis/assets/meshes/dragon)公开；粒子由 mesh／sampler 生成，材料继承默认值。**该脚本使用纯色 particle 显示**，资产有纹理不表示当前片已使用完整 PBR。**视觉参考＋代码可调**。 |
| **[完整瓶子抓取](https://raw.githubusercontent.com/Genesis-Embodied-AI/genesis-world/readme-assets/videos/rigid_grasp_bottle.webp)** | Franka 抓起瓶子，多环境；**Rigid**，配置ρ=`300`。这是硬物体／质量方向案例，未展示薄壁瓶挤压。 | [`grasp_bottle.py`](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/examples/rigid/grasp_bottle.py)公开，引用仓库 `urdf/3763/mobility_vhacd.urdf` 与 Franka MJCF。动作／质量配置明确；未逐项检查该瓶全部贴图。**视觉参考＋密度代码可调**。 |
| **[Franka 抓 FEM 球](https://raw.githubusercontent.com/Genesis-Embodied-AI/genesis-world/readme-assets/videos/sap_franka_grasp_fem_sphere.webp)** | 球被夹持、提起；**implicit FEM＋linear_corotated＋SAP**。此为受控几何补充。 | [脚本](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/examples/sap_coupling/franka_grasp_fem_sphere.py)公开，球由 primitive 生成，E／ν、机器人与动作均给出；属于**代码可调待参数对比**。 |
| **[带纹理 worm](https://raw.githubusercontent.com/Genesis-Embodied-AI/genesis-world/readme-assets/videos/tutorials_advanced_worm.webp)** | 完整虫形主动收缩／移动；**MPM.Muscle**，四个肌肉组。 | [脚本](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/examples/tutorials/advanced_worm.py)引用 `worm.obj`、`bdy_Base_Color.png`，E=`5e5`、ν=`0.45`、ρ=`10000`，模型 `neohooken`；外观和主动驱动公开。证明纹理软体链路，材料值／驱动不足以代表真实生物或被动软体。 |

**输出成本：**Genesis Nyx 插件现已公开安装；FEM／PBD 固定拓扑逐帧更新，MPM 固体通过蒙皮、流体通过重建表面。GLB／GLTF 保留内嵌材质；当前插件粒子显示、SPH／SF 的可见性与玻璃透射等有明确限制。`cut_dragon` 的 particle 模式不能原样交给 Nyx，切割后的蒙皮也需按拓扑选择输出方式。[Nyx 安装](https://genesis-embodied-ai.github.io/genesis-nyx/latest/user_guide/installation.html)、[支持表](https://genesis-embodied-ai.github.io/genesis-nyx/latest/user_guide/supported_features.html)

Newton 有 GL 捕帧、USD 输出和可选 RTX；保留既有 USD／OVStage PBR 的路径目前只更新刚体变换，官方明确该模式不更新原有 deformable prim。鸭子要形成带完整外观的软体片，应按具体 visual-mesh／USD→Blender 链路准备，不能只凭“支持 USD／RTX”省略这一步。[1.7 可视化说明](https://github.com/newton-physics/newton/blob/v1.7.0/docs/guide/visualization.rst)

4090 是上述硬件光追成片路径的适合候选；A800 可考虑承担 CUDA 物理，渲染要求另按 Nyx／RTX 的硬件条件核对。这是依据硬件功能的工作分配建议，本文未测两机速度。

## 6. 建议先看的四项

1. **[Genesis 被动 MPM 龙](https://raw.githubusercontent.com/Genesis-Embodied-AI/genesis-world/readme-assets/videos/coupling_cut_dragon.webp)**：先看完整外形的被动变形／接触；源码明确 MPM.Elastic，是当前并行候选的直接证据。
2. **[Newton DAT 作者视频](https://ankachan.github.io/Projects/DAT/)**：看体积软体、布壳、杆与刚体接触范围；选用页面顶部 *Divide and Truncate* 视频，结合论文 Gift Box Drop 理解完整组合结构。
3. **[Newton 工业线缆插接](https://developer.download.nvidia.com/video/devblog/waterhose.mp4)**：看具体外部使用和完整物体互动；该片不承担体积弹性 E／ν 的证明。
4. **[Genesis FEM 抓球](https://raw.githubusercontent.com/Genesis-Embodied-AI/genesis-world/readme-assets/videos/sap_franka_grasp_fem_sphere.webp)**：看隐式 FEM＋SAP 的受控夹持；随后可配 GAUGE 泡沫实拍理解量值参照与视觉演示的区别。

上述 Newton 两个 MP4、DAT 作者页和四个 Genesis 动画已做只读链接可访问检查；DAT YouTube HEAD 为 200，但返回空正文，实际播放仍依赖 YouTube 可达性，作者页有明确嵌入入口。本文没有以链接可达代替完整观看。

收束缺口：Newton 鸭子的确切动态入口、双方丰富完整体积物体的实测材料对应、宣传片的完整 PBR 资产包仍需按用户选中的场景补齐。近期可执行改造集中于参数、动作、现有几何与输出；新增本构、求解器、耦合算法继续列后续研发。
