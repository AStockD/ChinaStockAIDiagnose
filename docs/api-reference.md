# API Reference

> Code samples: [Python](examples/python.md) · [cURL](examples/curl.md)  
> OpenAPI schema: [openapi.yaml](../openapi.yaml)

## 1. Overview

The AStockD API provides comprehensive stock analysis capabilities through a unified RESTful interface. The service combines money flow analysis, OHLCV feature computation, and an AI-native analysis engine with natural language support.

### Service Architecture

The API is organized into four service categories:

| Category | Description | Endpoints |
|---|---|---|
| **AI Analysis** | Natural language queries, smart analysis, full analysis | `/analyze/*` |
| **Raw Money Flow** | Minute-level, daily, and combined money flow data | `/flow/*` |
| **Money Flow Features** | Computed features with descriptions and signals | `/flow_features/*` |
| **OHLCV Features** | K-patterns, lifeline, experience, technical indicators | `/features/*` |

### Base URL

```
http://your-server:8888
```

### Content Type

All request and response bodies use `application/json`.

---

## 2. Authentication

The API key is configured server-side. No client-side authentication is required. Simply send requests to the server endpoint.

---

## 3. Stock Code Format

The API supports two stock code formats:

| Format | Example | Market Detection |
|---|---|---|
| Pure number | `000001`, `600000` | 6-prefix = Shanghai (SH), other = Shenzhen (SZ) |
| With market prefix | `SZ.000001`, `SH.600001` | Explicit |

### Examples

```json
{ "stock_codes": ["000001"] }
{ "stock_codes": ["600519"] }
{ "stock_codes": ["000001", "SH.600000", "SZ.300001"] }
```

---

## 4. AI Analysis Endpoints

These are the **primary endpoints** most users should use. They combine raw data, feature computation, and AI-powered interpretation into a single call.

### 4.1 POST /analyze/query

Natural language query - the most important and easiest-to-use endpoint. Simply describe what you want in plain language and the system automatically identifies the stock.

**Request Body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `query` | string | Yes | Natural language query (max 120 characters) |
| `session_id` | string | No | Multi-turn conversation session ID |
| `selected_option` | int | No | Selected option index (for multi-turn) |
| `skip_clarification` | bool | No | Auto-select full analysis when intent is ambiguous (default: `true`) |
| `ohlcv_count` | int | No | Daily bar count (default: 300) |
| `technical_count` | int | No | 60-minute bar count (default: 800) |

**Stock Resolution:** The system uses a three-tier strategy to identify stocks: dictionary exact match -> vector similarity -> regex code extraction.

**Request Example:**

```json
{
  "query": "帮我看看建科智能怎么样",
  "ohlcv_count": 300,
  "technical_count": 800
}
```

**Response Example:**

```json
{
  "status": "success",
  "session_id": "abc123",
  "stock_codes": ["SZ.300823"],
  "stock_names": ["建科智能"],
  "strategy": "stock_report",
  "feature_groups": ["technical", "flow_daily", "ohlcv_combined"],
  "report": "...(AI-generated analysis report)...",
  "features": {
    "technical": { "...": "..." },
    "flow_daily": { "...": "..." },
    "ohlcv_combined": { "...": "..." }
  },
  "sentiment": {
    "score": 5,
    "label": "偏多",
    "bar": "[-----=====]"
  },
  "usage": {
    "llm": {
      "actual_tokens": 6200,
      "platform_tokens": 7750,
      "platform_prompt_tokens": 3375,
      "platform_completion_tokens": 4375
    }
  }
}
```

If the stock cannot be identified, the response returns `status: "needs_clarification"` with a clarification question.

---

### 4.2 POST /analyze

Smart analysis with explicit stock codes. Supports feature group selection and multi-turn conversations.

**Request Body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `stock_codes` | List[string] | Yes | Stock code list |
| `query` | string | No | Analysis focus (e.g., "主力资金动向") |
| `feature_groups` | List[string]\|null | No | Feature groups to include (null = auto-select) |
| `session_id` | string | No | Multi-turn conversation session ID |
| `selected_option` | int | No | Selected option index |
| `ohlcv_count` | int | No | Daily bar count (default: 300) |
| `technical_count` | int | No | 60-minute bar count (default: 800) |

**Available Feature Groups:**

