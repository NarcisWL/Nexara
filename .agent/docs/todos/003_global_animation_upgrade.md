# 全局动画与交互升级 (v2)

**状态**: ⚪ Draft
**优先级**: High

## 目标
实现类原生的转场体验，消除 React Native 默认导航的生硬感。

## 初步规划
1. **Hero Animations**: 在列表页到详情页的跳转中实现共享元素过渡。
2. **Shared Element**: 调研 `react-native-shared-element` 或 Expo Router v4 的原生支持。
3. **Gesture**: 优化全屏手势返回的物理反馈。

## 待调研
- Android 上的 Shared Element 兼容性。
- 复杂列表项 (如带有 Image 的 Card) 的性能开销。
