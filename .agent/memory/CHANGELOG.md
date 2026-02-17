# Nexara Changelog

## v1.2.56 (2026-02-17)
- **Feat**: 服务商管理界面动画优化
  - ProviderFormScreen: 预设卡片入场动画 (FadeInDown + stagger)
  - ProviderFormScreen: 保存按钮点击缩放动画 + 加载状态
  - ProviderFormScreen: 输入框焦点边框颜色渐变动画
  - ProviderModelsScreen: 模型卡片入场动画 (FadeIn)
  - ProviderModelsScreen: TypeButton/CapabilityTag 颜色过渡动画
  - ProviderModelsScreen: TypeButton/CapabilityTag 添加 React.memo

## v1.2.55 (2026-02-17)
- **Fix**: ChatInput 焦点状态视觉效果修复
  - 添加 Keyboard.addListener('keyboardDidHide') 监听
  - 键盘消失时自动重置焦点状态和动画
- **Docs**: 新增服务商管理界面审计报告 (`docs/archive/provider-management-audit-2026-02-17.md`)
  - 审计范围：ProviderList、ProviderFormScreen、ProviderModelsScreen
  - 整体评分：A- (优秀，有微小改进空间)
  - 发现问题：输入框无焦点动画、保存按钮无点击动画、预设卡片无入场动画

## v1.2.54 (2026-02-17)
- **Fix**: 时间轴组件滚动穿透修复
  - 添加手势响应者拦截，防止滚动事件穿透到外层消息列表
  - 禁用 ScrollView 的 bounces 和 overScrollMode，防止边界回弹触发外层滚动
  - 添加滚动边界检测，在到达顶部/底部时正确处理滚动事件
- **Build**: 优化 APK 打包配置
  - 配置 ndk.abiFilters 只打包 arm64-v8a 架构
  - 移除 32 位 ARM (armeabi-v7a) 和 x86 模拟器库
  - 预计 APK 体积减少约 30-40%

## v1.2.32 (2026-02-17)
- **Fix**: 核心会话页面性能优化 (Phase 1 & 2)
  - **renderItem useCallback**: 会话详情页和会话列表页的 renderItem 提取为 useCallback
  - **FlatList 配置**: 会话列表页添加 getItemLayout、removeClippedSubviews、maxToRenderPerBatch 等优化配置
  - **内存泄漏修复**: ChatBubble 中 `(React as any)._aiImages` 全局变量替换为 useMemo
  - **计算优化**: reversedMessages 和 latestAssistantIndex 提取到组件顶层
  - **输入框焦点动画**: ChatInput 添加 onFocus/onBlur 焦点状态动画，边框颜色渐变 + 阴影效果
  - **列表项入场动画**: SwipeableSessionItem 添加 FadeIn.duration(200) 入场动画
  - **子组件 memo**: LoadingDots、MessageMeta、RagReferencesChip、RagReferencesList 添加 React.memo
- **Docs**: 新增核心会话页面审计报告 (`docs/archive/chat-page-audit-2026-02-17.md`)

## v1.2.31 (2026-02-17)
- **Fix**: 知识图谱系统全面修复
  - **边去重**: `createEdge` 新增去重逻辑，相同边累加权重而非重复创建
  - **并发安全**: `upsertNode` 使用 `INSERT OR IGNORE` 模式避免竞态条件
  - **节点合并**: `mergeNodes` 新增自环边和重复边清理逻辑
  - **文档更新**: `updateDocumentContent` 新增知识图谱清理和重新抽取逻辑
  - **累积器持久化**: `kgAccumulator` 添加到 Zustand persist 配置
- **Fix**: 文本编辑器大文件崩溃修复
  - **文件大小检测**: 加载时检测文件大小，显示文件大小信息
  - **大文件阈值**: 100KB 以上为大文件，500KB 以上为超大文件
  - **语法高亮限制**: 大文件自动禁用语法高亮预览，超大文件禁止切换预览模式
  - **警告横幅**: 大文件显示性能警告提示
