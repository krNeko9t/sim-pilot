# 03：颗粒材料候选与动态演示

研究截止：2026-10-30。对应[工单 03](../issues/03-granular-material-candidates.md)与[探索规格](../spec.md)。本轮查阅作者论文、项目页、官方文档与公开源码，覆盖 2024–2026 公开成果并保留经典方法；记录的是候选和原作者结果，复跑原型在用户选定后开展。术语沿用 [GLOSSARY](../../../GLOSSARY.md)。

**AI 推荐先看 Mixed MPM 的参数视频，再看经典雪与沙演示。**前者已有可定位的 E 对照和公开实现；后两者最便于判断雪的压实、断裂以及沙的流动、堆积是否符合日常经验。需要成片外观时再看 PhysGaussian；需要理解“沙粒材料参数如何变成沙堆参数”时看 Homogenized Sand。该顺序是本次研究的判断，依据见各候选。

## 观看导航

| 候选与时间 | 覆盖与动态入口 | 具体观看指引 | 参数控制方式 | 演示证据与用途 |
| --- | --- | --- | --- | --- |
| **1. Mixed MPM / Newton，SIGGRAPH 2026** | 雪、沙；[参数视频 MP4][M-video]、[项目页与完整视频][M-home] | 先看 **00:03–00:08、00:30–00:50**：双块片段中左红块与右蓝块分别落在同形圆柱上，比较弹性形变；项目页另看雪崩开裂 | **直接输入物理量**：E、ν；ρ通过粒子质量赋值 | **视频已展示参数对比**：E=∞ / 10 MPa；ν、ρ为**代码支持调参，效果待复跑**。首选 |
| **2. Stomakhin 等，SIGGRAPH 2013** | 雪；[作者视频][S-video]、[作者页][S-home] | 找“雪块落在楔形障碍物上”、雪球撞击与滚雪球片段；观察压实、粘连、分块 | **直接输入物理量**：论文 E₀、ν、ρ₀；实现内部换算 Lamé 系数 | 当前按**仅有视觉效果参考**记录；论文有参数图对照，视频中各参数标签尚待逐项核实。雪的外观基准 |
| **3. Klár 等，SIGGRAPH 2016** | 干沙；[TeranGroup 作者视频][K-video] | 看沙漏、沙柱坍塌、球落入沙箱；结合论文 Fig.10 找“低 E 导致弹跳”的二维流入场景 | **直接输入物理量**：论文 E、ν、ρ；本构使用 Lamé 系数 | 当前按**仅有视觉效果参考**记录；论文已提供 E 数值对照，视频对应片段待定位。沙的参数依据强 |
| **4. PhysGaussian，CVPR 2024**（预印本 2023） | 沙状物件；[狼模型散落 MP4][P-wolf-video]、[完整视频][P-video] | 观察狼形物件逐层释放后成堆，以及纹理随运动变化；再看网页 Flexible Dynamics | **直接输入物理量**：E、ν、density；实际单位与场景缩放需匹配 | 颗粒场景为**代码支持调参，效果待复跑**；网页 E/ν 对照属于弹性物件。成片优先 |
| **5. Taichi Elements / MLS-MPM，2018 方法及后续项目** | 雪、沙；[Elements 三维动态演示][T-gif]、[2018 作者演示][T-video] | 先看小型多材质坠落；需要规模参考再看 [1 亿粒子视频][T-large] | Elements：E_scale 为**公式换算**；ν、ρ为源码常量可改。旧 3D 求解器：雪/沙输入 Lamé 系数 | **代码支持调参，效果待复跑**。轻量候选，版本与尺度需在选中后核实 |
| **6. Numerical Homogenization of Sand，SIGGRAPH Asia 2025** | 不同粒形的沙堆；[主视频 MP4][H-video]、[刚体参考视频][H-extra] | 看同形沙堡受撞、沙柱坍塌、沙漏中堵塞；比较球、六足粒、钩状粒 | **公式换算**：从颗粒接触实验拟合连续体本构；另有**视觉调节** | **仅有视觉效果参考**；主要比较粒形/摩擦/凝聚，本次未确认独立 E/ν/ρ 视频。物理语义补充 |

