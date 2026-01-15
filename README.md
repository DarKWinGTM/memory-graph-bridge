# Memory Graph Bridge

![License](https://img.shields.io/github/license/DarKWinGTM/memory-graph-bridge?style=flat-square)
![GitHub stars](https://img.shields.io/github/stars/DarKWinGTM/memory-graph-bridge?style=flat-square)
![GitHub issues](https://img.shields.io/github/issues/DarKWinGTM/memory-graph-bridge?style=flat-square)
![GitHub last commit](https://img.shields.io/github/last-commit/DarKWinGTM/memory-graph-bridge?style=flat-square)

![Memory Graph Bridge](./img/image_20260116_021122_0.png)

## 🌟 Overview

The **Memory Graph Bridge** is a specialized integration system designed to connect **Claude-Mem** (observation storage) with **Memora** (knowledge graph). This bridge enables seamless synchronization, visualization, and analysis of AI memory systems, creating a unified knowledge base that combines the structured logging of Claude-Mem with the graph capabilities of Memora.

> 🚀 **Goal**: Bridge the gap between linear observation logs and structured knowledge graphs.

## 🔗 Ecosystem References

This project serves as the connecting link between two powerful memory systems within the **DarKWinGTM Node Network**:

| System | Role | Function | Status |
|--------|------|----------|--------|
| **[Claude-Mem](https://github.com/DarKWinGTM/claude-mem)** | 🧠 **Short/Long-term Memory** | Logs observations, decisions, and task history in a linear, time-based format. | ![Stars](https://img.shields.io/github/stars/DarKWinGTM/claude-mem?style=social) |
| **[Memora](https://github.com/agentic-mcp-tools/memora)** | 🕸️ **Knowledge Graph** | Provides graph-based knowledge representation, typed relationships, and visualization capabilities. | ![Stars](https://img.shields.io/github/stars/agentic-mcp-tools/memora?style=social) |

## 💡 Core Concepts

### 1. The Gap
- **Claude-Mem**: Excellent for logging "what happened" (Stream).
- **Memora**: Powerful for understanding "how things relate" (Graph).
- **The Bridge**: Connects these two systems to provide both historical context and structural understanding.

### 2. Solution Architecture
The system operates through a "Sync Bridge" that:
1.  📥 **Extracts** observations from Claude-Mem
2.  🔄 **Transforms** data into Memora-compatible format
3.  🔗 **Auto-links** related memories (Heuristic & Semantic)
4.  📊 **Exports** visualizations (Obsidian, vis.js, Mermaid)

## 🛠️ Implementation Approaches

This project offers two distinct implementation paths tailored to different needs:

### Path A: Full Bridge (Production)
- **Features**: CLI tools, custom Python API, advanced auto-linking, multiple export formats.
- **Best for**: Production systems, automated pipelines, large-scale graph analysis.

### Path B: Minimal Integration (Skill + Hook)
- **Features**: AI-driven analysis via Claude Code Skill (`/sync-memora`), hook-based auto-sync.
- **Best for**: Rapid setup, personal use, prototyping, leveraging AI reasoning for linking.

## 📂 Repository Structure

```tree
memory-graph-bridge/
├── README.md           # This file
├── img/                # Visual assets
│   └── image_*.png     # Concept visualization
└── skills/             # Prototype skills directory
    └── sync-memora/    # Ready-to-use sync skill
```

## 🚀 Getting Started

### Prerequisites
- Node.js ≥16
- Python ≥3.10
- Claude Code CLI

### Quick Setup (Minimal Mode)

1.  **Install Memora MCP**:
    Follow standard Memora installation instructions.

2.  **Install Skill**:
    ```bash
    mkdir -p ~/.claude/skills/sync-memora
    # Copy skill files from this repo
    cp skills/sync-memora/* ~/.claude/skills/sync-memora/
    ```

3.  **Run Sync**:
    In Claude Code:
    ```
    /sync-memora
    ```

## 📄 License

Distributed under the MIT License. See `LICENSE` for more information.

---
<div align="center">
  <p>Built with ❤️ by <a href="https://github.com/DarKWinGTM">DarKWinGTM</a></p>
</div>
