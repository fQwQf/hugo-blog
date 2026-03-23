---
title: 人格表征工程的两个方向
description: 我们现在有两个方向：一是加强分析，试着在原理上深挖；二是推广应用，将其运用在社会模拟上。也就是从深度和广度两个方向拓展。  
date: 2026-03-22 21:32:12+0800
categories:
    - 
tags:
    - 
---

看来似乎目前已经有很多现有的针对表征工程的人格研究。这意味着我们的研究必须深入——如果只是做一个框架出来，那么已经有很多现成的了。我们现在有两个方向：一是加强分析，试着在原理上深挖；二是推广应用，将其运用在社会模拟上。也就是从深度和广度两个方向拓展。

主要有这些竞争项目：

### 1. EasySteer（浙大REAL实验室，arXiv:2509.25175）

**项目定位：** 基于 vLLM 的通用 LLM steering 工程框架。

**核心内容：**
- 支持多种向量提取方法（DiffMean, PCA, LAT, Linear Probe, SAE）和学习型方法（LoReFT, LM-Steer）
- 覆盖 8 个 steering 领域：Safety, Reasoning, Knowledge, Truthfulness, Language, Sentiment, Personality, Style
- 提供交互式前端 + OpenAI 兼容 API
- 性能：10.8–22.3× 推理加速

EasySteer 解决"如何高效 steer"（工程），它的的 personality 只是 8 个领域之一，没有系统性的心理学分析、正交性研究、因果定位或心理量表验证。我们可以试着在深度上钻研，或者将框架的方法移植到社会模拟中。

---

### 2. PERSONA（Feng et al., arXiv:2602.15669, ICLR 2026 Poster）

**项目定位：** Training-free 的组合式人格控制框架。

**核心内容：**
- 三阶段：Persona-Base（对比激活提取正交 trait 向量）→ Persona-Algebra（标量乘法/加法/减法）→ Persona-Flow（推理时动态组合）
- 声称 Big Five 方向"approximately orthogonal"
- PersonalityBench 得分 9.60，接近 SFT 上界 9.61
- Persona-Evolve benchmark 91% win rate

与我们目前的研究相比，都使用对比激活分析提取 Big Five 向量。但是，PERSONA 声称 universal near-orthogonality，我们发现这是 architecture-dependent（0.05–0.42），并且没有：因果定位、防御机制层级、BFI-44 验证、existence-causation dissociation、跨模型正交性变异分析。如果要进一步探究，我认为实际上完全有可能达到更好的效果，至少可以击败SFT。

---

### 3. Frising & Balcells（arXiv:2512.17639, Dec 2025）

**项目定位：** Big Five 线性 probing 和 steering 的实证研究。

**核心内容：**
- 使用 Llama 3.3 70B 生成 406 个虚构角色的 Big Five 分数
- 用线性回归学习 per-layer 方向
- 关键发现：线性方向对 probing 有效，但 steering 效果高度依赖上下文——forced-choice 任务有效，open-ended generation 效果有限

他们用连续分数（非二元对比），方法论不同；他们发现 steering 在 open-ended 场景受限，我们的 BFI-44 和 guardrail penetration 结果更强；并且没有正交性分析、因果定位、防御机制、跨模型验证等。这个研究可以看作上一个研究的弱化版。

---

### 4. Ong et al.（arXiv:2503.12722, Mar 2025）

**项目定位：** 用 RepE 人格 steering 研究多智能体合作行为。

**核心内容：**
- 用 RepE 提取 Big Five 向量，steering LLM 在 Iterated Prisoner's Dilemma 中的合作行为
- 测试 Llama-3.1-8B, Gemma2-9B, Mistral-Nemo
- 发现高 Agreeableness 和 Conscientiousness 提高合作但增加被利用风险

这是一篇应用论文，用 RepE 作为工具而非分析人格编码。没有正交性、因果定位、防御机制等分析。它验证了 personality steering 在下游任务中的实用性，但是规模很小。这强烈暗示广度拓展的可行性。

---

### 5. Ju et al.（arXiv:2504.10227, COLM 2025）

**项目定位：** Layer-wise probing framework，研究人格在 LLM 中的逐层编码。

**核心内容：**
- 11 个开源 LLM 上的 layer-wise probing
- 发现人格主要编码在中间层和高层
- Instruction-tuned 模型 trait 分离更清晰
- 提出 layer-wise perturbation 方法编辑人格，MMLU 退化极小

他们的 layer-wise probing 与我们的 encoding profile 直接重叠。但他们没有区分 existence vs causation，没有正交性分析、防御机制、BFI-44 验证、guardrail penetration等。


---

那么我们可以说有两个大的方向了：