视频核查深度：Mixed MPM 参数 MP4 已抽帧核对时间和标签；其余已核对作者动态入口与论文/页面的场景说明，观看指引按场景定位，尚未逐帧核对全部片段。视频入口提供观看依据，源码参数表提供控制能力，两者分别记录。

## 读参数时先明确物理含义

这些 MPM 候选通常把雪团或沙堆当作**连续体**，计算粒子携带的是一小块材料的质量、体积和变形。E、ν描述其弹性本构；摩擦角、屈服阈值、凝聚与硬化另行控制塑性行为。2016 沙论文的 §3、§6.3 与 2025 均匀化论文的 §4、§5.4 明确区分这一层次；后者还说明宏观弹性与单粒接触弹性之间存在依赖粒形和接触网络的关系。[沙论文][K-paper]、[均匀化论文][H-paper]

ρ应按所用体积解释：连续体密度为该材料点代表体积内的质量；单颗真实砂粒的实体密度、沙堆的堆积密度、离散采样点数密度需要分别记录。例如 PhysGaussian 明确按 `mass=density*particle_volume` 赋质量；Elements 的 `sample_density` 控制采样点数，`p_rho` 才参与质量赋值。上游若提供砂粒实体密度，还需要孔隙/堆积信息才能给出同一意义的连续体初始密度；这一映射属于待选候选的资料核对项。[PhysGaussian 源码][P-sand]、[Elements 源码][T-solver]

对于这里采用常量、各向同性弹性系数的实现，常见换算是 `μ=E/[2(1+ν)]`、`λ=Eν/[(1+ν)(1−2ν)]`；μ在此表示剪切模量，论文中的摩擦系数也常用同一字母，阅读数值时须看各自定义。上述换算可直接在 Taichi 作者代码中核查；带压力依赖的均匀化模型应按自身本构函数处理。[2018 作者代码][T-legacy]、[均匀化论文 §5.4][H-paper]

## 1. Mixed Material Point Methods for Stiff Elastoplasticity / Newton

**已核实。**NVIDIA 作者页将论文标为 SIGGRAPH 2026，并链接 Newton 实现。它统一处理高刚度弹塑性材料，现成动态包括沙流穿过建筑、雪层裂纹和雪崩。Newton 官方 API 把 `young_modulus` 标为 Pa，`poisson_ratio` 为弹性泊松比；这里的 E/ν是连续体弹性参数。雪球示例用 `spacing³*density` 赋粒子质量，支持直接调整初始连续体密度。[项目页][M-home]、[API][M-api]、[雪球源码][M-snow]

**已有参数对比。**[参数视频][M-video]的双块片段以左红 E=∞、右蓝 E=10 MPa 对照；论文 Fig.23 和 §6.3 给出解释。00:03–00:08 为 Drucker–Prager，00:30–00:50 包含硬化、屈服压力、拉伸屈服比例及流动规则变化。应在**同一片段内部**比较左右；跨片段同时换了塑性设定。约 01:06 起的黏性卷曲另外比较黏度。Fig.23 基准摩擦系数为 0.68、压缩屈服压力为∞，其余流动参数为 0；ν、ρ在该图未单列，固定值待核实。[论文 Fig.23][M-paper]

