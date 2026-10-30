# 固体形变候选与动态演示

研究日期：2026-10-30。对应[工单 01](../issues/01-solid-deformation-candidates.md)与[探索规格](../spec.md)。本文件提供人工审阅入口；代码、配置和小段作者媒体只做证据核查，安装与复跑留到用户选择之后。名称沿用根目录 [GLOSSARY](../../../GLOSSARY.md)。

**AI 初步推荐：先看 PhysGaussian 的枕头落沙发与材料球网格，再看 FreeForm 的玩具/葡萄落地；需要把 E、ν 分开解释时，优先考虑 IPC、DiffPD 或 MiNNIE 的受控场景。** PhysTwin 很适合判断毛绒玩具的日常视觉合理性，但其弹簧参数到 E、ν 的映射仍待核实。肉与水果的撕裂由 AnisoMPM 补充；金属压皱、玻璃破裂属于另一组现象，须同时考虑塑性或损伤参数。

## 如何阅读证据

- **已证实**：已读到作者论文、官方文档或具体代码；只证明对应能力或配置。
- **待核实**：资料入口存在，当前证据尚未支持该结论。
- **AI 推断**：拟议展示场景、观看顺序或效果判断；尚待选中后复跑。
- **抽帧已核查**：实际检查作者媒体的多个时间点，能够核对物件与形变过程；观察范围见具体条目。它与完整连续观看、仿真复跑分别记录。
- **作者定位，未逐帧核查**：根据作者图注、页面分节或代码说明提供观看位置；时间戳保持待核实。

参数控制方式与演示证据是两条独立轴。“有 E 输入”“论文使用某个 E”与“视频中已经控制变量比较 E”分别陈述。本文单组配置只作为后续复跑起点。除明确记录的作者对比外，建议试值均为 **AI 推断**：可先试 E 的 0.1×/1×/10×；ν 可从 0.2/0.35/0.45 选择适合模型的组合；其余输入沿用所选示例原设置并固定。近不可压缩材料另按求解器适用范围选值。

## 先看这些动态入口

