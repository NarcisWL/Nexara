# .agent 目录说明

欢迎来到 NeuralFlow 项目的 `.agent` 目录！

这个目录包含项目的规则、记忆、文档和检查清单，是 AI Assistant 和团队成员的重要参考资料。

---

## 📁 目录结构

```
.agent/
├── README.md                   # 本文档
├── PROJECT_RULES.md            # 项目核心规则（必读）
├── docs/                       # 详细文档
│   └── native-bridge-defensive-guide.md  # 原生桥接防御指南
├── memory/                     # 项目记忆
│   └── PROJECT_MEMORY.md       # 开发历史与经验教训
└── checklists/                 # 检查清单
    └── CODE_REVIEW.md          # 代码审查清单
```

---

## 📖 文档索引

### 🎯 新成员必读
1. [PROJECT_RULES.md](./PROJECT_RULES.md) - 项目核心规则
2. [docs/native-bridge-defensive-guide.md](./docs/native-bridge-defensive-guide.md) - 关键防御准则

### 📚 深入了解
- [memory/PROJECT_MEMORY.md](./memory/PROJECT_MEMORY.md) - 项目演进历史和重大决策

### ✅ 实用工具
- [checklists/CODE_REVIEW.md](./checklists/CODE_REVIEW.md) - PR 审查时使用

---

## 🚀 快速开始

### AI Assistant 使用
AI Assistant 会自动扫描 `.agent/` 目录，在每次会话开始时加载这些规则和记忆。

### 团队成员使用
1. **加入项目时**：阅读 `PROJECT_RULES.md`
2. **开发功能时**：参考具体文档（如 `native-bridge-defensive-guide.md`）
3. **提交 PR 时**：使用 `CODE_REVIEW.md` 自查
4. **遇到问题时**：查看 `PROJECT_MEMORY.md` 中的已知问题和解决方案

---

## 📝 维护指南

### 何时更新文档

#### PROJECT_RULES.md
- 架构重大变更
- 新增核心技术栈
- 发现新的最佳实践

#### PROJECT_MEMORY.md
- 修复重大 Bug
- 完成重要功能
- 发现重要经验教训
- 技术栈升级

#### docs/
- 新增防御准则
- 发现新的陷阱模式
- 更新排查流程

#### checklists/
- 检查清单遗漏项
- 发现新的检查点
- 优化审查流程

### 如何更新
1. 直接编辑对应的 Markdown 文件
2. 提交时在 commit message 中说明变更原因
3. 重大变更建议经过 PR Review

---

## 🛠️ 工具推荐

### 文档查看
- VSCode Markdown Preview
- Typora
- 任何 Markdown 编辑器

### 检查清单使用
- 复制 `CODE_REVIEW.md` 到 PR 描述
- 使用 GitHub/GitLab 的任务列表功能

---

## 💡 最佳实践

1. **定期复习**: 每月至少复习一次核心规则
2. **主动更新**: 发现新知识立即记录
3. **分享经验**: 在 PROJECT_MEMORY.md 中记录教训
4. **持续改进**: 规则不是一成不变的，持续优化

---

## 🤝 贡献指南

欢迎所有团队成员贡献内容！

### 贡献流程
1. 发现值得记录的知识/经验
2. 编辑对应文档
3. 提交 PR 并说明变更理由
4. 经过 Review 后合并

### 贡献原则
- ✅ 简洁明了，避免冗余
- ✅ 实用为主，理论为辅
- ✅ 包含示例，易于理解
- ✅ 及时更新，保持时效

---

## 📞 反馈与建议

如果您对 `.agent` 目录有任何建议或发现问题，请：
- 提交 Issue
- 发起 Discussion
- 直接联系项目负责人

---

**维护者**: AI Assistant + Project Team  
**创建日期**: 2025-12-26  
**最后更新**: 2025-12-26
