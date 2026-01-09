# 项目结构与组件架构 (Project Structure & Component Architecture)

> **上次更新**: 2026-01-10
> **版本**: v4.0
> **用途**: 供 AI 快速索引项目结构、核心组件与业务逻辑分布。

## 1. 核心目录全景 (Directory Overview)

```
/home/lengz/Nexara/
├── app/                  # Expo Router 页面路由 (基于文件系统)
│   ├── (tabs)/           # 一级 Tab 页面 (Chat, RAG, Settings)
│   ├── chat/             # 聊天详情与 Agent 配置页
│   ├── settings/         # 二级设置页 (Theme, RAG Config, Token Usage)
│   ├── rag/              # 知识库文件夹详情页
│   └── visual-demo.tsx   # 视觉组件演示页 (Playground)
├── plugins/              # Expo Config Plugins (签名注入、宽色域配置等)
├── scripts/              # 构建与版本维护脚本
├── src/
│   ├── components/       # 业务组件库
│   │   ├── ui/           # 基础原子组件 (Standard UI System)
│   │   ├── chat/         # 聊天模块业务组件
│   │   ├── rag/          # RAG 模块业务组件 (Breadcrumbs, KG View)
│   │   └── icons/        # 品牌与模型图标渲染器
│   ├── features/         # 复杂业务逻辑与专属面板
│   │   ├── chat/         # 消息气泡、Token 统计、推理面板
│   │   └── settings/     # 备份恢复、多供应商管理、RAG 设置面板
│   ├── lib/              # 核心基础设施 (LLM, RAG Engine, I18n)
│   ├── store/            # Zustand 状态管理 (Persist & Secure)
│   └── theme/            # 动态主题系统 (ThemeProvider)
└── .agent/               # 项目记忆、工作流与架构指南
```

## 2. 组件库清单 (Component Inventory)

### 2.1 基础 UI 系统 (`src/components/ui`)
*标准原子组件，遵循动态主题与 HDR 动效规范。*

| 组件名 | 用途 | 关键特性 |
| :--- | :--- | :--- |
| **`PageLayout`** | 统一页面容器 | 处理安全区域、背景色与 FlashList 适配 |
| **`Typography`** | 跨平台文本渲染 | 支持多种 Variant (h1-h4, body, mono, label) |
| **`Button`** | 标准按钮 | 支持动态主题色、Ghost 模式与加载状态 |
| **`GlassHeader`** | **二级页面标准头** | 毛玻璃效果，集成返回逻辑与右侧操作 |
| **`LargeTitleHeader`** | **一级页面大标题** | 适配 iOS 风格大标题，支持自定义右侧入口 |
| **`ConfirmDialog`** | 操作确认弹窗 | 标准化危险操作提醒 |
| **`Toast`** | 全局消息提示 | 基于 `useToast` Hook 的指令式触发 |
| **`FloatingTextEditor`** | **全屏文本编辑器** | 处理键盘动态避让，用于 Prompt 深度编辑 |
| **`RainbowSlider`** | **彩虹色调色盘** | 线性色彩选择滑块 |
| **`ParticleEnergyGlow`** | **HDR 能量球** | Skia 驱动的高动态范围粒子动效 |
| **`GlassBottomSheet`** | 通用半屏浮窗 | 适配手势关闭与毛玻璃质感 |
| **`ColorPickerPanel`** | 调色盘容器 | 支持十六进制输入与色块选择 |
| **`Switch` / `Slider`** | 基础表单控件 | 全量适配 `accentColor` 动态色彩 |

### 2.2 聊天与 AI 交互 (`src/components/chat` & `src/features/chat`)
| 组件名 | 路径 | 用途 |
| :--- | :--- | :--- |
| **`ChatBubble`** | `features/chat` | **消息核心** (Markdown, LaTeX, SVG, 代码高亮, 文献索引) |
| **`ChatInput`** | `features/chat` | 增强输入框，支持多行自适应与附件预览 |
| **`SuperAssistantFAB`** | `components/chat` | 超级助手悬浮入口，集成 HDR 粒子背景 |
| **`InferencePresets`** | `components/chat` | 快速推理模板选择器 |
| **`TokenStatsModal`** | `features/chat` | Token 消耗详情与可视化分析 |
| **`SwipeableAgentItem`** | `components/chat` | 支持侧滑操作的智能体列表项 |
| **`AgentAvatar`** | `components/chat` | 具备回退机制的圆形头像组件 |

### 2.3 知识库与 RAG 模块 (`src/components/rag`)
| 组件名 | 用途 |
| :--- | :--- |
| **`KnowledgeGraphView`** | **知识图谱可视化** (WebView + vis-network，支持 crud) |
| **`RagDocItem`** | 文档/文件列表项渲染 |
| **`FolderItem`** | 知识库目录项，支持聚合显示 |
| **`ControlBar`** | RAG 管理工具条 (全选、排序、筛选) |
| **`TagManagerSheet`** | 全量标签库管理界面 |
| **`TagAssignmentSheet`** | 快速为文档打标签的底部面板 |
| **`Breadcrumbs`** | 目录导航面包屑 |
| **`PdfExtractor`** | PDF 解析与元数据提取进度展示 |

### 2.4 设置与管理 (`src/features/settings`)
| 组件名 | 用途 |
| :--- | :--- |
| **`GlobalRagConfigPanel`** | 站点级 RAG 参数 (分片、检索模型、摘要阈值) |
| **`AgentRagConfigPanel`** | 为特定 Agent 覆盖全局配置的专用面板 |
| **`ProviderModal`** | **多供应商管理** (OpenAI, Gemini, VertexAI 等) |
| **`ModelPicker`** | 智能模型选择器，支持能力图标 (Reasoning, Vision) |
| **`BackupSettings`** | 数据库/配置的导出与本地文件恢复 |

## 3. 架构准则与废弃说明

### 3.1 核心准则
- **原子化**: 严禁在页面 (app/) 中直接书写复杂样式，必须沉淀至 `src/components/ui`。
- **动态色彩**: 所有颜色必须通过 `useTheme().colors` 获取，严禁使用 `#6366f1` 等硬编码十六进制。
- **国际化**: UI 字符串必须通过 `useI18n().t` 调用，严禁使用硬编码汉字。

### 3.2 已废弃 (Zombie / Deprecated)
- ❌ `src/components/chat/AgentCard.tsx` (由 `SwipeableAgentItem` 替代)
- ❌ `src/components/rag/RagFolderCard.tsx` (由 `FolderItem` 替代)
- ❌ `src/components/rag/RagSettingsPanel.tsx` (由 `GlobalRagConfigPanel` 替代)
- ❌ `src/features/settings/ProviderSettings.tsx` (由 `ProviderModal` 替代)
- ❌ `src/components/demo/ParticleGlow.tsx` (由 `ui/ParticleEnergyGlow.tsx` 替代)

---
**维护准则**: 每次重大功能演进后，须由 Agent 更新此架构图谱，确保全栈索引的一致性。

---
**维护准则**: 新增组件或重构核心逻辑后，请同步更新此文档。
