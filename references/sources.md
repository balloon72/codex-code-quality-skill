# 参考来源与设计说明

本 Skill 是原创整理与工程化改写，不是下列项目文档的全文复制。参考这些公开项目的高价值思想，并针对 Codex + Java/Python/React 的按需代码优化场景重新组织。

## 主要参考

1. Google Style Guides
   - https://github.com/google/styleguide
   - 用途：Java/Python 基础编码风格、命名、结构和一致性思想。

2. Alibaba P3C / Alibaba Java Coding Guidelines
   - https://github.com/alibaba/p3c
   - 用途：Java 生产工程实践、异常日志、数据库、安全、工程质量等维度。

3. Addy Osmani — agent-skills / code-review-and-quality
   - https://github.com/addyosmani/agent-skills
   - 用途：多轴 review（正确性、可读性、架构、安全、性能）、减少无意义复杂度、按严重度输出发现。

4. obra/superpowers — verification-before-completion
   - https://github.com/obra/superpowers
   - 用途：完成声明必须基于新鲜验证证据。

5. Vercel Agent Skills — React Best Practices
   - https://github.com/vercel-labs/agent-skills
   - 用途：React/Next.js 的 waterfall、bundle、server/client fetching、rerender、rendering 性能等高价值检查点。

6. OpenAI Skills documentation
   - https://developers.openai.com/api/docs/guides/tools-skills
   - https://developers.openai.com/blog/eval-skills
   - 用途：SKILL.md、references/、按需发现/加载的结构设计。

## 设计取舍

- 不把完整 Style Guide 常驻上下文。
- 主 `SKILL.md` 只保留路由、工作流和底线规则。
- Java/Python/React 分文件按需加载。
- 优化目标是降低认知负担，而不是增加抽象数量。
- 项目现有 formatter/linter/test/config 始终优先于通用规则。