| 当前公开示例默认值 | E / ν / ρ（kg/m³） | 还需固定的输入 |
| --- | --- | --- |
| `example_mpm_snow_ball.py` | 1.4×10⁶ Pa / 0.3 / 400 | friction=0.5；damping=0.01 s；yield_pressure=1.4×10⁶ Pa；tensile_yield_ratio=0.2；yield_stress=0；hardening=5；dilatancy=1；voxel_size=0.1；60 fps、每帧 1 substep；初始塑性体积比 0.975 |
| `example_mpm_granular.py` | 1×10¹⁵ Pa / 0.3 / 1000 | friction=0.68；yield_pressure=1×10¹² Pa；拉伸比例、剪切屈服、硬化、剪胀、黏度和阻尼为 0；voxel_size=0.1 |

表中数值来自两个示例的参数解析与初始化；沙示例的极高 E 用来近似刚性材料，属于求解设置。密度与米制粒子间距共同决定质量；实际场景尺度需在选用时核对。[雪球源码][M-snow]、[沙源码][M-sand]

论文雪崩的本体/弱层另用 E=14/1 MPa、ν=0.3、ρ=400，压缩屈服压力分别 10/1.5 MPa，其余塑性设定也不同。这是同一场景中的两层材料，适合看裂纹和滑移；E的独立效果由前述左右块对照支持。论文设置和当前雪球示例默认值各自成立。[论文 Table 5][M-paper]

**复跑输入。**Newton 公开提供程序化生成粒子的雪/沙示例，求解依托 Newton/Warp；默认查看器为 OpenGL，也支持 USD 输出，可作为公开仿真与可视化路线。论文大场景资产与成片渲染配置的齐备程度待选中后核实。[Newton 仓库][M-code]

**AI 推荐。**它最适合优先审阅和下一步候选选择：已有清楚的 E 极限对照，且雪/沙可沿用公开示例。若后续看 ν，可在屈服前做小幅压缩并观察横向变形；若看 ρ，可给同体积雪块相同外力/冲量。二者是待复跑场景建议，尚无本次验证的效果视频。

## 2. A Material Point Method for Snow Simulation

**已核实。**Stomakhin 等的 SIGGRAPH 2013 方法用可压缩弹塑性连续体表现雪的压实、断裂与雪球粘连。E₀是**初始**杨氏模量；压实/拉伸改变塑性体积后，Lamé 系数随硬化规则变化，因此雪在运动中的刚度随状态改变。ν控制弹性体积响应，ρ₀为初始连续体密度。[作者页][S-home]、[论文 §5][S-paper]

| 论文 Table 2 的建议起点 | 值 |
| --- | --- |
| E₀、ν、ρ₀ | 1.4×10⁵ Pa；0.2；400 kg/m³ |
| 临界压缩 θc、临界拉伸 θs、硬化系数 ξ | 0.025；0.0075；10 |

这些是论文建议起点，各成片场景可另设参数。Fig.8 的雪块过楔形场景展示多种参数对外观的影响；论文将压缩/拉伸阈值与碎块大小、硬化与脆性相联系。雪球场景还使用空间变化的刚度和密度，观察整体成片时应同时保留这些条件。[论文 Table 2、Fig.8、§10][S-paper]

**参数控制方式：直接输入物理量；演示证据：仅有视觉效果参考。**此档位针对本轮已核实的动态资料；论文静态对照确实存在。视频可供判断雪是否粉化、是否成块及压实是否自然；独立 E/ν/ρ 的片段标签与完整取值仍待定位。[作者视频][S-video]

**复跑输入。**Disney 将 Matterhorn 明确列为专有求解器，原生产程序与示例资产的公开获取条件待核实。经典论文可作为视觉与参数依据，实际候选可采用第 1/5 项公开实现；论文渲染使用专有体积渲染流程。[Matterhorn 官方说明][S-matterhorn]、[论文 §9][S-paper]

**AI 推荐。**先把“雪块落楔形障碍物”保留为 E 候选场景，后续固定 θc、θs、ξ、ν、ρ、形状与碰撞条件，仅改 E₀。可从 0.1/1/10 倍论文起点提出对照，具体稳定步长与适用范围留到复跑阶段；冻结全部材料状态会改变原模型含义，故记录初始状态即可。

