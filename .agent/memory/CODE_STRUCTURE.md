# 项目结构与组件架构 (Project Structure & Component Architecture)

> **上次更新**: 2026-01-02
> **版本**: v3.7
> **用途**: 供 AI 快速索引项目结构、核心组件与业务逻辑分布。

## 1. 核心目录全景 (Directory Overview)

```
g:\Nx\
├── app/                  # Expo Router 页面路由
│   ├── (tabs)/           # 一级 Tab 页面 (Chat, RAG, Settings)
│   ├── chat/             # 聊天详情与 Agent 配置页
│   ├── settings/         # 二级设置页
│   └── rag/              # 知识库详情页
├── src/
│   ├── components/       # UI 与业务组件库
│   │   ├── ui/           # 基础原子组件
│   │   ├── chat/         # 聊天业务组件
│   │   └── rag/          # RAG 业务组件
│   ├── features/         # 业务逻辑封装 (Settings, Rag, Chat)
│   ├── lib/              # 核心逻辑库 (LLM, DB, RAG Engine)
│   ├── store/            # Zustand 全局状态管理
│   └── theme/            # 样式与主题系统
└── .agent/               # 项目记忆与规则
```

## 2. 组件库清单 (Component Inventory)

### 2.1 基础 UI 系统 (`src/components/ui`)
*标准化 UI 原子组件，所有的页面开发应优先复用此目录下的组件。*

| 组件名 | 用途 | 关键 Props |
| :--- | :--- | :--- |
| **`PageLayout`** | 页面容器 | `safeArea` (bool) - 是否自动处理刘海屏 |
| **`Typography`** | 文本渲染 | `variant` (h1/h2/body/caption), `color` |
| **`Button`** | 交互按钮 | `variant` (primary/secondary/ghost), `size` |
| **`GlassHeader`** | **二级页面标准头** | `title`, `subtitle`, `leftAction`, `rightAction` |
| **`LargeTitleHeader`** | **一级页面大标题头** | `title`, `rightElement` |
| **`ConfirmDialog`** | 操作确认弹窗 | `visible`, `onConfirm`, `isDestructive` |
| **`Toast`** | 全局轻提示 | `useToast()` hook 调用 |
| **`Card`** | 内容容器 | 预设圆角与阴影 |
| **`Switch`** | 开关控件 | 开关状态切换 |

### 2.2 聊天模块 (`src/components/chat`)
| 组件名 | 用途 |
| :--- | :--- |
| `ChatBubble` | 消息气泡 (支持 Markdown, LaTeX, SVG, 代码高亮) |
| `ChatInput` | 输入框 (支持多行、语音输入) |
| `AgentAvatar` | 智能体头像 (支持自动回退首字母) |
| `AgentCard` | 智能体展示卡片 |
| `SuperAssistantFAB` | 超级助手悬浮入口 |
| `SwipeableAgentItem` | 列表项 (支持左滑置顶/右滑删除) |

### 2.3 知识库模块 (`src/components/rag`)
| 组件名 | 用途 |
| :--- | :--- |
| `FolderTree` | 文件夹与文档树形图 |
| `RagDocItem` | 文档列表项 |
| `DocumentPickerModal` | RAG 关联选择器 |
| `RagSettingsPanel` | 知识库参数配置 |

## 3. 核心逻辑架构 (Core Logic)

### 3.1 LLM 引擎 (`src/lib/llm`)
- **`providers/`**: 适配 OpenAI, Gemini, VertexAI 协议。
- **`factory.ts`**: 统一模型实例化工厂。
- **`model-specs.ts`**: 定义上下文窗口、费率等元数据。

### 3.2 RAG 引擎 (`src/lib/rag`)
- **`vector-store.ts`**: 本地向量数据库 (SQLite + FileSystem)。
- **`embedding.ts`**: 向量生成 (Local / API)。
- **`reranker.ts`**: 结果重排序优化。
- **`memory-manager.ts`**: 长期记忆管理。

### 3.3 状态管理 (`src/store`)
- `chat-store`: 会话、消息流、UI 状态 (Persist)。
- `agent-store`: 智能体定义与配置 (Persist)。
- `rag-store`: 文档索引与状态 (Persist)。
- `settings-store`: 应用级偏好 (语言、主题、触感)。
- `api-store`: API Key 与 Provider 配置 (Persist + Secure)。
- `token-stats-store`: Token 消耗统计。

## 4. 已废弃/移除 (Deprecated)
- ❌ `src/components/ui/Header.tsx` (已移除，统一使用 `GlassHeader` 或 `LargeTitleHeader`)
- ❌ `src/features/rag/utils.ts` (已移除，逻辑合并至 `src/lib/rag`)

---
**维护准则**: 新增组件或重构核心逻辑后，请同步更新此文档。
