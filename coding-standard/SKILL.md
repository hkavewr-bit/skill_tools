---
name: coding-skill
description: 基于 Gmall 项目开发规范生成概要设计、SPEC 文档、Java 后端代码或前端代码。Use when Codex needs to write or review Gmall-related technical design documents, implementation specifications, Java backend code, frontend pages/components/API modules, naming conventions, comments, logs, directory placement, or AI-generated code explanations for the all_ai_gmall project.
---

# Gmall 编码规范 Skill

## 总体目标

使用本 Skill 时，所有概要设计、SPEC 文档、Java 后端代码和前端代码都要遵循 Gmall 项目的前后端开发规范，并优先贴合当前仓库已有目录、命名、框架和代码风格。

## 基本工作流

1. 先阅读相关需求、已有代码、接口、实体、页面或文档，确认业务边界和现有约定。
2. 生成概要设计或 SPEC 时，明确模块边界、关键流程、数据对象、接口契约、异常处理、日志点和验收标准。
3. 生成 Python 或前端代码时，按仓库现有分层放置文件，保持命名、注释、日志和参数规范一致。
4. 生成或修改包含中文的代码、注释、日志、校验文案和文档时，必须使用 UTF-8 编码保存，不得产生中文乱码。
5. 交付前检查目录命名、类名、方法名、变量名、常量名、参数名、配置名、注释和日志是否符合本 Skill。
6. 交付前扫描新增或修改文件中的中文注释、日志和提示文案，确认不存在 `浼`、`寰`、`鎵`、`鏄`、`锛`、`銆`、`鍙`、`鐢`、`绫`、`�`、`锟斤拷` 等疑似乱码字符。
7. 如果是 AI 生成代码，在回复中补充文本形式的函数调用流程图和关系图，帮助用户理解调用链路。


## 目录与文件放置

前端目录规范：

- 目录使用小写字母，多个单词使用中划线，例如 `order-list`、`user-center`。
- 页面放在 `pages` 下。
- 公共组件放在 `components` 下。
- 接口请求放在 `api` 下。
- 工具函数放在 `utils` 下。
- 静态资源放在 `static` 下，并按业务或类型分目录，例如 `static/images`、`static/icons`。
- 状态管理、常量、配置分别放在 `store`、`constants`、`config`。

后端目录规范：

- 包名全部小写，使用公司或项目域名倒置，默认使用 `com.atguigu.gmall` 。
- 按职责分层：`controller`、`service`、`service.impl`、`mapper`、`entity`、`config`、`constant`、`util`。
- vo或dto的类也都放在entity中。
- 公共模块放在 `common`。
- 业务模块按业务名称划分，例如 `product`、`order`、`user`、`cart`。

## 文件路径
后端根目录保存在project/gmall2026 下
前端根目录在project/gmall2026-admin-frontend/ 下
微信前端根目录 在project/gmall2026-wx-frontend/ 下
## Java 命名规范

- 类名使用大驼峰，例如 `OrderInfoController`、`ProductService`。
- 实体类与数据库表对应时不额外增加结尾，例如表 `order_info` 对应实体 `OrderInfo`。
- Controller 以 `Controller` 结尾，例如 `UserController`。
- Service 接口以 `Service` 结尾，例如 `OrderInfoService`。
- Service 实现类以 `ServiceImpl` 结尾，例如 `OrderInfoServiceImpl`。
- Mapper 以 `Mapper` 结尾，例如 `OrderInfoMapper`。
- 配置类以 `Config` 结尾，例如 `RedisConfig`。
- 常量类以 `Constants` 结尾，例如 `OrderInfoConstants`。
- 工具类以 `Utils` 结尾，例如 `DateUtils`。

方法命名：

- 方法名使用小驼峰，名称见名知意，例如 `getOrderDetail`、`createOrder`。
- Mapper 层方法动词与 SQL 执行动词保持一致：`select`、`update`、`delete`、`insert`。
- 其他层按场景使用 `save`、`find`、`remove`、`get`、`set`、`check` 等动词。

变量、参数、常量命名：

- 变量和参数使用小驼峰，例如 `userName`、`orderAmount`、`userId`、`orderId`。

- 布尔变量使用肯定语义，例如 `enabled`、`paid`、`visible`。

- 集合变量体现复数或集合含义，例如 `userList`、`orderMap`、`skuIds`。

- 临时变量也要有业务含义，避免 `a`、`b`、`data`、`obj`。

- 参数不要使用缩写，通用缩写除外，例如 `id`、`url`、`skuId`。

- 常量使用全大写加下划线，例如 `ORDER_STATUS_PAID`、`MAX_RETRY_COUNT`。

- 常量统一放入 `constant`、`constants` 或业务常量类中。

- 枚举值命名清晰，例如 `WAIT_PAY`、`PAID`、`CANCELLED`。

架构要求：

