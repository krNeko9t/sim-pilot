# 重点固体候选与框架的正向仿真取舍

核查日期：2026-11-01。对应[重点固体候选与框架的正向仿真取舍](../issues/07-solid-forward-simulation-selection.md)，承接[固体形变的用户反馈](../issues/01-solid-deformation-candidates.md#comments)与[候选总览](06-candidate-overview-and-recommendations.md)。术语和阶段边界沿用[探索规格](../spec.md)。本次依据作者论文、官方文档、发布记录与源码形成选型建议；动态效果沿用已有观看记录，新增示例的参数效果属于待复跑证据。

**当前状态：用户反馈后重开研究，首轮示例优先排序撤回。** 本文已查参数入口、资产和维护，但完整物体的广覆盖能力与运动／形变质量仍需补足证据。当前采用各项目自带资产和成熟公开场景；数据格式及准备条件由研究说明，上游对接留待后续。下文具体例子保留为已找到的局部能力入口，路线选择按补充研究重新评价。

## 用户反馈后的质量补核

**VBD 的近似发生在哪一层。** VBD 是求解隐式 Euler 变分能量的顶点分块下降算法，可用于体 FEM；充分迭代时可收敛到该离散问题的解。迭代提前截断会留下残差。原论文 §3.7 明确讨论不同初始化和有限迭代造成外力响应失真、材料偏软、过度拉伸和堆叠接触困难，因此稳定性与材料响应准确性应分别判断。Newton 鸭子例的每子步5次迭代、每帧10子步只说明示例计算预算，当前研究尚缺其代表性物体误差和质量证据。[VBD 原论文](https://arxiv.org/html/2403.06321v4)、[鸭子配置](https://github.com/newton-physics/newton/blob/main/newton/examples/softbody/example_softbody_franka.py)。

对完整物体的判断还应包括物理模型是否表达相应材料／结构、空间和时间离散、接触与摩擦。增加迭代主要减少求解残差；改变网格、本构或接触才能处理相应层面的误差。Newton 当前官方仍将 SolverVBD 标为 experimental，前轮的材料入口和现成例子支持保留候选，首选地位还需质量与覆盖证据。[当前官方说明](https://newton-physics.github.io/newton/latest/solvers/vbd.html)。

**FreeForm 的运动表示与外观分开判断。** 作者使用 RKPM 和降阶 skinning 基计算运动，同时展示 mesh 和 Gaussian splat 两类外观；数字资产的网格／PBR 路径可以独立评价。原论文明确指出全局低频基难表达细皱纹、尖锐接触等局部细节，自碰撞与默认拓扑变化另有限制。PBR 影响外观呈现，降阶空间影响可表达的形变；研究应分别呈现原始运动和外观质量，现成水果资产数量只提供一部分覆盖证据。[FreeForm 方法、展示与限制](https://research.nvidia.com/labs/sil/projects/freeform/)。

**当前评估重心。** 使用各仿真器自身较丰富的公开物体场景，说明它们所表达的实体、薄壁、多材料或多部件结构、接触与作用条件、可达到的运动质量和资产后渲染路径。完整物体的外形、内部物理模型与外观资产各自承担不同作用；各项目的输入格式和建模工作由研究解释。已有离线算力条件下，优先比较可达到的质量和覆盖范围，再评价实时性与批量效率。

## 已找到的局部演示入口（首轮建议，待重新评价）

下表保留首轮根据参数入口和现成场景提出的建议，供后续覆盖与质量研究回查。E 的倍率可从 0.1×／1×／10×中选择适用组；这是拟议对比，原作者的单组演示仍按单组记录。其他输入沿用同一场景默认值并固定。

| 用户想观察的效果 | 候选与场景 | 参数及观察重点 | 选择时要接受的条件 |
| --- | --- | --- | --- |
| **玩具太软／太硬，夹起后变形与恢复** | **Newton：Franka 抓取橡皮鸭**，已有程序化抓取、抬起、放回、松开动作和四面体资产 | 首选 E；固定 ν、ρ、动作、摩擦与阻尼，观察自由部位下垂和松开后的恢复。原例是一组材料 | 使用体四面体的等效实体玩具；机械臂按指定运动驱动。源码的真实单位与材料换算明确，画面差异待复跑。[官方例程](https://github.com/newton-physics/newton/blob/main/newton/examples/softbody/example_softbody_franka.py) |
| **水果太软／太硬，保留具体物件外形** | **FreeForm：SimReady 苹果／橙子的受控拉伸**；另可从 Thingi10K 玩具形状选择 | 首选 E；固定 ν、ρ、尺度、加载和降阶设置 | 名单已有水果与玩具网格。原[狗玩具短片](https://research.nvidia.com/labs/sil/projects/freeform/assets/videos/splats/dog.mp4)、[葡萄短片](https://research.nvidia.com/labs/sil/projects/freeform/assets/videos/splats/grape.mp4)继续作视觉目标；其 3DGS 资产与评测包分开，原片复跑条件见下文 |
| **软体落地／相互挤压，重视接触与后渲染** | **libuipc：StableNeoHookean 体 FEM**；现成软兔为入口，水果可沿 VoMP 场景适配 | E 为主；ν 可另做受控挤压，ρ 可用于自重或动态响应 | 使用包含 2026-10-19 材料校准修复的实现；异质材料应按当前本构写入系数。水果完整资产与外观导出按所选场景衔接。[材料规格](https://spirimirror.github.io/libuipc-doc/specification/constitutions/stable_neo_hookean/)、[VoMP 橙子配置](https://github.com/nv-tlabs/VoMP/blob/main/configs/sim/falling_oranges.json) |
| **泊松比导致的横向鼓胀与体积保持** | **MiNNIE：bulging／受控软块** | 固定 E 与轴向加载，比较适用的 ν，如 0.3／0.45／0.49；接近 0.5 的组单独使用合适求解设置 | 准静态 bulging 承担 ν 展示；ρ 的动态作用应另用动态场景。受控几何便于解释，日常水果外形可后续替换 |
| **毛绒玩具拉伸、摆动，保留真实外观** | **PhysTwin／Boba：已有树懒等 twin** | 沿用拟合得到的弹簧、质量、阻尼与接触参数，观察互动效果 | 当前作为原生参数的前向演示；E／ν／ρ 的物理对应见材料映射结论 |
| **肉／橙瓣沿纹理撕裂** | **AnisoMPM：现成肉、橙子或参数扫值场景** | 若比较基础 E，固定纤维、强化、损伤与加载；同时明确额外参数决定的方向性与断裂 | 适合用户的材料结构兴趣；只提供 E／ν／ρ 时先选普通弹性形变路线，撕裂场景需沿用作者完整材料配置 |

E 展示优先观察受力后的自由形变和释放响应；ν 展示优先观察同轴向变形下的横向响应。纯强制位移下的单个终态，对不同物理量的辨识能力不同。ρ 的软体入液展示继续由[软体入液的密度差异展示](../issues/10-softbody-liquid-density-scene.md)承接。

## 材料输入与具体实现

对各向同性弹性，物理 Lamé 参数满足 `μ_L=E/[2(1+ν)]`、`λ_L=Eν/[(1+ν)(1−2ν)]`。具体能量函数可能还需将它们转换为内部系数；例如当前 libuipc 的 SNK1 要存 `μ=μ_L、λ=λ_L+μ_L`。因此，“出现 E／ν 字段”和“最终响应对应给定 E／ν”需要沿实际材料实现核对。采用 SI 时还须保持几何长度、E（Pa）、ρ（kg/m³）一致。[libuipc 材料规格](https://spirimirror.github.io/libuipc-doc/specification/constitutions/stable_neo_hookean/)。

MPM、FEM 表示离散方法，Warp 是计算基础；FreeForm 的 RKPM 降阶、PhysTwin／Boba 的弹簧模型、Newton 的 VBD／MPM、PhysX 的 GPU FEM、MuJoCo flex 和 libuipc 的 FEM＋IPC 各自使用不同的材料及接触实现。下文按具体路径比较。

### FreeForm：直接接收三物理量，优先选择公开水果网格

**实现与材料。** RKPM 粒子离散加 skinning eigenmodes 降阶，采用 Neo-Hookean 超弹性能；evaluation 调用 Kaolin 的 `SimplicitsObject.create_with_rkpm`、`FreeformScene`。`young_modulus / poisson_ratio / density` 支持均匀值，另可读取逐体素材料场。材料同时参与基构建和仿真，`run_sim.py` 从已存模型读取数值，因此参数对比应同步更新模型与运行输入。类别为**直接输入物理量；代码支持调参，效果待复跑**。[模型创建](https://github.com/nv-tlabs/freeform-eval/blob/main/sim/create_model.py)、[仿真与输出](https://github.com/nv-tlabs/freeform-eval/blob/main/sim/run_sim.py)。

**场景与几何。** 公开评测包含程序化梁、20 个 Thingi10K 网格和 19 个 SimReady 网格及材料；SimReady 名单有 Apple、Orange02、Lychee01、Lemon_01、草莓和糖果 GummyBear，Thingi10K 有 toy_Robot、toy_train、bunny 等。苹果／橙子可以沿评测的受控拉伸路径选择 E 场景。RKPM 使用体内采样与积分点；作者说明全局低频基对尖锐接触和细皱纹的表达有限，默认保持拓扑，自碰撞未显式处理，适合整体形变。[SimReady 名单](https://github.com/nv-tlabs/freeform-eval/blob/main/data/simready_20examples.csv)、[Thingi10K 名单](https://github.com/nv-tlabs/freeform-eval/blob/main/data/thingi10k_20examples.csv)、[作者方法与限制](https://research.nvidia.com/labs/sil/projects/freeform/)。

**输出与选择影响。** evaluation 面向论文 Tables 1、2，输出 `.pth`，用 Polyscope 对照 FEM；Kaolin 主库另有 mesh、Gaussian 与 3DGRUT 路线。原狗／葡萄视频的 splat 资产、材料数值和场景命令仍待定位。选择公开苹果／橙子网格可直接收束到已有材料路径；选择原片外观则增加资产和后渲染准备。评测 README 固定了测试依赖及 Kaolin 修订，复跑以那套依赖或明确适配后的主库版本为准。[评测范围与依赖](https://github.com/nv-tlabs/freeform-eval#readme)、[Kaolin 功能与教程](https://github.com/NVIDIAGameWorks/kaolin#features)。

### PhysTwin／Boba：已有毛绒 twin 的正向交互，原生参数属于视觉调节

两者确有可复用的 Warp 弹簧质量求解器。源码力式为 `F=Y_eff·(l/l0−1)·d`，PhysTwin 的 `Y_eff` 来自 `exp(wp_spring_Y)` 再限幅，Boba 直接读取 checkpoint 的 `spring_Y` 并缓存静止长度倒数。按此式推导，常见弹簧刚度 **`k=Y_eff/l0`**；若用 SI 解释，`Y_eff` 具有力的量纲。长度处理已明确，截面积、三维连续体等效 E／ν 的换算仍缺依据。[PhysTwin 力核](https://github.com/Jianghanxiao/PhysTwin/blob/main/qqtt/model/diff_simulator/spring_mass_warp.py#L82)、[Boba 力核](https://github.com/jianxiapyh/Boba-Public/blob/Boba_Batched/qqtt/model/diff_simulator/spring_mass_warp.py#L70)。

**ρ 与 ν 的缺口也具体可见。** 两者 trainer 都以 `masses=np.ones(...)` 初始化节点质量；底层使用质量计算加速度，但公开材料路径尚缺 `ρ×代表体积` 的分配。已查配置、trainer 与力核也缺少独立 ν 的本构入口。PhysTwin 的 `init_spring_Y=3e4` 是优化初值，交互时加载训练后的边参数及接触参数；在加载 checkpoint 的工作流中，只改这个初值不足以改变目标材料。[PhysTwin 初始化／保存与加载](https://github.com/Jianghanxiao/PhysTwin/blob/main/qqtt/engine/trainer_warp.py)、[运行配置](https://github.com/Jianghanxiao/PhysTwin/blob/main/configs/real.yaml)、[Boba 初始化与运行核心](https://github.com/jianxiapyh/Boba-Public/blob/Boba_Batched/qqtt/engine/trainer_warp.py)。

Boba 继承 PhysTwin 的弹簧模型、Gaussian 外观与蒙皮，同时用较少节点、更大步长构造 compact surrogate，再拟合原 twin 的渲染轨迹。这支持其效果与性能用途；论文未给出保持连续体 E／ν／ρ 的离散换算。[Boba 论文 §4.1](https://rsim.cs.illinois.edu/Pubs/Boba_ECCV.pdf)。

**可选现成场景：PhysTwin 的 `double_stretch_sloth` 树懒双手拉伸。** 官方交互入口为 `interactive_playground.py`，测试可保存 `inference.pkl` 节点轨迹及点云视频，Gaussian 成片走 `gs_run_simulate.sh`。Boba 提供 quality／perf 与 batched 路径，适合同一 twin 的多次独立 rollout；quality 路径有逐视角与合成 MP4。两者需要几何／相机数据、拟合 checkpoint、Gaussian 与蒙皮资源，选择已有 twin 的工作量较明确。Boba 当前建议 Linux、较新的 CUDA PyTorch 栈；成像路径的 X11／OpenGL 与无显示批量求解分开准备。[PhysTwin 资源与交互](https://github.com/Jianghanxiao/PhysTwin#interactive-playground)、[Boba 使用及资源说明](https://github.com/jianxiapyh/Boba-Public#run-boba-batched)。

**取舍：保留毛绒互动和后渲染价值，E／ν／ρ 直接展示降为辅助。** [PhysTwin 动态页](https://jianghanxiao.github.io/phystwin-web/)与[Boba Demos](https://jianxiapyh.github.io/Boba-project-page/#demos)仍值得选择。若比较软硬效果，标注“拟合边参数倍率”；若比较节点质量，标注实际质量调节。目标物理量的演示证据继续归为**仅有视觉效果参考**，原生弹簧参数的前向能力已由源码确认。

### AnisoMPM：保留食物结构效果，基础三量与纤维／损伤共同定义材料

Ziran 2020 的 C++ MPM 将 QR 分解构造的各向异性弹性与损伤／相场演化耦合。橙子用 `QRAnisotropic`，果肉 E=10000、ν=.4、ρ=500，果皮 E=50000、ν=.4、ρ=500；这些是场景数值，SI 与真实水果尺度仍待核。`paramEffects.h` 使用 `QRStableNeoHookean`，给定命令行 E，固定 ν=.25、ρ=2。基础 E／ν／ρ 是**直接输入；代码支持调参，效果待复跑**。[作者方法](https://joshuahwolper.com/anisompm)、[橙子](https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/examples/orange.h)、[参数例](https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/examples/paramEffects.h)。

橙肉还给径向纤维、各向异性强度、损伤启动／演化参数；参数例另有 `fiberScale / percent / eta / residual`。现成 E 扫值为 1／10／100／500／1000，可固定这些附加设置后比较；`--isotropic` 仅跳过某些额外纤维弹性设置，已读分支仍调用损伤 handler。**因此保留“肉／橙瓣沿纹理撕裂”作为专题选择；只给三量时，整体弹性形变由其他候选更直接承担。** [扫值脚本](https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/anisofractureBatch3D.py)、[分支代码](https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/examples/paramEffects.h)。

项目有 1 个 2D、13 个 3D demo 和 Data.zip 入口，输入包括 `.mesh`／`.vdb`。橙子例设置形变梯度输出供 meshing，并写初始 VTK 体网格；完整断裂表面重建和视频后渲染流程仍需随所选场景补齐。继续观看[作者主片](https://www.youtube.com/watch?v=eXJi7pkUZn0)的 Anisotropic Feast／Dongpo Pork；视频与 E 扫值脚本的一一对应沿用首轮待核记录。[demo／数据说明](https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/README.md)。

### SoftMAC：夹持耦合有价值，物理量解释成本较高

soft-rigid 路线由 Taichi MPM＋Jade 刚体构成，贡献是 forecast 接触与双向耦合；优化轨迹可以换成固定轨迹来做正向计算。现成 grip 为 corotated-plastic，另有 Neo-Hookean 和 elastic 分支。E／ν 先换为物理 Lamé；elastic 分支再乘 .3，按代码推得 **E_eff=.3E、ν_eff=ν**。原 grip 塑性分支把变形梯度奇异值硬夹到 **[.998,1.003]**，使用 `yield_stress` 的 von Mises 调用被注释；配置中的 `yield_stress=30` 应按这一实际分支解释。[grip 配置](https://github.com/minliu01/SoftMAC/blob/main/softmac/config/demo_grip_config.py)、[材料分支](https://github.com/minliu01/SoftMAC/blob/main/softmac/engine/mpm_simulator.py)。

质量路径是 `p_vol=(dx*.5)^2`、`p_rho=1`、`p_mass=p_vol*p_rho`，3D 场景也执行该表达式。它证明可调的数值质量路径；上游 kg/m³ 到三维体积和质量的对应仍需解释。**E／ν 为公式换算与代码调参证据，ρ 的 SI 对应保留缺口。** 对“已有材料量直接投入”的任务，这比 Newton VBD 等现成路径多一层适配工作；用户喜欢的夹爪／塑形互动仍保留。[粒子质量与动力学](https://github.com/minliu01/SoftMAC/blob/main/softmac/engine/mpm_simulator.py)。

[现成挤压 GIF](https://raw.githubusercontent.com/minliu01/SoftMAC/main/media/grip_merged.gif)比较优化轨迹。公开 soft-rigid 路线有资源、renderer 与 GIF 输出；soft-cloth 的 DiffClothAI 依赖公开状态继续单列。若选择此候选，固定夹持动作和实际塑性截断，或明确切换 elastic 模式，再形成目标参数对比。[运行说明](https://github.com/minliu01/SoftMAC#readme)。

### MiNNIE：ν 与近不可压缩形变的专项选择

CUDA mixed FEM／GPU multigrid 联合求解位移和压力，重点处理近不可压缩材料的体积锁死；配置使用 Neo-Hookean，直接输入 `young / poisson / density`。bunny 为动态 E=2e6、ν=.49、ρ=1000；bulging 为准静态 E=1e6、ν=.45、ρ=1000。类别为**直接输入物理量；代码支持调参，效果待复跑**。bunny 的默认重力为 `[0,-3,0]`，物理尺度还应与配置 `scale` 一起确定。[加载代码](https://github.com/LwRuan/MiNNIE/blob/main/src/physics/simulator/cudasimulator.cpp)、[bunny 配置](https://github.com/LwRuan/MiNNIE/blob/main/configs/bunny.yaml)、[bulging 配置](https://github.com/LwRuan/MiNNIE/blob/main/configs/bulging.yaml)。

**推荐 bulging 的 ν 对照，动态备选 wave。** 后者是运动学球撞软块，论文 Fig.8 使用 E=3e5、ν=.4999；这些原场景属于方法对比或单组演示。bulging 还要求启用源码 `OscillationHack()`。碰撞采用配置中的 penalty，自碰撞按场景开关；bunny 默认关闭、bulging 开启。[论文](https://www.tiantianliu.cn/papers/ruan24minnie/ruan24minnie.pdf)、[wave 配置](https://github.com/LwRuan/MiNNIE/blob/main/configs/wave.yaml)。

公开求解器与 Vulkan 查看器使用 xmake、CUDA≥12 等开放依赖；作者 Houdini 成片属于后渲染。已读 README、engine 和 simulator 中，现成逐帧几何导出命令仍待补，所以 Blender 成片需衔接输出。若优先出玩具 E 视频，已有资产和输出路径的框架例可先承担；若优先 ν，这项准备工作有明确用途。[运行要求](https://github.com/LwRuan/MiNNIE#requirements)、[查看器实现](https://github.com/LwRuan/MiNNIE/blob/main/src/engine/engine.cpp)、[作者动态视频](https://www.bilibili.com/video/BV1xZS4YXE65)。

## 四个框架落实到具体软体路径

### Newton：优先 VBD 橡皮鸭，区分同框架的其他 solver

**材料与几何。** VBD 是体四面体 FEM 的隐式 Vertex Block Descent 求解，使用 Stable Neo-Hookean。`add_soft_mesh` 接 `TetMesh` 或体网格数据，`density` 为体密度；E／ν 按物理 Lamé 公式换成 `k_mu / k_lambda`，VBD 内核再做能量系数校准。调用方提供物理 Lamé 即可。表面网格需先四面体化。类别为 **E／ν 公式换算、ρ 直接输入；代码支持调参，效果待复跑**。[ModelBuilder](https://newton-physics.github.io/newton/latest/api/_generated/newton.ModelBuilder.html#newton.ModelBuilder.add_soft_mesh)、[v1.7.0 VBD 材料内核](https://github.com/newton-physics/newton/blob/v1.7.0/newton/_src/solvers/vbd/particle_vbd_kernels.py)。

**现成物件。** `softbody_franka` 自动取得橡皮鸭预制 TetMesh USD，按米建模；ρ=100、μ_L=λ_L=1e6、阻尼=1，按公式推得 E=2.5 MPa、ν=.25。这是示例设定值。鸭子用 VBD，机器人由 Featherstone 按关键帧运动，属于刚体运动驱动软体的单向耦合；例中自接触关闭。它适合固定动作后观察自由部位与释放回弹，机械臂反馈受力不是这一场景的展示内容。[鸭子源码](https://github.com/newton-physics/newton/blob/main/newton/examples/softbody/example_softbody_franka.py)、[VBD 接触与耦合说明](https://newton-physics.github.io/newton/latest/api/_generated/newton.solvers.SolverVBD.html)。

**观看与输出。** 当前定位到[官方静态预览](https://raw.githubusercontent.com/newton-physics/newton/main/docs/images/examples/example_softbody_franka.jpg)，该例对应的公开视频仍待定位。运行入口是 `python -m newton.examples softbody_franka`；每帧 `viewer.log_state` 可配 `ViewerUSD` 输出带时间采样的形变表面供后渲染，或用 ViewerGL 查看。另一现成 `softbody_hanging` 当前也用 VBD，原片／原例比较阻尼；作为 E 场景时要固定阻尼。[可视化与 USD 输出](https://newton-physics.github.io/newton/latest/guide/visualization.html)、[软梁源码](https://github.com/newton-physics/newton/blob/main/newton/examples/softbody/example_softbody_hanging.py)。

同框架其他路径保留以下区别：

| 路径 | 已核模型及场景 | 选择影响 |
| --- | --- | --- |
| **SemiImplicit** | 条件稳定的 Symplectic Euler；tet 内核采用含 `log(Ic+1)` 修正的另一 SNH 形式。VoMP 软块使用此路径 | 数值系数与步长按该内核解释，VBD 的材料换算结论仅适用于 VBD。[内核](https://github.com/newton-physics/newton/blob/main/newton/_src/solvers/semi_implicit/kernels_particle.py) |
| **XPBD** | 位置约束迭代，tet 分别处理形变／体积约束，以 μ／λ 构造 compliance；软体能力标为 experimental | 保留为独立实现；现成鸭子继续用 VBD。[能力表](https://newton-physics.github.io/newton/latest/solvers/index.html)、[tet 内核](https://github.com/newton-physics/newton/blob/main/newton/_src/solvers/xpbd/kernels.py) |
| **ImplicitMPM** | 隐式粒子／网格求解，支持 Drucker–Prager、黏性、硬化／软化等材料分支；`mpm_beam_twist` 有 E=5 MPa、ν=.45、ρ=1000，质量按粒子体积×ρ赋值 | 梁扭转是固体备选；具体弹性能量形式本轮未完整追至内核，材料分支另按所选场景确定。v1.7.0 修正应力归一化，旧调参录像需区分版本。[梁例](https://github.com/newton-physics/newton/blob/main/newton/examples/mpm/example_mpm_beam_twist.py)、[材料说明](https://newton-physics.github.io/newton/latest/api/_generated/newton.solvers.SolverImplicitMPM.html)、[发布迁移](https://github.com/newton-physics/newton/releases/tag/v1.7.0) |

### libuipc：体 FEM、接触和 Blender 输出结合较直接

**材料与接触。** CUDA 隐式增量势能求解，体材料选 `StableNeoHookean`（SNK1），接触选 IPC，支持软硬、软软与自接触。输入是 `ElasticModuli.youngs_poisson(E,ν)` 和 `mass_density=ρ`，单位为 Pa、kg/m³；体几何用四面体，已提供封闭表面的原生四面体化。类别为**直接输入物理量；代码支持调参，效果待复跑**。[体 FEM 教程](https://spirimirror.github.io/libuipc-doc/tutorial/fem/)、[本构规格](https://spirimirror.github.io/libuipc-doc/specification/constitutions/stable_neo_hookean/)、[几何条件](https://spirimirror.github.io/libuipc-doc/specification/tetrahedralization/)、[接触配置](https://spirimirror.github.io/libuipc-doc/specification/scene_configs/contact/)。

**版本会改变材料含义。** v0.0.29（2026-10-18）仍存旧系数 `μ=4μ_L/3、λ=λ_L+5μ_L/6`，10 月 19 日修复后为 `μ=μ_L、λ=λ_L+μ_L`。官方给出的旧映射例：输入 100 kPa／.3，实际小应变响应约为 124.54 kPa／.214286。选此路线应使用包含修复的实现，重新从物理量应用本构；已有原始系数属性的场景另按其版本处理。[旧 tag 源码](https://github.com/spiriMirror/libuipc/blob/v0.0.29/src/constitution/stable_neo_hookean.cpp)、[修复后的源码](https://github.com/spiriMirror/libuipc/blob/main/src/constitution/stable_neo_hookean.cpp)、[迁移解释](https://spirimirror.github.io/libuipc-doc/specification/constitutions/stable_neo_hookean/)。

**现成体软体选软兔。** `89_mas_bunny` 用 `bunny2.msh`、E=100 kPa、ν=.49、ρ=1000，接触地面且有 GUI／headless；`92_twisting_bar` 可作固定边界的杆扭转。两个易混示例需辨认：苹果抓取的苹果采用 ABD，bunny＋cloth 的兔子也是固定 ABD；这些分别展示接触和布料，体 FEM 的 E／ν 场景应从真正体弹性例开始。[软兔](https://github.com/spiriMirror/libuipc-samples/blob/main/examples/89_mas_bunny/main.py)、[扭杆](https://github.com/spiriMirror/libuipc-samples/blob/main/examples/92_twisting_bar/main.py)、[苹果](https://github.com/spiriMirror/libuipc-samples/blob/main/examples/94_robot_hand_grasp_apple/main.py)、[兔子盖布](https://github.com/spiriMirror/libuipc-samples/blob/main/examples/11_bunny_cloth/main.py)。

**输出。** 每步取回状态后可 `SceneIO.write_surface` 写 OBJ；独立 Blender 扩展把体形变写成 MDD，通过 Mesh Cache 播放和渲染。现有样例图例与[官方动态合集](https://spirimirror.github.io/libuipc-web/)支持场景观看，软兔的目标物理量对比仍待复跑。Python wheels、源码构建和 Blender 扩展是不同安装路径；材料修复条件适用于实际使用的求解库。[Blender 工作流](https://spirimirror.github.io/libuipc-doc/build_install/blender/)、[OBJ 输出示例](https://github.com/spiriMirror/libuipc-samples/blob/main/examples/94_robot_hand_grasp_apple/main.py)。

### PhysX：GPU 体 FEM 备选，采用当前 DeformableVolume API

**参数路径完整。** 当前 `PxDeformableVolumeMaterial` 可选默认 corotational 或 Neo-Hookean；E／ν 用 `setYoungsModulus / setPoissons`，ρ 通过 `PxDeformableVolumeExt::updateMass` 按体积更新质量。采用 m／kg／s 时对应 Pa 和 kg/m³。`SnippetDeformableVolume` 有 cube／sphere／cone，面网格经 cooking 生成体网格；示例 E=200 kPa、ν=.3、ρ=100，GPU dynamics／TGS 下接触地面。模拟网格与碰撞网格可分开，例中自接触关闭，引擎可另行开启。类别为**直接物理量及体积质量换算；代码支持调参，效果待复跑**。[本构头文件](https://github.com/NVIDIA-Omniverse/PhysX/blob/main/physx/include/PxDeformableVolumeMaterial.h)、[参数头文件](https://github.com/NVIDIA-Omniverse/PhysX/blob/main/physx/include/PxDeformableMaterial.h)、[完整体 FEM 示例](https://github.com/NVIDIA-Omniverse/PhysX/blob/main/physx/snippets/snippetdeformablevolume/SnippetDeformableVolume.cpp)。

**版本和接触条件。** 旧 5.4.1 的 SoftBody 名称已在 5.5 改成 DeformableVolume，5.9 移除旧兼容层。SDK、Omniverse 标签和较新的 ovphysx Python／USD 包各有发布线，选择时配套其文档。GPU 体形变可与刚体、关节链和其他体形变对象接触／附着；当前 ovphysx 文档列出静摩擦限制及一体一个 collider 等条件，夹持选择需据此判断。[5.5 迁移](https://nvidia-omniverse.github.io/PhysX/physx/5.5.0/docs/MigrationTo55.html)、[当前 changelog](https://github.com/NVIDIA-Omniverse/PhysX/blob/main/physx/CHANGELOG.md)、[ovphysx 体形变条件](https://nvidia-omniverse.github.io/PhysX/ovphysx/latest/simulation_setup/deformables.html)。

**输出与取舍。** C++ snippet 从 GPU 取回顶点并用 OpenGL／GLUT 绘制，独立几何缓存需要衔接；ovphysx 提供 USD 外观网格嵌入及 E／ν／ρ authoring。Isaac Lab 还有柔性 cuboid 教程。若已有 NVIDIA／Isaac 工作流，这条线值得选；本任务从具体玩具起步时，Newton 鸭子的场景和输出入口更集中。[C++ 显示链](https://github.com/NVIDIA-Omniverse/PhysX/blob/main/physx/snippets/snippetdeformablevolume/SnippetDeformableVolumeRender.cpp)、[体 FEM 文档与图例](https://nvidia-omniverse.github.io/PhysX/physx/5.7.0/docs/DeformableVolume.html)、[Isaac Lab 软块](https://docs.nvidia.com/isaaclab/2.3/getting-started/tutorials/interacting-with-a-deformable-object)。

### MuJoCo：机器人场景复用备选，ρ 转质量、默认体模型为 StVK

3D flex 接四面体，`flexcomp/elasticity` 的 `young / poisson` 直接输入 E／ν；`flexcomp mass` 是总质量，编译时均分到生成的节点 body。ρ 需经静止体积换成 `M=ρV0`；对不均匀体网格，还应处理均分质量与局部体积集总质量的差别。规则网格或 GMSH `.msh` 是明确入口。默认模型是 Saint Venant–Kirchhoff，适合有限转动、较有限的应变。类别为**E／ν 直接、ρ 公式换算；代码支持调参，效果待复跑**。[建模说明](https://mujoco.readthedocs.io/en/stable/modeling.html#deformable-objects)、[XML 材料及质量](https://mujoco.readthedocs.io/en/stable/XMLreference.html#flexcomp)。

现成 `floppy.xml` 是 24×4×4 网格柔性条与铰接刚性圆柱互动，E=50 kPa、ν=.2、总质量25，自接触关闭。flex 支持刚软接触及按模式配置的自接触；3.17 新增插值 flex 自接触。3.15 新增的实验性简化 Stable Neo-Hookean 要非插值 3D flex、程序设置 `mjsFlex.elastic3d=1` 和 discrete integrator，默认 XML 路径仍为 StVK。若需要大形变玩具，先选前面本构和示例更直接的路线。[floppy](https://github.com/google-deepmind/mujoco/blob/3.17.0/model/flex/floppy.xml)、[3.15 发布](https://github.com/google-deepmind/mujoco/releases/tag/3.15.0)、[3.17 发布](https://github.com/google-deepmind/mujoco/releases/tag/3.17.0)。

原生 MuJoCo 主要走 CPU，GPU MJWarp 的 flex 仍有实验性／特性覆盖条件。自带 renderer 可逐帧离屏渲染并编码视频；`sample/record.cc` 给出状态推进与 RGB 读回路径，Blender 几何缓存另接。现成场景入口为[flex 模型目录](https://github.com/google-deepmind/mujoco/tree/3.17.0/model/flex)，本次对应物理量对比视频仍待定位。[离屏录制](https://github.com/google-deepmind/mujoco/blob/main/sample/record.cc)、[MJWarp 边界](https://github.com/google-deepmind/mujoco_warp)。

## VoMP 下游例子：采用证据与直接复用条件

VoMP 已公开 libuipc 橙子和 Newton 软块的适配代码，支持“有人把材料场交给这些框架”的判断。进一步读实现后，有三项会影响本任务的直接复用：

| 下游路径 | 核实结果 | 对选择的影响 |
| --- | --- | --- |
| **libuipc 橙子材料** | `meshes.py` 在调用 `StableNeoHookean.apply_to` 后，把物理 μ_L／λ_L 直接覆盖到 tet 原始 `mu/lambda`；当前 SNK1 需要 λ_L+μ_L。均匀材料分支则走库的 `apply_to` | 异质材料路径需要配合当前本构适配。可先用均匀上游三量和修复后的本构；水果材质分层保留为后续所选场景条件。[覆盖代码](https://github.com/nv-tlabs/VoMP/blob/main/vomp/sim/meshes.py#L1384-L1489)、[本构系数语义](https://spirimirror.github.io/libuipc-doc/specification/constitutions/stable_neo_hookean/) |
| **libuipc 橙子输出** | GUI 分支嵌入高分辨率外观网格并写逐帧 OBJ；headless 分支直接写物理表面。配置引用 `assets/orange` 的 PLY、NPZ、OBJ | 基础输出路径明确；完整资产获取与 headless 保留带纹理外观仍需衔接。[配置](https://github.com/nv-tlabs/VoMP/blob/main/configs/sim/falling_oranges.json)、[两个输出分支](https://github.com/nv-tlabs/VoMP/blob/main/vomp/sim/main.py#L649-L691) |
| **Newton 软块** | 使用 SemiImplicit。材料 NPZ 的密度分支先读取 `state_0`，其赋值在分支之后；异常被捕获。源码据此推断，该分支会保留初始密度，且本构系数还需按 SemiImplicit 校准 | 作为公开下游适配证据保留；三参数成功应用的结论要等适配后复跑。主推玩具场景用材料路径已核的 VBD。[代码](https://github.com/nv-tlabs/VoMP/blob/main/simulation/newton/mesh_falling_sim.py#L130-L201) |

这些判断来自静态源码对照。VoMP 对 PhysX 的示例仍写为后续计划，现阶段可计为兼容思路；公开运行采用按已发布代码分别记录。[VoMP Simulation](https://github.com/nv-tlabs/VoMP#-simulation)。

## 维护、后续继承与实际采用

下表区分发布日期、代码／文档更新和实际集成。最近提交日期只表明可见仓库活动，具体更新内容决定其含义；论文发表和组织归属另计。以下证据可用于判断持续性，活跃用户规模仍缺少可比较统计。

| 候选 | 可核实的时间与更新内容 | 后续继承／采用及其选择含义 |
| --- | --- | --- |
| **FreeForm** | 预印本 **2026-05-28**；evaluation **2026-10-07** 初始发布，固定 Kaolin 修订与测试依赖。[论文](https://arxiv.org/abs/2605.29318)、[发布提交](https://github.com/nv-tlabs/freeform-eval/commit/a969a62f765c5bed5b23258321afddaf3f171bd8) | Kaolin 主库已有 RKPM 与相关教程，是明确的一方集成和延续。相关 News 仍在 unreleased/master 语境，0.18.0 徽章本身不足以认定发布包已包含全部功能。NVIDIA／Toronto 为组织背景，独立生产采用待证实。[Kaolin News](https://github.com/NVIDIAGameWorks/kaolin#news) |
| **PhysTwin** | ICCV 2025；可见 main 最近提交 **2026-08-24**，说明为 `update`；官方 News 有 2026-03 MPC 和 2026-08 Boba。[提交](https://github.com/Jianghanxiao/PhysTwin/commit/81c718790a37e5e0102eb77af2c6edd34a9db25f)、[官方更新](https://github.com/Jianghanxiao/PhysTwin#updates--roadmap) | Boba 是具体技术继承；维护意向与社区环境支持可查。Columbia／UIUC 背景及作者承诺与实际用户规模分开记录 |
| **Boba** | ECCV 2026；09-12／14 渲染优化之后，**2026-09-29** 继续加入批量 LBS 的 CUDA graph。[代码更新](https://github.com/jianxiapyh/Boba-Public/commit/71bb5e59c846e1d53059ea79d79e33b38681fa21)、[News](https://github.com/jianxiapyh/Boba-Public#news) | **ILLIXR 2026-09-21 v4.2** 明确集成 Boba 并给出 Quest 3 互动演示，属于真实下游软件集成；双方有团队交集，独立广泛采用程度另计。[发布公告](https://illixr.org/about/news/release_4.2) |
| **AnisoMPM** | SIGGRAPH 2020；作者页更新至 **2021-06-01**，默认分支最近 **2021-07-12** 更新数据下载链接。[作者页](https://joshuahwolper.com/anisompm)、[提交](https://github.com/penn-graphics-research/ziran2020/commit/5c52c26936b30d445e668cd7ef700bcbf9e6ff7e) | 历史论文实现与食物场景明确；官方采访中的进入 Houdini／内部软件是愿景，近期外部产品采用仍待证实。因用户的撕裂效果兴趣保留。[作者采访](https://blog.siggraph.org/2020/06/new-research-from-university-of-pennsylvania-examines-realistic-damage-mechanics.html/) |
| **SoftMAC** | IROS 2024；预印本修订 **2025-04-05**；默认分支最近 **2025-06-09** 是添加 LICENSE。[论文历史](https://arxiv.org/abs/2312.03297)、[提交](https://github.com/minliu01/SoftMAC/commit/8cb655a6cf9612f60fcc6191247441ce6ce861b7) | ManiFoundation 作者论文说明用 SoftMAC 生成 1000 个 ShapeNet 软物件、每物件100个样本；有共同作者，属于同研究网络的具体科研复用。这增加采用证据，SI 材料映射仍按源码解释。[论文附录](https://manifoundationmodel.github.io/static/pdf/manifoundation.pdf)、[项目及作者](https://manifoundationmodel.github.io/) |
| **MiNNIE** | SIGGRAPH Asia 2024；默认分支最近 **2024-10-27** 为 `init`。[提交](https://github.com/LwRuan/MiNNIE/commit/f250b23ca761f3c2327d1a92f58cc4ca4051a8f7) | 本轮官方仓库、作者资料及采用检索支持论文实现与示例公开，近期维护和具体外部集成仍待证实。作为 ν 专题候选保留，通用玩具路线优先度较低 |
| **Newton** | **v1.7.0，2026-10-14**，继续更新 VBD 接触、渲染及 MPM 材料换算。[发布](https://github.com/newton-physics/newton/releases/tag/v1.7.0) | Linux Foundation、Disney、DeepMind、NVIDIA 为组织背景。Isaac Lab 3.0 已有 volume-deformable 配置，VoMP 有下游代码，是具体接入证据；各例子质量仍分别判断。[Isaac Lab schema](https://docs.nvidia.com/isaaclab/3.0/concepts/physics-schemas)、[项目背景](https://github.com/newton-physics/newton) |
| **libuipc** | **v0.0.29，2026-10-18** 新增原生四面体化、扩展 Blender；**10-19** 修正材料校准。[发布](https://github.com/spiriMirror/libuipc/releases/tag/v0.0.29)、[修复说明](https://spirimirror.github.io/libuipc-doc/specification/constitutions/stable_neo_hookean/) | README **2026-03-29** 记录 Genesis AI 对 AL-IPC／关节／wheel 的贡献，VoMP 提供橙子集成。贡献、具体采用、作者 v1.0 计划分别记录；材料适配条件见上表。[News](https://github.com/spiriMirror/libuipc#news) |
| **PhysX** | **2026-10-27** Omniverse 110.3 发布线含 SDK 5.9.1；**10-15** ovphysx 0.7.0；当前 main 版本头为5.12，发布线各自配套。[发布页](https://github.com/NVIDIA-Omniverse/PhysX/releases)、[版本头](https://github.com/NVIDIA-Omniverse/PhysX/blob/main/physx/include/foundation/PxPhysicsVersion.h) | Isaac Lab 的 soft-body 教程提供同厂生态内的真实体形变集成。SAPIEN 等通用 PhysX 采用主要支持刚体／机器人生态判断；当前体 FEM 的独立三参数演示采用程度仍待具体证据。[Isaac Lab 教程](https://docs.nvidia.com/isaaclab/2.3/getting-started/tutorials/interacting-with-a-deformable-object) |
| **MuJoCo** | **3.15.0，2026-10-05** 加实验性 SNH；**3.17.0，10-22** 更新 flex 自接触等。[3.15](https://github.com/google-deepmind/mujoco/releases/tag/3.15.0)、[3.17](https://github.com/google-deepmind/mujoco/releases/tag/3.17.0) | flex 接触力公开讨论有使用者模型和代码，可确认具体接入尝试；通用机器人生态与当前3D大形变效果分别评价。[使用者讨论](https://github.com/google-deepmind/mujoco/discussions/2641) |

## 当前未决问题与接续

当前继续完成完整物体路线的比较：用哪些公开场景证明覆盖范围与运动质量；各路线在哪些材料、结构和接触条件下适用；现成数字资产怎样参与仿真并保留后渲染质量。原示例优先排序待这些证据补齐后重评。候选仍按实际物理实现判断，用户此前的效果兴趣继续保留。

各节具体缺口保留：FreeForm 原片资产、AnisoMPM 尺度与断裂表面、MiNNIE 导出、SoftMAC 质量约定，以及下游适配代码的材料映射。当前研究使用各项目自己的数据，格式和准备条件一并说明。复跑继续在用户选择后建票；SimReady 派生资产的研究评估许可及 AnisoMPM／MiNNIE 仓库许可状态沿[首轮研究](01-solid-deformation-candidates.md)核对。SoliDualSPHysics 的动态入口继续作为已有缺口；进入相应具体材料选择时再补。固液耦合沿[软体入液的密度差异展示](../issues/10-softbody-liquid-density-scene.md)接续。
