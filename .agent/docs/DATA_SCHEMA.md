# Nexara 数据模式与核心类型定义

> **角色**: 数据结构的单一事实来源 (SSOT)
> **状态**: 实时同步 (`src/types` 与 `src/store`)
> **法则**: 任何对 `src/types/*.ts` 或 `src/store/*.ts` 的修改**必须**同步更新此处。

---

## 1. 核心类型 (`src/types/chat.ts`)

### 1.1 会话 (Session)
| 字段 | 类型 | 描述 |
| :--- | :--- | :--- |
| `id` | `string` (UUID) | 唯一会话识别码 |
| `agentId` | `string` | 关联智能体 ID |
| `title` | `string` | 对话标题 |
| `messages` | `Message[]` | 消息历史记录 |
| `summary` | `string?` | 自动生成的摘要 |
| `stats` | `object` | Token 使用统计 (`totalTokens`, `billing`) |
| `options` | `object` | 会话专属开关 (`webSearch`, `reasoning`) |
| `ragOptions` | `object` | 会话专属 RAG 设置 (`activeDocIds`) |
| `executionMode` | `'auto' | 'semi' | 'manual'` | 可控代理循环模式 |

### 1.2 消息 (Message)
| 字段 | 类型 | 描述 |
| :--- | :--- | :--- |
| `id` | `string` | 唯一消息 ID |
| `role` | `'user' | 'assistant' | 'system' | 'tool'` | 发送者角色 |
| `content` | `string` | Markdown 内容 |
| `reasoning` | `string?` | 思维链内容 (DeepSeek/Gemini) |
| `references` | `RagReference[]` | RAG 引用文献 |
| `tool_calls` | `ToolCall[]` | 工具调用数据 |
| `isArchived` | `boolean` | 向量化归档标记 |

### 1.3 智能体 (Agent)
| 字段 | 类型 | 描述 |
| :--- | :--- | :--- |
| `id` | `string` | 智能体 ID |
| `systemPrompt` | `string` | 核心人格设定指令 |
| `defaultModel` | `string` | 首选 LLM 模型 ID |
| `ragConfig` | `RagConfiguration?` | 智能体专属 RAG 覆盖配置 |

---

## 2. 全局状态存储 (`src/store/*.ts`)

### 2.1 ChatStore (`chat-store.ts`)
*管理活跃会话状态与消息流转。*
- **状态**:
    - `sessions`: `Session[]` (主要存储元数据，消息按需加载)
    - `activeRequests`: `Record<sessionId, LlmClient>` (中止控制器)
    - `currentGeneratingSessionId`: `string | null`
- **核心动作**:
    - `generateMessage(sessionId, content, options)`
    - `loadSessionMessages(sessionId, limit)`

### 2.2 RagStore (`rag-store.ts`)
*管理知识库文档、目录及向向量化队列。*
- **状态**:
    - `documents`: `RagDocument[]` (仅元数据，不含全文)
    - `folders`: `RagFolder[]` (层级结构)
    - `vectorizationQueue`: `VectorizationTask[]` (异步处理任务)
    - `processingState`: `object` (实时 RAG 进度指示)
- **核心动作**:
    - `addDocument(...)` -> 加入向量化队列
    - `search(query)` -> 向量相似度检索

### 2.3 SettingsStore (`settings-store.ts`)
*全局用户偏好与应用配置。*
- **状态**:
    - `language`: `'en' | 'zh'`
    - `globalRagConfig`: `RagConfiguration` (默认 RAG 配置)
    - `apiKeys`: (为安全起见，在 `api-store` 中独立管理)
- **持久化**: `AsyncStorage` (JSON 格式)

---

## 3. 数据库模式 (SQLite)

*使用 `op-sqlite` 的物理存储层。*

### `sessions` (会话表)
```sql
CREATE TABLE sessions (
  id TEXT PRIMARY KEY,
  agent_id TEXT,
  title TEXT,
  updated_at INTEGER,
  metadata JSON -- 存储 options, stats, executionMode 等
);
```

### `messages` (消息表)
```sql
CREATE TABLE messages (
  id TEXT PRIMARY KEY,
  session_id TEXT,
  role TEXT,
  content TEXT,
  created_at INTEGER,
  metadata JSON -- 存储 reasoning, usage, references 等
);
```

### `documents` (文档表)
```sql
CREATE TABLE documents (
  id TEXT PRIMARY KEY,
  title TEXT,
  content TEXT, -- 全文内容
  folder_id TEXT,
  vectorized INTEGER, -- 状态: 0: 等待, 1: 处理中, 2: 完成, -1: 失败
  metadata JSON
);
```

### `vectors` (向量表)
```sql
CREATE TABLE vectors (
  id TEXT PRIMARY KEY,
  doc_id TEXT, -- 如果是长期记忆则为空
  content TEXT, -- 切片内容
  embedding BLOB, -- 向量数据 (Float32Array)
  metadata JSON -- 包含 session_id (记忆) 或文档元数据
);
```
