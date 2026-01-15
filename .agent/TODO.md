# Nexara 项目 TODO 路线图

> **目的**: 记录待实现和待优化的功能清单，按优先级组织  
> **更新频率**: 每次迭代后更新  
> **最后更新**: 2026-01-15

---

## 优先级说明

- **P0 (Critical)**: 阻碍核心功能或严重影响用户体验，需立即处理
- **P1 (High)**: 重要功能缺失或明显体验问题，下个版本必须完成
- **P2 (Medium)**: 优化改进项，体验提升，可根据时间安排

---

## P0 - 核心功能补全

### 1. ~~ApprovalCard介入输入功能~~ ✅

**背景**:
- Steerable Agent Loop已实现审批机制，但缺少用户介入输入框
- 后端`resumeGeneration`支持`intervention`参数，UI未暴露

**目标**:
- 在`ApprovalCard`添加`TextInput`组件
- 用户可在批准时提供自定义指令（如"Only write to /tmp"）
- Timeline正确记录介入指令

**影响**:
- 完善可控Agent功能（当前评分4.7/5.0 → 5.0/5.0）
- 提升Semi-Auto模式的实用性

**预计时间**: 20分钟

**参考文档**:
- `brain/.../intervention_input_plan.md`
- `ApprovalCard.tsx`

**完成标准**:
- [x] TextInput组件集成
- [x] 批准按钮文本动态变化
- [x] Timeline记录"Human Instruction: xxx"
- [x] 暗色/亮色模式适配

---

### 2. ~~高风险工具识别优化~~ ✅

**背景**:
- 当前使用`includes`匹配，可能误判
- 高风险工具列表硬编码在逻辑中

**目标**:
- 改为严格匹配（`===`）
- 提取为配置化常量`HIGH_RISK_TOOLS`
- 补充缺失的高风险工具（如`send_command_input`）

**影响**:
- 提升Semi-Auto模式准确性
- 便于未来扩展新工具

**预计时间**: 10分钟

**位置**: `chat-store.ts` L2053-2056

**完成标准**:
- [x] `HIGH_RISK_TOOLS`常量定义
- [x] 严格匹配逻辑
- [x] 测试覆盖（write/run/replace等工具）

---

### 3. chat-store解耦重构 🔥🏗️

**背景**:
- `chat-store.ts`已超过3100行，维护困难
- 包含多个职责：消息管理、AgentLoop、RAG、工具执行、状态管理
- 单一文件过大影响开发体验和性能

**目标**:
- **拆分为模块化架构**：
  - `chat-store.ts` - 核心状态和基础操作
  - `agent-loop.ts` - AgentLoop逻辑
  - `tool-execution.ts` - 工具执行引擎
  - `message-manager.ts` - 消息CRUD操作
  - `approval-manager.ts` - 审批流程管理
  - `session-manager.ts` - 会话管理

**影响**:
- 代码可维护性提升
- 单元测试更容易
- 减少合并冲突
- 提升IDE性能

**预计时间**: 4-6小时

**关键原则**:
- 保持向后兼容
- 逐步迁移，确保功能正常
- 增加类型安全性
- 添加完整的JSDoc注释

**完成标准**:
- [ ] 设计模块拆分方案
- [ ] 创建新模块文件
- [ ] 迁移AgentLoop逻辑
- [ ] 迁移工具执行逻辑
- [ ] 更新所有导入引用
- [ ] 验证所有功能正常
- [ ] 删除冗余代码

---

## P1 - UI/UX 专项优化

### 3. 服务商管理组件性能与视觉优化 ⚡

**背景**:
- 服务商列表可能包含大量模型配置
- 当前渲染可能存在性能瓶颈
- 视觉风格需要与全局水晶态主题统一

**目标**:
- **性能优化**:
  - 实施`React.memo`和`useMemo`优化
  - 长列表使用虚拟滚动（`FlashList`）
  - 选择器隔离（避免全局状态触发重渲染）
- **视觉优化**:
  - 统一为Midnight Indigo水晶态风格
  - 添加玻璃材质（`BlurView`）
  - 卡片间距和圆角规范化
  - 暗色模式深度对比度优化

**影响**:
- 提升设置页面流畅度
- 视觉一致性达到5/5

**预计时间**: 2-3小时

**关键文件**:
- `app/(tabs)/settings/providers.tsx`（如果存在）
- `src/components/settings/ProviderCard.tsx`（如果存在）

**完成标准**:
- [ ] 60fps流畅滚动（测试100+模型）
- [ ] 视觉符合水晶态规范
- [ ] 暗色/亮色模式无闪烁

