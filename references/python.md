# Python 代码质量规则

基线参考 Google Python Style；如果项目已有 Ruff、Black、Pyright、mypy 等配置，以仓库配置为事实来源。

## 1. 类型边界

- 公共 API、复杂业务函数和容易出错的边界优先添加准确类型。
- 避免用 `Any` 掩盖模型不清；确有动态边界时局部使用，不让 `Any` 扩散。
- 稳定结构优先 `dataclass`、TypedDict、Protocol、Pydantic/项目既有模型，而不是长期依赖不受约束 dict。
- 容器泛型写清元素类型。
- nullable 明确表达为 `T | None`（在项目 Python 版本支持时）。

## 2. 函数与模块

- 函数职责清晰，不把整个业务流程堆在一个函数里。
- 不为了“面向对象”把简单纯函数包进无状态 class。
- 不为一次调用创建多层 wrapper。
- 避免全局可变状态。
- 模块边界按业务职责组织，警惕循环依赖。

## 3. Python 常见坑

- 禁止可变默认参数，例如 `def f(items=[])`。
- 不使用裸 `except:`。
- 谨慎捕获整个 `Exception`。
- 不通过 `except: pass` 或宽泛 fallback 隐藏真实错误。
- 资源使用 context manager。
- 文件系统代码在符合项目风格时优先 pathlib。
- 不依赖字典/集合迭代顺序之外的隐含实现细节。
- 避免复制大对象或列表仅为了链式表达。

## 4. async

- `async def` 内避免直接执行长时间阻塞 I/O 或 CPU 密集工作。
- 独立 I/O 可并发时考虑 `asyncio.gather` / TaskGroup（以项目版本为准），但要处理异常和取消语义。
- 不把同步函数机械改成 async。
- 并发数量应有边界，避免对外部服务制造无界 fan-out。

## 5. 异常与日志

- 在能处理、转换或补充上下文的层捕获异常。
- 自定义异常仅在能表达稳定领域语义时引入。
- 记录异常时避免重复堆栈。
- 禁止把 secret/token/credential 输出到日志。

## 6. 性能

优先发现结构性问题：

- 循环中的数据库/HTTP 调用。
- 无界读取大文件/大结果集。
- 热路径重复 parse/compile/序列化。
- 本可 O(1) 查找却在循环里反复 O(n) 扫描。

不要为了微小收益把清晰 Python 改成难理解的技巧代码。

## 7. 工具

优先复用仓库已有工具：

- Ruff：lint / format
- Pyright 或 mypy：类型检查
- pytest / unittest：测试

如果仓库没有某工具，不要为了这次小重构强行引入整套工具链；可把建议列为可选改进。
