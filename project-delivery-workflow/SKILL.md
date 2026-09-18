---
name: project-delivery-workflow
description: Use when developing or resuming a backend, Agent, or full-stack project with staged design approval, module Specs, and incremental acceptance. 适用于项目全流程交付及按本流程维护的项目；不用于普通知识问答或无关小修复。
metadata:
  version: "1.6.0"
---

# 项目交付工作流

## 开始与读取

核实项目、当前交付版本、前端/Agent 适用性和本次授权。新项目从业务流程开始；已有项目先读项目说明及 `user_workspace/项目进度.md`，核对相关实际文件，从首个未满足的阶段继续。复用未变化的确认，不覆盖用户修改。

每次只读当前阶段参考及相关项目资料；模板仅在创建/调整对应文档时读。当前上下文已有且未变化的内容不重复读取；恢复丢失上下文时补读所需部分。编码只带入当前切片 Spec、依赖契约和必要代码；全量契约核对留在跨模块检查阶段。

## 全程规则

1. 用户新指示优先于本流程约定；沿用已有授权并记录变化。确认须对应具体版本和用户明确依据，文件存在、测试通过、沉默均不等于确认。只重新确认语义变化影响的范围。
2. 业务编码前：本版本 PRD、实体、适用前端设计、总体架构及**全部技术模块 Spec** 已确认，骨架验证和跨模块检查通过。此前仅建立已确认架构的基础骨架；技术探测见架构参考。未来版本不计入本次门槛。
3. 实施保持 **WIP=1**：一次推进一个完整可执行功能流（业务切片），流程验证、文档同步及当前授权交付完成后可继续，不逐切片等待人工验收。模块开发完成后按该模块 Spec 集中测试并提交用户验收；阶段检查复用有效结果。不要求内部函数单独测试或审批。
4. 文档位于 `user_workspace/`，进度是状态与确认的唯一入口；变更同步受影响的需求、设计、Spec、测试和使用说明。交付方式与执行边界统一见[交付](references/delivery.md)，本流程默认只生成部署资料。

## 阶段路由

按顺序推进，已满足的步骤直接复用。每阶段完成条件在对应参考中。

| 阶段 | 读取 |
|---|---|
| 业务流程 → 详细 PRD | [需求](references/discovery.md) |
| 核心逻辑实体 | [实体](references/data-entities.md) |
| 概要方向（复杂项目保留；简单项目并入架构定稿） | [架构](references/architecture.md) |
| 前端低保真排版 → 交互 → 设计规范（无前端跳过） | [前端](references/frontend.md) |
| 总体架构定稿 → 基础骨架 | [架构](references/architecture.md) |
| 全部模块 Spec → 跨模块检查 → 切片拆分 | [Spec](references/spec.md) |
| 功能流开发/验证 → 模块测试验收 → 阶段检查 → 整体验收 | [实施](references/implementation.md) |
| 部署资料检查 → 文档同步 → 交付 → 最终清单 | [交付](references/delivery.md) |

仅在对应条件出现时追加读取：

- 初始化文档、恢复不一致或发生变更：[状态与文档](references/state-and-docs.md)。
- 绘图、设计工具、需求追问或其他专业能力接入：[工具协作](references/tools.md)。
- Agent/RAG 的需求、设计或效果验证：[Agent 评测](references/agent-evaluation.md)。

本流程项目的小修复仅走影响判断、受影响契约更新、实现和相关回归；不重开无关阶段。收尾报告当前成果、证据、未完成项和下一步；需要确认时，先提供可审阅版本，再聚焦影响下一步的未决点。
