# Dataset & Benchmark Comparison

## 0. Our Settings

本文数据集来自真实日常第一／第三视角视听记录，输入为带自然录制间隔的 clips，输出为 L0 活动与 L1 功能阶段的时间区间及 caption。训练监督采用教师生成标签，评测子集包含人工标注／修订，完整任务定义见 [Our Settings](related_works.md#0-our-settings)。截至2026-10-31，成功标签池为8,758窗，已训练英文基线使用3,020窗；原1,132窗评测子集划分为100窗 GRPO 与1,032窗测试。

## Dataset Comparison

| Type | Dataset & Benchmark | Domain / View | Annotations / Audio | Temporal structure | Scale |
|---|---|---|---|---|---|
| Train | [VAST-27M](pdfs/vast_2305.18500.pdf) | Web clips / mixed views | Generated visual/audio captions + subtitles | Clip-level AV captions | 27M clips |
| Train | [ASID-1M](pdfs/asid_2602.13013.pdf) | Multi-source video | Generated, automatically verified AV instructions | 8 attribute-specific + 1 overall description | ≈121K videos; ≈1M instructions |
| Train | [TimeChatCap-42K](pdfs/timechat_captioner_2602.08711.pdf) | Film and web video | Generated scene boundaries and AV captions | Flat scenes; 6 caption fields | 42K samples: 40K SFT + 2K GRPO |
| **Train** | **Ours: training corpus** | **Everyday recordings / ego + exo** | **Teacher-generated activity/stage labels** | **Intermittent clips; L0 activity → L1 functional stages; intervals + captions** | **8,758 labelled windows for now, and will be ended up as 10k†; 3,020 used for baseline training** |
| Corpus + tasks | [Ego4D](pdfs/ego4d_2110.07058.pdf) | Everyday life / ego | Human narrations and task labels; partial audio | Multiple task-specific temporal targets | 3,670 video h, paper v3 |
| Corpus + tasks | [Ego-Exo4D](pdfs/ego_exo4d_2311.18259.pdf) | Skilled activities / synchronized ego + exo | Time-aligned language and keysteps; synchronized audio | Skill/task segments; synchronized views | 1,286 video h across views, paper v4 |
| Train + Eval | [Ego4D Goal-Step](pdfs/goalstep_3BxYAaovKr.pdf) | Procedural activities / ego | Human temporal labels with taxonomy assistance | Goals → steps → substeps | Goals: 2,807 h; hierarchy: 851 videos / 368 h |
| Train + Eval | [Nymeria + NymeriaPlus](pdfs/nymeria_2406.09905.pdf) | Scenario-prompted everyday motion / ego + observer | Human motion/action/activity descriptions; Plus raw audio | Three temporal granularities | 300 h / 1,200 sequences; Plus >600 audio h across views‡ |
| Train + Eval | [EgoLife](pdfs/egolife_2503.03803.pdf) | Multi-day shared life / ego + fixed cameras | Human narrations; model-merged text; human-checked AV captions | Fine-grained → integrated descriptions; long-term QA | ≈300 h; ≈361K fine / 25K merged descriptions; 3K QA |
| Train + Eval | [EgoTools](pdfs/egotools_2609.39378.pdf) | Everyday tool use / ego | Generated AV captions; verified QA | Fixed 5 s → 1 min → 5 min aggregation; tool QA | 646 episodes / 100.37 h; 184,679 training examples / 1,000 QA§ |
| Train + Eval | [Ego4D-HCap / Video ReCap](pdfs/video_recap_2402.13250.pdf) | Ego4D everyday videos / ego | Human descriptions; training pseudo-labels; RGB/text model | Clip captions → segment descriptions → video summaries | 8,267 long-video summaries; train/val annotations |
| Train + Eval | [HiREST](pdfs/hirest_2303.16406.pdf) | Instructional web videos | Human moment/step labels; ASR and audio-feature baselines | Query-conditioned moment → captioned steps | ≈1.1K finely labelled videos; ≈8.6K step captions |
| Train + Eval | [LongVALE](pdfs/longvale_2411.19772.pdf) | Long YouTube videos | Generated AV labels; manually corrected test labels | Flat event intervals + AV captions | 7,240 train / 1,171 test videos; 105,730 events |
| Train + Eval | [EgoAVU-Instruct / Bench](pdfs/egoavu_2602.06139.pdf) | Egocentric AV videos | Generated training QA; human-verified test QA | Narration, sound-source relation and temporal QA | ≈3M train QA / 3K test QA; 900 test videos |
| Eval | [HD-EPIC](pdfs/hd_epic_2502.04144.pdf) | Unscripted home kitchens / ego | Actions, recipe structure and sound-event labels | Recipe/preparation relationships; fine actions | 41 h; 59,454 actions; 50,968 sound events |
| Eval | [OmniDCBench](pdfs/timechat_captioner_2602.08711.pdf) | Film and web video | Human scene intervals and AV captions | Flat scenes; 6 caption fields | 1,122 videos |
| Eval | [Daily-Omni](pdfs/daily_omni_2505.17862.pdf) | Short AV videos | Model-assisted, human-reviewed QA | Temporal AV reasoning QA | 684 videos / 1,197 questions |
| **Eval** | **Ours: evaluation subset** | **Everyday recordings / ego + exo** | **Human annotation/refinement¶** | **L0 activity → L1 functional stages; intervals + captions** | **1,132 windows: 100 GRPO + 1,032 test** |

**Table caption.** Comparison of datasets for everyday activity understanding and audiovisual captioning. Temporal structure distinguishes activity–stage hierarchies, fixed-duration aggregation, flat events, and QA targets. Scale uses each resource's native unit. Our training and evaluation subsets share the same activity–stage interval-caption target.

- **计量单位：**视频、窗口、指令和问题分别计数；视频小时、跨视角累计小时与层级区间累计小时分别统计。Goal-Step的368 h为层级子集源视频时长，430 h为step/substep区间累计量。
- **音频监督：**原始音轨、ASR、声音事件标签和AV caption分别对应不同监督信息，表中按具体形式列出。
- **† 训练规模：**8,758窗为2026-10-31成功标签池快照，其中八月5,933窗、九月2,825窗；英文基线训练使用其中3,020窗。
- **‡ Nymeria版本：**原论文v2为300 h／1,200 sequences；[发布仓库](https://github.com/facebookresearch/nymeria_dataset)列出1,100 sequences。[NymeriaPlus](pdfs/nymeriaplus_2603.18496.pdf)的>600 h为参与者及observer的累计原始音轨时长。
- **§ EgoTools规模：**表中为论文规模；[发布说明](https://github.com/Ropedia/EgoTools/blob/main/docs/DATA.md)截至2026-10-02列出131个公开episodes。
- **¶ 人工覆盖：**1,132窗中213窗具有逐条审核记录，GRPO100中19窗、test1032中194窗。
