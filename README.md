# 轮动雷达

**简体中文** | [English](README.en.md)

> 行业轮动与市场状态识别技能：用相对强弱、宽度、参与度和失效条件，判断市场资金是在切换领导者、做均值回归，还是只是被单日新闻扰动。

![type](https://img.shields.io/badge/type-regime--rotation-orange)
![domain](https://img.shields.io/badge/domain-sector--ETF-amber)
![license](https://img.shields.io/badge/license-GPLv3-blue)

---

## 这是什么

轮动雷达不是简单把行业按涨幅排序。它关心的是：**市场领导力是否正在迁移，迁移是否有内部参与度确认，以及这个判断什么时候会失效。**

很多轮动分析的问题在于只看价格强弱：今天银行涨了就说价值轮动，今天科技跌了就说成长失效。这个技能会强制把轮动拆成三层：

1. **状态层**：市场是 risk-on、risk-off、过渡期，还是区间震荡
2. **领导力层**：哪些行业或 ETF 在多个窗口里持续相对强势
3. **确认层**：宽度、成交、资金流或风格因子是否支持这次迁移

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

## 适用场景

- 行业轮动分析
- ETF 强弱排序
- 市场宽度和涨跌家数诊断
- 风格切换识别
- 战术资产配置
- 判断单日板块大涨是否能升级为趋势

## 快速开始

```bash
# 校验测试用例
python scripts/check_test_cases.py

# 查看轮动手册
sed -n '1,220p' references/playbook.md
```

### 推荐调用方式

```text
使用 $rotation-radar 分析最近 20 个交易日的行业轮动。对象是 A 股申万一级行业，需要给出状态判断、行业排名、确认信号和失效条件。
```

## 参数说明

| 参数 | 必填 | 说明 | 建议 |
| --- | --- | --- | --- |
| market_scope | 是 | 市场范围，如 A 股、美股、港股、ETF 池 | 不要混用不可比资产 |
| ranking_universe | 是 | 行业、板块、ETF、风格桶 | 保证成分定义一致 |
| observation_window | 是 | 观察窗口，如 5D、20D、60D | 与交易周期一致 |
| benchmark | 是 | 相对强弱基准 | 使用同市场宽基 |
| confirmation_signals | 否 | 宽度、成交量、资金流、风格因子等 | 至少两个独立信号 |
| invalidation_rule | 否 | 轮动失效条件 | 事前定义 |
| allocation_constraint | 否 | 最大权重、行业上限、持仓数量 | 避免排名变成满仓建议 |

## 输出结果

| 输出 | 说明 |
| --- | --- |
| 状态判断 | risk-on、risk-off、过渡期或区间震荡 |
| 行业/ETF 排名 | 领先、跟随、落后、观察四类 |
| 确认信号 | 宽度、成交、资金流或风格强弱是否支持 |
| 轮动解释 | 这次变化更像趋势、均值回复还是新闻扰动 |
| 交易/配置候选 | 给出候选而非无条件推荐 |
| 失效条件 | 什么发生时承认判断错误 |

## 目录结构

```text
rotation-radar/
├── SKILL.md
├── README.md
├── README.en.md
├── scripts/
│   └── check_test_cases.py
├── references/
│   ├── playbook.md
│   └── test-cases.md
├── assets/
│   └── rotation-radar.svg
├── agents/
│   └── openai.yaml
└── LICENSE
```

## 核心约束

| 约束 | 说明 |
| --- | --- |
| 不把涨幅排名当轮动 | 排名只是起点，不是结论 |
| 至少两个确认信号 | 相对强弱之外，还要宽度、成交或资金流确认 |
| 周期必须一致 | 不用 5 日信号支撑 3 个月配置 |
| 宽度恶化必须降级 | 少数权重股拉动不等于健康轮动 |
| 只述不荐 | 输出研究结构，不构成投资建议 |

## 测试用例

测试用例位于 [references/test-cases.md](references/test-cases.md)，覆盖：

- 新领导者
- 假突破
- 上涨变窄
- ETF 地图
- 板块幻象
- 状态翻转

运行：

```bash
python scripts/check_test_cases.py
```

## 免责声明

本项目仅用于市场状态与轮动研究流程整理，不构成任何投资建议，不保证任何配置收益。
