<div align="center">

# A股道（AStockD）- 股票数据 AI 医生

![Version](https://img.shields.io/badge/文档包-1.0.16-blue?style=flat-square)
![Features](https://img.shields.io/badge/特征-300%2B-orange?style=flat-square)
![API](https://img.shields.io/badge/API-OpenAPI%203.0-purple?style=flat-square)
![AI Agent Ready](https://img.shields.io/badge/AI%20Agent-Ready-green?style=flat-square)

</div>

**A股道（AStockD）** - 面向 A 股的智能分析引擎：说出股票名称，即可获得技术面、资金面与 K 线形态的综合诊断报告。引擎内置 **300+ 量化特征** 与 **LLM 报告生成**，专为 AI Agent、对话机器人和自动化工作流设计。

> **关于本仓库**：此处提供 A股道的**产品文档、开放 API 规范与 Agent 集成工具**（Skill、OpenAPI）。分析服务通过 [A股道官网](https://astockd.com) 使用，引擎源码不在本仓库。

---

## 1. 产品介绍

### 1.1 核心价值

- **自然语言查股** — 说「帮我看看茅台」即可
- **量化 + AI 双引擎** — 300+ 特征计算后，由 LLM 合成可读诊断报告
- **Agent 原生** — 统一网关、Skill 开箱即用，适配 Cursor、OpenClaw、Dify 等

### 1.2 功能场景

| 场景 | 你能得到什么 | 代表能力 |
|------|-------------|----------|
| **个股诊断** | 技术面 + 资金面 + K 线形态的综合报告 | 自然语言查股、智能分析、全量分析 |
| **资金流分析** | 分钟 / 日级主力资金、洗盘、背离等信号 | 原始资金流、资金流特征 |
| **技术指标** | 212 项技术特征（含 60 分钟、Z-score） | 技术特征、K 线形态、生死线 |
| **量化评分** | 单股 / 批量五维评分、近期高分榜 | `score`、`score.batch`、`scores.recent` |
| **盘中 & 策略** | 分时逐笔、热门板块、策略选股 | 逐笔分时、热门板块、策略选股 |
| **市场复盘** | 大盘早盘 / 全天复盘报告 | 大盘复盘 |
| **组合分析** | 持仓组合诊断与收益跟踪 | 组合持仓、收益跟踪 |

📊 [查看 300+ 特征完整说明](docs/features.md)

---

## 2. 如何使用？

> 支持 **Agent Skill**、**MCP**、**Open API** 三种接入 A股道，请先在 [A股道官网](https://astockd.com) 申请 API Key（`astk_...`）。

### 2.1 Agent Skill

OpenClaw、Claude 等 Agent 安装 Skill 后，即可调用 A股道分析能力。

**安装**

```bash
# npx
npx skills add AStockD/ChinaStockAIDiagnose@astockd-diagnose

# openclaw
git clone https://github.com/AStockD/ChinaStockAIDiagnose.git
cp -r ChinaStockAIDiagnose/skills/astockd-diagnose ~/.openclaw/skills/
```

执行 `export ASTOCKD_API_KEY="你的密钥"`，对 Agent 说「帮我看看比亚迪怎么样」验证接入。详见 [Skill 文档](skills/astockd-diagnose/SKILL.md)。

### 2.2 MCP

**配置**（Claude Desktop 等 MCP 客户端）：

```json
{
  "mcpServers": {
    "astockd-diagnose-mcp": {
      "url": "https://astockd.com/api/v1/mcp",
      "headers": {
        "Authorization": "Bearer 你的密钥"
      }
    }
  }
}
```

| MCP 工具 | REST 等价 |
|------|-------------------|
| `list_invoke_apis` | `GET /invoke/catalog` |
| `invoke_stock_api` | `POST /invoke` |

将 API Key 填入上方 `Authorization`，对 Agent 说「帮我看看比亚迪怎么样」验证接入。详见 [MCP 接入说明](docs/mcp-server.md)。

### 2.3 Open API

适合后端服务、脚本，以及 Dify、FastGPT、Coze 等低代码平台的 HTTP 工具节点。

**API 速览**

| 分类 | 文档 | 说明 | 代表 API |
|------|------|------|----------|
| AI 分析 | [01-analyze.md](docs/api/01-analyze.md) | 自然语言 / 智能分析 / 全量分析 | `analyze.query` / `analyze` / `analyze.full` |
| 策略 | [02-strategy.md](docs/api/02-strategy.md) | 大盘复盘 / 策略选股 | `analyze.market_review` / `strategy.screener` |
| 特征 | [03-features.md](docs/api/03-features.md) | 技术特征 / 全量特征 | `features.technical` / `features.all` |
| 资金流 | [04-flow.md](docs/api/04-flow.md) | 分钟 / 日级 / 合并资金流 | `flow.minute` / `flow.daily` / `flow.combined` |
| 评分 | [05-score.md](docs/api/05-score.md) | 单股 / 批量五维评分 | `score` / `score.batch` |

**示例**

**cURL 示例**

```bash
curl -X POST "https://astockd.com/api/v1/open/invoke" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer 你的密钥" \
  -d '{
    "api": "analyze.query",
    "query": "帮我看看贵州茅台的最新走势"
  }'
```

**Python 示例**

```bash
# 安装依赖
pip install requests
```

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {
    "Content-Type": "application/json",
    "Authorization": "Bearer 你的密钥",
}
payload = {
    "api": "analyze.query",
    "query": "帮我看看贵州茅台的最新走势",
}

resp = requests.post(url, headers=headers, json=payload, timeout=30)
resp.raise_for_status()
print(resp.json())
```

将 API Key 填入上方 `Authorization`，API 目录见 `GET /invoke/catalog`。详见 [API 通用说明](docs/api/00-common.md) · [完整请求/响应样例](docs/api/99-examples.md)。

### 2.4 Android App 下载

如果想快速体验 A股道，可下载安装 Android 客户端：
- 下载地址：`https://astockd.com/api/v1/app/download/android`

也可扫码下载：

![Android 下载二维码](./assets/android-app-qr.png)

## 参考链接
1. [A股道官网](https://www.astockd.com)
2. [联系我们](mailto:support@astockd.com)