# A股道 API - Features

> 本文件按分类整理 `features.*` 相关接口，便于按主题阅读。  
> 原始完整测试示例与真实响应仍保留在 [`99-examples.md`](./99-examples.md)。

## 1. 通用说明

- Base URL：`https://astockd.com/api/v1/open`
- 统一网关：`POST /invoke`
- 鉴权：`Authorization: Bearer <token>`
- 统一请求 / 统一响应 / 错误码 / 重试建议：见 [`00-common.md`](./00-common.md)

## 2. 接口清单

### 2.1 `features.k_patterns`

- 用途：K 线形态特征（吞没、十字星、锤子线等）
- 最小请求：
```json
{
  "api": "features.k_patterns",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.latest_features.pattern_*`、`data.results.<code>.latest_features.pattern_score`
- 完整请求/响应样例：[`99-examples.md#features-k_patterns`](./99-examples.md#features-k_patterns)

### 2.2 `features.lifeline`

- 用途：生死线指标（红蓝带、三色球、买卖点）
- 最小请求：
```json
{
  "api": "features.lifeline",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.latest_features.red_band`、`data.results.<code>.latest_features.blue_band`
- 完整请求/响应样例：[`99-examples.md#features-lifeline`](./99-examples.md#features-lifeline)

### 2.3 `features.experience`

- 用途：经验特征（基础量价、趋势与经验信号）
- 最小请求：
```json
{
  "api": "features.experience",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.latest_features`（经验因子集合）
- 完整请求/响应样例：[`99-examples.md#features-experience`](./99-examples.md#features-experience)

### 2.4 `features.combined`

- 用途：合并特征（K 线 + 生死线 + 经验）
- 最小请求：
```json
{
  "api": "features.combined",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.k_patterns`、`data.results.<code>.lifeline`、`data.results.<code>.experience`
- 完整请求/响应样例：[`99-examples.md#features-combined`](./99-examples.md#features-combined)

### 2.5 `features.technical`

- 用途：技术特征集（核心技术指标）
- 最小请求：
```json
{
  "api": "features.technical",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.latest_features.macd_*`、`rsi_*`、`ma_*`
- 完整请求/响应样例：[`99-examples.md#features-technical`](./99-examples.md#features-technical)

### 2.6 `features.all`

- 用途：全量特征，一次性返回全部主要特征
- 最小请求：
```json
{
  "api": "features.all",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.latest_features`（全量字段，建议按需解析）
- 完整请求/响应样例：[`99-examples.md#features-all`](./99-examples.md#features-all)
