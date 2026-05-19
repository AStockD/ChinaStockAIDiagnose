---
name: stock_analysis
description: A股智能分析 - 技术指标/资金流/K线形态/自然语言推理分析
metadata: {"openclaw": {"requires": {"bins": ["curl"], "env": ["ASTOCKD_API_URL"]}, "os": ["macos", "linux"]}}
---

# AStockD 股票分析 Skill

> Full skill definition: [skills/stock-analysis/SKILL.md](skills/stock-analysis/SKILL.md)

AI agent skill for A-share stock analysis via the AStockD API. Supports natural language queries, multi-turn clarification, and direct feature group selection.

## Quick Usage

```bash
curl -s -X POST "${ASTOCKD_API_URL:-http://localhost:8888}/analyze/query" \
  -H "Content-Type: application/json" \
  -d '{"query": "帮我看看比亚迪怎么样"}'
```

## Documentation

| Resource | Description |
|----------|-------------|
| [Skill Definition](skills/stock-analysis/SKILL.md) | Complete agent instructions |
| [API Reference](docs/api-reference.md) | All endpoints |
| [AI Integration](docs/ai-integration.md) | Dify / MCP / OpenClaw setup |
| [OpenAPI Spec](openapi.yaml) | Machine-readable schema |

## Installation (OpenClaw)

```bash
mkdir -p .openclaw/skills/
cp -r skills/stock-analysis .openclaw/skills/
export ASTOCKD_API_URL="http://your-server:8888"
```
