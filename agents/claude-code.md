# Claude Code 入口

本项目的核心技能说明是 `../SKILL.md`。当用户请求行业轮动、ETF 强弱排序、市场宽度或风格状态分析时，先读取该文件，再按需读取 `../references/playbook.md` 和 `../references/test-cases.md`。

必须保留观察窗口、基准、确认信号和失效条件；跨资产信号冲突时标记为过渡状态，不强行压缩成单一结论。
