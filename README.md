# Codex Code Quality Skill

面向 **Java + Python + React/TypeScript + Codex** 的按需代码优化与 Review Skill。

它的目标不是强迫 AI 每次写代码都加载一大堆规范，而是在你明确要求：

- 优化代码
- 重构代码
- 让代码更优雅/更容易维护
- Code Review
- 检查过度设计、重复逻辑、性能或安全问题

时，让 Codex 再加载完整代码质量工作流和当前语言的专项规则。

普通开发、问答和未要求质量优化的常规修复不触发此 Skill；明确说“优化这个模块”等自然语言请求或使用 `$code-quality` 时启用。基础安全和工作区保护仍遵守现有项目规则。

## 特点

- **按需加载**：平时只暴露 Skill 的 `name + description`。
- **语言路由**：Java / Python / React 规则拆分到 `references/`，只读当前需要的部分。
- **反过度设计**：不把“更多类、更多接口、更多模式”视为优化。
- **项目优先**：项目已有 `AGENTS.md`、formatter、lint、typecheck、测试配置优先。
- **行为保持**：只要求重构/优化时默认保持行为和 API 契约。
- **完成前验证**：没有实际构建/测试/lint 证据，不宣称“已通过”。

## 目录

```text
codex-code-quality-skill/
├── SKILL.md
├── README.md
├── LICENSE
├── references/
│   ├── common.md
│   ├── java.md
│   ├── python.md
│   ├── react.md
│   ├── review.md
│   ├── security-performance.md
│   ├── verification.md
│   └── sources.md
└── assets/
    └── review-template.md
```

## 安装到 Codex

Codex 支持用户级 Skill：

```text
~/.codex/skills/<skill-name>/SKILL.md
```

### macOS / Linux

```bash
git clone https://github.com/balloon72/codex-code-quality-skill.git ~/.codex/skills/code-quality
```

### Windows PowerShell

```powershell
git clone https://github.com/balloon72/codex-code-quality-skill.git "$HOME\.codex\skills\code-quality"
```

## 使用

```text
$code-quality 优化一下这个模块，保持行为不变，重点减少过度设计和重复逻辑。
```

```text
$code-quality review 当前 git diff，只报告 Critical/Required/Consider，不要改代码。
```

```text
$code-quality 检查这个 Java 服务有没有 N+1、循环远程调用、异常吞噬和无意义抽象，并直接修复高价值问题。
```

```text
$code-quality 优化这个 React 页面，重点检查 useEffect、请求 waterfall、重复状态和无意义 rerender。
```

详细参考来源见 [`references/sources.md`](references/sources.md)。

## License

MIT