---

### 4. 会话界面长历史滑动抖动修复 ⚡

**背景**:
- 长会话（1000+消息）滚动时可能出现抖动
- `FlashList`配置可能需要调优
- ChatBubble的`React.memo`比较函数可能过于严格

**目标**:
- **抖动修复**:
  - 优化`FlashList`的`estimatedItemSize`
  - 检查`onLayout`回调是否触发过多重渲染
  - 避免在滚动时更新无关状态
- **性能优化**:
  - ChatBubble的`arePropsEqual`精简比较字段
  - Markdown渲染缓存优化
  - 图片懒加载策略调整

**影响**:
- 长会话体验丝滑
- 减少内存占用

**预计时间**: 1.5-2小时

**关键文件**:
- `app/chat/[id].tsx`
- `src/features/chat/components/ChatBubble.tsx` (L1276-1318: React.memo)

**完成标准**:
- [ ] 2000消息会话无抖动
- [ ] 滚动FPS ≥ 55
- [ ] 内存占用合理（< 300MB）

---

### 5. 助手/会话列表手势颗粒度补全 ✋

**背景**:
- 当前手势交互可能不够细腻
- 缺少标准的侧滑删除、长按菜单
- 触感反馈可能不完整

**目标**:
- **会话列表** (`AgentList`):
  - 侧滑删除（Swipeable）
  - 长按弹出ContextMenu（重命名/归档/删除）
  - 触感反馈（Light/Medium/Heavy）
- **助手列表**:
  - 侧滑显示快捷操作（设置/删除）
  - 长按预览助手详情
  - 拖拽排序（可选）

**影响**:
- 操作效率提升30%
- 触感反馈一致性

**预计时间**: 2小时

**关键文件**:
- `src/features/chat/components/AgentList.tsx`
- `src/components/ui/SwipeableRow.tsx`（可能需要创建）

**完成标准**:
- [ ] 侧滑流畅（无卡顿）
- [ ] 长按菜单完整
- [ ] 触感反馈符合Rule 8（延迟10ms）

---

## P2 - 架构与扩展性

### 6. LLM抽象层文档完善 📖

**背景**:
- Rule 14规定了抽象层架构，但缺少详细使用文档
- 新增Provider时可能不清楚具体步骤

**目标**:
- 补充`.agent/docs/llm-abstraction-layer-guide.md`
- 添加"新增Provider"完整示例
- 提供常见问题FAQ

**预计时间**: 1小时

**完成标准**:
- [ ] 新手可独立添加Provider
- [ ] 示例代码可运行

---

### 7. RAG检索策略优化 🔍

**背景**:
- 当前使用固定Top-K策略
- 可能返回无关分块污染上下文

**目标**:
- 实施动态Top-K（根据相似度阈值）
- 添加重排序（Reranker）后的二次过滤
- 支持用户自定义检索参数

**预计时间**: 2-3小时

**完成标准**:
- [ ] 召回精度提升20%
- [ ] 上下文污染率降低

---

### 8. 自动化测试覆盖 🧪

**背景**:
- 当前无自动化测试
- 重构时容易引入回归bug

**目标**:
- 核心功能单元测试（vitest）
- 关键UI集成测试（Detox）
- CI/CD集成

**预计时间**: 4-6小时

**优先测试模块**:
- [ ] chat-store核心逻辑
- [ ] 虚拟拆分函数
- [ ] ApprovalCard交互

---

## P2 - 用户体验细节

### 9. 批量工具审批UX优化 💡

**背景**:
- 多个工具一起暂停时，只显示逗号分隔的名称
- 无法单独批准/拒绝

**目标**:
- 逐个显示工具详情
- 支持单独批准/拒绝
- 添加"全部批准"快捷按钮

**预计时间**: 1.5小时

---

### 10. 审批超时机制 ⏱️

**背景**:
- 当前无超时，可能无限等待

**目标**:
- 30分钟无操作自动拒绝
- 提前5分钟提醒用户

**预计时间**: 1小时

---

## 已完成功能 ✅

### v4.7 - Virtual Split Architecture
- ✅ 虚拟拆分核心逻辑
- ✅ 字段继承（reasoning/thought_signature）
- ✅ 历史累积策略
- ✅ XHR连接泄漏修复
- ✅ 测试覆盖（6+ providers）

