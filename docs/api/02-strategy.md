# A股道 API - Strategy

> 本文件按分类整理策略与搜索相关接口。  
> 原始完整测试示例与真实响应仍保留在 [`99-examples.md`](./99-examples.md)。

## 1. 通用说明

- Base URL：`https://astockd.com/api/v1/open`
- 统一网关：`POST /invoke`
- 鉴权：`Authorization: Bearer <token>`
- 统一请求 / 统一响应 / 错误码 / 重试建议：见 [`00-common.md`](./00-common.md)

## 2. 接口清单

### 2.1 `strategy.screener`

- 用途：策略筛股，基于条件过滤候选标的
- 最小请求：
```json
{
  "api": "strategy.screener",
  "params": {
    "top_n": 3
  }
}
```
- 关键返回：`data.result.top*_selected`、`data.result.llm_report`、`data.reasoning`
- 返回说明：无结果时可能返回 `code: 204`（`message: "无结果"`）
- 完整请求/响应样例：[`99-examples.md#strategy-screener`](./99-examples.md#strategy-screener)

### 2.2 `search.tavily`

- 用途：外部资讯搜索，辅助研究与分析上下文
- 最小请求：
```json
{
  "api": "search.tavily",
  "query": "A股 今日 行情",
  "params": {
    "max_results": 3
  }
}
```
- 关键返回：`data.answer`、`data.results[]`、`data.query`
- 完整请求/响应样例：[`99-examples.md#search-tavily`](./99-examples.md#search-tavily)
