# 02：布料、薄壳与植物物件候选与动态演示

检索基准日：2026-10-30。对应[工单 02](../issues/02-cloth-shell-plant-candidates.md)与[探索规格](../spec.md)。本记录完成候选搜索、动态演示入口和参数依据整理；安装、仿真复跑及效果确认进入用户选择之后的阶段。

**AI 初步推荐：**布料与薄壳参数展示先看 **C-IPC**；帽子日常互动先看 **DiffCloth**；花材先看 **M-PhyGs** 的真实录像／仿真并排短片；盆栽先看 **VoMP** 的厨房互动。若优先考察现成的 E、ρ 对照，**Gazebo Plants** 的论文图 6、7 最直接。**libuipc 的 NeoHookeanShell** 适合后续制作受控参数场景；其 Baraff–Witkin 布料模块需要单独解释参数含义。

## 先看这些动态演示

“看点”是观看导引。M-PhyGs 康乃馨和 VoMP 厨房短片已抽查实际视频画面；其余根据作者项目页、论文与视频入口整理，未逐段核实播放内容。表中均不提供未经核实的时间戳。

| 建议顺序 | 候选、时间 | 动态入口与看点 | 实际覆盖 | 参数控制方式 | 演示证据 |
| --- | --- | --- | --- | --- | --- |
| 1 | **C-IPC，SIGGRAPH 2021** | [YouTube](https://www.youtube.com/watch?v=jvBSDXxsgRA)、[Bilibili](https://www.bilibili.com/video/BV1zq4y177Qs/)；找抽桌布、扭布筒、桥式洗牌片段，看折皱、回弹与接触 | 桌布、衣裙、纸牌薄壳、杆；塑料瓶和植物需另建场景 | **直接输入物理量** E、ν、ρ；膜与弯曲倍率另设 | **代码支持调参，效果待复跑**；现有速度／厚度／分辨率对照另行说明 |
| 2 | **DiffCloth，SIGGRAPH 2022** | [作者视频](https://www.youtube.com/watch?v=WWmWuhJcPYY)、[帽子动图](https://people.csail.mit.edu/liyifei/publication/diffcloth/hat.gif)；看双端夹持戴帽、帽沿折叠，再看旋转裙 | 实际帽子、袜子、裙子、T 恤、旗帜 | **视觉调节** `k_stretch/k_bend`；`density` 与体密度 ρ 的单位换算待核 | **代码支持调参，效果待复跑**；作者有材料优化前后演示，单独 E／ν／ρ 对照待核 |
| 3 | **M-PhyGs，2025 预印本／CVPR 2026** | [9 秒康乃馨 MP4](https://vision.ist.i.kyoto-u.ac.jp/assets/videos/m-phygs_carnation.mp4)、[跨动作 MP4](https://vision.ist.i.kyoto-u.ac.jp/assets/videos/m-phygs_cross_carnation.mp4)、[完整视频](https://vimeo.com/1147652823)；比较左侧实拍、中间仿真、右侧 E 伪彩中的花茎与花头运动 | 单枝花材、多花头枝条、叶片；整束捆扎花束仍待覆盖 | **直接输入物理量**：粒子 E、ρ；ν 在论文估计阶段固定 | **代码支持调参，效果待复跑**；已抽查的三栏短片是实拍／仿真／材料场可视化 |
| 4 | **VoMP，ICLR 2026** | [50 秒厨房盆栽 MP4](https://research.nvidia.com/labs/sil/projects/vomp/assets/videos/nurec_compressed/kitchen_02_4k_spp128_rgb.mp4)、[总视频](https://www.youtube.com/watch?v=-0HnbUca1h4)；看机械臂接触后的枝叶响应 | 可见白盆、植株及机械臂；盆土内部力学待核 | **直接输入物理量**：预测 E、ν、ρ 场，再交给下游仿真器 | **代码支持调参，效果待复跑**；盆栽短片为视觉效果参考，具体场景配置待核 |
| 5 | **Gazebo Plants，2024** | [作者视频](https://youtu.be/r8Q31w4bNbs)；找枝条受扰、采果、植物参数片段；配合论文图 6、7 | 枝干、图结构叶片、球形果实；花盆与土未构成已核实的动力学组件 | **直接输入物理量** E、ρ；ν 对应关系待核 | **仅有视觉效果参考**；另有已核实的论文 E、ρ 对照，视频中的对应段落待核 |
| 6 | **libuipc，2024–2026；StiffGIPC 2025 技术来源** | [官方演示合集](https://spirimirror.github.io/libuipc-web/)、[官方嵌入视频 1](https://www.bilibili.com/video/BV1Yzp8zmEDE/)、[视频 2](https://www.bilibili.com/video/BV1FMU6Y6Eu2/)；结合 cloth stack／bunny + cloth 示例看接触与折叠 | 布片、缝合布、布堆、杆及软硬耦合；帽子／瓶子需核实具体资产 | **直接输入物理量＋公式换算**，依所选膜模型而异 | **代码支持调参，效果待复跑** |
| 7 | **Better Bending，SIGGRAPH 2026；libshell** | [作者 MP4](https://zhenchen-jay.github.io/uploads/videos/Better_bending.mp4)、[项目页](https://zhenchen-jay.github.io/publication/betterbending/)；看薄片弯曲、锐折和侧向屈曲，注意比较变量主要为离散模型 | 薄片／薄壳基准；库示例含兔形壳体，完整日常物件资产待核 | **公式换算** E、ν → 二维 Lamé 参数；ρ 动态输入待核 | **代码支持调参，效果待复跑**；公开代码已确认的重点为能量与侧向屈曲基准 |
| 8 | **Radioss 塑料瓶压缩示例，官方工程案例；发布日期待核，2026-10-30 检索** | [Wistia 视频](https://fast.wistia.net/embed/iframe/d8hugtye24)、[模型与说明](https://community.altair.com/discussion/62588/simulation-of-plastic-bottle-compression-with-radioss)；看同瓶形在空瓶、空气、水条件下的屈曲 | 真正的塑料瓶瓶体屈曲及内部内容物条件 | 示例材料卡中的 E、ν、ρ 及单位待核；已核实的控制变量为内容物／加载条件 | **仅有视觉效果参考**；作为商业流程观看参考保留 |

演示证据按规格独立于参数控制方式标注。此次尚未把任一入口认定为“已核实视频中的单因素 E／ν／ρ 对比”。公开代码、论文对照和材料伪彩各自支持不同结论。

## 对象覆盖与缺口

| 对象 | 已找到的具体覆盖 | 当前缺口及对选择的影响 |
| --- | --- | --- |
| 布料 | C-IPC 桌布／衣裙；DiffCloth 衣物；libuipc 布堆；2025 CPU 布料与 Progressive Dynamics++ | 候选充分；主要选择依据是目标物理量映射与日常场景偏好 |
| 帽子 | DiffCloth 戴帽；Progressive Dynamics++ 五顶软帽抛向衣帽架 | 硬质帽壳、帽檐内衬、缝线等分材料结构尚未核实 |
| 薄壳 | C-IPC 纸牌；Better Bending 弯曲／屈曲；ARCSim 褶皱与塑性薄片 | 材料层数、厚度及弯曲模型必须与上游几何共同确定 |
| 塑料瓶 | Radioss 官方瓶体压缩；薄壳候选具备建模基础 | **公开、现成、无需商业求解器的瓶体屈曲复跑场景仍缺**；薄壳能力本身不等于已有瓶子例子 |
| 花束 | M-PhyGs 实际花茎、花头、叶片及多花头枝条 | **整束多茎接触、捆扎／包装与插花容器联动仍缺**；单枝花材可先帮助选择运动风格 |
| 盆栽 | VoMP 厨房白盆植物；Gazebo Plants 枝叶受扰 | 可见花盆与植物互动已覆盖；**盆体是否可动、根系／盆土是否参与力学、植株与盆的连接**仍待核 |
| ν 的直观效果 | C-IPC 各向同性壳、libuipc NeoHookeanShell、libshell 有本构依据 | **日常布料／植物现成 ν 参数对比视频仍缺**；建议使用带网格纹理的薄片夹持拉伸 |

上述缺口是候选覆盖结论，当前研究阶段可据此收束。用户选择之后，再围绕所选候选核实运行环境与资产。

## 重点候选与可追溯参数

### 1. C-IPC：优先做布料与薄壳的 E 对比

**已核实。**作者演示包含抽桌布、衣裙、纸牌洗牌及扭布筒。抽桌布明确比较的是拉动速度，扭布筒比较的是接触厚度；球面布褶皱例同时改变分辨率和弯曲刚度。这些有助于看视觉合理性，但其变量与目标物理量对照须分开。[项目页](https://ipc-sim.github.io/C-IPC/)、[论文](https://ipc-sim.github.io/C-IPC/file/paper.pdf)

各向同性壳初始化显式接收 `density, E, nu, thickness`。补充材料给出膜能与铰链弯曲能，后者采用 `k = E h³/[24(1−ν²)]`。代码还允许 `membEMult` 与 `bendEMult` 独立变化，因此一个统一材料 E 对比，应按相同比例改变两者；只变其中一个时，标注“膜 E”或“弯曲 E”。[补充材料 §9](https://ipc-sim.github.io/C-IPC/file/supp.pdf)、[初始化及预设代码](https://raw.githubusercontent.com/ipc-sim/Codim-IPC/main/Python/Drivers/FEMDiscreteShellBase.py)

**附加输入及已知数值。**代码中的各向同性棉布预设为 E 基值 0.821 MPa、ν=0.243、ρ=472.641509 kg/m³、厚度 0.318 mm、应变上限 1.0608；纸预设为 E 基值 3 GPa、ν=0.3、ρ=800 kg/m³、厚度 0.3 mm。这是**材料预设基值**，实际膜与弯曲 E 还取决于场景倍率；代码另存有棉布膜倍率 0.1。洗牌示例 `membEMult=bendEMult=1`、Δt=0.02 s、牌地摩擦 0.1、牌间摩擦 0.02，并另外设置接触距离／偏移。[材料预设](https://raw.githubusercontent.com/ipc-sim/Codim-IPC/main/Python/Drivers/FEMDiscreteShellBase.py)、[洗牌脚本](https://raw.githubusercontent.com/ipc-sim/Codim-IPC/main/Projects/FEMShell/8_precision_card_shuffle.py)

**代码／示例／商业依赖／渲染。**[官方仓库](https://github.com/ipc-sim/Codim-IPC)公开源码及论文示例，README 给出 `Projects/FEMShell/batch.py` 入口；批处理含参数倍率、厚度和速度组合。依赖为公开 C++／Python 工具，后续复跑可沿用示例网格。论文成片的完整渲染场景尚待核实；可按仿真网格输出另接 Blender。[批处理](https://raw.githubusercontent.com/ipc-sim/Codim-IPC/main/Projects/FEMShell/batch.py)

**AI 推断与建议。**先用同一纸牌／薄片的弯曲释放或低速受力下垂，对比 E×{0.1,1,10}，固定 ν、ρ、厚度、接触、夹持、相机及求解设置。预期回弹与曲率差异更容易理解；实际差异待复跑。抽桌布更适合先欣赏接触效果，摩擦与速度会增加材料判断的干扰。

### 2. DiffCloth：帽子覆盖最直接，参数先按代码原名解释

**已核实。**帽子例优化两个夹持端的轨迹，使帽子戴到头模上；作者还提供帽子控制器。旋转裙例优化的是布料 `density` 与 `k_bend`；旗帜例同时优化拉伸、弯曲、密度及风场。帽子轨迹优化、裙子材料优化应分别观看。[项目页](https://people.csail.mit.edu/liyifei/publication/diffcloth/)、[论文附录 B](https://people.csail.mit.edu/liyifei/uploads/diffcloth-lowres.pdf)

**参数控制方式。**`k_stiff_stretching`、`k_stiff_bending`、`density` 是明确代码字段。前两者归为**视觉调节**，当前已读资料未给出可直接套用的 E、ν 校准；`density` 的面积／体积单位和厚度约定待核后，再决定如何接收上游 ρ。[结构定义](https://raw.githubusercontent.com/omegaiota/DiffCloth/main/src/code/simulation/Simulation.h)

**附加输入。**夹持点、夹持轨迹、摩擦、风场、网格与时间步均影响演示。作者帽子例使用 Δt=1/100 s、400 步；裙子例使用 Δt=1/120 s、125 步，属于**示例设置**。通用 `density`、刚度与碰撞厚度默认值尚待逐场景核实。[项目页](https://people.csail.mit.edu/liyifei/publication/diffcloth/)

**代码／示例／商业依赖／渲染。**[MIT 许可官方仓库](https://github.com/omegaiota/DiffCloth)含 `hat/sock/dress/tshirt/sphere` 示例、T 恤已保存优化输出及帽子控制器入口，提供自定义 OpenGL viewer；公开 C++／Python 工具链，作者测试 Ubuntu 22.04／macOS 12。高质量离线渲染资产是否随库提供待核。

**AI 推断与建议。**固定戴帽轨迹，分别比较 `k_bend×{0.1,1,10}` 或 `density×{0.25,1,4}`；视频标注代码原量。若目标必须是上游 E、ν，优先使用 C-IPC／NeoHookeanShell 制作受控薄片场景。帽子例更适合展示“过软导致帽沿塌陷／过硬难以贴合”的候选风格，效果待复跑。

### 3. libuipc：直接参数路线清楚，但要选对膜模型

**已核实。**官方库提供 CUDA 的刚体、FEM、布料、杆耦合；示例目录列出 `bunny + cloth`、`sewing pattern`、`cloth stack`。项目于 2024 年发布 alpha，2025 年 StiffGIPC 提供相关求解技术，2026 年持续更新。[官方仓库与示例目录](https://github.com/spiriMirror/libuipc)、[演示站](https://spirimirror.github.io/libuipc-web/)

**参数控制方式须分两条。**

- **NeoHookeanShell：直接输入 E、ν、ρ。**默认参数为 `ElasticModuli2D.youngs_poisson(1 MPa,0.49)`、`mass_density=200 kg/m³`、`thickness=0.001 m`；二维 Lamé 参数进入膜能。该 `thickness` 是**单侧半厚度 r**，能量与质量使用全厚度 `h=2r`。[头文件默认值](https://raw.githubusercontent.com/spiriMirror/libuipc/main/include/uipc/constitution/neo_hookean_shell.h)、[本构文档](https://spirimirror.github.io/libuipc-doc/specification/constitutions/neo_hookean_shell/)
- **StrainLimitingBaraffWitkinShell：E、ν 名称对应系数换算。**官方文档明确其方向刚度使用 `K=E(2r)/(1−ν²)`，法向应变之间缺少交叉项，输入 ν 不规定横向收缩比。因此这条路线应归为**公式换算／视觉调节**，ρ 仍为直接输入；适合布料效果和方向刚度，ν 展示应换用具备对应本构的模型。[参数解释](https://spirimirror.github.io/libuipc-doc/specification/constitutions/strain_limiting_baraff_witkin_shell/)

**弯曲与默认值。**`DiscreteShellBending` 的物理参数重载使用 `κ=E(2r)³/[24(1−ν²)]`，单位 N·m；原始 `bending_stiffness` 重载是铰链能系数。官方注明 2026-10-19 修正过公式系数，选定版本应保持一致。便捷布料重载默认 ρ=200、r=0.001 m、`strain_rate=100`；教程中拉伸 E=50 kPa、剪切 E=0.5 kPa、ν=0.40、Δt=0.01 s、摩擦 0.3 是**教程场景值**。[弯曲文档](https://spirimirror.github.io/libuipc-doc/specification/constitutions/discrete_shell_bending/)、[教程](https://spirimirror.github.io/libuipc-doc/tutorial/cloth/)

**代码／示例／商业依赖／渲染。**Apache-2.0，Python／C++，GPU 仿真；现成样例有 GUI，多数有 headless 入口。官方仓库存在 Blender 集成，具体视频导出链在选中场景后核实。[代码](https://github.com/spiriMirror/libuipc)、[样例](https://github.com/spiriMirror/libuipc-samples)

**AI 推断与建议。**采用 NeoHookeanShell＋公式弯曲，同步改变膜和弯曲 E。可从默认 E=1 MPa 改为 {0.1,1,10} MPa；ν 另做 {0,0.25,0.45} 的小应变薄片拉伸；ρ 另做 {50,200,800} kg/m³ 的相同外力脉冲。固定全厚度 2 mm 及其他默认值，实际可见程度待复跑。

### 4. Better Bending＋libshell：最新薄壳方法与理论对照候选

**已核实。**SIGGRAPH 2026 工作系统比较薄壳弯曲离散模型，含解析基准、锐折与大规模静／动态问题，提出 BAC 模型；视频适合观察“数值弯曲模型本身会怎样改变结果”。[作者项目页](https://zhenchen-jay.github.io/publication/betterbending/)

**参数控制方式。**libshell 示例明确换算 `λ₂D=Eν/(1−ν²)`、`μ=E/[2(1+ν)]`；提供 StVK、Neo-Hookean 模型及逐面厚度。示例中的 `young=1`、`thickness=0.1`、`poisson=0.5` 是**无 SI 标定说明的示例初值**，适用范围按二维模型解释。该示例计算静态能量极小化，ρ 与惯性不在已核实输入中。[库说明](https://github.com/evouga/libshell)、[示例源文件](https://raw.githubusercontent.com/evouga/libshell/master/example/static_sim.cpp)

**代码／示例／商业依赖／渲染。**[Better Bending 仓库](https://github.com/evouga/better-bending)公开能量／锐折与侧向屈曲基准，采用 libshell 和 Polyscope；libshell 为 MPL-2.0，论文基准仓库为 MIT。现有代码支持薄片实验；作者动态成片的全部资产与时间积分配置待核，Polyscope 可供几何查看。

**AI 推断与建议。**把它放在“弯曲／屈曲与理论定义”的备选位。若采用现成侧向屈曲实验，可固定几何、厚度、边界和载荷，在同一模型中比较 E×{0.1,1,10}；若原实验是位移驱动，应同时看反力，单看最终形状可能对 E 不敏感。动态释放与 ρ 对比须先确认动态例子，效果待复跑。

### 5. Gazebo Plants：E、ρ 对照依据最明确的枝条候选

**已核实。**Cosserat 杆描述枝干，叶片也转换成图结构，果实简化为附着球体；机器人向植物施加单向影响。论文直接把 `σ_stiffness` 解释为节点杨氏模量，ν 的对应关系待核。[论文 §3–4](https://arxiv.org/html/2402.02570v1)

**已有参数对照。**论文图 6 比较 E={1,2,3}×10⁷ Pa，固定节点间距参数 0.02 m 与 ρ=300 kg/m³；图 7 比较 ρ={300,600,900} kg/m³，固定同一间距与 E=2×10⁷ Pa。这是**作者实验取值**，通用默认值待核。两组静态图已核实；[作者视频](https://youtu.be/r8Q31w4bNbs)的相应动态段落尚待核实。

**附加输入／可用性。**枝条半径、拓扑、节点间距、叶果附着、风及断裂阈值都会影响运动。论文尤其说明节点数改变也会改变表观柔软度，参数对比需固定离散化。插件为 Gazebo 工作流，作者声明源码可按 MPL-2.0 **应请求分享**；当前公开下载、示例资产与完整默认配置仍待核。仿真与场景可视化基于 Gazebo＋自有插件，资产建模及最终渲染的商业软件依赖待核。[论文与代码获取说明](https://arxiv.org/html/2402.02570v1)

**AI 推断与建议。**优先复用图 6、7 的数值组，再以相同位置的横向力脉冲观察枝端偏移与回摆。E 和 ρ 分开变化；具体力值沿用作者例子或选中后根据尺寸设置。它对“枝条太软／过重”的日常判断较友好，适用花束中的茎和盆栽枝叶部分。

### 6. M-PhyGs：可与真实花材直接并排观看

**已核实。**2025-12 预印本、CVPR 2026；Phlowers 含 10 种花材／叶片、每种 5 个拍摄视角，动作包括人将花插入花插座。实际抽查康乃馨 9 秒片段，左列实拍、中列仿真、右列 E 伪彩；能够定位花茎、花头运动。物体覆盖集中于单枝／多花头枝条和叶片。[项目与数据说明](https://vision.ist.i.kyoto-u.ac.jp/research/m-phygs/)

**参数控制方式。**MLS-MPM 粒子赋予 E、ρ、ν，估计过程优化 E、ρ、保持 ν 常量；接触通过从视频恢复的粒子速度边界驱动。E 颜色分区是材料场可视化。改变同一材料场并比较前向仿真，才构成此任务的参数对比。[论文 §3.1–3.2](https://arxiv.org/html/2512.16885v1)

**附加输入与已知数值。**康乃馨配置含重力 `[0,0,−9.8]` m/s²、输出帧间隔约 1/60 s、仿真步长 `4.34×10⁻⁵ s`、`sim_grid_size=64`、固定区域、粒子填充与碰撞面；这些是**该配置值**，README 前向命令还会覆写 `grid_reso=90`。初始受重力状态、接触轨迹和固定区域需随参数实验保持约定一致。[配置文件](https://raw.githubusercontent.com/kyotovision-public/m-phygs/main/confs/push_carnation.json)

**代码／示例／商业依赖／渲染。**[官方代码](https://github.com/kyotovision-public/m-phygs)已公开，README 链接 Phlowers 与预处理数据，提供前向仿真及 Gaussian viewer。实际所需材料 checkpoint 是否随下载提供、GPU 显存与环境兼容性待选中后核实；公开工具链以 MLS-MPM 和 Gaussian 渲染为主。复跑优先使用预处理资产，材料重估训练属于另一笔投入。

**AI 推断与建议。**固定花形、接触轨迹、ν 与密度场，分别对花茎 E×{0.1,1,10}；另对 ρ×{0.25,1,4}，保持各材料分区和渲染一致。重点观察手释放后的花头回摆，而非夹持点受指定轨迹控制的部分。拟定的倍数组效果待复跑。

### 7. VoMP：盆栽的观看入口，材料场与仿真器分别核实

**已核实。**ICLR 2026 方法从 mesh／Gaussian 等表示预测体积 E、ν、ρ 场，再交给仿真器。厨房片段抽查到白色盆栽与机械臂互动；作者还给出 [100 棵树与推土机](https://research.nvidia.com/labs/sil/projects/vomp/assets/videos/splatsim_compressed/ficus_dozer.mp4)、[65 株榕树落入容器](https://research.nvidia.com/labs/sil/projects/vomp/assets/videos/splatsim_compressed/fiscus_container_b.mp4) 两种复杂场景。盆内土／根系、盆体自由度仍待核。[项目页](https://research.nvidia.com/labs/sil/projects/vomp/)

**参数与可用性。**[官方仓库](https://github.com/nv-tlabs/VoMP)分别列出 Simplicits、warp.fem、libuipc、Newton 路线。Newton 的高／低 E 立方体与 libuipc 的橙子是已列明例子，不能据此推定盆栽视频的整套工程已打包。公开材料场字段有 `youngs_modulus/poisson_ratio/density`，具体求解器的单位、插值、质量与厚度约定随场景核实。仿真器、网格／采样、支撑、接触、风和阻尼均是附加输入；盆栽通用默认值尚待核。

**代码／示例／商业依赖／渲染。**代码、模型和数据入口已公开；Gaussian 场景具备照片式渲染，mesh 路线可输出逐帧表面。公开路线包括 libuipc／Warp／Newton；厨房 NuRec 演示的完整运行与渲染依赖应按该具体例子确认。

**AI 推断与建议。**选中后优先核实一盆植物的资产和支撑配置，再固定盆／支撑与机械臂轨迹，只对植株材料区域 E×{0.1,1,10}；ρ 单独×{0.25,1,4}。给叶片与茎同时套倍率可保留原相对材料差异。花盆、土和根的处理若未明确，结果只解释为植株响应。效果待复跑。

### 8. 塑料瓶：官方压缩演示可看，开放复跑场景仍需补齐

**已核实。**Radioss 案例使用同一瓶形比较五种模型：空瓶、空气、两种水模型及带水重力效应的模型；页面链接视频、模型 ZIP 和说明 PDF。比较变量包含内容物、压力模型与加载条件，现有资料支持瓶体屈曲的日常视觉参考。[官方案例](https://community.altair.com/discussion/62588/simulation-of-plastic-bottle-compression-with-radioss)

**参数／附加输入。**瓶壁 E、ν、ρ、厚度分布、塑性／屈服参数、盖口密封、内压和加载速率会影响结果。该页面未直接列出完整材料卡及默认值，取值待核。给定几何和 E、ν、ρ 后，仍需瓶壁厚度；若展示永久压瘪，还需要弹塑性输入。

**代码／示例／商业依赖／渲染。**官网列出模型 ZIP 与 PDF 附件入口；本次直接访问 PDF 返回 403，附件可获取性和材料卡内容待核。流程围绕商业 Radioss 及其前后处理，当前列为**观看参考**；OpenRadioss 对该模型卡与输出流程的兼容性仍待核，进入复跑候选前需满足规格的商业求解器排除条件。

**AI 推断与建议。**未来可在 C-IPC／libuipc 等薄壳框架构造空瓶弹性压缩：沿用同一中面、厚度和约束，E×{0.1,1,10}，固定 ν、ρ。位移驱动应同步展示反力；固定外力／压力更适合比较形变幅度。该建议尚缺现成瓶子示例，实际屈曲与回弹效果待复跑。

## 保留的同类候选与最新补充

这些候选提供不同展示价值，优先级依据当前参数任务与资产证据排序。表中“待核”用于明确当前已到达的检索边界。

| 候选与原始来源 | 场景／动态入口 | 参数控制方式；演示证据 | 附加输入、代码／资产、商业依赖与渲染状态 | 保留理由 |
| --- | --- | --- | --- | --- |
| **Progressive Dynamics++，SIGGRAPH 2025**：[作者页](https://pcs-sim.github.io/pd++/) | 五顶软帽抛向衣帽架、气球碰撞；[5 分钟作者视频](https://dl.dropboxusercontent.com/scl/fi/ni313logyky24i6lt8bmd/PD-_Vid.mp4?rlkey=db9tmv1z375wfy13gj4xg69gq) | 物理参数输入细节**待核**；当前按**仅有视觉效果参考**，主要比较 LOD／积分算法 | 分辨率层次、初速度、摩擦；项目页有论文与视频，公开仿真代码／示例数据待核；作者致谢 Houdini，求解与渲染依赖需分别核实 | 2025 年明确帽子实例，日常运动比纯基准更易看懂 |
| **Unlocking Thickness Modeling for Codimensional Contact Simulation，2025-10 预印本**：[作者论文](https://arxiv.org/html/2510.10256v1) | 厚布垂落、绕球、扭转、纱线织物；当前已核实论文图，动态直链待补 | 论文**直接输入** E=0.8 MPa、ν=0.3、ρ=500 kg/m³；**仅有视觉效果参考**，参数影响待复跑 | 图 9 厚度 0.7 mm，图 11 为 1 mm；均为实验值。网格／接触过滤也改变观感；公开代码、示例、商业依赖和渲染流程待核 | 提醒厚布接触锁定会造成“看起来过硬”，可解释材料值以外的模型因素 |
| **High-performance CPU Cloth Simulation Using Domain-decomposed Projective Dynamics，SIGGRAPH 2025**：[作者页](https://sig25ddmpd.github.io/) | [作者视频](https://www.youtube.com/watch?v=INF-p3T1PZ0)；窗帘拉开、四层桌布、布带结 | E／ν／ρ 映射**待核**；**仅有视觉效果参考** | 布料刚度、分域／网格及接触；论文视频公开，代码与对应资产、默认值和渲染依赖待核 | 布料画面丰富；其研究重点是 CPU 性能，参数任务收益需后续确认 |
| **ARCSim，2012／2013／2014**：[作者发布页](https://jamesobrien.com/resources/ARCSim/) | 布、纸、塑料／金属薄片的褶皱、压皱、撕裂；[Folding/Crumpling 原始论文入口](https://jamesobrien.com/resources/ARCSim/)内相关视频 | 数据驱动布料模型／弯曲塑性；先归**视觉调节，E／ν／ρ 换算待核**；**仅有视觉效果参考** | 附加塑性、弱化、重网格设置；公开代码包含材料数据库与动作数据，研究许可需按用途确认；独立求解器，最终渲染另核 | 有价值的既有薄片项目，尤其永久折痕；材料模型复杂度高于首选 |
| **Botanical Materials Based on Biomechanics，SIGGRAPH 2017**：[作者页](https://viterbi-web.usc.edu/~jbarbic/proceduralMaterials/) | 植物／树枝的生物力学材料分布；作者页视频入口 | 生物力学规律生成空间变化材料，E、ρ、阻尼映射细节待核；**仅有视觉效果参考** | 枝条层次、空间变化和阻尼；代码、资产、通用默认、商业依赖及渲染配套待核 | 适合复杂树枝材料分区，作为旧项目补充 |

检索也发现作者列为 **SIGGRAPH Asia 2026（12 月）** 的 [Beyond Kirchhoff–Love](https://viterbi-web.usc.edu/~jbarbic/gshells/)；在本记录基准日按**作者已公开、会议尚未举行**处理，暂保留为薄壳方法线索，参数、代码与具体物件演示待核。这里的“最新”指已检索到的相关公开资料，不声称覆盖全部 2026 年论文。

## 目标物理量如何变成可观看的参数对比

以下为 **AI 提出的展示场景**。表中数值服务于明显差异的探索，所有新增对照的可见效果均待复跑。扫描同一物理 E 或 ν 时，在固定厚度下同步更新膜内 Lamé 参数与派生弯曲系数 κ；独立弯曲滑块对比则按其原生控制量标注。

| 目标 | 推荐场景与候选 | 实际取值／倍率 | 固定因素与观察对象 |
| --- | --- | --- | --- |
| E | C-IPC 纸牌／薄片弯曲后释放；或 libuipc NeoHookeanShell 薄片受同一外力下垂 | C-IPC 纸预设 E={0.3,3,30} GPa；libuipc E={0.1,1,10} MPa。各自代表独立实验 | ν、ρ、厚度、网格、夹持／力、接触、时间步与镜头固定；统一材料实验同步更新膜与弯曲 E；观察偏移、回弹周期、折皱 |
| ν | 带方格纹理的各向同性薄片，小应变单向夹持拉伸；C-IPC／NeoHookeanShell | ν={0,0.25,0.45}；固定 E；相同轴向伸长如 2% | 厚度、ρ、几何和自由侧边一致；观察横向收缩并标注宽度。按理论定义解释更合适；Baraff–Witkin 的系数型 ν 不用于该场景 |
| ρ | 同一薄片／枝条受到同一短时横向力，再自由回摆 | libuipc ρ={50,200,800} kg/m³；或 Gazebo 论文已有 {300,600,900} kg/m³ | E、ν、厚度／半径、支撑、阻尼、外力时程与网格固定；观察加速度、幅度和周期。纯真空自由落体对密度缺少区分力 |
| 植物 E | M-PhyGs 花茎／VoMP 植株区域，固定其他材料分区 | E 场×{0.1,1,10} | 同一几何、ν／ρ 场、支撑、接触轨迹和初始受重力状态处理；观察释放后枝端／花头运动 |
| 帽子软硬 | DiffCloth 现成戴帽轨迹 | `k_bend×{0.1,1,10}`，或另做 `density×{0.25,1,4}` | 一次只改一个原生量，固定夹持轨迹与拉伸刚度；标注代码量，避免映射未经核实的 E／ν |
| 塑料瓶 E | 未来空瓶弹性屈曲或侧向按压 | E×{0.1,1,10}；E₀取所选模型的有据基值 | 固定壁厚、密封／内压、塑性设定、加载及初始几何；位移驱动加反力曲线，永久压瘪另需塑性参数 |

薄壳参数解释依据：在所引用的各向同性壳模型中，二维 Lamé 换算与弯曲项均包含 E、ν，质量还依赖密度与厚度。厚度改变会同时影响质量、膜力和弯曲，因此厚度应在目标参数对比中固定。[C-IPC 补充材料](https://ipc-sim.github.io/C-IPC/file/supp.pdf)、[libshell 换算实现](https://raw.githubusercontent.com/evouga/libshell/master/example/static_sim.cpp)、[libuipc 厚度／质量约定](https://spirimirror.github.io/libuipc-doc/tutorial/cloth/)

**AI 判断的适用范围。**同一静态下垂形状可能由 E、ρ、厚度或支撑的不同组合产生；单段视觉合理性不足以分别标定三个目标物理量。当前最有收益的是分开改 E、ν、ρ，并保留固定因素。高质量渲染有助于理解运动，参数场颜色和几何替换应作为独立的展示信息。

## 本次交接

- 已具备布料、帽子、花材与盆栽的观看入口，以及薄壳和塑料瓶的补充入口；2025／2026 工作与既有可用项目并列。
- 用户可直接按先看表审阅；后续复跑优先核实选中候选的具体示例、材料默认值和视频导出。Gazebo 源码获取、M-PhyGs checkpoint、VoMP 盆栽配置与开放塑料瓶场景是各自明确的待核项。
- 本次工作仅研究公开资料、读取公开源码并抽查两段作者视频画面；本地仿真安装与运行尚处于后续阶段。
