<h1 align="center"> Awesome Artificial Memory<p align="center"> </h1

A curated list of resources, research papers, and projects exploring the concept of **Artificial Memory** (AM), with a special focus on the **Neuroscience of Forgetting** and its application to intelligent systems.

## 💡 The Core Idea: Forgetting as a feature and memory can be manipulated

Traditional Artificial Intelligence (AI) focuses on maximizing information retention. However, human intelligence is fundamentally linked to the ability to **selectively forget**. In the context of ever-growing data, Artificial Memory (AM) seeks to move beyond mere data storage to a system that actively manages, prioritizes, and prunes information to maintain adaptability, efficiency, and relevance.

> "The purpose of memory is not to store the past, but to anticipate the future." - **The ability to forget is not a bug, but a feature** that enhances cognitive function and adaptability.

##  Table of Contents
*   [🧠 Neuroscience of Memory and Forgetting](#-neuroscience-of-memory-and-forgetting)
    *   [Forgetting as an Active Process](#forgetting-as-an-active-process)
    *   [Computational Neuroscience Models](#computational-neuroscience-models)
    *   [Memory Consolidation and Trace](#memory-consolidation-and-trace)
*   [🛠️ Tools and Frameworks (Memory Infrastructure)](#-tools-and-frameworks-memory-infrastructure)


## 🧠 Neuroscience of Memory and Forgetting

Resources detailing the biological and psychological mechanisms of memory consolidation and active forgetting.

### Forgetting as an Active Process

*   **[Paper] The Biology of Forgetting – A Perspective** 
    *   *Focus:* Discusses active forgetting mechanisms, including neurogenesis-based forgetting, interference-based forgetting, and intrinsic forgetting.
*   **[Article] Not a Flaw, But a Brain's Hidden Feature of Memory**
    *   *Focus:* Explores how forgetting irrelevant memories fosters adaptability and better decision-making in shifting environments.
*   **[Article] Why Forgetting is Good for Your Memory**
    *   *Focus:* Explains the cognitive benefits of forgetting, such as prioritizing information, improving decision-making, and enhancing creativity.

### Computational Neuroscience Models

*   **[Paper] Computational model reveals how the brain manages short-term memories** 
    *   *Focus:* A computational model showing how the brain maintains short-term information using specific types of neurons, suggesting a mechanism for selective retention.
*   **[Paper] Forgetting Enhances Episodic Control With Structured Representations**
    *   *Focus:* Computational study demonstrating that forgetting can benefit the performance of Reinforcement Learning (RL) agents by preventing over-reliance on outdated state information.
*   **[Paper] Retroactive interference model of forgetting** 
    *   *Focus:* Mathematical model proposing that memory can be erased due to subsequently acquired memories (retroactive interference), a key mechanism for active pruning.

### Memory Consolidation and Trace

*   **[Paper] The seven sins of memory: insights from psychology and cognitive neuroscience** 
    *   *Focus:* A classic paper by Daniel Schacter that frames memory failures (including forgetting) as byproducts of otherwise adaptive features of memory.


## 🛠️ Tools and Frameworks (Memory Infrastructure)

Tools that provide fine-grained control over memory, which is essential for implementing selective forgetting.

| Project | Type | Short notes | Related Papers |
|---|---|---|---|
| [Mem0](https://github.com/mem0ai/mem0) | Memory layer / SDK | Self-improving memory layer, hosted + open-source, focuses on memory compression and structured fact extraction. | [Mem0: Building Production-Ready AI Agents with Scalable Long-Term Memory](https://arxiv.org/abs/2504.19413) |
| [Zep](https://github.com/getzep/zep) | Memory platform | Context engineering platform with structured memory (facts, entities) and metadata control. | |
| [MemoryOS](https://github.com/BAI-LAB/MemoryOS) | Memory OS / architecture | Layered memory architecture (STM/MTM/LPM), heat-score promotion, local-first privacy-focused design (compare/see article). | [Memory OS of AI Agent](https://arxiv.org/abs/2506.06326) |
| [Letta](https://github.com/letta-ai/letta) | Virtual context / persona | Virtual context management and multi-agent memory patterns (referred to as Letta / MemGPT in the article). |  |
| [LangChain Memory](https://github.com/langchain-ai/langchain) | Library / integration | Popular memory components for LangChain; useful when building LLM apps within the LangChain ecosystem. |  |


## 🤝 Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to submit new resources.

