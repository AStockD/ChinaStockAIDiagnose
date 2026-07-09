---
name: astockd-diagnose
description: >-
  通过 AStockD 统一 API 网关调用股票分析能力（REST）。
  先查目录再 invoke；支持资金流、特征、AI 分析、评分、策略选股等 28+ API。
  触发词：invoke、统一网关、flow.minute、features.lifeline、analyze、score、
  astockd api、股票分析、资金流、五维评分。
metadata:
  openclaw:
    requires:
      bins:
        - curl
      env:
        - ASTOCKD_API_KEY
    os:
      - macos
      - linux
      - windows
---

# AStockD 统一 API 网关

Platform API Base URL：`https://astockd.com/api/v1/open`（已固定，无需配置 `ASTOCKD_API_URL`）

所有股票分析能力通过**统一网关 `/invoke`** 调用。

## 接入方式

| 方式 | 端点 / 路径 | 文档 |
|------|-------------|------|
| REST（Platform） | `POST https://astockd.com/api/v1/open/invoke` | 本文（自包含说明） |

环境变量：

| 变量 | 说明 |
|------|------|
| `ASTOCKD_API_KEY` | **必填**，`Authorization: Bearer` 的密钥值（在 [astockd.com](https://astockd.com) 申请） |

鉴权：`Authorization: Bearer <YOUR_API_KEY>`（请求头）

| 端点（引擎） | 说明 |
|--------------|------|
| `POST /invoke` | 统一调用 |
| `GET /invoke/catalog` | API 目录 |

---

## 统一请求格式

```json
{
  "api": "API编码",
  "stock_codes": ["SH.600519"],
  "query": "自然语言查询（可选）",
  "params": { }
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `api` | string | **必填**，API 编码 |
| `stock_codes` | string[] | 股票代码列表（多数 API 使用） |
| `query` | string | 自然语言（`analyze.query` 等） |
| `params` | object | API 专属参数，**展开合并**到请求体 |

### 传参位置（重要）

| 位置 | 适用 API | 说明 |
|------|----------|------|
| 仅 `api` | `system.*`、`analyze.manifest` | 无其他参数 |
| 顶层 `stock_codes` | `flow.*`、`flow_features.*`、`features.*`、`analyze.full`、`analyze`、`score.batch`、`analyze.tick_intraday` | 股票列表，放请求体顶层 |
| 顶层 `query` | `analyze.query` | **必填**，自动识别股票，**不要**传 `stock_codes` |
| `params` 展开 | `score`、`analyze.portfolio`、`analyze.market_review` 等 | 专属字段放 `params`，服务端合并到请求体 |
| 顶层 + `params` | `features.*`、`analyze` | 如 `stock_codes` 顶层 + `params.count` |

### 五种参数模式速查

| 模式 | 必填字段 | 代表 API |
|------|----------|----------|
| A 无参 | 仅 `api` | `system.health`、`analyze.manifest` |
| B 股票列表 | `api` + `stock_codes` | `flow.minute`、`features.lifeline`、`score.batch` |
| C 股票+扩展 | `api` + `stock_codes` + `params` 可选 | `features.*`（+`frequency`/`count`）、`analyze`（+`query`/`session_id`） |
| D 自然语言 | `api` + `query` | `analyze.query` |
| E 仅 params | `api` + `params` | `analyze.portfolio`、`scores.recent`、`search.tavily` |

### 易混淆点

| 错误 | 正确 |
|------|------|
| `score` 传 `stock_codes` | 用 `params.stock_code`（**单数**，一只） |
| `analyze.query` 传 `stock_codes` | 只传顶层 `query`，系统自动识别股票 |
| `analyze.portfolio` 传 `stock_codes` | 用 `params.holdings`（含 code/cost_price/quantity） |
| 可选参数不知道放哪 | 放 `params` 即可，会展开合并；或先查 catalog |

### REST 调用示例

```bash
curl -X POST "https://astockd.com/api/v1/open/invoke" \
  -H "Authorization: Bearer ${ASTOCKD_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "api": "flow.minute",
    "stock_codes": ["SH.600519"]
  }'
