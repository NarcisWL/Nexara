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
