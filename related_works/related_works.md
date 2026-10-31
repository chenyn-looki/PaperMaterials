# Related Works

## 0. Our Settings

### 主要信息

本研究提供真实日常生活中的第一／第三视角视听数据集，包含训练监督和人工标注／修订的测试子集，并研究基于这些数据的post-training。

- L0: activity
- L1: stage

| 项目 | 设定 |
|---|---|
| 数据来源 | 日常生活的真实录制视频；保留原始声音、clip顺序与时间戳。 |
| 输入单位 | 同一用户、同一天内按时间排列的一组离散视听clips；clip之间存在自然录制间隙。训练窗口15–40 clips，评测选窗10–40 clips。 |
| 任务 | Hierarchical audiovisual captioning：联合预测L0活动实例、L1功能阶段的区间、父子关系与简短caption。 |
| 层级定义 | L0表示一次活动实例，彼此不重叠；L1描述活动内部的重要阶段，按clip顺序覆盖父活动。无法进一步细分时，以一个L1保留父活动的范围与描述。 |
| 数据规模 | 截至2026-10-31，训练标签池8,758窗口；评测标注1,132窗口，含3,635个L0、6,379个L1。 |
| 标注 | 训练标签由Gemini生成；测试标签经人工标注／修订，原1,132窗中213窗具有逐条复核记录。英文caption由中文标注翻译并抽查。 |
| 实验划分 | 评测标注中100窗用于GRPO，余1,032窗测试；测试集含3,183个L0、5,436个L1。现有SFT基线使用3,020个训练窗口。 |
| 模型输入与学习 | 默认每clip取第三帧及完整音频；以Qwen2.5-Omni为基座，比较普通SFT、分结构／caption loss的SFT及后续GRPO。 |
| 评价 | 基于真实时间tIoU匹配L0，再在对应父活动内匹配L1；分别报告分段、caption及联合P/R/F1。时间跨度包含录制间隙。 |

[数据集对比表](dataset_benchmark_comparison.md)

### Eval Metrics



## 1. Omni Models

Omni模型联合处理视觉、声音与语言，为视听活动理解提供基础。

