# AStockD

**AI-native A-share stock analysis engine** | AI 原生 A 股分析引擎

AStockD is an API-first stock analysis engine that understands natural language — say a stock name, get a full analysis. It combines 300+ financial features with LLM-powered report generation, designed to integrate seamlessly with AI agents and chat platforms.

---

## Key Features

- **Natural Language Stock Analysis** — Simply say the stock name (e.g., "贵州茅台") and receive a comprehensive analysis report
- **300+ Features Across 8 Groups** — Technical indicators (技术指标), money flow (资金流向), K-line patterns (K线形态), fundamental data, sector analysis, sentiment, and more
- **AI-Powered Report Generation** — LLM synthesizes raw feature data into structured, readable analysis reports (OpenAI-compatible interface)
- **AI Agent Integration** — Connect to Dify, FastGPT, Coze, MCP, and OpenClaw out of the box
- **Multi-Turn Conversation** — Context-aware follow-up questions for deeper analysis within a single session
- **Platform Token Billing** — Built-in metering for SaaS deployment and per-user usage tracking

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

## Documentation

| Document | Description |
|----------|-------------|
| [API Reference](api-reference.md) | Complete endpoint documentation |
| [AI Integration Guide](ai-integration.md) | Connect to Dify / FastGPT / Coze / MCP / OpenClaw |
| [Feature Reference](features.md) | 300+ features across 8 groups |
| [Changelog](CHANGELOG.md) | Version history |

---

## License

MIT
