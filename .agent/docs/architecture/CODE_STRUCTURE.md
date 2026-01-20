# 项目结构与组件架构 (Project Structure & Component Architecture)

> **上次更新**: 2026-01-14  
> **版本**: v5.0  
> **用途**: 供 AI 快速索引项目结构、核心组件与业务逻辑分布。  
> **重要变更**: 新增 LLM 抽象层完整架构（v1.0）

## 1. 核心目录全景 (Directory Overview)

```
/home/lengz/Nexara/
├── app/                  # Expo Router 页面路由 (基于文件系统)
│   ├── (tabs)/           # 一级 Tab 页面 (Chat, RAG, Settings)
│   ├── chat/             # 聊天详情与 Agent 配置页
│   ├── settings/         # 二级设置页 (Theme, RAG Config, Token Usage)
│   ├── rag/              # 知识库文件夹详情页
│   └── visual-demo.tsx   # 视觉组件演示页 (Playground)
├── plugins/              # Expo Config Plugins (签名注入、宽色域配置等)
├── scripts/              # 构建与版本维护脚本
├── src/
│   ├── components/       # 业务组件库
│   │   ├── ui/           # 基础原子组件 (Standard UI System)
│   │   ├── chat/         # 聊天模块业务组件
│   │   ├── rag/          # RAG 模块业务组件 (Breadcrumbs, KG View)
│   │   └── icons/        # 品牌与模型图标渲染器
│   ├── features/         # 复杂业务逻辑与专属面板
│   │   ├── chat/         # 消息气泡、Token 统计、推理面板
│   │   └── settings/     # 备份恢复、多供应商管理、RAG 设置面板
│   ├── lib/              # 核心基础设施 🔥
│   │   ├── llm/          # LLM 抽象层架构 (v1.0)
│   │   │   ├── providers/          # 网络层 HTTP Clients
│   │   │   │   ├── openai.ts       # OpenAI 兼容协议
│   │   │   │   ├── gemini.ts       # Gemini API
│   │   │   │   └── vertexai.ts     # Vertex AI
│   │   │   ├── formatters/         # Provider专属Formatters
│   │   │   │   └── provider-formatters.ts  # 5个Formatter实现
│   │   │   ├── response-normalizer.ts   # 响应标准化器
│   │   │   ├── stream-parser.ts         # 流式解析器 + 内容清理
│   │   │   ├── message-formatter.ts     # 格式化器接口
│   │   │   ├── formatter-factory.ts     # Provider路由工厂
│   │   │   ├── factory.ts               # Client创建工厂
│   │   │   ├── types.ts                 # 类型定义
│   │   │   ├── error-normalizer.ts      # 错误标准化
│   │   │   └── api-logger.ts            # API日志
│   │   ├── rag/          # RAG 引擎 (向量化、知识图谱)
│   │   ├── db/           # SQLite 数据库
│   │   ├── i18n/         # 国际化
│   │   └── skills/       # 工具/技能系统
│   ├── store/            # Zustand 状态管理 (Persist & Secure)
│   └── theme/            # 动态主题系统 (ThemeProvider)
└── .agent/               # 项目记忆、工作流与架构指南 🔥
    ├── docs/             # 详细架构文档
    │   ├── llm-abstraction-layer-guide.md      # LLM架构完整指引
    │   ├── android-build-guide.md              # Android构建指南
    │   ├── native-bridge-defensive-guide.md    # 原生桥接防御
    │   ├── release-protocol.md                 # 发布流程
    │   ├── settings-panels-reference.md        # 设置面板参考
    │   └── steerable-agent-loop-design.md      # 可控代理循环
    │   └── CODE_STRUCTURE.md   # 本文档
    ├── workflows/        # 工作流定义
    └── PROJECT_RULES.md  # 项目规则
```

## 2. 组件库清单 (Component Inventory)

### 2.1 基础 UI 系统 (`src/components/ui`)
*标准原子组件，遵循动态主题与 HDR 动效规范。*

