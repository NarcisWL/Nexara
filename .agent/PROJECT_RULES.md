# NeuralFlow 项目规则与准则

## 项目概述

**项目名称**: NeuralFlow  
**技术栈**: React Native + Expo + TypeScript + Zustand + NativeWind  
**架构模式**: 文件路由 (expo-router) + 底部Tab导航  
**开发环境**: Windows 11 + PowerShell

---

## 核心架构原则

### 1. 导航架构
- **路由系统**: `expo-router` 文件路由
- **Tab 导航**: `app/(tabs)/` 文件夹约定
- **关键配置**: Tab 导航器使用 `key={language}` 强制语言切换时重新挂载

### 2. 状态管理
- **全局状态**: Zustand store（主题、语言、设置）
- **持久化**: zustand persist middleware
- **状态初始化**: 使用 `_hasHydrated` 标志确保数据加载完成

### 3. 国际化 (i18n)
- **实现方式**: 自定义 `useI18n` hook
- **语言切换**: 通过 Zustand store 管理，切换会触发导航器重挂载
- **防御措施**: 语言状态变更必须延迟执行，避免死锁

### 4. 主题系统
- **实现**: ThemeProvider + Context
- **样式方案**: NativeWind (Tailwind CSS for React Native)
- **Dark Mode**: 完整支持亮色/暗色模式切换

---

## 关键组件规范

### PageLayout 组件
```tsx
// ✅ 正确实现（无嵌套View）
export function PageLayout({ safeArea = true, className, children, ...props }) {
    const containerClass = twMerge("flex-1 bg-white dark:bg-black", className);
    
    if (safeArea) {
        return (
            <SafeAreaView className={containerClass} {...props} edges={['top', 'left', 'right']}>
                {children}
            </SafeAreaView>
        );
    }
    
    return <View className={containerClass} {...props}>{children}</View>;
}
```

**关键要点**：
- ❌ 避免不必要的嵌套 `<View>` 包装
- ✅ 直接渲染 `children`
- ✅ 使用 `react-native-safe-area-context` 的 `SafeAreaView`

### 原生桥接调用规范

**强制要求**：所有原生桥接调用（Haptics、SecureStore、FileSystem等）必须延迟执行。

```tsx
// ❌ 错误 - 同步调用
<TouchableOpacity onPress={() => {
    Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
    setState(value);
}}>

// ✅ 正确 - 延迟调用
<TouchableOpacity onPress={() => {
    setTimeout(() => {
        Haptics.impactAsync(Haptics.ImpactFeedbackStyle.Light);
        setState(value);
    }, 10);
}}>
```

**必须延迟的场景**：
- 状态变更（setState、zustand set）
- 语言/主题切换
- 导航操作（router.push、Tab切换）
- 嵌套 TouchableOpacity
- Modal 打开/关闭

---

## 文件结构约定

```
NeuralFlow/
├── app/                    # Expo Router 路由
│   ├── (tabs)/            # Tab 导航页面
│   │   ├── _layout.tsx    # Tab 布局配置
│   │   ├── chat.tsx       # 对话页
│   │   ├── rag.tsx        # 文库页
│   │   └── settings.tsx   # 设置页
│   ├── rag/               # 文库子页面
│   │   └── [folderId].tsx # 文件夹详情
│   └── _layout.tsx        # 根布局
├── src/
│   ├── components/        # 公共组件
│   │   └── ui/           # UI 基础组件
│   ├── store/            # Zustand stores
│   ├── theme/            # 主题配置
│   ├── lib/              # i18n 等工具
│   └── features/         # 功能模块
└── .agent/               # 项目规则和文档
    ├── docs/             # 详细文档
    └── memory/           # 项目记忆
```

---

## 已知问题与解决方案

### 1. 导航上下文错误
**问题**: "Couldn't find a navigation context"  
**原因**: 
- PageLayout 组件嵌套 View
- 状态变更导致导航器重新挂载时同步调用原生模块

**解决方案**:
- 移除 PageLayout 的嵌套 View
- 延迟所有原生桥接调用

