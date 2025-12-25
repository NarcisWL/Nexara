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

---

## 待办事项

### 高优先级
- [ ] 实现供应商管理（AI Providers）
- [ ] 实现推理引擎配置
- [ ] 添加数据导出功能

### 中优先级
- [ ] 优化 i18n 翻译完整度
- [ ] 添加更多主题选项
- [ ] 实现通知推送

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

---

## 团队知识库

### 调试技巧
1. **触觉测试**: 逐个测试所有交互的震动反馈
2. **快速操作**: 快速连续操作暴露竞态问题
3. **低端设备**: 性能问题更容易复现

### 常见陷阱
1. ❌ 在状态变更时同步调用原生模块
2. ❌ 嵌套不必要的 View 组件
3. ❌ 忽视用户的"感觉不对"反馈

### 最佳实践
1. ✅ 所有原生调用延迟执行
2. ✅ 组件保持简洁，避免嵌套
3. ✅ 重视用户体验反馈

---

**维护者**: AI Assistant  
**最后更新**: 2025-12-26  
**版本**: 1.0
