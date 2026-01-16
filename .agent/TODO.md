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

### Session 1 (Morning)
- [x] **执行模式设置**: 集成至 `ChatInput`，与模型栏视觉对齐。
- [x] **视觉精简**: 移除会话设置及通用设置中冗余的执行模式配置项。
- [x] **RAG 配置修复**: 修复预设按钮背景色样式，解决文档检索数量的状态绑定冲突。
- [x] **Compact UI 规范**: 建立配置面板紧凑型布局标准，统一 SectionHeader/Card/Slider 样式。

### Session 2 (Afternoon - Performance & LLM Optimization)
- [x] **RAG 性能优化**:
  - [x] 解决 DeepSeek R1 长思维链渲染阻塞问题（`ToolExecutionTimeline` 推理文本截断至 1000 字符）
  - [x] `GraphExtractor` 和 `VectorStore` 添加线程让步机制（防止 UI 冻结）
  - [x] `ProcessingIndicator` 限制同时渲染切片数量（最后 5 个，避免布局抖动）
- [x] **RAG 指示器持久化**: 修复 RAG 状态指示器在检索结果为 0 时消失的 Bug（添加 `processingHistory` 检查）
- [x] **Gemini/VertexAI 原生搜索冲突修复**: 
  - [x] 自动过滤自定义 `search_internet` 工具当原生 `webSearch` 启用时
  - [x] 系统提示词智能切换，引导模型优先使用原生能力
  - [x] VertexAI Token 缓存机制审计确认（5 分钟过期缓冲）
- [x] **执行模式默认值修复**: 
  - [x] `ExecutionModeSelector` 回退逻辑从 `'auto'` 修正为 `'semi'`
  - [x] `app/chat/agent/[agentId].tsx` 新建会话默认值从 `'auto'` 修正为 `'semi'`
- [x] **发行包编译自动化**:
  - [x] Worktree 同步、物理清理、图标替换（`assets/icon.png`）
  - [x] 成功构建 v1.1.34 (build 34) 签名 APK
