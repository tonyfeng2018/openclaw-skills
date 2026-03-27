---
name: project-research
description: Web3 项目调研报告生成。当用户输入项目名称并要求调研时激活。自动从 RootData API、官方文档、官方 Blog 等来源收集：项目阶段、赛道标签、官网/推特/Discord/TG 链接、TGE 时间、用户数据、融资情况、团队背景、空投信息、社区热度，并按标准模板输出完整调研报告。触发词：调研、research、项目调研、帮我查、帮我调研。
---

# Project Research Skill v1.1

## 概述

按照标准模板，对 Web3 项目进行全面调研并输出结构化报告。

---

## 执行流程

### Step 1：RootData API 搜索项目

```bash
curl -s -X POST "https://api.rootdata.com/open/ser_inv" \
  -H "apikey: <API_KEY>" \
  -H "language: cn" \
  -H "Content-Type: application/json" \
  -d '{"query":"<项目名>","include_tags":1}'
```

**⚠️ 重要：搜索结果中 `type=3` 的条目是人物（团队成员），直接解析姓名+简介，补充团队信息，不要遗漏 CEO/创始人。**

获取项目 ID 后：

```bash
curl -s -X POST "https://api.rootdata.com/open/get_item" \
  -H "apikey: <API_KEY>" \
  -H "language: cn" \
  -H "Content-Type: application/json" \
  -d '{"project_id":<ID>,"include_team":1,"include_investors":1}'
```

返回字段：`total_funding`、`tags`、`social_media`（website/X/discord/telegram）、`investors`（lead_investor=1 为领投）、`token_symbol`、`establishment_date`

**⚠️ 若 `team` 字段为空数组，必须从 `ser_inv` 搜索结果的人物条目（type=3）补充团队成员，尤其是 CEO/创始人不可遗漏。**

---

### Step 2：抓取官方 Blog + 补充信息

优先级顺序：

1. **官方 Blog**：`<website>/blog` 或 `<website>/en/blog` → 获取近期重大事件、融资公告、产品上线、TGE、空投、合作、奖项
2. **官方文档**：`docs.<domain>` 或 gitbook → 获取产品功能描述、路线图
3. **官网首页**：`<website>` → 获取用户规模、数据声称
4. **Medium 博客**：`medium.com/@<项目名>` → 补充历史事件（官方 Blog 不足时使用）

抓取 Blog 时，重点关注：
- 近 6 个月内的所有文章标题 + 链接（评估近期进展密度）
- 融资公告（含参与机构）
- TGE / 代币相关公告
- 合作伙伴公告（尤其是知名机构/协议）
- 用户/生态数据公告（DAU/TVL/交易量）
- 奖项、黑客松、孵化计划

---

### Step 3：整合数据，按模板输出报告

---

## 报告模板

```
📋 <项目名> 项目调研报告

现阶段：[测试网 / 主网上线 / 公测阶段 / 早期 / ToB业务]
赛道：[来自 RootData 标签]

官网：<website>
官方文档：<docs链接，若无则注明>
推特：<X链接>
Discord：<discord链接，若无则注明暂未找到>
TG：<telegram链接，若无则注明暂未找到>

---

🚀 近期重大进展

TGE：[时间+是否完成，附链接；若无代币则说明，并分析是否有潜在 TGE 迹象]

[YYYY-MM] [事件描述，15字以内]
🔗 <链接>

[YYYY-MM] [事件描述]
🔗 <链接>

（按时间倒序，至少列出 3-5 条近期事件；无 Blog 可抓取时标注"需手动核查"）

路线图：[官方公布的关键节点，若未公布则标注未披露]

---

🎯 是否明牌空投
✅/❌ [说明 + 链接]
（若无代币但有积分/积累系统，分析是否为 TGE 前哨，给出判断）

---

💡 一句话项目概述
<一句话>

---

💰 融资情况

累计融资 XXX 万美元

- YYYY-MM-DD：$XXX 万，[轮次]，[主要参与机构]
- YYYY-MM-DD：$XXX 万，[轮次]，[主要参与机构]（*=领投）

（若融资轮次明细 API 未返回，从 Blog/Medium 融资公告文章补充；若完全无法获取，标注"融资明细未披露"）

---

👥 团队亮点

- **[姓名]**：[职位]。[背景：前公司/学校/成就]
- **[姓名]**：[职位]。[背景]

（必须包含 CEO/创始人。来源优先级：RootData get_item team 字段 → ser_inv 人物搜索结果 → Blog/融资公告文章）

---

📊 社群热度

社区热度[较高/中等/较低]。官推 @<handle> [X万粉丝]（需手动核查），近期推文阅读量 [X万+]，[头部/中腰部] KOL 讨论（举例：@XXX [X万粉丝]），讨论话题：[积分/空投/做任务/产品等]。
Discord：[X万用户，在线X千]（需手动核查）；TG：[X万用户，在线X千]（需手动核查）。
[是否有负面舆论]。

⚠️ X粉丝/Discord/TG 实时数据平台拦截，以上数据需手动核查。
```

---

## 阶段判断规则

| 条件 | 阶段判断 |
|------|---------|
| 代币已上线交易所 | 主网上线 / TGE 已完成 |
| 产品上线但代币未发 | 公测阶段 |
| L1/协议处于 TestNet | 测试网 |
| 仅有融资公告，无产品 | 早期 |
| 无 C 端产品，主做企业服务 | ToB 业务 |

---

## 数据来源优先级

1. **RootData API**（融资额、投资人、标签、社交链接、团队人物）
2. **官方 Blog**（近期事件时间线：融资/产品上线/TGE/空投/合作）
3. **官方文档**（产品功能描述、路线图）
4. **官网首页**（用户规模数据）
5. **Medium 博客**（历史事件补充）
6. **X/Discord/TG**（社区热度，平台登录限制，标注"需手动核查"）

---

## 常见踩坑（v1.1 新增）

1. **CEO/创始人遗漏**：RootData `team` 字段为空时，必须从 `ser_inv` 结果的 `type=3` 人物条目补充
2. **融资格式错误**：应输出 "累计总额 + 逐条：时间-金额-轮次-机构"，不能只写机构名不写金额
3. **近期进展缺失**：必须抓取官方 Blog 列表页（`/blog` 或 `/en/blog`），列出近 6 个月重大事件
4. **路线图/TGE 时间表**：不能只说"无代币"，需分析积分系统/奖励机制是否为潜在 TGE 前哨
5. **社群热度格式**：需包含粉丝数量级、KOL 覆盖（数量+粉丝段）、讨论话题类型，不能只说"需手动核查"
6. **官网抓不到内容**：大量项目官网是 JS 渲染（只返回版权信息），优先改抓 Blog 子页面或 docs
7. **Medium 404**：先试 `medium.com/@<项目名>` 再试 `medium.com/<项目名>`，都 404 则跳过

---

## 注意事项

- RootData API Key 存于 `references/config.md`
- RootData 当前套餐支持：`ser_inv`（搜索）、`get_item`（详情+投资人）
- 所有关键信息必须附带原始链接
- TG/Discord 链接若 RootData 未收录，从官网 Footer 或 Blog 文章内找

---

*v1.1 更新：2026-03-27，基于 Startale Labs 调研反馈迭代*
