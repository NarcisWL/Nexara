# Reliblefs

## Instructions

Add your skill instructions here.

---
name: reliblefs
description: ---
name: reliable-fs
description: 增强文件写入和编辑的可靠性，防止 apply_diff 失败或 write_to_file 截断
---

# 可靠文件操作指令

## 核心原则
1. **先读后改**：在调用 `apply_diff` 之前，必须先使用 `read_file` 获取文件的最新完整内容，确保上下文中的行号和内容与物理文件 100% 匹配。
2. **拒绝盲写**：禁止在未确认目标位置的情况下直接使用 `apply_diff`。如果相似度匹配失败，必须重新读取文件并缩小 diff 范围。
3. **大文件保护**：对于超过 300 行的文件，严禁使用 `write_to_file`（防止输出截断），必须强制使用多次小范围的 `apply_diff`。
4. **路径校验**：所有路径必须使用相对路径（相对于工程根目录），严禁使用以 `/` 开头的绝对路径。

## 错误恢复流程
- 如果写入失败报错 "No sufficiently similar match found"：
  - 立即重新执行 `read_file`。
  - 检查代码缩进，确保 diff 块包含足够的上下文行（至少前后各 3 行）。
  - 重新尝试 `apply_diff`。
- 如果报错 "EACCES" 或权限问题：
  - 检查文件是否被其他进程占用。
  - 尝试先创建父级目录（如果目录不存在）。
---