### 2. 语言切换器死锁
**问题**: 点击语言切换器时震动异常、应用崩溃  
**原因**: `setLanguage` 触发导航器 `key={language}` 变化，导致重新挂载，与 Haptics 同步调用产生竞争  
**解决方案**: 将 Haptics 和 setLanguage 都放在 setTimeout 中

### 3. 触觉反馈异常
**症状**: 某个交互的震动比其他地方"延迟但劲更大"  
**诊断**: 线程阻塞导致的性能问题  
**排查**: 检查是否有同步的原生桥接调用

---

## 开发流程规范

### 防御性开发策略
1. 从最简单的版本开始
2. 逐步添加功能
3. 每次添加后立即测试
4. 一旦崩溃，立即定位上一步的改动

### 性能检测方法
1. **触觉测试**: 逐个点击所有交互元素，感受震动是否一致
2. **快速操作测试**: 快速连续操作，观察是否有异常
3. **低端设备测试**: 在性能较差的设备上测试

---

## 代码审查清单

### 通用检查
- [ ] 无硬编码的绝对路径
- [ ] 组件文件不超过 500 行
- [ ] 复杂逻辑已抽离到独立 hook 或 util

### 原生桥接检查
- [ ] 所有 `Haptics.` 调用在 setTimeout 中
- [ ] 所有状态变更附近的原生调用已延迟
- [ ] 嵌套 TouchableOpacity 使用延迟模式
- [ ] 导航操作附近无同步原生调用

### 性能检查
- [ ] 列表使用 Memoization
- [ ] 大型组件使用 Lazy loading
- [ ] 无全屏模糊滤镜
- [ ] 加载状态使用骨架屏

---

## 技术债务追踪

### 当前已知技术债务
- 无（本次重构已清理）

### 待实现功能
- 供应商管理（AI Providers）完整实现
- 推理引擎配置
- 数据导出/备份功能

---

## 9. 跨设备 Gradle 编译卫士 (Gradle Hygiene)

### 9.1 背景
由于项目在多台设备间同步开发，各设备的构建缓存与原生依赖状态可能不一致。

### 9.2 核心准则
**拉取即物理清理 (Physical Deep Clean on Pull)**：
- **触发条件**：在执行 `git pull`、`git fetch` 或 `git reset` 之后的第一次构建。
- **强制操作**：必须先执行物理层面的全量清理，严禁仅依赖 `gradlew clean`。
- **清理命令**：`Remove-Item -Recurse -Force android/.cxx, android/.gradle, android/build, android/app/build`。
- **构建序列**：物理清理 -> `cd android` -> `./gradlew assembleRelease`。

---

## 10. Android 透明开发规范 (Android Visibility & Git Hygiene)

### 10.1 特权访问 (Privileged Access)
- **开关依赖**：确保在 Antigravity 设置中开启 `Agent Gitignore Access`。
- **透明编辑**：即便 `.gitignore` 中存在 `android/` 记录，Agent 必须利用特权直接操作原生文件，严禁使用非直观的正则脚本。

### 10.2 零操作隔离 (Zero-Op Isolation)
- **常驻忽略**：`/android` 目录应永久保留在 `.gitignore` 中，确保原生构建产物不污染远程仓库。
- **简化操作**：基于特权访问，无需在推送前动态修改 `.gitignore`。

---

## 11. 语言与沟通规范 (Communication)

- **零容忍语言漂移 (Zero English Drift)**：所有解释、规划、说明及任务描述必须使用**简体中文**。
- **技术转译**：分析英文日志或文档时，核心矛盾与结论必须翻译为中文呈现，禁止直接搬运长段英文。

---

## 12. 双流水线架构协议 (Dual Pipeline Protocol)

### 12.1 环境隔离
- **Dev 主环境 (`/home/lengz/Nexara`)**：仅用于日常开发与 Debug 编译。使用本地 `debug.keystore`，严禁注入发行版密码（SSOT/PoLP）。
- **Release 工厂 (`worktrees/release`)**：通过 `git worktree` 建立的独立发行环境。
    - **路径规范**：在 Linux/WSL 环境下建议使用 `worktrees/release`；在 Windows 原生环境下由于 MAX_PATH 限制建议使用极短路径（如 `/R`）。
    - **永久签名**：该环境允许硬编码正式版签名信息（`signingConfigs.release`），仅存在于本地。

