# .agent 目录说明

欢迎来到 NeuralFlow 项目的 `.agent` 目录！

这个目录包含项目的规则、记忆、文档和检查清单，是 AI Assistant 和团队成员的重要参考资料。

---

## ⚡ 新会话必读检查清单 (AI Agent Start Here!)

每次新会话开始时，**必须按顺序阅读**以下文档：

### 🔥 Tier 1: 核心架构（必读）
1. **[PROJECT_RULES.md](./PROJECT_RULES.md)** - 项目核心规则（包含第14章LLM架构规范）
2. **[memory/CODE_STRUCTURE.md](./memory/CODE_STRUCTURE.md)** - 项目架构图谱（v5.0，包含第4章LLM抽象层）
3. **[docs/llm-abstraction-layer-guide.md](./docs/llm-abstraction-layer-guide.md)** - LLM抽象层完整指引 ⭐⭐⭐⭐⭐

### 📖 Tier 2: 专项准则（推荐）
4. **[docs/native-bridge-defensive-guide.md](./docs/native-bridge-defensive-guide.md)** - 原生桥接防御
5. **[docs/steerable-agent-loop-design.md](./docs/steerable-agent-loop-design.md)** - 可控代理循环

### 📋 Tier 3: 流程文档（按需）
6. **[docs/release-protocol.md](./docs/release-protocol.md)** - 发布流程
7. **[docs/android-build-guide.md](./docs/android-build-guide.md)** - Android构建

---

## 📁 目录结构

```
.agent/
├── README.md                   # 本文档（新会话入口）
├── PROJECT_RULES.md            # 项目核心规则（v1.1）
├── docs/                       # 详细文档
│   ├── llm-abstraction-layer-guide.md      # 🔥 LLM架构完整指引
│   ├── product-requirements.md             # 产品需求规格
│   ├── android-build-guide.md              # Android构建
│   ├── native-bridge-defensive-guide.md    # 原生桥接防御
│   ├── release-protocol.md                 # 发布流程
│   ├── settings-panels-reference.md        # 设置面板参考
│   └── steerable-agent-loop-design.md      # 可控代理循环
├── memory/                     # 项目记忆
│   └── CODE_STRUCTURE.md       # 项目架构图谱（v5.0）
├── workflows/                  # 工作流定义
│   ├── build-android-release.md
│   └── reconstruct-worktree.md
└── checklists/                 # 检查清单
    └── CODE_REVIEW.md
```

---

## 🎯 关键文档说明

### LLM抽象层文档 🔥

**为什么重要**：
- 定义了所有LLM功能的三层架构（业务/抽象/网络）
- 确保"修A不坏B"的Provider独立性
- 新增Provider的唯一标准参考

**何时查阅**：
- ✅ **必须**：任何涉及LLM Provider的修改
- ✅ **必须**：添加新的LLM服务商
- ✅ **必须**：调试模型输出问题（XML泄露、reasoning错误等）
- ✅ **建议**：开发新的AI功能

**快速索引**：
- 添加新Provider → 第三章
- 调试现有Provider → 第四章 + 诊断位置表
- 架构原则 → 第二章
- 常见错误 → 第四章 最佳实践

### 项目规则（PROJECT_RULES.md）

**第14章：LLM抽象层架构规范**（新增，v1.1）
- 强制性架构准则
- 禁止事项清单
- 审查清单

### 代码架构（CODE_STRUCTURE.md）

**第4章：LLM抽象层架构**（新增，v5.0）
- 架构概览
- 组件清单
- 快速参考

---

## 📖 完整文档索引

### 核心架构文档

| 文档 | 版本 | 重要性 | 用途 |
|------|------|--------|------|
| `PROJECT_RULES.md` | v1.1 | ⭐⭐⭐⭐⭐ | 项目准则（包含LLM架构规范） |
| `memory/CODE_STRUCTURE.md` | v5.0 | ⭐⭐⭐⭐⭐ | 项目架构图谱 |
| `docs/llm-abstraction-layer-guide.md` | v1.0 | ⭐⭐⭐⭐⭐ | LLM架构完整指引 |

### 专项指南

| 文档 | 用途 | 维护频率 |
|------|------|----------|
| `native-bridge-defensive-guide.md` | 原生桥接防御 | 稳定 |
| `steerable-agent-loop-design.md` | 可控代理循环 | 稳定 |
| `android-build-guide.md` | Android编译 | 按需 |
| `release-protocol.md` | 发布流程 | 按需 |
| `settings-panels-reference.md` | 设置面板 | 随UI更新 |
| `product-requirements.md` | 产品需求 | 按版本更新 |

---

## 🚀 使用指南

### AI Assistant（新会话启动流程）

1. **自动扫描**: `.agent/` 目录
2. **必读文档**（按顺序）:
   - PROJECT_RULES.md（关注第14章）
   - memory/CODE_STRUCTURE.md（关注第4章）
   - docs/llm-abstraction-layer-guide.md
3. **任务前确认**: 是否涉及LLM？→ 复习架构文档

### 团队成员

1. **加入项目时**: 阅读 Tier 1 核心架构文档
2. **开发LLM功能**: **必须**先读LLM架构指引
3. **提交PR时**: 使用 `CODE_REVIEW.md` 自查
4. **遇到问题时**: 查看对应专项指南

---

## 📝 维护指南

### 何时更新

#### LLM架构文档（llm-abstraction-layer-guide.md）
- ✅ 新增Provider
- ✅ 架构优化
- ✅ 发现新陷阱

#### PROJECT_RULES.md
- ✅ 强制性架构变更
- ✅ 新的禁止事项
- ✅ 重要最佳实践

#### CODE_STRUCTURE.md
- ✅ 新增核心模块
- ✅ 重大重构
- ✅ 目录结构变化

### 更新原则
- ✅ 简洁明了，避免冗余
- ✅ 包含示例，易于理解
- ✅ 及时更新，保持时效
- ✅ 版本号清晰标注

---

## ⚠️ 关键提醒

### 对于AI Agent

**在处理任何LLM相关功能时，请问自己**：
1. [ ] 我是否已阅读 `llm-abstraction-layer-guide.md`？
2. [ ] 我的修改是否在正确的层级（业务/抽象/网络）？
3. [ ] 我是否遵守了第14章规范（禁止在业务层判断Provider）？
4. [ ] 我是否查阅了对应Provider的Formatter？

### 对于开发者

**修改前必读**：
- LLM功能 → `llm-abstraction-layer-guide.md`
- 原生调用 → `native-bridge-defensive-guide.md`
- 发布构建 → `release-protocol.md`

---

**维护者**: AI Assistant + Architecture Team  
**创建日期**: 2025-12-26  
**最后更新**: 2026-01-14（v2.0，新增LLM架构文档）
