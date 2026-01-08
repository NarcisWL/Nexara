# NeuralFlow 项目记忆

> **用途**: 记录项目开发过程中的关键决策、重大事件和经验教训  
> **更新频率**: 每次重大变更后更新

---

## 技术栈演进

### 核心技术选型
- **框架**: React Native (Expo SDK 52)
- **路由**: expo-router (文件路由)
- **状态管理**: Zustand + Persist
- **样式**: NativeWind (Tailwind CSS)
- **国际化**: 自定义 i18n hook
- **主题**: Context + Provider

### 关键决策理由
- **选择 Expo**: 快速开发、丰富生态、OTA 更新
- **选择 expo-router**: 类 Next.js 体验、类型安全
- **选择 Zustand**: 轻量、简洁、无样板代码
- **选择 NativeWind**: 熟悉的 Tailwind 语法、高性能

---

## 架构演进历史

### v1.0 - 初始架构
- 文件路由 + Tab 导航
- 基础 i18n 支持
- 简单主题切换

### v1.0.5 - Library 交互精度优化 (2025-12-25)
**新增功能**:
- 液态布局过渡 (LinearTransition)：为 Library 页面引入全屏协调平移动画
- 3D 悬浮操作栏：重新设计多选模式操作栏（elevation: 15 + 深度投影）

**修复**:
- `rag.tsx` JSX 闭合对齐错误（红屏崩溃）
- Android `elevation` 与半透明背景冲突渲染 Bug
- 触感反馈增强（Success 和 Medium）
- 补回误删的 "Documents" 分组标题

**优化**:
- 全局 Header 规范对齐（32px、Black 字重）
- 取消选中标记退场动画，提速交互

### v1.1 - 导航优化 (2025-12-26)
**问题**: 设置页崩溃  
**原因**: 
- PageLayout 嵌套 View 导致导航上下文错误
- 语言切换器同步调用触发死锁

**修复**:
- 移除 PageLayout 嵌套
- 所有原生桥接调用延迟执行

**影响**:
- ✅ 彻底解决导航崩溃
- ✅ 建立原生桥接防御准则
- ✅ 提升触感反馈一致性

### v3.5 - 性能与发布体系闭环 (2025-12-29)
**主要里程碑**:
- **Release Ready**: 完成 Keystore 生成与 Gradle 签名自动化修补，成功产出 Nexara 正式版 APK。
- **UI 深度抛光**: 完成 `ModelSettingsModal` 暗黑模式深度覆盖；全局面板动画去除震荡回弹。
- **Markdown 增强**: 成功集成 LaTeX 公式渲染支持。
- **防死锁与防崩溃**: 建立 Rule 8.4 网络层安全门禁；彻底移除原生 Alert 依赖。

**优化**:
- 应用振动选项改为**默认关闭**，增强用户初始宁静感。

### v3.6 - 视觉一致性重构 (2026-01-01)
**目标**: 消除硬编码颜色，统一使用 Zinc Theme 系统。
**变更**:
- **组件抽象**: 封装 `SettingsSection`, `SettingsItem`, `LargeTitleHeader`，统一设置页和标题栏视觉。
- **颜色归一**: 创建 `src/theme/colors.ts` 作为唯一颜色真理源。
- **核心链路清理**: 完成了 `(tabs)/settings`, `(tabs)/chat`, `BackupSettings`, `GlobalRagConfigPanel`, `SwipeableAgentItem` 的重构。

### v3.7 - Token 计费体系与可视化 (2026-01-02)
**目标**: 建立透明、可回溯的 LLM 消耗追踪体系，解决 API 计费不明的问题。
**核心架构**:
- **混合计费 (Hybrid Billing)**: 优先使用 API 返回的真实 Usage，缺失时优雅降级为本地估算 (IsEstimated 标记)。
- **全局账本**: 引入 `TokenStatsStore`，跨会话累积 Input/Output/System (RAG) 消耗。
- **全链路追踪**:
  - **Chat**: 捕获 LLM API Usage。
  - **RAG**: 捕获 Query Rewrite 和 Embedding (Document & Query) 的 Token 消耗。

