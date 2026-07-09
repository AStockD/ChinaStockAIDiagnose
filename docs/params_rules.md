## 参数传参规则

### 五种模式

| 模式 | 必填 | 代表 API |
|------|------|----------|
| A 无参 | 仅 `api` | `system.health`、`analyze.manifest` |
| B 股票列表 | `api` + `stock_codes` | `flow.minute`、`score.batch` |
| C 股票+扩展 | `api` + `stock_codes` + `params` | `features.*`、`analyze` |
| D 自然语言 | `api` + `query` | `analyze.query` |
| E 仅 params | `api` + `params` | `analyze.portfolio`、`search.tavily` |

### 易混淆

| 错误 | 正确 |
|------|------|
| `score` 用 `stock_codes` | `params.stock_code`（单数） |
| `analyze.query` 传 `stock_codes` | 只传 `query` |
| `analyze.portfolio` 传 `stock_codes` | `params.holdings` |

### 各 API 必填参数

| API | 必填 | 主要可选 |
|-----|------|----------|
| `flow.*` / `flow_features.*` | `stock_codes` | — |
| `features.*` | `stock_codes` | `frequency`、`count` |
| `analyze` | `stock_codes` | `query`、`session_id`、`ohlcv_count` |
| `analyze.query` | `query` | `session_id`、`ohlcv_count` |
| `score` | `params.stock_code` | — |
| `score.batch` | `stock_codes` | 最多 100 只 |
| `analyze.portfolio` | `params.holdings` | 最多 10 只 |
| `analyze.market_review` | 无 | `period`、`skip_llm`、`date` |
| `strategy.screener` | 无 | `top_n` |
| `search.tavily` | 无 | `query`、`max_results` |

股票代码：`000001`、`SZ.000001`、`SH.600519`
