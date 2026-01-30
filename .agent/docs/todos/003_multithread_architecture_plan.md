# Nexara 高性能并发架构蓝图 (v3.0)
**版本**: 3.0 (Zero-Lock UI / Bypass Main Thread)  
**状态**: 待执行 (Pending)  
**目标**: 实现 **"打字机绝对流畅"** —— 即便主线程被 React Render 占满，流式输出也能以 60/120fps 丝滑更新，彻底通过 Reanimated 旁路机制解耦 UI。

## 1. 核心架构拓扑：四级流水线

我们将引入一个全新的概念：**Direct UI Channel (直通 UI 通道)**。

```mermaid
graph TD
    subgraph Network Layer
        Socket[SSE / WebSocket Connection]
    end

    subgraph T1 [Interactive Worklet (Parser)]
        Parser[Stream Parser Engine]
        note1[Parse chunks in <1ms]
    end

    subgraph T_UI [UI Thread (Native/Reanimated)]
        SharedVal[SharedValue<string>]
        TextNode[Reanimated Text Input/Label]
        note2[Render @ 120fps independent of JS]
    end

    subgraph T0 [Main JS Thread (Business Logic)]
        React[React Reconciler]
        Store[Zustand State]
        DB_Write[Persistence]
    end

    subgraph T2 [Compute Worklet]
        RAG[RAG Engine]
        Graph[Graph Engine]
    end

    %% Data Flow
    Socket --> T1
    
    %% The Critical "Bypass" Path
    T1 -- 1. Update SharedValue --> T_UI
    T1 -- 2. Batch Sync (e.g. every 500ms) --> T0

    %% Standard React Flow (Slow Path)
    T0 -- React Render --> T_UI
    
    %% Background Compute
    T0 -- Job Dispatch --> T2
    T2 -- Result --> T0
```

### 1.1 T1 -> T_UI: 旁路模式 (The Bypass)
这是 v3.0 的精髓。
*   **传统模式**: Network -> Parser -> Zustand(T0) -> React Render(T0) -> Native UI。路径太长，T0 一卡就完蛋。
*   **旁路模式**: Network -> Parser(T1) -> `sharedText.value = newText` -> Reanimated 监听变化 -> 直接修改原生 View 的 Props (setNativeProps)。
*   **结果**: 文字的跳动完全脱离了 React 的控制，即便主线程在进行复杂的页面切换或 heavy calculation，打字机动画依然像 Native 游戏一样流畅。

### 1.2 T0: 降级为 "最终一致性保证者"
主线程不再负责每一帧的文字更新，而是负责 "归档"。
*   T1 每隔 500ms 或在结束时，把最终完整的 Text 发给 T0 存入 Zustand/DB。
*   这样既保证了数据持久化，又避免了高频 React Render。

---

## 2. 详细功能分布

### 2.1 交互即时线程 (T1 - Interactive Worklet)
*   **输入**: 网络流 Binary Chunk。
*   **逻辑**: 
    1.  执行 `StreamParser` 状态机。
    2.  清洗出 `content`, `reasoning`, `thinking_time`。
    3.  **直接写入**: `runOnUI(() => { textSV.value = content; })`。
*   **输出**: 周期性向 T0 发送 Snapshot。

### 2.2 UI 线程 (T_UI - Reanimated Runtime)
*   **组件改造**: 弃用标准的 `<Text>`，全面转向 `<AnimateableText>` (基于 `TextInput` 或 Custom Native View)。
*   **动画**: 光标闪烁、自动滚动 (Auto-Scroll) 全部在 UI 线程通过 `useAnimatedReaction` 实现。

### 2.3 密集计算线程 (T2 - Compute Worklet)
*   **职责维持不变**: 负责 RAG 的向量运算、Graph 的 JSON 解析。
*   **隔离性**: 即使 T2 把 CPU 跑到 100%，T1 和 T_UI 依然有操作系统的更高优先级（通常 UI 线程优先级极高），保证打字不卡。

---

## 3. 实施步骤规划 (含难度评级)

### Phase 1: 基础设施 (⭐⭐)
1.  集成 `react-native-worklets-core`。
2.  封装 `SharedValue` 通信协议。

### Phase 2: Parser 迁移与旁路验证 (⭐⭐⭐⭐⭐)
这是最难的一步，也是核心。
3.  **Port Parser**: 将 `StreamParser` 改写为 Worklet 友好的纯函数。
4.  **Reanimated Text Component**: 封装一个能接受 SharedValue 的文本组件（避免 React 重绘）。
    *   *技术难点*: 在 Android 上高性能地更新大量文本可能会触发 Layout Thrashing。需要使用 `TextInput` 的 `setNativeProps` hack 或原生 TextKit binding。

### Phase 3: RAG & Graph 迁移 (⭐⭐⭐)
5.  将 `MemoryManager` 的数学计算部分移入 T2。
6.  将 `GraphExtractor` 的 JSON 解析移入 T2。

### Phase 4: 数据库 IO 隔离 (⭐⭐⭐)
7.  尝试将 `op-sqlite` 的写入操作移至 T3 (Dedicated DB Thread)，主线程只读。

---

## 4. 风险预警

1.  **Layout 同步问题**: 如果 T1 更新了文字导致高度变化，而 T0 的 React Layout 系统不知道，可能会导致布局错位/重叠。
    *   *对策*: Auto-height 是个大坑。可能需要 T_UI 计算完高度后同步给 SharedValue，通知 List 组件调整。或者使用 `FlashList` 的动态高度测量机制。
2.  **Crash**: 多线程操作同一块内存（SharedArrayBuffer）若无锁保护易崩溃。但我们采用 "单写多读" (T1 写 UI 读)，相对安全。
