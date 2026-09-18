# 源码证据与适用边界

提取日期：2026-09-18。方式：目录枚举、代表性源码阅读和关键符号搜索；未启动两个应用，未验证外部数据库、模型或搜索服务。不是全量代码审计，也没有通过 Git 作者归因确认每段代码的作者。

## 样本根目录

- A：`E:\py_home\0331-sentiment-analyze\0331-sentiment-analyze`。用户输入的 `E:\py\_home\...` 不存在，已验证这里的真实路径。
- B：`F:\pyHome\PythonProject\sz0331-ecommerce-customer-service`。
- 以下 A 路径除前端外相对 `A/sentiment-service/`；B 路径相对 B 根目录。
- 行号是本次静态快照，后续源码可能变化。应用 skill 无需重新访问这些磁盘。

## 共同惯例证据

| 惯例 | A 证据 | B 证据 | 提取结论 |
| --- | --- | --- | --- |
| 路由轻量、调用 Service | `atguigu/app/routers/rest/research_router.py:23-42`，调用 `service.research` 并构造 Response | `atguigu/api/chat_router.py:53-65`，请求转领域、调用 service、转响应 | 两项目共同；不是路由只允许一行代码 |
| `_endpoint`、`Request`、`Response` | `research_router.py:24,34`、`app/schemas/research_schema.py:12-50` | `api/chat_router.py:54,120`、`api/schemas.py:24-43` | 可作为同类 FastAPI 默认命名 |
| 依赖工厂与 Dep 别名 | `atguigu/app/dependencies.py:23-25,83-86` | `atguigu/api/dependencies.py:17-42` | 共同使用 Annotated 与 Depends |
| Service 与 Engine 分工 | `atguigu/app/services/research/research_service.py:16-41`，创建任务并调用 orchestrator | `atguigu/services/dialogue_service.py:8-34`，load_state → handle_message → save_state | 共同的职责倾向，具体调用链不同 |
| 显式领域结构 | `atguigu/engines/contract/evidence.py:17-109`，多个 slots dataclass | `atguigu/domain/contexts.py:12-60`，TaskContext/SystemContext | dataclass 为内部数据倾向，不代替外部校验 |
| Repository 映射外部数据 | `atguigu/engines/insight_agent/tools/db/repository.py:21-86`，SQL 结果转 EvidenceDocument | `atguigu/repository/dialogue_repository.py:10-80`，数据库记录与 DialogueState 互转 | 共同隔离存储，存储策略不统一 |
| 类型与集合 | `contract/evidence.py:38-58,76-91` | `domain/contexts.py:21-37`、`api/schemas.py:24-38` | 现代 Python 类型标注、默认工厂 |
| 构造与能力边界 | `atguigu/engines/media_agent/web_search/base.py:29-99`、`factory.py:13-41` | `atguigu/engines/builder.py:26-56`、`task/action/base.py:19-25`、`task/action/register.py:7-16` | 两边都有装配/抽象；A Factory 当前固定单 Provider，不能称动态插件发现 |
| 集中配置 | `atguigu/engines/contract/settings.py:21,102-103` | `atguigu/config/settings.py:12,21` | BaseSettings + 环境文件；未复制配置值 |
| 提示词独立 | `atguigu/engines/prompts/insight.py:5-18` | `atguigu/prompt/loader.py:3-6`、`plan/planner.py:12,36-41` | A 用 Python 常量，B 用 Jinja2 文件；不强行选一种 |
| 中文职责与步骤 | `atguigu/app/services/research/research_service.py:25-31` | `atguigu/services/dialogue_service.py:16-34`、`api/chat_router.py:56-63` | 中文语义说明共同，注释密度不统一 |
| 包与依赖 | `pyproject.toml:14-42,96-97`，根目录 uv.lock | `pyproject.toml:5-17`，根目录 uv.lock | Python/FastAPI 与 uv 共同；A 要求 >=3.11，B >=3.12，不能锁死同版 |

以上“共同”指两个项目中都有证据，不等价于用户亲自声明的偏好，也不表示所有模块都严格遵守。

## 架构差异

### A：技术分层 + Agent 角色包

