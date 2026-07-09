# A股道 API - System

> 本文件按分类整理系统与健康检查接口。  
> 原始完整测试示例与真实响应仍保留在 [`99-examples.md`](./99-examples.md)。

## 1. 通用说明

- Base URL：`https://astockd.com/api/v1/open`
- 统一网关：`POST /invoke`
- 鉴权：`Authorization: Bearer <token>`
- 统一请求 / 统一响应 / 错误码 / 重试建议：见 [`00-common.md`](./00-common.md)

## 2. 接口清单

### 2.1 `system.status`

- 用途：服务状态信息
- 最小请求：
```json
{
  "api": "system.status"
}
```
- 关键返回：`data.service`、`data.version`、`data.status`、`data.services[]`
- 完整请求/响应样例：[`99-examples.md#system-status`](./99-examples.md#system-status)

### 2.2 `system.health`

- 用途：健康检查
- 最小请求：
```json
{
  "api": "system.health"
}
```
- 关键返回：`data.status`、`data.trading_hours`、`data.cache`、`data.timestamp`
- 完整请求/响应样例：[`99-examples.md#system-health`](./99-examples.md#system-health)
