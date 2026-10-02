# Java 代码质量规则

基线参考 Google Java Style 和 Alibaba Java Coding Guidelines，但项目自身 Checkstyle、Spotless、google-java-format、IDE formatter 等配置具有更高优先级。

## 1. 结构与命名

- 遵循仓库现有 package、分层和命名方式。
- 禁止 wildcard import（除非项目明确允许）。
- 控制结构使用大括号。
- 类、方法、变量名称表达业务语义。
- 同一类内成员保持可解释的逻辑顺序，不要机械地把新方法追加到文件末尾。
- 不因为“Java 企业级”就自动增加接口、实现类、DTO、Mapper、Factory、Manager 等层次。

## 2. Spring / 服务层常见问题

若项目使用 Spring：

- Controller 负责协议/参数/鉴权边界，不堆核心业务逻辑。
- Service/Domain 承担业务规则，但不要制造只有转发作用的 Service 链。
- 事务范围保持尽量小；避免把不必要的远程调用放在长事务里。
- 注意 self-invocation、代理事务、异步代理等框架语义，不做“看起来正确但实际不生效”的重构。
- Bean 生命周期、线程安全和 scope 要与状态使用方式一致。

## 3. Null 与 Optional

- 明确哪些值允许为空，不把 null 当成隐式状态机。
- public/domain 边界优先通过类型、校验或明确契约表达可空性。
- `Optional` 适合表达“可能没有返回值”的 API；不要机械用于所有字段、DTO 或方法参数。
- 避免层层 `if (x != null)`；若同一对象重复做 null 分支，检查模型和边界是否不清晰。

## 4. 集合与 Stream

- 选择能表达语义的数据结构：需要唯一性用 Set，需要 key 查找用 Map，不要长期线性扫描 List。
- Stream 用于清晰的数据变换；复杂分支、异常流程或带大量副作用时，普通循环往往更易读。
- 不为了“一行写完”拼接很长的 Stream pipeline。
- 避免在 hot path 中创建大量不必要的中间集合。
- 注意集合是否允许 null、顺序是否稳定、返回集合是否可修改。

## 5. equals / hashCode / compareTo

- value object 若用于 Set/Map key，应保证 `equals`/`hashCode` 一致。
- 比较对象使用适合的语义，不把引用比较当值比较。
- `BigDecimal` 比较数值时理解 `equals` 与 `compareTo` 的语义差异。
- 排序 comparator 保持传递性和稳定预期。

## 6. 异常与日志

- 不空 catch。
- 不默认 `catch (Exception)`；只有系统边界、统一转换或明确恢复场景才合理。
- 包装异常时保留 cause。
- 业务异常、系统异常、远程依赖异常应能被调用方区分到必要程度。
- 日志避免重复打印同一个异常堆栈。
- 不记录密码、Token、Cookie、密钥、完整隐私数据。

## 7. 数据库

- 检查 N+1 查询。
- 避免循环内单条查询/更新，可批量时优先批量。
- 大列表必须考虑分页、流式或分批处理。
- SQL 参数化，不拼接不可信输入。
- 事务中避免无界批量操作。
- JPA/Hibernate 项目注意懒加载、级联和 fetch plan；MyBatis/JOOQ 项目遵循已有查询抽象，不为了“统一”重写成熟 SQL。

## 8. 并发

- 共享可变状态必须有清晰并发策略。
- 不要自己实现成熟 JDK 并发工具已经提供的机制。
- 创建线程池时考虑队列、线程上限、拒绝策略、关闭和任务上下文。
- CompletableFuture/异步调用要明确 executor 和异常处理，不默认把阻塞操作丢到公共池。
- 并发优化没有压测/指标支持时保持保守。

## 9. 资源

- 可关闭资源优先 try-with-resources。
- HTTP/DB/文件连接遵循项目连接池和生命周期约束。
- 避免一次性把不可控大文件/结果集全部加载到内存。

## 10. 验证建议

从仓库配置中选择真实存在的命令，例如：

- Maven：相关模块 `test` / `verify`
- Gradle：相关模块 `test` / `check`
- Checkstyle / Spotless / SpotBugs / PMD

不要仅因为格式检查通过就声称业务正确。