- **Docs**: 新增知识图谱修复方案 (`docs/archive/kg-fix-plan-2026-02-17.md`)

## v1.2.30 (2026-02-17)
- **Fix**: RAG 与知识图谱数据级联删除修复
  - **会话删除**: 新增知识图谱数据清理逻辑，删除会话时同步清理 `kg_edges` 和孤立 `kg_nodes`
  - **批量删除文档**: 新增物理文件删除逻辑，批量删除文档时同步删除对应物理文件
- **Feature**: 孤立数据清理功能
  - **pruneOrphanSessions**: 扩展支持清理会话关联的孤立知识图谱数据
  - **pruneOrphanDocumentKG**: 新增方法清理已删除文档的孤立知识图谱数据
  - **设置页面**: RAG 配置面板新增「清理孤立数据」按钮，支持手动清理残留数据
- **Docs**: 
  - 新增 RAG 级联删除审计报告 (`docs/archive/rag-kg-cascade-delete-audit-2026-02-17.md`)
  - 新增知识图谱抽取机制审计报告 (`docs/archive/kg-extraction-audit-2026-02-17.md`)

## v1.2.29 (2026-02-17)
- **Performance**: 文库界面全面性能优化审计与实施
  - **PortalCards 组件**: 从内联定义提取为独立 `memo` 组件，避免每次渲染重新创建
  - **列表项动画**: `FadeIn/FadeOut` 时长从 200ms/150ms 优化为 120ms/80ms
  - **RagStatusIndicator**: 呼吸灯动画改为按需运行，空闲时自动停止降低 CPU 占用
  - **KnowledgeGraphView**: 新增 HTML 模板缓存机制，避免重复字符串生成
  - **批量操作工具栏**: 添加 `SlideInUp/SlideOutDown` 弹簧动画
- **Docs**: 新增文库界面审计报告 (`docs/archive/library-audit-2026-02-17.md`)

## v1.2.28 (2026-02-11)
- **Fix**: Markdown 预处理器全面重写 — 新增 7 条幂等正则修复"文字墙"问题 (`markdown-utils.ts`)。
- **Fix**: 标题无空格修复 (`###CJK` → `### CJK`)，适配 DeepSeek/Qwen 畸形输出。
- **Fix**: 粘连 bullet+bold 拆分 (`***text**` → `* **text**`)。
- **Fix**: 代码块保护 — 代码内 `#` 注释不再被误识为标题。
- **Fix**: `---` 分隔符不再被 `\s` 匹配换行符的 Bug 拆碎。
- **Cleanup**: 移除 `ChatBubble.tsx` 中废弃的 `formatDeepSeekOutput` 和 `parseMarkdownContent` 导入。
- **Docs**: 新增 Markdown 预处理器排障手册 (`docs/archive/markdown-preprocessing-guide.md`)。

## v1.2.27 (2026-02-07)
- **Robustness**: Enhanced Task Manager with strict cancellation protocol (`fail` action) and auto-skip logic for pending steps.
- **Protocol**: Updated System Prompt to explicitly handle user interruptions.
- **UI**: Cleaned up Task Monitor by removing confusing "Dismiss" button.
- **Build**: Successfully built Release APK v1.2.27 (Code 95).

## v1.2.25 (2026-02-06)
- **Feature**: Developed "Virtual Split Architecture" for multi-tool calling compatibility (DeepSeek/VertexAI).
- **UI**: Added visible loop counter ("De-blackboxing") on message bubbles.
- **UI**: Redesigned Context Management Panel for better information density.
- **Fix**: Resolved "Loop Limit" false positive by resetting counter on new turns.
- **Fix**: Standardized Super Assistant Settings UI with `SettingsSection` wrapper.
- **Audit**: Verified System Prompt construction logic effectiveness.
- **Build**: Successfully built release APK in isolated worktree.

## v1.2.24 (Previous)
- [Legacy updates inferred]
