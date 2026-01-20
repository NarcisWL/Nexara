# 全局页面切换动画升级方案

## 目标描述
根据 `animation_specs.md.resolved` 规范，将项目中的所有页面切换动画统一为“类 Native”风格。
重点在于 **Tab 切换动画** 需要支持基于索引的左右滑动（前进/后退语义），而这是标准 Expo Router Tabs 组件默认不支持的。

## User Review Required
> [!WARNING]
> **架构变更**: `app/(tabs)/_layout.tsx` 将不再使用 Expo Router 的标准 `<Tabs>` 组件，而是替换为自定义 Layout。
> 这意味着 Tab Bar 的外表现行需要手动维护，不再自动继承 Expo Router 的配置。我们将复用现有样式，但在未来添加新 Tab 时需手动更新 `CustomTabBar`。

> [!IMPORTANT]
> **Native Bridge Delay**: 新的 Tab Bar 交互将严格遵循 10ms 延迟原则，以防止此时触发的 Haptics 或 Navigation 与渲染线程竞争。

## Proposed Changes

### App Root
#### [MODIFY] [app/_layout.tsx](file:///home/lengz/Nexara/app/_layout.tsx)
- 调整 `Stack.screenOptions`:
    - `animationDuration`: 200ms -> 300ms (符合规范)

### Tabs Layout Refactor
#### [NEW] [src/components/navigation/CustomTabsLayout.tsx](file:///home/lengz/Nexara/src/components/navigation/CustomTabsLayout.tsx)
- 实现自定义 Tab 容器组件：
    - 管理 `activeTab` 状态与 `direction` ('forward' | 'backward')。
    - 使用 `Animated.View` 包裹 `<Slot />` (Key-Based Transition)。
    - 根据 Tab 索引顺序 (`tabOrder`) 动态计算动画方向。
    - 引入 `react-native-reanimated` 的 `FadeInRight`/`FadeInLeft` 等预设动画。

#### [NEW] [src/components/navigation/CustomTabBar.tsx](file:///home/lengz/Nexara/src/components/navigation/CustomTabBar.tsx)
- 实现自定义底部导航栏：
    - 复用现有的 Lucide 图标与 Theme 颜色逻辑。
    - 实现 Tab 点击事件处理：
        - 触发 Haptics (带 10ms 延迟)。
        - 执行路由跳转 (`router.push`).
    - 适配 iPhone 底部安全区域。

#### [MODIFY] [app/(tabs)/_layout.tsx](file:///home/lengz/Nexara/app/(tabs)/_layout.tsx)
- **完全重写**:
    - 移除 `<Tabs>` 及其子 `<Tabs.Screen>` 配置。
    - 引入 `<CustomTabsLayout>`。
    - 定义 Tab 路由映射 (path -> accessible routes).

## Verification Plan

### Automated Tests
- 无（UI 动画难以通过单元测试验证）。建议通过 `Verify` 阶段的截图/录屏验证。

### Manual Verification
1. **Stack 导航测试**:
    - 进入 `app/settings/skills`，观察进入和返回动画是否流畅 (Slide Right/Left)。
2. **Tab 切换测试**:
    - 从 `Chat` (Tab 0) 点击 `Library` (Tab 1) -> 预期：内容从右侧滑入 (Forward)。
    - 从 `Library` (Tab 1) 点击 `Chat` (Tab 0) -> 预期：内容从左侧滑入 (Backward)。
    - 快速连续切换 Tab，验证无闪烁或内容错乱。
3. **原生桥接测试**:
    - 点击 Tab 时是否有震动反馈。
    - 确认震动与页面切换无明显卡顿 (10ms 延迟生效)。
