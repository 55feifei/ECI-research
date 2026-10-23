# 基于超图建模相关因果效应结构用于文档级事件因果关系识别

**原文标题：** Modeling correlated causal-effect structure with a hypergraph for document-level event causality identification

**作者：** Wei Xiang ^a^, Cheng Liu ^b^, Bang Wang ^b^（通讯作者）

**期刊：** Computer Speech & Language, Vol. 90 (2025), Article 101752
**DOI：** https://doi.org/10.1016/j.csl.2024.101752
**投稿与录用：** 2023 年 7 月 31 日收稿；2024 年 8 月 13 日修回；2024 年 11 月 8 日录用；2024 年 11 月 19 日在线发表
**通讯作者邮箱：** wangbang@hust.edu.cn
**基金资助：** 华中师范大学中央高校基本科研业务费自主科研经费（Grant No: CCNU24XJ029）；国家自然科学基金（Grant No: 62172167）

**单位：**

- ^a^  华中师范大学人工智能教育学部（Central China Normal University, CCNU），中国武汉
- ^b^  华中科技大学电子信息与通信学院，湖北省智能互联网技术重点实验室（Huazhong University of Science and Technology, HUST），中国武汉

> **说明**
>
> 1. 本文为逐段中英文对照译本，按原文顺序编排，公式、表格与图注原位保留。
> 2. 本版已对照 Computer Speech & Language 正式版 PDF（含图 1 图内文字、脚注与参考文献）逐段校订，初版依据 OCR 文本产生的乱码与缺漏均已补齐。
> 3. 原文本身存在的笔误（如式 (6)(7) 说明中的"矩阵 I"、个别语法瑕疵）保留原样，并在译文对应处以括注说明。
> 4. 参考文献共 40 条，按原刊顺序照录于文末，不作翻译。

---

## 关键词 / Keywords

| 英文                               | 中文             |
| ---------------------------------- | ---------------- |
| Event causality identification     | 事件因果关系识别 |
| Correlated cause–effect structure | 相关因果效应结构 |
| Hypergraph convolutional network   | 超图卷积网络     |
| Pre-trained language model         | 预训练语言模型   |

---

## 摘要 / Abstract

**【EN】**
Document-level event causality identification (ECI) aims to detect causal relations in between event mentions in a document. Existing approaches for document-level event causality identification detect the causal relation for each pair of event mentions independently, while ignoring latent correlated cause–effect structure in a document, i.e., one cause (effect) with multiple effects (causes). We argue that identifying the causal relation of one event pair may facilitate the causality identification for other event pairs. In light of such considerations, we propose to model the correlated causal-effect structure by a hypergraph and jointly identify multiple causal relations with the same cause (effect). In particular, we propose an event-hypergraph neural encoding model, called EHNEM, for document-level event causality identification. In EHNEM, we first initialize event mentions' embeddings via a pre-trained language model and obtain potential causal relation of each event pair via a multilayer perceptron. To capture causal correlations, we construct a hypergraph by integrating potential causal relations for the same event as a hyperedge. On the constructed event-hypergraph, we use a hypergraph convolutional network to learn the representation of each event node for final causality identification. Experiments on both EventStoryLine corpus and English-MECI corpus show that our EHNEM model significantly outperforms the state-of-the-art algorithms.

**【中】**
文档级事件因果关系识别（ECI）旨在检测文档中事件提及之间是否存在因果关系。现有的文档级事件因果关系识别方法独立地检测每一对事件提及之间的因果关系，却忽略了文档中潜在的相关因果效应结构，即一个原因（结果）对应多个结果（原因）。我们认为，识别一对事件之间的因果关系可能有助于其他事件对的因果关系识别。基于这一考虑，我们提出用超图对相关的因果效应结构进行建模，并联合识别具有相同原因（结果）的多个因果关系。具体而言，我们提出了一种称为 EHNEM 的事件超图神经编码模型，用于文档级事件因果关系识别。在 EHNEM 中，我们首先借助预训练语言模型初始化事件提及的嵌入，并通过多层感知机获得每个事件对的潜在因果关系。为了捕捉因果相关性，我们将同一事件的潜在因果关系整合为一条超边，从而构建超图。在所构建的事件超图上，我们使用超图卷积网络学习每个事件节点的表示，以完成最终的因果关系识别。在 EventStoryLine 语料库和 English-MECI 语料库上的实验表明，我们的 EHNEM 模型显著优于当前最先进的算法。

---

## 1. 引言 / Introduction

### 段落 1

**【EN】**
Document-level event causality identification (ECI) aims to detect whether there exists a causal relation between two event mentions in a document. It is of great importance for many semantic web applications, such as knowledge graph construction (Heindorf et al., 2020; Khatib et al., 2020), information extraction (Yang et al., 2022; Xiang and Wang, 2019), question answer (Bondarenko et al., 2022; Sui et al., 2022), knowledge reasoning (Yao et al., 2021; Feder et al., 2022), and etc. Fig. 1 illustrates an example of annotated event mentions in a crime topic document with causality labels in the Event StoryLine Corpus (ESC) (Caselli and Vossen, 2017). Where event mentions are annotated as one or more words in raw sentence that most clearly expresses an event occurrence, and causal relation can exist in between both intra-sentence or cross-sentence event mentions. We note that an event may also contain some event arguments with a specific role, such as the time, place and participants of the event, to form a structured event. In this paper, we only use event mentions to identify causal relations between event pairs.

**【中】**
文档级事件因果关系识别（ECI）旨在检测文档中两个事件提及之间是否存在因果关系。该任务对许多语义网应用具有重要意义，例如知识图谱构建（Heindorf 等，2020；Khatib 等，2020）、信息抽取（Yang 等，2022；Xiang 和 Wang，2019）、问答（Bondarenko 等，2022；Sui 等，2022）、知识推理（Yao 等，2021；Feder 等，2022）等。图 1 展示了 Event StoryLine 语料库（ESC）（Caselli 和 Vossen，2017）中一篇犯罪主题文档里带有因果关系标签的已标注事件提及示例。其中，事件提及被标注为原始句子中最清晰地表达某一事件发生的一个或多个词，而因果关系既可以存在于句内事件提及之间，也可以存在于跨句事件提及之间。我们注意到，一个事件还可能包含若干具有特定角色的事件论元，例如事件的时间、地点和参与者，从而构成结构化事件。在本文中，我们仅使用事件提及来识别事件对之间的因果关系。

### 段落 2（图 1 图内示例文本）

**【EN】**
Prosecutors [release]e1 new video in [shooting trial]e2 of Brownsville mother of 12, Zurana Horton. Published: April 16, 2013 9:09 AM. Prosecutors have [released]e3 new video in the [trial]e4 of two men [accused]e5 of [killing]e6 a Brownsville mother of 12 in Oct. 2011. The video shows Zurana Horton [falling]e7 to the ground [surrounded]e8 by kids in the moments after [gunfire]e9 [erupted]e10 outside her children's school. Witnesses say Horton [died]e11 [trying]e12 to [shield]e13 several students. Authorities have [charged]e14 Andrew Lopez and Jonathan Carrasquillo with her [murder]e15.
（注：以上为图 1 图内文字，据 PDF 图像转写。图 1 同时给出三栏信息：Event Mention 事件提及列表；Causal Relation (Intra-Sentence) 句内因果关系，如 [accused]e5–[killing]e6、[accused]e5–[trial]e4、[falling]e7–[gunfire]e9、[shield]e13–[died]e11；Causal Relation (Cross-Sentence) 跨句因果关系，如 [accused]e5–[shooting trial]e2、[accused]e5–[died]e11、[accused]e5–[murder]e15、[died]e11–[charged]e14。）

**【中】**
检察官在 12 个孩子的母亲 Zurana Horton 的[枪击案审判]~e2~中[发布]~e1~了新视频。发布时间：2013 年 4 月 16 日 9:09。检察官公布了一段新视频，内容涉及两名于 2011 年 10 月被[指控]~e5~[杀害]~e6~这位 12 个孩子母亲的男子所受的[审判]~e4~。视频显示，Zurana Horton 在[枪声]~e9~于她孩子就读的学校外[爆发]~e10~之后[倒下]~e7~，[被]~e8~孩子们[围住]~e8~。目击者称，Horton 在[试图]~e12~[保护]~e13~数名学生时[死亡]~e11~。当局已[指控]~e14~Andrew Lopez 和 Jonathan Carrasquillo [谋杀]~e15~她。

> **Fig. 1.** Illustration of annotated event mentions and causality labels for a crime topic document in the ESC 0.9 corpus.
>
> **图 1.** ESC 0.9 语料库中一篇犯罪主题文档的已标注事件提及与因果关系标签示例。

### 段落 3

**【EN】**
Some methods have employed external knowledge bases to obtain causal knowledge for augmenting causality identification. For example, the FrameNet (Baker et al., 1998) contains abundant structured triples of events, entities, and relations. Both (Zuo et al., 2020) and (Liu et al., 2020) use such knowledge triplets in the FrameNet to boost representation learning. Moreover, the ConceptNet, a graph-structured knowledge base (Speer et al., 2017), as well as the WordNet (Miller, 1995) and VerbNet (Schuler, 2006), each a lexical knowledge base, have also been used to obtain external causal knowledge for the ECI task (Cao et al., 2021; Zuo et al., 2021a).

**【中】**
一些方法利用外部知识库获取因果知识，以增强因果关系识别。例如，FrameNet（Baker 等，1998）包含大量关于事件、实体和关系的结构化三元组。Zuo 等（2020）和 Liu 等（2020）都使用 FrameNet 中的这类知识三元组来提升表示学习。此外，图结构知识库 ConceptNet（Speer 等，2017），以及词汇知识库 WordNet（Miller，1995）和 VerbNet（Schuler，2006），也都被用于为 ECI 任务获取外部因果知识（Cao 等，2021；Zuo 等，2021a）。

### 段落 4

**【EN】**
Although these external knowledge bases can effectively augment causality detection, they are mainly used for identifying event causality in the sentence-level, but cannot fully utilize document-level contextual semantics for cross-sentence causality identification. Some recent methods design sophisticated neural models to learn a kind of document-level semantic representation for each event, such as the Rich Graph Convolutional Network (RichGCN) (Phu and Nguyen, 2021), Event Relational Graph Transformer (ERGO) (Chen et al., 2022), and Graph-based Event Structure Induction model (GESI) (Fan et al., 2022).

**【中】**
尽管这些外部知识库能够有效增强因果关系检测，但它们主要用于句子级的事件因果关系识别，无法充分利用文档级上下文语义来完成跨句因果关系识别。近期一些方法设计了复杂的神经模型，为每个事件学习一种文档级语义表示，例如富图卷积网络（RichGCN）（Phu 和 Nguyen，2021）、事件关系图 Transformer（ERGO）（Chen 等，2022）以及基于图的事件结构归纳模型（GESI）（Fan 等，2022）。

### 段落 5

**【EN】**
However, all these graph-based neural models have ignored the latent correlated cause-effect structure in a document, i.e., one cause (effect) with multiple effects (causes), such as the example illustrated in Fig. 1. We argue that the events' causal relations in a document are often interdependent and identifying one cause–effect pair may facilitate the identification of other causes (effects) with the same effect (cause). For example, the effect event [accused]e5 has multiple cause events including [shooting trial]e2, [killing]e6, [died]e11, and etc.; while identifying the causal relation between [killing]e6 and [accused]e5 may facilitate the identification of causal event pair ([accused]e5, [murder]e15), ([accused]e5, [trial]e4), and ([accused]e5, [died]e11), that have a same cause (effect) event [accused]e5.

