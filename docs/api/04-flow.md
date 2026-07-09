# A股道 API - Flow

> 本文件按分类整理资金流与资金流特征相关接口，便于按主题阅读。  
> 原始完整测试示例与真实响应仍保留在 [`99-examples.md`](./99-examples.md)。

## 1. 通用说明

- Base URL：`https://astockd.com/api/v1/open`
- 统一网关：`POST /invoke`
- 鉴权：`Authorization: Bearer <token>`
- 统一请求 / 统一响应 / 错误码 / 重试建议：见 [`00-common.md`](./00-common.md)

## 2. 原始资金流

### 2.1 `flow.minute`

- 用途：分钟级资金流，适合看盘中主力净流入、超大单与量能变化
- 最小请求：
```json
{
  "api": "flow.minute",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.data.netamount`、`data.results.<code>.data.price`、`data.results.<code>.data.change_pct`
- 完整请求/响应样例：[`99-examples.md#flow-minute`](./99-examples.md#flow-minute)

### 2.2 `flow.daily`

- 用途：日级资金流，适合看历史主力资金趋势与均值表现
- 最小请求：
```json
{
  "api": "flow.daily",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.data.latest`、`data.results.<code>.data.mean`、`data.results.<code>.data.data_count`
- 完整请求/响应样例：[`99-examples.md#flow-daily`](./99-examples.md#flow-daily)

### 2.3 `flow.combined`

- 用途：合并资金流，同时返回分钟级与日级视角
- 最小请求：
```json
{
  "api": "flow.combined",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.minute`、`data.results.<code>.daily`
- 完整请求/响应样例：[`99-examples.md#flow-combined`](./99-examples.md#flow-combined)

## 3. 资金流特征

### 3.1 `flow_features.minute`

- 用途：分钟级资金流特征，适合分析早盘/尾盘、洗盘、抢筹等盘中特征
- 最小请求：
```json
{
  "api": "flow_features.minute",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.latest_features`、`data.results.<code>.data_count`
- 完整请求/响应样例：[`99-examples.md#flow_features-minute`](./99-examples.md#flow_features-minute)

### 3.2 `flow_features.daily`

- 用途：日级资金流特征，适合分析 MFS/MFI/MFCS、资金趋势与背离
- 最小请求：
```json
{
  "api": "flow_features.daily",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.latest_features`、`data.results.<code>.data_count`
- 完整请求/响应样例：[`99-examples.md#flow_features-daily`](./99-examples.md#flow_features-daily)

### 3.3 `flow_features.combined`

- 用途：合并资金流特征，同时汇总分钟级与日级信号
- 最小请求：
```json
{
  "api": "flow_features.combined",
  "stock_codes": ["SZ.000001"]
}
```
- 关键返回：`data.results.<code>.minute.latest_features`、`data.results.<code>.daily.latest_features`
- 完整请求/响应样例：[`99-examples.md#flow_features-combined`](./99-examples.md#flow_features-combined)

## 4. 建议阅读顺序

如果你是第一次接入资金流能力，建议按下面顺序看：

1. `flow.minute`：理解实时资金流原始数据
2. `flow.daily`：理解日级资金趋势
3. `flow.combined`：理解分钟 + 日级的组合输出
4. `flow_features.minute`：看盘中信号
5. `flow_features.daily`：看趋势与背离
6. `flow_features.combined`：看汇总后的综合特征
