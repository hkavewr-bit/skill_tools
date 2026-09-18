---
name: personal-project-preferences
description: 按用户舆情分析和电商客服项目中提取的个人编码偏好编写或修改 Python/FastAPI 代码、规划模块和设计应用架构。适用于延续个人项目风格、创建同类业务模块，或用户要求按自己的编码习惯开发；Vue 约定仅在已有 Vue 前端中适用。不用于把其他技术栈强制迁移为这些项目的架构。
---

# 项目开发个人偏好

将下列约定作为个人风格的默认选择。它们来自两个本地项目的静态源码样本，是观察到的项目惯例，不代表全部代码均由用户本人编写，也不是经过用户逐条确认的绝对规则。

当前用户要求和目标仓库现有规范优先。保留现有公共契约；只因风格不同，不进行全仓重命名或架构迁移。

## 使用方式

1. 读取目标项目的入口、同类模块和直接调用方，确定当前使用的目录、对象生命周期和数据契约。
2. 优先应用下面的共同偏好；涉及工作流或前端时，再选择对应分支。
3. 实现前明确新增对象放哪里、由谁构造、输入是什么、结果由谁消费。简单修改不必另写设计文档。
4. 若当前项目和本 skill 不同，延续当前项目；缺乏样本支持的选择说明为本次建议，不称为用户既定偏好。
5. 需要核实提取依据、比较两个项目差异时，读取 [源码证据与边界](references/source-evidence.md)。日常应用不依赖原始 E/F 盘可访问。

## 共同编码偏好

### 命名：业务词汇 + 职责

- Python 包、文件、变量和函数采用 `snake_case`；类用 `PascalCase`；常量和枚举成员通常用 `UPPER_SNAKE_CASE`。
- 优先完整业务名：`user_message`、`dialogue_state`、`evidence_records`、`research_results`，避免到处只用 `data`、`obj`、`manager`。
- 方法使用动作词：`get_*` 获取对象，`build_*` 装配或构建，`load_*`/`save_*` 读写，`handle_*`/`process_*` 处理，`_build_*`/`_handle_*` 表示内部步骤。
- FastAPI 路由函数倾向以 `_endpoint` 结尾，依赖工厂用 `get_<业务>_service`，依赖类型别名用 `<业务>ServiceDep`。
- 用职责后缀表达区别：`Request`/`Response` 是接口契约，`State`/`Context` 是运行状态，`Result` 是处理产物，`Service` 组织应用用例，`Engine` 编排核心流程，`Repository` 访问和映射数据，`Client` 适配外部能力。
- `Handler`、`Planner`、`Validator`、`Loader`、`Builder`、`Node` 只在实际承担对应职责时使用，不为每个功能机械生成整套类。
- 多参数构造和跨层调用倾向用关键字参数，关键输入输出写类型标注；使用项目支持的 `list[T]`、`dict[K, V]`、`T | None`。

### 模块：先分技术边界，再按业务职责细分

- API 层负责参数接收、输入输出模型转换和调用 Service。SQL、模型提示词、流程推进不塞入路由。
- Service 层组织一个用例：例如读取状态、调用 Engine、保存结果，或创建任务并交给编排器。
- Engine 组织业务阶段和分支，将检索、回复、校验、动作执行交给职责明确的对象。
- Repository/Client 隔离数据库和外部协议，并将外部结果转换为领域对象或统一结果结构。
- 简单业务采用平铺文件，如 `services/dialogue_service.py`；复杂业务再拆子包，如 `app/services/research/research_service.py`。不要求统一目录深度。
- 以业务语义组织模块；可复用的共同逻辑才进入 `common`、`infrastructure`。已有模块内私有小函数不因“复用”名义提前抽离。
- Python 导入通常使用项目包的绝对路径。新项目用自己的包名，不固定使用来源项目的 `atguigu`。

### 数据契约：外部校验和内部状态分开

- HTTP 请求响应默认使用 Pydantic `BaseModel`，名称明确表达输入输出。边界校验写在对应模型或现有校验层。
- 内部结构化数据倾向使用 `@dataclass(slots=True)`；可变集合使用 `field(default_factory=...)`。存在运行时校验需求时，不强迫替换现有 Pydantic 模型。
- HTTP Schema、领域 State/Context 和数据库 Record 分工明确，在边界显式转换；不要让 API 请求对象贯穿所有业务层。
- 需要持久化的复杂状态，可沿用 `to_dict()` / `from_dict()` 转换惯例；转换契约要对齐真实字段，不能把类型标注当作运行时校验。
- 相近业务能力共享数据契约。例如不同检索渠道映射到统一证据结构，再供后续处理使用。

### 依赖、配置与异步