### 12.2 维护与同步
- **单向逻辑流**：业务逻辑在 `main` 提交 -> 在 `worktrees/release` 环境 `git pull` 同步 -> 执行打包。
- **配置防漂移**：发行环境的 `android/app/build.gradle` 允许与 `main` 存在差异（硬编码签名），禁止将此差异推送到远程。
- **清理习惯**：每次重大版本更新前，`R` 环境必须执行物理深度清理（见规则 9）。

---

## 13. 开发工具自动化 (DevTools Automation)

### 13.1 WSL2 ADB 桥接
- **自动化脚本**：项目根目录下 `scripts/start-adb-bridge.ps1` 包含动态查找物理设备逻辑。
- **运行规范**：
    - 在 Windows 宿主机执行该脚本（需管理员权限）。
    - 脚本使用 `--auto-attach` 模式，确保物理拔插后链路能自动恢复。
    - 若 WSL 出现 `Permission Denied`，确保执行了：`sudo service udev restart`。

---

## 14. LLM抽象层架构规范 🔥

> **版本**: v1.0 (2026-01-14)  
> **强制性**：⭐⭐⭐⭐⭐ 核心架构准则  
> **详细文档**: `.agent/docs/llm-abstraction-layer-guide.md`

### 14.1 三层架构强制分离

```
业务层 (chat-store.ts)      ← 纯业务逻辑，无Provider判断
   ↓
抽象层 (Response/Stream/Formatter) ← 所有Provider差异在此
   ↓
网络层 (openai/gemini/vertexai)   ← 纯HTTP通信
```

### 14.2 强制规范 (Mandatory Rules)

#### ❌ 严禁在业务层（chat-store.ts）出现

```typescript
// 禁止示例
if (provider === 'deepseek') { ... }
if (provider === 'zhipu') { ... }
content = content.replace(/<tool_call>.../, ''); // 直接清理XML
```

#### ✅ 必须在抽象层处理

- **格式转换** → `ResponseNormalizer.normalize[Provider]()`
- **内容清理** → `StreamParser.getCleanContent()`
- **历史构建** → `[Provider]Formatter.formatHistory()`

### 14.3 Provider颗粒度要求

**不允许**：粗粒度划分（"OpenAI兼容"）  
**必须**：细粒度划分（DeepSeek / GLM / KIMI 各自独立）

**支持的Provider**：
- OpenAI / SiliconFlow / GitHub (标准OpenAI)
- DeepSeek (支持reasoning)
- GLM / zhipu (XML工具调用)
- KIMI / moonshot (基本兼容)
- Gemini / Vertex (Google系)

### 14.4 扩展新Provider流程

**必须按序完成**：
1. `response-normalizer.ts` → 添加 `normalize[Provider]()`
2. `formatters/provider-formatters.ts` → 创建 `[Provider]Formatter`
3. `formatter-factory.ts` → 注册路由
4. `stream-parser.ts` → （可选）添加清理逻辑

### 14.5 调试定位规则

| 问题症状 | 诊断位置 | 操作方法 |
|---------|---------|---------|
| 输出包含XML/标签 | StreamParser | 修改`getCleanContent()` |
| reasoning显示错误 | ResponseNormalizer | 修改`normalize[Provider]()` |
| 历史记录API错误 | MessageFormatter | 修改`formatHistory()` |
| 循环终止异常 | chat-store通用逻辑 | 修改terminate conditions |

### 14.6 审查清单

添加/修改LLM功能时，必须问：
- [ ] 这是Provider特定的吗？ → 应在**抽象层**
- [ ] 这影响所有Provider吗？ → 应在**业务层**
- [ ] 这只是HTTP细节吗？ → 应在**网络层**

### 14.7 参考文档

- **完整指南**: `.agent/docs/llm-abstraction-layer-guide.md`
- **快速参考**: `.agent/memory/CODE_STRUCTURE.md` 第4章
- **最佳实践**: 指南第四章

---

## 15. 虚拟拆分架构规范 (Virtual Split Architecture) 🔥