## 3. Drucker–Prager Elastoplasticity for Sand Animation

**已核实。**Klár 等 SIGGRAPH 2016 使用 Hencky 应变弹性与 Drucker–Prager 塑性，E、ν属于沙连续体的弹性部分，ρ进入连续体质量与动量方程。论文把沙柱最终堆积角的差异归因于**摩擦角**；其 E 对比另外采用二维流入场景。[论文 §3、§6.3、§10][K-paper]

| 论文已有对比 | 变化量 | 已列固定条件 |
| --- | --- | --- |
| Fig.10：二维沙流入 | E=10³、10⁴、10⁵、10⁶ | ν=0.3；ρ=2200；硬化 h0/h1/h2/h3=35/9/0.3/10；表中角度量以度列出；网格 256×64、Δx=0.016；各组粒子数约 740、略有不同 |
| Fig.13：沙柱坍塌 | 摩擦角 20°、25°、30°、35°、40° | E=3.537×10⁵；ν=0.3；ρ=2200 |

表据论文 Tables 2–3。论文场景按米制长度/时间组织；该表 E、ρ列未在表头分别再标单位，应结合全文单位约定读取。作者指出 E 过低会出现弹跳/抖动，适度减小 E 又可能保持相似外观；因而视觉判断更适合发现数量级偏离。其文中引用的特定沙材参考值也应结合对应材料理解。[论文 Fig.10、§10、Tables 2–3][K-paper]

**参数控制方式：直接输入物理量；演示证据：仅有视觉效果参考，另有明确的论文 E 参数对照。**作者视频入口已核实；本轮尚未确认 Fig.10 对应的视频时段。ν、ρ独立动态对照待复跑。看沙漏时注意流动与成堆，看沙箱时注意受撞后的散开与恢复。[作者视频][K-video]

**复跑输入。**本轮找到作者论文和视频，原论文完整源码/场景数据的公开入口待核实。其后续公开实现包括 Taichi 与 PhysGaussian 的沙模型。论文所有场景用 SideFX Mantra 渲染；若采用公开求解器复现运动并接 Blender，商用后处理可替换，但原画面重现条件仍需核查。[论文 §9][K-paper]、[Taichi 作者代码][T-legacy]、[PhysGaussian 沙示例][P-sand]

**AI 推荐。**在已有证据中，它为“低 E 让沙表现出异常弹跳”提供了最直接的经典参数依据。这适合诊断数量级偏离；物理适用范围及数值稳定性要随步长一起确认。后续优先选简单沙流入，沿用表中 E 数列、固定 ν/ρ和塑性项；沙堆最终坡角的解释要保留摩擦项。

## 4. PhysGaussian

**已核实。**CVPR 2024 项目将 MPM 与 3D Gaussian Splatting 连接，能直接输出带原始外观的动态视频。配置列出 `E`、`nu`、`density`，材料包括 sand、snow；这些参数属于连续体力学。`density_threshold` 属于高斯内部填充判定，和力学质量密度 `density` 含义不同。[项目页][P-home]、[官方 README][P-code]

**参数控制方式：直接输入物理量；演示证据：代码支持调参，效果待复跑（颗粒场景）。**[狼散落视频][P-wolf-video]对应沙材外观；网页 [Flexible Dynamics][P-home]确有 E/ν方向对照，但展示的是弹性物件，不能据此确认沙/雪 E/ν可辨识性。狼模型的配置还会按时间逐层释放粒子，故其散落过程同时受边界事件控制。[狼配置][P-wolf]

| 公开例子 | 目标参数 | 固定/附加输入与数据 |
| --- | --- | --- |
| `wolf_config.json` | E=5×10⁷、ν=0.3、density=2000 | 摩擦角 30°；重力 (0,0,−9.8)；子步 2×10⁻⁵；网格 200；每帧 0.04 s、50 帧；粒子填充、粘着地面及逐层释放配置 |
| `mpm_solver_warp/run_sand.py` | E=2000、ν=0.2、density=200 | 摩擦角 35°；实际调用参数组重力 (0,0,−4)；`sand_column.h5` 自带；粒子体积 2.5×10⁻⁸；步长 0.002，输出 PLY |