- 在 `dependencies.py` 中使用 `Annotated[T, Depends(factory)]` 提供依赖；需要多个组件组合时，在 `builder.py` 或已有装配入口集中构造。
- 可替换能力优先通过已有抽象基类、Provider、注册表或 Factory 对接；没有替换需求时不额外创建插件框架。
- 对象生命周期按目标项目和状态归属决定。来源项目同时有服务单例和请求依赖，不能推导出“全部单例”或“全部逐请求创建”。
- 异步数据库、HTTP 和模型调用沿用 `async def` / `await`；纯转换函数保持同步。由现有生命周期或上下文管理器负责资源释放。
- 配置集中到 `Settings(BaseSettings)`，沿用目标项目的环境配置路径；提示词独立在 `prompt`/`prompts` 模块或模板文件中，不散落在路由。
- 两个项目均有 `pyproject.toml` 与 `uv.lock`，同类新项目可优先考虑 uv；保留项目自己的 Python 版本和依赖，不照搬来源项目的全部技术栈。

### 注释：中文解释职责和数据流

- 类/函数 docstring 用中文说明职责，复杂流程可用 `# 1. ...`、`# 2. ...` 标记业务阶段。
- 重点解释状态变化、输入输出、边界转换和业务原因；返回复杂结构时交代谁继续消费它。
- 教学任务可以展开类型和语法说明；普通开发不逐行解释 `self`、`await` 等基础语法。来源项目中的大量教学注释不是日常代码的必要密度。
- 不强制统一单/双引号、行宽、docstring 格式、日志框架：样本不一致，遵守目标项目配置。

## 场景分支

### 图式 Agent / 检索研究

适用于已经采用图工作流，或需求确实需要节点状态与条件边的项目。

- 每个业务 Agent 独立子包，按需要拆 `agent.py`、`graph.py`、`state.py`、`nodes/`、`tools/`。
- `graph.py` 负责节点、边和编译；节点类以 `Node` 结尾，文件采用对应 `*_node.py`，节点键采用动作名，如 `retrieve_evidence`。
- 节点通过 `__call__` 接收状态，返回状态更新字典；声明实际读写的字段，避免返回整个不相关状态。
- 共享契约集中到已有 `contract`，共享节点/运行上下文集中到已有 `common`；跨 Agent 结果保持统一结构。
- 工具内部可按 `db`、`vector`、`web_search/providers` 细分。保留来源差异的适配职责，不让底层 SDK 数据直接侵入所有节点。
- 不因为出现 LLM 就引入 LangGraph，也不强制每个图都使用同一个节点基类名称。

### 对话式客服 / 业务流程

适用于多轮状态、意图分流、槽位收集和可恢复业务流程。

- `DialogueEngine` 一类对象处理会话/轮次并路由；`Planner` 产出结构化计划，`Validator` 校验计划，再交给对应 `Handler`。
- 按职责保留 `task`、`knowledge`、`chitchat`、`clarify` 等分支；仅在当前产品有该业务时创建。
- 任务执行可进一步拆 `commands`、`flows`、`action`，区分命令处理、流程推进和实际业务动作。
- 业务流程确实需要配置化时，使用 YAML 描述流程并由 Loader 构建对象；新动作实现既有 `Action` 契约，在注册/装配入口加入。
- State/Context 表达当前流程、步骤和槽位，Service 协调持久化。不得将该样本误写为 LangGraph 架构。

### 已有 Vue 前端（单项目证据）

- 延续 Vue SFC 的 `<script setup>`；页面放 `views/*View.vue`，可复用展示块放 `components/`。
- 组件使用 PascalCase，JS 方法和变量使用 camelCase；通过 props 向下传数据、emit 向上传事件。
- 请求封装放现有 `services/*Api.js` 与公共 HTTP 模块，状态行为放 `composables/use*.js`，共享值放 `constants/`。
- 来源中也有 `composables/api.js`，先跟随调用链，不为统一目录擅自迁移。不能据此认定用户所有前端都必须用 Vue 或 JavaScript。

## 不固化的内容与交付检查

- 不复制拼写错误（如 `retrival`、`resonder`）、空异常捕获、调试接口、无效示例调用和无依据的 `type: ignore`。修改既有拼错的公共名称前先检查兼容调用方。
- 不把课程项目命名、数据库品类、服务商、具体模型、状态存储方式当作全局偏好。
- 样本不足以确认统一测试工具、强制 TDD、CI、微服务、DDD、认证、部署方式或日志规范；有需要时作为本次工程建议提出。
- 完成后检查命名与相邻代码一致、层间契约对应、装配入口接通、新逻辑有实际调用方；按改动执行适当验证，并明确静态检查和真实运行的区别。
