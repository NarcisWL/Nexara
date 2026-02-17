# Nexara 移动端全量视觉与性能审计报告

**审计日期**: 2026-02-17  
**审计范围**: `/home/lengz/Nexara/src` 全部移动端代码  
**审计维度**: 视觉设计、交互性能、运行性能、潜在BUG

---

## 一、问题总览

| 严重程度 | 数量 | 类型 |
|----------|------|------|
| 🔴 **严重** | 8 | Worklet 变量访问违规 |
| 🟠 **中等** | 12 | 性能优化、列表渲染 |
| 🟡 **轻微** | 15 | 代码规范、样式一致性 |

---

## 二、严重问题 (P0)

### 2.1 Worklet 变量访问违规

**问题描述**: `useAnimatedStyle` 中的 worklet 函数运行在 UI 线程，直接访问 JS 线程变量会导致动画不同步、主题切换时颜色不更新。

| 文件 | 组件 | 问题变量 |
|------|------|----------|
| `src/components/ui/Switch.tsx:52-68` | Switch | `isDark`, `colors` |
| `src/components/ui/AnimatedSearchBar.tsx:41-58` | AnimatedSearchBar | `isDark`, `colors` |
| `src/components/ui/AnimatedInput.tsx:52-77` | AnimatedInput | `isDark`, `colors` |
| `src/features/settings/screens/ProviderFormScreen.tsx:252-273` | ProviderFormScreen | `isDark`, `colors` |
| `src/features/settings/screens/ProviderModelsScreen.tsx:896-901` | TypeButton | `inactiveColor`, `activeColorRgb` |
| `src/features/settings/screens/ProviderModelsScreen.tsx:945-952` | CapabilityTag | `inactiveBg`, `activeColorRgb` |
| `src/features/chat/components/ChatInput.tsx:416-425` | ChatInput | `focusBorderColorInactive`, `focusBorderColorActive` |

**修复方案**: 在组件渲染时预先计算颜色值，再传入 worklet：

```typescript
// ❌ 错误
const animatedStyle = useAnimatedStyle(() => ({
    backgroundColor: interpolateColor(progress.value, [0, 1], [
        isDark ? '#fff' : '#000',  // JS 线程变量
        colors[500],                // JS 线程变量
    ]),
}));

// ✅ 正确
const inactiveColor = isDark ? '#fff' : '#000';
const activeColor = colors[500];
const animatedStyle = useAnimatedStyle(() => ({
    backgroundColor: interpolateColor(progress.value, [0, 1], [inactiveColor, activeColor]),
}));
```

---

## 三、中等问题 (P1)

### 3.1 列表渲染未使用虚拟化

| 文件 | 组件 | 数据量预估 | 建议 |
|------|------|------------|------|
| `src/features/chat/components/StreamingCardList.tsx:59-71` | StreamingCardList | 可能 > 20 卡片 | 改用 FlashList |
| `src/components/skills/ToolExecutionTimeline.tsx:681-689` | TimelineItem 列表 | 可能 > 50 步骤 | 改用 FlashList |
| `src/features/chat/components/RagReferences.tsx:220-297` | RagReferences | 可能 > 20 引用 | 改用 FlashList |

### 3.2 Slider 高频更新

| 文件 | 问题 |
|------|------|
| `src/features/settings/components/GlobalRagConfigPanel.tsx:273-279` | `onValueChange` 每次滑动都更新全局 Store |
| `src/features/settings/components/AdvancedRetrievalPanel.tsx:55-87` | 同上 |
| `src/features/settings/components/AgentRagConfigPanel.tsx:188-194` | 同上 |

**修复方案**: 使用 `onSlidingComplete` 代替 `onValueChange`，或使用本地状态 + 防抖。

### 3.3 CollapsibleSection 双重渲染

**文件**: `src/components/ui/CollapsibleSection.tsx:123-127`

**问题**: 使用绝对定位的隐藏层进行高度测量，children 被渲染两次。

### 3.4 Marquee 使用旧版 Animated API

**文件**: `src/components/ui/Marquee.tsx:17`

**问题**: 使用 React Native 内置的 `Animated` API 而非 Reanimated，性能较差。

---

## 四、轻微问题 (P2)

### 4.1 Haptics 调用不一致

