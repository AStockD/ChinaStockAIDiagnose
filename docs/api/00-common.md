# A股道 API - Common

> 本文件为所有分类 API 文档的**通用说明**（请求/响应/错误处理）。  

## 1. Base URL 与鉴权

- Base URL：`https://astockd.com/api/v1/open`
- 统一网关：`POST /invoke`
- 鉴权：
  - `Authorization: Bearer <你的密钥>`

## 2. 统一请求格式

所有能力通过 `POST /invoke`，请求体为 JSON。

```json
{
  "api": "<api_code>",
  "stock_codes": ["SZ.000001"],
  "query": "...",
  "params": {}
}
```

- `api`：接口编码（例如 `analyze.query` / `flow.minute`）
- `stock_codes`：股票列表（部分接口必填；部分接口也可只传 `query`）
- `query`：自然语言问题（`analyze.*` / `search.*` 常用）
- `params`：结构化参数（如 `score` 的 `stock_code`、`strategy.screener` 的 `top_n`）

## 3. 统一响应格式

```json
{
  "code": 200,
  "message": "ok",
  "data": {},
  "usage": {
    "cost": 1.5, 
    "balance": 100.0, 
    "balance_after": 98.5
  }
}
```

- `code`：业务状态码（**不是** HTTP status；以响应体为准）
- `message`：人类可读的状态/错误说明
- `data`：各接口返回数据
- `usage.cost`：本次调用扣费（若为 0 代表不计费/未扣费）
- `usage.balance` / `usage.balance_after`：余额信息（若服务端开启返回）

## 4. 常见状态码与处理建议

### 4.1 错误码（按响应体 `code`）

| code | 说明 | 处理建议 |
|------|------|----------|
| 200 | 成功 | 正常解析 `data` |
| 204 | 无结果 | 当作空结果分支（如 `strategy.screener` 可能返回 `message: "无结果"`）；可提示用户放宽条件或扩大样本 |
| 400 / 4000 | 参数错误 | 检查 `api`、必填字段、`stock_codes/query/params` 结构与类型；展示 `message` |
| 4004 | 未知 API 编码 | 检查 `api` 是否拼写正确、是否在文档/API 清单内；展示 `message` |
| 4005 / 429 | Credit 余额不足 | 提示充值/更换 key；避免重试 |
| 500 / 5000 | 服务内部错误 | 展示 `message`；记录 `api` + 关键参数（避免记录完整 token）；按“重试与超时”做有限重试 |

## 5. 重试与超时（建议）

- **客户端超时**：建议设置 30s（`analyze.full` 可能更久，可放宽）
- **重试**：
  - 适合重试：超时、限流、临时服务不可用（**指数退避**，最多 2-3 次）
  - 不建议重试：鉴权失败、参数校验失败、`code=204`

## 6. 返回解析推荐（给集成方）

- `analyze.*`：优先读 `data.results.<code>._report`（可直接展示给用户）
- `score*`：优先读 `data.overall_score / data.verdict / data.dimensions`
- `flow*` / `features*`：字段很多，建议按“关键返回”小节按需解析