| 候选与时间 | 观看入口与定位 | 本次核查范围 | 推荐用途（AI 推断） |
| --- | --- | --- | --- |
| [PhysGaussian](https://xpandora.github.io/PhysGaussian/)，CVPR 2024 | [枕头落沙发，6 秒](https://xpandora.github.io/PhysGaussian/static/videos/pillow2sofa.mp4)；[材料球网格，约 2 秒](https://xpandora.github.io/PhysGaussian/static/videos/materials_cropped.mp4) | 抽帧已核查：枕头 0/1.5/3.03 秒；材料球 0/约 1.03 秒 | 日常软硬感、枕头/家具；已有 E/ν 定性比较 |
| [FreeForm](https://research.nvidia.com/labs/sil/projects/freeform/)，CVPR 2026 | [狗玩具落地](https://research.nvidia.com/labs/sil/projects/freeform/assets/videos/splats/dog.mp4)；[葡萄落地](https://research.nvidia.com/labs/sil/projects/freeform/assets/videos/splats/grape.mp4)，各 10 秒 | 抽帧已核查 0/2/4/6/8/9.9 秒 | 真实外观的玩具与水果；E 对接触变形的影响待复跑 |
| [PhysTwin](https://jianghanxiao.github.io/phystwin-web/)，ICCV 2025 | 页面 Applications → Keyboard Control → sloth；Results → Double stretch sloth | 树懒玩具交互短片抽帧已核查 0/7.33/14.67/22 秒 | 毛绒物件视觉合理性参考 |
| [VR-Doh](https://simulation-intelligence.github.io/VR-Doh/)，SIGGRAPH 2025 | [双手挤压球体，5.7 秒](https://simulation-intelligence.github.io/VR-Doh/videos/contact1_compressed.mp4) | 抽帧已核查 0/1.14/2.28/3.42/4.56/5.6 秒 | 塑形/软玩具互动；释放后的残余形状值得观察 |
| [SoftMAC](https://minliu01.github.io/SoftMAC/)，IROS 2024 | [夹爪挤塑形材料 GIF](https://raw.githubusercontent.com/minliu01/SoftMAC/main/media/grip_merged.gif)；[作者全片](https://www.youtube.com/watch?v=3n_PlXzD_Sc) | GIF 0/1/2/3/4/4.9 秒抽帧已核查；全片待观看 | 夹爪作用易理解；轨迹优化比较与材料参数比较分开 |
| [IPC](https://ipc-sim.github.io/)，SIGGRAPH 2020 | [作者全片](https://www.youtube.com/watch?v=y96jk-eUCgI)；页面首个 Squeeze out 镜头 | 作者定位，未逐帧核查 | 挤压、多物体接触；E/ν 输入明确 |
| [DiffPD](https://diffpd.csail.mit.edu/)，TOG/SIGGRAPH 2022 | [作者全片](https://www.youtube.com/watch?v=DdNHmkzdWZE)，Cantilever / Bunny 示例名可与仓库对照 | 视频入口与示例对应关系已核，镜头时间待观看 | 简单梁、软兔受控实验 |
| [MiNNIE](https://lwruan.com/)，SIGGRAPH Asia 2024 | [作者链接的 B 站视频](https://www.bilibili.com/video/BV1xZS4YXE65)，论文 Fig.14 的软字母落箱场景 | 作者定位；视频取回受站点限制，未逐帧核查 | ν 接近 0.5 时的体积保持 |
| [Genesis World](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/physics/beyond_rigid_bodies.html)，2024 起、核查 v1.4.5 文档 | Beyond rigid bodies → MPM 小视频：弹性方块、液体方块、弹塑性球下落 | 官方图注与例程已核，视频未逐帧核查 | 几何简单、材料参数直接的起点 |
| [AnisoMPM / Ziran 2020](https://joshuahwolper.com/anisompm)，SIGGRAPH 2020 | [作者主视频](https://www.youtube.com/watch?v=eXJi7pkUZn0)，对应 Anisotropic Feast 与 Dongpo Pork | 作者图注定位，未逐帧核查 | 生肉、橙瓣、猪肉分层撕裂 |
| [SoliDualSPHysics](https://arxiv.org/html/2602.15149v2)，CPC 2026 | 论文 §4.3.2 / Fig.39 的 Taylor bar 连续时刻图；§4.2 的裂纹基准 | 已核论文与 XML；动态视频入口待找到 | 金属塑性、脆性破坏的理论对照候选 |

## 1. PhysGaussian：枕头、家具与参数球，优先观看

**原始来源与时间。** [论文](https://arxiv.org/abs/2311.12198)于 2023-11 首次提交，2024-04 版本对应 CVPR 2024；方法把 MPM 动力学与 3D Gaussian 渲染结合。[作者项目页](https://xpandora.github.io/PhysGaussian/)列出弹性、金属塑性等不同材料场景。

**动态观察。** 枕头短片中，0 秒的枕头/坐垫位于沙发上方，约 1.5–3.03 秒出现落座接触、压缩和回弹。材料球网格从 0 到约 1.03 秒可见不同压扁/鼓胀程度。[材料球论文 Fig.6](https://arxiv.org/html/2311.12198v3/filling_comparison.png)只标 E 向左增大、ν 向下增大，另含填充/空壳对比；[§4.3](https://arxiv.org/html/2311.12198v3#S4.SS3)说明重力下的体积保持。[罐体短片](https://xpandora.github.io/PhysGaussian/static/videos/can.mp4)的 0/2/3 秒显示罐身压皱；它是塑性效果参考。

**参数控制方式：直接输入物理量字段；单位与几何尺度关系待核实。**

| 目标物理量 | 已证实能力 | 待核实边界 |
| --- | --- | --- |
| E | MPM 参数字典接受 `E` | 归一化几何下的 SI 标度、上游几何单位映射 |
| ν | 接受 `nu`，进入 Lamé 参数 | 各场景可用范围与求解稳定性 |
| ρ | 接受 `density`，粒子质量由密度×体积计算 | 与真实 kg/m³ 的尺度一致性 |

依据：[MPM 实现](https://github.com/XPandora/PhysGaussian/blob/main/mpm_solver_warp/mpm_solver_warp.py)与[官方使用说明](https://github.com/XPandora/PhysGaussian)。作者的场景预处理包含几何归一化，因此本阶段保留代码数值，避免直接贴上 SI 单位。

**演示证据。** 材料球为“视频已展示参数对比”，但仅核实 E/ν 的变化方向；实际 E/ν 数值、ρ、完整固定条件及球体场景配置仍待定位。枕头为“代码支持调参，效果待复跑”。公开[枕头配置](https://github.com/XPandora/PhysGaussian/blob/main/config/pillow2sofa_config.json)是单组 `E=1e4, nu=0.3, density=2000, material=jelly, g=(0,0,-5), substep_dt=1e-4, n_grid=100, frame_dt=0.02, frame_num=300`；它与作者短片逐帧一致的运行记录待核实。该组值也不代表真实枕头材料标定。

**代码、数据、依赖、渲染。** [官方仓库](https://github.com/XPandora/PhysGaussian)公开求解与 Gaussian 渲染代码，提供预训练模型下载目录；[`download_sample_model.sh`](https://github.com/XPandora/PhysGaussian/blob/main/download_sample_model.sh)明确下载 ficus 示例。已见[配置目录](https://github.com/XPandora/PhysGaussian/tree/main/config)含 pillow2sofa、plane 等；材料球、罐体的对应配置和全部资产齐备性待核实。公开路线为 CUDA/Warp/PyTorch 与 Gaussian rasterizer；商业仿真软件必需依赖在已读安装说明中未出现。配套渲染已提供。

**AI 推荐。** 视觉合理性优先：枕头的下陷、恢复和坐垫接触很容易讨论，适合先选观看。参数辨识度：材料球已有清楚的相对变化；如要定量说明输入，复跑时应补齐标度与控制条件。理论对照：球体压缩与 ν 的体积保持可单独成组；罐体残余折皱还受屈服等参数控制。

## 2. FreeForm：2026 新作，玩具与葡萄的原始 3DGS 动态

**来源。** [CVPR 2026 作者页](https://research.nvidia.com/labs/sil/projects/freeform/)与[论文](https://arxiv.org/abs/2605.29318)，预印本 2026-05-28。方法从粒子表示构造降阶变形基，页面同时给出梁基准、Thingi10K、SimReady、3DGS 和机器人互动。

**动态观察。** 已检查 10 秒的狗玩具与葡萄短片 6 个时刻：初始物件在地板上方，约 2 秒发生接触形变，4 秒后进入较稳定的落地形状。狗玩具的身体和细肢形状有明显变化；葡萄串的颗粒相对布局与接触侧形状可观察。这里使用页面 **Simulation on 3D Gaussian Splats** 的原始视频；机器人章节另列 Cosmos-Transfer 视频，生成式外观处理后的画面应另作视觉参考。[狗玩具](https://research.nvidia.com/labs/sil/projects/freeform/assets/videos/splats/dog.mp4)、[葡萄](https://research.nvidia.com/labs/sil/projects/freeform/assets/videos/splats/grape.mp4)。

**参数控制方式：直接输入物理量。** E、ν、ρ **均已证实**：[`create_model.py`](https://github.com/nv-tlabs/freeform-eval/blob/main/sim/create_model.py)接受统一的 `young_modulus / poisson_ratio / density`，也接受每体素属性；[`run_sim.py`](https://github.com/nv-tlabs/freeform-eval/blob/main/sim/run_sim.py)把这些值传给 `PhysicsPoints`。实际玩具/葡萄视频数值及标度待核实。属性可以由外部提供；VoMP 的参数预测流程属于本工单范围外。

**演示证据：代码支持调参，效果待复跑。** 已看落地效果；作者梁视频主要比较求解方法/自由度，当前未定位同物件 E/ν/ρ 扫值短片。公开[梁创建配置](https://github.com/nv-tlabs/freeform-eval/blob/main/config/beam/beam5m_res50x10x10/create_rkpm.yaml)是 `E=5e6, ν=0.45, ρ=1000`；[仿真配置](https://github.com/nv-tlabs/freeform-eval/blob/main/config/beam/beam5m_res50x10x10/sim_rkpm_fix_right_0p5m.yaml)使用 `dt=0.01, num_steps=300`。这是单组例程，不是玩具或葡萄视频的参数。

**代码、数据、依赖、渲染。** [官方 evaluation 仓库](https://github.com/nv-tlabs/freeform-eval)提供 Beam 和 19 个 SimReady 四面体/材料示例，依赖 Kaolin、Warp、PyTorch 等，带 Polyscope 对照查看器；3DGS 展示场景是否都在公开包中待核实。Beam 数据与 evaluation 代码为 Apache-2.0；SimReady 数据是 NVIDIA 非商业研究/评估许可。公开求解路线无需商业仿真软件；作者 3DGS 高质量渲染的完整复现入口待核实。

**AI 推荐。** 视觉合理性：真实外观的毛绒物件比抽象网格更便于讨论。参数辨识度：先用玩具或梁分开改变 E，葡萄作为水果几何候选；仅凭果形外观尚不足以确认其采用真实果肉、果皮材料。理论对照：梁的 FEM 参考结果可辅助区分参数改变与降阶近似误差。

## 3. PhysTwin：毛绒玩具的日常互动参考

**来源。** [ICCV 2025 作者页](https://jianghanxiao.github.io/phystwin-web/)和[代码](https://github.com/jianghanxiao/phystwin)。作者从 RGB-D 互动观测构建弹簧质量动力学与 Gaussian 外观，展示玩具、绳、布和包裹。

**动态观察。** Applications → Keyboard Control 的树懒短片中，已检查 0/7.33/14.67/22 秒：两端在按键控制下抬升、拉开和晃动。可以观察身体随夹持部位变形；这些帧没有目标物理量切换标注。Results 另有 Double stretch sloth、Double lift zebra，便于人工对照原观测与重仿真。[作者动态页面](https://jianghanxiao.github.io/phystwin-web/)。

**参数控制方式：视觉调节（弹簧模型参数）。** E：**待核实**，`init_spring_Y=3e4` 是弹簧初始化值，连续体杨氏模量映射待建立；ν：**待核实**，已读入口未确认独立物理量输入；ρ：体积密度到离散质量的可直接输入路径 **待核实**，已读[初始化代码](https://github.com/jianghanxiao/phystwin/blob/main/qqtt/engine/trainer_warp.py#L244)使用全 1 质量数组。实际互动会从 [checkpoint 加载 spring_Y](https://github.com/jianghanxiao/phystwin/blob/main/qqtt/engine/trainer_warp.py#L1782)，因此 [`configs/real.yaml`](https://github.com/jianghanxiao/phystwin/blob/main/configs/real.yaml)的初始化值与作者视频最终材料设置分别记录。[material visualization](https://github.com/jianghanxiao/phystwin/blob/main/qqtt/engine/trainer_warp.py#L1916)先计算等效刚度，再按最小/最大值归一化着色；颜色与 E/ν 物理量之间的映射待核实。

**演示证据：对本工单目标 E/ν/ρ，目前为仅有视觉效果参考。** 既有视频是互动/预测结果，不是目标物理量控制变量比较。若选中，AI 建议先核实质量与弹簧控制的物理映射，再决定承担哪一项参数展示。

**代码、数据、依赖、渲染。** 官方仓库公开 MIT 主代码、下载数据/checkpoint 的说明、预训练交互入口和 Gaussian 渲染；外部子模块及资产分别依其许可。作者路线使用 Warp/CUDA/PyTorch，公开安装说明以这些工具为主。具体树懒 checkpoint 与本地环境适配留待选择后核对；复跑现有 twin 可避免先采集新物件视频。[README](https://github.com/jianghanxiao/phystwin)。

**AI 推荐。** 视觉合理性高优先；参数辨识度当前低于直接连续体输入的候选。理论对照价值在互动重建与轨迹预测，E/ν 理论定义对照尚待支撑。

## 4. IPC：直接 E/ν/ρ 与稳定接触的受控起点

**来源。** [SIGGRAPH 2020 作者页](https://ipc-sim.github.io/)与[参考实现](https://github.com/ipc-sim/IPC)。首个 **Squeeze out** 场景中，压板把多个软体有限元物件压过细管障碍；作者给出 `h=0.01 s`、neo-Hookean 材料与 181K 四面体。观看时关注接触区域的变扁、挤出及恢复；该定位依据作者图注，当前视频时间待人工观看。[作者视频](https://www.youtube.com/watch?v=y96jk-eUCgI)。

**参数控制方式：直接输入物理量。** E **已证实**，`stiffness` 使用 Pa，默认 `1e5`；ν **已证实**，默认 `0.4`；ρ **已证实**，`density` 使用 kg/m³，默认 `1000`。每物体可用 `material <density> <young-modulus> <poisson-ratio>`。[输入格式](https://github.com/ipc-sim/IPC#input-script-format)。

**演示证据：代码支持调参，效果待复跑。** 作者 Squeeze out [论文参数表](https://web.uvic.ca/~teseo/profile/publications/ipc/2020-IPC.pdf)给出 `ρ=1000 kg/m³, E=5e4 Pa, ν=0.4`，属于单场景参数。当前未核对 E/ν 扫值视频。AI 建议 E 用同一已知外力下的下陷/下垂，或同一夹持轨迹释放后自由部分的回弹；ν 用同一轴向变形观察横向鼓胀及体积变化，并固定接触参数。强制夹持端的位移由边界设定，观察应落在材料自由响应上。

**代码、数据、依赖、渲染。** [`input/`](https://github.com/ipc-sim/IPC/tree/main/input)含作者论文场景及教学示例；MIT 代码支持 Eigen、SuiteSparse/CHOLMOD、AMGCL 等求解路线。提供逐时刻 OBJ 与初末 PNG 输出，可另接 Blender；商业仿真软件属于论文比较对象，公开求解路线采用上述库。[官方 README](https://github.com/ipc-sim/IPC)。

**AI 推荐。** 日常视觉素材丰富程度中等，受控挤压的参数辨识度高。理论对照价值较高，尤其是接触与体积保持。代码能力与作者稳定性结论均未在本项目复跑。

## 5. DiffPD：梁与软兔，便于拆开材料参数

**来源。** [作者页](https://diffpd.csail.mit.edu/)、[2021 预印本](https://arxiv.org/abs/2101.05917)、官方仓库标注的 TOG/SIGGRAPH 2022。视频中 Cantilever、Bunny 与仓库同名脚本有明确对应；[作者全片](https://www.youtube.com/watch?v=DdNHmkzdWZE)的具体镜头时间待观看。[例程索引](https://github.com/mit-gfx/diff_pd_public#examples)。

**参数控制方式：直接输入物理量，并在内部公式换算。** E **已证实**，`youngs_modulus` 默认 `1e6`；ν **已证实**，`poissons_ratio` 默认 `0.45`；ρ **已证实于源码**，示例 `density=1e3` 在局部写死，修改该值需编辑示例。内部使用 `μ=E/[2(1+ν)]`、`λ=Eν/[(1+ν)(1−2ν)]`。[`cantilever_env_3d.py`](https://github.com/mit-gfx/diff_pd_public/blob/master/python/py_diff_pd/env/cantilever_env_3d.py)。数值与实际几何标度关系待所选场景确认。

**演示证据：代码支持调参，效果待复跑。** 现有视频主要承担求解、优化与系统辨识演示；本次没有把这些目标优化结果计作 E/ν 控制变量比较。AI 建议固定梁根部与加载条件比较 E 的弯曲/振动；ν 另用横向宽度与体积观测。现成 cantilever 有固定密度与生成几何，可减少新资产工作；其随机外力也需随示例种子固定。[环境源码](https://github.com/mit-gfx/diff_pd_public/blob/master/python/py_diff_pd/env/cantilever_env_3d.py)。

**代码、数据、依赖、渲染。** [官方仓库](https://github.com/mit-gfx/diff_pd_public)公开 C++/Python 与网格资产；README 推荐 Ubuntu 18.04/GCC 7.5，现代环境适配待选后确认。例程含 PBRT Python wrapper 与渲染脚本。代码路线可使用开放求解与渲染工具；现有代码的许可与 PBRT/其他子模块按各自文件核对。

**AI 推荐。** 玩具柔软程度的视觉合理性可用兔子等模型讨论；梁场景的 E 影响更清楚。理论对照优先级高于照片外观展示。

## 6. MiNNIE：把 ν 接近不可压缩的表现单独拿出来

**来源。** [作者主页](https://lwruan.com/)标注 SIGGRAPH Asia 2024；[论文](https://lwruan.com/assets/pub/minnie.pdf)与[补充推导](https://lwruan.com/assets/pub/minnie-sup.pdf)。[作者视频](https://www.bilibili.com/video/BV1xZS4YXE65)入口已找到；本次站点返回限制，观察说明依据作者论文 Fig.14：软的 SIGGRAPH 字母落入箱体，ν=0.49999，观察堆叠接触时的体积保持。该数值是场景值，视频中的 ν 扫值对比待核实。

**参数控制方式：直接输入物理量。** E、ν、ρ **均已证实**，配置字段是 `young / poisson / density`。[`bulging.yaml`](https://github.com/LwRuan/MiNNIE/blob/main/configs/bulging.yaml)为 `1e6 / 0.45 / 1e3`；[`bunny.yaml`](https://github.com/LwRuan/MiNNIE/blob/main/configs/bunny.yaml)为 `2e6 / 0.49 / 1e3`。模型为 mixed FEM，适合近不可压缩问题；单位/几何标度、具体参数组对应的视频待核实。

**演示证据：代码支持调参，效果待复跑。** 论文重点是求解方法与体积保持。上述两个配置的场景也不同，应分别使用。AI 建议在一个选定软块挤压场景固定 E、ρ，比较 ν=0.3/0.45/0.49；接近 0.5 的更极端值仅在所选模型与求解设置明确后加入。`bulging.yaml` 为准静态，适合 ν 形变展示；ρ 动态影响宜选择另一个动态场景。

**代码、数据、依赖、渲染。** [仓库](https://github.com/LwRuan/MiNNIE)含 assets、configs、CUDA 求解与 Vulkan/shader 查看器；依赖 xmake、CUDA≥12、Vulkan 等。论文致谢提到使用 Houdini Education 生成视频，这属于作者渲染流程；开源求解/查看器是独立路线，最终 Blender 输出衔接待核实。仓库根部未显式列出 LICENSE，复用权限需在所选后核对，当前保留作研究候选。[论文](https://www.tiantianliu.cn/papers/ruan24minnie/ruan24minnie.pdf)、[仓库文件列表](https://github.com/LwRuan/MiNNIE)。

**AI 推荐。** 日常直观性中等；ν 理论与参考对照优先级高。选择它是为了观察可压缩性差异，所见软硬差别仍要与 E 的影响分开。

## 7. Genesis World：默认设置齐全的弹性方块

**来源。** [官方仓库](https://github.com/Genesis-Embodied-AI/genesis-world)及核查时的 [v1.4.5 教程](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/physics/beyond_rigid_bodies.html)。它作为持续更新的工程项目纳入；版本文档时间与论文发表时间分别记录。

**动态入口。** 教程 MPM 分节展示弹性方块、液体方块、弹塑性球下落。作者图注与[`examples/tutorials/mpm.py`](https://github.com/Genesis-Embodied-AI/genesis-world/blob/main/examples/tutorials/mpm.py)相符；本次尚未逐帧核查。对固体重点看弹性方块接触地面后压缩与恢复，球体的残余形状则帮助讨论弹塑性。

**参数控制方式：直接输入物理量。** E、ν、ρ **均已证实**：[MPM Elastic 文档](https://genesis-world.readthedocs.io/en/v1.4.0/api_reference/engine/material/mpm/elastic.html)默认 `E=3e5, nu=0.2, rho=1000 kg/m³`，应力模型默认 corotation，另有 neo-Hookean 选项；FEM Elastic 的 E 单位 Pa 在[官方教程](https://genesis-world.readthedocs.io/en/v1.4.5/user_guide/physics/beyond_rigid_bodies.html)明确标注。MPM 与 FEM 的单位和数值传递应以选定版本复核，避免混用两个求解器的设置。

**演示证据：代码支持调参，效果待复跑。** 教程比较的是不同材料类型、形状与大小，当前不计作 E/ν 参数对比。其弹性方块尺寸 `0.2×0.2×0.2`，中心 `z=0.25`，例程 `dt=4e-3, substeps=10`；这些是可固定的代码设置。[例程](https://github.com/Genesis-Embodied-AI/genesis-world/blob/main/examples/tutorials/mpm.py)。AI 建议先复制这一固体场景比较 E，保持几何、落高、时间步及接触相同；ν 可另设受控压缩。

**代码、数据、依赖、渲染。** Apache-2.0 项目，基本几何由例程生成；同一 Scene 可设 viewer 与 camera，提供 rasterizer/ray tracer 等渲染选项。[仓库](https://github.com/Genesis-Embodied-AI/genesis-world)、[渲染配置](https://genesis-world.readthedocs.io/en/latest/user_guide/configuration/config_system.html)。公开求解路线使用项目自身引擎；商业仿真软件必需依赖在这些入口中未出现。复杂家具、实物玩具资产仍需另选。

**AI 推荐。** 优势是默认值与简洁例程；基础块体具有清晰参数辨识度。日常对象真实感需要选好几何与外观，简单块体不承担真实果肉/枕芯验证。

## 8. SoftMAC：夹爪挤塑形材料，参数语义要看实现

**来源。** [IROS 2024 作者页](https://minliu01.github.io/SoftMAC/)、[论文](https://arxiv.org/abs/2312.03297)、[仓库](https://github.com/minliu01/SoftMAC)。旧项目域名已迁移，本文使用 `minliu01` 新入口。

**动态观察。** [5 秒 GIF](https://raw.githubusercontent.com/minliu01/SoftMAC/main/media/grip_merged.gif)三列显示夹爪对橙色塑形材料的挤压；本次检查 0–4.9 秒的六帧。作者页面说明这类三列对应初始、中间、最终优化轨迹，因此应观察夹持动作与目标形状的改进，把材料常数对比另列。[作者视频](https://www.youtube.com/watch?v=3n_PlXzD_Sc)。

**参数控制方式：E/ν 字段输入＋实现中的换算；ρ 需要改源码。** E **已证实**，ν **已证实**：[grip 配置](https://github.com/minliu01/SoftMAC/blob/main/softmac/config/demo_grip_config.py)为 `E=3000, nu=0.2, yield_stress=30, ptype=0, material_model=0`。ρ **已证实为内部常量**：`p_rho=1`；SI 标度待核实。[MPM 初始化](https://github.com/minliu01/SoftMAC/blob/main/softmac/engine/mpm_simulator.py)把 E/ν 换为 λ/μ，且当 `ptype=1`（elastic）时再把二者乘 `0.3`。因此改成弹性示例后，字段 E 与最终使用的弹性系数有额外比例关系。

**演示证据：代码支持调参，效果待复跑。** 当前已看的是轨迹优化效果。公开 grip 的固定项还包括重力 `(0,-9.8,0)`、`dt=2e-4`；拟议 E 对比可保持原夹爪轨迹、屈服参数及接触设置，重点观察自由部分和释放后的响应，或另设相同外力加载。若直接改材料模式，须作为新展示场景记录。

**代码、数据、依赖、渲染。** MIT 仓库包含 soft-rigid 演示与资源，使用 Jade 刚体求解器、Taichi 等；有 renderer 和生成 GIF 的示例。soft-cloth 的 DiffClothAI 底层在 README 中仍标为未开源，因此 taco/towel 画面作为视觉候选保存，复跑可行性单独待核。[README](https://github.com/minliu01/SoftMAC)。

**AI 推荐。** 夹爪动作直观；适合塑形材料，而普通硬塑料瓶和弹性果冻需要另外验证。目标物理量的参数透明度低于 IPC/Genesis；理论对照先限定在其明确的材料模式内。

## 9. VR-Doh：双手压捏，弹塑性边界清晰

**来源。** [SIGGRAPH 2025 作者页](https://simulation-intelligence.github.io/VR-Doh/)与[论文 v5](https://arxiv.org/html/2412.00814v5)，首稿 2024-12，修订 2025-06。

**动态观察。** [contact1 短片](https://simulation-intelligence.github.io/VR-Doh/videos/contact1_compressed.mp4)中两组虚拟手掌压蓝球：约 2.28–3.42 秒压成较窄长形，约 4.56–5.6 秒手掌离开后仍保持长形。双栏本身的对比变量尚未定位；本次只据画面记录压捏与残余形状。用户可以据此判断是否喜欢这种建模互动。

**参数控制方式：直接输入物理量。** E、ν、ρ **均已证实**：[C# 组件](https://github.com/Simulation-Intelligence/VR-Doh/blob/main/Assets/Scripts/Mpm3DMarching.cs)有 `_E / _nu / p_rho`，质量由体积×`p_rho` 计算；[Taichi 核](https://github.com/Simulation-Intelligence/VR-Doh/blob/main/scripts/mpm3D_gaussian_part.kernel.py)使用 E/ν→λ/μ。论文报告 `E=1e6 Pa, ν=0.3, yield stress=1e5 Pa`；组件源码默认 `_E=1e4, _nu=0.3, p_rho=1000`，具体 Unity 场景还可覆盖它们。ρ 的场景单位与作者片段取值待核实。[论文实验设置](https://arxiv.org/html/2412.00814v5)。

**演示证据：代码支持调参，效果待复跑。** 已看的压球片段属于塑形操作；其 E/ν/ρ 数值以及是否控制变量比较尚待核实。AI 建议：要展示 E，使用相同外力加载，或固定手/工具轨迹后观察自由区域与释放响应，并保持塑性设置；要展示弹性回弹，另选代码的 elastic 模式作为新的展示场景。

**代码、数据、依赖、渲染。** [Apache-2.0 主仓库](https://github.com/Simulation-Intelligence/VR-Doh)提供预配置 Unity 场景和默认对象、Taichi AOT、Marching Cubes/3DGS 渲染。作者交互路线测试于 Meta Quest 3，要求 Unity 与 Quest Link；这是明确的商业宿主/设备依赖。独立离线求解和免头显运行路线待核实，故本阶段优先保留视频，复跑优先级低于开放命令行候选。

**AI 推荐。** 日常手感与塑形效果很直观；E/ν 辨识受手势轨迹、屈服应力共同影响，理论对照价值中等。

## 10. AnisoMPM / Ziran：肉、橙瓣与分层食物

**来源与覆盖。** [SIGGRAPH 2020 作者页](https://joshuahwolper.com/anisompm)的 Anisotropic Feast 明确标注生肉沿纹理撕裂、橙瓣径向撕裂、奶酪拉丝及东坡肉分层；[作者主片](https://www.youtube.com/watch?v=eXJi7pkUZn0)由该页直接链接。Dongpo Pork 图注比较正交各向异性、横观各向同性、各向同性三种情况；当前观看位置依作者图注，镜头时间待核查。

**参数控制方式：直接输入基础弹性物理量，叠加纤维/损伤控制。** E、ν、ρ **均已证实于源码**：[橙子例程](https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/examples/orange.h)果肉 `E=10000, ν=0.4, ρ=500`、皮层 `E=50000, ν=0.4, ρ=500`；[`pork.h`](https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/examples/pork.h)把 E 从命令行传入，ν=0.25、ρ=2。各例程单位/几何尺度关系待核实。纤维方向、强化与损伤阈值有独立输入，单个 E/ν 无法完整描述该异质各向异性场景。

**演示证据：代码支持调参，效果待复跑；已定位作者的参数扫值脚本。** [`anisofractureBatch3D.py`](https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/anisofractureBatch3D.py)的 parameterEffects 有 **E=1/10/100/500/1000**，同一分支固定 `fiberScale=10, percent=0.45, eta=0.1, residual=0.01`；[`paramEffects.h`](https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/examples/paramEffects.h)固定 ν=0.25、ρ=2。脚本证明现成比较配置可查，尚未把主视频某段与该组数值逐帧对应，因此保留“效果待复跑”分类。相同 batch 另有 hanging torus 密度组 `0.75/1/2/3/5/10/20/25/30`，固定 E=100、fiberScale=100；其完整固定条件仍需核对 torus 例程。

**代码、数据、依赖、渲染。** [公开仓库](https://github.com/penn-graphics-research/ziran2020)与[分项目说明](https://github.com/penn-graphics-research/ziran2020/blob/master/Projects/anisofracture/README.md)列出 1 个 2D、13 个 3D demo，含 orange、dongpoPork、cheese、fish，并提供作者 Data.zip 下载入口。数据链接本次只核实已公布，未下载；仓库根部未显式列 LICENSE，使用许可待核实。求解构建为 C++/Eigen/OpenVDB/TBB 等开放库；完整成片渲染流程待核实，可能需要另接渲染。

**既有同系候选。** [Ziran 2019](https://github.com/penn-graphics-research/ziran2019)公开 MIT 的 Silly Rubber / CD-MPM 参考实现、数据及复现说明，补充黏弹塑性和动态断裂。它与 Ziran 2020 的项目、许可和数据分开看待。

**AI 推荐。** 视觉合理性：肉、橙瓣撕裂的方向性非常容易讨论。E/ν 参数辨识度：主片的强烈差别常来自纤维与损伤，适合单列“材料结构参考”。若团队只输入各向同性 E/ν/ρ，先选弹性压缩候选，再把 AnisoMPM 作为覆盖缺口与扩展方向。

## 11. SoliDualSPHysics：2026 金属塑性和脆性破坏基准

**来源。** [论文](https://arxiv.org/abs/2602.15149)首发 2026-02、修订 2026-06，期刊记录为 Computer Physics Communications 327 (2026) 110257；[作者代码](https://github.com/naqibr/SoliDualSPHysics)。属于固体计算工程的一手补充，而非图形学会议论文。

**观察入口。** [§4.3.2 / Fig.39](https://arxiv.org/html/2602.15149v2)展示圆柱杆以初速度撞向约束底面后逐步蘑菇化的 0–70 μs 连续时刻图；§4.2 有动态裂纹分叉等基准。本文尚未找到作者动态视频入口，因此保留为论文/源码候选，观看优先级在前述动态候选之后。脆性板是否按真实玻璃材料标定待核实，现有覆盖记作“脆性材料”，玻璃仍保留缺口。

**参数控制方式：直接输入物理量。** E、ν、ρ **均已证实**，[`Taylor bar XML`](https://github.com/naqibr/SoliDualSPHysics/blob/main/SolidExamples/9_3D_Taylor_Bar_Impact/Solid_Case_Def.xml)为 `E=117 GPa, ν=0.35, ρ=8930 kg/m³`；模型还设屈服应力 `400 MPa`、硬化模量 `100 MPa`。杆半径 3.2 mm、长 32.4 mm、初速度 −227 m/s 由[论文 §4.3.2](https://arxiv.org/html/2602.15149v2#S4.SS3.SSS2)给出。

**演示证据：代码支持调参，效果待复跑。** 作者 Taylor bar 对比的是粒子间距 0.1/0.2/0.4 mm，材料参数固定；不能计作 E/ν/ρ 对比。AI 建议若选择它展示 E/ν，先用其自由振动梁/受控弹性例程；高速金属冲击更适合展示塑性与参考解。

**代码、数据、依赖、渲染。** LGPL-2.1-or-later 源码、CPU/GPU 构建和 9 组 SolidExamples XML 已公开；例程含几何、边界条件与运行脚本。依赖 C++/CUDA/OpenMP 等，后处理含 VTK 工具；商业仿真软件必需依赖在公开说明中未出现。成片渲染流程需另选或衔接。[README](https://github.com/naqibr/SoliDualSPHysics)、[SolidExamples](https://github.com/naqibr/SoliDualSPHysics/tree/main/SolidExamples)。

**AI 推荐。** 日常视觉直观性低于枕头/玩具；材料量纲与参考实验明确，理论对照价值较高。金属“压后不恢复”还涉及屈服与硬化，保留在 E/ν 弹性展示之外的一组。

## 同类与近期候选简表

下表保留有差异的后续入口，避免同类主条目重复展开；本次均未复跑。只有代码参数能力已明确者标为已证实，其余项留待选择后核查。

| 候选 / 时间 / 一手入口 | E、ν、ρ 与演示证据 | 代码、示例、商业依赖、渲染现状 | 保留理由与边界 |
| --- | --- | --- | --- |
| [Newton](https://github.com/newton-physics/newton)，持续更新；关联 [VBD，SIGGRAPH 2024](https://graphics.cs.utah.edu/research/projects/vbd/vbd-siggraph2024.pdf) | E/ν 可按 Lamé 公式换算到 `k_mu/k_lambda`，ρ 直接 density；[`softbody_hanging`](https://github.com/newton-physics/newton/blob/main/newton/examples/softbody/example_softbody_hanging.py)固定 `μ=λ=1e5,ρ=1000`，对比的是阻尼 `1e4/1e3/1e2/1e1`。代码支持调参、目标效果待复跑 | 官方代码/程序生成软梁例程与 viewer；Warp/GPU 路线；完整目标资产待核实，商业求解器依赖随选定 solver 核对 | AI：现代维护和软刚耦合有吸引力；避免把阻尼对比当成 E 对比。[ModelBuilder 文档](https://newton-physics.github.io/newton/latest/api/_generated/newton.ModelBuilder.html) |
| [MuJoCo deformables](https://mujoco.readthedocs.io/en/stable/XMLreference.html#flexcomp-elasticity)，当前文档 | elasticity 的 E/ν 字段可查；ρ 在 flexcomp 路线如何由质量/体积指定待核实；本次只列代码/文档能力，未看目标参数视频 | [官方代码与模型](https://github.com/google-deepmind/mujoco)公开，有 viewer；特定 deformable 示例和渲染输出待选后核对，商业软件必需依赖待逐路线确认 | AI：机器人互动备选；质量字段和几何体积需一起核实 |
| [StiffGIPC](https://github.com/KemengHuang/Stiff-GIPC)，TOG/SIGGRAPH 2025 | 面向刚/软混合与高刚度；E/ν/ρ 具体例程字段本次待核实；README 有动态 video 1，未逐帧核查 | CUDA 源码、Assets、OpenGL 查看器公开；README同时列 MPL-2.0 与商业用途需联系作者，许可关系待核；求解依赖为 CUDA/Eigen/GL 等 | AI：IPC 的 GPU/高刚度同类备选；不能仅据性能论文认定其参数展示更直观 |
| [Fracture-GS](https://proceedings.iclr.cc/paper_files/paper/2026/hash/3346f8d7c518513060e6ba087e0d5b7e-Abstract-Conference.html)，ICLR 2026 | 已证实物理 MPM 与 Gaussian 断裂渲染方法；E/ν/ρ 操作入口、扫值证据待核实 | 正式论文公开；作者代码、现成场景下载、动态入口、商业依赖均待定位；论文内有配套渲染方法 | AI：近期断裂方向线索，暂不排到已有动态和代码候选之前 |
| [Boba](https://jianxiapyh.github.io/Boba-project-page/)，ECCV 2026；由 [PhysTwin README](https://github.com/jianghanxiao/phystwin)于 2026-08-24 链出 | PhysTwin 同系批量仿真；E/ν/ρ 连续体物理映射待核实；仅列近期效果/性能方向 | 作者项目入口已确认；具体代码包、示例、许可与渲染复现完整性待核实 | AI：若用户喜欢 PhysTwin 玩具互动，再查此支线 |
| [Breaking Good](https://www.dgp.toronto.edu/projects/breaking-good/)，SIGGRAPH Asia 2022 | [作者视频 3:36 玻璃杯破碎](https://youtu.be/0k_tEk34nJQ?t=216)，时间来自[论文 Fig.19](https://www.dgp.toronto.edu/projects/breaking-good/fracture-harmonics.pdf)，本次未实际看该段。E/ν/ρ 控制均待核；仅有视觉效果参考 | [源码](https://github.com/sgsellan/fracture-modes)含兔子示例、GUI 与 OBJ 输出；需 MOSEK 许可（商业优化器、学术免费），主代码许可限学术非商业。玻璃杯资产与成片渲染链待核 | AI：补上具名玻璃的破碎观看入口；预计算破碎模式与刚体碎片用于破坏动画，优先作为效果参考 |

## 材料与物件覆盖及缺口

| 工单对象 | 当前有证据的覆盖 | 尚待补齐的目标 |
| --- | --- | --- |
| 玻璃 | Breaking Good 作者论文明确玻璃杯破碎并提供视频定位；SoliDualSPHysics 提供脆性裂纹基准；Fracture-GS 为近期破坏方向 | **玻璃的 E/ν/ρ 公开材料常数、控制变量动态演示与开放复跑路线仍待补齐。** Breaking Good 补足破碎视觉参考，弹性阶段的 E/ν 肉眼辨识场景仍待选 |
| 金属 | PhysGaussian 罐体压皱；SoliDualSPHysics Taylor bar 塑性冲击 | 原金属视频的 E/ν 控制变量对比待定位；塑性屈服/硬化与 E 的作用需分开 |
| 塑料 | SoftMAC plasticine、VR-Doh 塑形材料；Genesis 弹塑性球 | plasticine 对应塑形材料；普通塑料瓶、硬塑料玩具的薄壳屈曲及厚度数据待壳类工单补齐 |
| 果冻 | PhysGaussian 的 jelly 材料、IPC/Genesis/MiNNIE 软体块 | 带真实果冻几何与已知材料值的桌面晃动/挤压视频待补。把软体块看作果冻是 AI 场景设想 |
| 肉 | AnisoMPM 作者明确生肉、东坡肉，含方向性撕裂 | 单独 E/ν/ρ 对比、组织层次与各向同性假设的适配待核；弹性按压会比撕裂更贴近当前输入 |
| 水果 | FreeForm 葡萄落地；AnisoMPM 橙瓣撕裂 | 果皮/果肉分层与受控挤压；真实果实参数及视频扫值待核 |
| 家具 | PhysGaussian 沙发与坐垫接触 | 木/金属家具结构弯曲的物理参数视频待补，沙发各部分是否都可形变待核 |
| 玩具 | FreeForm 狗及其他玩偶；PhysTwin 树懒/斑马；DiffPD 软兔 | 硬塑料玩具、充气玩具及不同结构类别需分开；真实材质映射待核 |
| 枕头 | PhysGaussian pillow2sofa 现成视频和 JSON | 外皮与填充物的独立结构/参数待核；等效实体 jelly 与真实枕芯的对应性属于 AI 简化设想 |

## 已有参数对比与后续选择的交接

| 证据类型 | 已核实内容 | 固定条件与剩余问题 |
| --- | --- | --- |
| **视频已展示参数对比** | PhysGaussian 材料球的 E/ν 增减方向与压缩结果 | 数值/倍率、ρ、全部固定条件待定位；空壳/实心另是一条变化轴 |
| **已有扫值代码，效果待复跑** | AnisoMPM E=1/10/100/500/1000；ν=.25、ρ=2，纤维/损伤分支固定项见第 10 节 | 参数效应视频与脚本对应关系待核；这组本身包含损伤模型，结果解释需同时说明 |
| **单组参数配置** | FreeForm 梁、PhysGaussian 枕头、IPC Squeeze out、DiffPD 梁、MiNNIE 场景、Genesis MPM、SoftMAC grip、VR-Doh 组件/论文、SoliDualSPHysics Taylor bar | 用作复跑起点；不同场景/材料模型之间的数值差异不算控制变量比较 |
| **其他量的比较** | SoftMAC 轨迹优化、MiNNIE/FreeForm 求解方法、Newton 阻尼、SoliDualSPHysics 粒子分辨率、AnisoMPM 各向异性 | 这些演示各有价值，目标 E/ν/ρ 比较需要另行形成 |

**AI 建议的人工选择顺序：** 先从 PhysGaussian 枕头、FreeForm 玩具中选喜欢的日常效果；再选 IPC/DiffPD 的受控弹性场景承担 E，MiNNIE/IPC 的挤压场景承担 ν；ρ 留给独立密度工单。PhysTwin 作为毛绒互动参照；AnisoMPM、VR-Doh 与金属/脆性破坏按用户对撕裂、塑形的兴趣继续。选中以后才核实完整资产、量纲、示例环境并复跑，生成实际取值和固定条件清楚的结果视频。
