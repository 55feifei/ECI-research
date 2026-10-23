# Debate with Myself：零样本事件因果关系识别（逐段中英对照）

> **英文题目**：Debate with Myself: Zero-Shot Event Causality Identification with Adversarial Evidence Integration via Large Language Models
> **中文题目**：与自己辩论：借助大语言模型的对抗式证据集成实现零样本事件因果关系识别

**文档说明**

1. 本文档按论文原文的段落顺序，逐段给出「英文原文 + 中文译文」对照，便于逐句比对阅读。
2. 正文各章节、图题、表题与表格内容均给出译文；数学公式保持原式，并在公式后以文字说明其含义。
3. 原文由 PDF 自动转换而来，个别 OCR 噪声（多余的下标标记、断词、错字）已在不改变语义的前提下做了规范化；英文原文中的笔误（如 4.1 节标题 "Reasoning Phrases" 应为 "Reasoning Phases"）保留原样并在译注中说明。
4. 参考文献条目属于书目信息，按学术惯例保留原文，不在正文中逐条翻译。

**目录**

- 摘要（Abstract）
- 1 引言（Introduction）
- 2 相关工作（Related Works）
- 3 预备知识（Preliminaries）
- 4 方法（Methodology）
- 5 实验（Experiments）
- 6 结论（Conclusion）
- 致谢（Acknowledgments）
- 参考文献说明

---

## 标题与作者信息

**EN** Debate with Myself: Zero-Shot Event Causality Identification with Adversarial Evidence Integration via Large Language Models

**中文** 与自己辩论：借助大语言模型的对抗式证据集成实现零样本事件因果关系识别

**EN** Zefan Zeng¹, Yuehang Si¹, Xingchen Hu², Qing Cheng¹, Jiakun Liu³\*, and Zhong Liu¹

**中文** Zefan Zeng¹，Yuehang Si¹，Xingchen Hu²，Qing Cheng¹，Jiakun Liu³\*，Zhong Liu¹

**EN** ¹ Laboratory for Big Data and Decision, National University of Defense Technology; ² College of Computer Science and Technology, National University of Defense Technology; ³ Faculty of Computing, Harbin Institute of Technology. Emails: {zengzefan, siyuehang}@nudt.edu.cn, xhu4@ualberta.ca, chengqing@nudt.edu.cn, jiakunliu@hit.edu.cn, liuzhong@nudt.edu.cn

**中文** ¹ 国防科技大学大数据与决策实验室；² 国防科技大学计算机学院；³ 哈尔滨工业大学计算学部。邮箱：{zengzefan, siyuehang}@nudt.edu.cn，xhu4@ualberta.ca，chengqing@nudt.edu.cn，jiakunliu@hit.edu.cn，liuzhong@nudt.edu.cn

---

## 摘要（Abstract）

**EN** Event Causality Identification (ECI) is a crucial task in knowledge discovery that extracts structured causal relationships between annotated event mentions from unstructured text. However, existing approaches typically rely on extensive labeled data, which is scarce for specialized domains and topics. Although Large Language Models (LLMs) show strong promise for few-shot and zero-shot information extraction, they are prone to "causal hallucination," generating unreliable and spurious causal links.

**中文** 事件因果关系识别（Event Causality Identification，ECI）是知识发现中的一项关键任务，旨在从非结构化文本中抽取已标注事件提及（event mentions）之间的结构化因果关系。然而，现有方法通常依赖大量标注数据，而这类数据在专业领域与特定主题中十分稀缺。尽管大语言模型（Large Language Models，LLMs）在少样本与零样本信息抽取中展现出很强的潜力，但它们容易产生「因果幻觉」（causal hallucination），生成不可靠的、虚假的因果关联。

**EN** To address these limitations, we propose LLM-SD (Large Language Model Self-Debate), a novel framework that formulates ECI as a structured debate among multiple identical instances of a single LLM. Causality is determined through the integration of adversarial evidence. The framework employs LLMs in distinct roles: an affirmative team argues for the existence of causality, a negative team argues against it, and an adjudication committee evaluates the evidence for determination.

**中文** 为应对上述局限，我们提出 LLM-SD（Large Language Model Self-Debate，大语言模型自我辩论），这是一个新颖的框架，将 ECI 建模为同一 LLM 的多个相同实例之间的结构化辩论，并通过对抗式证据的集成来判定因果关系。该框架让 LLM 承担不同角色：正方团队论证因果关系存在，反方团队论证其不存在，裁决委员会则评估证据以作出判定。

**EN** An evidence strength grading rule guides the quantification and integration of adversarial evidence. The automatic and LLM-driven verification finally produce a reasoned verdict for event causality. LLM-SD reduces spurious causal links resulting from causal hallucination in LLMs and identifies more long-distance causalities by promoting a balanced evaluation of arguments. Extensive experiments on three benchmark datasets demonstrate that LLM-SD achieves state-of-the-art performance in a zero-shot setting.

**中文** 一套证据强度分级规则指导对抗式证据的量化与集成，自动验证与 LLM 驱动的验证最终给出有理有据的事件因果裁决。LLM-SD 减少了由 LLM 因果幻觉产生的虚假因果关联，并通过促进对论点的均衡评估识别出更多长距离因果关系。在三个基准数据集上的大量实验表明，LLM-SD 在零样本设定下取得了最先进的性能。

---

## 1 引言（Introduction）

**EN** Event Causality Identification (ECI) is a critical task in Natural Language Processing (NLP) that systematically identifies and validates causal relationships among events in unstructured text, facilitating automated extraction of structured knowledge [Cheng et al., 2025]. Revealing causalities between events in text is of significant importance for advancing automated knowledge extraction and decision-making processes in complex domains, such as military intelligence [Cockburn, 2010], scientific research [Gopalakrishnan et al., 2025], and social development [Radinsky et al., 2012]. In the ECI task, events are represented by their triggers, referred to as "event mentions." Event causality in text is classified into two types according to the distribution of event mentions: intra-sentence causality (or sentence-level causality), in which all related event mentions appear within a single sentence, and inter-sentence causality (or document-level causality), in which event mentions may span multiple sentences.

**中文** 事件因果关系识别（ECI）是自然语言处理（NLP）中的一项关键任务，它系统地识别并验证非结构化文本中事件之间的因果关系，从而促进结构化知识的自动抽取 [Cheng et al., 2025]。揭示文本中事件间的因果关系，对于推动复杂领域中的自动化知识抽取与决策过程具有重要意义，例如军事情报 [Cockburn, 2010]、科学研究 [Gopalakrishnan et al., 2025] 与社会发展 [Radinsky et al., 2012]。在 ECI 任务中，事件由其触发词（trigger）表示，称为「事件提及」（event mentions）。根据事件提及的分布，文本中的事件因果关系分为两类：句内因果关系（或称句子级因果关系），即所有相关事件提及都出现在同一个句子中；以及句间因果关系（或称文档级因果关系），即事件提及可能跨越多个句子。

**EN** Early ECI relied on lexical, temporal, and co-occurrence features [Chan et al., 1999; Mirza, 2014; Do et al., 2011]. With deep learning and Pre-trained Language Models (PLMs) [Devlin et al., 2019], performance improved through richer semantic representations [Zhao et al., 2024; Li et al., 2024; Hu et al., 2023; Kadowaki et al., 2019; Liu et al., 2021]. However, these supervised methods require large-scale annotated datasets, which are often inaccessible for domain-specific texts. Large Language Models (LLMs) exhibit strong few-shot and zero-shot capabilities [Kiciman et al., 2024], yet they face significant challenges in ECI tasks, primarily: (1) Causal hallucination, where LLMs systematically overestimate causal links due to architectural and pretraining biases [Gao et al., 2023]; and (2) Underestimation of long-distance causalities, as cross-sentence event pairs are often overlooked due to lexical distance and sequence modeling constraints. Figure 1 intuitively illustrates the two challenges mentioned above.

**中文** 早期 ECI 依赖词汇、时间与共现特征 [Chan et al., 1999; Mirza, 2014; Do et al., 2011]。随着深度学习与预训练语言模型（Pre-trained Language Models，PLMs）[Devlin et al., 2019] 的发展，更丰富的语义表示带来了性能提升 [Zhao et al., 2024; Li et al., 2024; Hu et al., 2023; Kadowaki et al., 2019; Liu et al., 2021]。然而，这些有监督方法需要大规模标注数据集，而这类数据对于领域特定文本往往难以获取。大语言模型（LLMs）展现出强大的少样本与零样本能力 [Kiciman et al., 2024]，但在 ECI 任务中面临重大挑战，主要包括：（1）因果幻觉，即由于架构与预训练偏差，LLM 系统性地高估因果关联 [Gao et al., 2023]；（2）低估长距离因果关系，即由于词汇距离与序列建模约束，跨句子事件对常常被忽略。图 1 直观展示了上述两个挑战。