| 组件名 | 用途 | 关键特性 |
| :--- | :--- | :--- |
| **`PageLayout`** | 统一页面容器 | 处理安全区域、背景色与 FlashList 适配 |
| **`Typography`** | 跨平台文本渲染 | 支持多种 Variant (h1-h4, body, mono, label) |
| **`Button`** | 标准按钮 | 支持动态主题色、Ghost 模式与加载状态 |
| **`GlassHeader`** | **二级页面标准头** | 毛玻璃效果，集成返回逻辑与右侧操作 |
| **`LargeTitleHeader`** | **一级页面大标题** | 适配 iOS 风格大标题，支持自定义右侧入口 |
| **`ConfirmDialog`** | 操作确认弹窗 | 标准化危险操作提醒 |
| **`Toast`** | 全局消息提示 | 基于 `useToast` Hook 的指令式触发 |
| **`FloatingTextEditor`** | **全屏文本编辑器** | 处理键盘动态避让，用于 Prompt 深度编辑 |
| **`RainbowSlider`** | **彩虹色调色盘** | 线性色彩选择滑块 |
| **`ParticleEnergyGlow`** | **HDR 能量球** | Skia 驱动的高动态范围粒子动效 |
| **`GlassBottomSheet`** | 通用半屏浮窗 | 适配手势关闭与毛玻璃质感 |
| **`ColorPickerPanel`** | 调色盘容器 | 支持十六进制输入与色块选择 |
| **`Switch` / `Slider`** | 基础表单控件 | 全量适配 `accentColor` 动态色彩 |

### 2.2 聊天与 AI 交互 (`src/components/chat` & `src/features/chat`)
| 组件名 | 路径 | 用途 |
| :--- | :--- | :--- |
| **`ChatBubble`** | `features/chat` | **消息核心** (Markdown, LaTeX, SVG, 代码高亮, 文献索引) |
| **`ChatInput`** | `features/chat` | 增强输入框，支持多行自适应与附件预览 |
| **`SuperAssistantFAB`** | `components/chat` | 超级助手悬浮入口，集成 HDR 粒子背景 |
| **`InferencePresets`** | `components/chat` | 快速推理模板选择器 |
| **`TokenStatsModal`** | `features/chat` | Token 消耗详情与可视化分析 |
| **`SwipeableAgentItem`** | `components/chat` | 支持侧滑操作的智能体列表项 |
| **`AgentAvatar`** | `components/chat` | 具备回退机制的圆形头像组件 |

### 2.3 知识库与 RAG 模块 (`src/components/rag`)
| 组件名 | 用途 |
| :--- | :--- |
| **`KnowledgeGraphView`** | **知识图谱可视化** (WebView + vis-network，支持 crud) |
| **`RagDocItem`** | 文档/文件列表项渲染 |
| **`FolderItem`** | 知识库目录项，支持聚合显示 |
| **`ControlBar`** | RAG 管理工具条 (全选、排序、筛选) |
| **`TagManagerSheet`** | 全量标签库管理界面 |
| **`TagAssignmentSheet`** | 快速为文档打标签的底部面板 |
| **`Breadcrumbs`** | 目录导航面包屑 |
| **`PdfExtractor`** | PDF 解析与元数据提取进度展示 |

### 2.4 设置与管理 (`src/features/settings`)
| 组件名 | 用途 |
| :--- | :--- |
| **`GlobalRagConfigPanel`** | 站点级 RAG 参数 (分片、检索模型、摘要阈值) |
| **`AgentRagConfigPanel`** | 为特定 Agent 覆盖全局配置的专用面板 |
| **`ProviderModal`** | **多供应商管理** (OpenAI, Gemini, VertexAI 等) |
| **`ModelPicker`** | 智能模型选择器，支持能力图标 (Reasoning, Vision) |
| **`BackupSettings`** | 数据库/配置的导出与本地文件恢复 |

