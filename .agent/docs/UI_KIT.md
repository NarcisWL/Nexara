# Nexara UI 组件库与设计规范

> **风格**: Lumina 美学 (毛玻璃效果 + 极简主义)
> **技术栈**: NativeWind (Tailwind) + Reanimated
> **法则**: 所有新 UI 必须使用这些原子组件，严禁重复造轮子。

---

## 1. 字体系统 (`<Typography />`)

基于 React Native `<Text>` 的封装，提供标准化的变体 (Variants)。

| 变体 | Tailwind 类名 | 使用场景 |
| :--- | :--- | :--- |
| `largeTitle` | `text-3xl font-bold` | 页面大标题 (首页) |
| `h1` | `text-2xl font-bold` | 板块标题 |
| `h2` | `text-xl font-semibold` | 卡片标题 |
| `h3` | `text-lg font-semibold` | 小标题 |
| `body` | `text-base` | 标准正文 |
| `caption` | `text-xs text-text-secondary` | 元数据、时间戳 |
| `sectionHeader` | `text-sm font-bold uppercase` | 列表分组标题 |

**常用属性**: `color` ('primary', 'secondary', 'brand', 'danger')

---

## 2. 核心原子组件 (Atoms)

### 2.1 按钮 (`<Button />`)
*内置 10ms 延迟以确保原生桥接 (Native Bridge) 安全。*

- **变体**:
    - `primary`: 品牌主色 (Indigo 600)
    - `secondary`: 灰色表面 + 边框
    - `ghost`: 透明背景，仅文字
    - `danger`: 红色警示
- **属性**: `loading` (加载动画), `icon` (ReactNode), `size` (sm/md/lg)

### 2.2 卡片 (`<Card />`)
- **变体**:
    - `default`: 实色表面 (`bg-surface-primary`)
    - `glass`: 毛玻璃层 (BlurView) + 半透明边框
    - `elevated`: 带阴影 (仅限亮色模式)
- **特性**: 默认 20px 圆角 (黄金标准)。

---

## 3. 导航组件 (Navigation)

### 3.1 毛玻璃顶栏 (`<GlassHeader />`)
*透明/毛玻璃风格的页面标题栏。*

- **属性**:
    - `title`, `subtitle`
    - `leftAction`, `rightAction` (`{ icon, onPress }`)
    - `intensity`: 模糊强度 (默认 100)
- **行为**: 绝对定位，自动处理安全区域 (SafeArea) 边距。

---

## 4. 复杂分子组件 (Molecules)

### 4.1 折叠板块 (`<CollapsibleSection />`)
*具备平滑动画的手风琴组件。*
- **特性**: 
    - 自动测量内容高度
    - 箭头旋转动画
    - 切换时的触感反馈 (Haptic)

---

## 5. 设计令牌 (Tailwind 配置)

- **核心颜色**:
    - `primary`: Indigo (#6366f1)
    - `surface-primary`: `bg-white` (亮色) / `bg-black` (暗色)
    - `surface-secondary`: `bg-gray-50` / `bg-zinc-900`
- **圆角规范**:
    - `xl`: 12px (内部原子)
    - `2xl`: 16px (容器)
    - `3xl`: 24px (卡片/弹窗)
 Greenland
