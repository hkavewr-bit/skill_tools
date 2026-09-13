---
name: project-delivery-workflow
description: Use when a user wants to develop or resume a backend, Agent, or full-stack project through visual requirements alignment, module-level approvals and function-level specifications, or continue a project governed by this workflow. 适用于项目全流程开发、可视化需求对齐、模块 Spec 和交付；不用于普通知识问答或无关的一次性小修复。
metadata:
  version: "1.0.0"
---

# 项目交付工作流

帮助用户先理解业务与设计，再按可运行功能逐步验收。当前交付版本的全部模块 Spec 确认且跨模块检查通过后，才开发业务代码；之前仅建立已确认的基础骨架。

## 启动与恢复

先读 [协作与技能接入](references/orchestration.md) 和 [状态与文档维护](references/state-and-docs.md)。识别项目、当前版本范围、是否有前端、是否包含 Agent，以及本次是完整交付、恢复还是小改动。

新项目从业务流程开始，在项目根目录建立 `user_workspace/`；已有项目先核对仓库说明、实际文件和进度，复用现有成果，不覆盖用户修改。目录是产物目录，不是第二个代码仓库。

每个阶段只读取对应参考，不一次加载全部技能。用户已有明确确认的范围和选择直接沿用；技能不授予额外权限。目录或流程有用户新指示时，记录变更后采用新指示。

## 阶段与交接

| 阶段 | 所需参考 | 进入下一阶段的条件 |
|---|---|---|
| 业务探索与流程图 | [需求对齐](references/discovery.md) | 用户确认可视化业务流程 |
| 详细 PRD | [需求对齐](references/discovery.md) | 按业务模块对齐后，用户确认当前版本 PRD |
| 前端排版（有前端） | [前端设计](references/frontend.md) | pen.dev 页面排版获得确认 |
| 前端交互（有前端） | [前端设计](references/frontend.md) | 交互、状态、权限、数据需求确认，PRD 已同步 |
| 总体架构 | [架构与 Spec](references/architecture.md) | 模块边界、依赖、接口与总体架构图确认 |
| 基础骨架 | [架构与 Spec](references/architecture.md) | 目录、配置框架及基础运行验证有证据 |
| 模块 Spec | [架构与 Spec](references/architecture.md) | 当前版本所有技术模块的函数级 Spec 分别确认 |
| 跨模块检查与任务拆分 | [架构与 Spec](references/architecture.md) | 契约及完整链路检查通过，形成可运行功能清单 |
| 小步开发、测试、验收 | [实施与验证](references/implementation.md) | 每个业务切片有测试结果、文档同步及用户验收 |
| 整体验收 | [实施与验证](references/implementation.md) | 当前版本需求全部映射到验收结果；Agent 另读 [效果评测](references/agent-evaluation.md) |
| 部署资料交付 | [交付](references/delivery.md) | 文件、说明及不启动服务的配置检查完成，明确部署未执行 |

## 每轮工作契约

1. 根据 `user_workspace/项目进度.md` 确认当前阶段、版本、范围和下一步。
2. 提供当前阶段允许的具体成果；有待确认事项时，展示可审阅的版本和一个聚焦问题。
3. 更新实际受影响的 PRD、总体设计、流程图、模块 Spec、测试报告和进度。
4. 交付说明：完成了什么、什么证据、文档改动、剩余问题、是否需要用户确认。

确认针对具体版本；不把沉默、文件存在或助手总结当批准。已确认且未变化的内容无需重复问。模块规则、异常、边界、验收清楚且关键依赖已解决，就收敛追问并提交确认。

## 关键分支

- 纯后端/纯 Agent：跳过前端排版和交互阶段，仍对齐业务流程与总体架构。
- 恢复：核对确认版本及实际文件；从第一个未满足交接条件的位置继续，不重跑整个流程。
- 小修复：只走影响分析、受影响的 Spec/需求同步、实现和相关回归，不要求重做无关 PRD 或前端。
- 技术未知：先说明验证问题、范围、成本和副作用；获准后做隔离的小范围探测，其结果回写设计，不自动升级为业务实现。
- 用户明确改变已批准流程或范围：记录替代的决定和影响，按新授权执行。仅催促进度不等于改变交接条件。
- 工具缺失：说明影响，只继续不依赖该工具的工作；用户指定的可视化/pen.dev 不可用时，不默默当成已交付。

## 常见偏差

| 容易误判的情况 | 本流程的处理 |
|---|---|
| 一个模块 Spec 已确认，急着演示 | 展示已确认设计及骨架进度，继续补齐本版本其余 Spec；不能以模块独立为由提前写业务代码 |
| “继续”遇到文档版本不一致 | 先核对差异与确认记录，不能给新版本套用旧批准 |
| 页面之外没有需求入口 | 由 PRD 额外检查后台任务、权限、Agent 恢复等能力 |
| 部署文件需要“测试” | 只检查配置，不启动 Compose、不运行部署初始化、不声明实际部署成功 |

文档模板、命名与使用时机见 [状态与文档维护](references/state-and-docs.md)。本 Skill 的交付范围不包含自动提交、推送、发布或安装社区依赖；这些动作按实际用户授权执行。