**可视化**:
- **会话级**: `TokenStatsModal` 支持显示估算标记 (≈) 和会话重置。
- **全局级**: 新增 `Settings -> Token Usage` 面板，支持多模型分布统计与全局重置。

### v3.8 - RAG 性能优化与交互升级 (2026-01-02)
**目标**: 解决 RAG 系统性能瓶颈，并提供丝滑的聊天滚动体验。
**核心优化**:
- **Query Rewrite 提速 (86%)**: 通过 15s 超时熔断 + 共享 Summary 快速模型，将优化时间从 ~138s 降至 ~15s。
- **Vector 数据治理**: 彻底修复 Vector 无法随 Document 删除的 Bug，并新增全局一键清空功能。
- **交互引擎重构**:
  - **自动滚动 (Auto-scroll)**: 完整实现 5 条交互法则，支持流式输出追踪、用户操作打断检测、Reasoning 过程折叠。
  - **性能调优**: 优化 FlashList 渲染窗口 (drawDistance=2000)，解决快速滚动回弹问题。
- **指示器优化**: 移除 "Searching web..." 文本提示，改用 RAG 状态指示器，界面更纯净。

---


### v3.9 - Knowledge Graph 2.0 & Release Fixes (2026-01-05)
**核心功能 (Knowledge Graph 2.0)**:
- **三维图谱视图**: 实现了全局 (Global) 和会话级 (Session) 的知识图谱可视化。
- **文件夹图谱**: 新增递归解析逻辑，支持查看文件夹及其子目录的聚合图谱。
- **交互增强**: 
  - 节点/边点击支持。
  - 自动后台提取实体。
  - 设置面板开关控制。

**Release 构建闭环**:
- **构建自动化**: 修复了版本号自动叠加 (Bump) 失效的问题。
- **签名注入**: 实现了 `secure_env` 自动注入 keystore 和 `gradle.properties` 的流程。
- **Crash 修复**: 
  - 修复 `KnowledgeGraphView` Android WebView 闪退 (Hook 顺序 + 资源清理)。
  - 修复 Release 包 R8 混淆导致的闪退 (`minifyEnabled false`)。
  - 清理 Native Module 链接错误 (Prebuild --clean)。

### v3.9.1 - Stability & UX Refinements (2026-01-07)
**修复与优化**:
- **App 交互**: 修复了消息发送后瞬间显示绿勾的问题（逻辑竞态），并解决了转圈动画卡死（`React.memo` 比较失效）。
- **Graphing 鲁棒性**: 增强 `GraphExtractor` JSON 解析能力，防止非 JSON 输出导致红屏崩溃。
- **Crash 修复**: 补全 `MemoryManager.upsertMemory` 方法，解决手动向量化崩溃。
- **WebUI 同步**: 修复了 WebUI 模型列表、搜索开关状态同步及 WebSocket 断连问题。
- **视觉微调**: 统一了 Graphing 指示器与 Token/Model 指示器的尺寸与风格。

### v3.9.7 - Library UI 抛光与交互闭环 (2026-01-08)
**目标**: 修复文库界面布局高度偏差，并优化 ContextMenu 响应逻辑。
**修复与优化**:
- **ContextMenu 升级**: 新增 `triggerOnPress` 属性，实现点击“三点”图标立即弹出菜单（摒弃长按带来的操作负担）。
- **布局矫正**: 重构 `FolderItem` 和 `CompactDocItem` 容器，解决了在某些设备上出现的垂直高度拉伸 Bug。
- **发布版本 v1.1.23**: 正式启用“双环境”持久化签名流程。

### v3.9.6 - Git Worktree & 路径限界突破 (2026-01-08)
**目标**: 解决 Windows 路径过长 (MAX_PATH) 导致的编译中断，并隔离开发与发行环境。
**核心架构 (Dual Pipeline)**:
- **隔离方案**: 建立极简路径 Worktree `D:\NF\R`。
  - **Dev 主环境 (`D:\NF`)**: 还原为干净的 `debug.keystore`，无打包密码，极速启动。
  - **Release 工厂 (`D:\NF\R`)**: 硬编码正式版签名，物理隔离构建缓存。