**【中】**
然而，所有这些基于图的神经模型都忽略了文档中潜在的相关因果效应结构，即一个原因（结果）对应多个结果（原因），如图 1 所示的例子。我们认为，文档中事件的因果关系往往是相互依存的，识别一个因果对可能有助于识别与同一结果（原因）相关联的其他原因（结果）。例如，结果事件 [accused]~e5~ 拥有多个原因事件，包括 [shooting trial]~e2~、[killing]~e6~、[died]~e11~ 等；而识别 [killing]~e6~ 与 [accused]~e5~ 之间的因果关系，可能有助于识别具有相同原因（结果）事件 [accused]~e5~ 的那些因果事件对（[accused]~e5~，[murder]~e15~）、（[accused]~e5~，[trial]~e4~）和（[accused]~e5~，[died]~e11~）。

### 段落 6

**【EN】**
Besides, we argue that the identification of causal event pair with co-reference events can also benefit from such document correlated causal-effect structure learning. For example, the co-reference event [trial]e4 and [shooting trial]e2 both have causal relation with event [accused]e5, as illustrated in Fig. 1; While the cross-sentence causal relation between [accused]e5 and [shooting trial]e2 is more difficult to identify than the identification of intra-sentence event causality [accused]e5 and [trial]e4 for its discontinuous semantics. As such, identifying the intra-sentence event causality may also facilitate the identification of cross-sentence event causality with co-reference events.

**【中】**
此外，我们认为，对含有共指事件的因果事件对的识别同样能从这种文档相关因果效应结构的学习中获益。例如，如图 1 所示，共指事件 [trial]~e4~ 与 [shooting trial]~e2~ 都与事件 [accused]~e5~ 存在因果关系；而由于语义不连续，[accused]~e5~ 与 [shooting trial]~e2~ 之间的跨句因果关系比句内事件因果关系（[accused]~e5~ 与 [trial]~e4~）的识别更为困难。因此，识别句内事件因果关系也可能有助于识别带有共指事件的跨句事件因果关系。

### 段落 7

**【EN】**
Motivated from such considerations, we propose to model latent correlated causal-effect structure at the document-level by an event-hypergraph for jointly identifying causal relations. In contrast to conventional graph neural network where one relation edge can only connect two event nodes, the hyperedge in a hypergraph can connect more than two event nodes (Feng et al., 2019). As a result, the representation of multiple event nodes can be learned correlatively based on such kind of hyperedge. To this end, we propose utilizing hyperedges in a hypergraph to model the latent correlated cause–effect structure in a document for event causality identification.

**【中】**
受这些考虑的启发，我们提出用事件超图在文档级对潜在的相关因果效应结构进行建模，以联合识别因果关系。与传统图神经网络中一条关系边只能连接两个事件节点不同，超图中的超边可以连接两个以上的事件节点（Feng 等，2019）。因此，基于这类超边，多个事件节点的表示可以被关联地学习。为此，我们提出利用超图中的超边对文档中潜在的相关因果效应结构进行建模，以用于事件因果关系识别。

### 段落 8

**【EN】**
In this paper, we propose an event-hypergraph neural encoding model, called EHNEM, for document-level event causality identification. In EHNEM, we first use a pre-trained language model (PLM) to initialize event mentions' embeddings and obtain potential causal relation of each event-pair via a multilayer perceptron. To capture latent causal correlations, we construct a hypergraph by integrating potential causal relations for the same event as a hyperedge, in which each node represents an event and a hyperedge represents potential causal relations in between its connected event nodes. On the constructed event-hypergraph, we use a hypergraph convolutional network to learn the representation of each event node for final causality identification. Experiment results have shown that our proposed EHNEM significantly outperforms the state-of-the-art algorithms in terms of much higher F1 score on both EventStoryLine 0.9 corpus and English-MECI corpus. (Footnote 1: Source codes will be released later.)

**【中】**
（原文脚注 1：源代码将于以后发布。）

本文提出了一种称为 EHNEM 的事件超图神经编码模型，用于文档级事件因果关系识别。在 EHNEM 中，我们首先使用预训练语言模型（PLM）初始化事件提及的嵌入，并通过多层感知机获得每个事件对的潜在因果关系。为了捕捉潜在的因果相关性，我们将同一事件的潜在因果关系整合为一条超边来构建超图，其中每个节点表示一个事件，每条超边表示其连接的事件节点之间的潜在因果关系。在所构建的事件超图上，我们使用超图卷积网络学习每个事件节点的表示，用于最终的因果关系识别。实验结果表明，我们提出的 EHNEM 在 EventStoryLine 0.9 语料库和 English-MECI 语料库上均以更高的 F1 分数显著优于当前最先进的算法。

### 段落 9（主要贡献）

**【EN】**
Our main contributions are summarized as follows:

1. Propose an event-hypergraph neural encoding model (EHNEM) to model the correlated causal-effect structure by a hypergraph for document-level event causality identification.
2. Propose an event semantic encoding module to initialize events' embedding and detect potential causal relations of each event-pair.
3. Propose an event-hypergraph learning module to model correlated causal-effect structure and jointly identify multiple causal relations.
4. Experiment results on both EventStoryLine 0.9 corpus and English-MECI corpus validate the effectiveness of our proposed EHNEM.

**【中】**
我们的主要贡献总结如下：

1. 提出事件超图神经编码模型（EHNEM），通过超图对相关因果效应结构建模，用于文档级事件因果关系识别。
2. 提出事件语义编码模块，用于初始化事件嵌入并检测每个事件对的潜在因果关系。
3. 提出事件超图学习模块，用于建模相关因果效应结构并联合识别多个因果关系。
4. 在 EventStoryLine 0.9 语料库和 English-MECI 语料库上的实验结果验证了所提 EHNEM 的有效性。

### 段落 10（全文组织）

**【EN】**
The rest of this article is organized as follows: Section 2 reviews some approaches of knowledge-based, reasoning-based and graph-based event causality identification. Section 3 introduces our EHNEM model including event semantics encoding, event-hypergraph learning and causality classification module. We present the experiments settings in Section 4 and compare the performance of our EHNEM model with some discussion in Section 5. Finally, Section 6 concludes this article and discusses our future work.

**【中】**
本文其余部分组织如下：第 2 节回顾基于知识、基于推理和基于图的事件因果关系识别方法；第 3 节介绍我们的 EHNEM 模型，包括事件语义编码、事件超图学习和因果关系分类模块；第 4 节给出实验设置；第 5 节比较 EHNEM 模型的性能并展开讨论；最后，第 6 节总结全文并讨论未来的工作。

---

## 2. 相关工作 / Related work

### 2.1. 基于知识的因果关系识别 / Knowledge-based causality identification

#### 段落 1

**【EN】**
Existing knowledge bases, like the FrameNet (Baker et al., 1998), ConceptNet (Speer et al., 2017), and etc., have stored large-scale structured information that can provide some kind of abstract causal knowledge for causality reasoning. Some studies try to learn some causal patterns from external knowledge bases to implement a kind of knowledgeable event causality inference (Cao et al., 2021; Liu et al., 2020). For example, Cao et al. (2021) perform event causality reasoning based on the induced descriptive knowledge and relational path knowledge from the graph-structured knowledge base ConceptNet. Liu et al. (2020) enhance the ability of their neural model for previously unseen causal relation identification by mining some event-agnostic and context-specific patterns from the ConceptNet. Zuo et al. (2021a) learn some context-specific causal patterns from external causal statements based on a contrastive transfer learning framework.

**【中】**
现有的知识库，如 FrameNet（Baker 等，1998）、ConceptNet（Speer 等，2017）等，存储了大规模结构化信息，能够为因果推理提供某种抽象因果知识。一些研究尝试从外部知识库中学习因果模式，以实现一种知识驱动的事件因果推理（Cao 等，2021；Liu 等，2020）。例如，Cao 等（2021）基于从图结构知识库 ConceptNet 中归纳出的描述性知识和关系路径知识进行事件因果推理。Liu 等（2020）通过从 ConceptNet 中挖掘若干与事件无关（event-agnostic）且与上下文相关的模式，增强了其神经模型对未见因果关系的识别能力。Zuo 等（2021a）基于对比迁移学习框架，从外部因果陈述中学习了一些与上下文相关的因果模式。

#### 段落 2

**【EN】**
Besides, some studies directly use the large amount of elaborately created items in lexical knowledge bases for data expansion and model training augmentation (Zuo et al., 2020, 2021b). For example, Zuo et al. (2020) distantly label event causality sentence based on the synonyms of each annotated causal event pair extracted from two lexical knowledge bases WordNet (Miller, 1995) and VerbNet (Schuler, 2006). They further generate new causal sentences interactively for event causality data augmentation based on existing knowledge bases (Zuo et al., 2021b). These approaches, however, ignore the contextual information and semantic evidence in a document for event causality identification.

**【中】**
此外，一些研究直接利用词汇知识库中大量精心构建的条目进行数据扩展和模型训练增强（Zuo 等，2020，2021b）。例如，Zuo 等（2020）基于从 WordNet（Miller，1995）和 VerbNet（Schuler，2006）两个词汇知识库中为每个已标注因果事件对抽取的同义词，对事件因果句进行远程标注。他们进一步基于现有知识库以交互方式生成新的因果句子，用于事件因果关系数据增强（Zuo 等，2021b）。然而，这些方法忽略了文档中用于事件因果关系识别的上下文信息和语义证据。

### 2.2. 基于推理的因果关系识别 / Reasoning-based causality identification

#### 段落 1

**【EN】**
Some studies try to identify event causalities by relation reasoning like event relation chain reasoning (Yuan et al., 2023), event counterfactual reasoning (Mu and Li, 2023), and etc. For example, Yuan et al. (2023) design various types of attention mechanism to capture long-distance semantic dependence for an event mention in document and learn a kind of sparse event representation as intra-sentential features. They further constitute a possible reasoning chain based on intra-sentential event pairs for inter-sentential event causality reasoning. Mu and Li (2023) propose a counterfactual reasoning mechanism to estimate context-keywords bias and event-pairs bias and then subtract the biases from factual predictions for event causality identification. Zhang et al. (2024) propose a multi-task learning framework to enhance event causality identification with rationale and structure-aware causal question answering.

**【中】**
一些研究尝试通过关系推理来识别事件因果关系，例如事件关系链推理（Yuan 等，2023）、事件反事实推理（Mu 和 Li，2023）等。例如，Yuan 等（2023）设计了多种类型的注意力机制，以捕捉文档中事件提及的长距离语义依赖，并学习一种稀疏事件表示作为句内特征。他们进一步基于句内事件对构建可能的推理链，用于句间事件因果推理。Mu 和 Li（2023）提出了一种反事实推理机制，用于估计上下文—关键词偏置和事件对偏置，然后从事实预测中减去这些偏置，以进行事件因果关系识别。Zhang 等（2024）提出了一种多任务学习框架，借助理由生成（rationale）和结构感知的因果问答来增强事件因果关系识别。

#### 段落 2

**【EN】**
The recent pre-train, prompt, and predict paradigm models the probability of text in Pre-trained Language Models (PLMs) to perform a prediction task, which has achieved promising results for many NLP tasks (Seoh et al., 2021; Xiang et al., 2022) and has been successfully applied in the ECI task (Shen et al., 2022; Liu et al., 2020). For example, Liu et al. (2020) use an event mention masking generalization mechanism to encode some event causality patterns for causal relation reasoning. Shen et al. (2022) use a masked language model to predict a causality answer word between two event mentions for causal relation classification, assisted by two derivative prompt tasks to identify the explicit causality based on the causal cue word detection. Man et al. (2024) leverage an optimal transport to automatically select the most important sentences and words from full documents for input into the generative language model T5, which is trained to generate causal relations.

**【中】**
近期的"预训练、提示、预测"（pre-train, prompt, and predict）范式利用预训练语言模型（PLM）中文本的概率来执行预测任务，已在许多 NLP 任务上取得了良好效果（Seoh 等，2021；Xiang 等，2022），并被成功应用于 ECI 任务（Shen 等，2022；Liu 等，2020）。例如，Liu 等（2020）使用事件提及掩码泛化机制（event mention masking generalization）编码若干事件因果模式，以进行因果关系推理。Shen 等（2022）使用掩码语言模型预测两个事件提及之间的因果答案词，从而完成因果关系分类，并借助两个派生提示任务，通过因果线索词检测来识别显式因果关系。Man 等（2024）利用最优传输自动从完整文档中选择最重要的句子和词，输入生成式语言模型 T5，并训练其生成因果关系。