**EN** We argue that zero-shot ECI should return to the essence of causality, making judgments based on definitions. Although predictions from an individual LLM may be unreliable, their reasoning exhibits consistent internal patterns [Wang et al., 2023], suggesting that systematic consensus from multiple perspectives can lead to more reliable causal inference [Cai et al., 2025]. Inspired by this, we propose LLM Self-Debate (LLM-SD). Unlike recent multi-agent methods that deploy different LLMs for the same task, our approach employs multiple identical LLM agents. Each agent is assigned a unique, non-overlapping role to evaluate a specific dimension of causality, with the final decision emerging from the adversarial integration of their heterogeneous evidence, not from model diversity. Specifically, the framework formalizes ECI as a structured debate process, consisting of affirmative and negative teams that collect multi-dimensional evidence, and an adjudication committee that evaluates the evidence to deliver a final verdict. This role-decomposed design is key to reducing causal hallucination while improving the recall of long-distance causal relations. It effectively mitigates spurious causal links and false negatives caused by single-evidence errors, as illustrated in Figure 2.

**中文** 我们认为，零样本 ECI 应当回归因果性的本质，基于定义作出判断。尽管单个 LLM 的预测可能不可靠，但其推理呈现出内在一致的模式 [Wang et al., 2023]，这表明来自多视角的系统性共识能够带来更可靠的因果推断 [Cai et al., 2025]。受此启发，我们提出 LLM 自我辩论（LLM Self-Debate，LLM-SD）。与近期将不同 LLM 部署于同一任务的多智能体方法不同，我们的方法采用多个相同的 LLM 智能体。每个智能体被赋予唯一且互不重叠的角色，用以评估因果性的某一特定维度；最终决策来自其异构证据的对抗式集成，而非模型多样性。具体而言，该框架将 ECI 形式化为一个结构化辩论过程，由收集多维证据的正方团队与反方团队，以及评估证据并给出最终裁决的裁决委员会组成。这种角色分解设计是减少因果幻觉、同时提升长距离因果关系召回率的关键。如图 2 所示，它有效缓解了单一证据错误所导致的虚假因果关联与漏检（假阴性）。

**EN** Figure 1: The primary challenges of directly applying LLMs to the ECI task.

**中文** 图 1：将 LLM 直接应用于 ECI 任务所面临的主要挑战。