```

```bash
curl "https://astockd.com/api/v1/open/invoke/catalog" \
  -H "Authorization: Bearer ${ASTOCKD_API_KEY}"
```

---

## 统一响应格式

```json
{
  "code": 200,
  "message": "ok",
  "data": { },
  "usage": {
    "cost": 0.1,
    "balance": 100.0,
    "balance_after": 99.9
  }
}
```

| code | 说明 |
|------|------|
| 200 | 成功 |
| 400 / 4000 | 参数错误 |
| 4004 | 未知 API 编码（上游） |
| 4005 / 429 | Credit 余额不足 |
| 500 / 5000 | 服务内部错误 |

---

## 支持的全部 API

### 系统（免费）

| API | 说明 | 配额 |
|-----|------|------|
| `system.status` | 服务状态检查 | 0 |
| `system.health` | 健康检查 | 0 |

### 资金流-原始

| API | 说明 | 配额 |
|-----|------|------|
| `flow.minute` | 分钟级资金流 | 0.1 |
| `flow.daily` | 日级资金流 | 0.1 |
| `flow.combined` | 合并资金流（分钟+日级） | 0.2 |

### 资金流-特征

| API | 说明 | 配额 |
|-----|------|------|
| `flow_features.minute` | 分钟级资金流特征 | 0.2 |
| `flow_features.daily` | 日级资金流特征 | 0.2 |
| `flow_features.combined` | 合并资金流特征 | 0.4 |

### 特征计算

| API | 说明 | 配额 |
|-----|------|------|
| `features.k_patterns` | K 线形态特征（18 种） | 0.2 |
| `features.lifeline` | 生死线指标（7 个） | 0.2 |
| `features.experience` | 经验特征 | 0.2 |
| `features.combined` | 合并特征 | 0.6 |
| `features.technical` | 技术特征集（212 个） | 0.6 |
| `features.all` | 全量特征 | 1.2 |

### AI 分析

| API | 说明 | 配额 |
|-----|------|------|
| `analyze` | AI 原生智能分析 | 5 |
| `analyze.full` | 全量分析 | 3 |
| `analyze.query` | 自然语言查询（自动识别股票） | 3 |
| `analyze.manifest` | 特征清单 | 0 |

### 评分

| API | 说明 | 配额 |
|-----|------|------|
| `score` | 单股五维评分 | 1.5 |
| `score.batch` | 批量五维评分 | 1.5 × 股票数 |
| `scores.recent` | 近期高分评分 | 3 |

### 盘中

| API | 说明 | 配额 |
|-----|------|------|
| `analyze.tick_intraday` | 逐笔分时特征 | 1 |

### 策略 / 复盘

| API | 说明 | 配额 |
|-----|------|------|
| `analyze.hot_sector` | 热门板块联动跟风 | 3 |
| `analyze.market_review` | 大盘复盘报告 | 3 |
| `analyze.portfolio` | 组合持仓分析 | 3 |
| `analyze.stock_track` | 股票收益跟踪 | 0 |
| `strategy.screener` | 策略选股 | 3 |

### 搜索

| API | 说明 | 配额 |
|-----|------|------|
| `search.tavily` | Tavily 搜索 | 1 |

配额单位为 **Credit**。参数细节以 catalog 为准；下表列出各 API **必填参数**。

---

## 各 API 必填 / 主要参数

| API | 必填 | 可选（放顶层或 params） |
|-----|------|-------------------------|
| `system.status` / `system.health` | 无 | — |
| `flow.minute` / `flow.daily` / `flow.combined` | `stock_codes` | — |
| `flow_features.*` | `stock_codes` | — |
| `features.k_patterns` / `lifeline` / `experience` / `combined` | `stock_codes` | `frequency`（默认 1d）、`count`（默认 300） |
| `features.technical` | `stock_codes` | `count`（默认 800，60m 条数） |
| `features.all` | `stock_codes` | — |
| `analyze` | `stock_codes` | `query`、`feature_groups`、`session_id`、`selected_option`、`ohlcv_count`、`technical_count` |
| `analyze.full` | `stock_codes` | — |
| `analyze.query` | `query`（顶层） | `session_id`、`selected_option`、`ohlcv_count`、`technical_count` |
| `analyze.manifest` | 无 | — |
| `score` | `params.stock_code`（单数） | — |
| `score.batch` | `stock_codes`（最多 100） | — |
| `scores.recent` | 无 | `params.min_score`、`hours`、`source` |
| `analyze.tick_intraday` | `stock_codes`（最多 50） | `params.tick_num`（默认 10000） |
| `analyze.hot_sector` | 无 | `params.limitup_codes`、`env_factor`、`skip_llm` |
| `analyze.market_review` | 无 | `params.period`（morning/noon/full）、`skip_llm`、`use_cache`、`date` |
| `analyze.portfolio` | `params.holdings` | `ohlcv_count`、`technical_count`；holdings 含 code/cost_price/quantity，最多 10 只 |
| `analyze.stock_track` | 无 | `params.days`（默认 4） |
| `strategy.screener` | 无 | `params.top_n`（默认 3） |
| `search.tavily` | 无 | `params.query`、`stock_code`、`stock_name`、`max_results` |

---

## 常用调用示例

### 分钟级资金流

```json
{ "api": "flow.minute", "stock_codes": ["SH.600519"] }
```

### 生死线指标

```json
{ "api": "features.lifeline", "stock_codes": ["SH.600519"] }
```

### 自然语言分析

```json
{ "api": "analyze.query", "query": "帮我看看贵州茅台怎么样" }
```

### 单股五维评分

```json
{ "api": "score", "params": { "stock_code": "SH.600519" } }
```

### 批量评分

```json
{
  "api": "score.batch",
  "stock_codes": ["SH.600519", "SZ.000001"]
}
```

### 大盘复盘

```json
{
  "api": "analyze.market_review",
  "params": { "period": "full", "skip_llm": true, "use_cache": true }
}
```

### 组合持仓分析

```json
{
  "api": "analyze.portfolio",
  "params": {
    "holdings": [
      { "code": "SZ.300750", "cost_price": 200.0, "quantity": 100 },
      { "code": "SH.600519", "cost_price": 1800.0, "quantity": 50 }
    ]
  }
}
```

### 策略选股

```json
{ "api": "strategy.screener", "params": { "top_n": 3 } }
```

### AI 智能分析（指定股票 + 分析问题）

```json
{
  "api": "analyze",
  "stock_codes": ["SH.600519"],
  "params": { "query": "主力资金动向", "ohlcv_count": 300 }
}
```

### 特征带条数

```json
{
  "api": "features.technical",
  "stock_codes": ["SZ.000001"],
  "params": { "count": 800 }
}
```

### 近期高分股

```json
{
  "api": "scores.recent",
  "params": { "min_score": 60, "hours": 24 }
}
```

### Tavily 搜索

```json
{
  "api": "search.tavily",
  "params": { "query": "贵州茅台最新消息", "max_results": 5 }
}
```

### 健康检查（无参）

```json
{ "api": "system.health" }
```

---

## Agent 工作流

1. 不确定 API 时：REST `GET /invoke/catalog`
2. 按用户意图选择 `api`，构造 `stock_codes` / `query` / `params`
3. REST `POST /invoke`
4. 解读 `data`，告知用户 `usage.cost` 消耗

## 股票代码格式

`000001`、`SZ.000001`、`SH.600519`（6 开头自动识别为沪市）

## Skill 安装

```bash
npx skills add AStockD/ChinaStockAIDiagnose@astockd-diagnose
```

OpenClaw 也可手动安装（先克隆仓库）：

```bash
git clone https://github.com/AStockD/ChinaStockAIDiagnose.git
cp -r ChinaStockAIDiagnose/skills/astockd-diagnose ~/.openclaw/skills/
```

配置环境变量 `ASTOCKD_API_KEY` 后，对 Agent 说「帮我看看比亚迪怎么样」测试接入。

---

## 备注

本 Skill 文档为自包含说明：请求格式、鉴权、参数规则、示例均以本文为准，不依赖其他外部文档。
