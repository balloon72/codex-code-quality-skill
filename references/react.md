# React / TypeScript 代码质量规则

基线参考 TypeScript 严格类型实践和 Vercel React Best Practices；项目 ESLint、TypeScript、formatter、框架版本及现有架构优先。

## 1. TypeScript

- 避免 `any`；优先精确类型、泛型、联合类型或 `unknown` + narrowing。
- 不为了通过编译随意 `as` 断言。
- `@ts-ignore` / `@ts-expect-error` 必须有明确理由，并尽量局部。
- API 请求、返回值、公共组件 props 使用明确类型。
- 优先使用项目已有领域类型，不复制一套近似类型。

## 2. 组件边界

- 组件围绕一个清晰 UI/业务职责组织。
- 复杂业务规则不要全部塞进 JSX。
- 也不要把每几行 JSX 都拆成新组件；只有职责、复用、性能边界或可测试性真正改善时再拆。
- 避免“超级组件”通过大量 boolean props 控制几十种模式；更适合时使用组合或更明确的变体模型。
- 不在组件内部定义会在每次 render 重新创建的组件类型。

## 3. 状态

- 状态尽可能靠近使用位置。
- 能由 props/其他 state 直接推导出的值，不重复存 state。
- 不用 effect 做本可在 render 期间计算的派生状态。
- 跨组件共享状态前先判断是否真的需要全局 store。
- 服务端状态与本地 UI 状态分开考虑，优先复用项目已有请求/缓存方案。

## 4. Effect

使用 effect 处理与 React 外部系统同步的副作用，而不是把所有流程都放进 effect。

警惕：

- effect 里计算派生状态。
- 用户点击后才需要执行的逻辑却被 effect 间接触发。
- 依赖数组不稳定导致重复执行。
- effect 链条：A 更新 state → B effect → C 更新 state → D effect。

能在事件处理器、数据层或 render 中直接表达时，优先直接表达。

## 5. 数据请求与 waterfall

- 独立请求可以并行时，避免串行 await。
- 尽早启动异步操作，尽量在真正需要结果时再 await。
- 避免父组件请求完成后才让子组件开始不相关请求的 waterfall。
- 使用项目已有 cache/dedup 机制，避免同一数据重复请求。
- 列表请求有分页/上限。
- Server/Client 边界按框架实际版本和项目架构处理，不机械套 Next.js 模式到普通 React。

## 6. Render 与性能

- 不默认给所有函数加 `useCallback`，不给所有计算加 `useMemo`。
- memoization 应解决实际重复昂贵工作、稳定引用需求或已观察到的 rerender 问题。
- 大列表考虑分页、虚拟化或 `content-visibility` 等适合方案。
- 重复查找可在有实际规模价值时构建 Map/Set。
- 静态内容可移出频繁 render 路径，但不要牺牲可读性做微优化。

## 7. Bundle

- 谨慎使用 barrel exports，尤其大型库或组件库；如果会导致不可控 bundle，直接 import 目标模块。
- 重型且非首屏组件可考虑动态加载。
- 第三方统计/非关键脚本尽量不阻塞关键渲染路径。
- 新增大型依赖前检查项目已有能力和 bundle 影响。

## 8. UI 状态与可访问性

关键交互明确处理：

- loading
- empty
- error
- success
- disabled / submitting

表单避免重复提交；可交互控件使用正确语义元素和必要的键盘/可访问性属性。

## 9. 验证

从项目脚本中选择真实命令：

- TypeScript typecheck
- ESLint
- formatter
- 单元/组件/E2E 测试
- 构建

UI 性能优化如果没有 profiler、bundle 报告或明确路径证据，不要宣称“性能提升明显”。