> **版本**: v1.0 (2026-01-15)  
> **强制性**：⭐⭐⭐⭐⭐ 核心协议兼容准则  
> **相关文件**: `chat-store.ts:virtualSplitAssistantToolPairs`

### 15.1 背景与目标

**问题**: 不同LLM供应商对多工具调用的协议要求不一致：
- **DeepSeek Reasoner**: 要求所有`assistant`消息必须包含`reasoning_content`字段（即使为空）
- **VertexAI Gemini**: 要求多轮工具调用时继承`thought_signature`
- **OpenAI协议**: 允许单个`assistant`包含多个`tool_calls`，但部分模型执行时会丢失中间状态

**解决方案**: 在Agent Loop内部实施"虚拟拆分"——将单个multi-tool的`assistant`拆分为多个single-tool的`assistant+tool`对。

### 15.2 核心架构

```
原始格式 (Multi-Tool):
user -> assistant(tool_calls: [A, B]) -> tool(A) -> tool(B)

虚拟拆分后 (Single-Tool Pairs):
user -> assistant(tool_calls: [A]) -> tool(A) 
     -> assistant(tool_calls: [B]) -> tool(B)
```

### 15.3 字段继承规则 (Field Inheritance)

**强制要求**: `virtualSplitAssistantToolPairs`函数必须正确继承以下字段：

1. **reasoning** (内部格式):
   - 第一个virtual assistant: 包含完整reasoning内容
   - 后续virtual assistants: 空字符串`''` (不是`undefined`)
   - **关键**: DeepSeekClient会将`reasoning`转换为`reasoning_content`发送给API

2. **thought_signature** (VertexAI):
   - 所有virtual assistants必须继承原始assistant的`thought_signature`
   - 即使assistant没有reasoning，也必须传递signature

3. **tool_calls**:
   - 每个virtual assistant只包含**一个**tool_call
   - `tool_calls`字段仍然是数组，但长度固定为1

4. **user消息**:
   - 虚拟拆分函数必须处理`user`消息，直接传递（不拆分）

### 15.4 历史累积策略 (History Accumulation)

**错误方案** ❌:
```typescript
// 每次从session重新提取完整历史
const rawSegment = session.messages.slice(userMsgIdx);
currentMessages = [...baseHistory, ...virtualSplit(rawSegment)];
```

**正确方案** ✅:
```typescript
// 只提取当前Turn新增的assistant+tool，追加到currentMessages
const newSegment = session.messages.slice(currentAssistantIdx);
currentMessages = [...currentMessages, ...virtualSplit(newSegment)];
```

**理由**: Agent Loop中复用同一个`assistantMsgId`，每次Turn都在更新它。如果重新提取整个session，只会看到1个assistant + 多个tool，导致历史丢失。

### 15.5 内部格式 vs API格式

**严格区分**:
- **内部格式** (Zustand存储): `message.reasoning`
- **API格式** (发送给供应商): `msg.reasoning_content`

**转换位置**: 由各Provider的Client负责（如`DeepSeekClient`）

**关键检查**:
```typescript
// ❌ 错误 - 空字符串会被过滤
if (m.reasoning) {
  msg.reasoning_content = m.reasoning;
}

// ✅ 正确 - 允许空字符串
if (m.reasoning !== undefined) {
  msg.reasoning_content = m.reasoning;
}
```

### 15.6 XHR连接泄漏防御

**问题**: 流式API在收到`[DONE]`后，XHR可能仍处于`readyState 3`，导致并发连接累积。

**修复**: 在`[DONE]`处理时显式关闭XHR

```typescript
if (data === '[DONE]') {
  // 🔑 显式关闭XHR以防止并发连接残留
  if (xhr.readyState !== 4) {
    xhr.abort();
  }
  resolve();
  return;
}
```

**影响**: 解决GLM等严格并发限额（=3）供应商的400错误。

### 15.7 调试检查清单

虚拟拆分相关问题排查：

- [ ] `[VirtualSplit]`日志显示所有virtual assistants都有`reasoning_content`？
- [ ] `[AgentLoop]`日志显示`currentMessages`在累积（不是固定4条）？
- [ ] API请求体（`[API_DEBUG][REQ_BODY]`）中所有assistant都有必需字段？
- [ ] 是否有字段名不匹配（`reasoning` vs `reasoning_content`）？
- [ ] XHR关闭逻辑是否在`[DONE]`处理中？

