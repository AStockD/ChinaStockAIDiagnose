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

---

## Key Features

| Capability | Description |
|------------|-------------|
| Natural Language Interface | Say a stock name, get a full analysis — no complex parameters |
| 300+ Financial Features | Technical indicators, money flow, K-line patterns, fundamentals |
| AI-Powered Reports | LLM synthesizes features into structured diagnostic reports |
| AI Agent Ready | Dify, FastGPT, Coze, MCP, OpenClaw out of the box |

---

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

---

## Quick Start

```bash
curl -X POST http://your-server:8888/analyze/query \
  -H "Content-Type: application/json" \
  -d '{"query": "帮我看看贵州茅台的最新走势"}'
```

See [Quick Start Guide](docs/quick-start.md) for setup and Python examples.

---

## Project Structure

```
AStockD/
├── docs/                    # Documentation
│   ├── api-reference.md     # API endpoints
│   ├── ai-integration.md    # Agent platform integration
│   ├── features.md          # 300+ feature reference
│   ├── quick-start.md       # 5-minute guide
│   └── examples/            # Code samples
├── skills/
│   └── stock-analysis/      # OpenClaw agent skill
├── openapi.yaml             # OpenAPI 3.0 spec
├── SKILL.md                 # Root skill entry
├── CHANGELOG.md
└── CONTRIBUTING.md
```

---

## Documentation

| Document | Description |
|----------|-------------|
| [Quick Start](docs/quick-start.md) | 5-minute setup and first API call |
| [API Reference](docs/api-reference.md) | Complete endpoint documentation |
| [AI Integration Guide](docs/ai-integration.md) | Dify / MCP / OpenClaw / FastGPT / Coze |
| [Feature Reference](docs/features.md) | 300+ features across 8 groups |
| [Python Examples](docs/examples/python.md) | Client code samples |
| [cURL Examples](docs/examples/curl.md) | Command-line samples |
| [OpenAPI Spec](openapi.yaml) | Machine-readable API schema |
| [SKILL.md](SKILL.md) | Root skill entry for AI agents |
| [OpenClaw Skill](skills/stock-analysis/SKILL.md) | Full agent skill definition |
| [Changelog](CHANGELOG.md) | Version history |
| [Contributing](CONTRIBUTING.md) | How to contribute |

---

## Contributing

We welcome contributions! See [Contributing Guide](CONTRIBUTING.md).

---

## License

MIT License — see [LICENSE](LICENSE).