### 2.3. 基于图的因果关系识别 / Graph-based causality identification

#### 段落 1

**【EN】**
The graph-based approaches usually model the ECI task as a node classification problem and apply graph neural networks for learning event node representations from document-level contextual semantics (Phu and Nguyen, 2021; Cao et al., 2021; Fan et al., 2022; Pu et al., 2023; Hu et al., 2023; He et al., 2024). For example, Phu and Nguyen (2021) model diverse connections in between words of a document, like positional connection, syntactic dependency and etc., for the graph construction. They use a graph convolutional network to learn the event mention nodes' representations, and identify causalities through event node pair classification. Fan et al. (2022) build the explicit connections between events and their mentions, as well as their contexts, to construct a co-occurrence graph for event causal relation identification. Pu et al. (2023) predict causality-related events as a kind of event causal label information and construct an event pair interaction graph to learn the representation of each event pair node. Hu et al. (2023) utilize a graph neural network (GNN) based event aggregator to integrate event-centric structure information, and a long short term memory (LSTM) based path aggregator to capture event-associated structure information between two events for causal relation classification. He et al. (2024) introduce a heterogeneous graph interaction network to model the long-distance dependencies between events and propose a multi-granularity contrastive transfer learning module to align the causal representations across languages.

**【中】**
基于图的方法通常将 ECI 任务建模为节点分类问题，并应用图神经网络从文档级上下文语义中学习事件节点表示（Phu 和 Nguyen，2021；Cao 等，2021；Fan 等，2022；Pu 等，2023；Hu 等，2023；He 等，2024）。例如，Phu 和 Nguyen（2021）对文档词与词之间的多种连接（如位置连接、句法依存等）进行建模以构建图。他们使用图卷积网络学习事件提及节点的表示，并通过事件节点对分类来识别因果关系。Fan 等（2022）在事件及其提及、以及它们的上下文之间建立显式连接，构建共现图用于事件因果关系识别。Pu 等（2023）将因果关系相关事件预测为一种事件因果标签信息，并构建事件对交互图来学习每个事件对节点的表示。Hu 等（2023）利用基于图神经网络（GNN）的事件聚合器整合以事件为中心的结构信息，并利用基于长短期记忆网络（LSTM）的路径聚合器捕捉两个事件之间与事件相关的结构信息，用于因果关系分类。He 等（2024）引入异构图交互网络对事件之间的长距离依赖建模，并提出多粒度对比迁移学习模块，以实现跨语言因果表示的对齐。

#### 段落 2

**【EN】**
Besides node classification, some studies also infer the potential causal edges in an event graph for causal relation identification (Zhao et al., 2021; Chen et al., 2022; Tao et al., 2023; Chen et al., 2023; Liu et al., 2024). For example, Zhao et al. (2021) initialize event nodes' embeddings from a document-level encoder and use a graph inference mechanism for causal edge prediction. Chen et al. (2022) build an event relational graph where each node denotes a pair of events. They further use a graph transformer model to capture potential causal chains among nodes. However, all these graph-based methods have not jointly considered pairwise and document-wise event causality. Tao et al. (2023) generate event causality graph based on a pre-trained language model and perform structural discriminative training to further improve the understanding of event causality. Chen et al. (2023) predict and encode event centrality information into the constructed heterogeneous event interaction graph for causality reasoning. Liu et al. (2024) design an iterative learning and identifying framework that first construct an event causality graph, on which events' causal structure representations are updated for boosting causal identification.

**【中】**
除节点分类外，一些研究还通过推断事件图中的潜在因果边来识别因果关系（Zhao 等，2021；Chen 等，2022；Tao 等，2023；Chen 等，2023；Liu 等，2024）。例如，Zhao 等（2021）从文档级编码器初始化事件节点嵌入，并使用图推理机制进行因果边预测。Chen 等（2022）构建事件关系图，其中每个节点表示一对事件。他们进一步使用图 Transformer 模型捕捉节点之间潜在的因果链。然而，所有这些基于图的方法都没有联合考虑成对（pairwise）与文档级（document-wise）的事件因果关系。Tao 等（2023）基于预训练语言模型生成事件因果图，并进行结构化判别训练，以进一步提升对事件因果关系的理解。Chen 等（2023）预测事件中心性信息并将其编码到所构建的异构图事件交互图中，用于因果推理。Liu 等（2024）设计了一个迭代学习与识别框架，首先构建事件因果图，并在该图上更新事件的因果结构表示，以促进因果关系识别。

#### 段落 3

**【EN】**
The proposed EHNEM constructs a hypergraph to learn the causal correlations structure for event causality identification.

**【中】**
本文提出的 EHNEM 构建超图来学习因果相关结构，用于事件因果关系识别。

---

## 3. 方法 / Method

### 段落 1（模型总览）

**【EN】**
Fig. 2 illustrates our EHNEM model, including the event semantics encoding, event-hypergraph construction and learning, and document-level causality identification. The event semantics encoding uses a pre-trained language model to encode event semantics as well as predict potential causal relations of each event-pair for next correlated causal-effect structure learning. Then we construct a document-level causal hypergraph based on predicted causal relations to fine-tune events' representations by a hypergraph convolutional neural network. Finally, two kinds of representations are concatenated for document-level event causality identification.

**【中】**
图 2 展示了我们的 EHNEM 模型，包括事件语义编码、事件超图构建与学习、以及文档级因果关系识别。事件语义编码使用预训练语言模型编码事件语义，同时预测每个事件对的潜在因果关系，供后续的相关因果效应结构学习使用。然后，我们基于预测出的因果关系构建文档级因果超图，通过超图卷积神经网络对事件表示进行微调。最后，将两类表示拼接起来，用于文档级事件因果关系识别。

> **Fig. 2.** Illustration of our EHNEM model.
>
> **图 2.** 我们的 EHNEM 模型示意图。

### 3.1. 事件语义编码 / Event semantics encoding

#### 段落 1

**【EN】**
The event semantic encoding (ESE) module is to initialize events' representations and detect a potential causal relation for each event-pair. The ESE module is built upon a pre-trained language model (PLM), here the RoBERTa (Liu et al., 2019), to encode an initial representation for each event mention Evt_i, as well as predicting a potential causal relation Rel_k for each event-pair. The RoBERTa is an advanced PLM proposed by Facebook, which is pre-trained using a cloze task on a large amount of unlabeled text in an unsupervised way.

**【中】**
事件语义编码（ESE）模块的任务是初始化事件表示，并为每个事件对检测潜在因果关系。ESE 模块构建在预训练语言模型（PLM）之上，此处采用 RoBERTa（Liu 等，2019），为每个事件提及 Evt_i 编码初始表示，同时为每个事件对预测潜在因果关系 Rel_k。RoBERTa 是 Facebook 提出的一种先进 PLM，它以无监督方式在大量无标注文本上通过完形填空（cloze）任务进行预训练。

#### 段落 2

**【EN】**
As the event mentions are annotated by a few words in a sentence, we use the raw sentence of event mentions S_i and S_j for each event pair x_k = (Evt_i, Evt_j) to include the full contextual semantics, as the input of RoBERTa. Let S_i = [v_i^1, v_i^2, …, v_i^m] and S_j = [v_j^1, v_j^2, …, v_j^n] denote raw sentence containing the annotated event mention Evt_i and Evt_j, where Evt_i ⊂ S_i, Evt_j ⊂ S_j, where v_i's and v_j's are all vocabulary tokens. Note that, the two events of an event pair may also be in the same sentence, i.e. S_i = S_j, as such the input sentence T(x_k) of RoBERTa is denoted as:

**【中】**
由于事件提及由句子中的少数几个词标注而成，对于每个事件对 x_k = (Evt_i, Evt_j)，我们使用包含事件提及的原始句子 S_i 和 S_j 作为 RoBERTa 的输入，以纳入完整的上下文语义。令 S_i = [v_i^1, v_i^2, …, v_i^m]、S_j = [v_j^1, v_j^2, …, v_j^n] 分别表示包含已标注事件提及 Evt_i 与 Evt_j 的原始句子，其中 Evt_i ⊂ S_i、Evt_j ⊂ S_j，v_i 与 v_j 均为词表中的词元。注意，一个事件对中的两个事件也可能位于同一句中，即 S_i = S_j，此时 RoBERTa 的输入句子 T(x_k) 表示为：

