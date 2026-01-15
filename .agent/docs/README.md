# 📚 Nexara 文档中心

> **最后更新**: 2026-01-14  
> **版本**: v2.0

## 文档分类

### 🔥 核心架构文档

| 文档 | 版本 | 重要性 | 说明 |
|------|------|--------|------|
| **[llm-abstraction-layer-guide.md](./llm-abstraction-layer-guide.md)** | v1.0 | ⭐⭐⭐⭐⭐ | LLM抽象层完整指引（新会话必读） |
| **[product-requirements.md](./product-requirements.md)** | - | ⭐⭐⭐⭐ | 产品需求规格 |

### 📖 专项技术指南

| 文档 | 用途 | 维护频率 |
|------|------|----------|
| **[chat-store-refactor-phase2.md](./chat-store-refactor-phase2.md)** | chat-store模块化重构Phase 2指南 ⭐ | 按需更新 |
| **[chat-store-refactor-overview.md](./chat-store-refactor-overview.md)** | chat-store重构方案总览 | 稳定 |
| **[native-bridge-defensive-guide.md](./native-bridge-defensive-guide.md)** | 原生桥接防御机制 | 稳定 |
| **[steerable-agent-loop-design.md](./steerable-agent-loop-design.md)** | 可控代理循环设计 | 稳定 |
| **[android-build-guide.md](./android-build-guide.md)** | Android构建指南 | 随构建流程变化 |
| **[release-protocol.md](./release-protocol.md)** | 发布流程协议 | 随流程优化更新 |
| **[settings-panels-reference.md](./settings-panels-reference.md)** | 设置面板参考 | 随UI变化更新 |

---

## 📌 新会话快速索引

**AI Agent必读顺序**：
1. `../PROJECT_RULES.md`（第14章：LLM架构规范）
2. `../memory/CODE_STRUCTURE.md`（第4章：LLM抽象层）
3. `llm-abstraction-layer-guide.md`（完整架构）

---

## 🎯 按场景查找

### 开发LLM功能
→ **[llm-abstraction-layer-guide.md](./llm-abstraction-layer-guide.md)**
- 添加新Provider → 第三章
- 调试现有Provider → 第四章
- 架构原则 → 第二章

### 原生模块开发
→ **[native-bridge-defensive-guide.md](./native-bridge-defensive-guide.md)**
- Haptics调用规范
- 死锁防御机制
- 检查清单

### Android构建
→ **[android-build-guide.md](./android-build-guide.md)**
- Release构建流程
- 签名配置
- 常见问题

### 代理循环优化
→ **[steerable-agent-loop-design.md](./steerable-agent-loop-design.md)**
- 执行模式设计
- 审批卡片
- 干预机制

---

## 📊 文档维护记录

### 2026-01-15
- ✅ 品牌名统一更新（NeuralFlow → Nexara）
- ✅ 新增 chat-store 重构文档系列
  - `chat-store-refactor-phase2.md` (Phase 2实施指南)
  - `chat-store-refactor-overview.md` (重构方案总览)
  - `chat-store-refactor-phase1-report.md` (Phase 1完成报告)
- ✅ 更新文档索引

### 2026-01-14（重大更新）
- ✅ 新增 `llm-abstraction-layer-guide.md`（v1.0）
- ✅ 标记为核心架构文档
- ✅ 更新文档索引

### 2026-01-08
- ✅ 整合文档目录
- ✅ 迁移 PRD.md → product-requirements.md
- ✅ 清理过时archive文档

---

## 💡 维护建议

1. **定期审查**：每季度审查一次，归档过时内容
2. **命名规范**：使用 kebab-case，明确主题
3. **版本标识**：重要设计文档标注版本号和日期
4. **交叉引用**：文档间使用相对路径链接

---

**维护者**: Architecture Team + AI Assistant