### 2.5 核心基础设施 (`src/lib`)\n*LLM引擎、RAG系统、技能管理与数据库*

#### 2.5.1 LLM抽象层 (`lib/llm`) 🔥

> **版本**: v1.0 (2026-01-14)  
> **详细文档**: `.agent/docs/llm-abstraction-layer-guide.md`

**三层架构**：

| 层级 | 组件 | 文件 | 职责 |
|------|------|------|------|
| **抽象层** | ResponseNormalizer | `response-normalizer.ts` | 统一各Provider响应格式 |
| | StreamParser | `stream-parser.ts` | 解析工具调用 + Provider特定清理 |
| | MessageFormatter | `message-formatter.ts` | 接口定义 |
| | Provider Formatters | `formatters/provider-formatters.ts` | 5个专属实现 |
| | FormatterFactory | `formatter-factory.ts` | Provider路由 |
| **网络层** | OpenAI Client | `providers/openai.ts` | OpenAI兼容协议 |
| | Gemini Client | `providers/gemini.ts` | Gemini API |
| | Vertex Client | `providers/vertexai.ts` | Vertex AI |
| **工具层** | Factory | `factory.ts` | 创建LLM Client |
| | Types | `types.ts` | 类型定义 |
| | Error Normalizer | `error-normalizer.ts` | 错误标准化 |
| | API Logger | `api-logger.ts` | API调用日志 |

**支持的Provider**（按服务商细分）：
- OpenAI / SiliconFlow / GitHub
- DeepSeek（支持reasoning）
- GLM / zhipu（XML工具调用）
- KIMI / moonshot（基本兼容）
- Gemini / Vertex AI

#### 2.5.2 RAG引擎 (`lib/rag`)

| 组件 | 用途 |
|------|------|
| **`rag-manager.ts`** | 向量化检索核心管理器 |
| **`memory-manager.ts`** | 记忆归档与上下文管理 |
| **`graph-extractor.ts`** | 知识图谱提取器 |
| **`text-splitter.ts`** | 文档分片策略 |

#### 2.5.3 技能系统 (`lib/skills`)

| 组件 | 用途 |
|------|------|
| **`skill-manager.ts`** | 工具/技能注册与执行 |
| **`registry.ts`** | 内置技能库 |

#### 2.5.4 数据库 (`lib/db`)

| 组件 | 用途 |
|------|------|
| **`db.ts`** | SQLite 数据库初始化与模式 |

#### 2.5.5 国际化 (`lib/i18n`)

| 组件 | 用途 |
|------|------|
| **`useI18n.ts`** | 国际化Hook（中文/英文） |
| **`translations/`** | 翻译资源文件 |

## 3. 架构准则与废弃说明

### 3.1 核心准则
- **原子化**: 严禁在页面 (app/) 中直接书写复杂样式，必须沉淀至 `src/components/ui`。
- **动态色彩**: 所有颜色必须通过 `useTheme().colors` 获取，严禁使用 `#6366f1` 等硬编码十六进制。
- **国际化**: UI 字符串必须通过 `useI18n().t` 调用，严禁使用硬编码汉字。

### 3.2 已废弃 (Zombie / Deprecated)
- ❌ `src/components/chat/AgentCard.tsx` (由 `SwipeableAgentItem` 替代)
- ❌ `src/components/rag/RagFolderCard.tsx` (由 `FolderItem` 替代)
- ❌ `src/components/rag/RagSettingsPanel.tsx` (由 `GlobalRagConfigPanel` 替代)
- ❌ `src/features/settings/ProviderSettings.tsx` (由 `ProviderModal` 替代)
- ❌ `src/components/demo/ParticleGlow.tsx` (由 `ui/ParticleEnergyGlow.tsx` 替代)

---
**维护准则**: 每次重大功能演进后，须由 Agent 更新此架构图谱，确保全栈索引的一致性。