$$
T(x_k) = [t_k^1, t_k^2, \ldots, t_k^L] = \left\{ \begin{array}{l l} [v_i^1, v_i^2, \ldots, v_i^m, v_j^1, v_j^2, \ldots, v_j^n], & if \quad S_i \neq S_j, \\ [v^1, v^2, \ldots, v^L], & if \quad S_i = S_j, \end{array} \right. \tag{1}
$$

> 其中，L 为输入句子的长度。

#### 段落 3

**【EN】**
After RoBERTa encoding, we obtain a hidden state for each token of the input sentence:

**【中】**
经过 RoBERTa 编码后，我们得到输入句子中每个词元的隐藏状态：

$$
\mathbf{H}_k = [\mathbf{h}_k^1, \mathbf{h}_k^2, \dots, \mathbf{h}_k^L] = RoBERTa([t_k^1, t_k^2, \dots, t_k^L]), \tag{2}
$$

#### 段落 4

**【EN】**
where H_k ∈ R^{L*d} and d is the dimension of hidden state. The event mentions for kth event pair are initially represented by an average pooling of their contained tokens' hidden state e_k^i = 1/l Σ h_k^i and e_k^j = 1/l Σ h_k^j, where e_k^i, e_k^j ∈ R^d. h_k^i, h_k^j are the hidden states of event mention contained tokens that are subsets of input sentence H_k and l is the length of event mention contained tokens. To detect the existence of a potential causal relation for each event-pair, we use a Multilayer Perceptron (MLP) to transform the concatenation of two event mention vectors to a causality probability. The transition function is:

**【中】**
其中 H_k ∈ R^{L×d}，d 为隐藏状态的维度。第 k 个事件对的事件提及首先通过对所含词元的隐藏状态做平均池化来表示：e_k^i = (1/l) Σ h_k^i，e_k^j = (1/l) Σ h_k^j，其中 e_k^i, e_k^j ∈ R^d；h_k^i、h_k^j 为输入句子 H_k 中属于事件提及的词元的隐藏状态，l 为事件提及所含词元的个数。为了检测每个事件对是否存在潜在因果关系，我们使用多层感知机（MLP）将两个事件提及向量的拼接映射为因果概率。其变换函数为：

$$
P_k = ReLU(\mathbf{W}_k[\mathbf{e}_k^i \parallel \mathbf{e}_k^j] + \mathbf{b}_k), \tag{3}
$$

> 其中 ∥ 表示拼接操作，W_k 与 b_k 为可学习参数。

#### 段落 5

**【EN】**
We pre-train the MLP and fine-tune the RoBERTa by the ground truth causal relations in the training dataset. The prediction with the highest overall F1 in the validation dataset is adopted as potential causal relations Rel_k for the event-hypergraph construction. The cross entropy loss is adopted as cost function.

**【中】**
我们利用训练集中的真实因果关系对 MLP 进行预训练，并对 RoBERTa 进行微调。在验证集上整体 F1 最高的预测结果被用作潜在因果关系 Rel_k，用于事件超图的构建。代价函数采用交叉熵损失。

---

### 3.2. 事件超图学习 / Event-hypergraph learning

**【EN】**
This module is to model correlated causal relations among events via a hypergraph and fine-tuning events' representations in the document-level.

**【中】**
该模块通过超图对事件之间的相关因果关系进行建模，并在文档级对事件表示进行微调。

#### 3.2.1. 事件超图的构建 / Construction of event-hypergraph

##### 段落 1

**【EN】**
We first construct an event-hypergraph 𝒢 = (ℰ, ℛ) for a document, where ℰ is the event node set and ℛ the hyperedge set. As an event mention is differently encoded by the RoBERTa in each event-pair after the ESE module, we use the average of their vectors e_n as the initial hypergraph node representation E^(0) = (e_1^0, …, e_n^0), where e_n ∈ R^d and E^(0) ∈ R^{n×d}. The hyperedges are used to model the possibly correlated causal-effect structure of different events. According to the ESE output, an event node may be with multiple potential causal relations to several other nodes, which are used to create a hyperedge.

**【中】**
我们首先为文档构建事件超图 𝒢 = (ℰ, ℛ)，其中 ℰ 为事件节点集合，ℛ 为超边集合。由于在 ESE 模块之后，同一事件提及在每个事件对中都会被 RoBERTa 以不同方式编码，我们取这些向量的平均值 e_n 作为初始超图节点表示 E^(0) = (e_1^0, …, e_n^0)，其中 e_n ∈ R^d，E^(0) ∈ R^{n×d}。超边用于对不同事件之间可能存在的相关因果效应结构进行建模。根据 ESE 的输出，一个事件节点可能与若干其他节点存在多条潜在因果关系，这些关系被用来创建一条超边。

##### 段落 2

**【EN】**
We construct the hypergraph incidence matrix A ∈ R^{|ℰ|×|ℛ|} with node as row and hyperedges as column. As illustrated in Fig. 2, each hyperedge has one vertex as the center node and connects several other nodes by potential causal relations Rel_k. Let R = (r_1, …, r_m) denotes the hyperedge set, r_m the hyperedge of mth vertex, and Ẽ_m denotes the node set of connected events to the mth vertex. The element of hypergraph incidence matrix A(e_n, r_m) can be formulated as:

**【中】**
我们构建超图关联矩阵 A ∈ R^{|ℰ|×|ℛ|}，以节点为行、超边为列。如图 2 所示，每条超边以一个顶点作为中心节点，并通过潜在因果关系 Rel_k 连接若干其他节点。令 R = (r_1, …, r_m) 表示超边集合，r_m 为第 m 个顶点对应的超边，Ẽ_m 表示与第 m 个顶点相连的事件节点集合。超图关联矩阵的元素 A(e_n, r_m) 可表示为：

$$
\mathbf{A}(e_n, r_m) = \left\{ \begin{array}{l l} 1, & if \quad e_n \in \tilde{\mathbf{E}}_m, \\ 0, & if \quad e_n \notin \tilde{\mathbf{E}}_m, \end{array} \right. \tag{4}
$$

> 其中 e_n 表示文档中的第 n 个事件节点。若事件 e_n 是图 𝒢 上超边 r_m 的一个节点，则 A(e_n, r_m) = 1；否则 A(e_n, r_m) = 0。

##### 段落 3

**【EN】**
Note that in the training phase, we use the ground truth relations for the event-hypergraph construction; While in the testing phase, we use the predicted ones by the ESE module.

**【中】**
需要注意的是，在训练阶段，我们使用真实关系来构建事件超图；而在测试阶段，我们使用 ESE 模块预测出的关系。

#### 3.2.2. 相关因果效应结构学习 / Correlated causal-effect structure learning

##### 段落 1

**【EN】**
On the event-hypergraph, we adopt a hypergraph convolution network (HGNN) (Feng et al., 2019) to fine-tune the document-level representation for each event node. The HGNN employs a kind of flexible hyperedges to connect multiple event nodes (usually more than two) and construct a hypergraph for convolutional operations. This enables the HGNN to learn hidden layer representation considering the high-order data structure of the hypergraph. The transition function is as follows:

**【中】**
在事件超图上，我们采用超图卷积网络（HGNN）（Feng 等，2019）对每个事件节点的文档级表示进行微调。HGNN 采用一类灵活的超边来连接多个（通常多于两个）事件节点，并构建超图以执行卷积运算。这使得 HGNN 能够在考虑超图高阶数据结构的情况下学习隐藏层表示。其变换函数如下：

$$
\mathbf{E}^{(l)} = \sigma(\mathbf{D}_e^{-\frac{1}{2}} \mathbf{A} \mathbf{I} \mathbf{D}_r^{-1} \mathbf{A}^{\top} \mathbf{D}_e^{-\frac{1}{2}} \mathbf{E}^{(l-1)} \boldsymbol{\Theta}^{(l-1)}), \tag{5}
$$

> 其中 σ 为 ReLU 激活函数，I 为单位矩阵，Θ ∈ R^{d×d} 为可学习参数。D_e 与 D_r 分别表示节点度与超边度的对角矩阵，用于归一化。对于顶点 e ∈ ℰ 和超边 r ∈ ℛ，其度计算如下：

$$
d(e) = \sum_{r \in \mathcal{R}} w(r) a(e, r), \tag{6}
$$

$$
d(r) = \sum_{e \in \mathcal{E}} a(e, r), \tag{7}
$$

> 其中 a(e, r) 为关联矩阵 A 的元素，w(r) 为矩阵 I 对角线上的元素，表示超边 r 的权重。（原文如此；按 HGNN 原式，此处应为超边权重矩阵 W，式 (5) 中的 I 为单位矩阵。）

##### 段落 2

**【EN】**
According to Eq. (5), the process of hypergraph convolution layer is as follows:

**【中】**
根据式（5），超图卷积层的处理过程如下：

- **【EN】** Step 1: The learnable parameter matrix at l−1 layer Θ^(l−1) is used to transform the l−1 layer node features E^(l−1) to the new node features. Note that the initial node features E^(0) is obtained from the event semantic encoding module;
- **【中】** 步骤 1：使用第 l−1 层的可学习参数矩阵 Θ^(l−1) 将第 l−1 层的节点特征 E^(l−1) 变换为新的节点特征。注意，初始节点特征 E^(0) 来自事件语义编码模块；
- **【EN】** Step 2: The new node features on the hyperedges are gathered to obtain the hyperedge features by the multiplication of A^⊤;
- **【中】** 步骤 2：通过乘以 A^⊤ 汇聚超边上的新节点特征，得到超边特征；
- **【EN】** Step 3: The related hyperedge features are associated to obtain the l layer node features E^(l), which is implemented by multiplying incidence matrix A;
- **【中】** 步骤 3：将相关的超边特征关联起来，得到第 l 层的节点特征 E^(l)，这一步通过乘以关联矩阵 A 实现。

##### 段落 3

**【EN】**
The outputs after l convolution layers include the document-level events' representation E^(l+1) ∈ R^{n×d}, and the last layer outputs E^(l) = (e_1^l, …, e_n^l) are used for the final causal relation classification.

**【中】**
经过 l 个卷积层后的输出包含文档级事件表示 E^(l+1) ∈ R^{n×d}，其中最后一层的输出 E^(l) = (e_1^l, …, e_n^l) 用于最终的因果关系分类。

### 3.3. 事件因果关系分类 / Event causality classification

#### 段落 1

**【EN】**
We argue that the initial event-hypergraph nodes' encodings from the ESE module output contain a kind of local contextual semantics for each event mention, which are also useful for final causal relation identification. To this end, we concatenate the representations learned from the event-hypergraph E^(l) with its initial representation E^(0) as the final representation for an event pair v_k ∈ R^{4d} for final causal relation classification. The causality classifier is a fully connected layer with softmax to transform each event-pair representation v_k to a probability distribution ŷ for predicting the event causal relation:

**【中】**
我们认为，来自 ESE 模块输出的初始事件超图节点编码包含了每个事件提及的一类局部上下文语义，这些语义对最终的因果关系识别同样有用。为此，我们将从事件超图学习到的表示 E^(l) 与其初始表示 E^(0) 拼接，作为事件对的最终表示 v_k ∈ R^{4d}，用于最终的因果关系分类。因果分类器是一个带 softmax 的全连接层，它将每个事件对表示 v_k 变换为概率分布 ŷ，以预测事件因果关系：

$$
\hat{\mathbf{y}} = softmax(\mathbf{W}_c \mathbf{v}_k + \mathbf{b}_c), \tag{8}
$$

> 其中 W_c、b_c 为可学习参数。

#### 段落 2

**【EN】**
The EHNEM model is trained at the document-level. We compute the cross entropy loss for each event-pair in a document and their summation is used as the cost function:

**【中】**
EHNEM 模型在文档级进行训练。我们为文档中的每个事件对计算交叉熵损失，并将其求和作为代价函数：

$$
J(\theta) = -\frac{1}{K}\sum_{k=1}^{K} \mathbf{y}^{(k)} \log(\hat{\mathbf{y}}^{(k)}) + \lambda \|\theta\|^2, \tag{9}
$$

> 其中 y^(k) 与 ŷ^(k) 分别为第 k 个事件对的真实标签与预测标签，λ 与 θ 分别为正则化超参数与模型参数。

---

## 4. 实验设置 / Experiment settings

### 段落 1

**【EN】**
This section presents our experiment settings, including the EventStoryLine and MECI dataset, competitors, and parameter settings.

**【中】**
本节介绍我们的实验设置，包括 EventStoryLine 和 MECI 数据集、对比方法以及参数设置。

### 4.1. 数据集 / Datasets

#### 段落 1

**【EN】**
Our experiments are conducted on two widely used document-level event causality identification datasets, that is, the EventStoryLine 0.9 Corpus (ESC) (Caselli and Vossen, 2017) and the English dataset in Multilingual Event Causality Identification (English-MECI) Corpus (Lai et al., 2022).

**【中】**
我们的实验在两个被广泛使用的文档级事件因果关系识别数据集上进行，即 EventStoryLine 0.9 语料库（ESC）（Caselli 和 Vossen，2017）和多语言事件因果关系识别语料库中的英文数据集（English-MECI）（Lai 等，2022）。

#### 段落 2

**【EN】**
EventStoryLine is a widely used document-level event causality dataset for the ECI task. The ESC dataset contains 22 topics, 258 documents and in total 5334 event mentions from various news web-sites. A total number of 5625 event pairs are annotated with causal relations (1770 intra-sentence and 3855 cross-sentence). Following the standard data splitting (Gao et al., 2019), we use the last two topics as the development set, and conduct 5-fold cross-validation on the remaining 20 topics. The average results of precision (P), recall (R), and F1 score are adopted as performance metrics.

**【中】**
EventStoryLine 是 ECI 任务中广泛使用的文档级事件因果关系数据集。ESC 数据集包含来自各类新闻网站的 22 个主题、258 篇文档，共 5334 个事件提及。共有 5625 个事件对标注了因果关系（1770 个句内、3855 个跨句）。遵循标准数据划分方式（Gao 等，2019），我们将最后两个主题作为开发集，并在其余 20 个主题上进行 5 折交叉验证。采用精确率（P）、召回率（R）和 F1 分数的平均值作为性能指标。

（原文脚注 2：https://github.com/tommasoc80/EventStoryLine）

#### 段落 3

**【EN】**
MECI is a multi-language event causality identification dataset, and we conduct our experiments on English language dataset (English-MECI), which contains 438 documents from 5 topics on Wikipedia. There are in total 8732 event mentions in the English-MECI corpus. A total number of 2050 event pairs are annotated with causal relations, among which 1889 event pairs are from intra-sentence event pairs and 161 causal relations are from cross-sentence event pairs. Following the standard data splitting provided by Lai et al. (2022), 263 documents are used as the training set, 88 as the development set and 87 as the testing set. We randomly select 5 different initialization seeds and averaged the results of precision (P), recall (R), and F1 score in our experiments.

**【中】**
MECI 是一个多语言事件因果关系识别数据集，我们在其中的英文数据集（English-MECI）上开展实验，该数据集包含来自维基百科 5 个主题的 438 篇文档。English-MECI 语料库中共有 8732 个事件提及。共有 2050 个事件对标注了因果关系，其中 1889 个事件对来自句内事件对，161 个因果关系来自跨句事件对。遵循 Lai 等（2022）提供的标准数据划分，263 篇文档用作训练集，88 篇用作开发集，87 篇用作测试集。我们在实验中随机选择 5 个不同的初始化种子，并对精确率（P）、召回率（R）和 F1 分数的结果取平均。

（原文脚注 3：https://github.com/nlp-uoregon/meci-dataset）

### 4.2. 对比方法 / Competitors

**【EN】**
We compare with the following competitors:

**【中】**
我们与以下对比方法进行比较：

- **【EN】** **ILP** (Gao et al., 2019) uses integer linear programming to identify causal relations based on document-level causal structures constraints.
  **【中】** **ILP**（Gao 等，2019）基于文档级因果结构约束，使用整数线性规划识别因果关系。
- **【EN】** **RichGCN** (Phu and Nguyen, 2021) uses a graph convolutional network to learn a document context-augmented representation of event-pairs.
  **【中】** **RichGCN**（Phu 和 Nguyen，2021）使用图卷积网络学习事件对的文档上下文增强表示。
- **【EN】** **GESI** (Fan et al., 2022) builds an event co-reference graph to identify event causal relation by a graph convolutional network.
  **【中】** **GESI**（Fan 等，2022）构建事件共指图，通过图卷积网络识别事件因果关系。
- **【EN】** **ERGO** (Chen et al., 2022) builds an event relational graph using an event-pair as a node to capture causation transitivity via a transformer-like neural network.
  **【中】** **ERGO**（Chen 等，2022）以事件对为节点构建事件关系图，通过类 Transformer 神经网络捕捉因果传递性。
- **【EN】** **CHEER** (Chen et al., 2023) builds a heterogeneous event interaction graph to integrate event centrality information for causality reasoning.
  **【中】** **CHEER**（Chen 等，2023）构建异构图事件交互图，整合事件中心性信息用于因果推理。
- **【EN】** **SENDIR** (Yuan et al., 2023) constitutes a reasoning chain based on intra-sentential event pairs for inter-sentential event causality reasoning.
  **【中】** **SENDIR**（Yuan 等，2023）基于句内事件对构建推理链，用于句间事件因果推理。
- **【EN】** **KnowDis** (Zuo et al., 2020) uses external lexicon knowledge to augment event causality data.
  **【中】** **KnowDis**（Zuo 等，2020）利用外部词汇知识增强事件因果关系数据。
- **【EN】** **KnowMMR** (Liu et al., 2020) uses external knowledge to mine some event causality patterns.
  **【中】** **KnowMMR**（Liu 等，2020）利用外部知识挖掘若干事件因果模式。
- **【EN】** **CauSeRL** (Zuo et al., 2021a) adopts a contrastive strategy to transfer learned external causal statements.
  **【中】** **CauSeRL**（Zuo 等，2021a）采用对比策略迁移已学习的外部因果陈述。
- **【EN】** **LearnDA** (Zuo et al., 2021b) leverages existing knowledge bases to interactively generate training data.
  **【中】** **LearnDA**（Zuo 等，2021b）利用现有知识库以交互方式生成训练数据。
- **【EN】** **LSIN** (Cao et al., 2021) uses a graph induction model to learn external structural and relational knowledge.
  **【中】** **LSIN**（Cao 等，2021）使用图归纳模型学习外部结构知识与关系知识。
- **【EN】** **DPJL** (Shen et al., 2022) leverages two derivative prompt tasks to identify the explicit and implicit causality.
  **【中】** **DPJL**（Shen 等，2022）利用两个派生提示任务识别显式与隐式因果关系。
- **【EN】** **CF-ECI** (Mu and Li, 2023) estimates context-keywords bias and event-pairs bias to identify causal relations by counterfactual reasoning.
  **【中】** **CF-ECI**（Mu 和 Li，2023）通过反事实推理估计上下文—关键词偏置与事件对偏置以识别因果关系。
- **【EN】** **ECLEP** (Pu et al., 2023) integrates event causality-related information into event pair interaction graph.
  **【中】** **ECLEP**（Pu 等，2023）将事件因果关系相关信息整合到事件对交互图中。

**【EN】**
We note that not all of these competitors are designed for document-level causal relation identification, and some are only applicable for intra-sentence event-pair causality identification.

**【中】**
我们注意到，这些对比方法并非都为文档级因果关系识别而设计，其中一些仅适用于句内事件对因果关系识别。

### 4.3. 参数设置 / Parameter settings

**【EN】**
We use the pre-trained RoBERTa (Liu et al., 2019) model with 768-dimension base version provided by the HuggingFace transformers (Wolf et al., 2020). The RoBERTa is an advanced PLM proposed by Facebook, which is pre-trained by a cloze task on a mass of unlabeled text in an unsupervised way. We run PyTorch framework with CUDA on NVIDIA GTX 3090 GPUs. The learning rate is set to 1e−5, the hypergraph layer to 2, the causal connection threshold for hyperedge construction to 0.7, and all trainable parameters are randomly initialized from normal distributions. We use the Adam optimizer with L2 regularization and combine dropout for model training.

**【中】**
我们使用 HuggingFace transformers（Wolf 等，2020）提供的 768 维 base 版本预训练 RoBERTa（Liu 等，2019）模型。RoBERTa 是 Facebook 提出的一种先进 PLM，以无监督方式在大量无标注文本上通过完形填空任务预训练。我们在配备 NVIDIA GTX 3090 GPU 的机器上，使用带 CUDA 的 PyTorch 框架运行。学习率设为 1e−5，超图层数设为 2，用于构建超边的因果连接阈值设为 0.7，所有可训练参数均从正态分布随机初始化。我们使用带 L2 正则化的 Adam 优化器，并结合 dropout 进行模型训练。

（原文脚注 4：https://github.com/huggingface/transformers；脚注 5：https://github.com/pytorch/fairseq/；脚注 6：pytorch.org）

---

## 5. 结果与分析 / Results and analysis

### 5.1. 总体结果 / Overall results

#### 段落 1

**【EN】**
Tables 1 and 2 compare the overall performance between our EHNEM and the competitors on the ESC corpus; While Table 3 compares the performance on the English-MECI corpus. The competitors in Tables 1 and 3 have reported all intra-sentence, cross-sentence, and overall results on ESC and English-MECI datasets; While the competitors in Table 2 have only reported the intra-sentence results on the ESC dataset, respectively. Besides, we note that the competitors in Table 1 all exploit some document-level information to encode event mentions for causality classification; While the competitors in Table 2 all use some kind of external knowledge to enhance event causality identification.

**【中】**
表 1 和表 2 比较了我们的 EHNEM 与各对比方法在 ESC 语料库上的总体性能；表 3 比较了在 English-MECI 语料库上的性能。表 1 和表 3 中的对比方法均报告了在 ESC 和 English-MECI 数据集上的句内、跨句及整体结果；而表 2 中的对比方法则分别只报告了在 ESC 数据集上的句内结果。此外，我们注意到表 1 中的所有对比方法都利用了某种文档级信息来编码事件提及以进行因果关系分类；而表 2 中的所有对比方法都使用了某种外部知识来增强事件因果关系识别。

#### 段落 2

**【EN】**
The first observation is that our EHNEM achieves significant improvements over all competitors in terms of much higher F1 score for intra-sentence, cross-sentence, and overall causal relation classification in Table 1, even some of these competitors have used graph-based neural networks to learn a document-wise event representation with a large scale of trainable parameters. The outstanding performance of our EHNEM can be attributed to the exploitation of correlated cause–effect structure in a document for jointly identify multiple causal relations with the same event mention; While these competitors identify causal relations on per event-pair basis.

**【中】**
第一个观察是：在表 1 中，我们的 EHNEM 在句内、跨句和整体因果关系分类上都以更高的 F1 分数显著优于所有对比方法，尽管其中一些对比方法使用了基于图的神经网络、以大量可训练参数学习文档级事件表示。EHNEM 的突出性能可归因于：它利用了文档中的相关因果效应结构，联合识别与同一事件提及相关的多个因果关系；而这些对比方法则是在逐个事件对的基础上识别因果关系。

#### 表 1

> **Table 1.** Overall results of comparison models for document-level event causality identification on the ESC corpus.
>
> **表 1.** 各对比模型在 ESC 语料库上进行文档级事件因果关系识别的总体结果。

| Model / 模型                | Intra-Sentence 句内 P (%) | R (%)          | F1 (%)         | Cross-Sentence 跨句 P (%) | R (%)          | F1 (%)         | Overall 整体 P (%) | R (%)          | F1 (%)         |
| --------------------------- | ------------------------- | -------------- | -------------- | ------------------------- | -------------- | -------------- | ------------------ | -------------- | -------------- |
| ILP (NAACL, 2019)           | 38.8                      | 52.4           | 44.6           | 35.1                      | 48.2           | 40.6           | 36.2               | 49.5           | 41.9           |
| RichGCN (NAACL, 2021)       | 49.2                      | 63.0           | 55.2           | 39.2                      | 45.7           | 42.2           | 42.6               | 51.3           | 46.6           |
| GESI (SIGIR, 2022)          | -                         | -              | 50.3           | -                         | -              | 49.3           | -                  | -              | 49.4           |
| ERGO (COLING, 2022)         | 57.5                      | 72.0           | 63.9           | 51.6                      | 43.3           | 47.1           | 48.6               | 53.4           | 50.9           |
| CHEER (ACL, 2023)           | 56.9                      | 69.6           | 62.6           | 45.2                      | 52.1           | 48.4           | 49.7               | 53.3           | 51.4           |
| SENDIR (ACL, 2023)          | 65.8                      | 66.7           | 66.2           | 33.0                      | 90.0           | 48.3           | 37.8               | 82.8           | 51.9           |
| **Our EHNEM（本文）** | **63.2**            | **70.4** | **66.6** | **62.3**            | **59.9** | **61.0** | **62.6**     | **63.1** | **62.8** |

#### 表 2

> **Table 2.** Intra-sentence results of event causality identification on the ESC corpus.
>
> **表 2.** 在 ESC 语料库上进行事件因果关系识别的句内结果。

| Model / 模型                 | Precision 精确率 | Recall 召回率   | F1 score F1 分数 |
| ---------------------------- | ---------------- | --------------- | ---------------- |
| KnowDis (COLING, 2020)       | 39.7%            | 66.5%           | 49.7%            |
| KnowMMR (IJCAI, 2020)        | 41.9%            | 62.5%           | 50.1%            |
| CauSeRL (ACL-Findings, 2021) | 41.9%            | 69.0%           | 52.1%            |
| LearnDA (ACL, 2021)          | 42.2%            | 69.8%           | 52.6%            |
| LSIN (ACL, 2021)             | 47.9%            | 58.1%           | 52.5%            |
| DPJL (COLING, 2022)          | 65.3%            | 70.8%           | 67.9%            |
| CF-ECI (ACL-Short, 2023)     | 47.1%            | 66.4%           | 55.1%            |
| ECLEP (ACL-Findings, 2023)   | 49.3%            | 68.1%           | 57.1%            |
| **Our EHNEM（本文）**  | **63.2%**  | **70.4%** | **66.6%**  |

#### 表 3

> **Table 3.** Overall results of comparison models for document-level event causality identification on the English-MECI corpus.
>
> **表 3.** 各对比模型在 English-MECI 语料库上进行文档级事件因果关系识别的总体结果。

| Model / 模型                | Intra-Sentence 句内 P (%) | R (%)          | F1 (%)         | Cross-Sentence 跨句 P (%) | R (%)          | F1 (%)         | Overall 整体 P (%) | R (%)          | F1 (%)         |
| --------------------------- | ------------------------- | -------------- | -------------- | ------------------------- | -------------- | -------------- | ------------------ | -------------- | -------------- |
| MECI-Base (COLING, 2022)    | -                         | -              | -              | -                         | -              | -              | 48.7               | 59.9           | 53.7           |
| RichGCN (NAACL, 2021)       | 62.0                      | 68.8           | 65.2           | 8.8                       | 21.9           | 12.6           | 52.7               | 64.8           | 58.1           |
| DPJL (COLING, 2022)         | 67.0                      | 81.7           | 73.6           | -                         | -              | -              | -                  | -              | -              |
| ERGO (COLING, 2022)         | 60.5                      | 73.1           | 66.2           | 8.4                       | 34.4           | 13.5           | 47.9               | 69.8           | 56.8           |
| **Our EHNEM（本文）** | **65.7**            | **80.1** | **72.2** | **22.9**            | **12.5** | **16.2** | **63.9**     | **74.3** | **68.7** |

#### 段落 3

**【EN】**
The second observation is that the improvement of our EHNEM in cross-sentence causality identification (10%+) is much more significant than that of intra-sentence (2%+). This can be attributed to its modeling the interdependence of both intra-sentence and cross-sentence causal relations with the same event mention by a hyperedge, so that the intra-sentence event causality with abundant contextual semantics can facilitate the identification of cross-sentence causal relations. Indeed, an intra-sentence event-pair is often mentioned with an explicit relation description in its contextual semantics, it is not unexpected to perform better than the cross-sentence relation identification.

**【中】**
第二个观察是：我们的 EHNEM 在跨句因果关系识别上的提升（10%+）远大于句内（2%+）。这可归因于它通过超边对与同一事件提及相关的句内和跨句因果关系的相互依赖性进行建模，从而使具有丰富上下文语义的句内事件因果关系能够促进跨句因果关系的识别。事实上，句内事件对通常在其上下文语义中带有明确的关系描述，因此其表现优于跨句关系识别并不意外。

#### 段落 4

**【EN】**
Table 2 compares the intra-sentence performance between our EHNEM and the competitors that have only reported intra-sentence results. It can be observed that our EHNEM can outperform most intra-sentence competitors, except for a small performance gap with the DPJL. The better performance of DPJL might be attributed to its explicitly using a causal cue word for event causality identification. However, such a cue word does not exist in a cross-sentence event-pair. As such, it cannot be applied in the cross-sentence case.

**【中】**
表 2 比较了我们的 EHNEM 与仅报告句内结果的对比方法在句内性能上的差异。可以看出，我们的 EHNEM 优于大多数句内对比方法，仅与 DPJL 存在小幅性能差距。DPJL 表现更好可能归因于它显式地使用因果线索词进行事件因果关系识别。然而，这种线索词在跨句事件对中并不存在，因此该方法无法应用于跨句情形。

#### 段落 5

**【EN】**
We reproduce some competitors on the English-MECI dataset in Table 3 for fair comparison, including the state-of-the-art algorithms of overall results in Table 1 (ERGO) and intra-sentence results in Table 2 (DPJL), as well as a most representative graph convolutional network based document-level information encoding algorithm (RichGCN) for event causality identification. The MECI-base is a baseline algorithm provided by (Lai et al., 2022). We can observe similar results with the experiments on the ESC dataset in Tables 1 and 2, that is, our EHNEM has achieved significant performance improvements over all competitors, except for a slight performance gap with the DPJL in intra-sentence event causality identification for its utilization of more prior knowledge.

**【中】**
为进行公平比较，我们在表 3 中于 English-MECI 数据集上复现了一些对比方法，包括表 1 中整体结果最优的算法（ERGO）、表 2 中句内结果最优的算法（DPJL），以及最具代表性的基于图卷积网络的文档级信息编码算法（RichGCN）。MECI-base 是 Lai 等（2022）提供的基线算法。我们可以观察到与表 1、表 2 中 ESC 数据集实验相似的结果，即我们的 EHNEM 相较所有对比方法都取得了显著的性能提升，仅在句内事件因果关系识别上与 DPJL 存在小幅差距，因为后者利用了更多先验知识。

#### 段落 6

**【EN】**
We can also observe that the MECI-base cannot outperform the RichGCN, ERGO, and our EHNEM in Table 3. This is not unexpected, as the MECI-base is a baseline model that simply uses a PLM to encode the semantic of two input event mentions for causal relation classification. Besides, it can be observed that the performance of both our EHNEM and the competitors are quite poor in cross-sentence event causality identification. This might be attributed to the fact that the English-MECI corpus contains very few instances of cross-sentence causal relations, leading to an insufficient training of neural network models. According to our statistics, only 93 cross-sentence event causal pairs are available for model training, with 36 for validation and 32 for testing.

**【中】**
我们还可以观察到，表 3 中 MECI-base 的表现不及 RichGCN、ERGO 和我们的 EHNEM。这并不意外，因为 MECI-base 只是一个简单地使用 PLM 编码两个输入事件提及语义以进行因果关系分类的基线模型。此外，可以观察到我们的 EHNEM 与各对比方法在跨句事件因果关系识别上的表现都相当差。这可能归因于 English-MECI 语料库中跨句因果关系实例极少，导致神经网络模型训练不足。根据我们的统计，仅有 93 个跨句事件因果对可用于模型训练，验证集 36 个，测试集 32 个。

### 5.2. 消融研究 / Ablation study

#### 段落 1

**【EN】**
Module ablation: To examine the module effectiveness, we design the following ablation studies.

**【中】**
模块消融：为检验各模块的有效性，我们设计了如下消融实验。

- **【EN】** EHNEM (layer) is our EHNEM model using different hypergraph convolution layers.
  **【中】** EHNEM（layer）是采用不同超图卷积层数的 EHNEM 模型。
- **【EN】** EHNEM w/o HGNN is our EHNEM model without the event-hypergraph learning, which only uses the event semantic encoding outputs as the final prediction.
  **【中】** EHNEM w/o HGNN 是去掉事件超图学习的 EHNEM 模型，仅使用事件语义编码的输出作为最终预测。
- **【EN】** EHNEM w GCN is our EHNEM model that uses a graph convolutional network (layer=2) to replace the hypergraph convolution neural network for document causal structure learning.
  **【中】** EHNEM w GCN 是用图卷积网络（layer=2）替换超图卷积神经网络来进行文档因果结构学习的 EHNEM 模型。

#### 表 4

> **Table 4.** Ablation results of different hypergraph convolution layers for event causality identification on the ESC corpus.
>
> **表 4.** 不同超图卷积层用于事件因果关系识别在 ESC 语料库上的消融结果。

| Model / 模型                            | Intra-Sentence 句内 P (%) | R (%)          | F1 (%)         | Cross-Sentence 跨句 P (%) | R (%)          | F1 (%)         | Overall 整体 P (%) | R (%)          | F1 (%)         |
| --------------------------------------- | ------------------------- | -------------- | -------------- | ------------------------- | -------------- | -------------- | ------------------ | -------------- | -------------- |
| EHNEM (layer = 1)                       | 63.1                      | 68.2           | 65.5           | 63.1                      | 57.7           | 60.2           | 63.1               | 61.0           | 61.9           |
| EHNEM (layer = 2)                       | 63.2                      | 70.4           | 66.6           | 62.3                      | 59.9           | 61.0           | 62.6               | 63.1           | 62.8           |
| EHNEM (layer = 3)                       | 62.8                      | 70.0           | 66.2           | 61.2                      | 59.7           | 60.3           | 61.7               | 62.9           | 62.2           |
| EHNEM (layer = 4)                       | 60.8                      | 70.8           | 65.4           | 60.6                      | 60.2           | 60.3           | 60.7               | 63.5           | 62.0           |
| EHNEM w/o HGNN                          | 70.3                      | 60.5           | 64.8           | 68.3                      | 43.2           | 52.5           | 69.0               | 48.6           | 56.7           |
| EHNEM w GCN                             | 63.0                      | 69.1           | 65.8           | 61.8                      | 57.2           | 59.3           | 62.3               | 60.9           | 61.5           |
| **EHNEM full (Ours)（完整模型）** | **63.2**            | **70.4** | **66.6** | **62.3**            | **59.9** | **61.0** | **62.6**     | **63.1** | **62.8** |

#### 段落 2

**【EN】**
Table 4 presents results of our ablation study. We can first observe that the EHNEM (layer=1) cannot outperform the others using more hypergraph convolution layers, for its insufficient training with fewer convolution layers and trainable parameters. We can also observe that the EHNEM (layer=2) achieves the best performance and the EHNEM with more convolution layers cannot further improve the performance. This might be explained by the over-smoothing effect of a hypergraph neural network. That is, more convolution layers suggest to involve more farther away nodes into the message passing and aggregation for event representation learning.

**【中】**
表 4 给出了消融研究的结果。首先可以观察到，EHNEM（layer=1）由于卷积层数和可训练参数较少、训练不充分，未能优于使用更多超图卷积层的其他变体。我们还可以观察到，EHNEM（layer=2）取得了最佳性能，而层数更多的 EHNEM 无法进一步提升性能。这可以用超图神经网络的过平滑（over-smoothing）效应来解释，即更多的卷积层意味着在事件表示学习时会引入更多距离更远的节点参与消息传递与聚合。

#### 段落 3

**【EN】**
The second group of Table 4 presents the results of ablation modules. We observe that neither the EHNEM w/o HGNN nor the EHNEM w GCN can outperform the full EHNEM model. This, however, is not unexpected. As the EHNEM w/o HGNN totally ignores the document causal structure that can be used to learn the interdependence of event causal relations from the whole document; While although the EHNEM w GCN uses a conventional graph convolutional network to learn a kind of document-level event representations, it is unable to learn the document correlated causal-effect structure for its using of simple edges that can only connect two event nodes, as such it cannot benefit from the other causal relation edges. As the EHNEM w GCN also includes the event semantic encoding module and uses a graph convolutional network for document causal structure learning, it achieves comparable performance to the EHNEM full (Ours) model in intra-sentence causality identification. However, the performance gap in inter-sentence (−1.7%) and overall (−1.3%) causality identification remains evident.

**【中】**
表 4 的第二组给出了消融模块的结果。我们观察到，EHNEM w/o HGNN 和 EHNEM w GCN 都未能超过完整的 EHNEM 模型。这并不意外。因为 EHNEM w/o HGNN 完全忽略了可用于从整篇文档学习事件因果关系相互依赖性的文档因果结构；而 EHNEM w GCN 虽然使用传统图卷积网络学习了一类文档级事件表示，但由于其使用的是只能连接两个事件节点的简单边，无法学习文档的相关因果效应结构，因而不能从其他因果关系边中获益。由于 EHNEM w GCN 同样包含事件语义编码模块并使用图卷积网络进行文档因果结构学习，它在句内因果关系识别上取得了与完整 EHNEM（Ours）模型相当的性能。然而，在句间（−1.7%）和整体（−1.3%）因果关系识别上的性能差距仍然明显。

#### 段落 4

**【EN】**
Document causal structure: To further investigate the impact of correlated cause–effect structure, we plot the overall performance of our EHNEM against different hyperedge degrees in Fig. 3(a).

**【中】**
文档因果结构：为进一步考察相关因果效应结构的影响，我们在图 3(a) 中绘制了 EHNEM 整体性能随不同超边度（hyperedge degree）变化的情况。

#### 段落 5

**【EN】**
The degree of an event hyperedge represents how many event nodes it connects, that is, the causal relation between its connected nodes are identified jointly. It is not unexpected that our EHNEM benefit from an increase of hyperedge degrees, even the instance number is decreasing. This again validates our argument that learning such correlated cause–effect structure for mining interdependence of multiple cross-sentence events can help improving document-level causal relation identification.

**【中】**
事件超边的度表示该超边连接了多少个事件节点，也就是其连接节点之间的因果关系是被联合识别的。不出所料，即使实例数量在减少，我们的 EHNEM 仍能从超边度的增大中获益。这再次验证了我们的论点：学习这种相关因果效应结构、挖掘多个跨句事件之间的相互依赖关系，有助于提升文档级因果关系识别。

#### 段落 6

**【EN】**
Document-level event causality: To explore the effectiveness of our EHNEM model for document-level causality identification, we plot the overall performance of our EHNEM against different event distances in Fig. 3(b).

**【中】**
文档级事件因果关系：为探究 EHNEM 模型在文档级因果关系识别上的有效性，我们在图 3(b) 中绘制了 EHNEM 整体性能随不同事件距离变化的情况。

#### 段落 7

**【EN】**
We use the number of event mentions between two events as the event interval distance. It is not unexpected that our EHNEM suffers from the increase of event distance, as two farther apart event mentions may contain discontinuous semantics. It is difficult to identify their causal relation mainly from their respective contextual semantics. However, we can observe that its performance does not decrease much with an increase of event distances (the distance of {1, …, 6} in the figure). This also validates our argument that constructing a hypergraph-like correlated causal-effect structure can help mining the interdependence among multiple causal relations with the same event for document-level causality identification.

**【中】**
我们以两个事件之间的事件提及数量作为事件间隔距离。不出所料，EHNEM 的性能会随事件距离增大而下降，因为相距较远的两个事件提及可能包含不连续的语义，仅依据各自上下文语义很难识别它们之间的因果关系。然而，我们可以观察到，其性能并未随事件距离增大（图中距离 {1, …, 6}）而大幅下降。这也验证了我们的论点：构建类超图的相关因果效应结构，有助于挖掘与同一事件相关的多个因果关系之间的相互依赖，从而促进文档级因果关系识别。

> **Fig. 3.** Performance of different hyperedge degrees and different event distances.
>
> **图 3.** 不同超边度与不同事件距离下的性能。

### 5.3. 案例分析 / Case study

#### 段落 1

**【EN】**
We use case study to visualize and compare both intra-sentence and cross-sentence causalities identified by the EHNEM and EHNEM w/o HGNN respectively, as illustrated in Fig. 4. We first observe that both the EHNEM and EHNEM w/o HGNN can identify most intra-sentence causal relation and miss only one event causality pair (shield; died), as illustrated in Fig. 4(a). This is not unexpected. They both contain the event semantic encoding module to encode local event contextual semantics from its belonging raw sentence.

**【中】**
我们通过案例分析，对 EHNEM 与 EHNEM w/o HGNN 分别识别出的句内和跨句因果关系进行可视化比较，如图 4 所示。首先观察到，如图 4(a) 所示，EHNEM 与 EHNEM w/o HGNN 都能识别出大部分句内因果关系，仅漏掉了一个事件因果对（shield；died）。这并不意外。两者都包含事件语义编码模块，能够从其所属原始句子中编码局部事件上下文语义。

> **Fig. 4.** Visualization of the ECI results for both intra-sentence and cross-sentence in a document of the ESC corpus.
>
> **图 4.** ESC 语料库某篇文档中句内与跨句 ECI 结果的可视化。

#### 段落 2

**【EN】**
By contrast, for cross-sentence causal relations, the EHNEM w/o HGNN only recognizes two event causality pairs (shooting trial; accused) and (murder; accused), but missing many causality pairs that can be recognized by the EHNEM as illustrated in Fig. 4(b). This can be attributed to the document causal structure learning of our EHNEM via a hypergraph convolutional network. We can also observe that those causalities only identified by the EHNEM are with the same effect event "charged". This again validates our argument that event causalities in a document are often correlated, which should be exploited to augment document-level causality identification.

**【中】**
相比之下，对于跨句因果关系，EHNEM w/o HGNN 只识别出两个事件因果对（shooting trial; accused）和（murder; accused），却漏掉了许多 EHNEM 能够识别的因果对，如图 4(b) 所示。这可以归因于我们的 EHNEM 通过超图卷积网络进行的文档因果结构学习。我们还可以观察到，那些仅被 EHNEM 识别出的因果关系都具有相同的结果事件"charged"。这再次验证了我们的论点：文档中的事件因果关系往往是相互关联的，应当加以利用以增强文档级因果关系识别。

### 5.4. 错误分析 / Error analysis

#### 段落 1

**【EN】**
We can first observe that the misidentification of cross-sentence event causality identification is significantly higher compared to intra-sentence identification, despite our proposed EHNEM model has achieved significant improvement in cross-sentence event causality identification (F1 10%+) compared to intra-sentence (F1 2%+), as shown in Table 1. This can be also observed in Fig. 3(b), where the misidentification of intra-sentence causality identification (Distance = 0) is considerably lower than that of cross-sentence (Distance = 1–6). We attribute this misidentification of event causal relations to the semantic discontinuities between events in cross-sentence causal relations; While identifying intra-sentence event causality is relatively easier due to the presence of some explicit relation descriptions within the contextual information.

**【中】**
首先可以观察到，跨句事件因果关系识别的误识别率显著高于句内识别，尽管如表 1 所示，我们提出的 EHNEM 模型在跨句事件因果关系识别上的提升（F1 10%+）相比句内（F1 2%+）更为显著。这一点在图 3(b) 中同样可以观察到：句内因果关系识别（Distance = 0）的误识别率明显低于跨句（Distance = 1–6）。我们将事件因果关系的误识别归因于跨句因果关系中事件之间的语义不连续性；而句内事件因果关系识别相对容易，因为其上下文信息中存在一些显式的关系描述。

#### 段落 2

**【EN】**
We can also observe that the misidentification of causal relations without cause–effect correlation (hyperedge degree = 2) is significantly higher compared to that with more cause–effect correlation (hyperedge degree = 4–8), as shown in Fig. 3(a). This again validated our arguments, that is, the identification of one event causality may benefit from other causal relations with the same cause (effect) event. On the other hand, the identification of causal event pair with co-reference events may also benefit from such correlated causal-effect structure. However, the individual event pairs, i.e. hyperedge degree = 2, experience more misidentification for the absence of correlated cause–effect events.

**【中】**
我们还可以观察到，如图 3(a) 所示，不含因果相关性的因果关系（超边度 = 2）的误识别率显著高于因果相关性更强的那些（超边度 = 4–8）。这再次验证了我们的论点：一个事件因果关系的识别可能受益于与同一原因（结果）事件相关的其他因果关系。另一方面，含有共指事件的因果事件对的识别也可能受益于这种相关因果效应结构。然而，孤立的事件对（即超边度 = 2）由于缺少相关因果效应事件，会经历更多的误识别。

---

## 6. 结论 / Conclusion

### 段落 1

**【EN】**
Existing approaches detect pair-wise causal relation independently, while we argue that the latent correlated cause–effect structure in a document is important for document-level causal relation identification. Motivated from such considerations, we have designed the EHNEM to model latent correlated causal-effect structure by an event-hypergraph and jointly identify multiple causal relations. The EHNEM constructs an event-hypergraph by integrating a potential causal relation for involved event nodes as a hyperedge to capture causal correlations, and a hypergraph convolutional network is used on the constructed event-hypergraph to learn the events' representations for final causality identification. Experiments on the ESC 0.9 corpus have validated our design objective in terms of significant performance improvements over all competitors, with our EHNEM achieving the new state-of-the-art results.

**【中】**
现有方法独立地检测成对因果关系，而我们认为文档中潜在的相关因果效应结构对文档级因果关系识别十分重要。基于这一考虑，我们设计了 EHNEM，通过事件超图对潜在的相关因果效应结构建模，并联合识别多个因果关系。EHNEM 将涉及的事件节点的潜在因果关系整合为超边来构建事件超图，以捕捉因果相关性；并在所构建的事件超图上使用超图卷积网络学习事件表示，用于最终的因果关系识别。在 ESC 0.9 语料库上的实验以显著优于所有对比方法的性能验证了我们的设计目标，EHNEM 取得了新的最先进结果。

### 段落 2

**【EN】**
In our future work, we shall investigate how to unequally encode each event pair causal relation according to its importance for document-level overall event causality identification.

**【中】**
在未来的工作中，我们将研究如何依据每个事件对因果关系对文档级整体事件因果关系识别的重要性，对其进行非等权编码。

---

## 作者贡献声明 / CRediT authorship contribution statement

**【EN】**
**Wei Xiang:** Writing – review & editing, Writing – original draft, Visualization, Validation, Supervision, Methodology, Investigation, Formal analysis, Data curation, Conceptualization. **Cheng Liu:** Visualization, Methodology, Investigation, Data curation. **Bang Wang:** Writing – review & editing, Supervision, Methodology, Investigation, Conceptualization.

**【中】**
**Wei Xiang：** 撰写——审阅与编辑、撰写——初稿、可视化、验证、监督、方法学、调查、形式分析、数据管理、概念化。**Cheng Liu：** 可视化、方法学、调查、数据管理。**Bang Wang：** 撰写——审阅与编辑、监督、方法学、调查、概念化。

## 利益冲突声明 / Declaration of competing interest

**【EN】**
The authors declare the following financial interests/personal relationships which may be considered as potential competing interests: Wei Xiang reports financial support was provided by self-determined research funds of CCNU from the colleges' basic research and operation of MOE and Bang Wang reports financial support was provided by National Natural Science Foundation of China.

**【中】**
作者声明以下可能被视为潜在利益冲突的财务利益和个人关系：Wei Xiang 报告获得了华中师范大学（CCNU）中央高校基本科研业务费自主科研经费的资助；Bang Wang 报告获得了国家自然科学基金（National Natural Science Foundation of China）的资助。

## 数据可用性 / Data availability

**【EN】**
Data will be made available on request.

**【中】**
数据可应要求提供。

---

## 参考文献 / References

> 以下按原文顺序照录（文献条目不译），个别条目中的作者姓名拼写与标点保留原刊原样。

1. Baker, C.F., Fillmore, C.J., Lowe, J.B., 1998. The Berkeley FrameNet project. In: 36th Annual Meeting of the Association for Computational Linguistics and 17th International Conference on Computational Linguistics. Association for Computational Linguistics, Quebec, Canada, pp. 86–90, URL https://aclanthology.org/P98-1013/.

2. Bondarenko, A., Wolska, M., Heindorf, S., Blübaum, L., Ngomo, A.N., Stein, B., Braslavski, P., Hagen, M., Potthast, M., 2022. CausalQA: A benchmark for causal question answering. In: Proceedings of the 29th International Conference on Computational Linguistics. International Committee on Computational Linguistics, Gyeongju, Republic of Korea, pp. 3296–3308, URL https://aclanthology.org/2022.coling-1.291.

3. Cao, P., Zuo, X., Chen, Y., Liu, K., Zhao, J., Chen, Y., Peng, W., 2021. Knowledge-enriched event causality identification via latent structure induction networks. In: Proceedings of the 59th Annual Meeting of the Association for Computational Linguistics and the 11th International Joint Conference on Natural Language Processing. Association for Computational Linguistics, Virtual, pp. 4862–4872. http://dx.doi.org/10.18653/v1/2021.acl-long.376.

4. Caselli, T., Vossen, P., 2017. The event StoryLine corpus: A new benchmark for causal and temporal relation extraction. In: Proceedings of the Events and Stories in the News Workshop@ACL 2017. Association for Computational Linguistics, Vancouver, Canada, pp. 77–86. http://dx.doi.org/10.18653/v1/w17-2711.

5. Chen, M., Cao, Y., Deng, K., Li, M., Wang, K., Shao, J., Zhang, Y., 2022. ERGO: Event relational graph transformer for document-level event causality identification. In: Proceedings of the 29th International Conference on Computational Linguistics. International Committee on Computational Linguistics, Gyeongju, Republic of Korea, pp. 2118–2128, URL https://aclanthology.org/2022.coling-1.185.

6. Chen, M., Cao, Y., Zhang, Y., Liu, Z., 2023. CHEER: Centrality-aware high-order event reasoning network for document-level event causality identification. In: Proceedings of the 61st Annual Meeting of the Association for Computational Linguistics. Association for Computational Linguistics, Toronto, Canada, pp. 10804–10816, URL https://aclanthology.org/2023.acl-long.604.

7. Fan, C., Liu, D., Qin, L., Zhang, Y., Xu, R., 2022. Towards event-level causal relation identification. In: The 45th International ACM SIGIR Conference on Research and Development in Information Retrieval. Association for Computing Machinery, Madrid, Spain, pp. 1828–1833. http://dx.doi.org/10.1145/ 3477495.3531758.

8. Feder, A., Keith, K.A., Manzoor, E., Pryzant, R., Sridhar, D., Wood-Doughty, Z., Eisenstein, J., Grimmer, J., Reichart, R., Roberts, M.E., Stewart, B.M., Veitch, V., Yang, D., 2022. Causal inference in natural language processing: Estimation, prediction, interpretation and beyond. Trans. Assoc. Comput. Linguist. 10, 1138–1158. http://dx.doi.org/10.1162/tacl_a_00511.

9. Feng, Y., You, H., Zhang, Z., Ji, R., Gao, Y., 2019. Hypergraph neural networks. In: The Thirty-Third AAAI Conference on Artificial Intelligence. AAAI Press, Honolulu, Hawaii, USA, pp. 3558–3565. http://dx.doi.org/10.1609/aaai.v33i01.33013558.

10. Gao, L., Choubey, P.K., Huang, R., 2019. Modeling document-level causal structures for event causal relation identification. In: Proceedings of the 2019 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies. Association for Computational Linguistics, Minneapolis, MN, USA, pp. 1808–1817. http://dx.doi.org/10.18653/v1/n19-1179.

11. He, Z., Cao, P., Jin, Z., Chen, Y., Liu, K., Zhang, Z., Sun, M., Zhao, J., 2024. Zero-shot cross-lingual document-level event causality identification with heterogeneous graph contrastive transfer learning. (1), pp. 1–18. http://dx.doi.org/10.48550/arXiv.2403.02893.

12. Heindorf, S., Scholten, Y., Wachsmuth, H., Ngomo, A.N., Potthast, M., 2020. CauseNet: Towards a causality graph extracted from the web. In: The 29th ACM International Conference on Information and Knowledge Management. ACM, Virtual, pp. 3023–3030. http://dx.doi.org/10.1145/3340531.3412763.

13. Hu, Z., Li, Z., Jin, X., Bai, L., Guan, S., Guo, J., Cheng, X., 2023. Semantic structure enhanced event causality identification. In: Proceedings of the 61st Annual Meeting of the Association for Computational Linguistics. Association for Computational Linguistics, Toronto, Canada, pp. 10901–10913, URL https://aclanthology.org/2023.acl-long.610. ===== PAGE 12 ===== W. Xiang et al.

14. Khatib, K.A., Hou, Y., Wachsmuth, H., Jochim, C., Bonin, F., Stein, B., 2020. End-to-end argumentation knowledge graph construction. In: The Thirty-Fourth AAAI Conference on Artificial Intelligence. AAAI Press, New York, NY, USA, pp. 7367–7374, URL https://ojs.aaai.org/index.php/AAAI/article/view/6231.

15. Lai, V.D., Veyseh, A.P.B., Nguyen, M.V., Dernoncourt, F., Nguyen, T.H., 2022. MECI: a multilingual dataset for event causality identification. In: Proceedings of the 29th International Conference on Computational Linguistics. International Committee on Computational Linguistics, Gyeongju, Republic of Korea, pp. 2346–2356, URL https://aclanthology.org/2022.coling-1.206.

16. Liu, J., Chen, Y., Zhao, J., 2020. Knowledge enhanced event causality identification with mention masking generalizations. In: Proceedings of the Twenty-Ninth International Joint Conference on Artificial Intelligence. ijcai.org, Virtual, Japan, pp. 3608–3614. http://dx.doi.org/10.24963/ijcai.2020/499.

17. Liu, Y., Ott, M., Goyal, N., Du, J., Joshi, M., Chen, D., Levy, O., Lewis, M., Zettlemoyer, L., Stoyanov, V., 2019. RoBERTa: A robustly optimized BERT pretraining approach. pp. 1–13, arXiv preprint URL http://arxiv.org/abs/1907.11692.

18. Liu, C., Xiang, W., Wang, B., 2024. Identifying while learning for document event causality identification. (1), pp. 1–13. http://dx.doi.org/10.48550/arXiv.2405. 20608, arXiv preprint arXiv:2405.20608.

19. Man, H., Nguyen, C.V., Ngo, N.T., Ngo, L., andThien Huu Nguyen, F.D., 2024. Hierarchical selection of important context for generative event causality identification with optimal transports. In: Proceedings of the 2024 Joint International Conference on Computational Linguistics, Language Resources and Evaluation. ELRA and ICCL, Torino, Italy, pp. 8122–8132, URL https://aclanthology.org/2024.lrec-main.711.

20. Miller, G.A., 1995. WordNet: A lexical database for English. Commun. ACM 38 (11), 39–41, URL http://doi.acm.org/10.1145/219717.219748.

21. Mu, F., Li, W., 2023. Enhancing event causality identification with counterfactual reasoning. In: Proceedings of the 61st Annual Meeting of the Association for Computational Linguistics. Association for Computational Linguistics, Toronto, Canada, pp. 967–975, URL https://aclanthology.org/2023.acl-short.83.

22. Phu, M.T., Nguyen, T.H., 2021. Graph convolutional networks for event causality identification with rich document-level structures. In: Proceedings of the 2021 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies. Association for Computational Linguistics, Online, pp. 3480–3490. http://dx.doi.org/10.18653/v1/2021.naacl-main.273.

23. Pu, R., Li, Y., Wang, S., Li, D., Zheng, J., Liao, J., 2023. Enhancing event causality identification with event causal label and event pair interaction graph. In: Findings of the Association for Computational Linguistics. ACL 2023, Association for Computational Linguistics, Toronto, Canada, pp. 10314–10322, URL https://aclanthology.org/2023.findings-acl.655.

24. Schuler, K.K., 2006. VerbNet: A Broad-Coverage, Comprehensive Verb Lexicon (Ph.D. thesis). University of Pennsylvania.

25. Seoh, R., Birle, I., Tak, M., Chang, H., Pinette, B., Hough, A., 2021. Open aspect target sentiment classification with natural language prompts. In: Proceedings of the 2021 Conference on Empirical Methods in Natural Language Processing. Association for Computational Linguistics, Punta Cana, Dominican Republic, pp. 6311–6322. http://dx.doi.org/10.18653/v1/2021.emnlp-main.509.

26. Shen, S., Zhou, H., Wu, T., Qi, G., 2022. Event causality identification via derivative prompt joint learning. In: Proceedings of the 29th International Conference on Computational Linguistics. International Committee on Computational Linguistics, Gyeongju, Republic of Korea, pp. 2288–2299, URL https://aclanthology.org/2022.coling-1.200.

27. Speer, R., Chin, J., Havasi, C., 2017. ConceptNet 5.5: An open multilingual graph of general knowledge. In: Proceedings of the Thirty-First AAAI Conference on Artificial Intelligence. AAAI Press, San Francisco, California, USA, pp. 4444–4451, URL http://aaai.org/ocs/index.php/AAAI/AAAI17/paper/view/14972.

28. Sui, Y., Feng, S., Zhang, H., Cao, J., Hu, L., Zhu, N., 2022. Causality-aware enhanced model for multi-hop question answering over knowledge graphs. Knowl.-Based Syst. 250 (1), 108943. http://dx.doi.org/10.1016/j.knosys.2022.108943.

29. Tao, Z., Jin, Z., Bai, X., Zhao, H., Dou, C., Zhao, Y., Wang, F., Tao, C., 2023. SEAG: Structure-aware event causality generation. In: Findings of the Association for Computational Linguistics. ACL 2023, Association for Computational Linguistics, Toronto, Canada, pp. 4631–4644, URL https://aclanthology.org/2023. findings-acl.283.

30. Wolf, T., Debut, L., Sanh, V., Chaumond, J., Delangue, C., Moi, A., Cistac, P., Rault, T., Louf, R., Funtowicz, M., Davison, J., Shleifer, S., von Platen, P., Ma, C., Jernite, Y., Plu, J., Xu, C., Scao, T.L., Gugger, S., Drame, M., Lhoest, Q., Rush, A.M., 2020. Transformers: State-of-the-art natural language processing. In: Proceedings of the 2020 Conference on Empirical Methods in Natural Language Processing: System Demonstrations. Association for Computational Linguistics, Online, pp. 38–45. http://dx.doi.org/10.18653/v1/2020.emnlp-demos.6.

31. Xiang, W., Wang, B., 2019. A survey of event extraction from text. IEEE Access 7 (1), 173111–173137. http://dx.doi.org/10.1109/ACCESS.2019.2956831.

32. Xiang, W., Wang, Z., Dai, L., Wang, B., 2022. ConnPrompt: Connective-cloze prompt learning for implicit discourse relation recognition. In: Proceedings of the 29th International Conference on Computational Linguistics. International Committee on Computational Linguistics, Gyeongju, Republic of Korea, pp. 902–911, URL https://aclanthology.org/2022.coling-1.75.

33. Yang, J., Han, S.C., Poon, J., 2022. A survey on extraction of causal relations from natural language text. Knowl. Inf. Syst. 64 (5), 1161–1186. http://dx.doi.org/10.1007/s10115-022-01665-w.

34. Yao, L., Chu, Z., Li, S., Li, Y., Gao, J., Zhang, A., 2021. A survey on causal inference. ACM Trans. Knowl. Discov. Data 15 (5), http://dx.doi.org/10.1145/3444944.

35. Yuan, C., Huang, H., Cao, Y., Wen, Y., 2023. Discriminative reasoning with sparse event representation for document-level event-event relation extraction. In: Proceedings of the 61st Annual Meeting of the Association for Computational Linguistics. Association for Computational Linguistics, Toronto, Canada, pp. 16222–16234, URL https://aclanthology.org/2023.acl-long.897.

36. Zhang, B., Chen, Q., Zhou, J., Jin, J., He, L., 2024. Enhancing event causality identification with rationale and structure-aware causal question answering. pp. 1–8. http://dx.doi.org/10.48550/arXiv.2403.11129, arXiv preprint arXiv:2403.11129.

37. Zhao, K., Ji, D., He, F., Liu, Y., Ren, Y., 2021. Document-level event causality identification via graph inference mechanism. Inform. Sci. 561 (1), 115–129. http://dx.doi.org/10.1016/j.ins.2021.01.078.

38. Zuo, X., Cao, P., Chen, Y., Liu, K., Zhao, J., Peng, W., Chen, Y., 2021a. Improving event causality identification via self-supervised representation learning on external causal statement. In: Findings of the Association for Computational Linguistics. ACL/IJCNLP 2021, Association for Computational Linguistics, Virtual, pp. 2162–2172. http://dx.doi.org/10.18653/v1/2021.findings-acl.190.

39. Zuo, X., Cao, P., Chen, Y., Liu, K., Zhao, J., Peng, W., Chen, Y., 2021b. LearnDA: Learnable knowledge-guided data augmentation for event causality identification. In: Proceedings of the 59th Annual Meeting of the Association for Computational Linguistics and the 11th International Joint Conference on Natural Language Processing. Association for Computational Linguistics, Virtual, pp. 3558–3571. http://dx.doi.org/10.18653/v1/2021.acl-long.276.

40. Zuo, X., Chen, Y., Liu, K., Zhao, J., 2020. KnowDis: Knowledge enhanced data augmentation for event causality detection via distant supervision. In: Proceedings of the 28th International Conference on Computational Linguistics. International Committee on Computational Linguistics, Barcelona, Spain, pp. 1544–1550. http://dx.doi.org/10.18653/v1/2020.coling-main.135.

---

*本译文已对照 Computer Speech & Language 90 (2025) 101752 正式版逐段校订；仅供阅读参考，术语与表述以英文原文为准。*
