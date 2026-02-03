# Nexara Project Dashboard

> **单一事实来源 (SSOT)**: 
> 本文档仅作为仪表盘。具体实施细节请查阅 [.agent/docs/todos](docs/todos) 下的方案文档。

---

## 🚀 进行中 (Active Tasks)

| ID | 任务名称 | 优先级 | 对应方案文档 | 当前进度 |
| :--- | :--- | :--- | :--- | :--- |
| **002** | **RAG 异步体验优化** | 🔴 High | [new_01_rag_async_optimization_plan.md](docs/todos/new_01_rag_async_optimization_plan.md) | **Step 1 Done** (Param Tuning). Pending Phase 2 (Prefetch). |
| **---** | **工具链稳定性** | 🟡 Medium | N/A (Backlog) | 待修复 `build.gradle` patch 脚本问题。 |

---

## ✅ 最近完成 (Recently Completed)

| ID | 任务名称 | 完成时间 | 实施方案 (Archive) | 验证结论 |
| :--- | :--- | :--- | :--- | :--- |
| **001** | **MCP SSE 传输支持** | 2026-02-03 | [001_mcp_sse_transport_plan_done.md](docs/archive/001_mcp_sse_transport_plan_done.md) | 单元测试通过 (4 pass)。代码已合入主线。 |

---

## 🧊 待办规划 (Backlog)

### High Priority
| ID | 任务名称 | 方案草稿 |
| :--- | :--- | :--- |
| **003** | **全局动画升级** | [003_global_animation_upgrade.md](docs/todos/003_global_animation_upgrade.md) |
| **004** | **应用冷启动优化** | [004_app_cold_start_optimization.md](docs/todos/004_app_cold_start_optimization.md) |

### Medium Priority
| ID | 任务名称 | 方案草稿 |
| :--- | :--- | :--- |
| **005** | **多模态 RAG** | [005_multimodal_rag.md](docs/todos/005_multimodal_rag.md) |
| **006** | **性能监控** | [006_performance_monitoring.md](docs/todos/006_performance_monitoring.md) |

---

## 🗄️ 归档历史
> 2026-02-03 以前的历史任务请查看 Git Log 或旧版归档。
- [x] **本地模型落地**: `llama.rn` 集成 (v1.2.0)
- [x] **Markdown 渲染引擎重构**: Webview 迁移 (v.1.1.0)
- [x] **v1 动画策略**: [000_animation_v1_strategy.md](docs/archive/000_animation_v1_strategy.md)