---
**维护准则**: 新增组件或重构核心逻辑后，请同步更新此文档。

## 4. LLM抽象层架构 🔥

> **版本**: v1.0 (2026-01-14)  
> **重要性**: ⭐⭐⭐⭐⭐ 核心架构  
> **完整文档**: `.agent/docs/llm-abstraction-layer-guide.md`

### 4.1 架构概览

**三层分离原则**：业务层 → 抽象层 → 网络层

```
chat-store.ts (业务层)
  ↓ 调用抽象层API
【抽象层】← 所有Provider差异隔离在此
  ├─ ResponseNormalizer    统一响应格式
  ├─ StreamParser          解析+清理Internal content
  ├─ MessageFormatter      构建历史记录
  └─ FormatterFactory      Provider路由
  ↓ 调用HTTP Client
openai.ts / gemini.ts / vertexai.ts (网络层)
```

### 4.2 组件清单与职责

| 组件 | 文件位置 | Provider颗粒度 | 职责 |
|------|---------|---------------|------|
| **ResponseNormalizer** | `lib/llm/response-normalizer.ts` | ✅ 已细分 | 统一各Provider响应为`NormalizedChunk` |
| **StreamParser** | `lib/llm/stream-parser.ts` | ✅ 已细分 | 增量解析工具调用+Provider特定清理 |
| **MessageFormatter** | `lib/llm/message-formatter.ts`<br>`formatters/provider-formatters.ts` | ✅ 已细分 | 处理历史记录构建差异 |
| **FormatterFactory** | `lib/llm/formatter-factory.ts` | - | 根据provider类型返回Formatter |

**支持的Provider**（按服务商划分）：
- OpenAI / SiliconFlow / GitHub
- DeepSeek
- GLM (智谱AI)
- KIMI (月之暗面)
- Gemini / Vertex AI

### 4.3 核心设计原则

1. **职责隔离** ✅
   - ❌ 业务层**禁止**包含 `if (provider === 'xxx')` 判断
   - ✅ 所有Provider差异**必须**在抽象层处理

2. **Provider颗粒度** ✅
   - 不是粗粒度的"OpenAI vs Gemini"
   - 而是细粒度的"DeepSeek / GLM / KIMI"

3. **独立扩展** ✅
   - 新增Provider不影响现有逻辑
   - 调试某个Provider不会"修A坏B"

### 4.4 关键使用场景

**场景1：修复GLM输出XML问题**
```typescript
// ✅ 正确位置：StreamParser.getCleanContent()
case 'zhipu':
  cleaned = cleaned.replace(/<tool_call>[\s\S]*?<\/tool_call>/gi, '');
```

**场景2：DeepSeek支持reasoning回传**
```typescript
// ✅ 正确位置：DeepSeekFormatter.formatHistory()
// 保留reasoning字段，不像OpenAI那样删除
```

**场景3：添加新Provider（Anthropic）**
1. `response-normalizer.ts` - 添加`normalizeAnthropic()`
2. `provider-formatters.ts` - 创建`AnthropicFormatter`
3. `formatter-factory.ts` - 注册路由
4. `stream-parser.ts` - （可选）添加清理逻辑

### 4.5 ⚠️ 常见错误

| ❌ 错误做法 | ✅ 正确做法 |
|-----------|-----------|
| 在chat-store中写正则清理XML | 在StreamParser.getCleanContent() |
| 在业务层判断reasoning支持 | 在MessageFormatter中处理 |
| 修改网络层做格式转换 | 在ResponseNormalizer中处理 |

### 4.6 快速参考

- **添加新Provider** → 参考 `llm-abstraction-layer-guide.md` 第三章
- **调试现有Provider** → 参考 `llm-abstraction-layer-guide.md` 第四章
- **架构原则** → 参考 `llm-abstraction-layer-guide.md` 第二章

---
**最后更新**：2026-01-14  
**维护者**：Agent + Architecture Team