- **开源模型主要参考**：[Qwen2.5-Omni](https://arxiv.org/abs/2503.20215)、[Qwen3-Omni](https://arxiv.org/abs/2509.17765)、MiniCPM-o-2.6、[OmniVinci](https://arxiv.org/abs/2510.15870)。
- **闭源模型主要使用**：Gemini 3 Flash、Gemini 3.1 Pro、Gemini 3.5 Flash。

## 2. Everyday and Egocentric Datasets

相关数据覆盖自然生活、程序性操作和技能活动，提供不同视角与时间粒度的标注。

| 工作 | 数据与任务 | 与本文的关系 | 论文与资源 |
|---|---|---|---|
| **Ego4D** · CVPR 2022 | 3,670小时、931名佩戴者；日常第一视角视频、narration及多任务标注。 | 同属日常活动理解；本文以活动与阶段的区间caption为目标。 | [论文](https://arxiv.org/abs/2110.07058)；[PDF](pdfs/ego4d_2110.07058.pdf)；[官方文档](https://ego4d-data.org/docs/) |
| **Ego-Exo4D** · CVPR 2024 | 同步第一／第三视角技能活动，含音频、keysteps及专家讲解；多视角合计1,286小时。 | 同步多视角的技能记录；本文涵盖日常活动的断续记录。 | [论文](https://arxiv.org/abs/2311.18259)；[PDF](pdfs/ego_exo4d_2311.18259.pdf)；[官方文档](https://docs.ego-exo4d-data.org/overview/) |
| **EgoLife** · CVPR 2025 | 6人、7天、约300小时；361K细粒度narrations、25K合并描述及3K记忆QA。 | 已有两级描述和采集间断；评测侧重长期记忆QA。 | [论文](https://arxiv.org/abs/2503.03803)；[PDF](pdfs/egolife_2503.03803.pdf)；[代码](https://github.com/EvolvingLMMs-Lab/EgoLife)；[数据](https://huggingface.co/datasets/lmms-lab/EgoLife) |
| **HD-EPIC** · CVPR 2025 | 41小时家庭厨房记录；recipe steps、细动作及50,968个声音事件。 | 厨房流程与声音标注；本文覆盖更广泛的生活活动。 | [论文](https://arxiv.org/abs/2502.04144)；[PDF](pdfs/hd_epic_2502.04144.pdf)；[项目](https://hd-epic.github.io/site/)；[标注仓库](https://github.com/hd-epic/hd-epic-annotations) |
| **Ego4D Goal-Step** · NeurIPS 2023 | Goal→step→substep；2,807小时goal标注，层级子集851视频／368小时。 | 功能目的导向的程序层级；本文包含非程序性日常活动。 | [正式论文页](https://papers.nips.cc/paper_files/paper/2023/hash/7a65606fa1a6849450550325832036e5-Abstract-Datasets_and_Benchmarks.html)；[PDF](pdfs/goalstep_3BxYAaovKr.pdf)；[代码／数据入口](https://github.com/facebookresearch/ego4d-goalstep) |
| **EgoAVU** · 2026 | 约3M训练QA、3K人工核验QA，含视听narration与声源关系任务。 | 提供第一视角AV训练监督，主要输出为narration和QA。 | [论文](https://arxiv.org/abs/2602.06139)；[PDF](pdfs/egoavu_2602.06139.pdf)；[代码](https://github.com/facebookresearch/EgoAVU)；[数据](https://huggingface.co/datasets/facebook/EgoAVU_data) |
| **HoloAssist** · ICCV 2023 | 166小时协作操作；7路同步数据、动作和对话标注。 | 指导式物体操作，任务包括错误检测与干预预测。 | [论文](https://arxiv.org/abs/2309.17024)；[项目](https://holoassist.github.io/) |
| **Nymeria** · 2024 | 300小时、264人、1,200段；人工标注motion、action、activity三种粒度的区间描述。 | 以人体运动为中心的多粒度叙述；本文组织活动实例及其内部阶段。 | [论文](https://arxiv.org/abs/2406.09905)；[PDF](pdfs/nymeria_2406.09905.pdf)；[官方资源](https://github.com/facebookresearch/nymeria_dataset) |
| **NymeriaPlus** · 2026 | 扩充Nymeria，公开参与者及observer视角的600余小时音频。 | 为原多粒度叙述提供音频资源；原叙述主要描述动作。 | [论文](https://arxiv.org/abs/2603.18496)；[PDF](pdfs/nymeriaplus_2603.18496.pdf)；[发布仓库](https://github.com/facebookresearch/nymeria_dataset) |
| **EgoTools** · 2026 | 100.37小时；5秒→1分钟→5分钟描述，184,679训练例及1,000题QA。 | 按固定时长聚合caption，测试侧重工具推理。 | [论文](https://arxiv.org/abs/2609.39378)；[PDF](pdfs/egotools_2609.39378.pdf)；[代码](https://github.com/Ropedia/EgoTools)；[数据说明](https://github.com/Ropedia/EgoTools/blob/main/docs/DATA.md) |

## 3. Omni Captioning

### 3.1 视听描述与Post-training

| 工作 | 数据与任务 | 与本文的关系 | 论文与资源 |
|---|---|---|---|
| **VALOR** · 2023/2024 | 视听语言对齐、条件文本生成及AV caption数据。 | 学习片段级视听描述。 | [论文](https://arxiv.org/abs/2304.08345)；[代码／数据](https://github.com/CASIA-IVA-Lab/VALOR) |
| **VAST** · NeurIPS 2023 | 27M clips；视觉、音频描述与字幕融合为训练caption。 | 大规模自动构造视听描述监督。 | [论文](https://arxiv.org/abs/2305.18500)；[PDF](pdfs/vast_2305.18500.pdf)；[代码／数据](https://github.com/CASIA-IVA-Lab/VAST) |
| **AVCaps** · 2025 | 2,061 clips；分别提供audio、visual和AV人工caption。 | 提供模态独立及联合描述，便于比较音画信息。 | [作者论文](https://researchportal.tuni.fi/files/151635043/AVCaps_An_Audio-Visual_Dataset_With_Modality-Specific_Captions.pdf)；[数据卡](https://huggingface.co/datasets/TUT-ARG/AVCaps) |
| **video-SALMONN 2** · 2025 | Caption训练与多轮DPO；483视频人工AV caption测试集。 | 通过描述训练提升视听理解，评估内容遗漏与幻觉。 | [论文](https://arxiv.org/abs/2506.15220)；[PDF](pdfs/video_salmonn2_2506.15220.pdf)；[代码](https://github.com/bytedance/video-SALMONN-2)；[测试集](https://huggingface.co/datasets/videoSALMONN2/video-SALMONN_2_testset) |
| **UGC-VideoCaptioner** · 2025 | 1,000个TikTok评测视频；分模态及联合标注，模型采用SFT＋GRPO。 | 短视频的整体视听细节描述。 | [论文](https://arxiv.org/abs/2507.11336)；[评测数据](https://huggingface.co/datasets/openinterx/UGC-VideoCap) |
| **AVoCaDO** · ICLR 2026 | 107K AV caption训练对；2K用于GRPO，奖励内容覆盖、对话准确性和长度。 | 视听caption的SFT＋GRPO；本文学习活动／阶段区间与描述。 | [论文](https://arxiv.org/abs/2510.10395)；[PDF](pdfs/avocado_2510.10395.pdf)；[正式出版页](https://proceedings.iclr.cc/paper_files/paper/2026/hash/29c51fb5618fb34eaf43781e6da07d97-Abstract-Conference.html)；[代码](https://github.com/AVoCaDO-Captioner/AVoCaDO) |
| **ASID-Captioner** · 2026 | 121K视频、约1M指令；单属性及综合描述，经自动验证后用于SFT。 | 按内容属性组织监督；本文按活动／阶段组织时间区间。 | [论文](https://arxiv.org/abs/2602.13013)；[PDF](pdfs/asid_2602.13013.pdf)；[代码](https://github.com/HVision-NKU/ASID-Caption)；[数据](https://huggingface.co/datasets/AudioVisual-Caption/ASID-1M) |
| **AVSCap** · 2026 | 130K训练语料，SFT＋GRPO；1,226视频的AVSCapBench。 | 优化声音覆盖与跨模态事件联系。 | [论文](https://arxiv.org/abs/2607.12820)；[PDF](pdfs/avscap_2607.12820.pdf)；[代码](https://github.com/NJU-LINK/AVSCap)；[评测集](https://huggingface.co/datasets/NJU-LINK/AVSCapBench) |
| **AVCap** · 2026 | AVCap-100K含预留1K测试；主SFT使用60K，Da-GRPO奖励原子细节。 | 以细节覆盖优化联合视听描述。 | [论文](https://arxiv.org/abs/2608.06930)；[PDF](pdfs/avcap_2608.06930.pdf)；[作者数据／模型合集](https://huggingface.co/collections/Apryle/avcap) |
| **EVA-Cap** · 2026 | 63K训练数据；SFT＋GRPO优化事件完整性、归属及音画同步。 | 按原子事件优化caption；本文同时学习层级区间。 | [论文](https://arxiv.org/abs/2610.22186)；[PDF](pdfs/eva_cap_2610.22186.pdf)；[代码](https://github.com/OPPO-Mente-Lab/EVA-Cap) |
| **Script-a-Video** · 2026 | Reference／Shot／Event／Global多流，通过身份与时间关系连接；包含SFT实验。 | 结构化视听叙述；本文采用活动—阶段的时间层级。 | [论文](https://arxiv.org/abs/2604.11244)；[PDF](pdfs/script_a_video_2604.11244.pdf) |

### 3.2 时间定位与分层描述

| 工作 | 数据与任务 | 与本文的关系 | 论文与资源 |
|---|---|---|---|
| **ActivityNet Captions** · ICCV 2017 | 多个事件的时间区间与自然语言描述。 | 单层dense captioning的数据与任务基础。 | [作者项目](https://cs.stanford.edu/people/ranjaykrishna/densevid/) |
| **PDVC** · ICCV 2021 | 端到端预测事件数、时间区间与caption。 | 联合学习定位与描述。 | [论文](https://arxiv.org/abs/2108.07781) |
| **Vid2Seq** · CVPR 2023 | 联合生成时间与文本token，使用转写句子及边界作弱监督。 | 从大规模弱监督学习区间caption。 | [论文](https://arxiv.org/abs/2302.14115)；[作者项目](https://antoyang.github.io/vid2seq.html) |
| **LongVALE** · CVPR 2025 | 7,240训练／1,171测试视频，105,730事件；自动AV标注与人工检查测试集。 | 单层AV事件定位与描述；本文输出嵌套活动／阶段。 | [论文](https://arxiv.org/abs/2411.19772)；[PDF](pdfs/longvale_2411.19772.pdf)；[代码](https://github.com/ttgeng233/LongVALE)；[数据及权重](https://huggingface.co/datasets/ttgeng233/LongVALE/tree/main) |
| **TimeChat-Captioner** · ICML 2026 | TimeChatCap-42K：40K SFT＋2K GRPO；OmniDCBench：1,122人工标注视频。 | 场景区间＋六维内容描述；本文按活动实例及功能阶段划分。 | [论文](https://arxiv.org/abs/2602.08711)；[PDF](pdfs/timechat_captioner_2602.08711.pdf)；[代码](https://github.com/yaolinli/TimeChat-Captioner)；[训练集](https://huggingface.co/datasets/yaolily/Timechat-OmniCaptioner-42K)；[测试集](https://huggingface.co/datasets/yaolily/OmniDCBench) |
| **PadCaptioner** · 2026 | 全局事件规划与并行解码，生成带时间范围的AV描述。 | 优化dense captioning的生成效率与事件组织。 | [论文](https://arxiv.org/abs/2607.02963)；[PDF](pdfs/padcaptioner_2607.02963.pdf)；[代码](https://github.com/showlab/PadCaptioner) |
| **Video ReCap／Ego4D-HCap** · CVPR 2024 | Clip→segment→video三层描述，新增8,267条长视频summary。 | 在给定时间尺度上递归描述；本文预测活动／阶段边界。 | [论文](https://arxiv.org/abs/2402.13250)；[PDF](pdfs/video_recap_2402.13250.pdf)；[代码](https://github.com/md-mohaiminul/VideoRecap)；[数据说明](https://github.com/md-mohaiminul/VideoRecap/blob/master/datasets.md) |
| **HiREST** · CVPR 2023 | 约1.1K细标视频、8.6K step captions；查询→moment→steps，使用视觉与ASR。 | 查询条件的教学步骤描述；本文从整窗观察恢复活动结构。 | [论文](https://arxiv.org/abs/2303.16406)；[PDF](pdfs/hirest_2303.16406.pdf)；[代码／数据](https://github.com/j-min/HiREST) |
| **OpenHOUSE** · ICCV 2025 | 在线预测step／substep边界及描述，另生成整段goal描述。 | 层级定位与自由描述；本文处理有限窗口内的断续观察。 | [论文](https://arxiv.org/abs/2509.12145)；[PDF](pdfs/openhouse_2509.12145.pdf)；[项目](https://sites.google.com/view/yunsupark/projects/openhouse) |
| **HiVid-Narrator** · CVPR 2026 Findings | 电商视频的事件描述与章节摘要，使用ASR引导的层级压缩。 | 事件—章节叙述；本文描述日常活动及功能阶段。 | [论文](https://arxiv.org/abs/2601.07366)；[PDF](pdfs/hivid_narrator_2601.07366.pdf)；[正式出版页](https://openaccess.thecvf.com/content/CVPR2026F/html/Li_HiVid-Narrator_Hierarchical_Video_Narrative_Generation_with_Scene-Primed_ASR-anchored_Compression_CVPRF_2026_paper.html) |
| **Stay in your Lane** · 2026 | 分离定位与caption查询，通过对齐和重叠抑制联合学习。 | 研究定位／描述的任务交互；本文采用分组loss优化。 | [论文](https://arxiv.org/abs/2603.11439)；[PDF](pdfs/stay_in_your_lane_2603.11439.pdf)；[代码](https://github.com/MMAI-Konkuk/ROS-DVC) |

### 3.3 外部评测

可能会考虑作为post-training model进行cross-domain的几个benchmark。

| benchmark | 任务与规模 | 来源 |
|---|---|---|
| **Daily-Omni** | 684视频、1,197题视听QA；AVoCaDO采用caption→QA评估描述的信息覆盖。 | [论文](https://arxiv.org/abs/2505.17862)；[PDF](pdfs/daily_omni_2505.17862.pdf)；[数据](https://huggingface.co/datasets/liarliar/Daily-Omni) |
| **OmniBench** | 图像、音频与文本联合问答；另提供OmniInstruct训练资源。 | [论文](https://arxiv.org/abs/2409.15272)；[项目](https://m-a-p.ai/OmniBench/) |
| **WorldSense** | 1,662视频、3,172题视听理解QA。 | [论文](https://arxiv.org/abs/2502.04326)；[项目](https://jaaackhongggg.github.io/WorldSense/) |
