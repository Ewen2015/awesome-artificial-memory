<h1 align="center"> Awesome Artificial Memory<p align="center"> </h1

A curated list of resources, research papers, and projects exploring the concept of **Artificial Memory** (AM), with a special focus on the **Neuroscience of Forgetting** and its application to intelligent systems.

## 💡 The Core Idea: Forgetting as a Feature

Traditional Artificial Intelligence (AI) focuses on maximizing information retention. However, human intelligence is fundamentally linked to the ability to **selectively forget**. In the context of ever-growing data, Artificial Memory (AM) seeks to move beyond mere data storage to a system that actively manages, prioritizes, and prunes information to maintain adaptability, efficiency, and relevance.

> "The purpose of memory is not to store the past, but to anticipate the future." - **The ability to forget is not a bug, but a feature** that enhances cognitive function and adaptability.

## 🧠 Neuroscience of Memory and Forgetting

Resources detailing the biological and psychological mechanisms of memory consolidation and active forgetting.

### Forgetting as an Active Process

*   **[Paper] The Biology of Forgetting – A Perspective** [1]
    *   *Focus:* Discusses active forgetting mechanisms, including neurogenesis-based forgetting, interference-based forgetting, and intrinsic forgetting.
*   **[Article] Not a Flaw, But a Brain's Hidden Feature of Memory** [2]
    *   *Focus:* Explores how forgetting irrelevant memories fosters adaptability and better decision-making in shifting environments.
*   **[Article] Why Forgetting is Good for Your Memory** [3]
    *   *Focus:* Explains the cognitive benefits of forgetting, such as prioritizing information, improving decision-making, and enhancing creativity.

### Computational Neuroscience Models

*   **[Paper] Computational model reveals how the brain manages short-term memories** [11]
    *   *Focus:* A computational model showing how the brain maintains short-term information using specific types of neurons, suggesting a mechanism for selective retention.
*   **[Paper] Forgetting Enhances Episodic Control With Structured Representations** [12]
    *   *Focus:* Computational study demonstrating that forgetting can benefit the performance of Reinforcement Learning (RL) agents by preventing over-reliance on outdated state information.
*   **[Paper] Retroactive interference model of forgetting** [13]
    *   *Focus:* Mathematical model proposing that memory can be erased due to subsequently acquired memories (retroactive interference), a key mechanism for active pruning.

### Memory Consolidation and Trace

*   **[Paper] The seven sins of memory: insights from psychology and cognitive neuroscience** [4]
    *   *Focus:* A classic paper by Daniel Schacter that frames memory failures (including forgetting) as byproducts of otherwise adaptive features of memory.

## 💻 Computational Models and AI Challenges

Resources on how the principles of forgetting are being applied or should be applied to artificial systems, particularly Large Language Models (LLMs) and AI Agents.

### Controlled Forgetting and Pruning

*   **[Survey] ”Forgetting” in Machine Learning and Beyond: A Survey** [5]
    *   *Focus:* A comprehensive review of forgetting mechanisms in machine learning, highlighting the fundamental distinction between human and machine memory.
*   **[Article] Controlled Forgetting: The Next Big Challenge in AI's Memory** [6]
    *   *Focus:* Discusses the emerging field of controlled forgetting and its necessity for building more human-like, adaptive AI.
*   **[Paper] Bayesian continual learning and forgetting in neural networks** [7]
    *   *Focus:* Presents a model that balances learning from a continuous data stream with forgetting to prevent saturation and overconfidence.

### Machine Unlearning (Intentional Forgetting)

*   **[Survey] A Comprehensive Survey of Machine Unlearning for Large Language Models** [14]
    *   *Focus:* Systematic review of over 180 papers on LLM unlearning, focusing on techniques to intentionally remove data from large-scale models.
*   **[Survey] Machine Unlearning: A Survey** [15]
    *   *Focus:* General survey on machine unlearning, where the goal is to reproduce a model that behaves as if trained without seeing the unlearned data.
*   **[Article] Forget LLM Memory – Why LLMs Need Adaptive Forgetting** [16]
    *   *Focus:* Argues that intentional forgetting can refine learning processes, bolster flexibility, and amplify adaptability in LLMs.


## 🛠️ Tools and Frameworks (Memory Infrastructure)

Tools that provide fine-grained control over memory, which is essential for implementing selective forgetting.

*   **[Tool] Zep**
    *   *Focus:* Context Engineering Platform for AI Agents. Provides structured memory (Facts, Entities) and fine-grained API control, enabling selective deletion based on metadata.
*   **[Tool] Mem0**
    *   *Focus:* Universal memory layer for LLM applications. Focuses on memory compression and context optimization, which is a form of functional forgetting.
*   **[Project] coa-goldfish-mcp** [anortham/coa-goldfish-mcp - GitHub](https://github.com/anortham/coa-goldfish-mcp)
    *   *Focus:* An example project that explicitly embraces **forgetting as a feature** by automatically expiring old memories to keep the working context fresh and focused.

## 🤝 Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to submit new resources.

