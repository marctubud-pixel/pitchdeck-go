# C · 架构 v0.1

## 一句话

以「比稿项目」为单位的状态机：Agent 干活，闸门由人拍板，结果回流成档案。

## 流程

```
Brief
  │
  ▼
[接单 Intake] ──▶ [先例检索*] ──▶ [守门 Qualify] ──▶ [情报 Research]
                                                          │
                                                          ▼
                                                    [策略 Strategy]
                                                          │
                                          ┌───────────────┴───────────────┐
                                          │  执行总监：选模块 / 定故事线     │
                                          └───────────────┬───────────────┘
                                                          ▼
                          ┌──────────┬──────────┬──────────┬──────────┐
                          │ 创意      │ 媒介?    │ 内容?    │ 电商?    │
                          └──────────┴──────────┴──────────┴──────────┘
                                                          │
                                                          ▼
                                                    [合稿 Compose]
                                                          │
                                          ┌───────────────┴───────────────┐
                                          │ 验收官 QA · 评审官 Critic      │
                                          └───────────────┬───────────────┘
                                                          ▼
                                                    [交付 Deliver]
                                                          │
                                                          ▼
                                                    [复盘 Debrief]
                                                          │
                                                          ▼
                                                    [档案 Archivist]
                                                          │
                                                          └──▶ 下一单的先例检索

* 先例检索 v0.2 上线；v0.1 只存不取，但字段按可检索设计。
```

## 角色

| 角色 | 回答的问题 | 何时跑 | 否决权 |
|---|---|---|---|
| 执行总监（AI） | 做什么、先做什么、听谁的 | 全程 | 有（在 AI 层） |
| 总导演（人） | 拍板 | 5 个闸门 | 最高 |
| 接单员 | brief 说了什么、没说什么 | 入口 | 无 |
| 档案管理员 | 以前类似的事怎么做的 | 入口 + 结束 | 无 |
| 守门员 | 值不值得接 | 入口后 | 建议权 |
| 情报员 | 外面发生了什么 | 中段 | 无 |
| 策略 | 该占哪个角度 | 中段 | 无 |
| 创意 | 怎么表达 | 中段（可选） | 无 |
| 媒介 | 怎么投、花多少 | 中段（可选） | 无 |
| 合稿 | 拼起来顺不顺 | 后段 | 无 |
| 验收官 | 做对了吗、全不全 | 每阶段 | 打回权 |
| 评审官 | 能赢吗、会被问倒吗 | 策略 / 终稿 | 只给判断 |

## 五个人工闸门

| 闸门 | 位置 | 人的动作 |
|---|---|---|
| G1 | 接单后 | 确认任务书、补反问 |
| G2 | 判断后 | 决定接不接、报多少 |
| G3 | 策略卡后 | 选方向、定调 |
| G4 | 合稿后 | 定稿 |
| G5 | 答辩后 | 录输赢原因 |

## 数据模型（最小版）

```
Project     id, client, industry, budget, deadline, state, owner
Brief       project_id, raw, structured, gaps, questions
Evidence    project_id, claim, source_url, source_date, confidence
Strategy    project_id, angle, insight, proof, budget_focus, chosen
Asset       project_id, type, content, version, model_used, cost
Critique    project_id, rubric_scores, issues, compliance_flags
Outcome     project_id, result(won/lost), reason, client_feedback
Playbook    industry, stage, winning_patterns, losing_patterns
Client      name, industry, preferences, history, price_sensitivity
```

## 设计原则

1. 状态机 > 聊天：项目有状态、有产物、有审批。
2. 证据 > 生成：所有结论带来源，缺的标「待补充」。
3. 闸门 > 全自动：5 个 gate，保证可控、可解释、可背锅。
4. 复盘 > 交付：输赢数据是唯一越用越强的护城河。
5. 单项目成本 < ¥50，否则接不住低价单。
