# A股道 API - Score

> 本文件按分类整理评分相关接口，便于按主题阅读。  
> 原始完整测试示例与真实响应仍保留在 [`99-examples.md`](./99-examples.md)。

## 1. 通用说明

- Base URL：`https://astockd.com/api/v1/open`
- 统一网关：`POST /invoke`
- 鉴权：`Authorization: Bearer <token>`
- 统一请求 / 统一响应 / 错误码 / 重试建议：见 [`00-common.md`](./00-common.md)

## 2. 接口清单

### 2.1 `score`

- 用途：单股五维评分
- 最小请求：
```json
{
  "api": "score",
  "params": {
    "stock_code": "SZ.000001"
  }
}
```
- 关键返回：`data.overall_score`、`data.verdict`、`data.dimensions`、`data.risk_hint`
- 完整请求/响应样例：[`99-examples.md#score`](./99-examples.md#score)

### 2.2 `score.batch`

- 用途：批量五维评分
- 最小请求：
```json
{
  "api": "score.batch",
  "stock_codes": ["SZ.000001", "SH.600036"]
}
```
- 关键返回：`data.results[]`、`data.total`、`data.success`、`data.failed`
- 完整请求/响应样例：[`99-examples.md#score-batch`](./99-examples.md#score-batch)

### 2.3 `scores.recent`

- 用途：近期高分评分记录查询
- 最小请求：
```json
{
  "api": "scores.recent",
  "params": {
    "min_score": 60,
    "hours": 48
  }
}
```
- 关键返回：`data.total`、`data.scores[]`、`data.min_score`、`data.hours`
- 完整请求/响应样例：[`99-examples.md#scores-recent`](./99-examples.md#scores-recent)