上述是两个不同例子的原始数值，不构成对照。README要求把重建场景居中和缩放，物理尺度需与 E、ρ解释一起核查；当前资料支持名称与计算用途，直接当作真实尺寸 SI 标定输入仍需确认。沙例的质量赋值与 E/ν→Lamé 换算在代码中明确。[狼配置][P-wolf]、[沙示例][P-sand]、[求解器源码][P-solver]

**复跑输入。**公开代码、预训练高斯模型下载入口、JSON 配置以及沙柱 H5 已提供。完整流程支持 `--render_img --compile_video`；旧版环境说明列 Python 3.9、PyTorch/CUDA 与 Warp，选中后再核实当前硬件兼容性。README 使用 Houdini 辅助检查自定义高斯场景的朝向，现成例子的仿真和渲染命令走公开 Python/CUDA 流程。[官方 README][P-code]、[Warp 示例说明][P-mpm-readme]

**AI 推荐。**若用户重视物件外观与现成渲染，它优于只有点粒子预览的候选。若优先观察目标物理量，先使用沙柱数据更容易固定边界；狼场景可在参数证据补齐后作为成片展示。

## 5. Taichi Elements 与 2018 MLS-MPM

**已核实。**2018 作者仓库提供 MLS-MPM/CPIC、多材质示例与 88 行二维雪模型；后续 Elements 提供雪/沙、CPU/GPU及粒子渲染。两套项目的默认值与接口分别维护。[2018 作者仓库][T-legacy]、[Elements README][T-home]

| 实现 | 参数控制方式与已核实值 | 演示证据 |
| --- | --- | --- |
| Elements `MPMSolver` | E=`10⁶*size*E_scale`、ν=0.2、`p_rho=1000`；E_scale 属于**公式换算**，ν/ρ可改源码；沙摩擦角默认 45° | **代码支持调参，效果待复跑** |
| 2018 3D 求解器：雪 | 输入 `mu_0=58333.3`、`lambda_0=38888.9`，由 E/ν换算；hardening=10、θc=.025、θs=.0075 | **代码支持调参，效果待复跑**；ρ入口另行核实 |
| 2018 3D 求解器：沙 | 输入 `mu_0=136038`、`lambda_0=204057`；friction_angle=30、cohesion=0、beta=1 | 同上 |

旧 3D 求解器对 jelly 才在 README 直接列 E、ν；雪/沙列 Lamé 参数，应按各自入口换算。Elements 的 `p_vol=dx^dim`、`p_mass=p_vol*p_rho` 与 `sample_density` 分别控制积分粒子质量和采样数；演示的尺度/采样约定要在接收真实物理量前确认。[旧 API 文档][T-legacy]、[Elements 源码][T-solver]

**复跑输入。**Elements 包含简单程序化示例、PLY 模型下载脚本、GGUI 预览及 `render_particles.py`，另有实验性 Blender 插件。README 混有不同年代的 Blender/Taichi版本说明，兼容性待复跑核实。2018 高性能求解器依赖 legacy Taichi，原可视化指向 Houdini；若要用公开后处理路线，Elements 与基础二维示例更值得先选。[Elements README][T-home]、[2018 README][T-legacy]

**AI 推荐。**保留为简单雪块坠落、沙柱坍塌的可改代码候选。E/ν/ρ的参数含义可追溯，现有大规模渲染视频主要支持视觉合理性和计算规模，独立参数差异仍待复跑。另有现代 [Taichi `mpm99.py`][T-99]，E=1000、ν=.2、ρ=1，采用二维单位方域；实际尺寸单位约定待确认，其默认值应单独记录。

