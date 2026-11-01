# 完整物体正向仿真的连续体与接触路线证据

研究日期：2026-11-01。范围：已有几何及 E／ν／ρ，离线正向仿真、完整物体、多材料／多部件、公开资产和 PBR 后渲染。本轮从物理需求重新比较，旧候选兴趣与旧考察排序不作为推荐依据。

本文中的“事实”来自官方文档、作者论文或源代码；“判断”是据此作出的选型推断；“待核实”表示公开证据仍有具体缺口。本次完成了网页与源文件读取、官方仓库资产目录核查；安装、仿真执行和渲染留给选定后的复跑。

## 1. 可比较结论

**判断：在本次比较的连续体路线中，主推 PolyFEM + IPC，定位为完整体积弹性物体、多材料体积结构及复杂接触的离线主线。** 推荐理由是可追溯的超弹性方程、E／ν／ρ 入口、接触可行性控制，以及公开的高阶离散和数值对照证据。离线条件允许用计算时间换取更高的空间／时间精度；这些优势比交互帧率更贴近当前任务。当前官方数据中有完整软体被滚轮挤压、软玩具落入碗、球体撞板和螺栓螺母；完整多部件的迁移证据还包括牙颌和髋关节。[材料方程](https://polyfem.github.io/details/formulations/)、[IPC 教程与数据入口](https://polyfem.github.io/tutorials/ipc_quick_start_guide/ipc_quick_start_guide/)、[高阶 IPC 论文与视频](https://zferg.us/research/high-order-ipc/)。

| 路线 | 这次适合承担的角色 | 形成推荐的物理证据 | 主要条件 |
| --- | --- | --- | --- |
| **PolyFEM + IPC** | 体积弹性与复杂接触主线 | 公开的材料公式；接触／大形变求解；高阶位移与曲面离散；跨求解器验证 | 完整物体要有体积、材料区域和连接关系；后渲染需另接；高阶论文场景须匹配版本 |
| **libuipc** | GPU 上大量接触、软硬物体互动以及 Blender 制作便利优先时的备选 | 统一 FEM／ABD 接触；当前原生 E／ν／ρ 入口；完整 Blender 缓存路径 | 主要体积本构为 Stable Neo-Hookean；ABD 的刚性参数与真实 E／ν 分开；公开完整软体资产目前偏基础 |
| **SOFA + 对应插件** | 有腔体、驱动、刚软耦合、连接／映射关系的完整机构 | 公开气动夹爪含软指、加硬层、气腔、刚体抓取；可换超弹性材料 | 要选定具体力场、质量与接触算法；教学默认场景的本构和阻尼需按真实用途解释 |
| **FEBio** | 组织、纤维增强、粘弹性、损伤等材料行为成为决定因素时的备选 | 成熟的非线性材料体系、共享完整模型与接触示例 | 高级本构所需参数超出 E／ν／ρ；极端动态接触需要更多模型调节 |

上述角色分别依据 [libuipc 原生定位](https://github.com/spiriMirror/libuipc)、[SOFA SoftRobots](https://github.com/SofaDefrost/SoftRobots)、[FEBio solid 模块](https://febiosoftware.github.io/febio-docs/features/modules/module_solid/) 和下文具体资产，属于任务适配判断。

**判断：四条路线都只在选定材料与结构假设内提供可信预测。** E、ν、ρ 能确定各向同性小应变弹性与惯性；大形变时还必须选本构。相同 E／ν 对应的不同 Neo-Hookean 变体在大应变下会产生不同响应。水果的皮／果肉、枕头的外套／填料、家具的接头／局部弯曲、塑料瓶的壁厚／屈曲，分别需要相应区域、内部结构、连接或薄壁模型。把它们统一填成一个实心超弹性体，会改变所模拟的物件。[PolyFEM 方程](https://polyfem.github.io/details/formulations/)、[libuipc SNK 方程](https://spirimirror.github.io/libuipc-doc/specification/constitutions/stable_neo_hookean/)、[FEBio 材料范围](https://febiosoftware.github.io/febio-docs/features/modules/module_solid/)。

## 2. 求解质量：推荐依据和证据边界

### PolyFEM：质量优势来自可选择的离散与接触求解

- **事实：** PolyFEM 使用 IPC Toolkit 处理接触；官方材料页列出线弹性、Saint Venant–Kirchhoff、Neo-Hookean、Mooney–Rivlin、Ogden 和粘性阻尼。Neo-Hookean 可直接输入 E／ν／ρ，高级模型有额外系数。[接触](https://polyfem.github.io/details/contact/)、[材料](https://polyfem.github.io/details/formulations/)、[JSON](https://polyfem.github.io/json/)。
- **事实：** 高阶 IPC 工作把高阶体积位移与线性碰撞网格相映射。公开梁弯曲、垫体扭转及 Armadillo 滚轮例子比较了离散阶数／网格：粗线性网格可表现出额外刚硬与折叠伪影，高阶解能更接近细网格参考。这个证据支持“在相同物理模型下可减少离散误差”，其外推范围仍受几何、模型和离散选择约束。[作者结果与视频](https://zferg.us/research/high-order-ipc/)、[论文数据](https://github.com/zfergus/high-order-ipc-data)。
- **事实：** 2024 年系统对比使用 PolyFEM 1.0 和 FEBio 3.7：覆盖静态、动态、接触、超弹性验证与髋／牙颌；结果总体接近，部分大形变／高能接触例子由 PolyFEM 完成而 FEBio 失败，很多成功例子中 FEBio 更快。作者明确主要目标是数学实现验证，具体物件的现实验证仍由建模者完成。**判断：** 可把该研究作为 PolyFEM 的可信度依据；对 2026 年 FEBio 4.13 的相对性能应保留为待核实。[作者论文](https://cims.nyu.edu/gcl/papers/2023-polyfem-systematic.pdf)。
- **事实：** 官方教程分别暴露非线性收敛容差、接触激活距离 `dhat`、切向摩擦阈值 `epsv` 和摩擦迭代；也说明大时间步隐式 Euler 适合接近准静态的结果。**判断：** 较大步长下顺利完成主要证明鲁棒性；反弹、振动与撞击持续时间还受时间离散与数值耗散影响。[精度控制教程](https://polyfem.github.io/tutorials/ipc_quick_start_guide/ipc_quick_start_guide/)。

**与 libuipc 的实质区别：** 高阶体积与碰撞映射提供了可公开核查的精度改善手段；libuipc 目前核查到的体积路线是四面体 FEM + SNK，优势集中在 GPU 求解与统一接触。本文没有找到二者在相同模型、分辨率、时间步及收敛要求下的现成精度对照，因此推荐依据是任务需要的能力和已有验证层级；具体速度／误差排名留待同场景证据。[libuipc 体积 FEM](https://spirimirror.github.io/libuipc-doc/tutorial/fem/)、[PolyFEM 高阶证据](https://zferg.us/research/high-order-ipc/)。

### libuipc：接触和制作路径强，物理模型应明确选型

- **事实：** 当前库将体积 FEM、ABD、布和线统一在 IPC 框架，CUDA 执行碰撞、接触装配与线性代数。官方说明将非穿透保障限定在有效初始几何、成功碰撞检测与求解条件下。[README](https://github.com/spiriMirror/libuipc)。
- **事实：** SNK 入口是 `StableNeoHookean.apply_to(..., ElasticModuli.youngs_poisson(E, nu), mass_density=...)`；材料系数存于四面体，质量由静止体积和密度得到。2026-10-19 的系数映射修复使小应变响应对应输入 E／ν。**版本条件：** 复跑使用包含修复的运行库；旧包与当前文档应分开核实。[材料规范](https://spirimirror.github.io/libuipc-doc/specification/constitutions/stable_neo_hookean/)。
- **事实：** ABD 限制每个物体使用仿射运动，以少量自由度处理硬物体；其刚性惩罚控制仿射形变。**判断：** 适合家具／工具中可视作刚体的部件；局部弯曲、应力分布、真实泊松效应应由体积或薄壁模型承担。给整个物件一个 ABD 参数不足以展示该物件的 E／ν 差异。[ABD 规范](https://spirimirror.github.io/libuipc-doc/specification/constitutions/affine_body/)。
- **事实：** 官方 `94_robot_hand_grasp_apple` 使用自由 ABD 苹果、脚本驱动手指、接触与摩擦；苹果由近似球面生成。**判断：** 这是完整机器人／物件抓取证据；苹果皮／果肉大形变的真实性需要另外的材料与结构模型。[样例说明](https://github.com/spiriMirror/libuipc-samples/blob/main/examples/94_robot_hand_grasp_apple/README.md)。

### SOFA：完整机构可表达，具体场景算法决定质量

- **事实：** SOFA 的 `TetrahedronFEMForceField` 用 E／ν，`large` 是允许大转动的共旋线弹性；独立 `TetrahedronHyperelasticityFEMForceField` 支持 Neo-Hookean、Mooney–Rivlin、Ogden 等。Neo-Hookean 参数为 `[μ, λ]`，应按该材料的公式从 E／ν 换算。密度可通过 `MeshMatrixMass.massDensity` 输入。[共旋教程](https://github.com/sofa-framework/sofa/blob/master/examples/Tutorials/ForceFields/TutorialTetrahedronFEMForceField.html)、[超弹性参数](https://sofa-framework.github.io/doc/components/solidmechanics/fem/hyperelastic/tetrahedronhyperelasticityfemforcefield/)、[质量组件](https://sofa-framework.github.io/doc/components/mass/meshmatrixmass/)。
- **事实：** 完整 PneuNet 例子当前采用共旋 FEM、`FrictionContactConstraint`、约束迭代、气腔压力和规定总质量；加硬层通过选取局部四面体叠加刚度表示。**判断：** 它实质覆盖“中空、多区域、驱动、软硬接触”，比单个兔子更有迁移意义；真实弹性体的大应变材料曲线、耗散与驱动响应仍需匹配。[完整场景源码](https://github.com/SofaDefrost/SoftRobots/blob/master/examples/tutorials/PneunetGripper/details/step7-grabTheCube.py)。
- **事实：** 标准 `liver.scn` 使用共旋 FEM、Rayleigh 阻尼和球代理碰撞；显示表面另经重心映射。**判断：** 该场景适合验证多表示协作与器官交互，视觉表面精细度和真实接触几何精度要分开评价。[liver.scn](https://github.com/sofa-framework/sofa/blob/master/examples/Demos/liver.scn)。
- **待核实：** 本轮确认了公开完整资产及材料入口；当前这些具体 SOFA 场景与实测三维形变／接触力的误差表尚未核实。官网图库包含公开及私有插件场景，图库展示不能整体算作已公开可复跑资产。[图库的范围说明](https://www.sofa-framework.org/applications/gallery/)。

### FEBio：复杂材料与完整组织数据突出

- **事实：** `neo-Hookean` 提供 `E`、`v`、`density`，同时明确标准位移单元接近不可压缩时存在锁死风险，建议相应采用 Mooney–Rivlin 路径。FEBio 提供近不可压缩材料、不同实体单元、滑动／粘接接触和刚柔连接，材料库还覆盖纤维、粘弹性、损伤与塑性。[Neo-Hookean](https://febiosoftware.github.io/febio-docs/features/features/solid_material_neo-hookean/)、[实体单元](https://febiosoftware.github.io/febio-docs/theory/chapter4/4.1-solid-elements/)、[近不可压缩材料](https://febiosoftware.github.io/febio-docs/theory/chapter5/5.3-nearly-incompressible-materials/)。
- **判断：** 当行为依赖纤维方向、松弛时间、孔隙流动或屈服等因素，FEBio 比只用 SNK 的路线更接近所需建模；这些物理量需要来自选定公开模型或额外输入，E／ν／ρ 无法唯一给出它们。[solid 模块](https://febiosoftware.github.io/febio-docs/features/modules/module_solid/)。
- **事实：** 官方 pyfebio 接触教程直接展示粗网格尖角处的接触穿透，并解释接触选择对结果的影响。**判断：** 公开模型中较温和、已调好的接触适合复用；把它改为高速、多物体、大形变互动时，需要重新检查接触设置及网格。[pyfebio 接触示例](https://febiosoftware.github.io/pyfebio/examples.html)。

## 3. 现成完整物体及数据获取

以下列出**已核查到实际源文件／官方数据目录**的资产。网格存在、场景可读、作者完成过运行属于不同层级；本次未执行这些场景。机械网格普遍只带几何／分区，公开资料未建立成套日常物件 PBR 材质库。

| 资产与作用 | 已有内容及数据获取 | 对本任务的证据与边界 |
| --- | --- | --- |
| **PolyFEM Armadillo：完整软体穿过六个滚轮** | [当前场景 JSON](https://github.com/polyfem/polyfem-data/blob/main/contact/examples/3D/friction/armadillo-roller.json)；[体网格目录](https://github.com/polyfem/polyfem-data/tree/main/contact/meshes/3D/creatures/armadillo)，文件 `armadillo_verschoor.msh`；滚轮 OBJ 由 JSON 直接引用。官方树中该 MSH 大小 14,681,495 字节。 | 已有完整躯干／四肢、自接触和机械挤压；当前文件 E=100,000 Pa、ν=0.2、ρ=1000 kg/m³、dt=0.025 s、总时长 10 s。物体是合成软玩具式材料，现实生物体材料验证应另论。 |
| **PolyFEM Octocat 落入碗** | [JSON](https://github.com/polyfem/polyfem-data/blob/main/contact/examples/3D/pile/octocat-bowl.json)、[`octocat.msh`](https://github.com/polyfem/polyfem-data/blob/main/contact/meshes/3D/creatures/octocat.msh)、[`bowl.obj`](https://github.com/polyfem/polyfem-data/blob/main/contact/meshes/3D/obstacles/bowl.obj)。官方树确认网格大小分别 904,637／78,115 字节。 | 3 个完整形状的软体与刚性碗互动；E=100,000、ν=0.4、ρ=1000，dt=0.07 s。可用来观看软体整体互动；快速反弹和振动的精度要另选时间离散。 |
| **PolyFEM 球体撞板** | [golf-ball.json](https://github.com/polyfem/polyfem-data/blob/main/contact/examples/3D/golf-ball.json)、[`sphere19K.msh`](https://github.com/polyfem/polyfem-data/blob/main/contact/meshes/3D/simple/sphere/sphere19K.msh)。当前文件 E=10⁷ Pa、ν=0.45、ρ=1150，初速 67 m/s、dt=2×10⁻⁵ s。 | 原 IPC 论文用泡沫练习球高速摄影作现实对照；PolyFEM 数据继承该几何／参数设置，当前积分器和原实验设置一致性仍需核查。适合变形质量参照；受控压缩是可复用同一球网格的新动作。[原 IPC 对照](https://ipc-sim.github.io/)。 |
| **PolyFEM 高阶 Armadillo／球** | [高阶数据仓库](https://github.com/zfergus/high-order-ipc-data)，包含曲面 MSH、碰撞 OBJ、映射 HDF5；[P2 Armadillo 配置](https://github.com/zfergus/high-order-ipc-data/blob/main/scripts/friction/armadillo-roller-P2.json)。当前主数据另有 [P2 弹跳球配置](https://github.com/polyfem/polyfem-data/blob/main/contact/examples/3D/higher-order/ball-bounce/P2.json)。 | 这组资产直接支撑提高离散质量；旧 P2 Armadillo 配置仍用旧字段，复跑需论文版本或配置迁移。当前弹跳球配置明确使用 `space/discr_order=2`，可作为当前接口参考。 |
| **PolyFEM 螺栓／螺母** | [官方教程](https://polyfem.github.io/tutorials/screw/screw/)链接[完整场景／网格仓库](https://github.com/DuoZhangRobotics/Screw_Polyfem_Tutorial)，包含规定旋转、轴向自由、顶端驱动等动作。 | 证明凹槽与多部件接触能力；真实金属很硬时的可见运动主要来自接触／约束，E／ν 的直观差异应另用有形变的物体。 |
| **PolyFEM：LibHip 与 Open-Full-Jaw** | [LibHip](https://github.com/diku-dk/libhip)提供 11 位受试者的双侧髋区域、骨／软骨、多体表面／体网格和 FE 文件；[Open-Full-Jaw](https://github.com/diku-dk/Open-Full-Jaw)提供 17 个牙颌模型、牙周膜、分区体网格及咬合／牙倾斜场景。按各仓库说明通过 Git LFS 获取真实网格。 | 证明体积分区、相邻界面、刚度差异和多部件建模可落地。临床几何核查与具体材料／动态响应验证须分开；这组数据主要承担结构覆盖证据。 |
| **SOFA：PneuNet 完整三指夹爪** | 运行资产入口为 [`details/step7-grabTheCube.py`](https://github.com/SofaDefrost/SoftRobots/blob/master/examples/tutorials/PneunetGripper/details/step7-grabTheCube.py)；[mesh 目录](https://github.com/SofaDefrost/SoftRobots/tree/master/examples/tutorials/PneunetGripper/details/data/mesh)含 `pneunetCutCoarse.vtk`、`pneunetCut.vtk`、外表面与气腔 STL、方块及地面。 | 是公开的完整多区域／中空／驱动模型。`pneunetgripper-tuto.py` 当前只是练习空模板，应直接使用 details 完整场景。现有质量入口是 `totalMass`，改用 ρ 需依据体积或质量组件；场景单位需统一。 |
| **SOFA：肝脏** | [`liver.scn`](https://github.com/sofa-framework/sofa/blob/master/examples/Demos/liver.scn)直接引用 `liver.msh`、`liver-smooth.obj` 和 `liver.sph`。 | 完整器官外形有现成模拟、显示、碰撞三种表示；标准例子按单材料及球代理碰撞建模。 |
| **FEBio：纤维增强膝关节** | [模型页](https://repo.febio.org/permalink/project/118)，`Knee_TId.feb`、`Knee_FR.feb`；[直接 ZIP](https://repo.febio.org/modelRepo/api/v1.05/files/0/118)。本次点击确认服务器返回 ZIP 类型；包内容与本地运行仍待选定后核查。 | 多组织、纤维／双相材料、步态支撑期，原模型使用 FEBio 2.9.1。对真实复杂材料覆盖有意义；输入参数多于 E／ν／ρ。 |
| **libuipc：球、兔、机械零件与机器人手** | [官方样例资产](https://github.com/spiriMirror/libuipc-samples/tree/main/assets/sim_data)：`tetmesh/ball.msh`、`bunny0.msh`、`bunny2.msh`、`wheel_axle.msh`、`link.msh`，以及齿轮／螺母螺栓 OBJ、机器人手 URDF；[机器人抓苹果场景](https://github.com/spiriMirror/libuipc-samples/tree/main/examples/94_robot_hand_grasp_apple)。 | 可以支撑软体与硬部件互动及场景构造。现有完整可变形物体集中在球／兔及合成网格；本轮资产核查尚未形成日常物件多材料体网格目录。 |

**资产核查方法：** 上表 PolyFEM、libuipc、SOFA 的文件名和大小来自其官方 GitHub tree API 及原始配置读取，例如 [PolyFEM 数据树](https://api.github.com/repos/polyfem/polyfem-data/git/trees/main?recursive=1)、[SoftRobots 数据树](https://api.github.com/repos/SofaDefrost/SoftRobots/git/trees/master?recursive=1)。部分 GitHub 网页目录经浏览工具访问时缓存缺失，本次以官方 API 补齐。单个 `.msh` 下载后仍需保留场景所用相对目录，避免只拿到外形却漏掉障碍物／分区／驱动文件。

**版本策略：** 当前 `polyfem-data` 常规 JSON 与 PolyFEM 在 2026-10-30 合并 `no-legacy` 后的主分支兼容性尚未实跑；选定场景后应固定相互匹配的求解器和数据版本。高阶论文的旧 JSON 则需要匹配论文版本或迁移字段，本文把它列为已公开的复跑材料。[本轮主分支提交](https://github.com/polyfem/polyfem/commit/ddadb5398f0fae2e825dedcf754aa7dd00bc3bb0)、[旧 P2 配置](https://github.com/zfergus/high-order-ipc-data/blob/main/scripts/friction/armadillo-roller-P2.json)。

## 4. 从完整资产迁移到目标物体，需要什么模型条件

| 目标结构／行为 | 连续体主线的适配判断 | 具体建模条件 |
| --- | --- | --- |
| 实心橡胶玩具、近似均匀软块 | PolyFEM／libuipc 都匹配；PolyFEM 更便于选高阶与材料变体 | 闭合体积、合理尺度、体网格、本构、外力／边界和接触参数；外形复杂度本身不要求更换方法 |
| 多材料实心体，如软外层包硬芯 | PolyFEM 的分区体网格、FEBio 的多材料域、SOFA 的分区力场均可承接 | 每区材料、空间位置及界面；接合界面须共享自由度或有明确连接，单纯放入一个场景不会自动粘合 |
| 多部件家具、工具、机械玩具 | 硬部件运动可交给 libuipc ABD／SOFA 刚体；局部弹性另用体积或薄壁 | 接头自由度、胶合／固定／滑动、软硬耦合；对象外部形状无法确定这些关系 |
| 枕头／软垫／水果／肉 | 按内部结构分别选择；当前 E／ν／ρ 可先确定一个明确的等效弹性假设 | 外套或皮层、填充或果肉区域、可能的孔隙和耗散；大压缩／松弛／永久变形需要相应本构 |
| 塑料瓶、帽子、薄壁家具部件 | 只记录覆盖：libuipc／SOFA／FEBio 有薄壁相关路径；PolyFEM 官方 IPC 教程把受模拟薄壳列为缺口 | 壁厚、中面／壳离散、屈曲及可能塑性；此轮不展开薄壁独立选型 |

该表是由已核查能力作出的迁移判断：[PolyFEM 多材料](https://polyfem.github.io/tutorials/multi-material/multi-material/)、[SOFA 夹爪结构](https://github.com/SofaDefrost/SoftRobots/blob/master/examples/tutorials/PneunetGripper/details/step7-grabTheCube.py)、[libuipc 多几何／材料概念](https://spirimirror.github.io/libuipc-doc/tutorial/concepts/)、[FEBio solid](https://febiosoftware.github.io/febio-docs/features/modules/module_solid/)、[PolyFEM 薄壳边界](https://polyfem.github.io/tutorials/ipc_quick_start_guide/ipc_quick_start_guide/)。

**判断：** 完整体积 FEM 具有较广的几何迁移范围；“覆盖多数完整物体”的最终约束通常落在结构和本构，而非能否读取更多 OBJ。上述现成实例足以支持这条路线进入复跑选择；它们对家具、食物、填充物的真实模型仍留有明确覆盖缺口。

## 5. E／ν／ρ 展示建议：资产复用与新动作分开

以下全为**代码支持调参、具体效果待复跑**。数值是建议对比组，物体现实参考值仍沿用选定公开模型；新动作明确标注。

| 参数 | 资产／动作 | 建议改变与固定项 | 预期观察与适用边界 |
| --- | --- | --- | --- |
| E | **已有** Armadillo 滚轮；或**新动作**用同一体网格作低高度落地／固定受力 | 原 E=100 kPa，可比较 10／100／1000 kPa，固定 ν=.2、ρ=1000、几何、接触、时间离散、外部运动／外力 | 下垂、局部挤压、回弹和接触持续时间。滚轮规定运动会强制一部分形状，单独自由受力更适合看软硬差异 |
| ν | **复用** sphere19K 体网格，**新动作**两平板受控压缩 | 固定 E／ρ、压缩位移及速度，比较 ν=.2／.35／.45；接近 .5 的一组只有在离散与收敛可支持时加入 | 侧向鼓胀和体积变化；由完整外形与剖面辅助观察，低阶网格锁死可能掩盖材料差异 |
| ρ | **复用**完整 Armadillo 或球网格，**新动作**固定表面牵引短脉冲；或相同预变形释放 | ρ=0.25／1／4 倍，固定 E／ν、总力时程或总冲量、施力区域、阻尼与时间离散 | 加速、位移、振动周期；同样预变形下，频率随惯性变化。实际幅度受阻尼及几何影响 |

参数基础直接来自 [Armadillo JSON](https://github.com/polyfem/polyfem-data/blob/main/contact/examples/3D/friction/armadillo-roller.json)、[球体 JSON](https://github.com/polyfem/polyfem-data/blob/main/contact/examples/3D/golf-ball.json)。上述趋势是弹性与惯性方程下的推断，具体可见程度仍待复跑。

**密度对比已核查到一个关键实现语义：** 当前 PolyFEM `RhsAssembler::assemble` 将体积 `rhs` 乘密度再积分，因此固体的重力 `rhs=[0,g,0]` 是每单位质量的体力／加速度输入。只固定这个字段会让总外力随 ρ 变化。Neumann 边界装配独立按面力积分，未乘 ρ；可在相同网格、同一表面区域使用相同世界向量牵引时程，并记录积分后的总力／冲量。自由落体到首次接触前加速度相同，适合的密度展示是受力响应、振动或和其他物体的动量交换。[当前装配源码](https://github.com/polyfem/polyfem/blob/main/src/polyfem/assembler/RhsAssembler.cpp)。

对比中固定网格、积分器、时间步、接触距离、摩擦和阻尼；模型默认值作为起点，在明显限制可见运动质量时再修改。原公开 JSON 中的 `tests.err_*` 是回归测试字段，解释为对现实物件的实验误差需要另有对应证据。

## 6. PBR 离线渲染与已有数字资产

| 路线 | 已核查输出／映射 | 对 PBR 制作的判断 |
| --- | --- | --- |
| PolyFEM | PVD／VTU 时间序列，保存位移、速度、材料／体 ID 等；高阶 IPC 可另用碰撞代理与线性映射。[JSON 输出](https://polyfem.github.io/json/)、[高阶数据](https://github.com/zfergus/high-order-ipc-data) | 提取变形表面，保留或重新关联顶点／UV 后送 Blender；若外观网格比仿真网格精细，需固定的空间映射。已经有导出数据链，PBR 材质保留和动画导入仍是实现工作 |
| libuipc | 官方 Blender 扩展使用 MDD／原生缓存播放；体积网格准备可保留原表面 UV、顶点和面连接；直接在 Blender 烘焙再渲染。[扩展文档](https://spirimirror.github.io/libuipc-doc/build_install/blender/) | 四条路线中公开制作链最直接；当前原生体网格生成依赖比 v0.0.28 更新的源码运行库，所述 Blender 实测平台主要是 Windows 4.5.3 LTS。Linux／当前目标机器仍需选定后确认 |
| SOFA | 显示表面可用 BarycentricMapping 随体网格变形；`VisualModelOBJExporter` 按步输出 OBJ 序列。[肝脏映射](https://github.com/sofa-framework/sofa/blob/master/examples/Demos/liver.scn)、[导出器](https://sofa-framework.github.io/doc/components/io/mesh/visualmodelobjexporter/) | 能将视觉网格交给 Blender；现成 OglModel 演示与最终 PBR 质量分别评估，UV／材质链需要逐资产保留 |
| FEBio | 结果通常交给 Studio；当前 `FEVTKExport` 有逐状态单独导出选项。[官方 VTK 导出源码](https://github.com/febiosoftware/FEBioStudio/blob/develop/PostLib/FEVTKExport.cpp) | 可通过 VTK 位移与表面提取进入 Blender；本轮未核实官方一键 PBR／Blender 管线 |

**判断：** PBR 能保留数字资产的颜色、粗糙度、法线等外观，但高分辨率显示网格不会自动增加求解器里的内部结构、接触细节或形变自由度。采用已有数字资产时，优先保留其外观表面，再明确它怎样跟随物理表示；现成高质量外观与现成可信物理模型是两项独立资产条件。[SOFA 多表示实例](https://github.com/sofa-framework/sofa/blob/master/examples/Demos/liver.scn)、[高阶碰撞代理方法](https://zferg.us/research/high-order-ipc/)。

## 7. 维护、使用与执行条件

- **PolyFEM：事实。** MIT 许可，官方支持 Windows／macOS／Linux 的 C++ 源码构建和命令行 JSON 使用。官方主分支查询到 2026-10-30 的提交；最新稳定发行版本本轮未核实。实际持续使用证据包括 2024 DiffIPC，将正向求解集成到参数／形状优化并发布场景；本任务可只调用其正向模拟。[仓库](https://github.com/polyfem/polyfem)、[本轮查询到的提交](https://github.com/polyfem/polyfem/commit/ddadb5398f0fae2e825dedcf754aa7dd00bc3bb0)、[DiffIPC 作者页](https://huangzizhou.github.io/research/diffipc.html)。
- **libuipc：事实。** Apache-2.0、Python／C++、CUDA，有独立样例库和 2026 年发布活动；当前 main 与已发布 wheel 的能力存在差异。按选定场景锁定相互匹配的库／样例／扩展组合，并确保包含 2026-10-19 的 E／ν 映射修复。[发布页](https://github.com/spiriMirror/libuipc/releases)、[样例](https://github.com/spiriMirror/libuipc-samples)、[材料修复说明](https://spirimirror.github.io/libuipc-doc/specification/constitutions/stable_neo_hookean/)。
- **SOFA：事实。** 官方下载页当前发布 v26.06.00，含 SofaPython3、SoftRobots、STLIB、SofaCUDA 等；核心 LGPL，部分应用 GPL。现成 Python 场景和配套资产可以直接作为复跑入口，GPU 支持取决于选定组件。[下载及许可](https://www.sofa-framework.org/download/)、[26.06 变更](https://www.sofa-framework.org/about/news/sofa-v26-06-has-been-released/)。
- **FEBio：事实。** 官方 GitHub 最新 release 查询为 v4.13，发布日期 2026-08-04。源码 MIT，官网二进制使用单独许可；FEBio／Studio 可免费使用，且有官方模型仓库。直接使用 `.feb`／Studio 不依赖商业仿真软件，GIBBON／MATLAB 等可选上游工具应另算依赖。[v4.13](https://github.com/febiosoftware/FEBio/releases/tag/v4.13)、[官方说明](https://febio.org/)、[接触模型包](https://repo.febio.org/permalink/project/61)。

**可直接交给下一步的选择：** 先审阅 PolyFEM 的完整 Armadillo／Octocat 动态和高阶 IPC 对比，选择一个完整软物体作 E 展示；同一体积路线配球体压缩与固定外力／释放动作覆盖 ν、ρ。若更看重现成 Blender 制作和大量软硬接触，再选 libuipc；若目标对象明确带气腔／驱动／机构关系，选 SOFA PneuNet；若物件行为由纤维、粘弹性、损伤等决定，转用 FEBio 的相应公开材料模型。这里的条件排序来自本轮物理需求与证据，后续复跑只需围绕用户选定场景展开。