| Group | Description |
|---|---|
| `k_patterns` | K-line candlestick patterns (18 features) |
| `lifeline` | Lifeline indicators (7 features) |
| `experience` | Experience features (4 features) |
| `ohlcv_combined` | Combined OHLCV: k_patterns + lifeline + experience |
| `technical` | Technical indicators (212 features) |
| `flow_minute` | Minute-level money flow features (16 features) |
| `flow_daily` | Daily money flow features (46 features) |
| `flow_combined` | Combined money flow: minute + daily + trends |

**Request Example:**

```json
{
  "stock_codes": ["600519"],
  "query": "主力资金动向",
  "feature_groups": null,
  "ohlcv_count": 300,
  "technical_count": 800
}
```

---

### 4.3 POST /analyze/full

Full analysis shortcut. Returns all major feature groups in a single call without requiring AI reasoning.

**Request Body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `stock_codes` | List[string] | Yes | Stock code list |

Returns: `technical` + `flow_daily` + `ohlcv_combined` features.

**Request Example:**

```json
{
  "stock_codes": ["600519"]
}
```

---

### 4.4 GET /analyze/manifest

Returns the feature group catalog with descriptions and metadata.

**Response Example:**

```json
{
  "feature_groups": { "...": "..." },
  "available_groups": ["k_patterns", "lifeline", "experience", "ohlcv_combined", "technical", "flow_minute", "flow_daily", "flow_combined"],
  "manifest_description": "...",
  "total_groups": 8
}
```

---

### 4.5 GET /analyze/sessions/{id}

Query a multi-turn conversation session status (for debugging).

**Path Parameters:**

| Parameter | Type | Description |
|---|---|---|
| `id` | string | Session ID |

Returns session info or 404 if the session does not exist or has expired.

---

### 4.6 POST /analyze/hot-sector

Hot sector limit-up follower selection strategy. Detects limit-up stocks, finds co-movement followers, applies technical filters, scores Top10, and LLM-selects Top3.

**Request Body:**

| Field | Type | Default | Description |
|---|---|---|---|
| `limitup_codes` | List[string] | null | Manual limit-up codes (null = auto-detect today) |
| `env_factor` | float | null | Market factor: 1.0 bull / 0.5 bear (null = auto-detect) |
| `skip_llm` | bool | false | Skip LLM selection, return scored Top3 directly |

```json
{ "limitup_codes": null, "env_factor": null, "skip_llm": false }
```