- 基础的单表插删改查时，直接使用mybatis-plus在Service层提供的方法，比如saveOrUpdate,list等， 不要在xml生成sql。
- 通过lombok 的@Data注解，生成getter和setter方法。
- 在设计各种entity时，尽可能不舍计VO 或者DTO， 统一使用最基本的entity对象 然后用对数据库以外的字段用  @TableField(exist = false) 进行扩展。如果一定有必须使用，陈述使用 VO或者DTO的理由(比如跨模块访问、复杂的页面对象), 且尽量复用,避免设计太多的Entity、VO、DTO增加维护成本，并在代码或者SPEC 中说明使用的理由。

## 前端编码规范

- 文件和目录命名优先使用小写字母加中划线。
- 前端接口参数对象统一使用 `params` 或具体业务名，例如 `queryParams`、`submitForm`。
- 前端事件变量可使用 `event`。
- 配置文件名使用小写加中划线，例如 `application-dev.yml`、`vite.config.js`。
- 前端环境变量按框架规范添加前缀，例如 `VITE_API_BASE_URL`。
- 关键接口调用前后使用 `console.log` 打印参数和返回值，方便调试。
- 生成页面、组件、API、工具函数时，优先复用仓库已有组件、请求封装、状态管理和样式约定。

### 微信小程序自定义导航与安全区规范

- 微信端页面位于 `project/gmall2026-wx-frontend/`，使用 uniapp + unibest。只要页面配置了 `navigationStyle: 'custom'`，就必须显式处理顶部状态栏/安全区，不能只依赖 `pt-safe` 或只依赖 CSS `env(safe-area-inset-top)`。
- 顶部搜索框、返回按钮、筛选栏等导航元素必须放在微信状态栏下方，不能与顶部时间、电量、网络状态或右侧胶囊按钮并排、遮挡、重叠。
- 优先从 `src/utils/systemInfo.ts` 引入 `safeAreaInsets` 和 `systemInfo`，用 `safeAreaInsets?.top || systemInfo?.statusBarHeight || 0` 计算顶部安全区；再结合原型给出最小高度兜底。
- 首页原型的顶部状态栏最小高度按 `62px` 处理，搜索框在状态栏下方内容区再保留约 `12px` 间距；列表搜索页原型的状态栏最小高度按 `30px` 处理，导航行高度按 `52px` 处理。
- 页面主体或 `scroll-view` 高度必须使用同一份 `statusBarHeight` 参与计算，避免顶部占位调整后内容区高度仍使用旧的 `env(safe-area-inset-top)` 或固定值。
- 编写 SPEC 或验收标准时，必须加入“搜索框/导航栏不能与微信顶部状态栏、胶囊按钮重叠”的检查项；生成代码时也要同步实现该布局约束。

### 数据库设计规范

- 所有表必须有唯一主键，且名称统一为id，不再额外设置业务唯一标识比如order_Id ，user_id。
- 所有表必须有以下字段

  -  create_user_id   bigint   非空
  -  create_time     datetime 非空
  -  update_user_id bigint   可空
  -  update_time  datetime 可空
  -  is_deleted   int 非空

- 不要设置外键

- 不要设置索引


## 注释与日志

### 编码与乱码防护

- 所有 Java、XML、YAML、Markdown、Vue、TypeScript、JavaScript 文件统一按 UTF-8 编码生成和保存。
- 中文注释、中文日志、中文异常提示、校验提示和页面文案必须直接输出正常中文。
- 交付前优先使用 `rg` 扫描本次新增或修改的文件，确认没有残留疑似乱码字符；若发现乱码，必须先修复再交付。

注释规范：

- 类和方法上都要增加说明。
- 公共类、公共方法、复杂业务逻辑必须添加注释。
- 方法内行数超过20行时, 在其中的核心代码行上添加注释。
- 核心代码行上要增加详细注释，解释业务意图或关键判断。
- 注释要说明"为什么"和"业务规则"，避免只复述代码。

后端日志规范：

- 核心代码进入时打印 debug 级日志。
- 核心代码完成时打印 debug 级日志。
- 关键信息或关键结果打印 info 级日志。
- 日志内容包含必要业务标识，例如 `userId`、`orderId`、`skuId`，避免输出敏感信息。

前端日志规范：

- 关键接口调用时使用 `console.log` 打印请求参数和返回值。
- 日志文案要能定位业务动作，例如查询、提交、更新、删除。

 

## 交付前检查清单

- 目录是否放在规范位置，并符合仓库已有结构。
- 类名、文件名、方法名、变量名、参数名、常量名是否符合规范。
- Java 分层是否清晰，Controller、Service、ServiceImpl、Mapper、Entity 是否各司其职。
- 前端页面、组件、API、工具函数、静态资源是否放置正确。
- 参数数量是否过多，是否需要封装对象。
- 注释是否覆盖类、方法、公共逻辑和复杂业务。
- 中文注释、日志、异常提示和校验文案是否为正常 UTF-8 中文，是否已扫描并排除疑似乱码字符。
- 后端 debug/info 日志点是否完整，前端关键接口日志是否补充。
- 概要设计和 SPEC 是否包含流程、接口、数据对象、日志点和验收标准。
- `*.ts` `*.json` `*.vue` 是否无乱码、无注释吞配置问题。