- Web 入口：`atguigu/app/app.py`；HTTP/SSE 路由分别在 `app/routers/rest` 和 `app/routers/sse`。
- 应用用例：`app/services/{research,host,report,system,...}`。
- 业务引擎：`engines/{insight_agent,media_agent,host_agent,report_engine,orchestrator}`。
- 共享契约/运行逻辑：`engines/contract`、`engines/common`。
- `atguigu/engines/insight_agent/graph.py:30-59` 明确组装 StateGraph、注册七个节点、顺序边和条件边，最后 compile。
- `atguigu/engines/insight_agent/nodes/evidence_retrieval_node.py:15-27` 的 `EvidenceRetrievalNode.__call__` 返回 `retrieved_records` 更新。
- `atguigu/engines/insight_agent/state.py:11-22` 声明节点间共享字段。
- 只能据此提取图工作流组织方式，不能声称各角色完整运行通过。

### B：技术分层 + 对话业务分支

- `api`、`services`、`engines`、`repository`、`domain`、`infrastructure` 构成主要边界。
- `atguigu/engines/builder.py:26-56` 装配 Planner、Validator、各 Handler、FlowExecutor、ActionRunner 和知识 Provider。
- `atguigu/engines/dialogue_engine.py:34-72` 准备会话、开启轮次、区分文本/对象消息、提交本轮结果。
- `atguigu/plan/turn_plan.py:8-85` 区分 task/knowledge/chitchat 计划及校验结果。
- `atguigu/task/flows/loader.py:12-50` 读取 YAML 并组合 FlowList；根目录 `flow_config` 保存流程配置。
- `atguigu/task/action/base.py:10-25` 定义 ActionResult/Action；`register.py:7-16` 以动作名注册和获取。
- 没有把这个项目解释为 LangGraph 图。注册表、状态对象和流程引擎本身不等于 LangGraph。

## 单项目的前端样本

只在 A 发现前端，以下路径相对 A 根目录：

- `user-frontend/src/views/ResearchView.vue:1-14,28-55`：script setup、camelCase 状态、props/emit、页面发起请求。
- `user-frontend/src/components/ResearchHero.vue:1-14`：展示组件参数与事件。
- `user-frontend/src/services/configApi.js:1-9`：调用公共 request，封装业务接口。
- 目录中存在 `services/*Api.js`、`composables/useSSE.js`、`constants/`；ResearchView 当前从 `composables/api.js` 导入，说明 API 目录并未完全统一。

因此只将 Vue 风格用作条件偏好，不能认定为跨项目共同选择。

## 不宜提升为规范的观察

- `atguigu` 是两个样本的共同包名，但有明显课程语境；不作为新项目包名。
- A 存在 `retrival_service.py` / `RetrivalService`，B 存在 `knowledge/resonder.py`；这是兼容性敏感的现有拼写，不是推荐命名。
- A `user-frontend/src/views/ResearchView.vue:53` 有空 catch，不作为异常处理范式。
- A `atguigu/engines/insight_agent/tools/db/repository.py:97` 示例调用 `retrival_db`，而该文件展示的实现入口为 `db_call`；示例不能证明接口可用。
- B `atguigu/api/chat_router.py:15-50` 保留 hello/test 演示接口，不复制到新业务模块。
- A `app/dependencies.py:18-76` 包含模块服务实例；B `api/dependencies.py:24-39` 依赖会话和工厂；生命周期不存在单一共同模式。
- A 部分 Python 文件含系统性的类型/语法教学注释；这些注释与普通开发注释用途不同。
- A 有 pytest 断言测试，也有发布订阅演示文件；B 文件清单中测试较少。不足以推出统一测试框架或强制 TDD。
- 引号、空格、返回类型完备程度及日志方式不一致。没有据此生成未经验证的格式化或日志要求。

## 应用判例

- 新增 FastAPI 业务接口：先匹配相邻 router/schema/service；有持久化才加入 Repository，无复杂编排不硬加 Engine。
- 新增客服业务动作：实现现有 Action 契约，接入注册/装配和需要的流程配置，不另起 Graph。
- 新增舆情图节点：补节点类和 state 字段，注册图节点及边，再核对结果消费者。
- 维护其他技术栈：保持原有栈和仓库约定，最多迁移“职责清楚、业务命名明确”的适用原则。
- 仅请求方案：按偏好组织方案，不将设计请求解释为安装依赖或开发全部模块的授权。
