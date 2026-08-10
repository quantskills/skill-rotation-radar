---
name: rotation-radar
description: 当需要分析市场状态、行业轮动、ETF 强弱排序、市场宽度、风格切换或战术配置信号时，使用此 skill。适用于 A 股、港股、美股和 ETF 池的行业轮动分析、risk-on/risk-off 状态识别、相对强弱排名、宽度确认、假突破过滤和轮动失效条件设计。
metadata:
  organization: QuantSkills
  organization_url: https://github.com/quantskills
  repository: skill-rotation-radar
  repository_url: https://github.com/quantskills/skill-rotation-radar
  project_type: skill
  collection: quantitative-research
  project_status: community-project
  review_status: unreviewed
  license: GPL-3.0-only
  category: quantitative-finance
---

# 轮动雷达

## 项目声明

- 项目类型：Community Project / Skill；当前未声明为官方、认证或生产项目
- 维护者：本仓库维护者及贡献者
- 数据来源：由使用者提供或指定的市场/ETF 行情、成分股宽度、基准、成交量和资金流数据
- 研究边界：仅用于市场状态与轮动研究及教育示例，不自动获取数据、不执行交易
- 已知限制：结论依赖观察窗口、基准选择、成分股可得性、宽度口径和确认信号质量；本技能不能替代实时行情或交易规则核验

## 适用场景

1. 用户需要判断当前市场是 risk-on、risk-off、过渡期还是区间震荡
2. 用户需要对行业、板块、ETF 或风格资产做轮动排名
3. 用户需要判断某个板块上涨是真轮动、补涨、均值回归还是新闻扰动
4. 用户需要把相对强弱、市场宽度、成交量和资金流合并成配置观察框架
5. 用户提到行业轮动、ETF 配置、市场宽度、风格切换、领先板块、假突破或状态翻转

## 研究立场

轮动不是涨幅排名。真正值得跟踪的轮动必须同时回答三个问题：**谁在变强、内部有没有参与、什么情况说明判断错了。** 如果只有价格强，没有宽度和参与度，本 skill 默认把它降级为“待确认”。

## 核心逻辑

```text
relative_strength = sector_return - benchmark_return
breadth_score     = advancing_members / tradable_members
persistence       = rank_stability(short_window, medium_window)
confirmation      = breadth + volume_participation + factor_leadership
rotation_score    = weighted(relative_strength, breadth_score, persistence, confirmation)
regime_state      = risk_on / risk_off / transition / range_bound
valid_rotation    = rotation_score 高 + 至少两个确认信号同向
```

## 输入参数

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| market_scope | str | 是 | 市场范围，如 A 股、美股、港股、全球 ETF |
| ranking_universe | str/list | 是 | 行业、板块、ETF、风格桶或主题篮子 |
| observation_window | str/list | 是 | 观察窗口，如 5D、20D、60D |
| benchmark | str | 是 | 相对强弱基准，如沪深 300、标普 500、恒生指数 |
| confirmation_signals | list | 否 | 宽度、成交量、资金流、风格因子、领涨家数等 |
| regime_definition | str | 否 | 用户自定义 risk-on/risk-off 标准 |
| invalidation_rule | str | 否 | 失效条件，如跌破相对强弱均线或宽度低于阈值 |
| allocation_constraint | dict/str | 否 | 权重上限、持仓数量、行业上限和调仓频率 |

## 工作流

### 1. 状态识别

- 先判断市场环境，不直接输出行业排名
- 用宽度、波动、强弱扩散和防御/进攻资产表现判断 risk-on、risk-off、过渡期或震荡
- 如果跨资产信号冲突，标记为过渡期，不强行给单一结论

### 2. 领导力排序

- 用相对强弱而不是绝对涨幅排序
- 同时看短窗口和中窗口，避免把单日冲高当趋势
- 将对象分成领先、跟随、落后和观察四类

### 3. 确认过滤

- 至少检查两个独立确认：宽度、成交量、资金流、风格因子或内部参与度
- 如果上涨由少数权重股驱动，降级为“窄幅领导”
- 如果价格创新高但宽度恶化，提示趋势脆弱

### 4. 失效设计

- 为每个主要结论给出失效条件
- 失效条件应尽量来自相对强弱、宽度或状态变化，而不是事后解释
- 不把排名直接变成执行指令

## 输出格式

返回结果必须包含：

1. **状态判断**：risk-on、risk-off、过渡期或区间震荡
2. **排名表**：领先、跟随、落后、观察对象
3. **确认信号**：哪些信号支持，哪些信号冲突
4. **轮动解释**：趋势迁移、补涨、均值回归或新闻扰动
5. **配置含义**：只给研究意义上的候选方向
6. **失效条件**：什么变化会推翻当前判断

## 验收要求

1. 不得只凭涨幅排名判断轮动
2. 至少给出两个确认维度，或明确说明确认不足
3. 时间窗口必须与用户的交易/配置周期一致
4. 宽度恶化时必须提示风险
5. 输出必须包含失效条件

## 资源

- 研究手册：[references/playbook.md](references/playbook.md)
- 测试用例：[references/test-cases.md](references/test-cases.md)
- 用例校验：[scripts/check_test_cases.py](scripts/check_test_cases.py)
