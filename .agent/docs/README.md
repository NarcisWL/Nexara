# 📚 Nexara 文档中心

> **最后更新**: 2026-01-21  
> **版本**: v3.0

## 文档分类

### 🔥 核心架构文档

| 文档 | 版本 | 重要性 | 说明 |
|------|------|--------|------|
| **[llm-abstraction-layer-guide.md](./llm-abstraction-layer-guide.md)** | v1.0 | ⭐⭐⭐⭐⭐ | LLM抽象层完整指引（新会话必读） |
| **[product-requirements.md](./product-requirements.md)** | v1.1.46 | ⭐⭐⭐⭐⭐ | 产品需求规格 (PRD) |
| **[architecture/README.md](./architecture/README.md)** | v1.0 | ⭐⭐⭐⭐ | 架构文档目录索引 |

### 📖 专项技术指南

| 文档 | 用途 | 维护频率 |
|------|------|----------|
| **[architecture/steerable-agent-loop.md](./architecture/steerable-agent-loop.md)** | 可控代理循环设计 (Mode/Budget/Approval) | 稳定 |
| **[architecture/rag-system.md](./architecture/rag-system.md)** | RAG 检索与记忆管理系统架构 | 稳定 |
| **[native-bridge-defensive-guide.md](./native-bridge-defensive-guide.md)** | 原生桥接防御机制 (Deadlock Prevention) | 稳定 |
| **[android-build-guide.md](./android-build-guide.md)** | Android 构建与签名指南 | 随构建流程变化 |
| **[release-protocol.md](./release-protocol.md)** | 发布流程与版本管理协议 | 随流程优化更新 |
| **[settings-panels-reference.md](./settings-panels-reference.md)** | 四大设置面板架构参考 (v2.0) | 随 UI 变化更新 |
| **[architecture/CODE_STRUCTURE.md](./architecture/CODE_STRUCTURE.md)** | 项目目录结构与组件库全景 (v5.0) | 随目录调整更新 |

### 📂 历史与重构档案 (Archive)

| 文档 | 说明 | 状态 |
|------|------|------|
| **[archive/chat-store-refactor-overview.md](./archive/chat-store-refactor-overview.md)** | chat-store 重构方案总览 | 已结项 |
| **[archive/chat-store-refactor-phase2.md](./archive/chat-store-refactor-phase2.md)** | 模块化集成进度记录 | 已归档 |
| **[archive/local-model-feature-plan.md](./archive/local-model-feature-plan.md)** | 本地推理功能实施方案 | 已落地 |
| **[archive/optimization-plan-task-brain.md](./archive/optimization-plan-task-brain.md)** | Agent 大脑能力增强方案 | 已转化 |

---

## 📌 新会话快速索引

**AI Agent 必读顺序**：
1. `../PROJECT_RULES.md`（核心开发准则）
2. `llm-abstraction-layer-guide.md`（LLM 协议适配架构）
3. `architecture/rag-system.md`（RAG 逻辑流转）
4. `architecture/CODE_STRUCTURE.md`（目录核心映射）
5. `settings-panels-reference.md`（UI 结构映射）

---

## 📊 文档维护记录

### 2026-01-25 (备份系统与 UI 组件化)
- ✅ **版本发布**: 升级至 v1.1.57，发布正式版发行包。
- ✅ **模块化备份**: 实现 `BackupManager` 颗粒度控制，支持 5 类数据独立备份。
- ✅ **UI 原子化**: 新增 `CollapsibleSection` 弹簧动画折叠组件，统一设置页视觉。
- ✅ **能力扩展**: 本地 JS Code Interpreter 引擎落地。

### 2026-01-21 (重大审计与整理)
- ✅ **PRD 升级**: 版本同步至 v1.1.46，新增 v4.13 执行面板优化日志。
- ✅ **架构目录重组**: 创建 `architecture/` 子目录，迁移核心组件设计文档（含 `CODE_STRUCTURE.md`）。
- ✅ **清理归档**: 迁移 10 份已过期/已落地/历史审计方案至 `archive/`。
- ✅ **索引刷新**: 更新 `README.md` 与 `settings-panels-reference.md`。

### 2026-01-16
- ✅ **RAG 优化记录**: 记录推理链截断与主线程让步优化。
- ✅ **原生能力整合**: 记录 Gemini 原生搜索补全与工具过滤逻辑。

### 2026-01-15
- ✅ **品牌名统一**: NeuralFlow -> Nexara。
- ✅ **模块化重构**: 记录 chat-store 解耦 Phase 1 成果。

---

## 💡 维护建议

1. **SSOT 原则**：确保单一事实来源，避免在多个文档中维护相同的路由路径。
2. **相对路径链接**：文档间引用必须使用标准 Markdown 相对链接。
3. **版本对齐**：PRD 中的版本号必须与 `package.json` 保持同步。

---

**维护者**: Architecture Team + AI Assistant