![Figure 1](https://cdn-mineru.openxlab.org.cn/result/2026-10-21/e68f5f13-4fe0-45ab-ae61-772a152e88e7/220e3da8d23b97f51f42f9080bb01a9ea598ac03d28b98f845bcedcd85187cd8.jpg)

**EN** Our contributions are threefold:

**中文** 我们的贡献有三点：

**EN** • We systematically summarize prerequisite conditions for causal relations and their supporting/rebuttal evidence based on causality definitions, providing a comprehensive argumentative foundation for multi-evidence causal reasoning.

**中文** • 我们基于因果性定义，系统总结了因果关系的先决条件及其支持性/反驳性证据，为多证据因果推理提供了全面的论证基础。

**EN** • We innovatively reformulate ECI as an LLM debate, where identical LLMs act as debaters and adjudicators to present, evaluate, and adjudicate causal evidence.

**中文** • 我们创新性地将 ECI 重新表述为 LLM 辩论，由相同的 LLM 担任辩手与裁决者，对因果证据进行呈现、评估与裁决。

**EN** • Extensive experiments on three benchmarks show that LLM-SD achieves state-of-the-art zero-shot performance, with notable robustness in long-text and long-distance ECI tasks.

**中文** • 在三个基准上的大量实验表明，LLM-SD 取得了最先进的零样本性能，并在长文本与长距离 ECI 任务中具有显著的鲁棒性。

---

## 2 相关工作（Related Works）

### 2.1 事件因果关系识别（Event Causality Identification）

**EN** Early research on ECI primarily focused on sentence-level ECI (SECI) [Liu et al., 2023; Liu et al., 2021; Zuo et al., 2020]. However, document-level ECI (DECI) has recently gained significant attention and is an emerging research area. Current DECI strategies include: (1) Prompt-based fine-tuning models [Xiang et al., 2023; Yuan et al., 2023], which adapt pre-trained models through innovative prompt engineering. (2) Event graph reasoning methods [Liu et al., 2024; Chen et al., 2022], which model causality as graph inference problems using structured event representations. (3) Contextual encoding techniques [Man et al., 2024; Huang et al., 2024], which leverage deep learning architectures to capture semantic and contextual features. In the past two years, LLM-based zero-shot ECI models have been explored but are limited by causal hallucination. Models such as KnowQA [Wang et al., 2024] and Dr.ECI [Cai et al., 2025] proposed targeted solutions: the former utilizes event argument structures to enhance reasoning, while the latter addresses diverse causal structures by incorporating prior causal knowledge.

**中文** 早期 ECI 研究主要关注句子级 ECI（SECI）[Liu et al., 2023; Liu et al., 2021; Zuo et al., 2020]。然而，文档级 ECI（DECI）近来受到广泛关注，是一个新兴的研究方向。当前的 DECI 策略包括：（1）基于提示的微调模型 [Xiang et al., 2023; Yuan et al., 2023]，通过创新的提示工程适配预训练模型；（2）事件图推理方法 [Liu et al., 2024; Chen et al., 2022]，利用结构化事件表示将因果关系建模为图推理问题；（3）上下文编码技术 [Man et al., 2024; Huang et al., 2024]，利用深度学习架构捕获语义与上下文特征。过去两年间，研究者探索了基于 LLM 的零样本 ECI 模型，但这些模型受因果幻觉制约。KnowQA [Wang et al., 2024] 与 Dr.ECI [Cai et al., 2025] 等模型提出了针对性解决方案：前者利用事件论元结构增强推理，后者通过引入先验因果知识来应对多样的因果结构。

**EN** However, existing LLM-based ECI models oversimplify causality analysis by neglecting essential causal definitions and supporting/contradictory evidence, leading to persistent hallucination. Our LLM-SD mitigates this by simulating evidence-based human reasoning through LLMs' self-consistent causal understanding.

**中文** 然而，现有基于 LLM 的 ECI 模型忽视了因果性的基本定义以及支持性/矛盾性证据，使因果分析过于简化，导致幻觉持续存在。我们的 LLM-SD 借助 LLM 自洽的因果理解来模拟基于证据的人类推理，从而缓解这一问题。

### 2.2 多 LLM 智能体辩论（Multiple LLM Agents Debate）

**EN** Multiple LLM agents debating has emerged as a promising approach to enhance reasoning and factual accuracy by letting models challenge and refine each other's outputs. [Estornell and Liu, 2025] provide a formal framework for debate dynamics and propose interventions to overcome majority convergence. [Ku et al., 2025] demonstrate that structured two-agent debates can effectively recover implicit premises, outperforming single-agent models. MRBalance [Zou et al., 2025] employs multiple LLM agents in a joint question-answering framework for ECI. However, it mainly aggregates agents that repeatedly reason about causality at a coarse granularity. More broadly, most of current multi-agent LLM paradigms rely on heterogeneous agents (different models or differently tuned agents) to solve the same task instance, expecting diversity to improve accuracy.

**中文** 多 LLM 智能体辩论已成为一种颇具前景的方法，通过让模型相互质疑并改进彼此的输出，提升推理能力与事实准确性。[Estornell and Liu, 2025] 为辩论动力学提供了形式化框架，并提出克服多数趋同的干预措施。[Ku et al., 2025] 证明结构化的双智能体辩论能够有效恢复隐含前提，优于单智能体模型。MRBalance [Zou et al., 2025] 在面向 ECI 的联合问答框架中采用多个 LLM 智能体，但它主要聚合的是在粗粒度上反复推理因果性的智能体。更广泛地说，当前大多数多智能体 LLM 范式依赖异构智能体（不同模型或经不同调优的智能体）求解同一任务实例，期望以多样性提升准确率。

**EN** By contrast, LLM-SD uses identical LLM instances and achieves diversity through role decomposition: each agent is constrained to produce a specific type of evidence. Therefore, LLM-SD is not "multiple models doing the same reasoning," but rather "one model performing multiple complementary causal tests," enabling adversarial evidence integration that is directly grounded in the causality definition.

**中文** 相比之下，LLM-SD 使用相同的 LLM 实例，并通过角色分解实现多样性：每个智能体被约束产生特定类型的证据。因此，LLM-SD 并非「多个模型做同样的推理」，而是「一个模型执行多种互补的因果检验」，从而实现直接植根于因果性定义的对抗式证据集成。

---

## 3 预备知识（Preliminaries）

### 3.1 问题形式化（Problem Formulation）

**EN** Given an input document D comprising multiple sentences {S₁, S₂, …, S_N}, containing a set of annotated event mentions {e_i, e_j, …, e_M}, ECI seeks to identify all causal event pairs {p₁, p₂, …}, where each pair p_l = (e_i, e_j) denotes a causal relationship C(p_l) ∈ {0, 1}, where i, j ∈ {1, 2, …, M}.

**中文** 给定由多个句子 {S₁, S₂, …, S_N} 组成的输入文档 D，其中包含一组已标注的事件提及 {e_i, e_j, …, e_M}，ECI 旨在识别所有因果事件对 {p₁, p₂, …}，其中每个事件对 p_l = (e_i, e_j) 表示一种因果关系 C(p_l) ∈ {0, 1}，且 i, j ∈ {1, 2, …, M}。

### 3.2 事件因果性定义（Event Causality Definition）

**EN** Grounding in both logical/commonsense causality definitions [Cheng et al., 2025] and established annotation protocols from prior work [Caselli and Vossen, 2017; Wang et al., 2022; Lai et al., 2022], we define event causality as follows:

**中文** 基于逻辑/常识因果性定义 [Cheng et al., 2025] 以及先前工作确立的标注规范 [Caselli and Vossen, 2017; Wang et al., 2022; Lai et al., 2022]，我们对事件因果性定义如下：

**EN** Definition 1. For a given context D, causality e_i → e_j between two events e_i, e_j ∈ D exists if and only if they satisfy at least one of the following three conditions:

**中文** 定义 1. 对于给定上下文 D，两个事件 e_i, e_j ∈ D 之间的因果关系 e_i → e_j 存在，当且仅当它们至少满足以下三个条件之一：

**EN** • Enablement/Prevention: e_i occurs before e_j and has effect on (enables or prevents) the occurrence of e_j;

**中文** • 促成/阻止（Enablement/Prevention）：e_i 先于 e_j 发生，并对 e_j 的发生产生影响（促成或阻止其发生）；

**EN** • Counterfactual Dependence: e_j would not occur if e_i did not occur (counterfactual necessity);

**中文** • 反事实依赖（Counterfactual Dependence）：若 e_i 未发生，则 e_j 不会发生（反事实必要性）；

**EN** • Deterministic Implication: The occurrence of e_i necessarily entails the occurrence of e_j (logical sufficiency).

**中文** • 确定性蕴含（Deterministic Implication）：e_i 的发生必然导致 e_j 的发生（逻辑充分性）。

**EN** Figure 2: Case studies comparing ECI approaches: (a) simple LLM-based methods (vanilla LLM, LLM + CoT, LLM + ICL) versus (b) LLM-Self-Debate framework, implemented using DeepSeek-Chat as backbone model. Our LLM-SD reduces the spurious causalities led by causal hallucination and mitigates false negatives (with long lexical distance) caused by errors in single-evidence analysis via adversarial evidence integration.

**中文** 图 2：ECI 方法对比案例研究：(a) 简单的基于 LLM 的方法（原生 LLM、LLM + CoT、LLM + ICL）与 (b) LLM 自我辩论框架，二者均以 DeepSeek-Chat 作为骨干模型实现。我们的 LLM-SD 通过对抗式证据集成，减少了因果幻觉导致的虚假因果关系，并缓解了单一证据分析错误所造成的假阴性（长词汇距离情形）。

![Figure 2](https://cdn-mineru.openxlab.org.cn/result/2026-10-21/e68f5f13-4fe0-45ab-ae61-772a152e88e7/54af48673250d10c3ca4e46938022254d72990908423856e2d2845dd7ad397bf.jpg)

---

## 4 方法（Methodology）

**EN** LLM-SD consists of three modules: Affirmative Team (AT), Negative Team (NT), and Adjudication Committee (AC), each comprising identical LLM agents to collect different evidence or issue a verdict across two distinct reasoning phases (see Figure 3).

**中文** LLM-SD 由三个模块组成：正方团队（Affirmative Team，AT）、反方团队（Negative Team，NT）与裁决委员会（Adjudication Committee，AC）。每个模块都由相同的 LLM 智能体构成，在两个不同的推理阶段中收集不同证据或作出裁决（见图 3）。

**EN** Figure 3: Framework of LLM-Self-Debate. All debate and adjudication agents in the framework are identical LLMs.

**中文** 图 3：LLM 自我辩论框架。框架中所有辩论与裁决智能体均为相同的 LLM。

![Figure 3](https://cdn-mineru.openxlab.org.cn/result/2026-10-21/e68f5f13-4fe0-45ab-ae61-772a152e88e7/642910b7b6cfbcf64f82e018a124da6e25d0b3d115b59cc8941afb0a0d7510ed.jpg)

### 4.1 推理阶段（Reasoning Phases）

> 译注：原文小标题写作 "Reasoning Phrases"，据文意应为 "Reasoning Phases"（推理阶段）。

**EN** The first phase is the Evidence Presentation Phase, AT and NT deploy LLM agents to collect supporting or rebuttal evidence for the existence of causality. In this phase, the LLM agents function as "debaters," articulating their positions by leveraging allocated reasoning evidence to support their claims. This step parallels the Constructive Speech stage in formal debates, where teams systematically present their core arguments.

**中文** 第一阶段是证据陈述阶段（Evidence Presentation Phase），AT 与 NT 部署 LLM 智能体，为因果关系是否存在收集支持性证据或反驳性证据。在此阶段，LLM 智能体充当「辩手」，利用分配给它们的推理证据阐明立场、支撑主张。这一步对应于正式辩论中的「立论发言」（Constructive Speech）环节，各队在此系统地陈述其核心论点。

**EN** The second phase is the Adjudication Phase, which involves evaluating the strength (reliability) of both supporting and rebuttal evidence in the first phase through two adjudication mechanisms: 1) An automatic adjudication method that applies pre-determined rules to cases where the evidence strength demonstrates a clear disparity; and 2) A panel adjudication method where the collected evidence is submitted to AC for contradiction analysis and final verdict. This phase resembles the Open Argumentation stage in formal debates, where the debaters present and rebut claims, the adjudication mechanisms systematically weigh opposing evidence. The automatic adjudication handles clear-cut cases efficiently, while the panel adjudication resembles moderated deliberation.

**中文** 第二阶段是裁决阶段（Adjudication Phase），通过两种裁决机制评估第一阶段中支持性证据与反驳性证据的强度（可靠性）：1）自动裁决方法，对证据强度差距明显的情形应用预先确定的规则；2）合议裁决方法，将收集到的证据提交 AC 进行矛盾分析与最终裁决。该阶段类似于正式辩论中的「自由辩论」（Open Argumentation）环节：辩手提出并反驳主张，裁决机制则系统地权衡对立双方的证据。自动裁决高效地处理明确无争议的情形，而合议裁决则类似于有主持的合议审议。

### 4.2 辩论团队（Debate Teams）

**EN** Aligned with standard debate formats, LLM-SD comprises two opposing teams: AT (arguing for causality) and the NT (rejecting it). For an ordered query event pair (e_i, e_j), these two teams target for collecting and presenting evidence that supports/rebuts the causality e_i → e_j (see Figure 4).

**中文** 与标准辩论形式一致，LLM-SD 包含两个对立团队：AT（为因果性辩护）与 NT（否证因果性）。对于有序的查询事件对 (e_i, e_j)，这两个团队旨在收集并呈现支持/反驳因果关系 e_i → e_j 的证据（见图 4）。

**EN** Figure 4: Simplified prompt templates for LLM agents to query supporting/rebuttal evidence. The mediator evidence queries employ a two-stage prompt design.

**中文** 图 4：LLM 智能体查询支持性/反驳性证据的简化提示模板。中介证据查询采用两阶段提示设计。

![Figure 4](https://cdn-mineru.openxlab.org.cn/result/2026-10-21/e68f5f13-4fe0-45ab-ae61-772a152e88e7/5a0c2b07f9855cecc5bf6e77549d4035965d830f7c20da201a59f24a2e59c9ef.jpg)

**EN** Supporting Evidence of Affirmative Team. Building upon formal definitions of causality, our analysis employs six categories of supporting evidence collected through LLM agents: (1) direct causal question answering result E_dir^s, (2) temporal ordering validation result E_tem^s, (3) necessity testing result via counterfactuals E_nec^s, (4) sufficiency analysis result E_suf^s (if e_i occurs, e_j must occur), and (5) explicit lexical evidence E_exp^s identifying causal markers that linguistically connect the events in context. (6) mediating factor evidence result E_med^s, whether there exists a mediator e_m that satisfies e_i → e_m and e_m → e_j. The supporting evidence can be formalized as:

**中文** 正方团队的支持性证据。在因果性形式化定义的基础上，我们的分析采用由 LLM 智能体收集的六类支持性证据：（1）直接因果问答结果 E_dir^s；（2）时间顺序验证结果 E_tem^s；（3）基于反事实的必要性检验结果 E_nec^s；（4）充分性分析结果 E_suf^s（若 e_i 发生，则 e_j 必然发生）；（5）显式词汇证据 E_exp^s，用于识别在上下文中从语言层面连接这些事件的因果标记；（6）中介因素证据 E_med^s，即是否存在满足 e_i → e_m 且 e_m → e_j 的中介事件 e_m。支持性证据可形式化为：

$$
\mathcal{E}_{intra}^{s} = \{E_{dir}^{s}, E_{tem}^{s}, E_{nec}^{s}, E_{suf}^{s}, E_{exp}^{s}\},\tag{1}
$$

$$
\mathcal{E}_{inter}^{s} = \{E_{dir}^{s}, E_{tem}^{s}, E_{nec}^{s}, E_{suf}^{s}, E_{med}^{s}\}.\tag{2}
$$

**中文** 式 (1) 为句内（intra）事件对的支持性证据集合，式 (2) 为句间（inter）事件对的支持性证据集合；两者的差别在于：句内使用显式词汇证据 E_exp^s，句间使用中介因素证据 E_med^s。

**EN** Explicit lexical evidence E_exp^s is used only for intra-sentence event pairs due to the difficulty of accurately identifying causal markers across sentences. Mediating factor evidence E_med^s is applied only to inter-sentence event pairs, as mediators are less common within single sentences and prone to confusion in intra-sentence contexts.

**中文** 显式词汇证据 E_exp^s 仅用于句内事件对，因为跨句准确识别因果标记较为困难。中介因素证据 E_med^s 仅用于句间事件对，因为中介在单句内较为少见，且在句内语境中容易混淆。

**EN** Rebuttal Evidence of Negative Team. The opposing debater agents gather the following three categories of rebuttal evidence: (1) reverse temporal evidence E_tem^r, which demonstrates that the cause event does not precede the effect event; (2) non-dependency evidence E_dep^r, indicating that the change of e_i has no effect on e_j; (3) contradictory relation evidence E_con^r, where the relationship r_ab between the two events contradicts causality, such as coreferential events or inclusive (sub-event) relations. The rebuttal evidence can be formalized as:

**中文** 反方团队的反驳性证据。反方辩手智能体收集以下三类反驳证据：（1）逆向时间证据 E_tem^r，表明原因事件并不先于结果事件发生；（2）非依赖证据 E_dep^r，表明 e_i 的变化对 e_j 没有影响；（3）矛盾关系证据 E_con^r，即两个事件之间的关系 r_ab 与因果性相矛盾，例如共指事件或包含（子事件）关系。反驳证据可形式化为：

$$
\mathcal{E}^{r} = \{E_{tem}^{r}, E_{dep}^{r}, E_{con}^{r}\}.\tag{3}
$$

**EN** We only selects reverse temporal evidence for rebuttal and does not select the evidence that contradicts necessity and sufficiency, because according to Definition 1, sufficiency and necessity are not prerequisites for the existence of causality. The purpose of non-dependency evidence is to prevent LLMs from engaging in excessive reasoning.

**中文** 我们在反驳中只选取逆向时间证据，而不选取与必要性和充分性相矛盾的证据，因为根据定义 1，充分性与必要性并非因果关系存在的前提条件。非依赖证据的目的在于防止 LLM 进行过度推理。

### 4.3 证据强度分级（Evidence Strength Grading）

**EN** As the affirmative and negative teams may present perspectively divergent evidence, we evaluate the strength of both supporting and rebuttal evidence via a predefined rule-based approach. Strength grading rules are designed according to the importance and decisiveness of different evidence types (denoted f_s for support and f_r for rebuttal). Temporal precedence (E_tem^s) is treated as a near-necessary constraint and combined with complementary tests—direct causal judgment (E_dir^s) and counterfactual necessity (E_nec^s)—to avoid over-reliance on any single cue. Strong support is assigned only under multi-test agreement accompanied by a decisive connector. On the rebuttal side, higher strength grades require multiple independent refutation cues, enhancing robustness against noisy single-evidence rebuttals.

**中文** 由于正方与反方可能呈现视角上相互分歧的证据，我们通过预定义的基于规则的方法评估支持性证据与反驳性证据的强度。强度分级规则依据不同证据类型的重要性与决定性来设计（支持性记为 f_s，反驳性记为 f_r）。时间先后性（E_tem^s）被视为近乎必要的约束，并与互补检验——直接因果判断（E_dir^s）与反事实必要性（E_nec^s）——相结合，以避免过度依赖任何单一线索。只有在多项检验一致且伴随决定性连接词时，才赋予「强支持」。在反驳一侧，更高的强度等级要求多个独立的反驳线索，从而增强对含噪单一证据反驳的鲁棒性。

**EN** Table 1 presents the grading rules. These rules are consistent with the fundamental causality logic in Definition 1, and the multi-evidence grading reduces misidentification risks caused by individual misinterpretations and overestimation biases.

**中文** 表 1 给出了分级规则。这些规则与定义 1 中的基本因果逻辑一致，多证据分级降低了因个体误读与高估偏差所导致的误判风险。

**表 1：支持性证据与反驳性证据的强度分级规则**

| 强度等级 | 满足的支持性证据 | 满足的反驳性证据 |
|---|---|---|
| 强（Strong） | $E_{tem}^{s}+E_{dir}^{s}+E_{nec}^{s}\ge 2$，且（$E_{exp}^{s}/E_{med}^{s}=1$ 或 $E_{suf}^{s}=1$） | $E_{tem}^{r}+E_{con}^{r}+E_{dep}^{r}\ge 2$ |
| 中（Medium） | $E_{tem}^{s}+E_{dir}^{s}+E_{nec}^{s}\ge 2$ | $E_{tem}^{r}+E_{con}^{r}+E_{dep}^{r}=1$ |
| 弱（Weak） | $E_{tem}^{s}+E_{dir}^{s}+E_{nec}^{s}\le 1$ | $E_{tem}^{r}+E_{con}^{r}+E_{dep}^{r}=0$ |

> 译注：表中「E_exp^s / E_med^s」表示句内情形使用显式词汇证据、句间情形使用中介因素证据，二者按事件对的类型择一取用。

### 4.4 证据裁决（Evidence Adjudication）

**EN** Based on the supporting and rebuttal evidence strengths, G_s = f_s(ℰ^s) and G_r = f_r(ℰ^r), we employ two adjudication methods: automatic adjudication and LLM-based (AC) adjudication. The automatic method follows the rules in Table 2. If it cannot reach a conclusion, the evidence is submitted to AC, which then reasons comprehensively to deliver a final verdict. The causality verdict C(e_i, e_j) thus integrates rule-based (Φ) and LLM-based (Ψ) adjudication via Equation 4.

**中文** 基于支持证据强度 G_s = f_s(ℰ^s) 与反驳证据强度 G_r = f_r(ℰ^r)，我们采用两种裁决方法：自动裁决与基于 LLM 的（AC）裁决。自动方法遵循表 2 中的规则。若无法得出结论，则将证据提交 AC，由其进行综合推理并给出最终裁决。因此，因果裁决 C(e_i, e_j) 通过式 (4) 集成基于规则的（Φ）与基于 LLM 的（Ψ）裁决。

$$
\mathcal{C}(e_i,e_j)=
\begin{cases}
1, & \text{if } \Phi(G_s,G_r)=\text{"Exists"},\\
0, & \text{if } \Phi(G_s,G_r)=\text{"Not Exist"},\\
\Psi(\mathcal{E}^{s},\mathcal{E}^{r}), & \text{if } \Phi(G_s,G_r)=\text{"Uncertain"}.
\end{cases}\tag{4}
$$

**中文** 式 (4) 的含义是：当自动裁决 Φ 判定为「存在（Exists）」时，因果裁决取 1；判定为「不存在（Not Exist）」时取 0；判定为「不确定（Uncertain）」时，则交由基于 LLM 的裁决函数 Ψ 依据支持证据集合 ℰ^s 与反驳证据集合 ℰ^r 给出最终结果。

**EN** Here Φ(·) denotes leveraging prompt template to guider LLM for causal determination according to support evidence and rebuttal evidence.

**中文** 这里 Φ(·) 表示依据支持证据与反驳证据，利用提示模板引导 LLM 进行因果判定。

> 译注：原文此句将 Φ(·) 描述为「用提示模板引导 LLM 判定」，而式 (4) 中 Φ 表示基于规则的自动裁决函数、Ψ 才表示 LLM 裁决，前后表述略有出入，译文照录原文。另原文 "guider" 应为 "guide"。

**表 2：自动裁决规则**

| 支持证据强度 | 反驳证据强度 | 结论 |
|---|---|---|
| 强（Strong） | 弱（Weak） | 存在（Exists） |
| 强（Strong） | 中（Medium） | 不确定（Uncertain） |
| 强（Strong） | 强（Strong） | 不存在（Not Exist） |
| 中（Medium） | 强/中（Strong/Medium） | 不存在（Not Exist） |
| 中（Medium） | 弱（Weak） | 不确定（Uncertain） |

---

## 5 实验（Experiments）

**EN** To evaluate the effectiveness and advancement of LLM-SD and verify the rationality of the framework design, we conducted extensive comparative experiments on three benchmark datasets. In our experiments, we primarily investigate the following five research questions (RQs):

**中文** 为评估 LLM-SD 的有效性与先进性，并验证框架设计的合理性，我们在三个基准数据集上开展了大量对比实验。实验中，我们主要研究以下五个研究问题（RQs）：

**EN** • RQ1: Can LLM-SD effectively mitigate the excessive false positives caused by causal hallucination?

**中文** • RQ1：LLM-SD 能否有效缓解因果幻觉所导致的过度假阳性？

**EN** • RQ2: Are all "debaters" (i.e., LLM agents for evidence collection) in the affirmative and opposing teams necessary?

**中文** • RQ2：正方团队与反方团队中的所有「辩手」（即用于证据收集的 LLM 智能体）是否都是必要的？

**EN** • RQ3: Are the two adjudication mechanisms necessary for the model?

**中文** • RQ3：两种裁决机制对模型是否必要？

**EN** • RQ4: Are the evidence strength grading rules effective and necessary for the model?

**中文** • RQ4：证据强度分级规则对模型是否有效且必要？

**EN** • RQ5: How robust is LLM-SD with respect to event distance and text length?

**中文** • RQ5：LLM-SD 在事件距离与文本长度方面的鲁棒性如何？

### 5.1 实验设置（Experimental Setup）

**EN** Datasets. We selected three datasets for comparative validation.

**中文** 数据集。我们选取三个数据集进行对比验证。

**EN** • Causal-TimeBank (CTB) [Mirza et al., 2014] focuses on explicit causality. It is commonly used for evaluating intra-sentence ECI performance. We conduct 10 independent trials on CTB, each time randomly selecting 10% of the dataset as the test set, and report the average results.

**中文** • Causal-TimeBank（CTB）[Mirza et al., 2014] 聚焦显式因果关系，常用于评估句内 ECI 性能。我们在 CTB 上进行 10 次独立试验，每次随机选取数据集的 10% 作为测试集，并报告平均结果。

**EN** • EventStoryLine Corpus (ESL) [Caselli and Vossen, 2017] is designed for cross-document reasoning and narrative generation. We perform 5 independent trials, each excluding 2 topics and taking 20% of the remaining data as the test set, then report the average results.

**中文** • EventStoryLine 语料库（ESL）[Caselli and Vossen, 2017] 面向跨文档推理与叙事生成而设计。我们进行 5 次独立试验，每次排除 2 个主题，并从其余数据中取 20% 作为测试集，然后报告平均结果。

**EN** • MAVEN-ERE [Wang et al., 2022] is a large-scale event relation extraction dataset covers coreference, temporal, causal, and subevent relations. Following the original split, we directly use its development set as the test set for evaluation.

**中文** • MAVEN-ERE [Wang et al., 2022] 是一个大规模事件关系抽取数据集，涵盖共指、时间、因果与子事件关系。遵循原始数据划分，我们直接将其开发集作为测试集进行评估。

**EN** Baselines. Given that we focus on model performance in zero-shot scenarios, we employed different reasoning methods based on LLMs as unsupervised baselines. Specifically, we selected five prompt-based reasoning paradigms for comparison:

**中文** 基线模型。鉴于我们关注零样本场景下的模型性能，我们采用基于 LLM 的不同推理方法作为无监督基线。具体而言，我们选取五种基于提示的推理范式进行比较：

**EN** • Simple Prompt (SP) [Brown et al., 2020]: We use a simple prompt to guide the LLM to directly judge and answer whether a causal relationship exists based on the context and event pair.

**中文** • 简单提示（Simple Prompt，SP）[Brown et al., 2020]：我们使用简单提示，引导 LLM 根据上下文与事件对直接判断并回答是否存在因果关系。

**EN** • In-Context Learning (ICL) [Min et al., 2022]: In addition to the simple prompt, we provide positive and negative examples for the LLM to perform in-context learning. Following the findings in [Gao et al., 2023], we selected 2 positive and 4 negative examples as demonstrations.

**中文** • 上下文学习（In-Context Learning，ICL）[Min et al., 2022]：在简单提示之外，我们提供正例与负例，供 LLM 进行上下文学习。遵循 [Gao et al., 2023] 的发现，我们选取 2 个正例与 4 个负例作为示例。

**EN** • Zero-Shot CoT (ZSCoT) [Wei et al., 2022]: Based on the simple prompt, we add the instruction "think step-by-step" to require the LLM to reason step-by-step.

**中文** • 零样本思维链（Zero-Shot CoT，ZSCoT）[Wei et al., 2022]：在简单提示的基础上，我们加入「逐步思考（think step-by-step）」指令，要求 LLM 逐步推理。

**EN** • Self-Ask [Press et al., 2023]: Following the causal judgment logic, we crafted a prompt template to guide the LLM to reason sequentially from perspectives of temporality, sufficiency, and necessity.

**中文** • Self-Ask [Press et al., 2023]：遵循因果判断逻辑，我们设计了提示模板，引导 LLM 依次从时间性、充分性与必要性三个视角进行推理。

**EN** • Definition Prompt: We formulated the definition of event causality proposed in this paper into a prompt, requiring the LLM to judge based on this definition and select the corresponding causal relation type.

**中文** • 定义提示（Definition Prompt）：我们将本文提出的事件因果性定义编写为提示，要求 LLM 依据该定义进行判断并选择相应的因果关系类型。

**EN** In addition, we compared LLM-SD with two advanced unsupervised LLM-based models, KnowQA [Wang et al., 2024] and Dr.ECI [Cai et al., 2025]. To quantify the gap between unsupervised and supervised models, we also selected multiple supervised models for comparison, including BERT [Devlin et al., 2019], RoBERTa [Zhuang et al., 2021], Longformer [Beltagy et al., 2020], KADE [Wu et al., 2023], ERGO [Chen et al., 2022] and iLIF [Liu et al., 2024].

**中文** 此外，我们将 LLM-SD 与两个先进的基于 LLM 的无监督模型 KnowQA [Wang et al., 2024] 和 Dr.ECI [Cai et al., 2025] 进行比较。为量化无监督模型与有监督模型之间的差距，我们还选取多个有监督模型进行对比，包括 BERT [Devlin et al., 2019]、RoBERTa [Zhuang et al., 2021]、Longformer [Beltagy et al., 2020]、KADE [Wu et al., 2023]、ERGO [Chen et al., 2022] 与 iLIF [Liu et al., 2024]。

**EN** Evaluation Metrics. Consistent with the previous works, we use Precision (P), Recall (R), and F1-score (F1) to evaluate model performance.

**中文** 评价指标。与先前工作一致，我们采用精确率（Precision，P）、召回率（Recall，R）与 F1 值（F1）评估模型性能。

**EN** Implementation Details. We selected two advanced LLMs as the backbone models: GPT-4o-mini and Deepseek-V3 (DeepSeek-Chat) for main experiment. In Section 5.6, we compared the performance of LLM-SD on different LLMs.

**中文** 实现细节。我们选取两个先进的 LLM 作为骨干模型：GPT-4o-mini 与 Deepseek-V3（DeepSeek-Chat），用于主实验。在第 5.6 节中，我们比较了 LLM-SD 在不同 LLM 上的性能。

**EN** In experiments, we utilized Python's concurrent.futures.ThreadPoolExecutor to implement multi-threaded parallel calls to all LLM agents, thereby saving inference time. We set temperature = 0 to reduce the randomness of generated outputs while maintaining logical consistency.

**中文** 实验中，我们使用 Python 的 concurrent.futures.ThreadPoolExecutor 对所有 LLM 智能体进行多线程并行调用，以节省推理时间。我们将 temperature 设为 0，以降低生成输出的随机性，同时保持逻辑一致性。

### 5.2 主要结果（Main Results，RQ1）

**EN** Tables 3–4 show that LLM-SD achieves state-of-the-art performance among unsupervised models across all benchmarks. It significantly improves precision by reducing false positives from causal hallucination and enhances recall on inter-sentence ECI through multi-evidence reasoning. Compared to supervised baselines, LLM-SD remains competitive and even outperforms several supervised models on ESL, highlighting its strong potential. Simpler strategies like Self-Ask or providing the Definition yield only marginal gains, confirming that explicit instructions alone cannot fix LLMs consistency errors in causal reasoning. The consistent results across different LLMs (GPT-4o-mini and DeepSeek-Chat) further validate the robustness of our approach.

**中文** 表 3–4 表明，LLM-SD 在所有基准上于无监督模型中取得了最先进的性能。它通过减少因果幻觉导致的假阳性显著提升了精确率，并通过多证据推理提升了句间 ECI 的召回率。与有监督基线相比，LLM-SD 仍具竞争力，在 ESL 上甚至优于若干有监督模型，凸显了其强大潜力。Self-Ask 或提供定义等更简单的策略仅带来边际增益，说明仅靠显式指令无法修正 LLM 在因果推理中的一致性错误。不同 LLM（GPT-4o-mini 与 DeepSeek-Chat）上的结果保持一致，进一步验证了我们方法的鲁棒性。

**表 3：LLM-SD 与基线在 CTB 和 ESL 上的实验结果（P/R/F1，单位 %）**

| 模型 | CTB 句内 P | CTB 句内 R | CTB 句内 F1 | ESL 句内 P | ESL 句内 R | ESL 句内 F1 | ESL 句间 P | ESL 句间 R | ESL 句间 F1 | ESL 总体 P | ESL 总体 R | ESL 总体 F1 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| BERT (2019) | 47.6 | 55.1 | 51.1 | 47.8 | 57.2 | 52.1 | 36.8 | 29.2 | 32.6 | 41.3 | 38.3 | 39.7 |
| RoBERTa (2021) | 39.9 | 60.9 | 48.2 | 59.7 | 38.0 | 46.4 | 31.3 | 34.2 | 32.7 | 37.3 | 35.5 | 36.4 |
| LongFormer (2020) | 29.6 | 68.6 | 41.4 | 59.0 | 40.5 | 48.0 | 35.2 | 30.5 | 32.7 | 41.6 | 33.8 | 37.3 |
| ERGO (2022) | 62.1 | 61.3 | 61.7 | 57.5 | 72.0 | 63.9 | 51.6 | 43.3 | 47.1 | 48.6 | 53.4 | 50.9 |
| KADE (2023) | 56.8 | 70.6 | 66.7 | 61.5 | 73.2 | 66.8 | 51.2 | 74.2 | 60.5 | 51.9 | 70.6 | 59.8 |
| **无监督（Unsupervised）** | | | | | | | | | | | | |
| davinci-002 (2023) | 5.0 | 75.2 | 9.3 | 23.2 | 80.0 | 36.0 | – | – | – | – | – | – |
| davinci-003 (2023) | 8.5 | 64.4 | 15.0 | 33.2 | 74.4 | 45.9 | – | – | – | – | – | – |
| GPT-3.5 (2023) | 7.0 | 82.6 | 12.8 | 27.6 | 80.2 | 41.0 | – | – | – | – | – | – |
| GPT-4 (2023) | 6.1 | 97.4 | 11.5 | 27.2 | 94.7 | 42.2 | – | – | – | – | – | – |
| Dr. ECI (2025) | 11.7 | 71.8 | 13.0 | 29.0 | 75.4 | 41.9 | – | – | – | – | – | – |
| **gpt-4o-mini** | | | | | | | | | | | | |
| SP (2020) | 11.4 | 65.8 | 13.5 | 29.1 | 62.7 | 31.8 | 2.9 | 34.7 | 6.2 | 10.6 | 44.3 | 18.5 |
| ICL (2022) | 10.7 | 67.7 | 14.4 | 34.6 | 60.4 | 35.3 | 3.6 | 30.6 | 6.9 | 12.5 | 46.9 | 20.7 |
| ZSCoT (2022) | 9.2 | 74.9 | 13.1 | 26.0 | 80.7 | 37.0 | 4.2 | 48.0 | 8.6 | 8.1 | 62.8 | 18.3 |
| Self-Ask (2023) | 11.3 | 59.8 | 16.4 | 21.2 | 74.4 | 38.3 | 4.9 | 45.1 | 9.5 | 8.7 | 58.5 | 18.2 |
| Definition (ours) | 8.8 | 79.5 | 12.7 | 22.5 | 83.1 | 40.2 | 4.5 | 39.3 | 8.6 | 9.2 | 60.5 | 17.3 |
| LLM-SD | 16.3 | 81.5 | 23.7 | 39.6 | 75.5 | 41.5 | 11.1 | 74.2 | 14.4 | 21.2 | 74.7 | 28.5 |
| improved（提升） | 4.9 | 6.6 | 7.3 | 5.0 | 2.2 | 6.3 | 6.2 | 26.2 | 4.9 | 8.7 | 11.9 | 7.8 |
| **deepseek-chat** | | | | | | | | | | | | |
| SP (2020) | 11.3 | 90.6 | 16.0 | 28.4 | 91.6 | 40.2 | 9.7 | 37.7 | 13.7 | 21.9 | 66.6 | 30.7 |
| ICL (2022) | 12.2 | 75.0 | 16.4 | 29.3 | 77.1 | 42.6 | 8.8 | 39.4 | 12.8 | 22.7 | 73.9 | 30.6 |
| ZSCoT (2022) | 9.6 | 91.7 | 13.8 | 27.0 | 90.0 | 37.2 | 8.4 | 53.1 | 12.6 | 16.2 | 78.9 | 25.3 |
| Self-Ask (2023) | 12.9 | 89.2 | 17.3 | 34.3 | 87.6 | 42.2 | 7.1 | 56.1 | 11.8 | 16.0 | 77.6 | 27.2 |
| Definition (ours) | 9.7 | 96.9 | 16.3 | 26.0 | 94.0 | 40.5 | 8.0 | 41.5 | 11.7 | 19.1 | 66.3 | 27.6 |
| LLM-SD | 15.5 | 85.0 | 26.2 | 42.4 | 87.3 | 49.1 | 18.3 | 86.4 | 28.0 | 24.9 | 87.1 | 35.7 |
| improved（提升） | 2.6 | – | 8.9 | 8.1 | – | 6.5 | 8.6 | 30.3 | 14.3 | 2.2 | 8.2 | 5.0 |

> 说明：原表中加粗表示无监督模型中的最优值、下划线表示所有模型中的总体最优值；这些格式在 PDF 文本转换中丢失，本表仅保留数值。"improved" 行为原文给出的相对提升数值（因原文未说明具体比较对象，此处照录）。

**表 4：LLM-SD 与基线在 MAVEN-ERE 上的实验结果（P/R/F1，单位 %）**

| 模型 | 句内 P | 句内 R | 句内 F1 | 句间 P | 句间 R | 句间 F1 |
|---|---|---|---|---|---|---|
| **有监督（Supervised）** | | | | | | |
| BERT (2019) | 46.8 | 50.3 | 48.5 | 43.0 | 46.8 | 44.8 |
| RoBERTa (2021) | 45.9 | 48.8 | 47.2 | 42.3 | 45.1 | 43.6 |
| LongFormer (2020) | 39.3 | 55.9 | 44.6 | 35.8 | 46.0 | 39.2 |
| ERGO (2022) | 63.1 | 65.3 | 64.2 | 48.7 | 62.0 | 54.6 |
| KADE (2023) | 58.7 | 64.4 | 60.0 | 43.7 | 59.3 | 48.0 |
| iLIF (2024) | 74.4 | 51.5 | 60.9 | 67.1 | 49.2 | 56.8 |
| **无监督（Unsupervised）** | | | | | | |
| davinci-002 (2023) | 19.6 | 92.9 | 32.4 | – | – | – |
| davinci-003 (2023) | 25.0 | 75.1 | 37.5 | – | – | – |
| GPT-3.5 (2023) | 19.9 | 85.8 | 32.3 | – | – | – |
| GPT-4 (2023) | 22.5 | 92.4 | 36.2 | – | – | – |
| KnowQA (2024) | 29.2 | 62.1 | 39.7 | – | – | – |
| Dr. ECI (2025) | 22.0 | 80.0 | 34.5 | – | – | – |
| **deepseek-chat** | | | | | | |
| Self-Ask (2023) | 22.5 | 87.7 | 35.3 | 22.6 | 53.4 | 32.6 |
| Definition (ours) | 23.0 | 87.5 | 31.0 | 24.5 | 65.8 | 34.4 |
| LLM-SD | 34.2 | 61.2 | 43.9 | 27.7 | 69.2 | 38.2 |
| improved（提升） | 11.7 | – | 4.2 | 3.2 | 3.4 | 3.8 |

### 5.3 消融研究（Ablation Studies，RQ2–RQ3）

**EN** This section presents ablation studies from three perspectives to verify the effectiveness of each evidence type and module. All experiments were conducted on a test subset of the ESL dataset, with LLM parameters kept identical to the main experiments. The ablation experiments results are shown in Figure 5.

**中文** 本节从三个角度呈现消融研究，以验证每种证据类型与每个模块的有效性。所有实验均在 ESL 数据集的测试子集上进行，LLM 参数与主实验保持一致。消融实验结果见图 5。

**EN** Evidence Ablation. To systematically evaluate the independent contribution and indispensability of each type of evidence, an ablation study on evidence was conducted. Specifically, according to the rules in Table 1, when a specific evidence-collection agent is ablated, the corresponding term is excluded from the evidence aggregation formula (with the threshold kept unchanged), and the assessment outcome of causal relations is re-determined. The experimental results in Figure 5 demonstrate that removing any supportive or rebuttal evidence leads to a decline in overall model performance, indicating that all the aforementioned evidence is essential for the framework.

**中文** 证据消融。为系统评估每类证据的独立贡献与不可或缺性，我们开展了证据消融研究。具体而言，依据表 1 的规则，当消融某个特定的证据收集智能体时，从证据聚合公式中剔除相应项（阈值保持不变），并重新判定因果关系的评估结果。图 5 的实验结果表明，移除任何支持性证据或反驳性证据都会导致模型整体性能下降，说明上述所有证据对框架都是必要的。

**EN** LLM Adjudication Mechanism Ablation. We further ablated the LLM-based adjudication mechanism (AC) by forcing uncertain cases to be judged as either always non-existent (-adj(-)) or always existent (-adj(+)). Both ablation settings lead to performance degradation, confirming that the LLM adjudicator is an indispensable component of the framework. This indicates that while our rule-based automatic adjudication is logically grounded, it benefits from the flexible judgment of an LLM to handle nuanced and complex scenarios that pre-defined rules may not fully cover. It also compensates for potential errors arise from the subjectivity of rules.

**中文** LLM 裁决机制消融。我们进一步消融基于 LLM 的裁决机制（AC），将不确定的案例强制判定为始终不存在（-adj(-)）或始终存在（-adj(+)）。两种消融设置均导致性能下降，证实 LLM 裁决器是框架中不可或缺的组件。这表明，尽管我们基于规则的自动裁决具有逻辑依据，但它仍需借助 LLM 的灵活判断来处理预定义规则可能无法完全覆盖的微妙复杂情形；同时，这也能补偿因规则主观性而产生的潜在错误。

**EN** Automatic Adjudication Mechanism Ablation. We removed the predefined adjudication rules and relied solely on the LLM adjudicator (AC) to judge all cases. As shown in Figure 5, this leads to a significant performance drop, especially in precision. This result demonstrates that relying exclusively on LLM judgment is unreliable; without structured rules to guide the synthesis of multiple evidences, the LLM tends to make simplistic decisions based on evidence counts, which increases false positives and confirms the necessity of our hybrid adjudication design.

**中文** 自动裁决机制消融。我们移除预定义的裁决规则，仅依靠 LLM 裁决器（AC）对所有案例进行判断。如图 5 所示，这导致性能显著下降，尤其在精确率方面。该结果表明，仅依赖 LLM 判断并不可靠；在没有结构化规则引导多证据综合的情况下，LLM 倾向于依据证据数量作出简单化决策，从而增加假阳性，这印证了我们混合裁决设计的必要性。

![Figure 5](https://cdn-mineru.openxlab.org.cn/result/2026-10-21/e68f5f13-4fe0-45ab-ae61-772a152e88e7/6cff7f75a47a5400d4057a7e2aa5d9be44534d920880754691985ed461e1566e.jpg)

**EN** Figure 5: Results of ablation study on ESL.

**中文** 图 5：ESL 上的消融实验结果。

### 5.4 证据分级规则的验证（Validating the Evidence Grading Rules，RQ4）

**EN** To further examine whether the evidence strength grading rules in Table 1 are reasonable and not overly tailored, we keep the debater agents and evidence extraction prompts unchanged, and only replace the mapping from evidences to (G_s, G_r) and the subsequent adjudication. We consider the following alternatives:

**中文** 为进一步检验表 1 中的证据强度分级规则是否合理、是否过度定制，我们保持辩手智能体与证据抽取提示不变，仅替换从证据到 (G_s, G_r) 的映射及后续裁决过程。我们考虑以下替代方案：

**EN** (1) Count-based grading, which assigns strength levels based solely on the count of satisfied evidences:

**中文** （1）基于计数的分级（Count-based grading）：仅依据满足条件的证据数量来赋予强度等级：

$$
G_{s} = \begin{cases}
\text{"Strong"}, & \text{if } \sum \mathcal{E}^{s} \ge 4,\\
\text{"Medium"}, & \text{if } 2 \le \sum \mathcal{E}^{s} \le 3,\\
\text{"Weak"}, & \text{otherwise},
\end{cases}
\qquad
G_{r} = \begin{cases}
\text{"Strong"}, & \text{if } \sum \mathcal{E}^{r} = 3,\\
\text{"Medium"}, & \text{if } 1 \le \sum \mathcal{E}^{r} \le 2,\\
\text{"Weak"}, & \text{otherwise}.
\end{cases}\tag{5}
$$

**中文** 式 (5)：支持证据数量不少于 4 时记为「强」，2–3 时记为「中」，其余为「弱」；反驳证据数量等于 3 时记为「强」，1–2 时记为「中」，其余为「弱」。

**EN** (2) Simple-voting grading, which determines causality directly by comparing the counts of supporting and rebuttal evidences with a threshold k:

**中文** （2）简单投票分级（Simple-voting grading）：直接通过将支持证据与反驳证据的数量同阈值 k 比较来判定因果关系：

$$
\mathcal{C}(e_i,e_j) = \begin{cases}
1, & \text{if } \sum \mathcal{E}^{r} = 0 \text{ and } \sum \mathcal{E}^{s} \ge k,\\
0, & \text{otherwise}.
\end{cases}\tag{6}
$$

**中文** 式 (6)：当不存在任何反驳证据、且支持证据数量不少于 k 时判定为存在因果关系，否则判定为不存在。

**EN** In this method, k serves as a hyperparameter representing the minimum number of supporting evidences required when no rebuttal evidence is present.

**中文** 在该方法中，k 是一个超参数，表示在不存在反驳证据时所要求的最少支持证据数量。

**EN** Table 5 shows that our evidence grading rules significantly outperform the other two alternatives, confirming their effectiveness. The count-based method performs worst, as merely tallying evidence amplifies the LLM's systematic errors and yields low precision. Simple-voting grading strongly depends on threshold k, with precision rising and recall falling as k increases, making it difficult to achieve an effective balance.

**中文** 表 5 显示，我们的证据分级规则显著优于另外两种替代方案，印证了其有效性。基于计数的方法表现最差，因为单纯统计证据会放大 LLM 的系统性误差并导致精确率偏低。简单投票分级强烈依赖阈值 k：随着 k 增大，精确率上升而召回率下降，难以取得有效平衡。

**表 5：ESL 上证据强度分级规则的验证（P/R/F1，单位 %）**

| 分级策略 | 句内 P | 句内 R | 句内 F1 | 句间 P | 句间 R | 句间 F1 |
|---|---|---|---|---|---|---|
| 基于计数（Count-based） | 18.0 | 70.0 | 28.6 | 11.8 | 20.3 | 15.0 |
| 简单投票（Simple-voting，k=1） | 22.3 | 56.4 | 36.4 | 12.8 | 28.2 | 17.6 |
| 简单投票（Simple-voting，k=2） | 30.5 | 43.6 | 37.0 | 15.0 | 20.5 | 18.5 |
| 简单投票（Simple-voting，k=3） | 41.0 | 44.4 | 42.7 | 17.8 | 18.0 | 17.9 |
| 简单投票（Simple-voting，k=4） | 42.7 | 20.5 | 32.0 | 18.3 | 15.4 | 16.0 |
| 本文方法（Ours） | 42.4 | 87.3 | 49.1 | 18.3 | 86.4 | 28.0 |

**EN** Table 5: Validation of evidence strength grading rules on ESL.

**中文** 表 5：证据强度分级规则在 ESL 上的验证。

### 5.5 词汇距离与文本长度的影响（Impact of Lexical Distance and Text Length，RQ5）

**EN** Prior research suggests that LLM-based ECI performance degrades with increasing document length and event distance. To assess LLM-SD's robustness, we analyzed its performance across different lexical distance and document length intervals on CTB and ESL datasets (Figure 6).

**中文** 先前研究表明，基于 LLM 的 ECI 性能会随文档长度与事件距离的增加而下降。为评估 LLM-SD 的鲁棒性，我们分析了其在 CTB 与 ESL 数据集上不同词汇距离与文档长度区间内的性能（图 6）。

**EN** The results confirm that all unsupervised baselines suffer significant performance drops as distance and length increase, with the Definition baseline showing a near-linear decline. In contrast, LLM-SD exhibits strong resilience, maintaining stable performance (F1 > 0.3) with only minor degradation. This demonstrates LLM-SD's superior robustness in handling long-range dependencies and confirms the effectiveness of its multi-evidence reasoning approach for complex ECI tasks.

**中文** 结果证实，随着距离与长度增加，所有无监督基线均出现显著性能下降，其中 Definition 基线呈近乎线性的下降趋势。相比之下，LLM-SD 表现出很强的韧性，仅出现轻微退化，性能保持稳定（F1 > 0.3）。这表明 LLM-SD 在处理长程依赖方面具有优越的鲁棒性，也证实了其多证据推理方法在复杂 ECI 任务中的有效性。

**EN** Figure 6: Performance of LLM-SD and baseline models as a function of event distance and document length.

**中文** 图 6：LLM-SD 与基线模型的性能随事件距离和文档长度的变化。

![Figure 6](https://cdn-mineru.openxlab.org.cn/result/2026-10-21/e68f5f13-4fe0-45ab-ae61-772a152e88e7/8515ef20713b16b2ddbdd8067987ede3835d9b3238b617df91157a798d16a411.jpg)

### 5.6 不同 LLM 架构下的 LLM-SD 评估（Evaluating LLM-SD with Different LLM Architectures）

**EN** To assess the sensitivity and practical deployability of the LLM-SD framework across different LLM architectures, we conduct additional experiments on the ESL dataset using the same zero-shot prompts, identical evidence role assignments, and consistent decoding configurations (temperature = 0). We evaluate several widely adopted LLMs with distinct architectures, including LLaMA3-8B, GPT-3.5, Qwen3-max, and GLM4-32B, and compare their performance with DeepSeek-Chat (our backbone).

**中文** 为评估 LLM-SD 框架在不同 LLM 架构下的敏感性与实际可部署性，我们在 ESL 数据集上开展补充实验，使用相同的零样本提示、相同的证据角色分配以及一致的解码配置（temperature = 0）。我们评估了若干广泛采用且架构各异的 LLM，包括 LLaMA3-8B、GPT-3.5、Qwen3-max 与 GLM4-32B，并将其性能与 DeepSeek-Chat（我们的骨干模型）进行比较。

**EN** Figure 7 reveals two key findings. First, all LLM-SD variants outperform unsupervised baselines using the same corresponding LLM (overall F1 < 0.2), confirming that its role-decomposition framework structurally reduces causal hallucination and enhances performance independently of the backbone model. Second, performance does not scale monotonically with parameter count: DeepSeek-Chat performs best, while the larger Qwen3-max scores lower; the smaller GPT-3.5-turbo outperforms the larger GLM4-32B. This indicates that ECI capability depends on factors beyond scale, such as pre-training data, architecture, and specialized tuning. The backbone model's inductive biases and training alignment are also crucial for effective deployment within LLM-SD.

**中文** 图 7 揭示了两个关键发现。第一，所有 LLM-SD 变体均优于使用相应同一 LLM 的无监督基线（总体 F1 < 0.2），证实其角色分解框架能够从结构上减少因果幻觉，并独立于骨干模型提升性能。第二，性能并不随参数规模单调提升：DeepSeek-Chat 表现最佳，而规模更大的 Qwen3-max 得分反而更低；规模较小的 GPT-3.5-turbo 优于规模更大的 GLM4-32B。这表明 ECI 能力取决于规模之外的因素，如预训练数据、架构与专门调优。骨干模型的归纳偏置与训练对齐，对于在 LLM-SD 中有效部署同样至关重要。

**EN** Figure 7: Performance comparison of different backbone models on the ESL dataset (overall metrics).

**中文** 图 7：不同骨干模型在 ESL 数据集上的性能比较（总体指标）。

![Figure 7](https://cdn-mineru.openxlab.org.cn/result/2026-10-21/e68f5f13-4fe0-45ab-ae61-772a152e88e7/e68b80ac7d91ba45462184a829080628c1ec260c67b59aa37bb9f90989b3a3d5.jpg)

---

## 6 结论（Conclusion）

**EN** We propose LLM-SD, a framework that casts ECI as a structured debate among specialized LLM agents. LLM-SD employs Affirmative and Negative Teams to collect supporting and rebuttal evidence, evaluated by an Adjudication Committee using rule-based and LLM-driven methods. Experiments on three benchmarks show state-of-the-art zero-shot performance, with improved precision and robust recall. However, the reliance on multiple LLM invocations raises cost, and performance on inter-sentence causality remains below that of supervised models. Future research could explore fine-tuning strategies or alignment mechanisms that constrain LLM outputs to further mitigate hallucinations, enhance evidence consistency, and improve multi-hop reasoning capabilities.

**中文** 我们提出 LLM-SD，一个将 ECI 塑造为专门化 LLM 智能体之间结构化辩论的框架。LLM-SD 采用正方团队与反方团队收集支持性证据与反驳性证据，并由裁决委员会使用基于规则与 LLM 驱动的方法进行评估。在三个基准上的实验表明其零样本性能达到最先进水平，精确率得到提升且召回率稳健。然而，对多次 LLM 调用的依赖提高了成本，且句间因果关系的性能仍低于有监督模型。未来研究可探索约束 LLM 输出的微调策略或对齐机制，以进一步缓解幻觉、增强证据一致性并提升多跳推理能力。

---

## 致谢（Acknowledgments）

**EN** Zefan Zeng and Yuehang Si contributed equally to this work.

**中文** Zefan Zeng 与 Yuehang Si 对本文贡献相同。

---

## 参考文献说明

论文原文在结论与致谢之后附有完整的参考文献列表（References），包括 Beltagy et al., 2020、Cai et al., 2025、Caselli and Vossen, 2017 等 40 条条目。参考文献属于书目信息，按学术惯例保留英文原文，不纳入逐段翻译；如需查阅，请参见原文件 `Debate_with_Myself_2112898295071109120.md` 的 References 部分。

---

*本文档由论文原文逐段对照翻译整理，共覆盖摘要、第 1–6 节正文、图表题注与表格内容。*