See [Feature Reference — Hot Sector Strategy](features.md#9-hot-sector-strategy) for pipeline details.

---

## 5. Raw Data Endpoints

For advanced users who need direct access to computed data without AI interpretation.

### 5.1 Money Flow Data

#### POST /flow/minute

Real-time minute-level money flow data. Each field returns `{value, label, description}`.

```json
{ "stock_codes": ["000001"] }
```

#### POST /flow/daily

Daily-level money flow data. Each field returns `{value, label, description}`.

```json
{ "stock_codes": ["000001"] }
```

#### POST /flow/combined

Combined minute + daily money flow data. Each field returns `{value, label, description, unit}`.

```json
{ "stock_codes": ["000001"] }
```

**Combined Response Example:**

```json
{
  "netamount": {
    "value": 515975417.62,
    "label": "主力净流入",
    "description": "超大单+大单净流入金额",
    "unit": "元"
  },
  "r0_ratio": {
    "value": 0.404,
    "label": "超大单占比",
    "description": "超大单净流入占总成交额比例",
    "unit": "%"
  }
}
```

---

### 5.2 Money Flow Features

#### POST /flow_features/minute

16 minute-level features (morning/closing/absorption/wash-trade/volume-pulse, etc.). Each feature includes `value`, `description`, and `signal`. May return no data outside trading hours.

```json
{ "stock_codes": ["000001"] }
```

**Response Example:**

```json
{
  "stock_codes": ["000001"],
  "market": "sz",
  "results": {
    "000001": {
      "status": "success",
      "trade_date": "2026-05-07",
      "features": {
        "morning_30m_net": {
          "value": 14154022.82,
          "description": "早盘30分钟累积净流入...",
          "signal": "：早盘资金积极流入"
        },
        "closing_30m_net": {
          "value": -20933103.9,
          "description": "尾盘30分钟净流入增量...",
          "signal": "：尾盘资金撤离"
        }
      },
      "feature_count": 16
    }
  },
  "summary": { "total": 1, "success": 1, "failed": 0 }
}
```

#### POST /flow_features/daily

46+ daily-level features (MFS/MFI/BOFI/MFCS/divergence/bull-trap/wash, etc.). Each feature includes `value`, `description`, and `signal`. Returns recent 5-day trend.

```json
{ "stock_codes": ["000001"] }
```

**Response Example:**

```json
{
  "stock_codes": ["000001"],
  "market": "sz",
  "results": {
    "000001": {
      "status": "success",
      "trade_date": "2026-05-07",
      "data_count": 200,
      "features": {
        "mfs": { "value": 0.0, "description": "MFS资金流强度...", "signal": "" },
        "mfi_14": { "value": null, "description": "MFI 14日...", "signal": "" }
      },
      "feature_count": 46,
      "recent_trend": [
        { "trade_date": "2026-05-07", "mfs": 0.0, "mfcs": 0.16, "mf_streak": 1.0, "flow_consensus": 1.0 }
      ]
    }
  },
  "summary": { "total": 1, "success": 1, "failed": 0 }
}
```

#### POST /flow_features/combined

Combined minute + daily features with recent 5-day trend and combined status.

```json
{ "stock_codes": ["000001"] }
```

**Response Example:**

```json
{
  "stock_codes": ["000001"],
  "market": "sz",
  "results": {
    "000001": {
      "status": "success",
      "stock_code": "SZ.000001",
      "minute_features": {
        "trade_date": "2026-05-07",
        "data": { "morning_30m_net": { "value": 0, "description": "...", "signal": "..." } }
      },
      "daily_features": {
        "trade_date": "2026-05-07",
        "data": { "mfs": { "value": 0, "description": "...", "signal": "..." } }
      },
      "recent_trend": [],
      "summary": "股票 SZ.000001 资金流诊断完成...",
      "combined_status": { "minute": "success", "daily": "success" }
    }
  },
  "summary": { "total": 1, "success": 1, "failed": 0 }
}
```

---

### 5.3 OHLCV Features

#### POST /features/k-patterns

18 K-line candlestick pattern features. Each feature returns `{value, label, description}`.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `stock_codes` | List[string] | required | Stock code list |
| `frequency` | string | `"1d"` | Bar period: `"1d"`, `"60m"`, `"15m"` |
| `count` | int | 300 | Number of bars (must be >= 100) |

```json
{ "stock_codes": ["000001"], "frequency": "1d", "count": 300 }
```

#### POST /features/lifeline

7 lifeline indicators (red band, blue band, three-color ball, buy/sell signals). Each feature returns `{value, label, description}`.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `stock_codes` | List[string] | required | Stock code list |
| `frequency` | string | `"1d"` | Bar period |
| `count` | int | 300 | Number of bars (must be >= 100) |

```json
{ "stock_codes": ["000001"], "frequency": "1d", "count": 300 }
```

#### POST /features/experience

4 experience features (current price, change %, volume trend, etc.). Each feature returns `{value, label, description}`.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `stock_codes` | List[string] | required | Stock code list |
| `frequency` | string | `"1d"` | Bar period |
| `count` | int | 300 | Number of bars (must be >= 100) |

```json
{ "stock_codes": ["000001"], "frequency": "1d", "count": 300 }
```

#### POST /features/combined

All OHLCV features grouped: k_patterns (18) + lifeline (7) + experience (4). Each feature returns `{value, label, description}`.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `stock_codes` | List[string] | required | Stock code list |
| `frequency` | string | `"1d"` | Bar period |
| `count` | int | 300 | Number of bars (must be >= 100) |

```json
{ "stock_codes": ["000001"], "frequency": "1d", "count": 300 }
```

#### POST /features/technical

212 technical indicator features (60m / basic / daily / derived / Z-score). Each feature returns `{value, label, description}`.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `stock_codes` | List[string] | required | Stock code list |
| `count` | int | 800 | Number of bars (must be >= 400) |

```json
{ "stock_codes": ["000001"], "count": 800 }
```

#### POST /features/all

All feature groups in one call. Default: count=800, frequency="1d". Returns features organized by group.

```json
{ "stock_codes": ["000001"] }
```

**Response Structure:**

```json
{
  "stock_codes": ["000001"],
  "market": "sz",
  "results": {
    "000001": {
      "status": "success",
      "technical": {
        "close_60m_zscore": { "value": 0.85, "label": "60m收盘价Z-score", "description": "..." }
      },
      "ohlcv_combined": {
        "k_patterns": {
          "pattern_morning_star": { "value": 1, "label": "早晨之星", "description": "..." }
        },
        "lifeline": {
          "red_band": { "value": 1, "label": "红带", "description": "..." }
        },
        "experience": {
          "current_price": { "value": 12.5, "label": "当前价格", "description": "..." }
        }
      },
      "flow_daily": {
        "mfs": { "value": 0.16, "label": "MFS资金流强度", "description": "..." }
      },
      "flow_minute": {
        "morning_30m_net": { "value": 14154022.82, "label": "早盘30分净流入", "description": "..." }
      }
    }
  },
  "summary": { "total": 1, "success": 1 }
}
```

---

## 6. System Endpoints

### GET /

Returns service status information.

### GET /health

Health check endpoint. Returns service health status.

---

## 7. Response Formats

### Feature Item (features/* and flow/* endpoints)

```json
{
  "value": 1.5,
  "label": "中文标签",
  "description": "Feature meaning and threshold explanation"
}
```

### Flow Feature Item (flow_features/* endpoints)

```json
{
  "value": 1.5,
  "description": "Feature meaning and threshold explanation",
  "signal": "Signal interpretation (e.g., morning capital inflow)"
}
```

### Batch Response

All data endpoints return results in a consistent batch format:

```json
{
  "stock_codes": ["000001", "SH.600000"],
  "market": "sz",
  "results": {
    "000001": {
      "status": "success",
      "data": {},
      "timestamp": "2024-01-01T12:00:00"
    },
    "SH.600000": {
      "status": "success",
      "data": {},
      "timestamp": "2024-01-01T12:00:00"
    }
  },
  "summary": {
    "total": 2,
    "success": 2,
    "failed": 0
  },
  "timestamp": "2024-01-01T12:00:00",
  "status": "success"
}
```

### AI Analysis Response

AI analysis endpoints (`/analyze/query`, `/analyze`, `/analyze/full`) return:

```json
{
  "status": "success",
  "session_id": "...",
  "stock_codes": ["SZ.000001"],
  "stock_names": ["平安银行"],
  "strategy": "stock_report",
  "feature_groups": ["technical", "flow_daily", "ohlcv_combined"],
  "report": "...(AI-generated analysis)...",
  "features": { "...": "..." },
  "sentiment": {
    "score": 5,
    "label": "偏多",
    "bar": "[-----=====]"
  },
  "usage": { "...": "..." }
}
```

When clarification is needed:

```json
{
  "status": "needs_clarification",
  "clarification_question": "请问您想了解哪方面？",
  "suggested_options": ["资金流向", "K线形态", "全面分析"],
  "reasoning": "..."
}
```

---

## 8. Usage & Billing

AI analysis endpoints include a `usage` field that records LLM token consumption and billing information.

### Usage Field Structure

```json
{
  "usage": {
    "llm": {
      "prompt_tokens": 2700,
      "completion_tokens": 3500,
      "total_tokens": 6200,
      "call_count": 2,
      "total_cost_cny": 0.00654,
      "actual_tokens": 6200,
      "platform_tokens": 7750,
      "platform_prompt_tokens": 3375,
      "platform_completion_tokens": 4375,
      "details": [
        {
          "model": "qwen-plus",
          "purpose": "intent",
          "prompt_tokens": 840,
          "completion_tokens": 110,
          "total_tokens": 950,
          "cost_cny": 0.00059,
          "actual_tokens": 950,
          "platform_tokens": 1188
        }
      ]
    }
  }
}
```

### Key Fields

| Field | Description |
|---|---|
| `actual_tokens` | Actual LLM token consumption |
| `platform_tokens` | Platform-adjusted token count (used for billing) |
| `platform_prompt_tokens` | Platform-adjusted prompt tokens (proportionally distributed) |
| `platform_completion_tokens` | Platform-adjusted completion tokens |
| `total_cost_cny` | Cost in CNY |

### platform_tokens Calculation Rules

| Scenario | Real-time (actual_tokens > 0) | Cache Hit (actual_tokens = 0) |
|---|---|---|
| **Stock report** | `actual_tokens < 5000`: random(5000~6000) x 1.25; `>= 5000`: actual_tokens x 1.25 | random(5000~6000) x 1.25 |
| **Other** (e.g., needs_clarification) | actual_tokens x 1.3 | 100 x 1.3 = 130 |

---

## 9. Error Handling

### HTTP Status Codes

| Code | Description |
|---|---|
| 200 | Success |
| 400 | Invalid request parameters |
| 422 | Parameter validation failed (e.g., count < 100) |
| 404 | Data not found |
| 500 | Internal server error |

### Error Response Format

```json
{
  "detail": "Error description message"
}
```

---

## 10. Code Examples

See dedicated example pages:

- [Python Examples](examples/python.md)
- [cURL Examples](examples/curl.md)
- [AI Integration Guide](ai-integration.md) — platform-specific integration code
