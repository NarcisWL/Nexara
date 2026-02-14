# 重构服务商管理界面 (Refactor Service Provider Management UI)

## 目标描述
优化“服务商管理”界面，将当前自定义的“伪二级页面”（Modal 弹窗）替换为项目中标准的二级页面导航样式。此外，增强服务商列表的视觉效果，在服务商名称前添加品牌 Logo 以便于识别。

## 用户审查要求
> [!IMPORTANT]
> `ProviderModal` 组件将被**删除**。请确保应用中没有其他部分依赖此组件。（目前的分析显示它仅在 `settings.tsx` 中使用）。

## 建议变更

### 设置功能 (Settings Feature)
#### [NEW] [ProviderFormScreen.tsx](file:///home/lengz/Nexara/src/features/settings/screens/ProviderFormScreen.tsx)
- 创建一个新的屏幕组件，实现 `ProviderModal` 的逻辑，但作为全屏页面展示。
- 使用 `PageLayout` 和 `GlassHeader` 保持 UI 一致性。
- 由于是独立页面，`onSave` 操作将直接（或通过包装器）与 `useApiStore` 交互。
- 接收导航参数以编辑现有服务商（例如：`id`）。

#### [MODIFY] [ProviderList.tsx](file:///home/lengz/Nexara/src/features/settings/components/ProviderList.tsx)
- 引入 `BrandIcon` 或 `ModelIconRenderer`。
- 在 `ProviderListItem` 中，于服务商名称左侧添加品牌 Logo。
- 实现将 `provider.type` 映射到相应图标 Slug 的逻辑。

### 应用导航 (App Navigation)
#### [NEW] [app/settings/provider/form.tsx](file:///home/lengz/Nexara/app/settings/provider/form.tsx)
- 为新的 `ProviderFormScreen` 创建路由入口。

#### [MODIFY] [app/(tabs)/settings.tsx](file:///home/lengz/Nexara/app/(tabs)/settings.tsx)
- 移除 `ProviderModal` 的导入和使用。
- 将“添加服务商”按钮的 `onPress` 事件更新为 `router.push('/settings/provider/form')`。
- 更新 `ProviderList` 的 `onEdit` 属性，使其导航至 `/settings/provider/form` 并附带 `id` 参数。

### 清理 (Cleanup)
#### [DELETE] [ProviderModal.tsx](file:///home/lengz/Nexara/src/features/settings/ProviderModal.tsx)

## 验证计划

### 手动验证
1.  **视觉检查**:
    - 构建应用并导航至 设置 -> 服务商管理。
    - 验证列表中的服务商是否显示了相应的品牌 Logo（例如：OpenAI 服务商显示 OpenAI Logo）。
2.  **添加服务商**:
    - 点击“添加服务商”按钮。
    - 验证是否推入了一个新页面（标准动画），而不是打开一个 Modal 弹窗。
    - 填写新服务商的详细信息（例如：一个测试用的 OpenAI 兼容服务商）。
    - 保存并验证该服务商是否出现在列表中。
3.  **编辑服务商**:
    - 点击现有服务商上的“编辑”按钮。
    - 验证是否推入了表单页面，且字段已预填充。
    - 修改名称或 API Key。
    - 保存并验证列表中的详细信息已更新。
4.  **边界情况**:
    - 验证取消表单时的行为（应返回上一页）。
    - 验证新页面上的验证逻辑（如空名称/Key）是否仍然有效。