- **路径极限压缩**:
  - 系统侧：开启 Windows 11 `LongPathsEnabled` 注册表与组策略。
  - 目录侧：使用单字母 `R` 目录，为 Ninja 编译器争取 ~30 字符深度。
  - Git 侧：配置 `core.longpaths true`。
- **成果**: 成功在隔离环境下由于重叠路径压缩，顺利完成 `react-native-keyboard-controller` 等深层 NDK 模块的编译，产出 `v1.1.22` 精品 APK。

### v3.9.5 - Mobile Workbench & HDR Visuals (2026-01-08)
**核心功能**:
- **Mobile Workbench 2.0**:
  - **自动登录**: Web 端输入 6 位 PIN 码后自动连接。
  - **连接保活**: 引入心跳机制，解决僵尸连接问题。
  - **后台运行**: 集成 `expo-keep-awake`。

**视觉突破 (HDR/EDR)**:
- **OLED 激发**: 启用 `wideColorGamut` 色域支持。
- **Skia 升级**: 强制开启 `Display P3` 模式。

**发布基建 (v1.1.22)**:
- **Signed Build**: 成功执行基于 `secure_env` 的正式版签名。
- **重大发现**: 识别并修复了 Expo 模板中隐藏的 `signingConfig signingConfigs.debug` 覆写逻辑（Last Rule Wins）。
- **特权访问**: 启用了 `Agent Gitignore Access` 设置，实现了 Android 目录编辑与 Git 隔离的解耦。

---

## 技术债务 / 待改进项 (Technical Debt)

### 2026-01-01: 视觉一致性残留
虽然核心路径已完成重构，但仍有以下区域存在硬编码颜色或设计限制，**暂缓处理，留待后续迭代**：

1.  **RAG 模块 (`app/rag`, `src/components/rag`)**: 文档管理界面仍含有大量硬编码颜色 (Tailwind Indigo 类名)，需后续排期统一。
2.  **二级设置页**: Proivder 设置弹窗、Agent 详情设置页 (`app/chat/[id]/settings.tsx`) 尚未重构。
3.  **Chat 界面颜色绑定**:
    *   **现象**: `app/chat/[id].tsx` 和 `ChatInput.tsx` 中，关键交互元素（发送按钮、新会话按钮、Input 模型图标、Markdown 边框）均绑定了 `agent.color` (Agent 预设颜色)。
    *   **限制**: 目前 UI 上没有更改 Agent 预设颜色的入口，导致如果 Agent 颜色与主题不符或对比度不够，用户无法调整。且这些元素的颜色没有统一使用主题色 (`Colors.primary`)。
    *   **建议**: 后续应提供“自定义 Agent 颜色”功能，或强制让这些交互元素回退到全局主题色。

---

## 重大Bug记录

### 2025-12-26: 设置页导航上下文崩溃

#### 症状
- 切换 Tab 时红屏："Couldn't find a navigation context"
- 语言切换器点击时震动异常（延迟但劲更大）

#### 诊断过程
1. 采用防御性重建策略
2. 逐步添加功能定位问题
3. 发现 PageLayout 嵌套 View 问题
4. 发现语言切换器同步调用问题

#### 根本原因
1. **PageLayout 嵌套**: 额外的 `<View>` 在状态重渲染时干扰导航上下文
2. **语言切换死锁**: `setLanguage` → `key={language}` 变化 → 导航器重挂载 + Haptics 同步调用 → 线程竞争

#### 解决方案
```tsx
// 修复前
const content = (
    <View style={{ flex: 1 }}>
        {children}
    </View>
);

// 修复后
// 直接渲染 children
```

```tsx
// 修复前
onPress={() => setLanguage('zh')}

// 修复后
onPress={() => {
    setTimeout(() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
        setLanguage('zh');
    }, 10);
}}
```