部分地方正确使用了 `setTimeout(..., 10)` 延迟，部分地方直接调用：

| 文件 | 状态 |
|------|------|
| GlobalRagConfigPanel.tsx:129-135 | ✅ 正确使用延迟 |
| ProviderModelsScreen.tsx:507-511 | ❌ 直接调用 |
| ProviderModelsScreen.tsx:530 | ❌ 直接调用 |

### 4.2 搜索输入无防抖

| 文件 | 问题 |
|------|------|
| `src/features/settings/ModelPicker.tsx:275-281` | 搜索输入无防抖，模型数量多时可能卡顿 |

### 4.3 内联样式过多

| 文件 | 内联样式数量 |
|------|-------------|
| ProviderModelsScreen.tsx | 30+ 处 |
| RagReferences.tsx | 15+ 处 |
| ExecutionModeSelector.tsx | 10+ 处 |
| GlobalRagConfigPanel.tsx | 15+ 处 |

### 4.4 CachedSvgUri 使用过时 API

**文件**: `src/components/ui/CachedSvgUri.tsx:6`

```typescript
import * as FileSystem from 'expo-file-system/legacy';  // 已弃用
```

### 4.5 动画未显式清理

**文件**: `src/features/chat/components/ChatBubble.tsx:286-301`

LoadingDots 组件的动画在 `useEffect` 中启动，但没有在 cleanup 中调用 `cancelAnimation`。

---

## 五、视觉设计一致性分析

### ✅ 设计规范良好的方面

| 设计元素 | 规范 | 状态 |
|----------|------|------|
| 圆角 | 统一使用 `rounded-lg/2xl/3xl` | ✅ |
| 毛玻璃 | 使用 `Glass` 常量统一管理 | ✅ |
| 动画曲线 | 使用 `animations.ts` 统一配置 | ✅ |
| 触感反馈 | 统一通过 `haptics.ts` 包装 | ✅ |
| 弹簧配置 | Button/Card 使用相同配置 | ✅ |

### ⚠️ 需要统一的方面

| 设计元素 | 问题 |
|----------|------|
| 间距 | 部分组件使用 `p-3`，部分使用 `p-4`，缺乏统一 Token |
| 阴影 | GlassBottomSheet 和 GlassAlert 的阴影参数略有不同 |
| 边框颜色 | 部分使用 `border-indigo-500/10`，部分使用 `rgba()` 硬编码 |

---

## 六、正面发现

### 优秀实践

1. **FlashList 正确使用**:
   - ProviderModelsScreen - 模型列表
   - ModelPicker - 模型选择器
   - CoreMemoryList - 核心记忆列表

2. **React.memo 正确使用**:
   - 多个组件使用 `React.memo` 减少不必要重渲染
   - ProviderModelsScreen 的 ModelItem 使用自定义比较函数

3. **动画配置统一**:
   - `animations.ts` 集中管理动画参数
   - 弹簧配置复用

4. **主题适配完整**:
   - 所有设置界面组件完整支持浅色/深色模式
   - 品牌色动态切换

---

## 七、修复优先级排序

| 优先级 | 问题 | 影响范围 | 修复工作量 |
|--------|------|----------|------------|
| **P0** | Worklet 变量访问问题 (8处) | 动画不同步、主题切换异常 | 中 |
| **P1** | StreamingCardList 虚拟化 | 长对话性能 | 低 |
| **P1** | ToolExecutionTimeline 虚拟化 | 长时间线性能 | 低 |
| **P1** | Slider 高频更新 | 滑动卡顿 | 低 |
| **P2** | Haptics 延迟统一 | 潜在死锁风险 | 低 |
| **P2** | 搜索防抖 | 输入卡顿 | 低 |
| **P2** | 内联样式优化 | 渲染性能 | 高 |
| **P3** | Marquee API 迁移 | 动画流畅度 | 中 |
| **P3** | CachedSvgUri API 迁移 | 未来兼容性 | 低 |

---

## 八、建议修复顺序

1. **立即修复 (P0)**: 所有 Worklet 变量访问问题
2. **短期修复 (P1)**: 列表虚拟化、Slider 防抖
3. **中期优化 (P2)**: Haptics 统一、搜索防抖
4. **长期优化 (P3)**: API 迁移、样式重构