## 6. Numerical Homogenization of Sand from Grain-level Simulations

**已核实。**Chen、Ly、Wojtan，SIGGRAPH Asia 2025；ISTA 存档标明发表日期 2025-12-04。方法从极硬颗粒的接触模拟提取宏观应力—应变与屈服模型，展示不同粒形沙堡的坍塌、受撞和沙漏堵塞。主视频是 MPM，附加视频是刚体参考模拟。[机构记录][H-record]、[作者项目页][H-home]、[论文 §7][H-paper]

**参数控制方式：公式换算（均匀化拟合），另有视觉调节。**微观接触的 Young modulus `Y` 与连续体的等效 E/ν分属两层。宏观模型拟合随变形改变的体积/剪切模量，输入任意固定 E/ν来替换该模型的对应操作仍待核实；ρ也需结合初始堆积体积解释。论文明确说明当前模型对堆积密度与宏观行为关系的重建仍有限。[论文 §4.1、§5.4、§8][H-paper]

**演示证据：仅有视觉效果参考。**重点观看相同宏观几何、不同粒形的沙堡和沙漏；它们比较的是接触网络、摩擦、凝聚和本构组合。论文 Fig.18d 的艺术化增强同时把屈服曲线缩放 β=0.1、质量密度乘 10，属于混合调节，不能作为独立 ρ 对比。[论文 §7.3.1][H-paper]

已知附加输入包括粒形网格、颗粒接触摩擦、接触刚度、阻尼、初始化堆积与均匀化载荷路径。Table 1 的 Hertz 接触实验多用 Y=10¹¹ Pa、步长 0.002 s；球形摩擦系数取 0.1–0.6，六足粒为 0.3/0.8。论文将完整弹性拟合参数放在补充材料 §F，MPM模拟用自适应步长和 120 fps 输出；“沿用默认值”在这里应理解为选择一个现成材料/场景配置。[论文 Table 1、§7][H-paper]、[补充材料][H-supp]

**复跑输入。**作者分别发布[颗粒均匀化代码][H-grain]与[连续体代码][H-code]，README给出 `scenes/`、`configs/`及无GUI入口；C++/Eigen/OpenMP等公开依赖，颗粒数据拟合用 Python。支持 PLY 导出，论文成片用 Blender；完整拟合数据是否已覆盖所选场景仍待核实。README说明当前连续体程序每次模拟只用一种材料，适合同材料间分别复跑比较。[连续体 README][H-readme]、[均匀化 README][H-grain-readme]、[论文 §7][H-paper]

**AI 推荐。**它适合解释为什么相同颗粒实体材料会形成不同宏观沙堆行为，或选择“互锁颗粒堵住沙漏”这类醒目场景。对于已经给定 E/ν/ρ的快速演示，先选直接接受这些输入的候选更省工作；本项保留为物理语义与新视觉现象的补充。

## 保留的同类候选

