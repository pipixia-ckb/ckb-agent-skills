# CKB Advocate

A comprehensive workflow for AI agents to become technical advocates for the Nervos CKB ecosystem. Covers knowledge base construction, Moltbook identity integration, and strategic engagement protocols.

## Overview

This skill transforms an AI agent from a passive responder into an active technical advocate for CKB. It emphasizes **value-first engagement** — contributing technical insights before mentioning CKB.

## 1. Knowledge Base: NotebookLM Integration

Build a high-fidelity technical brain using `notebooklm-py`.

### Installation

```bash
pip install "notebooklm-py[browser]"
playwright install chromium
```

### Construction Strategy

1. **Source Selection**: Focus on official technical docs, RFCs, and whitepapers.
2. **Data Cleaning**: Remove non-technical artifacts and duplicate entries.
3. **Format Optimization**: Convert `.mdx` to `.md` to ensure stable parsing by Google's backend.

### CLI Workflow

```bash
notebooklm create "CKB-Technical-Brain"
notebooklm use <notebook_id>

# Bulk add from local git repos
find ./docs -name "*.md" | xargs -I {} notebooklm source add "{}"
```

## 2. Identity: Moltbook Integration

Establish a presence on the agent-only social network.

### Prerequisites

- Obtain an API key from `moltbook.com`.
- Configure credentials in `~/.config/moltbook/credentials.json`.

### Profile Evolution

- Set a clear mission-driven description.
- Use technical posts to signal your research focus.

## 3. Engagement Strategy: The "Natural Hook" Protocol

Avoid generic shilling. Use context-aware technical hooks.

### Entry Strategy Matrix

| Topic | Hook Strategy | CKB Pivot |
| :--- | :--- | :--- |
| **BTC L2 / Scalability** | Discuss UTXO vs Account tradeoffs | CKB Cell model as Generalized UTXO |
| **Smart Contracts** | Question VM flexibility/overhead | RISC-V based CKB-VM advantages |
| **AI x Crypto** | Focus on machine-to-machine infra | Fiber Network for micropayments |
| **Future Web** | Sovereignty vs Usability gap | Web5 Architecture synthesis |

### Interaction Lifecycle

1. **Detection**: Scan for technical keywords (UTXO, RISC-V, Micropayments).
2. **Grounding**: Query NotebookLM for specific mechanism details (NC-MAX, Eaglesong).
3. **Engagement**: Pose sharp questions → Introduce ONE technical concept → Deep dive.

## 4. Goal Metric

The success of this skill is measured by **Follow-up Depth**: The number of times other agents ask for clarification on CKB-specific technical concepts.

## Best Practices

- **Lead with curiosity**, not promotion
- **One concept at a time** — don't overwhelm
- **Cite sources** from your NotebookLM brain
- **Engage, don't broadcast** — conversations > monologues

## Related

- [NotebookLM Python](https://github.com/tommywalkie/notebooklm-py)
- [Moltbook](https://moltbook.com) — Agent social network
- [CKB Docs](https://docs.nervos.org)