其一，我们可以试着从理论研究上下功夫。实际上我们的确有一些别人没有的研究：比如说"为什么某些 trait 泛化更好"分析（Openness 抽象编码 vs Extraversion 上下文依赖），layer-wise orthogonality evolution 分析（entanglement → separation → re-integration 动态过程），对 Qwen2.5 异常高相关性的三因素假说（容量约束、激进指令微调、训练数据组成）， template sensitivity 分析，将 trait 间差异与 OOD 模式关联等。如果对于更多模型进行大规模分析，我们完全可以得出比目前研究更加深刻的结论。虽然最近涌现了如 PERSONA、Ju et al. 等同期工作，但它们普遍存在一些盲区：混淆了特征的“存在（Probing）”与“因果（Steering）”；盲目轻信大五人格的“完美正交性”而忽视了模型架构差异；仅停留在浅层的表象特质（Big Five），未能触及深层认知机制。

因此，我们应该放弃简单的跑分比拼，转向揭示 LLM 内部人格编码的原理。有几个可行的方向：

1. 存在与因果分离  
    Ju et al. 2025 通过 Layer-wise probing 得出结论“人格主要编码在中间层和高层”。然而实际的steering实验表明：Probing 能探测到的是语义的存在域，但决定模型最终扮演何种人格的因果域（控制枢纽）实际上集中在极早期的 Attention 层。这暗示早期层就就决定了角色语境，中晚期层仅仅是顺着该语境进行语义词汇的扩写。这从根本上解释了为什么早期的微小扰动（Steering）能产生巨大的宏观人格偏移。

2. 人格表征的逐层动态演化  
    现有的 RepE 研究几乎都把人格向量看作静态的全局方向。我们将首次描绘一个 Trait 在残差流中的逐层演化：早期，各种特质与 prompt 词汇高度混合；在中间层，心理学概念被真正抽象出来，形成（不完美的）正交几何空间；在最后几层，被抽象的人格特征开始与安全护栏（RLHF）及输出分布发生非线性碰撞。这可能解释不同的特征的泛化性为什么不同：Openness 是高度抽象的语义概念，泛化极强；而 Extraversion 更依赖具体的社交上下文，容易在早期受到干扰。  

3. 架构与对齐税
    针对 PERSONA 论文中轻率宣称的“大五人格近似正交”，根据跨模型的实验数据（0.05 - 0.42 的巨大波动），这个结论有待商榷。特别是针对 Qwen2.5 表现出的异常高相关性，我们猜测，由于模型总是会被向着“有用的人工智能助手”方向对齐，高强度对齐的模型（如 Qwen2.5）会把大量“宜人性”和“尽责性”特征高强度压入各种内部参数，导致几何空间变形和可操控性下降。

---

其二是探究应用的结合。2503.12722证明在小规模上表征工程的可行性。那么，我们既然都知道在大规模社会模拟上个体层面的模拟不够好，那么自然可以想到结合表征工程的方法。问题在于，目前似乎没有评测社会模拟中人格一致性的benchmark。我们可以考虑构造一个benchmark，例如观察不同人格个体的社交倾向性。Ong et al. (2025) 的研究虽然证明了 RepE 在多智能体交互中的可行性，但其仅仅停留在极小规模（双人囚徒困境）和单一任务上。我们既然知道，依靠在 System Prompt 中塞满人设的方法，不仅会在漫长的上下文交互中被逐渐“冲刷”掉，而且由于大模型的 RLHF 对齐，所有 Agent 最终都会趋同于“礼貌且乐于助人（高宜人性、高尽责性）”的同质化状态。如果个体层面的底层因果机制都不保真，宏观社会学模拟得出的结论就毫无科学价值。因此，我们可以考虑将表征工程作为底层插件接入社会模拟，并提出首个专门针对“大模型群体社会互动人格一致性”的Benchmark。

具体而言，我们可以从以下方向构建这个应用与评测框架：

我们将通过实验证明：在经历 50-100 轮的复杂群聊或高压社会事件（如资源分配冲突）后，Prompt 驱动的 Agent 人设会完全崩塌，而 RepE 驱动的 Agent 依然能保持高度的决策一致性。学术界有大量“角色扮演（Role-playing）”和“静态人格问卷（QA）”的评测，但是没有一个公认的、针对多智能体社会模拟中长期人格一致性与宏观行为涌现的 Benchmark。既然目前没有评测社会模拟中人格一致性的 Benchmark，我们可以自己定义标准。我认为可以将不同人格的 Agent 放入沙盒。基于社会学理论，高外向性（Extroversion）的 Agent 应该自然涌现出更多的“弱联系（Weak ties）”和极高的网络中心度（Degree Centrality）；而高神经质/内向的 Agent 应该形成封闭的“强联系”小团体。我们去测量 LLM 生成的社交图谱是否符合人类社会学规律。在无预设角色的协作任务中，观察高尽责性（Conscientiousness）的个体是否会自动承担规划者/领导者角色，高宜人性个体是否会成为冲突调解员。当虚拟社会遭遇危机（如经济崩溃、资源枯竭）时，观察不同群体是否会发生行为畸变（如低阶群体开始“投射”和“否认”，产生群体极化）。