#### 经验教训
- ✅ 用户触觉异常是性能问题的信号
- ✅ 状态变更可能触发隐式重挂载
- ✅ 默认延迟优于条件判断
- ✅ 防御性构建能快速定位问题

#### 影响范围
- 修复文件: `src/components/ui/PageLayout.tsx`
- 修复文件: `app/(tabs)/settings.tsx`
- 新增准则: 原生桥接死锁防御

---

## 性能优化历史

### 触感反馈优化 (2025-12-26)
**问题**: 部分交互震动不一致  
**方案**: 延迟所有 Haptics 调用 10ms  
**效果**: 触感反馈完全一致，无异常

### 对话加载性能 (2025-12-28)
- **方案**: `Inverted List` + `InteractionManager` 延迟重渲染。
- **效果**: 1000+ 消息会话加载时间从 1-2s 感知降至 < 300ms。

---

### 2026-01-02: RAG 系统审计与协议扩展性设计

**审计背景**: 对 RAG 系统进行全面审计，发现 4 个关键缺陷和 3 个架构改进点。

**关键发现**:

1. **Google Embedding 缺失** (P0):
   - 现象: `embedding.ts` 中 Google 方法抛出异常，阻断 Gemini 用户使用向量化功能。
   - 根因: Google/Gemini API 协议与 OpenAI 完全不同（端点格式、认证方式、请求/响应结构）。
   - 影响: 功能阻断，Vendor Lock-in 风险。

2. **Token 计费泄漏** (P0):
   - 现象: RAG 检索成本（Query Rewrite + Embedding）未计入会话 Token 统计。
   - 根因: `chat-store.ts` 的 `generateMessage` 函数捕获了 `billingUsage` 但未累加到最终 Token 更新中。
   - 影响: 用户看到的成本低于实际消耗，财务不透明。

3. **硬编码 Provider 逻辑** (P1):
   - 现象: `vectorization-queue.ts` 和 `memory-manager.ts` 硬编码 OpenAI 优先级，忽略用户的 `defaultEmbeddingModel` 配置。
   - 影响: 用户无法选择 Embedding 模型，违背统一配置原则。

4. **协议扩展性缺失**:
   - 现状: 每新增一种 Embedding 协议需修改核心逻辑，维护成本高。
   - 市场现状: 除 OpenAI 兼容和 Google 外，还有 Cohere、Voyage AI、Hugging Face 等变体。

**架构决策**:

采用 **策略模式 (Strategy Pattern)** 重构 Embedding 协议层：

```
抽象基类 (EmbeddingProtocol)
├─ 定义 5 个钩子方法:
│  ├─ buildEndpoint()
│  ├─ buildHeaders()
│  ├─ buildRequest()
│  ├─ parseResponse()
│  └─ getBatchSize()
│
具体实现
├─ OpenAIProtocol (覆盖 SiliconFlow, DeepSeek, Moonshot, 智谱)
├─ GoogleProtocol (覆盖 Gemini, Google)
└─ [未来] CohereProtocol, VoyageAIProtocol...
```

**优势**:
- ✅ **开闭原则**: 新增协议仅需新增文件 + 修改路由表一行代码。
- ✅ **单一职责**: 每个协议独立文件，职责清晰。
- ✅ **易测试**: 可单独测试每个协议实现。
- ✅ **未来可配置化**: 预留接口，后续可改为 JSON 配置驱动（用户自定义协议）。

**实施路线** (见 `implementation_plan.md` v2):
- **Phase 1 (P0)**: 实现 Google Embedding + 修复 Token 泄漏 + 连接配置与业务逻辑 (2-3h)
- **Phase 2 (P1)**: UI 暴露 Embedding 模型选择器 + 增强错误提示 (2-3h)
- **Phase 3 (P2)**: 实现 Trigram 分词器（中文友好） (2-3h)

**参考文档**:
- `brain/5ab78dbb-2fd4-43b5-8d3e-1c6ee0d9bfde/RAG_SYSTEM_AUDIT.md` - 完整审计报告
- `brain/5ab78dbb-2fd4-43b5-8d3e-1c6ee0d9bfde/implementation_plan.md` - 修复方案（v2 修订版）
- `brain/5ab78dbb-2fd4-43b5-8d3e-1c6ee0d9bfde/PROTOCOL_EXTENSIBILITY_DESIGN.md` - 协议扩展性设计（含配置化方案）

