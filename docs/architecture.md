# C · 架构 v0.2

> 同步至 ADR 0005（九步五闸）、0004（三审 + 多模型通道）、0003（单人优先）。

## 一句话

以「比稿项目」为单位的状态机：Agent 干活，每个阶段先由验收官查完整性，再交人拍板；结果回流成档案。

## 流程

```
Brief（语音 / PDF / 微信）
  │
  ▼
[S0 接单 · 判断] ──── G1  任务书对不对 / 接不接 / 报多少
  │
  ▼
[S1 启动] ─────────── G2  论点 / 模块 / 骨架 / 未决问题
  │
  ▼
[S2 调研]  （无闸门，弹药）
  │
  ▼
[S3 策略] ─────────── G3  三个互斥方向里选一个
  │
  ▼
[S4 生产]  （AI 出活 + 人提意见，循环收敛）
  │        文本 / 图像 / 排版 三条通道
  ▼
[S5 合稿] ─────────── G4  定稿
  │
  ▼
[S6 终验]  （整份稿子：必答项 / 合规 / 答辩）
  │
  ▼
[S7 交付]  （5 件套 + 只读链接）
  │
  ▼
[S8 复盘] ─────────── G5  赢在哪 / 输在哪 / 下次改什么
  │
  ▼
[档案] ──▶ 下一单的先例检索（v0.2；v0.1 只存不取）
```

每个阶段结束固定跑一次 **验收官**（打回权，最多 2 轮）；验收不过，闸门不开放。

## 角色

### 人

| 角色 | 回答的问题 | 何时在场 |
|---|---|---|
| 总导演 / 主理人 | 拍板 | 5 个闸门 |
| 客户 | 看，不改 | 只读链接 |

### AI

| 角色 | 回答的问题 | 何时跑 | 权力 |
|---|---|---|---|
| 接单员 | brief 说了什么、没说什么 | S0 | 无 |
| 守门员 | 值不值得接、多少人天 | S0 | 建议权（报价由人输） |
| 执行总监 | 做什么、先做什么、砍什么 | S1 全程 | 在 AI 层 |
| 调研员 | 外面发生了什么 | S2 | 无 |
| 策略 | 该占哪个角度 | S3 | 无 |
| 生产 | 按策略出资产 | S4 | 无 |
| 合稿 | 拼起来顺不顺、哪里打架 | S5 | 无 |
| **验收官** | 做**对**了吗、全不全 | 每个阶段 | **打回权，最多 2 轮** |
| **评审官** | 能**赢**吗、会被问倒吗 | 策略 / 终稿 | 只给判断 |
| **合规扫描** | 踩线没有 | 生产 / 终验 | **阻断** |
| 档案管理员 | 以前类似的事怎么做的 | 入口 + 结束 | 无 |

边界：验收官不评价「好不好」，评审官不给打回权，合规只管红线。

## 五个闸门

| 闸门 | 位置 | 人的动作 |
|---|---|---|
| G1 | S0 接单 · 判断后 | 确认任务书、决定接不接、输入报价 |
| G2 | S1 启动后 | 回答 A 类未决问题、确认论点与骨架 |
| G3 | S3 策略后 | 三个方向里选一个 |
| G4 | S5 合稿后 | 定稿 |
| G5 | S8 复盘后 | 录输赢原因 |

放行规则：**未决问题驱动**。A 类（不回答无法继续）必须人工；B 类已定只通知；C 类假设错了再改。零条未决视为装懂，打回。不看预算、不看案例数、不看置信度。

## 生产通道

| 资产 | 通道 | v0.1 |
|---|---|---|
| 文案 / 脚本 / 策略文本 | 文本（Claude / DeepSeek） | ✅ |
| KV / 海报 / 配图 | 图像（Seedream） | ✅ |
| Deck 排版 | 模板引擎（python-pptx） | ✅ |
| 短视频成片 | 视频（Seedance / Kling） | 接口预留 |
| 配音 | TTS（火山 / 讯飞） | 接口预留 |

统一接口 `generate(asset_type, prompt, refs)`；换模型是改配置，不是改代码。

## 数据模型（最小版）

```
Project     id, client, industry, budget, deadline, state, owner, quote
Brief       project_id, raw, structured, gaps
Question    project_id, class(A/B/C), text, answer, decided_by
Evidence    project_id, claim, source_url, source_date
Strategy    project_id, angle, insight, proof, budget_focus, chosen
Asset       project_id, type, channel, content, version, model_used, cost
Critique    project_id, stage, type(qa/critic/compliance), verdict, items
Outcome     project_id, result(won/lost/no_go), reason, client_feedback
Playbook    industry, stage, winning_patterns, losing_patterns
Client      name, industry, preferences, history, price_sensitivity
```

所有产物带 `actor` / `version`，为将来加人留口；v0.1 不建 `team` / `member` / 权限表。

## 设计原则

1. 状态机 > 聊天：项目有状态、有产物、有审批；对话只是入口，每句话都落到状态上。
2. 证据 > 生成：结论带来源，缺的标「待补充」，不编。
3. 验收 > 闸门：机器先查完整，人再判方向。
4. 复盘 > 交付：输赢数据是唯一越用越强的护城河，且先给反例。
5. 单人 > 多人：v0.1 一个项目一个主理人；多人从只读链接开始。
6. 单项目成本 < ¥50、人时 < 6h，否则接不住低价单。