### v4.8 - Steerable Agent Loop (部分完成)
- ✅ 三档执行模式（Auto/Semi/Manual）
- ✅ ExecutionModeSelector UI
- ✅ ApprovalCard UI（基础版）
- ✅ 审批/拒绝逻辑
- ✅ Timeline集成
- ⚠️ **待补充**: 介入输入框

---

## 长期愿景 🚀

### 未来规划
1. **多模态支持**: 语音输入/输出、图像理解
2. **协作功能**: 多用户共享会话
3. **插件系统**: 社区贡献工具/技能
4. **离线模式**: 本地模型支持（LLaMA/Mistral）
5. **性能监控**: 实时性能分析面板

---

## 更新日志

### 2026-01-15
- 创建TODO路线图文档
- 添加P0-P2优先级分类
- 补充5个核心待办项（ApprovalCard输入、服务商优化、会话性能等）

---

**文档版本**: 1.0  
**维护者**: AI Assistant + Project Team  
**下次审查**: 每周一

---

## P0 - 新增核心修复 (2026-01-15)

### 11. 虚拟拆分架构修复 - 总结在新气泡问题 🚨

**背景**:
- 审批批准后，AI总结出现在新气泡，违反单气泡设计
- `resumeGeneration` (L704) 调用 `generateMessage(sessionId, '')`创建新用户消息

**目标**:
- 重构`resumeGeneration`，不调用`generateMessage`
- 创建`continueGenerationInSameBubble`方法，在同一气泡内追加总结
- 审批通过后直接在当前assistant消息context下继续

**影响**:
- 修复虚拟拆分核心设计缺陷
- 符合"单气泡内完成所有任务"原则

**预计时间**: 1.5小时

**参考文档**:
- `brain/.../virtual_split_fix_plan.md`

**完成标准**:
- [/] 重构resumeGeneration逻辑
- [ ] 创建continueGenerationInSameBubble方法
- [ ] 测试验证：总结在同一气泡内

---

### 12. 文件系统路径统一 🔧

**背景**:
- 文件系统工具路径 `agent_sandbox/workspace/`
- "文库文档"界面workspace路径可能不一致
- 文件写入后在文库界面看不到

**目标**:
- 调查`rag-store.ts`的`SANDBOX_ROOT`配置
- 统一文件系统工具和RAG workspace路径
- 确保文件可在文库文档界面访问

**预计时间**: 1小时

**完成标准**:
- [ ] 路径统一
- [ ] 文件写入后在文库文档可见

---

## P2 - UI细节优化 (New)

### 13. Timeline审批UI视觉增强 ✨

**背景**:
- Timeline审批框已实现无边界羽化边缘风格
- 但视觉效果仍有提升空间

**目标**:
- 调整工具详情卡片背景色深度
- 优化介入输入框阴影和深度效果
- 增强按钮触觉反馈视觉
- 添加展开/收起动画
- 微调元素间距

**预计时间**: 1小时

**完成标准**:
- [ ] 视觉层次更清晰
- [ ] 动画流畅自然

---

## 📝 CHANGELOG

### 2026-01-15 (下午 - 虚拟拆分完整修复)
- ✅ 完成虚拟拆分架构3处关键修复（P0级）
  - 跳过用户消息创建（L777-780）
  - contextMsgs过滤优化（L1239-1246）
  - assistant消息复用逻辑（L789-797）
- ✅ 修复DeepSeek审批循环问题（resumption时跳过审批检测）
- ✅ 实现thinking步骤保存到Timeline（过程文本不泄露到正文区）
- ✅ 修复会话卡死bug（AgentLoop结束后重置loopStatus为idle）
- ✅ TypeScript类型修复（添加idle状态到loopStatus）
- ➕ 新增P0任务：chat-store解耦重构（3100+行→6个模块）
- ➕ 新增P2任务：执行模式选择器UI位置优化
- ➕ 新增P2任务：Embedding & Rerank模型本地化

### 2026-01-15 (上午)
- ✅ 完成ApprovalCard介入输入功能（P0-1）
- ✅ 完成高风险工具识别优化（P0-2）
- ➕ 新增P0任务：虚拟拆分架构修复
- ➕ 新增P2任务：Timeline审批UI视觉优化

### 2026-01-14
- ➕ 新增P1任务：文件路径统一化（agent写入文件在文库中可见）
- ➕ 新增P1任务：Flash模型输出优化
- ➕ 新增P1任务：RAG指示器完整验证

### Previous
- ✅ 完成Virtual Split架构（`key`驱动Navigator重挂载）
- ✅ 完成批量创建聊天会话功能（P1-1）
- ✅ 完成服务商管理性能优化（P1-3）

---