### Chat UI & Logic Refinements (2026-01-06)
- **ContextMenu Refactor**: Switched message context menu to `ui/ContextMenu` with touch-event positioning. Added manual KG extraction, vectorization, and summarization triggers.
- **Typography Optimization**: Reduced AI message font size to 15px and line height to 26px for higher information density. Tightened indices for markdown elements.
- **Auto-Scroll Intelligence**: Implemented stricter "user-away" detection and manual resume threshold (20px).
- **Search Audit & Fixes**: Verified Google Search integration. Implemented client-side search fallback for non-native providers. Fixed citation visibility bug during streaming.
- **Regenerate Logic**: Distinct "Regenerate" (AI in-place) vs "Resend" (User clone).
- **KG Indicator**: Refined UI with breathing glow animation and compact text size.

---

## 工程准则更新记录 (Engineering Principles Updates)

### Rule 9: Embedding 协议扩展性准则 (2026-01-02)

**背景**: 市场上存在多种 Embedding API 协议变体，硬编码会导致维护噩梦。

**准则**:
1. **抽象优先**: 所有 Embedding 协议必须继承 `EmbeddingProtocol` 基类。
2. **协议隔离**: 每个协议实现独立文件（`src/lib/rag/protocols/{provider}.ts`）。
3. **路由表集中**: 协议选择逻辑集中在 `EmbeddingClient.selectProtocol()`，新增协议仅修改此处。
4. **统一批量策略**: 核心逻辑 (`embedBatch`, `embedLoop`, `embedChunked`) 不关心具体协议细节。

**禁止**:
- ❌ 在 `embedding.ts` 核心类中硬编码具体协议请求/响应逻辑。
- ❌ 在多处 switch-case 中重复协议判断。

**未来演进方向**:
- 🔮 配置化协议：用户通过 JSON 定义端点、认证、请求/响应映射，无需写代码。
- 🔮 协议市场：社区分享协议配置文件。

---

## 经验教训 (Lessons Learned)

### 2026-01-02: "为什么要单独实现 Google Embedding？"

**用户质疑**: Google 的 API 协议与其他服务商有何不同？为何不能统一？

**答案**: Google/Gemini 的 API 协议与 OpenAI **完全不同**：

| 差异维度 | OpenAI/兼容者 | Google/Gemini |
|:---|:---|:---|
| 端点格式 | `/v1/embeddings` | `/v1beta/models/{model}:embedContent` |
| 认证方式 | `Authorization: Bearer {key}` | Query Param `?key={apiKey}` |
| 请求体结构 | `{ model, input: [...] }` | `{ content: { parts: [{ text }] } }` |
| 响应结构 | `{ data: [{ embedding }] }` | `{ embedding: { values: [...] } }` |
| 批量支持 | ✅ 最多 2048 条 | ❌ 必须逐条调用 |

**深层启示**:
- 即使是同一功能（Embedding），不同厂商的 API 设计哲学可能完全不同。
- "统一抽象层" 的价值在于**隐藏差异**，而非**消除差异**。
- 策略模式是应对"行为多样但目标统一"场景的有效模式。

**引申问题**: 用户进一步提出——"如果未来出现第三种协议怎么办？"  
**架构应对**: 引入策略模式后，新增协议成本从 **修改 N 处** 降至 **新增 1 个文件 + 修改 1 行路由**。

### 2026-01-07: React.memo 与 Zustand/Immer 的陷阱
**问题**: 消息状态 (`vectorizationStatus`) 更新了，但 UI (`ChatBubble`) 没刷新，导致 Loading 转圈卡死。  
**根因**: 
- `ChatBubble` 使用了 `React.memo` 进行性能优化，且自定义了 `arePropsEqual` 函数。
- 状态更新通过 Zustand + Immer 修改了深层属性 (`message.vectorizationStatus`)，产生了新的 Message 对象引用。
- 但 `arePropsEqual` 显式列出了比较字段，**漏掉了** `vectorizationStatus`。导致即使 Message 对象变了，比较结果仍为 `true` (不重新渲染)。