### 15.8 扩展新Provider注意事项

添加新Provider时，检查：

1. **特殊字段要求**: 是否有类似`reasoning_content`/`thought_signature`的特殊字段？
2. **字段继承**: 是否需要在`virtualSplitAssistantToolPairs`中添加继承逻辑？
3. **格式转换**: 是否需要在Provider的Client中添加字段转换逻辑？
4. **多tool支持**: 该Provider是否支持multi-tool，还是必须虚拟拆分？

### 15.9 禁止事项 (Anti-Patterns)

❌ **禁止在业务层手动拆分tool_calls**:
```typescript
// 不要这样做
for (const tc of toolCalls) {
  await executeToolIndividually(tc);
}
```

❌ **禁止跳过虚拟拆分**:
```typescript
// 不要认为某个Provider"不需要"虚拟拆分
if (provider !== 'deepseek') {
  // 所有Provider都应该走统一的虚拟拆分逻辑
}
```

❌ **禁止在虚拟拆分中修改原始session**:
```typescript
// 虚拟拆分只是生成API请求，不应该修改session.messages
```

### 15.10 参考实现

- **核心函数**: `chat-store.ts:virtualSplitAssistantToolPairs` (L1270-1370)
- **历史累积**: `chat-store.ts` isTaskCreate分支 (L1988-2028) / 正常分支 (L2125-2160)
- **字段转换**: `deepseek.ts` (L364-370)
- **XHR关闭**: `openai.ts` / `deepseek.ts` (L140-145)
- **项目记忆**: `.agent/memory/PROJECT_MEMORY.md` v4.7章节

---

## 16. Chat Store 架构红线 (Chat Store Architecture Red Lines) 🔥

> **版本**: v1.0 (2026-01-20)
> **强制性**: ⭐⭐⭐⭐⭐ (Defcon 1/最高警戒)
> **相关文件**: `src/store/chat-store.ts`

### 16.1 核心原则

**"No New Logic Policy (逻辑冻结协议)"**

`chat-store.ts` 已经因为过度膨胀而在历史重构中引发了灾难性的 Bug 回归。为了阻止熵增，即刻起实施以下红线：

1.  **禁止新增非 UI 逻辑**: `chat-store.ts` 仅作为 Zustand 状态容器和 UI 交互的胶水层。
2.  **UI 状态允许适度扩展**: 涉及纯 UI 状态（如 `togglePanel`, `setViewMode`）的逻辑允许增加。
3.  **零容忍膨胀**: 严禁将复杂的业务逻辑（如音视频处理、文件解析、算法等）直接写入 Store 内部函数。

### 16.2 功能扩展规范 (Use Hooks Pattern)

任何新功能（Feature）必须采用 **Hooks 注入模式**，严禁侵入 Store。

#### ❌ 错误示例 (直接写入)

```typescript
// src/store/chat-store.ts
actions: {
  // ⛔️ 严禁这样写！
  startSpeechToText: async () => {
    await Audio.requestPermission();
    // ... 50行语音识别逻辑
    // ... 30行错误处理
    set({ input: result });
  }
}
```

#### ✅ 正确示例 (Hook 封装)

1.  **新建 Hook**: `src/features/chat/hooks/useSpeechToText.ts`
2.  **在组件中组合**:

```typescript
// src/components/ChatInput.tsx
const useSpeech = () => {
  const { setInput } = useChatStore(); // 只从 Store 取最基本的 setter
  
  const startRecording = async () => {
     // ... 逻辑全部在这里闭环
     const text = await recognize();
     setInput(text); // 仅在最后调用 Store 更新状态
  };
  
  return { startRecording };
};
```

### 16.3 审查清单 (Checklist)

在修改 `chat-store.ts` 之前，必须自问：

- [ ] 我是在添加新的**状态**吗？(允许)
- [ ] 我是在添加新的**业务流程**吗？(禁止 -> 移至 Hook 或 Service)
- [ ] 我是在修复现有 Bug 吗？(允许，但需尽量抽取逻辑)

---

**文档维护**: AI Assistant + Project Team
