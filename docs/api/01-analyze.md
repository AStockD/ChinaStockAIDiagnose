# A股道 API - Analyze

> 本文件按分类整理 `analyze.*` 相关接口，便于按主题阅读。  
> 原始完整测试示例与真实响应仍保留在 [`99-examples.md`](./99-examples.md)。

## 1. 通用说明

- Base URL：`https://astockd.com/api/v1/open`
- 统一网关：`POST /invoke`
- 鉴权：`Authorization: Bearer <token>`
- 统一请求 / 统一响应 / 错误码 / 重试建议：见 [`00-common.md`](./00-common.md)

## 2. 接口清单

### 2.1 `analyze`

- 用途：AI 原生智能分析，适合指定股票代码并结合问题做综合诊断
- 最小请求：
```json
{
  "api": "analyze",
  "stock_codes": ["SZ.000001"],
  "query": "主力资金动向"
}
```
- 关键返回：`data.results.<code>._report`、`data.results.<code>.simple_verdict`、`usage.cost`
- 完整请求/响应样例：[`99-examples.md#analyze`](./99-examples.md#analyze)

### 2.2 `analyze.full`

- 用途：全量分析，一次返回技术面、资金面与量价综合分析
- 最小请求：
```json
{
  "api": "analyze.full",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.feature_groups`、`data.results.<code>.ohlcv`、`data.summary`
- 完整请求/响应样例：[`99-examples.md#analyze-full`](./99-examples.md#analyze-full)

### 2.3 `analyze.query`

- 用途：自然语言查询，自动识别股票并生成分析结果
- 最小请求：
```json
{
  "api": "analyze.query",
  "query": "帮我看看平安银行怎么样"
}
```
- 关键返回：`data.results`、`data.summary`、`data.reasoning`
- 完整请求/响应样例：[`99-examples.md#analyze-query`](./99-examples.md#analyze-query)

### 2.4 `analyze.manifest`

- 用途：返回特征清单与分组说明
- 最小请求：
```json
{
  "api": "analyze.manifest"
}
```
- 关键返回：`data.feature_groups`、`data.features`
- 完整请求/响应样例：[`99-examples.md#analyze-manifest`](./99-examples.md#analyze-manifest)

### 2.5 `analyze.tick_intraday`

- 用途：逐笔分时分析，适合观察盘中强弱与分时趋势
- 最小请求：
```json
{
  "api": "analyze.tick_intraday",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.simple_verdict`、`data.results.<code>._report`
- 完整请求/响应样例：[`99-examples.md#analyze-tick_intraday`](./99-examples.md#analyze-tick_intraday)

### 2.6 `analyze.hot_sector`

- 用途：热门板块联动与跟风机会分析
- 最小请求：
```json
{
  "api": "analyze.hot_sector",
  "query": "最近最强板块有哪些"
}
```
- 关键返回：`data.summary`、`data.results`
- 完整请求/响应样例：[`99-examples.md#analyze-hot_sector`](./99-examples.md#analyze-hot_sector)

### 2.7 `analyze.market_review`

- 用途：大盘复盘报告，适合早盘/全天市场总结
- 最小请求：
```json
{
  "api": "analyze.market_review",
  "query": "今日A股复盘"
}
```
- 关键返回：`data.summary`、`data.results`
- 完整请求/响应样例：[`99-examples.md#analyze-market_review`](./99-examples.md#analyze-market_review)

### 2.8 `analyze.portfolio`

- 用途：组合持仓分析，适合多只股票的整体诊断
- 最小请求：
```json
{
  "api": "analyze.portfolio",
  "stock_codes": ["SZ.000001", "SH.600036"],
  "query": "组合风险和机会"
}
```
- 关键返回：`data.summary`、`data.results`
- 完整请求/响应样例：[`99-examples.md#analyze-portfolio`](./99-examples.md#analyze-portfolio)

### 2.9 `analyze.stock_track`

- 用途：股票收益跟踪，查看近期跟踪标的表现
- 最小请求：
```json
{
  "api": "analyze.stock_track",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results`、`data.summary`
- 完整请求/响应样例：[`99-examples.md#analyze-stock_track`](./99-examples.md#analyze-stock_track)

## 3. 建议阅读顺序

如果你是第一次接入，建议按下面顺序看：

1. `analyze.query`：最快上手，自然语言查股
2. `analyze.full`：理解完整分析输出
3. `analyze`：理解结构化输入方式
4. `analyze.manifest`：查看特征分组
5. 其余 `analyze.*`：按场景使用