**教训**: 
- 在使用自定义 `React.memo` 比较函数时，必须维护一份完整的"敏感属性清单"。
- 每当数据模型 (`Message`) 新增状态字段时，必须同步检查相关的 `React.memo` 逻辑。

### 2026-01-07: LLM 输出的不可预测性与 JSON 解析
**背景**: 本地 Graph Extraction 功能调用 LLM 提取 JSON。
**问题**: LLM 有时会"自作聪明"地返回 Markdown 说明 ("以下是 JSON...") 而非纯 JSON，导致 `JSON.parse` 抛出异常。在 Expo 开发模式下，未捕获的 Error 会导致**红屏 (RedBox)**，严重打断体验。

**解决方案**:
1. **正则外科手术**: 使用 `/```json([\s\S]*?)```/` 优先提取代码块，而非简单的 `startsWith` 检测。
2. **容错解析**: 正则失败时尝试寻找首尾 `{` `}`。
3. **降级日志**: 将 `console.error` 降级为 `console.warn`。在非关键业务路径（如后台提取）上，宁可静默失败，不可崩溃应用。

---

## 项目里程碑 (Milestones)

### 2026-01-02: RAG 系统架构优化启动
- ✅ 完成 RAG 系统全面审计
- ✅ 制定可扩展协议架构方案
- 🚧 Phase 1 实施中（预计 3 小时）

---

## 重大经验记录 (Experiences & Constraints)

### Windows 编译环境 (2025-12-26)
- **限制**: Windows 路径 260 字符限制经常导致 Android Gradle 生成产物失败。
- **对策**: 项目必须放置于根目录（如 `G:\Nx`）下，缩短物理路径。

### 网络解析安全 (Rule 8.4)
- **风险**: 502/404 等 HTML 报错页会搞崩所有 `JSON.parse` 的调用链。
- **准则**: 解析前强制校验 `Content-Type: application/json`，否则按文本阻塞处理。

---

## 待办事项 (Updated)

### 高优先级
- [ ] 解决 RAG 与二级设置页的视觉一致性残留 (Technical Debt)
- [ ] 自动化构建补丁脚本 (Fix `build.gradle` after Expo Prebuild)
- [ ] 实现针对大规模文档库的向量检索分页/缓存

### 中优先级
- [ ] PDF 文件导入支持

### 低优先级
- [ ] 性能监控工具集成
- [ ] 自动化测试覆盖

---

## 文档演进

### 已创建文档
- `.agent/PROJECT_RULES.md` - 项目核心规则
- `.agent/docs/native-bridge-defensive-guide.md` - 原生桥接防御指南
- `.agent/memory/PROJECT_MEMORY.md` - 本文档

### 计划文档
- [ ] API 设计文档
- [ ] 组件库使用指南
- [ ] 部署流程文档

## Release Protocol (2026-01-01)
### 版本号规则
1. **Patch Update (+0.0.1)**: 小调整、Bug 修复、视觉微调。
2. **Minor Update (+0.1.0)**: 重构模块（RAG, Retrieval）、新增大型功能。
3. **Major Update (+1.0.0)**: 迭代累计或手动触发。

### 构建清单
- **Checklist**:
  - [ ] `package.json` version
  - [ ] `app.json` version & versionCode
  - [ ] `android/app/build.gradle` versionName & versionCode
  - [ ] `settings.tsx` Dynamic Display Check

### 安全构建流程 (Secure Build)
- **密钥管理**:
  - 真实 Keystore 与密码存储于 `secure_env/` (被 gitignore)。
  - `secure_env/secure.properties` 存储敏感凭证。
- **构建命令**:
  - **禁止**直接运行 `gradlew assembleRelease` (会导致未签名或签名失败)。
  - **必须**运行 `.\build-release.ps1`。
  - 该脚本会自动注入凭证 -> 编译 -> 清理现场。
