---
name: python-rag-coding-standards
description: Use only when the user explicitly invokes $python-rag-coding-standards or requests these extracted coding standards for Python backend, FastAPI, LangGraph, or RAG code, implementation Specs, and code reviews. 来源于 0331kb，适用于目录组织、命名、中文注释、方法拆分、状态契约及日志规范。
---

# Python / RAG 代码规范

从 `0331kb` 提炼的可复用编码约定。先继承目标项目约束，再应用适用规则。仅手动调用；本 skill 不启动项目交付流程，也不把代码审查请求变成修改授权。

## 使用步骤

1. 读取目标项目说明、相关代码和依赖清单，确定 Python 版本、框架、已有规范和本次范围。版本能从文件确定时直接采用，不重复询问。
2. 始终读取 [编码规则](references/coding-rules.md)。涉及工作流、检索、导入、任务或流式接口时追加读取 [RAG 与工作流规则](references/rag-workflow.md)。需要解释来源或遇到原项目不同写法时读取 [源码依据与取舍](references/source-evidence.md)。
3. 按实际调用链确定修改点：入口 → 编排 → 业务逻辑 → 外部服务。明确输入、返回和消费者，沿用真实符号；新增名字采用下述约定。
4. 生成代码时把复杂过程拆为可读步骤；生成 Spec 时将规则落实为具体文件、方法、字段和验收条目；审查时给出具体位置、影响与建议。
5. 使用目标项目已有的检查工具验证本次改动。报告实际运行结果，区分静态检查、自动化测试和真实依赖联调。

## 快速约定

| 对象 | 默认约定 |
|---|---|
| 文件、函数、变量 | `snake_case`；名称表达动作或业务数据 |
| 类、数据模型 | `PascalCase` |
| 常量、事件标识定义 | `UPPER_SNAKE_CASE`，避免散落字符串 |
| 内部方法 | `_action_name`；多步骤教学流程可用 `_step_1_validate_inputs` |
| 工作流节点 | 文件 `node_<action>.py`、类 `Node<Action>`、注册 ID `node_<action>` |
| 状态模型 | `<Business>GraphState`，显式字段与类型 |
| 文档与注释 | 中文说明职责、输入输出、关键业务条件和设计原因 |
| 外部能力 | 按服务职责封装，业务节点调用稳定适配接口 |
| 日志 | 统一 logger，带 task/request ID、节点名、耗时和必要数量 |

节点命名只用于已有或明确需要节点式编排的项目。普通 Python 模块使用函数或服务即可。源码中的 `atguigu` 包名、模型品牌、GPU 源和业务字段不作为通用默认值。

## 规则性质

源码依据文件区分“原项目明文规定”“已观察到的模式”“提炼后的改进建议”。新代码采用规则文件的约定；维护旧代码时只调整与任务相关的部分。不要为了统一风格大范围改名，也不要把不一致或错误实现作为标准复制。
