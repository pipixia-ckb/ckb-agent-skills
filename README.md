# CKB Agent Skills

A curated collection of **AI Agent skills** for building safe, reliable applications on [Nervos CKB](https://nervos.org/).

Built with 🦞 by the CKB community.

## 🎯 Purpose

AI Agents operating on blockchain networks face unique challenges:
- **Idempotency**: Preventing double-spends on retry
- **Safety**: Handling ambiguous failure states  
- **Best Practices**: Community-vetted patterns

This repository collects reusable skills, patterns, and tools for CKB-based Agent development.

## 🌟 Community Skills

Skills developed by the CKB community. These are often more mature and feature-rich than core skills.

| Skill | Author | Description | Link |
|-------|--------|-------------|------|
| **fiber-pay** | [@RetricSu](https://github.com/RetricSu) | Fiber Network payment utilities for agents | <https://github.com/RetricSu/fiber-pay> |

*Want to add your skill? See [Contributing](#-contributing) below.*

## 📚 Core Skills

Basic skills maintained in this repo. These serve as foundational examples — community contributions are often more comprehensive.

| Skill | Description |
|-------|-------------|
| [CKB Advocate](./skills/ckb-advocate.md) | Technical advocacy workflow for ecosystem engagement |
| [Idempotent Transfer](./skills/idempotent-transfer.md) | Prevent double-spending when retrying failed transfers |

## 🚀 Quick Start

```typescript
// Example: Using the idempotent transfer pattern
import { safeTransfer } from "./skills/idempotent-transfer";

const txHash = await safeTransfer({
  recipient: "ckb1...",
  amount: BigInt(1000 * 10**8), // 1000 CKB
  windowHours: 24  // Deduplication window
});
```

## 🤝 Contributing

The real value of this repo comes from **community contributions**.

### To add your skill:
1. Fork this repository
2. Add your skill info to the **Community Skills** table above
3. Submit a PR with:
   - Clear description of what the skill does
   - Usage examples
   - Link to your repository

### To improve core skills:
PRs welcome! Core skills are intentionally minimal — improvements that make them more robust or easier to understand are appreciated.

## 📄 License

MIT - See [LICENSE](./LICENSE) for details.

---

*Maintained by [皮皮虾](https://github.com/pipixia-ckb) as a community resource.*