| 候选 | 动态入口与观看重点 | 参数控制方式 / 演示证据 | 已知输入、代码与配套；保留理由 |
| --- | --- | --- | --- |
| **Matter v1，GMD 2025** | [沙柱坍塌 GIF][A-matter-video]；[雪崩地形 GIF][A-matter-mountain]；看流动、堆积与起伏地形 | **直接输入物理量** E/ν/ρ；**代码支持调参，效果待复跑** | [官方代码][A-matter-code]的最小坍塌例 E=10⁶ Pa、ν=.3、ρ=1000，Drucker–Prager参数 M=.5、cohesion=0、g=−9.81，1m方块；这组是示例配置。另有 μ(I) 的 `rho_s`（颗粒实体密度），应与连续体ρ区分。[论文][A-matter-paper]明确二维也用三维弹性参数的平面应变约定。CPU/OpenMP、示例与PLY导出；Houdini属于作者后处理路线，转Blender待选后适配。理论定义清楚，值得保留 |
| **Volume-Preserving LBM-MPM Coupling，SIGGRAPH 2026** | [作者视频][A-coupling-video]；看水侵蚀沙堡、沉积、湿沙与气泡 | 本轮按**视觉调节／目标物理量映射待核实**；**仅有视觉效果参考** | [作者页][A-coupling-home]与[代码][A-coupling-code]已公开；水/气/沙耦合会增加饱和度、滞留、表面张力等输入。各相密度的具体定义和E/ν入口、现成数据及渲染依赖待核实。适合作为后续ρ/浮沉场景候选 |
| **Digital Animation of Powder-Snow Avalanches，SIGGRAPH 2025** | [作者视频][A-avalanche-video]；看雪崩前沿卷起的粉雪云及地形互动 | 本轮按**视觉调节／E、ν适用性待核实**；**仅有视觉效果参考** | [作者页][A-avalanche-home]说明密雪层用 Savage–Hutter，粉雪层用两相 Navier–Stokes；[源码][A-avalanche-code]公开。密度、卷入和层间质量交换更相关，E/ν目标匹配较弱；示例数据和渲染完整链待核实。保留宏观雪景 |

## AI 建议的参数演示与证据边界

| 目标 | 优先候选与待复跑展示场景 | 应固定的条件 | 可以讨论的判断 |
| --- | --- | --- | --- |
| E | Mixed MPM同塑性设置的块体落圆柱；经典沙的二维流入；雪块过楔形障碍物 | ν、ρ、初始材料状态、塑性项、几何、边界、外力/重力、采样和渲染；步长满足对应稳定条件 | 弹性形变、弹跳与恢复是否明显偏离；已有视频/论文支持大差异可见 |
| ν | 选直接接受ν的候选，在塑性屈服前压缩材料块，观察侧向变形和体积 | E、ρ与其余设置；记录是否进入屈服 | 可联系泊松比的弹性定义判断；本轮颗粒视频证据仍待复跑 |
| ρ | 同体积雪块受相同外力/冲量；或沙对可运动刚体的冲击 | ρ以外材料参数、体积、外部质量、施力过程和重力 | 先看惯性/载荷差异；改变密度也可能改变塑性应力状态，解释须保留它 |

以上三行是 AI 基于候选方程和参数入口提出的场景建议。按沙论文动量方程，纯重力自由落体的加速度中ρ会消去；单纯比较下落快慢不适合作为密度演示。对固定几何的自由振动/碰撞，E与ρ也可能共同改变时间尺度，视觉结果要结合已固定的量解释。[沙论文式(2)][K-paper]

本轮最强的**动态目标物理量证据**是 Mixed MPM 的 E=∞/10 MPa 左右对照；经典沙论文又提供有限 E 的四档对照。雪和沙均已覆盖日常互动与受控场景。ν、ρ的独立动态比较，以及给定真实几何尺度后的参数意义，是下一阶段选定候选后最值得补齐的证据。现有资料支持视觉合理性与候选选择，材料参数的唯一识别仍需额外观测和已知条件。

## 一手来源

正文逐项链接一手来源；代码的 `main/master/latest` 为研究日所读公开版本，实际复跑时再选择相容版本。

