# 完成前验证

核心规则：**没有本轮实际验证证据，就不要声称“已通过”“已修复”“没有问题”。**

## 1. 先识别仓库真实命令

从以下位置寻找，而不是凭经验直接发明命令：

- `package.json` scripts
- `pom.xml`
- `build.gradle` / `build.gradle.kts`
- `Makefile`
- `pyproject.toml`
- CI workflow
- README / CONTRIBUTING / AGENTS.md

执行前确认命令的副作用：

- Review 只使用检查模式，不运行 formatter 写入、lint 自动修复或生成源码的命令；检查会改动受保护文件时，改用隔离副本或报告未执行原因。
- Optimize / Fix + Quality 的格式化和自动修复只作用于本次修改的文件或代码区域；文件内混有其他任务改动时，避免整文件重写。工具无法限制范围时使用检查模式，不执行整仓自动修复。
- 执行后与开始时的状态比较，确认没有产生无关源码、配置或锁文件改动；不要通过回滚用户已有改动来清理结果。

## 2. 选择与改动匹配的验证

### Java

常见但必须确认项目实际配置：

- 编译相关模块
- 单元/集成测试
- Checkstyle / Spotless / SpotBugs / PMD

### Python

- Ruff lint/format
- Pyright/mypy
- pytest/unittest

### React / TypeScript

- TypeScript typecheck
- ESLint
- test
- build
- 有 UI 行为变化时按项目能力跑组件/E2E 或实际页面验证

## 3. Bug 修复

理想验证链：

1. 能复现原问题。
2. 添加或找到会覆盖原问题的测试。
3. 修改实现。
4. 测试通过。
5. 检查 diff，确认修的是根因而不是仅绕过症状。

条件允许时，可验证测试确实能在旧实现下失败，避免“永远绿色”的无效回归测试。

## 4. 重构

纯重构至少确认：

- 相关测试仍通过。
- 对外接口没有意外变化。
- formatter/lint/typecheck 没有新增问题。
- `git diff` 中没有无关改动。

## 5. 结果表述

正确：

```text
已运行 ./mvnw -pl service test，42 个测试通过；
已运行 Checkstyle，无新增错误。
未运行全仓库集成测试，因为本地缺少依赖服务。
```

错误：

```text
应该没问题。
看起来测试会通过。
代码已经完全正确。
```

如果某项没跑，就明确写“未验证”，并说明原因。
