# Nexara TODO

> **最后更新**: 2026-01-25

---

## 🔴 高优先级 (High Priority)

### 1. 全局动画与交互升级 (Native-like Transitions)
> **来源**: `.agent/docs/animation_implementation_plan.md`
- [x] **Tab 切换动画重构**: 采用 Native Fade (淡入淡出) 策略，弃用高开销的自定义左右滑动方案。
- [x] **Stack 导航手势优化**: 启用 iOS 全屏边缘手势 (`fullScreenGestureEnabled`)。
- [x] **原生桥接延迟规范**: 严格执行 `10ms` 延迟触发 Haptics 规则。

### 2. 工具链稳定性
- [ ] **自动化构建补丁脚本**: 解决 Expo Prebuild 后需手动 Patch `build.gradle` 的问题。
- [ ] **应用冷启动优化**: 分析首屏渲染耗时，优化 Zustand Hydration 流程。

---

## 🟡 中优先级 (Medium Priority)

### 3. MCP 集成探索 (Model Context Protocol) 🚧 实验性
> **来源**: `.agent/docs/archive/mcp_feasibility_report.md`
- [ ] **Phase 1: SDK 验证**: 验证 `@modelcontextprotocol/sdk` 在 RN 环境的兼容性。
- [ ] **Phase 2: 基础连接**: 实现 SSE 客户端，连接到简单的测试服务器（Localhost）。
- [ ] **Phase 3: 简单工具调用**: 让 Agent 能够读取服务器上的一个文本文件。

### 4. RAG 系统持续优化
- [ ] **多模态 RAG**: 研究图片 RAG 方案（目前仅支持文本/PDF）。
- [ ] **增量索引机制**: 避免每次文件变动都重新全量向量化。

---

## 🟢 低优先级 (Low Priority)

### 5. 基础设施与质量
- [ ] **性能监控 (APM)**: 集成 Firebase Performance 或 Sentry Tracing 以量化掉帧。
- [ ] **单元测试**: 对核心 Reducer (Chat/Settings) 补充 Jest 测试用例。
- [ ] **文档完善**: 更新 API 接入文档以便社区贡献新的 Provider。

---

## ✅ 已完成功能归档 (Completed Features)

### 2026-01-25: 核心能力里程碑
- [x] **本地模型 (Local Model) 完整落地**:
  - 集成 `llama.rn` (v0.10+)，支持 GGUF 模型加载与推理。
  - 实现 `LocalModelServer`，支持主模型、Embedding 模型、Rerank 模型热插拔。
  - 实现混合状态管理 (Zustand + AsyncStorage) 与自动恢复机制。
- [x] **RAG 系统增强**:
  - 实现 PDF 解析 (`PdfExtractor` + `pdf.js`)。
  - 实现 Top-K 检索优化 (集成 BGE/Jina Reranker 本地推理)。
  - 网络层健壮性：强制 MIME 校验，前置拦截 HTML 错误页。
- [x] **Markdown 渲染归一化**:
  - 优化 `ChatBubble`，支持任务自动化结果 (Thinking/Task) 的流式渲染与样式统一。
  - 修复 `TaskFinalResult` 独立组件导致的样式割裂与功能缺失。

### 2026-01-18: UI/UX 与 性能优化
- [x] **设置页重构**: Crystal UI 规范落地，Provider/Agent 设置页视觉统一。
- [x] **性能优化**: 解决 JSON 粘贴卡顿、列表渲染 Memoization、长思维链截断优化。
- [x] **动画细节**: Tab 指示器平滑过渡，页面转场阴影修复。
