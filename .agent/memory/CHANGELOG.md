# Nexara Changelog

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
