# Quick Start

Get your first stock analysis result in under 5 minutes.

## Prerequisites

- A running AStockD API server (default: `http://localhost:8888`)
- Optional: API key if your deployment requires authentication

## 1. Verify the Service

```bash
curl http://localhost:8888/health
```

## 2. Natural Language Query (Recommended)

The easiest way to analyze a stock — just say the name:

```bash
curl -X POST http://localhost:8888/analyze/query \
  -H "Content-Type: application/json" \
  -d '{"query": "帮我看看贵州茅台的最新走势"}'
```

## 3. Python Client

```python
import requests

response = requests.post(
    "http://localhost:8888/analyze/query",
    headers={"Content-Type": "application/json"},
    json={"query": "贵州茅台的技术指标如何？"},
    timeout=120,
)
result = response.json()
print(result.get("summary") or result.get("report"))
```

## 4. Response Overview

A successful analysis response includes:

| Field | Description |
|-------|-------------|
| `status` | `success` or `needs_clarification` |
| `strategy` | Analysis type: `stock_diagnosis`, `hot_sector`, `portfolio` |
| `summary` | AI-generated analysis report |
| `usage` | LLM token usage and billing info |

## Next Steps

- [API Reference](api-reference.md) — all endpoints and parameters
- [AI Integration Guide](ai-integration.md) — connect to Dify, MCP, Coze, etc.
- [Feature Reference](features.md) — 300+ financial features explained
