# cURL Examples

Replace `localhost:8888` with your server address.

## AI Analysis

```bash
# Natural language query (recommended)
curl -X POST http://localhost:8888/analyze/query \
  -H "Content-Type: application/json" \
  -d '{"query": "帮我看看建科智能怎么样"}'

# Full analysis with stock code
curl -X POST http://localhost:8888/analyze/full \
  -H "Content-Type: application/json" \
  -d '{"stock_codes": ["600519"]}'

# Smart analysis with query focus
curl -X POST http://localhost:8888/analyze \
  -H "Content-Type: application/json" \
  -d '{"stock_codes": ["600519"], "query": "主力资金动向"}'
```

## Feature Endpoints

```bash
# K-line patterns
curl -X POST http://localhost:8888/features/k-patterns \
  -H "Content-Type: application/json" \
  -d '{"stock_codes": ["000001"], "frequency": "1d", "count": 300}'

# Combined money flow features
curl -X POST http://localhost:8888/flow_features/combined \
  -H "Content-Type: application/json" \
  -d '{"stock_codes": ["000001"]}'

# All features (technical + money flow + OHLCV)
curl -X POST http://localhost:8888/features/all \
  -H "Content-Type: application/json" \
  -d '{"stock_codes": ["000001"]}'

# Batch stocks
curl -X POST http://localhost:8888/flow_features/daily \
  -H "Content-Type: application/json" \
  -d '{"stock_codes": ["000001", "600519", "SZ.300001"]}'
```

## System

```bash
curl http://localhost:8888/health
curl http://localhost:8888/analyze/manifest
```

See also: [Quick Start](../quick-start.md) · [API Reference](../api-reference.md)
