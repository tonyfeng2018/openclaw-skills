# web3Project-research Skills

Web3 项目调研 Skill 合集，适用于 [OpenClaw](https://openclaw.ai) 智能助手。

## Skills 列表

### 📋 project-research — Web3 项目调研

自动从 RootData API、官方 Blog、官方文档等来源收集项目信息，按标准模板输出调研报告。

**报告包含：**
- 项目阶段 / 赛道标签
- 官网 / 推特 / Discord / TG 链接
- 近期重大进展（附链接）/ TGE 时间表
- 融资情况（累计总额 + 逐条明细）
- 团队亮点（含 CEO/创始人）
- 空投分析 / 社区热度

**使用方式：**
```
请帮我调研 [项目名]
```

👉 [查看 SKILL.md](./project-research/SKILL.md)

---

### 📊 project-rating — Web3 项目评级

结合 RootData、DefiLlama、CoinMarketCap 多维度评分，输出 A / A- / B+ / B- 综合评级。

**评级维度：**
- ① 基本面 & 产品（TVL/Fees/用户数）
- ② 赛道风口
- ③ 融资规模
- ④ 团队背景
- ⑤ 社群热度
- ⑥ 交易所预期

**使用方式：**
```
请对 [项目名] 做评级
```

👉 [查看 SKILL.md](./project-rating/SKILL.md)

---

## 版本历史

| 版本 | 日期 | 更新内容 |
|---|---|---|
| v1.1 | 2026-03-27 | project-research：修复 CEO 遗漏、融资格式、Blog 抓取、TGE 分析、社群热度模板 |
| v1.0 | 2026-03-01 | 初始版本，project-research + project-rating |

---

## 配置说明

两个 Skill 均需要 RootData API Key，存放于各自的 `references/config.md`（不含在仓库中，需自行配置）。

获取方式：[rootdata.com](https://www.rootdata.com/zh) → 登录 → 个人中心 → API 申请

---

作者：Gater (Tony) × AI Agent | GitHub: [@tonyfeng2018](https://github.com/tonyfeng2018)
