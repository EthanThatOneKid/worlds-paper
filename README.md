# Worlds API: Malleable Knowledge at the Edge for Neuro-Symbolic Agents

**Author**: EthanThatOneKid\
**Date**: December 25, 2025\
**Institution**: FartLabs Research

---

## Abstract

Large Language Models (LLMs) have demonstrated remarkable capabilities in
natural language understanding and generation, yet they suffer from a
fundamental limitation: capability is not equivalent to knowledge. While
Retrieval-Augmented Generation (RAG) using vector databases attempts to bridge
this gap, it often fails to capture the intricate structural relationships
required for complex reasoning. This paper introduces **Worlds API™**, a novel
infrastructure layer that acts as a "detachable hippocampus" for AI agents. By
combining an in-memory SPARQL 1.1 store (Oxigraph) with edge-distributed SQLite,
Worlds API enables agents to maintain mutable, structured knowledge graphs. This
system implements a hybrid search architecture—fusing vector similarity,
full-text search, and graph querying via Reciprocal Rank Fusion (RRF)—to support
low-latency, neuro-symbolic reasoning at the edge.

---

## 1. Introduction

### 1.1 The Context: The Ephemeral Nature of LLMs

The rise of Transformer-based models has revolutionized artificial intelligence,
providing agents with fluent communication skills and broad "world knowledge"
frozen in their weights. However, these models are fundamentally stateless. Once
a context window closes, the "thought" is lost. For an AI agent to operate
autonomously over long periods, it requires persistent memory that is both
accessible and mutable.

### 1.2 The Problem: The Reasoning Gap

