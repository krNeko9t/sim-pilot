# 知名仿真产品的工程适配与完整物体案例

研究日期：2026-11-01。承接主工作区的《知名仿真产品的工程适配与完整物体案例》研究票；用于用户返回《审阅固体主力路线与首组参数场景》选择路线。研究分支：`research/established-simulation-products`。本次阅读官方文档、源码、发布记录、公开使用者资料和原始论文，定位公开视频及资产；未安装或运行仿真。视频看点依据作者说明与对应源码，链接检查与完整播放评审分别记录。

## 推荐与新增候选

**本轮建议以 Newton 作为近期工程主力候选，Genesis MPM.Elastic 作为同轮审阅的并行候选，PolyFEM 保留为重视体弹性与复杂接触质量的专门路线。** 相较上轮以 PolyFEM 为主的排序，本次把现成流程、具体采用证据、用户对 Newton 的熟悉程度和跨效果覆盖纳入更高权重；物理可信度仍由所选求解器和场景的证据支撑。此处形成研究建议，最终路线与首组参数场景由用户审阅后选择。

| 候选及位置 | 当前能具体承担什么 | 选择依据与主要准备 |
| --- | --- | --- |
| **Newton：工程主候选** | VBD 体弹性完整鸭子；布料、杆、刚软互动；另有隐式 MPM 的颗粒与黏性材料 | Python 场景直接；已有可定位的外部工程使用。VBD 仍标 experimental；鸭子参数视频及高质量软体外观输出需要在现成代码上制作。其默认例为实心弹性体，原参数属于示例设置。[项目](https://github.com/newton-physics/newton)、[VBD](https://newton-physics.github.io/newton/stable/solvers/vbd.html) |
| **Genesis MPM：并行候选** | 被动完整弹性龙、刚体工具互动；直接 E／ν／ρ，现有外观网格路径；另有 FEM＋SAP 抓球 | 本次补到被动完整物体及动画／脚本配对，弥补此前只看到主动 worm 的缺口。切割龙脚本使用粒子纯色显示；保持完整的参数场景与外观输出需改动作和显示方式。[脚本](https://github.com/Genesis-Embodied-AI/genesis-world/blob/v1.4.5/examples/coupling/cut_dragon.py)、[公开资产](https://github.com/Genesis-Embodied-AI/genesis-world/tree/v1.4.5/genesis/assets/meshes/dragon) |
| **PolyFEM：数值与接触条件优先时选用** | 非线性体弹性、多材料、完整 Armadillo 滚轮挤压、部件接触 | 高阶离散对照和材料／接触机制便于解释；标准路径的编译、旧配置适配与后渲染成本较高。它的研究专业性有依据，行业工程采用与即用材质资产的证据较少。[作者对照](https://zferg.us/research/high-order-ipc/)、[项目](https://polyfem.github.io/) |
| **SOFA：新增完整软结构候选** | 多区域、气腔、驱动、软指与硬物体接触；Blender 动画导入导出 | 有具体医疗仿真产品采用，公开 PneuNet 是完整结构入口；Dice Salad 有 .blend／贴图，但其物理体采用立方格近似，宜先看制作链。[实际采用](https://www.insimo.com/32077/crossed-interview-sofa-2/)、[完整夹爪](https://github.com/SofaDefrost/SoftRobots/blob/master/examples/tutorials/PneunetGripper/details/step7-grabTheCube.py) |
| **MuJoCo／RoboCasa：完整硬物件与ρ补充** | 厨房、家具、器具、关节动作；同外力／冲量下的质量与惯性展示 | 公开贴图资产、动作回放和视频输出，日常物件丰富。该资产路线以刚体为主；E／ν 要另选合适的变形实现。[完整资产与录制](https://github.com/robocasa/robocasa) |
| **Chrono：机械、梁壳与轮胎条件候选** | FEA 轮胎、梁／绳／壳与机构；颗粒和流固互动 | 有工程验证与 QBlade 等具体集成。完整轮胎体网格和代码公开，但可看历史视频与当前复跑配置的对应、PBR 成片配套尚不齐。[实际集成](https://qblade.org/)、[验证](https://www.projectchrono.org/validation/)、[轮胎代码](https://github.com/projectchrono/chrono/blob/main/src/demos/fea/demo_FEA_abaqus_wheel.cpp) |

扩搜还覆盖 FEBio、MOOSE、Gazebo、Bullet／PyBullet、Blender：FEBio 按复杂材料需要保留；MOOSE 偏工程多物理应用开发；Gazebo 偏机器人系统组织；Bullet 有可用软体但本轮未补到更强的完整案例／量值依据组合；Blender 主要承担材质和后渲染。商业 Ansys／Abaqus／COMSOL／Houdini 只作为产品类别参照。各项筛选与依据见[扩搜证据](13-established-alternatives-evidence.md#1-广搜后的筛选)。

组织背景与具体采用分别记录：Newton 除发起机构外，查到了 Skild 的刚体装配工作流、Lightwheel 的线缆耦合展示，Samsung 为公开采用计划；Genesis 的新增依据包括外部开源集成与实测对照研究，当前所查工业采用证据较少；PolyFEM 的明确采用以科研项目为主。SOFA／Chrono 的企业应用分别对应医疗和工程结构。这些事实用于说明工程积累，适用范围仍落在各自的物理实现。[Newton 工程案例](https://developer.nvidia.com/blog/newton-adds-contact-rich-manipulation-and-locomotion-capabilities-for-industrial-robotics/)、[Newton／Genesis 采用明细](13-primary-framework-evidence.md#2-工程与采用组织维护实际使用分列)、[其他产品明细](13-established-alternatives-evidence.md)

## 先看这些案例

下列链接对应作者已有视频／循环动画，用户可直接观看判断运动与外观。它们大多是功能、方法或制作效果证据；本项目的 E／ν／ρ 参数对比仍待选定后复跑。每个案例的代码和资产状态在右列说明。

| 观看入口 | 看什么、实际方法 | 现成配套与本任务关系 |
| --- | --- | --- |
| **[Genesis 完整龙下落切割：循环动画](https://raw.githubusercontent.com/Genesis-Embodied-AI/genesis-world/readme-assets/videos/coupling_cut_dragon.webp)** | 被动 MPM.Elastic＋CPIC；完整外形、形变与工具互动 | 动画与脚本一一对应；OBJ／MTL／PNG 公开，当前片用纯色粒子。适合作普通弹性完整物体的候选入口，保形参数场景需改动作。 |
| **[Genesis FEM 抓球：循环动画](https://raw.githubusercontent.com/Genesis-Embodied-AI/genesis-world/readme-assets/videos/sap_franka_grasp_fem_sphere.webp)** | 隐式共旋 FEM＋SAP；夹持、提起、形变 | 球、机器人、材料和动作脚本公开。是受控形变补充，E／ν 对比待复跑。 |
| **[Newton DAT：作者视频](https://www.youtube.com/watch?v=gdUB5NDl_E8)**／[作者页](https://ankachan.github.io/Projects/DAT/) | 看体积软体、布带、杆和刚体接触；论文中 Gift Box Drop 对应软块＋交叉布带组合物体 | DAT 已集成 Newton VBD，`softbody_gift` 代码公开；电影版本与当前脚本、PBR 包逐项对应仍待核。 |
| **[Newton 冰箱线缆插接：MP4](https://developer.download.nvidia.com/video/devblog/waterhose.mp4)** | MuJoCo Warp＋VBD cable 双向耦合，完整工业物体互动 | 支撑外部工程使用；影片的整套资产／渲染包未核公开。线缆效果与体积 E／ν 证据分开。 |
| **[PolyFEM 高阶接触：作者视频](https://www.youtube.com/watch?v=YpXX6lPs0gM)**／[Armadillo-rollers 对照页](https://zferg.us/research/high-order-ipc/) | 完整软体经滚轮挤压；看高阶位移和碰撞表示对形变的影响 | 论文配置、体网格、碰撞网格与映射数据公开；旧版本配置及成片材质需衔接。现有片比较离散方法。 |
| **[SOFA Dice Salad：MP4](https://github.com/user-attachments/assets/3aa80034-c865-4510-b93b-979e02fead4d)** | 带材质的软／硬骰子、碗与接触；SOFA＋Blender | 场景、网格、贴图、可渲染 .blend 和输出工具公开。软骰子由立方体物理格驱动外观，先作制作链参考；直接材料量值解释需相符物理几何。 |
| **[MuJoCo／RoboCasa 抓取放置：MP4](https://robocasa.ai/assets/videos/basic_skills/pnp.mp4)**／[家具开门](https://robocasa.ai/assets/videos/basic_skills/door.mp4) | 带外观的日常完整物件与家具；刚体和关节 | 公开资产、贴图、回放与录视频脚本。适合ρ与硬物件范围；现成机器人闭环动作需换成显露质量差异的受力条件。 |
| **[Chrono 轮胎与颗粒：作者视频](https://www.youtube.com/watch?v=P-1VZoBnD_c)** | 看轮胎／颗粒互动与工程场景广度 | 官方 Gallery 已定位视频，具体历史模块／配置未披露。当前另有开放 FEA 轮胎代码；两者暂按效果与复跑入口分别记录。 |

更多入口及逐项来源见[Newton／Genesis 视频与资产](13-primary-framework-evidence.md#5-可直接观看的案例与公开资产)、[其他产品视频与资产](13-established-alternatives-evidence.md#7-直接观看与公开资产对应)。Newton 鸭子已有明确代码／体网格入口，本轮尚未定位与该脚本确切对应的直接动画，故未把静态缩略图放入观看清单。YouTube 影片通过作者页面定位，播放依赖平台可达性；Genesis 动画确认具有动画标记，部分 MP4 已核可访问，完整影片均留给用户审阅。

## 框架支持的方法与效果

表中是已定位的实现或案例；“按插件／模块”表示还要选定对应依赖。密度的可见影响横跨刚体、软体、流体等模型，具体看加载与作用条件。植物由材料、杆／叶片、连接和支撑共同组成，具备杆能力只证明相应结构建模基础。

| 框架 | 体弹性／刚软接触 | 布料／薄壳／杆 | 颗粒／塑性 | 水／黏性流体 | 已有耦合与主要条件 |
| --- | --- | --- | --- | --- | --- |
| **Newton** | VBD 体 FEM；多刚体求解器；完整鸭子与组合物体 | VBD 膜／弯曲、Cosserat 杆；外接 Style3D 分列 | ImplicitMPM：颗粒、雪、弹塑性 | ImplicitMPM 黏性／水示例 | VBD 内部刚／软／布／杆；另有 MuJoCo–VBD／MPM 等耦合示例，部分路径 experimental |
| **Genesis** | MPM.Elastic、FEM、PBD；各自材料和输出不同 | PBD 布／绳；外接 libuipc 壳；主动软体／hybrid | MPM Sand／Snow／ElastoPlastic | MPM／SPH／PBD Liquid | Legacy 有多种配对；SAP 为 Rigid＋隐式 FEM；IPC 由 libuipc 提供。按组合与模型选择 |
| **PolyFEM** | 体 FEM＋IPC、多材料、障碍物接触 | 细长体可用体网格；专门杆／完整植物案例待核，教程列受模拟壳体限制 | 多弹性体接触；颗粒材料场景待核 | 实现 Stokes／Navier–Stokes；蜂蜜完整例待核 | 本次实证集中于固体接触；流体求解器与软体双向 FSI 另待对应证据 |
| **SOFA** | 多种 FEM、刚体、映射、约束与驱动 | 膜／壳组件；BeamAdapter／Cosserat 等插件 | 本轮未核成熟高密度颗粒配置 | 本轮未核蜂蜜等现成路径 | 气腔压力、刚软抓取、刚柔映射明确；通用软体入液配套待核 |
| **Chrono** | FEA 实体、刚体多体、接触；柔性轮胎 | ANCF／Reissner 壳，多类梁／绳 | DEM、CRM 颗粒 | FSI::SPH 黏性流体 | 有物体入液、柔性 cable／plate／tire FSI 例；各模块支持范围分别选定 |
| **MuJoCo** | 刚体／关节、flex；全自由度与降阶分开 | flex 薄片、绳／杆及插件 | 刚体颗粒可表达；工程规模配置另看 | 原生环境流体力模型；自由表面求解另需路线 | 刚软接触明确；RoboCasa 主要提供刚体日常场景 |
| **Blender** | Bullet 刚体、传统 soft body；可接外部物理结果 | cloth／hair；XPBD 布／杆另有开发文档 | 视觉粒子／多刚体 | Mantaflow 液体与黏度 | 材质与成片主力；物理量映射及多类物体双向耦合逐例判断 |

实现来源与更细的“原生／外接／可用例”对照见[Newton／Genesis 效果矩阵](13-primary-framework-evidence.md#3-框架--效果--实际实现)、[其他框架效果矩阵](13-established-alternatives-evidence.md#6-框架--效果--已有实现)。本次只盘点其他效果的覆盖，保持固体主线和既有暂缓分支安排。

## 本次采用的取舍

最终用途继续是给定几何及 E／ν／ρ，生成能显露参数差异、有依据解释其合理性的运动视频。用户确认：在参数语义明确、目标场景有质量依据的前提下，优先工程成熟、完整案例丰富、使用方便；高阶数值能力按场景需要提高权重。近期允许修改现成场景、参数、动作和输出，新增材料模型／求解／耦合算法列后续研发。商业产品只作行业参照，PhysX 按用户偏好排除。

“多”落实到公开完整物体及材料／结构／作用范围；“真”分别看参数对应的物理关系、运动质量依据和外观；“快上手”同时看环境、脚本、资产及出片准备。框架支持多个求解器，与它们能共同参与同一场景，是两项需要分别核实的能力。

## 参数响应怎样才有说服力

研究按下列问题解释每个案例。这是阅读现有证据的口径，具体运行工作留给选定后的复跑。

| 要回答的问题 | 可以支撑的判断 | 需要保留的具体条件 |
| --- | --- | --- |
| E／ν／ρ 进入了什么物理模型？ | 直接输入或有据换算的数值，具有明确单位、质量与材料语义 | 物体尺度、实心／空腔／多材料结构、材料模型及附加参数 |
| 哪种运动能显露这个参数？ | E 可通过同一外载下的形变与自由恢复；ν 可通过横向与体积响应；ρ 可通过固定力／冲量下的惯性体现 | 相同初态、驱动、接触、阻尼与播放时间；完全规定的运动、重力自由落体等场景有各自辨识范围 |
| 响应量值有什么参照？ | 理论关系、解析解、数值比较或实测运动，能帮助解释形变量／时间尺度 | 对照所用材料、结构、版本、离散与收敛条件；结果的外推范围随条件限定 |

上表为基于既有材料与场景证据的任务推论，具体 E／ν／ρ 展示方案及物理依据见[已有参数场景](07-demand-driven-solid-selection.md#可选择复跑的参数场景)和[密度场景依据](05-density-demonstration-candidates.md#什么条件下ρ会产生可辨认的变化)。

PBD 的疑虑有具体技术来源：传统 PBD 的有效刚度受时间步与迭代数影响；XPBD 则将约束与弹性能量／柔度联系起来以处理这一问题。因此应追到具体材料与求解实现，再检查参数解释和数值条件。相应地，FEM／MPM 的名称也只是进一步核查的入口。[XPBD 原论文](https://mmacklin.com/xpbd.pdf)

有两类现成参照值得保留：PolyFEM／FEBio 的系统比较提供了特定材料和接触条件下的数值对照；GAUGE 的仿真器轨道使用实测材料参数与真实运动比较，表明现成默认配置在某些软体场景仍有偏差。前者支持所测数值实现，后者支持所测场景的现实对照。GAUGE 的 Newton 体积软体采用隐式 MPM、Genesis 采用显式 MPM，并有版本标记待核问题，不能据此给当前 VBD／FEM 作统一排名。[PolyFEM／FEBio 原研究](https://pmc.ncbi.nlm.nih.gov/articles/PMC10843651/)、[GAUGE 原论文](https://arxiv.org/html/2608.05948v1)

GAUGE 可直接观看的[真实泡沫压缩](https://internrobotics.github.io/GAUGE/trials/foam-compressing.mp4)和[真实泡沫弯曲](https://internrobotics.github.io/GAUGE/trials/foam-bending.mp4)均已确认返回 `video/mp4`。它们是实验参考片；框架案例的观看入口另列。当前 GAUGE 主仓库公开目录以 README／media 为主，文档规划的脚本与实际可取文件需分别看，故本轮将其作为质量证据和实验参考。[项目仓库](https://github.com/InternRobotics/GAUGE)
