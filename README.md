# AStockD

<div align="center">

**AI-native A-share stock analysis engine** | AI 原生 A 股分析引擎

![Version](https://img.shields.io/badge/version-0.2.0-blue?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)
![Python](https://img.shields.io/badge/python-3.8+-blue?style=flat-square)
![API](https://img.shields.io/badge/API-OpenAPI%203.0-orange?style=flat-square)
![AI Agent Ready](https://img.shields.io/badge/AI%20Agent-Ready-purple?style=flat-square)

</div>

AStockD is an API-first stock analysis engine that understands natural language — say a stock name, get a full analysis. It combines 300+ financial features with LLM-powered report generation, designed to integrate seamlessly with AI agents and chat platforms.


## Key Features

| Capability | Description |
|------------|-------------|
| Natural Language Interface | Say a stock name, get a full analysis — no complex parameters |
| 300+ Financial Features | Technical indicators, money flow, K-line patterns, fundamentals |
| AI-Powered Reports | LLM synthesizes features into structured diagnostic reports |
| AI Agent Ready | Dify, FastGPT, Coze, MCP, OpenClaw out of the box |

📊 [View complete feature reference](docs/features.md) — 300+ features across 8 groups


## Architecture

```
┌──────┐     ┌──────────┐     ┌─────────────┐     ┌──────────────┐
│ User │ ──▶ │ AI Agent │ ──▶ │ AStockD API │ ──▶ │ Data Sources │
└──────┘     └──────────┘     └─────────────┘     └──────────────┘
  Chat /       Dify /          FastAPI +            A-share market
  Voice        FastGPT /       Feature Engine       data providers
               Coze / MCP      + LLM Report
               OpenClaw         Generation
```


## Core API Endpoints

| Endpoint | Description | Usage |
|----------|-------------|-------|
| [`POST /analyze/query`](docs/api-reference.md#41-post-analyzequery) | Natural language queries | "帮我看看贵州茅台的最新走势" |
| [`POST /analyze`](docs/api-reference.md#42-post-analyze) | Smart analysis with stock codes | Explicit stock codes + analysis focus |
| [`POST /analyze/full`](docs/api-reference.md#43-post-analyzefull) | Full analysis shortcut | All major features in one call |
| [`GET /analyze/manifest`](docs/api-reference.md#44-get-analyzemanifest) | Feature group catalog | Available feature groups and metadata |
| [`POST /flow/*`](docs/api-reference.md#5-raw-money-flow-endpoints) | Raw money flow data | Minute-level, daily, combined flow |
| [`POST /features/*`](docs/api-reference.md#7-ohlcv-feature-endpoints) | OHLCV features | K-patterns, technical indicators |

### Example: Natural Language Query

```bash
curl -X POST http://your-server:8888/analyze/query \
  -H "Content-Type: application/json" \
  -d '{"query": "帮我看看贵州茅台的最新走势"}'
```

### Example: Stock Code Analysis

```bash
curl -X POST http://your-server:8888/analyze \
  -H "Content-Type: application/json" \
  -d '{
    "stock_codes": ["600519"],
    "query": "主力资金动向",
    "feature_groups": ["technical", "flow_daily"]
  }'
```
> [OpenAPI Spec](./docs/openapi.yaml) 


## AStockD stock-analysis Skill

> Full skill definition: [skills/stock-analysis/SKILL.md](skills/stock-analysis/SKILL.md)

AI agent skill for A-share stock analysis via the AStockD API. Supports natural language queries, multi-turn clarification, and direct feature group selection.


## Contributing

We welcome contributions! See [Contributing Guide](CONTRIBUTING.md).


## License

MIT License — see [LICENSE](LICENSE).