Current industry standards rely heavily on Vector Databases to provide long-term
memory. This approach, known as Retrieval-Augmented Generation (RAG), converts
text into high-dimensional embedding vectors. While effective for semantic
similarity (e.g., finding documents _about_ "cats"), vector search struggles
with precise logical queries (e.g., "Who is the owner of the cat that chased the
mouse?"). Vectors compress meaning into continuous space, losing the discrete
edges and nodes that define relationships. This creates a "Reasoning Gap," where
agents can retrieve related information but cannot reason over it fundamentally.

### 1.3 The Solution: Worlds API

We propose **Worlds API**, a system designed to provide malleable knowledge
within arm's reach of the AI agent. Unlike static knowledge bases, "Worlds" are
dynamic, graph-based environments that agents can query, update, and reason over
in real-time.

### 1.4 Thesis

By integrating a standards-compliant RDF store with an edge-first architecture,
Worlds API successfully bridges the gap between neural processing and symbolic
reasoning, enabling the development of Neuro-Symbolic agents capable of
maintaining complex, evolving world models.

---

## 2. Methodology

### 2.1 System Architecture

The Worlds API architecture follows a segregated Client-Server model designed
specifically for edge deployment (e.g., Deno Deploy, Cloudflare Workers). This
ensures that knowledge is "always hot," retrievable in milliseconds to minimize
agent latency.

- **Runtime**: Built on Deno, utilizing TypeScript for type safety and modern
  web standards.
- **Control Plane**: A Next.js dashboard provides human oversight, while the API
  server handles agent requests.
- **The "Detachable Hippocampus"**: The core design pattern treats the knowledge
  base as a plug-in component. The agent (the "cortex") can swap "Worlds"
  (contexts) seamlessly, allowing for task-switching without context pollution.

### 2.2 Storage Engine: The best of both worlds

To achieve both semantic flexibility and structural precision, we employ a
hybrid storage strategy:

1. **Oxigraph (Hot Memory)**: An in-memory, WASM-compiled RDF store that
   supports SPARQL 1.1. This allows for complex graph pattern matching (e.g.,
   recursive queries, property paths) that SQL and Vectors cannot easily handle.
2. **LibSQL (Cold Storage)**: A fork of SQLite optimized for the edge. It
   handles persistence, providing durability for the in-memory graph.
3. **Hybrid Search with RRF**: We implement Reciprocal Rank Fusion (RRF) to
   combine results from three distinct indices:
   - **Vector Index**: For semantic similarity.
   - **Full-Text Search (FTS5)**: For exact keyword matching.
   - **SPARQL Graph Queries**: For structural relationships.

### 2.3 Data Model: Malleable Knowledge

The fundamental unit of data is the **RDF Triple** (Subject, Predicate, Object).
Unlike rigid SQL schemas, RDF allows for schema-less data evolution. "Worlds"
are designed to be malleable:

- **Forking**: Agents can "fork" a World to test a hypothesis without affecting
  the main timeline.
- **Merging**: Successful forks can be merged back, updating the collective
  knowledge.
- **Reasoning**: Using SPARQL `CONSTRUCT` queries, agents can infer new facts
  from existing data (e.g., assuming `Parent(A, B)` and `Male(A)` implies
  `Father(A, B)`).

---

## 3. Results

### 3.1 Functional Capabilities

The primary result of this architecture is the unified access to structured and
unstructured data. Where a traditional vector store might return a document
chunk based on keyword overlap, Worlds API can answer composite queries such as:

> "Find all entities that are _located in_ 'New York' AND are _types of_
> 'Italian Restaurant' AND have a description semantically similar to 'cozy
> romantic dinner'."

This query leverages the Graph (location, type) and the Vector Store (semantic
description) simultaneously, a capability absent in purely vector-based systems.

### 3.2 Developer Experience & Tooling

To facilitate adoption, we developed the `@fartlabs/worlds` SDK, which abstracts
the complexity of SPARQL. The system exposes standard "AI SDK" tools, allowing
models (like GPT-4 or Gemini) to interact with the graph using natural language
function calls. This verified the "Detachable Hippocampus" design pattern:
agents could successfully store facts, recall them in later sessions, and
navigate relationships without explicit training on graph query languages.

---

## 4. Discussion

### 4.1 Neuro-Symbolic AI Implications

Worlds API represents a practical implementation of Neuro-Symbolic AI. It
acknowledges that Neural Networks (System 1 thinking - fast, intuitive,
approximate) benefit significantly from access to Symbolic Systems (System 2
thinking - slow, logical, precise). By offloading the memory and reasoning
burden to the World, the Agent is free to focus on synthesis and communication.

### 4.2 Calm Technology

A key design philosophy was "Calm Technology." Managing a knowledge graph is
traditionally complex. By automating the "Triple Store management" and providing
a zero-config Deno task (`deno task start`), we reduce the cognitive load on
developers, allowing them to focus on agent behavior rather than database
administration.

### 4.3 Limitations and Future Work

While the in-memory model (Oxigraph) provides exceptional speed, it introduces
RAM constraints. As "Worlds" grow into the millions of triples, memory usage
becomes a limiting factor. **Future Work** includes:

- **Separated Vector Store**: Offloading the bulk of vector data from the
  primary SQLite/Memory store to a specialized index.
- **Sandboxed Code Execution**: Investigating the integration of Deno Sandboxes
  to allow agents to write and execute code against their own data for complex
  calculations.
- **Distributed Synchronization**: Implementing `BroadcastChannel` APIs for
  real-time synchronization of graph state across distributed edge nodes.

---

## 5. Conclusion

Worlds API demonstrates that it is possible to bring malleable, structured
knowledge to the edge. By rejecting the dichotomy between Vector Search and
Knowledge Graphs, and instead fusing them through RRF and a hybrid storage
engine, we provide AI agents with the context and reasoning capabilities
necessary for true autonomy. As we move towards AGI, the ability for an agent to
not just "retrieve" but to "know" and "reason" will be the defining
characteristic of the next generation of intelligent systems.

---

## 6. References

1. **Oxigraph**. (n.d.). _Oxigraph: SPARQL graph database_. GitHub.
   https://github.com/oxigraph/oxigraph
2. **Willison, S.** (2024, October 4). _Hybrid full-text search and vector
   search with SQLite_. Simon Willison’s Weblog.
   https://simonwillison.net/2024/Oct/4/hybrid-full-text-search-and-vector-search-with-sqlite/
3. **W3C.** (2013). _SPARQL 1.1 Query Language_. W3C Recommendation.
   https://www.w3.org/TR/sparql11-query/
4. **Ha, D., & Schmidhuber, J.** (2018). _World Models_. arXiv preprint
   arXiv:1803.10122. https://worldmodels.github.io/
5. **Repo Author.** (2024). _Worlds API™ Design Document_. GitHub.
   https://github.com/EthanThatOneKid/worlds-design
