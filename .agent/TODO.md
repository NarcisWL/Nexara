# Nexara TODO

> **最后更新**: 2026-01-18

---

## 高优先级 (High Priority)

### Steerable Agent Loop 完善
- [x] **状态可视化**: TaskMonitor 显示 "Step 3/10" 风格进度（已实现）

### RAG 系统
- [ ] 优化 `QueryVectorDbSkill` 的 Top-K 检索策略，减少无关分块对上下文的污染
- [ ] PDF 文件导入支持

### 本地模型功能 ⏳ 待实施
- [ ] **本地模型推理模块** (方案已归档: `.agent/docs/local-model-feature-plan.md`)
  - [ ] Phase 1: llama.rn 服务器集成 (~3h)
  - [ ] Phase 2: 模型存储管理 (~2h)
  - [ ] Phase 3: Local Provider 集成 (~2h)
  - [ ] Phase 4: Embedding 本地化 (~3h)

### 工具链
- [ ] 自动化构建补丁脚本 (Fix `build.gradle` after Expo Prebuild)

---

## 中优先级 (Medium Priority)

### 协议兼容
- [x] 适配 `DeepSeek-V3` 官方工具调用特有协议（已通过 Virtual Split Architecture 完成）

### UI/UX
- [x] 二级设置页视觉统一 (ProviderModal 、Agent 详情) - 已应用 Crystal UI 规范
- [x] 提供"自定义 Agent 颜色"功能（ColorPickerPanel 已实现）

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
- [x] **Steerable Agent 完善**:
  - [x] 干预输入交互：ApprovalCard 支持可选的修改指令输入
  - [x] DeepSeek-V3 协议适配：Virtual Split Architecture 完整支持
  - [x] 状态可视化：TaskMonitor 显示 "Step 3/10" 风格进度
- [x] **UI/UX 统一化**:
  - [x] 二级设置页 (ProviderModal/Agent 详情) 已应用 Crystal UI 规范
  - [x] 自定义 Agent 颜色: ColorPickerPanel 实现

## Recently Completed (2026-01-18)

### Session 1 (Settings Panel Performance & UX)
- [x] **设置界面性能优化 (Provider Management)**:
  - [x] 修复 JSON 粘贴阻塞主线程问题 (新建 `ParsedInput.tsx` 组件)
  - [x] 服务商列表 Memoization 优化 (`ProviderList.tsx` 抽离)
  - [x] `ProviderModal.tsx` 重构，移除同步 JSON Parse
- [x] **Model Management 优化**:
  - [x] `Switch.tsx` 组件 `React.memo` 包装，阻断无效渲染
  - [x] `ModelSettingsModal.tsx` 移除高开销 `key={model.uuid}`
  - [x] 整合冗余 `useEffect` 与 `renderItem` 稳定化
- [x] **标签页平滑过渡动画**:
  - [x] 使用 Reanimated SharedValue 实现 Cross-fade 内容过渡
  - [x] Tab 指示器平移动画 (`Easing.bezier` 非弹跳曲线)
  - [x] 暗黑模式指示器颜色适配修复
- [x] **服务商列表布局密度提升**:
  - [x] 卡片高度压缩约 25%
  - [x] 列表间距收紧 (16 -> 10)
  - [x] 模型管理按钮改为轻量边框样式
- [x] **发行包编译**: 成功编译 v1.1.36 (Build 44)

### Session 2 (NewAPI Support & RAG Logic Fixes)
- [x] **NewAPI / 聚合服务商适配**:
  - [x] 新增 `openai-compatible` 类型与快捷预设
  - [x] URL 启发式补全（动态自动添加 `/v1` 路径）
  - [x] 统一 `ModelSettingsModal` 与 `ModelService` 的抓取逻辑
  - [x] HTML 错误页前置拦截 (Rule 8.4)
- [x] **RAG 设置修复**:
  - [x] 修复 `GlobalRagConfigPanel` 导航错误跳转至统计页的问题
  - [x] 强化清理逻辑：向量清空时同步物理删除知识图谱 `kg_nodes` / `kg_edges`
- [x] **UI 细节打磨**:
  - [x] 修复设置标签页指示器上下间距不一致几何缺陷
  - [x] 解决 Android 端 Transition 阴影残存闪烁 (基于 `borderBottom` 的物理厚度替代方案)
  - [x] 响应式调整滑块厚度从 2.5 -> 1.5 (极致细腻)

### Session 3 (LLM Robustness & Rule 8.4)
- [x] **网络层 MIME 类型强制校验 (Rule 8.4)**:
  - [x] 在 `OpenAiClient`, `VertexAiClient`, `Gemini` 路径中全面落实
  - [x] 拦截 HTML 错误页并转换为可读错误，防止 JSON Parse 崩溃
- [x] **URL 启发式纠偏**:
  - [x] 在 Embedding 请求阶段自动识别聚合器并补全 `/v1` 路径
- [x] **代码重构**:
  - [x] 消除 `EmbeddingClient` 与 `OpenAiClient` 的冗余 Fetch 逻辑 (DRY)

### Session 4 (Timeline UI Polish \u0026 Model Behavior Analysis, 2026-01-21)
- [x] **Timeline 交互优化**:
  - [x] 实现持久化模糊页眉 (折叠显示汇总, 展开显示标题)
  - [x] 统一 Chevron 方向逻辑 (展开=Up, 收起=Down)
  - [x] 修复 Markdown 行内代码暗色模式渲染问题
- [x] **图标精准对齐**:
  - [x] TaskMonitor/Timeline/FinalResult 所有图标与头像中心竖向对齐
  - [x] 像素级内边距调整 (29px/25px/22px)
- [x] **国际化完善**:
  - [x] 新增 "执行详情"/"最终结果"/"网页解析" 的中英文翻译键
  - [x] 组件动态切换国际化标题
- [x] **模型行为分析**:
  - [x] 确认工具禁用时思考流保留机制
  - [x] 验证 Gemini 原生搜索的指令抑制逻辑
  - [x] 梳理 GLM/DeepSeek/Gemini 的思考模式架构差异
- [x] **文档同步**:
  - [x] 更新 PROJECT_MEMORY.md (v4.13 条目)
  - [x] 生成详细 Walkthrough

---

## 待办事项状态更新 (2026-01-21)
- ℹ️ 架构文档审核待进行 (UI 组件层级变更)
- ℹ️ 下一步优先级: 考虑补充 UI 组件交互规范文档

