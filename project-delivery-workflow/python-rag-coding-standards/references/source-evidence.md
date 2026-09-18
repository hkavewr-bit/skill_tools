# 源码依据与提炼取舍

来源：`F:/pyHome/PythonProject/0331kb`，检查日期：2026-09-15。以下依据来自静态阅读，未启动服务、加载模型或访问数据库。路径均相对此来源根目录，行号为提炼时定位；源文件变化后应重新查找符号。使用本 skill 不要求本机存在该来源仓库。

## 已观察到的规则与模式

| 依据 | 观察 | 提炼方式 |
|---|---|---|
| `AGENTS.md`、`CLAUDE.md` | PEP 8、类型注解、docstring、复杂逻辑注释、参考已有依赖 | 保留编码要求；版本先从项目读取，不强制重复询问 |
| `pyproject.toml:9` | Python >=3.12；uv 源与 Hatch 打包配置 | 先确认实际运行版本和工具，不把 3.12、CUDA 源或依赖版本固定到新项目 |
| `atguigu/import_process/`、`atguigu/query_process/` | 分离导入与查询；各自包含 state、base、main_graph、nodes | 提炼为按业务流程组织代码的可选结构 |
| `atguigu/query_process/main_graph.py:15` 的 `KBQueryWorkflow` | `_init_nodes`、`_register_nodes`、`_register_edges`、`compile`、`run` | 保留编排与节点业务职责分离 |
| `atguigu/import_process/base.py` 的 `NodeBase` | `__call__` 包装日志/计时/进度，`process` 执行业务 | 保留模板方法结构；失败状态语义单独修正 |
| `atguigu/import_process/nodes/node_bge_embedding.py:14` | 中文步骤说明、`_step_1_get_inputs`、`_step_2_batch_embedding` | 保留主流程可读性，步骤编号作为可选惯例 |
| 同文件 `process` 与 `atguigu/import_process/state.py` | 节点返回 `chunks` 更新；集中定义状态字段 | 提炼字段所有权与增量返回建议，补齐元素类型 |
| `atguigu/query_process/nodes/node_rerank.py:51` | 本地与网络文档转换为一致字段 | 保留在边界规范化文档的方式 |
| `atguigu/utils/embedding_utils.py` | 模型获取函数缓存模型；批量生成向量 | 保留资源复用，补充并发和结果等长约束 |
| `atguigu/config/config.py` 类及环境变量读取点 | 模型、MinIO、Embedding、Milvus、Mongo 等配置集中定义 | 保留按领域配置与环境变量注入；不读取或复制 .env |
| `atguigu/tools/logger.py:9` 的 `setup_logger` | 类型标注、中文参数说明、防重复 handler、可选轮转日志 | 作为注释及统一日志设计依据 |
| `atguigu/web/api/query_service.py:43` 的 `QueryRequest` | Pydantic 请求模型与字段说明 | 保留入口模型，修正可空类型与默认值不匹配 |
| `atguigu/utils/task_utils.py`、`atguigu/utils/sse_utils.py:8` | 任务状态常量、节点展示映射与事件常量 | 保留集中标识，明确进程边界和终止语义 |

## 未作为推荐模式复制的实现

| 位置或现象 | 静态观察 | 生成新代码时的建议 |
|---|---|---|
| `node_rrf.py:41`、`node_rerank.py:46` 与向量节点返回方式不同 | 有的修改并返回全量状态，有的返回字段更新 | 明确节点写入契约，优先局部更新并检查合并语义 |
| `query_process/base.py` 和实际查询节点导入 | 查询基类构造需要 state；被抽样的查询节点导入的是导入流程基类 | 核对继承及构造契约，不把两份基类当成已统一设计 |
| `node_rrf.py` docstring 和输入组装 | 注释称融合 Web，但该方法只组装两路向量；Web 在后续重排合并 | 注释与实际数据流一致，按消费者追溯行为 |
| `node_rerank.py` 的 `_step_2_rerank_merged_docs` | 异常路径尝试将列表作为字典解包；zip 未显式校验等长 | 定义类型一致的降级结构，核对文档与分数数量 |
| `query_service.py` 的 `QueryRequest.session_id` | `str` 注解配 `None` 默认值 | 可空字段显式标注 `str | None` |
| `query_service.py` 的同步查询路径 | 内部吞掉异常后，外部仍构造“处理完成”响应 | 将失败结果显式传播到响应层 |
| `sse_utils.py` 的投递与事件生成 | 后台线程直接向 asyncio.Queue 投递；生成器未按终止事件退出 | 检查跨线程投递、超时、终止与清理；未验证运行表现 |
| `task_utils.py`、查询任务 ID | 单进程全局字典，查询把会话 ID 用作任务 ID | 明确运行边界，并发请求采用独立执行 ID |
| `web/api/import_service.py` | 上传名称/相对路径直接拼接；对象键主要按日期和文件名组成 | 解析后验证目录范围，避免同名文件意外覆盖 |
| `atguigu/test/`、各模块 `__main__` | 可见多个学习演示与手工运行入口 | 不宣称存在完整自动化回归；按目标项目实际配置验证 |

## 抽取边界

保留：业务分层、节点命名、步骤化方法、中文说明、状态模型、适配器、配置和日志。

适配：目录名、Python 版本、模型、数据库、依赖管理及是否采用图编排。

强化建议：异步边界、状态更新、错误类型、并发隔离与输入路径验证。这些是基于源码观察提出的规范，不表示源项目已满足。
