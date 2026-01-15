# Nexara TODO

> **最后更新**: 2026-01-16

---

## 高优先级 (High Priority)

### Steerable Agent Loop 完善
- [ ] **干预输入交互 (Intervention)**: 在 Agent 运行时，输入框切换为"插入干预"模式，允许动态注入指令
- [ ] **审批卡片 (Approval Card)**: 完善 Timeline 中的 `waiting_for_approval` 状态展示与 [批准/拒绝] 交互
- [ ] **状态可视化**: 在 Timeline 顶部显示当前 Loop 进度 (e.g., Step 3/10)

### RAG 系统
- [ ] 优化 `QueryVectorDbSkill` 的 Top-K 检索策略，减少无关分块对上下文的污染
- [ ] PDF 文件导入支持

### 工具链
- [ ] 自动化构建补丁脚本 (Fix `build.gradle` after Expo Prebuild)

---

## 中优先级 (Medium Priority)

### 协议兼容
- [ ] 适配 `DeepSeek-V3` 官方工具调用特有协议（如果与标准 OpenAI 不同）

### UI/UX
- [ ] 二级设置页视觉统一 (Provider 弹窗、Agent 详情)
- [ ] 提供"自定义 Agent 颜色"功能

---

## 低优先级 (Low Priority)

### 基础设施
- [ ] 性能监控工具集成
- [ ] 自动化测试覆盖

### 文档
- [ ] API 设计文档
- [ ] 组件库使用指南
- [ ] 部署流程文档

---

## Recently Completed (2026-01-16)

- [x] **执行模式设置**: 集成至 `ChatInput`，与模型栏视觉对齐。
- [x] **视觉精简**: 移除会话设置及通用设置中冗余的执行模式配置项。
- [x] **RAG 配置修复**: 修复预设按钮背景色样式，解决文档检索数量的状态绑定冲突。
- [x] **Compact UI 规范**: 建立配置面板紧凑型布局标准，统一 SectionHeader/Card/Slider 样式。
