# Rotation Radar

[简体中文](README.md) | **English**

> A sector rotation and market-regime skill that uses relative strength, breadth, participation, and invalidation rules to distinguish real leadership migration from mean reversion or headline noise.

![type](https://img.shields.io/badge/type-regime--rotation-orange)
![domain](https://img.shields.io/badge/domain-sector--ETF-amber)
![license](https://img.shields.io/badge/license-GPLv3-blue)

## What This Is

Rotation Radar is not a simple return-ranking tool. It asks whether market leadership is migrating, whether that migration is confirmed internally, and what would invalidate the read.

## Core Logic

```text
relative_strength = sector_return - benchmark_return
breadth_score     = advancing_members / tradable_members
persistence       = rank_stability(short_window, medium_window)
confirmation      = breadth + volume_participation + factor_leadership
rotation_score    = weighted(relative_strength, breadth_score, persistence, confirmation)
regime_state      = risk_on / risk_off / transition / range_bound
valid_rotation    = high score + at least two confirmations
```

## Quick Start

```bash
python scripts/check_test_cases.py
sed -n '1,220p' references/playbook.md
```

## Parameters

| Parameter | Required | Description |
| --- | --- | --- |
| market_scope | yes | Market or ETF scope |
| ranking_universe | yes | Sectors, industries, ETFs, styles |
| observation_window | yes | 5D, 20D, 60D, etc. |
| benchmark | yes | Relative-strength benchmark |
| confirmation_signals | no | Breadth, volume, flow, style factors |
| invalidation_rule | no | Predefined failure condition |
| allocation_constraint | no | Max weight, number of holdings, caps |

## Validation

Run:

```bash
python scripts/check_test_cases.py
```

## Disclaimer

For regime and rotation research only. Not investment advice.
