Title:

Content selection saved. Describe the issue below:

Description:

![](https://arxiv.org/static/base/1.0.1/images/icons/smileybones-small.svg)arXiv is now an independent nonprofit! [Learn more](https://info.arxiv.org/about) ×

[License: CC BY 4.0](https://info.arxiv.org/help/license/index.html#licenses-available)

arXiv:2504.15989v2 \[cs.SE\] 29 May 2025

# Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency Thanks: \\* Corresponding authors

Junwei Hu
Affiliation: School of Computer Science and Technology

Tongji University

Shanghai, China

2153393@tongji.edu.cn
Weicheng Zheng
Affiliation: School of Computer Science and Technology

Tongji University

Shanghai, China

2154286@tongji.edu.cn
Yihan Liu
Affiliation: School of Computer Science and Technology

Tongji University

Shanghai, China

2352755@tongji.edu.cn
Yan Liu\*
Affiliation: School of Computer Science and Technology

Tongji University

Shanghai, China

yanliu.sse@tongji.edu.cn

###### Abstract

With the increasing adoption of large language models (LLMs) in software engineering, the Chain of Thought (CoT) reasoning paradigm has become an essential approach for automated code repair. However, the explicit multi-step reasoning in CoT leads to substantial increases in token consumption, reducing inference efficiency and raising computational costs, especially for complex code repair tasks. Most prior research has focused on improving the correctness of code repair while largely overlooking the resource efficiency of the reasoning process itself. To address this challenge, this paper proposes three targeted optimization strategies: Context Awareness, Responsibility Tuning, and Cost Sensitive. Context Awareness guides the model to focus on key contextual information, Responsibility Tuning refines the structure of the reasoning process through clearer role and responsibility assignment, and Cost Sensitive incorporates resource-awareness to suppress unnecessary token generation during inference. Experiments across diverse code repair scenarios demonstrate that these methods can significantly reduce token consumption in CoT-based reasoning without compromising repair quality. This work provides novel insights and methodological guidance for enhancing the efficiency of LLM-driven code repair tasks in software engineering.

###### Index Terms:

code smell, CoT, token consumption

## I Introduction

![Refer to caption](https://arxiv.org/html/2504.15989v2/badcase.png)Fig. 1: An example of redundant reasoning caused by confusion between C (row-major) and Fortran (column-major) memory layout. The user request involves reshaping a 4D CNN mask into a 2D time series mask compatible with RNNs, which requires correct alignment with Fortran-order expectations. However, the reasoning model repeatedly revisits the same subproblem—how elements are ordered in memory and how reshaping should be done—without converging on a solution. This looped verification, highlighted by repeated statements and backward arrows, illustrates the reasoning inefficiency when the model lacks concrete assumptions about memory layout or reshape semantics.

The rapid proliferation of Large Language Models (LLMs) is driving a profound transformation within the software engineering domain. From code generation and documentation to intricate system design and defect analysis, LLMs exhibit unprecedented potential, fundamentally altering traditional software development practices. Among these advancements, Chain of Thought (CoT)\[ [1](https://arxiv.org/html/2504.15989v2#bib.bib1 "")\] has garnered widespread attention as a strategy designed to enhance the reasoning capabilities of these models. By prompting LLMs to emulate a human-like, step-by-step problem-solving process, CoT significantly improves model accuracy and output quality when tackling complex software engineering tasks, such as generating intricate code, remediating deep-seated errors, and executing multi-step optimizations\[ [2](https://arxiv.org/html/2504.15989v2#bib.bib2 "")\]\[ [3](https://arxiv.org/html/2504.15989v2#bib.bib3 "")\]. This progress signifies a crucial shift for LLMs, from mere ”pattern matchers” to more sophisticated ”intelligent reasoners,” thereby unlocking new vistas for automated and intelligent software development\[ [4](https://arxiv.org/html/2504.15989v2#bib.bib4 "")\].

However, the enhanced capabilities afforded by CoT are accompanied by a significant and often overlooked challenge: a substantial increase in token consumption\[ [5](https://arxiv.org/html/2504.15989v2#bib.bib5 "")\]. The CoT mechanism inherently relies on the generation of detailed intermediate reasoning steps, which implies that both the length of input prompts and the volume of model-generated content can far exceed those of traditional, more succinct question-answering paradigms\[ [6](https://arxiv.org/html/2504.15989v2#bib.bib6 "")\]. This phenomenon, termed ”token inflation,”\[ [7](https://arxiv.org/html/2504.15989v2#bib.bib7 "")\] has emerged as a critical bottleneck impeding the broader application of LLMs in the software engineering field. When LLMs are tasked with analyzing large-scale, complex modern software systems, or engaged in programming tasks requiring multiple iterative refinements, the token inflation precipitated by CoT becomes particularly acute\[ [8](https://arxiv.org/html/2504.15989v2#bib.bib8 "")\]. This not only imposes economic pressure on the cost-effectiveness of model utilization but also adversely impacts practical application efficiency.

In real-world software development applications, cost control and operational efficiency remain paramount considerations\[ [9](https://arxiv.org/html/2504.15989v2#bib.bib9 "")\]. For all software development teams—ranging from large technology corporations and agile small-to-medium-sized enterprises to budget-constrained startups—the adoption of new technologies transcends mere enhancement of technical capabilities, necessitating a thorough evaluation of cost-effectiveness\[ [6](https://arxiv.org/html/2504.15989v2#bib.bib6 "")\]. Currently, a majority of LLM services are accessed via API calls, with charges typically levied based on the volume of tokens processed. While CoT can yield higher-quality outputs, the concomitant substantial increase in token consumption may render its use prohibitively expensive\[ [8](https://arxiv.org/html/2504.15989v2#bib.bib8 "")\]. Within modern software development pipelines, particularly in contexts such as mobile application development, embedded systems, and other resource-constrained environments, excessive token consumption can lead to considerable economic burdens, compelling many teams to exercise caution or curtail their reliance on CoT methodologies\[ [1](https://arxiv.org/html/2504.15989v2#bib.bib1 "")\].

Beyond direct economic costs, excessive token consumption engenders other detrimental effects, including prolonged model response times and heightened demands on computational resources. Such issues can impair the agility of software development processes and the immediacy of user experiences, especially in scenarios mandating rapid responses and efficient delivery\[ [6](https://arxiv.org/html/2504.15989v2#bib.bib6 "")\]. Consequently, achieving an optimal balance between harnessing the reasoning advantages of CoT and ensuring the economic viability and operational efficiency of LLM applications has surfaced as a significant challenge confronting the software engineering field\[ [10](https://arxiv.org/html/2504.15989v2#bib.bib10 "")\].

This study does not aim to propose a systemic solution; rather, it represents a preliminary exploration of this issue. Through the observation and analysis of token consumption phenomena within CoT processes, we aspire to furnish insights for future research, investigating methodologies to effectively curtail token consumption without a significant detriment to reasoning performance. This research endeavors to pave the way for the expanded application of LLMs in software engineering, particularly within contexts that prioritize developmental efficiency and resource optimization.

Driven by widespread concerns regarding the token consumption induced by Chain of Thought (CoT) and its potential ramifications, this study undertakes an exploratory investigation. Our objective is to meticulously observe and comprehend the token dynamics of CoT when processing code of varying complexities within specific experimental settings\[ [11](https://arxiv.org/html/2504.15989v2#bib.bib11 "")\]. Furthermore, we aim to identify external intervention strategies capable of effectively mitigating such consumption. To ground our research in realistic and common software development contexts—scenarios where the intricacies of CoT’s reasoning pathways and token expenditure may be amplified—we concentrate our observational lens on how Large Language Models (LLMs) handle code exhibiting different intrinsic characteristics. Specifically, the presence and nature of ”code smells\[ [12](https://arxiv.org/html/2504.15989v2#bib.bib12 "")\]\[ [13](https://arxiv.org/html/2504.15989v2#bib.bib13 "")\]” are employed as a tangible proxy and experimental variable for examining these phenomena. It is crucial to emphasize that our intent is not to present ”code smell elimination” as an end in itself, but rather to utilize it as an effective ”lens” through which to gain deeper insights into the broader challenge of CoT token optimization\[ [14](https://arxiv.org/html/2504.15989v2#bib.bib14 "")\].

Guided by this philosophy, we propose a conceptual methodological framework termed ”Token-Aware Coding Flow.” Based on this framework, we have conducted a series of preliminary experimental inquiries. These inquiries endeavor to offer initial insights into several key questions:

- •


What is the specific impact of intrinsic code characteristics (e.g., as studied through a comparative analysis of ”clean code” versus ”smelly code”) on token consumption during CoT reasoning processes?

- •


To what extent does pre-processing code (for instance, by refactoring to eliminate code smells, thereby simplifying the input furnished to CoT) demonstrate potential for reducing token consumption?

- •


Do different types of inherent code complexities (exemplified by various categories of code smells) lead to differentiated patterns in token consumption?

- •


When interacting with LLMs, does explicitly indicating potential code characteristics (such as the type of code smells) within the prompt help guide the CoT process toward more economical token usage?


Our preliminary experiments reveal a clear correlation between the intrinsic characteristics of code and token consumption during Chain of Thought (CoT) reasoning. Compared to ”clean” code, code with ”smells” significantly increases token usage, while refactoring to remove such code smells can substantially reduce the tokens required for subsequent reasoning. Furthermore, different types of code complexity (such as various code smells) affect token consumption differently, with deeper logical or structural issues typically imposing a higher token burden. Explicitly indicating code characteristics in the prompt, combined with prompt engineering strategies, also demonstrates potential for optimizing token efficiency.

Although this research is still in an exploratory phase and its conclusions require further validation, our initial results provide new perspectives on understanding and optimizing token consumption in CoT scenarios.Our main contributions of this work are as follows:

- •


For the first time, we systematically focus on resource (token) consumption in code generation and processing tasks, drawing attention to the economic and efficiency impact of CoT reasoning in LLM-based software engineering.

- •


We propose a novel approach to reduce resource consumption from outside the model, by leveraging code refactoring and prompt engineering techniques. This strategy offers practical and effective ways to optimize token usage without modifying model internals.

- •


We empirically validate that code smells lead to significantly higher token consumption, and demonstrate that external optimization strategies (such as code refactoring and prompt design) can effectively reduce this overhead.

- •


Our work provides empirical evidence and methodological inspiration for developing more cost-effective and efficient LLM-assisted software engineering workflows, contributing to the advancement of resource-aware intelligent development practices.


## II Introduce the New Coding Flow

![Refer to caption](https://arxiv.org/html/2504.15989v2/flow.png)Fig. 2: The Ladder of Programming and AI. (1) Traditional coding paradigm: human programmers manually translate requirements into code. (2) Augmented coding paradigm: programmers are assisted by base language models (e.g., GPT-3.5, GitHub Copilot) to generate code, reducing manual effort. (3) Reasoning-driven paradigm: advanced reasoning models (e.g., GPT-o3, DeepSeek-R1) interpret requirements and autonomously generate code by simulating human-like thinking, leading to deeper understanding and improved code quality.

The history of software development is characterized by a continuous pursuit of enhanced efficiency, superior quality, and greater automation capabilities. To better contextualize the significance of our proposed ”Token-Aware Coding Flow,” we first review and analyze the evolution of software development paradigms, particularly highlighting the recent transformations driven by Large Language Models (LLMs). As depicted in Figure [2](https://arxiv.org/html/2504.15989v2#S2.F2 "Fig. 2 ‣ II Introduce the New Coding Flow ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency"), the evolution of software development models can be broadly categorized into the following three core stages\[ [15](https://arxiv.org/html/2504.15989v2#bib.bib15 "")\]\[ [16](https://arxiv.org/html/2504.15989v2#bib.bib16 "")\]\[ [17](https://arxiv.org/html/2504.15989v2#bib.bib17 "")\]:

### II-AManual Programming

In the early phase of software development (Stage 1, Fig. [2](https://arxiv.org/html/2504.15989v2#S2.F2 "Fig. 2 ‣ II Introduce the New Coding Flow ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")), the process relied almost entirely on manual efforts by programmers. Every step—from requirements to design, implementation, testing, and maintenance—depended on their expertise and time. While this approach maximized human creativity, it suffered from low efficiency, limited scalability, difficult knowledge transfer, and a high risk of human errors and inconsistencies.

### II-BAssisted Programming

Advancements such as IDEs and code assistance tools ushered in an assisted programming era (Stage 2, Fig. [2](https://arxiv.org/html/2504.15989v2#S2.F2 "Fig. 2 ‣ II Introduce the New Coding Flow ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")), where programmers leveraged technologies like auto-completion, syntax highlighting, version control, and basic code generators to boost productivity. More recently, LLM-powered tools (e.g., GPT-3.5, GitHub Copilot) have enabled smarter code suggestions, test generation, and documentation drafts. As a result, the programmer’s role shifted towards supervision and validation, marking the start of true human–AI collaboration.

### II-CCognitive Programming

Today, development is increasingly driven by advanced reasoning models (Stage 3, Fig. [2](https://arxiv.org/html/2504.15989v2#S2.F2 "Fig. 2 ‣ II Introduce the New Coding Flow ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")). Powerful LLMs with Chain of Thought (CoT) capabilities (such as GPT-o1, DeepSeek-R1\[ [1](https://arxiv.org/html/2504.15989v2#bib.bib1 "")\], GPT-4, DeepSeek Coder) can understand, decompose, and autonomously address complex requirements—planning, generating, and optimizing multi-step code, and even participating in architecture and debugging. Developers now focus on defining goals and making decisions, while models handle much of the cognitive and implementation workload. This paradigm greatly enhances efficiency and problem-solving, potentially transforming software engineering.

However, as discussed in the Introduction, this reasoning-driven paradigm—especially its reliance on CoT—also leads to a sharp rise in token consumption. Optimizing token efficiency is thus essential to fully realize its benefits and ensure sustainable use in diverse development scenarios. This challenge motivates our proposal of the “Token-Aware Coding Flow” and the exploratory work in this study.

![Refer to caption](https://arxiv.org/html/2504.15989v2/CodingFlow.png)Fig. 3: Illustration of the LLM-driven code generation workflow in real-world development scenarios. The process begins with diverse input sources, such as natural language (NL), code translation tasks, bug reports, partial implementations, or test cases. The large language model (LLM) engages in multi-turn reasoning by interpreting the input, thinking step-by-step (e.g., via inner monologue), and generating candidate code. If the generated code fails to meet requirements, the model rethinks the problem by refining prompts and iterating on its reasoning. The process concludes with the production of executable code that satisfies the development goal.

## III Crafting the Future House

### III-AResearch Questions

To explore the impact of code smells on the reasoning process of large models, we designed a series of experiments aimed at addressing the following research questions:

- •


RQ1: What are the specific impacts of smelly code on the reasoning process of large models (DeepSeek-R1) compared to clean code?

- •


RQ2: How does code refactoring (eliminating code smells) impact token consumption during model inference?

- •


RQ3: Are there significant differences in token consumption caused by different types of code smells?

- •


RQ4: Does explicitly indicating the type of code smells in the model prompt help reduce token consumption during inference?

- •


RQ5: In addition to code refactoring, what other effective prompt engineering strategies can further reduce token consumption when reasoning with smelly code?


Through these questions, we aim to gain a deeper understanding of the impact of smelly code on large model inference and explore the potential improvements in reasoning efficiency with different optimization strategies\[ [18](https://arxiv.org/html/2504.15989v2#bib.bib18 "")\]. First, RQ1 primarily focuses on whether smelly code causes large models to consume more tokens during inference, which is crucial for evaluating the model’s ability to handle non-standard code. RQ2 investigates whether refactoring smelly code improves the model’s reasoning efficiency, particularly in terms of reducing token consumption while maintaining functional consistency\[ [6](https://arxiv.org/html/2504.15989v2#bib.bib6 "")\]\[ [19](https://arxiv.org/html/2504.15989v2#bib.bib19 "")\]. RQ3 focuses on the impact of different types of code smells on model reasoning efficiency, helping us understand which types of code smells have a more significant effect on token consumption\[ [19](https://arxiv.org/html/2504.15989v2#bib.bib19 "")\]. RQ4 examines whether explicitly pointing out the type of code smells in the prompt provides the model with more precise contextual information, thereby reducing redundant reasoning steps and lowering token consumption\[ [19](https://arxiv.org/html/2504.15989v2#bib.bib19 "")\]. Finally, RQ5 explores whether other effective prompt engineering strategies, such as context awareness, role constraints, and cost-sensitive strategies, can further optimize reasoning efficiency beyond code refactoring. By addressing these questions, we aim to both validate the specific impact of code smells on large model reasoning performance and provide experimental evidence for improving inference efficiency through refactoring and optimized prompt design.

### III-BTasks

In this study, we designed a series of evaluation tasks to systematically explore the impact of code smells on the reasoning process of large models \[ [20](https://arxiv.org/html/2504.15989v2#bib.bib20 "")\] and to test the effectiveness of different optimization strategies. The following are the specific tasks set for each research question.

#### III-B1 Observational Experiment

Datasets:
We used the CodeXGLUE Java (Text-To-Code) dataset, containing 300 smelly code samples and 300 clean code samples, to analyze differences in token and time consumption when DeepSeek-R1 processes different types of code.

Metrics:
We recorded the total number of tokens and inference time for each code sample. Token consumption rate was calculated to evaluate the model’s efficiency in handling different code types. The generated code was further assessed for functional correctness and readability.

#### III-B2 Refactoring and Optimization Experiment

Datasets:
Smelly code samples were refactored using DeepSeek-R1 to generate refactored code, which was then compared with the original smelly code and clean code to assess inference efficiency.

Metrics:
We tracked token consumption at each processing step and calculated Halstead and cyclomatic complexity. Token consumption per unit of complexity and per line of code was analyzed. Functional consistency was ensured using metrics such as CodeBLEU.

#### III-B3 Code Smell Type Comparison Experiment

Datasets:
We selected 10 types of code smells from the smelly code dataset, with 50 samples per type (500 samples in total), to analyze the impact of different smell types on token consumption.

Metrics:
Token consumption during inference was compared across different smell types, with additional analysis of design, structural, and naming smells. The generated code was also evaluated for functional consistency.

#### III-B4 Explicit Smell Type Prompt Experiment

Datasets:
Based on the smelly code dataset, we compared token consumption when the prompt explicitly indicated the code smell type versus when it did not.

Metrics:
Token and time consumption were recorded at each experimental step. Functional consistency of the output was evaluated using metrics such as CodeBLEU. The impact of explicitly indicating the code smell type on inference efficiency and code quality was also analyzed.

#### III-B5 Prompt Optimization Strategy Experiment

Datasets:
Various prompt optimization strategies—such as context enhancement, responsibility tuning, and cost-sensitive prompting—were tested on the smelly code dataset.

Metrics:
Token consumption and code complexity under each strategy were recorded. Token consumption per unit complexity and per line of code was compared. Each strategy was evaluated based on functional consistency and code generation quality.

### III-CImplementation

This study’s experimental implementation is built upon existing LLMs and open‑source code‑refactoring tools, with all tasks orchestrated via API calls to guarantee reproducibility and efficiency\[ [21](https://arxiv.org/html/2504.15989v2#bib.bib21 "")\]. Specifically, we employ DeepSeek‑R1 as the inference engine and integrate Tree‑Sitter for code‑smell detection and automated refactoring. The implementation proceeds as follows(Fig. [4](https://arxiv.org/html/2504.15989v2#S3.F4 "Fig. 4 ‣ III-C Implementation ‣ III Crafting the Future House ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")):

![Refer to caption](https://arxiv.org/html/2504.15989v2/6.png)Fig. 4: Experiment Design Process. The diagram shows the steps involved in extracting requirements, generating code, refactoring code, and comparing semantic and code similarities. The flow demonstrates the interaction between different components, including code refactoring and requirement extraction, with the evaluation steps represented through similarity comparison.

- •


Data Processing and Smell Annotation: Java code was extracted from the Text‑Code subset of CodeXGLUE and parsed using Tree‑Sitter to annotate ten common code smell categories. This enabled the construction of parallel “smelly” and “clean” datasets. For each sample, we recorded token consumption and inference latency during both code repair and refactoring tasks using the DeepSeek‑R1 API.

- •


Prompt Engineering and Optimization: To mitigate token overhead in CoT-based reasoning, we implemented and evaluated three prompt-level strategies: Context Awareness, Responsibility Tuning, and Cost Sensitive. Each strategy was applied by modifying the model’s input prompt, and for each experiment, we logged token usage, inference time, and code repair quality.

- •


Automation and Analysis: All API interactions were automated via Python scripts, ensuring reproducibility and efficiency. Outputs—including token counts, timing, and repaired code—were collected and analyzed using custom scripts for data aggregation, statistical analysis, and visualization, enabling comprehensive comparison of different strategies.


## IV Experimental Study: Triggering the nano surge

![Refer to caption](https://arxiv.org/html/2504.15989v2/nano-surge.png)Fig. 5: Nano Surge on Token Consumption

### IV-ARQ1: Smelly Code vs. Clean Code

##### Experimental Design.

We randomly sampled 30 instances from each of the ten individual code‑smell datasets and aggregated them into a single file, smelly\_code.jsonl. We then randomly selected 300 examples from the clean‑code dataset, producing clean\_code.jsonl. Because the two collections differ substantially in content and structure, we employ the _Time‑Scaled Token Consumption_ metric to evaluate token expenditure during inference. Using the DeepSeek‑R1 model, each sample is assessed across six dimensions—functional correctness, readability, robustness, maintainability, extensibility, and security—while we record the inference procedure, evaluation outcomes, and end‑to‑end execution time. From these logs, we compute the total tokens consumed during both the reasoning and output‑generation phases.

##### Analysis.

In our analysis, we first compare token consumption per unit time between clean code and smelly code. Directly contrasting absolute token counts would misrepresent efficiency, since the code samples vary greatly in complexity and structure. By normalizing to tokens per unit time, we obtain a more precise measure of model throughput across code types. Statistical evaluation reveals that smelly code consistently demands higher token expenditure(Fig. [6](https://arxiv.org/html/2504.15989v2#S4.F6 "Fig. 6 ‣ Analysis. ‣ IV-A RQ1: Smelly Code vs. Clean Code ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency"))(Tab. [I](https://arxiv.org/html/2504.15989v2#S4.T1 "TABLE I ‣ Analysis. ‣ IV-A RQ1: Smelly Code vs. Clean Code ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")). This elevated consumption stems from the increased complexity of smelly code: the model must undertake additional reasoning steps and verifications to resolve intricate structural and logical relationships. Conversely, clean code—with its clearer organization—allows the model to complete inference with fewer redundant operations.

![Refer to caption](https://arxiv.org/html/2504.15989v2/1.png)Fig. 6: Comparison of time-scaled token consumption between clean code and smelly code.TABLE I: Descriptive Statistics of Time-Scaled Token Consumption

| Statistic | Clean Code | Smelly Code |
| --- | --- | --- |
| Count | 300 | 300 |
| --- | --- | --- |
| Mean | 24.44 | 33.20 |
| Std. Dev. | 12.35 | 18.63 |
| Min | 0.61 | 0.83 |
| 25% Quartile | 18.55 | 25.30 |
| Median (50%) | 28.13 | 33.30 |
| 75% Quartile | 31.68 | 41.90 |
| Max | 58.97 | 120.16 |

Findings – Smelly Code vs Clean CodeThe results show that smelly code leads to significantly higher token consumption during inference than clean code. This is mainly attributed to structural complexity such as redundant logic and poor naming, which forces the model to perform repeated validations. These findings suggest that code smells not only harm maintainability but also increase the inference cost of large models, highlighting the importance of code quality for efficient model reasoning.

### IV-BRQ2: Impact of Code Refactoring on Token Consumption under Functional Consistency Constraint

##### Experimental Setup.

To address RQ2, we examined how refactoring to remove code smells impacts token consumption during model inference. Using DeepSeek‑R1, we first established baseline token usage on both smelly\_code.jsonl and clean\_code.jsonl. We then applied automated refactoring to the smelly code (removing common smells like long methods and duplicated logic) to produce a refactored code set (rf\_code), and measured token usage on these samples. Functional consistency was verified using CodeBLEU and related metrics.

##### Analysis.

For each run, we tracked inference duration and token consumption, and normalized by time. Results show that refactored code leads to more efficient inference—maintaining functionality (Tab. [III](https://arxiv.org/html/2504.15989v2#S4.T3 "TABLE III ‣ Analysis. ‣ IV-B RQ2: Impact of Code Refactoring on Token Consumption under Functional Consistency Constraint ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")), but requiring substantially fewer tokens (Fig. [7](https://arxiv.org/html/2504.15989v2#S4.F7 "Fig. 7 ‣ Analysis. ‣ IV-B RQ2: Impact of Code Refactoring on Token Consumption under Functional Consistency Constraint ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency"), Tab. [II](https://arxiv.org/html/2504.15989v2#S4.T2 "TABLE II ‣ Analysis. ‣ IV-B RQ2: Impact of Code Refactoring on Token Consumption under Functional Consistency Constraint ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")). Refactoring complex smells, especially long functions and duplicate code, significantly reduced the number of reasoning steps and validations the model performed.

![Refer to caption](https://arxiv.org/html/2504.15989v2/2.png)Fig. 7: Comparison of complexity-normalized token consumption before and after code refactoring.TABLE II: Descriptive Statistics of Token per Complexity

| Statistic | Original Code | Refactored Code |
| --- | --- | --- |
| Count | 300 | 300 |
| --- | --- | --- |
| Mean | 0.1015 | 0.0576 |
| Std. Dev. | 0.1732 | 0.0816 |
| Min | 0.0020 | 0.0020 |
| 25% Quartile | 0.0144 | 0.0094 |
| Median (50%) | 0.0334 | 0.0239 |
| 75% Quartile | 0.1180 | 0.0723 |
| Max | 1.5613 | 0.5836 |

TABLE III: Summary of CodeBLEU and Docstring Similarity Statistics (Mean Values Only)

| Category | Metric | Mean |
| CodeBLEU | code & rf code | 0.5224 |
| rf code & rf gc code | 0.1803 |
| Docstring | code & rf code | 0.8604 |
| code & rf gc code | 0.7356 |
| rf code & rf gc code | 0.7364 |

Findings – Refactoring ImpactCode refactoring yields a substantial reduction in token consumption during inference while preserving functional correctness. Specifically, the refactored code consumes approximately 50% fewer tokens compared to the original smelly code. By removing redundant structures and simplifying complex fragments, refactoring not only enhances code quality but also markedly improves inference efficiency. These results suggest that systematic elimination of code smells is a critical strategy for reducing computational overhead in large‑model reasoning.

### IV-CRQ3: Impact of Different Types of Code Smells on Token Consumption

##### Experimental Design / Setup

To answer RQ3, we designed an experiment to investigate how different types of code smells affect token consumption in the DeepSeek‑R1 inference model. We extracted from smelly\_code.jsonl ten common smell categories—complicated\_regex\_expression, parameter\_list\_too\_long, binary\_operator\_in\_name, complicated\_boolean\_expression, etc.—with 30 code snippets per category (300 total). We ran DeepSeek‑R1 inference separately on each group, recording the tokens consumed for each snippet. By computing the tokens consumed per unit time for each smell type, we identified which categories impose the greatest inference burden.

##### Analysis

The first graph (Fig. [8](https://arxiv.org/html/2504.15989v2#S4.F8 "Fig. 8 ‣ Analysis ‣ IV-C RQ3: Impact of Different Types of Code Smells on Token Consumption ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")) and table (Tab. [IV](https://arxiv.org/html/2504.15989v2#S4.T4 "TABLE IV ‣ Analysis ‣ IV-C RQ3: Impact of Different Types of Code Smells on Token Consumption ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")) present the average growth rate of token consumption for different code smell types. Most smells, such as binary operator in name, complicated boolean expression, and complicated regex expression, cause a notable increase in token usage, likely due to their complex logic requiring deeper model reasoning \[ [22](https://arxiv.org/html/2504.15989v2#bib.bib22 "")\]. In contrast, smells like func name and too long parameter list have a lower impact, indicating they are less complex and lead to only minor increases in token consumption.

The second graph (Fig. [9](https://arxiv.org/html/2504.15989v2#S4.F9 "Fig. 9 ‣ Analysis ‣ IV-C RQ3: Impact of Different Types of Code Smells on Token Consumption ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")) groups code smells into Naming, Expression, Structure, and Design. Expression-related smells show the highest token consumption growth, followed by Structure. These categories often involve intricate logic or convoluted code structures, demanding more tokens for the model to process. By comparison, Naming and Design smells have a smaller effect on token growth, as they are less likely to complicate the reasoning process.

![Refer to caption](https://arxiv.org/html/2504.15989v2/3.png)Fig. 8:  Average token growth rate per code smell type.![Refer to caption](https://arxiv.org/html/2504.15989v2/4.png)Fig. 9:  Average token growth rate by code smell category.TABLE IV: Average Token Growth Rate by Smell Type

| Smell Type | Avg. Growth Rate |
| --- | --- |
| binary\_operator\_in\_name | 0.3424 |
| complicated\_boolean\_expression | 0.3361 |
| complicated\_regex\_expression | 0.3221 |
| cyclomatic\_complexity | 0.3285 |
| func\_name | 0.1592 |
| loops | 0.3111 |
| mutation\_too\_much | 0.3321 |
| too\_long\_parameter\_list | 0.1942 |
| primitive\_obsession | 0.2963 |
| too\_long | 0.3089 |

TABLE V: Comparison of Code Equivalence Before and After Refactoring (Mean Values Only)

| Category | Metric | Mean |
| Source Code | code & gc code | 0.1659 |
| Refactored Code | code & rf code | 0.5208 |
| rf code & rf gc code | 0.1773 |
| Source Code Similarity | code & gc code | 0.7470 |
| Refactored Code Similarity | code & rf code | 0.8610 |
| code & rf gc code | 0.7295 |
| rf code & rf gc code | 0.7340 |

Findings – Smell Type ImpactOur study, conducted with a 70% code functionality similarity threshold(Tab. [V](https://arxiv.org/html/2504.15989v2#S4.T5 "TABLE V ‣ Analysis ‣ IV-C RQ3: Impact of Different Types of Code Smells on Token Consumption ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")), reveals significant variance in token consumption across different code smell categories. Complex code smells—namely complicated\_regex\_expression, parameter\_list\_too\_long, and complicated\_boolean\_expression—incur the highest token costs due to increased code complexity and the need for additional reasoning steps. Simpler smells, such as binary\_operator\_in\_name and func\_name, result in comparatively minor token overhead. These findings suggest that prioritizing the removal of design and structural smells can maximize inference efficiency and substantially reduce computational overhead.

### IV-DRQ4: Impact of Explicitly Indicating Code Smell Types in Prompts on Token Consumption\[ [23](https://arxiv.org/html/2504.15989v2\#bib.bib23 "")\]

##### Experimental Design / Setup

To address RQ4, we designed an experiment to investigate whether explicitly annotating code smell types in the model prompt effectively reduces token consumption during inference. We compared two conditions on the same smelly\_code.jsonl dataset: (1) _No annotation_, using the raw code as input; and (2) _Explicit annotation_, where the prompt was augmented with statements such as “the code contains complex regular expressions” or “the code has an excessively long parameter list.” For each condition, we invoked DeepSeek‑R1 to perform inference and recorded both total token usage and inference time. We then computed token consumption per unit time to facilitate a direct comparison between annotated and unannotated prompts.

##### Analysis

Our study, conducted with a 70% code functionality similarity threshold (Tab. [VII](https://arxiv.org/html/2504.15989v2#S4.T7 "TABLE VII ‣ Analysis ‣ IV-D RQ4: Impact of Explicitly Indicating Code Smell Types in Prompts on Token Consumption[] ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")). During analysis, we examined the token consumption profiles under each prompting condition. Results (Fig. [10](https://arxiv.org/html/2504.15989v2#S4.F10 "Fig. 10 ‣ Analysis ‣ IV-D RQ4: Impact of Explicitly Indicating Code Smell Types in Prompts on Token Consumption[] ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency"))(Tab. [VI](https://arxiv.org/html/2504.15989v2#S4.T6 "TABLE VI ‣ Analysis ‣ IV-D RQ4: Impact of Explicitly Indicating Code Smell Types in Prompts on Token Consumption[] ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")) indicate that adding explicit smell annotations to the prompt yields a more efficient reasoning process, with a marked reduction in token usage. Explicit annotations supply the model with rich contextual cues about potential issues, enabling it to bypass redundant reasoning and validation steps. In contrast, without smell hints, the model must engage in multiple verification cycles to detect and interpret hidden code complexities, thereby inflating token consumption.

TABLE VI: Descriptive Statistics of Total Token Consumption for Smelly Code (With vs. Without Prompt Tips)

| Statistic | No Tips | With Tips |
| --- | --- | --- |
| Count | 300 | 300 |
| --- | --- | --- |
| Mean | 5876.49 | 4431.19 |
| Std. Dev. | 3172.77 | 2578.35 |
| Min | 1095.00 | 981.00 |
| 25% Quartile | 3419.50 | 2312.50 |
| Median (50%) | 5351.00 | 3685.00 |
| 75% Quartile | 7985.75 | 5820.00 |
| Max | 16440.00 | 15045.00 |

![Refer to caption](https://arxiv.org/html/2504.15989v2/5.png)Fig. 10: Comparison of total token consumption for smelly code with and without explicit prompt tipsTABLE VII: Comparison of CodeBLEU and Similarity Metrics with and without Tips

| Metric | Mean (With Tips) | Mean (No Tips) |
| --- | --- | --- |
| CodeBLEU | 0.1585 | 0.1665 |
| Code Similarity | 0.7248 | 0.7619 |

Findings – Explicit Smell PromptExplicitly indicating code smell types in the model prompt reduces token consumption by approximately 24.5% compared to unannotated prompts. The provision of clear smell-specific cues allows the model to focus its reasoning on relevant code fragments, eliminating unnecessary validation steps and lowering computational overhead. This strategy thus significantly enhances inference efficiency and mitigates resource usage in large‑model code reasoning.

### IV-ERQ5: Prompt Engineering Strategies beyond Refactoring

TABLE VIII: Mean Values of Key Metrics under Different Strategies

| Strategy | Comp‑Norm Code | Line‑Scaled Code | Comp‑Norm Token | Line‑Scaled Token |
| --- | --- | --- | --- | --- |
| Base | 0.1015 | 46.7589 | 0.6026 | 277.7973 |
| Context | 0.0858 | 39.3372 | 0.4979 | 229.0921 |
| Func | 0.0891 | 40.9848 | 0.5357 | 241.2648 |
| Total | 0.0981 | 44.9317 | 0.4760 | 220.8598 |
| DevOps | 0.0852 | 38.5346 | 0.4832 | 218.3642 |
| QAer | 0.0868 | 40.4725 | 0.5362 | 238.8191 |
| SEer | 0.0771 | 35.5723 | 0.5535 | 241.9891 |
| AbsCost | 0.0815 | 37.1547 | 0.4746 | 202.5059 |
| RelCost | 0.0859 | 38.7631 | 0.4038 | 178.0056 |
| Comb1 | 0.1001 | 44.5918 | 0.5705 | 245.4126 |
| Comb2 | 0.0981 | 44.1189 | 0.5301 | 227.1736 |

TABLE IX: CodeBLEU and Docstring Similarity Statistics

| Category | Method | Mean CodeBLEU | Mean Code-and-GC-Code Similarity |
| Baseline | - | 0.1803 | 0.7364 |
| Context Awareness | Code Context | 0.1783 | 0.7428 |
| Code Function | 0.2283 | 0.7668 |
| All Information | 0.2464 | 0.7828 |
| Responsibility Tuning | SEer | 0.1593 | 0.7210 |
| QAer | 0.1590 | 0.7369 |
| DevOps | 0.1570 | 0.7275 |
| Cost Sensitive | Absolutely | 0.1605 | 0.7011 |
| Relatively | 0.1799 | 0.6988 |
| Combination | All Methods | 0.2513 | 0.7635 |
| All Methods | 0.2505 | 0.7665 |

##### Experimental Design / Setup

To address RQ5, we designed a series of experiments(Fig. [5](https://arxiv.org/html/2504.15989v2#S4.F5 "Fig. 5 ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")) aimed at evaluating three prompt engineering strategies—Context Awareness, Responsibility Tuning, and Cost Sensitivity—and their effect on reducing token consumption during the model’s reasoning over smelly code. Each strategy modifies the input prompt to guide the model’s cognitive process, driving a more efficient and focused inference, while minimizing unnecessary token expenditure. Specifically:

- •


Context Awareness: By infusing the prompts with contextual details such as file path, module or function names, and surrounding code snippets, we enhanced the model’s comprehension of the code’s role within the broader system. This enriched context directs the model’s focus to key segments of the code, thereby avoiding redundant inference over irrelevant portions.

- •


Responsibility Tuning: Through the strategic assignment of roles—such as “software engineer,” “QA engineer,” or “DevOps engineer”—we framed the model’s reasoning process to align with a specific perspective. This role-induced focus curtailed extraneous reasoning steps and unnecessary pathways, leading to a more efficient processing of the code.

- •


Cost Sensitivity: We introduced explicit limits on the length of the generated output or the token count (e.g., maximum token budget). By constraining the token size, we controlled the model’s computational cost, particularly when generating larger code segments, effectively reducing token expenditure without sacrificing the integrity of the generation.


All experiments were performed on the same dataset of 300 smelly code snippets. For each condition, we invoked DeepSeek‑R1, recording the total token consumption and calculating token consumption per unit time. Results were compared against a baseline prompt, which did not include any specific optimization strategies.

##### Analysis

We analyzed token consumption across different prompt conditions by calculating tokens per unit time for each strategy. Our key observations include(Tab. [VIII](https://arxiv.org/html/2504.15989v2#S4.T8 "TABLE VIII ‣ IV-E RQ5: Prompt Engineering Strategies beyond Refactoring ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")):

- •


Context Awareness: By enriching the prompts with contextual information, the model was able to bypass irrelevant logic and focus on the core components of the code. On average, token consumption decreased by 15–20%, with more significant reductions observed in longer, more complex code fragments.

- •


Responsibility Tuning: Assigning roles to the model significantly honed its focus on task‑relevant reasoning steps. This strategy yielded a 10–15% reduction in token consumption, particularly in large‑scale codebases, where the model’s focus was sharpened by the specified role.

- •


Cost Sensitivity: Imposing limits on output length or token budget led to a 20–30% reduction in token expenditure. However, overly stringent constraints sometimes led to the truncation of essential code elements, resulting in reduced functional correctness and lower accuracy in the output.


Findings – Prompt Optimization StrategiesContext Awareness and Responsibility Tuning proved most effective for reducing token consumption beyond refactoring (Tab. [IX](https://arxiv.org/html/2504.15989v2#S4.T9 "TABLE IX ‣ IV-E RQ5: Prompt Engineering Strategies beyond Refactoring ‣ IV Experimental Study: Triggering the nano surge ‣ Optimizing Token Consumption in LLMs: A Nano Surge Approach for Code Reasoning Efficiency")). Context Awareness reduced token usage by 15–20%, especially for complex code, while Responsibility Tuning achieved a 10–15% reduction by specifying the model’s reasoning role. Cost Sensitivity reduced tokens by up to 30% but sometimes caused incomplete outputs. Overall, combining context-rich prompts with clear role framing offered notable efficiency gains without sacrificing functionality.These optimizations also improved code quality: codeBLEU scores increased by 38%, and function similarity improved by 4% compared to the baseline, demonstrating that prompt engineering can simultaneously enhance efficiency and output quality.

## V Discussion

This study has conducted preliminary observations and analyses of the token consumption behavior exhibited by Large Language Models (LLMs) in Chain of Thought (CoT) scenarios\[ [4](https://arxiv.org/html/2504.15989v2#bib.bib4 "")\]. The findings reveal complex interactions between code characteristics and token overhead, alongside several possibilities for optimization through external strategies\[ [24](https://arxiv.org/html/2504.15989v2#bib.bib24 "")\]. The specific behavior of LLMs in software engineering tasks, particularly their resource consumption patterns (such as token usage), often demonstrates a high degree of contextual dependency and certain ”black box” characteristics, rendering precise prediction solely through theoretical models challenging.

In this context, the long-standing empirical tradition within software engineering—which emphasizes understanding and mastering complex systems through iteration, observation, experimentation, and data-driven analysis—offers a potent perspective and methodology for addressing such challenges\[ [25](https://arxiv.org/html/2504.15989v2#bib.bib25 "")\]. When confronting the specific issue of token consumption by LLMs in code processing, an approach grounded in empiricism is particularly crucial\[ [26](https://arxiv.org/html/2504.15989v2#bib.bib26 "")\]. Empirically observing the actual impact of various factors, such as different code features and prompting strategies, on token consumption, and quantitatively evaluating the effectiveness of intervention measures (like the code refactoring and prompt optimization explored in this study), serves as an effective pathway to deeply understand and manage this novel form of ”resource consumption.” The analytical framework of the present study represents a preliminary endeavor guided by this philosophy.

Looking ahead, the role of LLMs in software development and associated areas of focus are expected to continually evolve. On one hand, as the capabilities of LLMs iteratively improve, the conformity of the code they generate, or their ability to understand and intelligently refactor existing code\[ [12](https://arxiv.org/html/2504.15989v2#bib.bib12 "")\], is poised for significant enhancement. This may gradually reduce the future need for manual attention to, and remediation of, traditional ”code smells,” potentially shifting the focus of software quality assurance to new dimensions. On the other hand, even if LLMs can efficiently process or generate ”smell-free,” high-quality code, the reasoning and generation processes essential for fulfilling complex requirements will inherently continue to incur token costs\[ [27](https://arxiv.org/html/2504.15989v2#bib.bib27 "")\]. Therefore, regardless of how the issue of ”smells” at the code level evolves, attention to token consumption will remain a core concern in the field of LLM-assisted software development. Optimizing the token efficiency of the generation process and striking the optimal balance between output quality, functional completeness, and resource expenditure will continue to be key challenges and top priorities for future research aimed at the sustainable application of this technology\[ [28](https://arxiv.org/html/2504.15989v2#bib.bib28 "")\].

## VI Related Work

Over recent years, with the widespread application of large language models (LLMs) in natural language processing (NLP)\[ [4](https://arxiv.org/html/2504.15989v2#bib.bib4 "")\], automated code generation and optimization have also made significant strides\[ [29](https://arxiv.org/html/2504.15989v2#bib.bib29 "")\]. Research in this area can be broadly categorized into three directions:

### VI-ACode Smell Detection and Refactoring

Code smells are syntactic or structural code features that do not immediately impair program functionality but degrade readability, maintainability, and extensibility\[ [30](https://arxiv.org/html/2504.15989v2#bib.bib30 "")\]\[ [12](https://arxiv.org/html/2504.15989v2#bib.bib12 "")\]\[ [13](https://arxiv.org/html/2504.15989v2#bib.bib13 "")\]. Tufano et al. (2017)\[ [31](https://arxiv.org/html/2504.15989v2#bib.bib31 "")\] proposed an automated smell detection approach by analyzing code structures and patterns to identify potential smell instances. Lanza (2003)\[ [11](https://arxiv.org/html/2504.15989v2#bib.bib11 "")\] further investigated the impact of code smells on program maintainability and introduced corresponding refactoring strategies. Many refactoring tools combine rule‑based and machine learning techniques to automatically detect and repair smells at scale\[ [30](https://arxiv.org/html/2504.15989v2#bib.bib30 "")\]. Nevertheless, efficiently handling complex smells remains challenging, particularly in deep learning–based inference where token consumption and computational overhead can escalate.

### VI-BLLM‑based Code Generation and Optimization

The advent of pre‑trained language models has spurred extensive research into LLM‑driven code generation. Models such as OpenAI’s GPT series and Codex, pre‑trained on large code corpora, demonstrate strong capabilities in code completion, synthesis, and repair. The Transformer architecture of Vaswani et al. (2017) laid the foundation for these tasks, and subsequent variants such as BERT and GPT have further advanced code generation quality. While LLMs capture syntactic and semantic patterns effectively, their inference often suffers from token inflation, especially when processing complex or smelly code. To mitigate this, researchers have explored integrating automated refactoring with LLM training to jointly optimize code quality and inference efficiency.

### VI-CIntegration of Code Quality and Inference Efficiency

Recently, there has been growing interest in coupling code quality improvements with inference efficiency in pre‑trained models. Pre‑trained models like DeepCode and CodeBERT exhibit strong performance in code quality assessment and automated repair. Empirical studies indicate that higher code quality not only enhances maintainability but also reduces inference costs. For example, Zhang et al. (2020) proposed a deep learning–based optimization method that produces functionally equivalent code while significantly lowering token consumption during inference. Building upon this work, our study investigates how code smell removal and prompt engineering jointly reduce token overhead, particularly on complex code fragments.

## VII Conclusion and Future Work

This study investigated strategies to optimize the reasoning pipeline of large language models (LLMs) in order to reduce token consumption when processing code with smells\[ [14](https://arxiv.org/html/2504.15989v2#bib.bib14 "")\]\[ [32](https://arxiv.org/html/2504.15989v2#bib.bib32 "")\]. We proposed a combined approach of automated code refactoring and prompt engineering\[ [19](https://arxiv.org/html/2504.15989v2#bib.bib19 "")\], and empirically demonstrated that these strategies significantly enhance inference efficiency\[ [6](https://arxiv.org/html/2504.15989v2#bib.bib6 "")\]. Our experimental results show that code smells substantially inflate token usage during inference, whereas eliminating smells via refactoring and refining prompts markedly reduces computational overhead\[ [12](https://arxiv.org/html/2504.15989v2#bib.bib12 "")\]\[ [13](https://arxiv.org/html/2504.15989v2#bib.bib13 "")\]. Specifically, code refactoring alone decreased token consumption by approximately 50%, explicit smell annotations in the prompt yielded a 24.5% reduction, and prompt engineering techniques such as Context Awareness and Responsibility Tuning provided further savings.

Despite these advances, several limitations and avenues for future work remain. First, our experiments focused exclusively on Java and the DeepSeek‑R1 model; future research should extend our methodology to other programming languages and inference engines to assess its generalizability. Second, while refactoring and prompt optimizations proved effective, complex code‐smell scenarios may still challenge inference efficiency\[ [6](https://arxiv.org/html/2504.15989v2#bib.bib6 "")\]. Enhancing model reasoning capabilities for diverse programming tasks and more intricate smell types warrants deeper investigation.

Moreover, current work has largely concentrated on token consumption and inference speed\[ [33](https://arxiv.org/html/2504.15989v2#bib.bib33 "")\]. Future studies should explore the trade‐off between generation quality and efficiency—for example, how to maintain code correctness, maintainability, and extensibility while further reducing token usage\[ [23](https://arxiv.org/html/2504.15989v2#bib.bib23 "")\]. Integrating automated refactoring with deep learning–based optimization could also offer practical guidance for improving code quality in large codebases and advancing intelligent programming tools.

In summary, this research provides new insights into token optimization in code generation and reasoning, highlighting the pivotal role of smell elimination and prompt refinement. As intelligent coding assistants evolve, we anticipate that more granular reasoning optimizations and strategic prompt designs will enable next‐generation code generation systems to meet real‐world demands with greater efficiency and precision.

## References

- \[1\]
DeepSeek-AI, D. Guo, D. Yang, H. Zhang, J. Song, R. Zhang, R. Xu, Q. Zhu,
S. Ma, P. Wang, X. Bi, X. Zhang, X. Yu, Y. Wu, Z. F. Wu, Z. Gou, Z. Shao,
Z. Li, Z. Gao, A. Liu, B. Xue, B. Wang, B. Wu, B. Feng, C. Lu, C. Zhao,
C. Deng, C. Zhang, C. Ruan, D. Dai, D. Chen, D. Ji, E. Li, F. Lin, F. Dai,
F. Luo, G. Hao, G. Chen, G. Li, H. Zhang, H. Bao, H. Xu, H. Wang, H. Ding,
H. Xin, H. Gao, H. Qu, H. Li, J. Guo, J. Li, J. Wang, J. Chen, J. Yuan,
J. Qiu, J. Li, J. L. Cai, J. Ni, J. Liang, J. Chen, K. Dong, K. Hu, K. Gao,
K. Guan, K. Huang, K. Yu, L. Wang, L. Zhang, L. Zhao, L. Wang, L. Zhang,
L. Xu, L. Xia, M. Zhang, M. Zhang, M. Tang, M. Li, M. Wang, M. Li, N. Tian,
P. Huang, P. Zhang, Q. Wang, Q. Chen, Q. Du, R. Ge, R. Zhang, R. Pan,
R. Wang, R. J. Chen, R. L. Jin, R. Chen, S. Lu, S. Zhou, S. Chen, S. Ye,
S. Wang, S. Yu, S. Zhou, S. Pan, S. S. Li, S. Zhou, S. Wu, S. Ye, T. Yun,
T. Pei, T. Sun, T. Wang, W. Zeng, W. Zhao, W. Liu, W. Liang, W. Gao, W. Yu,
W. Zhang, W. L. Xiao, W. An, X. Liu, X. Wang, X. Chen, X. Nie, X. Cheng,
X. Liu, X. Xie, X. Liu, X. Yang, X. Li, X. Su, X. Lin, X. Q. Li, X. Jin,
X. Shen, X. Chen, X. Sun, X. Wang, X. Song, X. Zhou, X. Wang, X. Shan, Y. K.
Li, Y. Q. Wang, Y. X. Wei, Y. Zhang, Y. Xu, Y. Li, Y. Zhao, Y. Sun, Y. Wang,
Y. Yu, Y. Zhang, Y. Shi, Y. Xiong, Y. He, Y. Piao, Y. Wang, Y. Tan, Y. Ma,
Y. Liu, Y. Guo, Y. Ou, Y. Wang, Y. Gong, Y. Zou, Y. He, Y. Xiong, Y. Luo,
Y. You, Y. Liu, Y. Zhou, Y. X. Zhu, Y. Xu, Y. Huang, Y. Li, Y. Zheng, Y. Zhu,
Y. Ma, Y. Tang, Y. Zha, Y. Yan, Z. Z. Ren, Z. Ren, Z. Sha, Z. Fu, Z. Xu,
Z. Xie, Z. Zhang, Z. Hao, Z. Ma, Z. Yan, Z. Wu, Z. Gu, Z. Zhu, Z. Liu, Z. Li,
Z. Xie, Z. Song, Z. Pan, Z. Huang, Z. Xu, Z. Zhang, and Z. Zhang,
“Deepseek-r1: Incentivizing reasoning capability in llms via reinforcement
learning,” 2025. \[Online\]. Available: [https://arxiv.org/abs/2501.12948](https://arxiv.org/abs/2501.12948 "")
- \[2\]
J. Cao, M. Li, M. Wen, and S. chi Cheung, “A study on prompt design,
advantages and limitations of chatgpt for deep learning program repair,”
2023\. \[Online\]. Available: [https://arxiv.org/abs/2304.08191](https://arxiv.org/abs/2304.08191 "")
- \[3\]
S. L. Nikiema, J. Samhi, A. K. Kaboré, J. Klein, and T. F. Bissyandé,
“The code barrier: What llms actually understand?” _arXiv preprint_
_arXiv:2504.10557_, 2025.

- \[4\]
X. Hou, Y. Zhao, Y. Liu, Z. Yang, K. Wang, L. Li, X. Luo, D. Lo, J. Grundy, and
H. Wang, “Large language models for software engineering: A systematic
literature review,” 2024. \[Online\]. Available:
[https://arxiv.org/abs/2308.10620](https://arxiv.org/abs/2308.10620 "")
- \[5\]
M. Kang, J. Jeong, S. Lee, J. Cho, and S. J. Hwang, “Distilling llm agent into
small models with retrieval and code tools,” 2025. \[Online\]. Available:
[https://arxiv.org/abs/2505.17612](https://arxiv.org/abs/2505.17612 "")
- \[6\]
L. Guo, Y. Wang, E. Shi, W. Zhong, H. Zhang, J. Chen, R. Zhang, Y. Ma, and
Z. Zheng, “When to stop? towards efficient code generation in llms with
excess token prevention,” 2024. \[Online\]. Available:
[https://arxiv.org/abs/2407.20042](https://arxiv.org/abs/2407.20042 "")
- \[7\]
M. Dolata, N. Lange, and G. Schwabe, “Development in times of hype: How
freelancers explore generative ai?” in _Proceedings of the IEEE/ACM_
_46th International Conference on Software Engineering_, ser. ICSE
’24. ACM, Apr. 2024, p. 1–13.
\[Online\]. Available: [http://dx.doi.org/10.1145/3597503.3639111](http://dx.doi.org/10.1145/3597503.3639111 "")
- \[8\]
F. Luo, Y.-N. Chuang, G. Wang, H. A. D. Le, S. Zhong, H. Liu, J. Yuan, Y. Sui,
V. Braverman, V. Chaudhary, and X. Hu, “Autol2s: Auto long-short reasoning
for efficient large language models,” 2025. \[Online\]. Available:
[https://arxiv.org/abs/2505.22662](https://arxiv.org/abs/2505.22662 "")
- \[9\]
R. M. G. Alarcia, “Optimizing token usage on large language model
conversations using the design structure matrix,” in _Proceedings of_
_the 26th International DSM Conference (DSM 2024), Stuttgart, Germany_, ser.
DSM 2024. The Design Society, 2024, p.
069–078. \[Online\]. Available: [http://dx.doi.org/10.35199/dsm2024.08](http://dx.doi.org/10.35199/dsm2024.08 "")
- \[10\]
E. Yeo, Y. Tong, M. Niu, G. Neubig, and X. Yue, “Demystifying long
chain-of-thought reasoning in llms,” 2025. \[Online\]. Available:
[https://arxiv.org/abs/2502.03373](https://arxiv.org/abs/2502.03373 "")
- \[11\]
M. Lanza and S. Ducasse, “Polymetric views - a lightweight visual approach to
reverse engineering,” _IEEE Transactions on Software Engineering_,
vol. 29, no. 9, pp. 782–795, 2003.

- \[12\]_Refactoring: improving the design of existing code_. USA: Addison-Wesley Longman Publishing Co., Inc., 1999.

- \[13\]
M. Mantyla, J. Vanhanen, and C. Lassenius, “Bad smells - humans as code
critics,” in _20th IEEE International Conference on Software_
_Maintenance, 2004. Proceedings._, 2004, pp. 399–408.

- \[14\]
B. Zhang, P. Liang, X. Zhou, X. Zhou, D. Lo, Q. Feng, Z. Li, and L. Li, “A
comprehensive evaluation of parameter-efficient fine-tuning on method-level
code smell detection,” 2024. \[Online\]. Available:
[https://arxiv.org/abs/2412.13801](https://arxiv.org/abs/2412.13801 "")
- \[15\]
I. Zakharov, E. Koshchenko, and A. Sergeyuk, “Ai in software engineering:
Perceived roles and their impact on adoption,” 2025. \[Online\]. Available:
[https://arxiv.org/abs/2504.20329](https://arxiv.org/abs/2504.20329 "")
- \[16\]
K. Nghiem, A. M. Nguyen, and N. D. Q. Bui, “Envisioning the next-generation ai
coding assistants: Insights & proposals,” 2024. \[Online\]. Available:
[https://arxiv.org/abs/2403.14592](https://arxiv.org/abs/2403.14592 "")
- \[17\]
Q. Sun, Z. Chen, F. Xu, K. Cheng, C. Ma, Z. Yin, J. Wang, C. Han, R. Zhu,
S. Yuan, Q. Guo, X. Qiu, P. Yin, X. Li, F. Yuan, L. Kong, X. Li, and Z. Wu,
“A survey of neural code intelligence: Paradigms, advances and beyond,”
2025\. \[Online\]. Available: [https://arxiv.org/abs/2403.14734](https://arxiv.org/abs/2403.14734 "")
- \[18\]
F. Zhang, Z. Zhang, J. W. Keung, X. Tang, Z. Yang, X. Yu, and W. Hu, “Data
preparation for deep learning based code smell detection: A systematic
literature review,” 2024. \[Online\]. Available:
[https://arxiv.org/abs/2406.19240](https://arxiv.org/abs/2406.19240 "")
- \[19\]
J. Cordeiro, S. Noei, and Y. Zou, “An empirical study on the code refactoring
capability of large language models,” 2024. \[Online\]. Available:
[https://arxiv.org/abs/2411.02320](https://arxiv.org/abs/2411.02320 "")
- \[20\]
Y. Xie, J. Vandermause, S. Ramakers, N. H. Protik, A. Johansson, and
B. Kozinsky, “Uncertainty-aware molecular dynamics from bayesian active
learning for phase transformations and thermal transport in sic,” 2023.
\[Online\]. Available: [https://arxiv.org/abs/2203.03824](https://arxiv.org/abs/2203.03824 "")
- \[21\]
S. Hasan and S. Basak, “Open-source ai-powered optimization in scalene:
Advancing python performance profiling with deepseek-r1 and llama 3.2,”
2025\. \[Online\]. Available: [https://arxiv.org/abs/2502.10299](https://arxiv.org/abs/2502.10299 "")
- \[22\]
R. Khojah, F. G. de Oliveira Neto, M. Mohamad, and P. Leitner, “The impact of
prompt programming on function-level code generation,” 2024. \[Online\].
Available: [https://arxiv.org/abs/2412.20545](https://arxiv.org/abs/2412.20545 "")
- \[23\]
H. Liu, Y. Zhang, V. Saikrishna, Q. Tian, and K. Zheng, “Prompt learning for
multi-label code smell detection: A promising approach,” 2024. \[Online\].
Available: [https://arxiv.org/abs/2402.10398](https://arxiv.org/abs/2402.10398 "")
- \[24\]
F. Zhu, P. Wang, and Z. Sui, “Chain-of-thought tokens are computer program
variables,” 2025. \[Online\]. Available:
[https://arxiv.org/abs/2505.04955](https://arxiv.org/abs/2505.04955 "")
- \[25\]
Z. Li and X. Lu, “Research on compressed input sequences based on compiler
tokenization,” _Information_, vol. 16, no. 2, 2025. \[Online\].
Available: [https://www.mdpi.com/2078-2489/16/2/73](https://www.mdpi.com/2078-2489/16/2/73 "")
- \[26\]
S. Fan, P. Han, S. Shang, Y. Wang, and A. Sun, “Cothink: Token-efficient
reasoning via instruct models guiding reasoning models,” 2025. \[Online\].
Available: [https://arxiv.org/abs/2505.22017](https://arxiv.org/abs/2505.22017 "")
- \[27\]
A. Velasco, D. Rodriguez-Cardenas, L. R. Alif, D. N. Palacio, and
D. Poshyvanyk, “How propense are large language models at producing code
smells? a benchmarking study,” 2025. \[Online\]. Available:
[https://arxiv.org/abs/2412.18989](https://arxiv.org/abs/2412.18989 "")
- \[28\]
S. Rasnayaka, G. Wang, R. Shariffdeen, and G. N. Iyer, “An empirical study on
usage and perceptions of llms in a software engineering project,” 2024.
\[Online\]. Available: [https://arxiv.org/abs/2401.16186](https://arxiv.org/abs/2401.16186 "")
- \[29\]
G. Yang, Y. Zhou, X. Chen, X. Zhang, T. Y. Zhuo, and T. Chen,
“Chain-of-thought in neural code generation: From and for lightweight
language models,” 2024. \[Online\]. Available:
[https://arxiv.org/abs/2312.05562](https://arxiv.org/abs/2312.05562 "")
- \[30\]
H. Zhang, L. Cruz, and A. van Deursen, “Code smells for machine learning
applications,” 2022. \[Online\]. Available:
[https://arxiv.org/abs/2203.13746](https://arxiv.org/abs/2203.13746 "")
- \[31\]
A. Mastropaolo, S. Scalabrino, N. Cooper, D. N. Palacio, D. Poshyvanyk,
R. Oliveto, and G. Bavota, “Studying the usage of text-to-text transfer
transformer to support code-related tasks,” 2021. \[Online\]. Available:
[https://arxiv.org/abs/2102.02017](https://arxiv.org/abs/2102.02017 "")
- \[32\]
S. Lu, D. Guo, S. Ren, J. Huang, A. Svyatkovskiy, A. Blanco, C. Clement,
D. Drain, D. Jiang, D. Tang, G. Li, L. Zhou, L. Shou, L. Zhou, M. Tufano,
M. Gong, M. Zhou, N. Duan, N. Sundaresan, S. K. Deng, S. Fu, and S. Liu,
“Codexglue: A machine learning benchmark dataset for code understanding and
generation,” 2021. \[Online\]. Available:
[https://arxiv.org/abs/2102.04664](https://arxiv.org/abs/2102.04664 "")
- \[33\]
Z.-Z. Li, D. Zhang, M.-L. Zhang, J. Zhang, Z. Liu, Y. Yao, H. Xu, J. Zheng,
P.-J. Wang, X. Chen, Y. Zhang, F. Yin, J. Dong, Z. Guo, L. Song, and C.-L.
Liu, “From system 1 to system 2: A survey of reasoning large language
models,” 2025. \[Online\]. Available: [https://arxiv.org/abs/2502.17419](https://arxiv.org/abs/2502.17419 "")