[M-home]: https://research.nvidia.com/labs/prl/mixed_mpm/
[M-paper]: https://research.nvidia.com/labs/prl/mixed_mpm/files/mixed_mpm.pdf
[M-video]: https://research.nvidia.com/labs/prl/mixed_mpm/files/mixed_mpm_params_supplemental.mp4
[M-api]: https://newton-physics.github.io/newton/latest/api/_generated/newton.solvers.SolverImplicitMPM.html
[M-code]: https://github.com/newton-physics/newton
[M-snow]: https://github.com/newton-physics/newton/blob/main/newton/examples/mpm/example_mpm_snow_ball.py
[M-sand]: https://github.com/newton-physics/newton/blob/main/newton/examples/mpm/example_mpm_granular.py
[S-home]: https://alexey.stomakhin.com/research/snow.html
[S-paper]: https://alexey.stomakhin.com/research/siggraph2013_snow.pdf
[S-video]: https://vimeo.com/160322962
[S-matterhorn]: https://www.disneyanimation.com/technology/matterhorn/
[K-paper]: https://www.math.ucdavis.edu/~jteran/papers/KGPSJT16.pdf
[K-video]: https://vimeo.com/163601169
[P-home]: https://xpandora.github.io/PhysGaussian/
[P-code]: https://github.com/XPandora/PhysGaussian
[P-video]: https://www.youtube.com/watch?v=V96GfcMUH2Q
[P-wolf-video]: https://xpandora.github.io/PhysGaussian/static/videos/wolf.mp4
[P-wolf]: https://github.com/XPandora/PhysGaussian/blob/main/config/wolf_config.json
[P-sand]: https://github.com/XPandora/PhysGaussian/blob/main/mpm_solver_warp/run_sand.py
[P-solver]: https://github.com/XPandora/PhysGaussian/blob/main/mpm_solver_warp/mpm_solver_warp.py
[P-mpm-readme]: https://github.com/XPandora/PhysGaussian/tree/main/mpm_solver_warp
[T-home]: https://github.com/taichi-dev/taichi_elements
[T-solver]: https://github.com/taichi-dev/taichi_elements/blob/master/engine/mpm_solver.py
[T-legacy]: https://github.com/yuanming-hu/taichi_mpm
[T-99]: https://github.com/taichi-dev/taichi/blob/master/python/taichi/examples/simulation/mpm99.py
[T-video]: https://www.youtube.com/watch?v=8iyvhGF9f7o
[T-gif]: https://github.com/taichi-dev/public_files/raw/master/taichi_elements/demo_3d_ggui.gif
[T-large]: https://youtu.be/klMDVUzFFnk
[H-home]: https://visualcomputing.ist.ac.at/publications/2025/HomogenizedSand/
[H-record]: https://research-explorer.ista.ac.at/record/20628
[H-paper]: https://research-explorer.ista.ac.at/download/20628/20629/main_paper.pdf
[H-supp]: https://research-explorer.ista.ac.at/download/20628/20630/paper_supplemental.pdf
[H-video]: https://research-explorer.ista.ac.at/download/20628/20631/main_video.mp4
[H-extra]: https://research-explorer.ista.ac.at/download/20628/20632/extra_video.mp4
[H-code]: https://git.ista.ac.at/wojtan-group/yi-lu-chen/lgw-mmmm
[H-grain]: https://git.ista.ac.at/wojtan-group/yi-lu-chen/lgw-gggg
[H-readme]: https://git.ista.ac.at/wojtan-group/yi-lu-chen/lgw-mmmm/-/raw/master/README.md
[H-grain-readme]: https://git.ista.ac.at/wojtan-group/yi-lu-chen/lgw-gggg/-/raw/master/README.md
[A-matter-paper]: https://gmd.copernicus.org/articles/18/9149/2025/
[A-matter-code]: https://github.com/larsblatny/matter
[A-matter-video]: https://larsblatny.github.io/images/collapse_fps50_cropped_medium.gif
[A-matter-mountain]: https://larsblatny.github.io/images/mountain_cropped_medium_new.gif
[A-coupling-home]: https://lwkobe.github.io/papers/XWYDL2026/
[A-coupling-code]: https://github.com/DacianShaw/LBM-MPM-Air-Water-Sand
[A-coupling-video]: https://www.youtube.com/watch?v=LPqutmyjDyM
[A-avalanche-home]: https://filipecn.github.io/psa_anim/
[A-avalanche-code]: https://github.com/filipecn/psa_anim
[A-avalanche-video]: https://www.youtube.com/watch?v=rHvtYA-lLIk
