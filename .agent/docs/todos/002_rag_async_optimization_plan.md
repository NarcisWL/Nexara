# RAG 异步优化与流水线前移实施方案
**版本**: 1.1 (Config-Aware Refine)  
**状态**: 待执行 (Pending)  
**依赖**: 建议优先实施 [003_multithread_architecture_plan](./003_multithread_architecture_plan.md)
**目标**: 通过“推测性预加载”消除 RAG 检索的首字延迟，同时严格遵循用户的功能开关配置。

## 1. 核心设计理念

**Config-Aware Prefetch (感知配置的预取)**:
预取逻辑不能无脑全开。必须先读取 `SettingsStore` 或 `RagConfiguration`：
*   如果用户关闭了 Rerank，预取阶段也不能跑 Rerank。
*   如果用户关闭了 Rewrite，预取阶段绝不调大模型。
*   **基础策略**: `Embedding` 是必须的 (因为向量检索是 RAG 的核心)，其他步骤按需执行。

---

## 2. 详细架构变更

### 2.1 状态管理 (`src/store/rag-store.ts`)
新增 `prefetch` 状态切片。

```typescript
interface RagState {
  prefetch: {
    query: string;
    status: 'idle' | 'loading' | 'ready';
    context?: string;
    references?: any[];
    timestamp: number;
    // 新增：记录这次预取用了哪些开关，防止用户在输入期间切换开关导致不一致
    configSnapshot?: { enableRewrite: boolean; enableRerank: boolean };
    promise?: Promise<any>;
  }
}
```

### 2.2 核心逻辑 (`src/lib/rag/memory-manager.ts`)
改造 `retrieveContext`，拆解为原子步骤，并在 `prefetchContext` 中动态组合。

**预取流水线逻辑**:
1.  **Check Config**: 读取当前 RAG 配置。
2.  **Rewrite (Optional)**:
    *   如果 `enableQueryRewrite == true`：**跳过** (太贵/太慢) 或 **仅做 Lite Rewrite** (本地关键词)。
    *   *策略*: 预取阶段默认跳过昂贵的大模型 Rewrite，仅使用原始 Query 进行检索。这是一种 "Good Enough" 策略。
3.  **Embedding (Required)**:
    *   执行向量化。这是预取的核心收益点。
4.  **Hybrid Search**:
    *   执行数据库与向量库的并行搜索。
5.  **Rerank (Optional)**:
    *   如果 `enableRerank == true`：**执行**。虽然有 API 成本，但 Rerank 决定了结果质量，且通常比 Rewrite 快。
    *   *成本控制*: 限制 Rerank 的 Candidate 数量 (例如只排 Top 10 而不是 Top 50)。

### 2.3 UI 交互调整

#### A. 输入框感知
在 `ChatInput` 中挂载 Debounce 监听器 (800ms)。

#### B. 预防幻觉与一致性检查
当用户点击发送时 (`consumePrefetch`)：
1.  检查 `prefetch.query === currentQuery`。
2.  **关键**: 检查 `prefetch.configSnapshot` 是否与当前配置兼容。
    *   例如：用户在输入时突然打开了 "Query Rewrite"。
    *   如果预取结果没包含 Rewrite，但用户现在要求 Rewrite -> **废弃预取，重新执行全流程**。
    *   如果配置一致（或预取涵盖了必要步骤） -> **复用结果**。

---

## 3. 实施步骤规划

### Phase 1: 基础设施
1.  **RagStore**: 实现带 Config Snapshot 的 `startPrefetch`。
2.  **MemoryManager**:
    *   提取 `runVectorSearch` 和 `runRerank` 为独立 Public 方法。
    *   实现 `prefetchContext`，默认关闭 Heavy Rewrite。

### Phase 2: UI 集成
3.  **ChatInput**: 集成状态指示器。
4.  **ChatStore**: 在 `generateMessage` 中实现 "Prefetch Consumption Logic" (预取消费逻辑)。
    *   `Logger`: 记录 "Prefetch Hit" 或 "Prefetch Miss (Config Mismatch)" 以便分析。

### Phase 3: 验证
5.  **Case测试**:
    *   开启 Rerank -> 输入预取 -> 检查 Network Log 是否调用 Rerank API。
    *   关闭 Rerank -> 输入预取 -> 确保无多余调用。

---

## 4. 风险控制
*   **Token 浪费**: 限制预取频率（Debounce 800ms + Min Length 8 chars）。
*   **UI 卡顿**: 请务必等待 **[多线程改造]** 完成，否则后台的 Embedding 计算 (尤其是本地模型) 会卡死打字。
