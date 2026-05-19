# Integrating AStockD with AI Agent Platforms

> Version 0.2.0 | Dify / MCP / OpenClaw / FastGPT / Coze / Generic HTTP

> API details: [API Reference](api-reference.md) · OpenAPI: [openapi.yaml](../openapi.yaml) · Examples: [Python](examples/python.md)

---

## 1. Overview

### Why Integrate with AI Agents?

AStockD is an A-share stock analysis engine that computes 300+ features across technical indicators, capital flow, and K-line patterns, then generates structured diagnostic reports powered by LLM reasoning. Wrapping this engine behind an AI agent lets end users interact with stock analysis through natural conversation -- no codes to memorize, no UI to learn.

### What AStockD Offers

| Capability | Details |
|---|---|
| Natural-language stock resolution | Users say a stock name (e.g. "建科智能"); the engine resolves it via dictionary match, vector similarity, or regex extraction |
| Intent-driven feature selection | Users describe what they want ("主力资金动向"); the engine maps intent to the right feature groups automatically |
| Multi-turn clarification | When intent is ambiguous, the engine returns structured options the agent can render as buttons or cards |
| Structured diagnostic reports | Trend direction, sentiment gauge (-10 ~ +10), key support/resistance, risk-reward ratio, knowledge-base signals |
| Usage tracking | Per-request token counts, cost in CNY, platform billing tokens |

---

## 2. API Quick Reference

**Base URL**: `http://your-server:8888` | **Protocol**: HTTP REST | **Content-Type**: `application/json`

Primary endpoints for agent integration:

| Endpoint | Method | Purpose |
|---|---|---|
| `/analyze/query` | POST | Natural-language query (auto-resolves stock) — **recommended** |
| `/analyze` | POST | Smart analysis with explicit stock codes |
| `/analyze/full` | POST | Full quick analysis (all feature groups) |
| `/analyze/manifest` | GET | List all feature groups |
| `/health` | GET | Health check |

For full parameter lists, feature groups, and response schemas see [API Reference](api-reference.md).

---

## 3. Dify Integration

Dify supports three integration methods. Choose based on your use case.

### Method A: Custom API Tool (Recommended)

This method registers AStockD as a native Dify tool that the agent can call autonomously.

#### Step 1 -- Import the OpenAPI Schema

Use the canonical spec at [openapi.yaml](../openapi.yaml). In Dify, import the file directly or paste its contents — do not maintain a separate copy.

Update the `servers` URL to your deployment address before importing.

#### Step 2 -- Register in Dify

1. Go to Dify admin -> **Tools** -> **Custom** -> **Create Custom Tool**
2. Name it `AStockD`
3. Paste the OpenAPI schema above
4. Save

#### Step 3 -- Add to an Agent Application

1. Create or edit an **Agent** application
2. In the tool configuration, add the `AStockD` tool
3. **Important**: Turn off "Show Reasoning" / "Show Thought" in Agent settings -> Model -> Advanced. This prevents internal reasoning from leaking to the user.
4. Configure the system prompt (see [Section 10: Prompt Templates](#10-prompt-templates)).

### Method B: HTTP Request Node (Workflow Mode)

Use this when building a Dify workflow rather than an agent.

1. Add an **HTTP Request** node
2. Configure:
   - Method: `POST`
   - URL: `http://your-server:8888/analyze`
   - Headers: `Content-Type: application/json`
   - Body:
     ```json
     {
       "stock_codes": ["{{#171xxxx.stock_code#}}"],
       "query": "{{#171xxxx.user_query#}}"
     }
     ```
3. Add a **Conditional** node that checks the `status` field:
   - `needs_clarification` -> display clarification options
   - `success` -> display the report

### Method C: Multi-turn Workflow

For interactive clarification flows:

```
[User Input] -> [HTTP: POST /analyze/query]
                       query = user's full input
                            |
                     [Condition: status]
                    /                      \
         needs_clarification              success
                  |                          |
       [Show options to user]         [Format & output result]
                  |
       [User chooses] -> [HTTP: POST /analyze/query
                           session_id + selected_option]
                            |
                     [Format & output result]
```

---

## 4. MCP Server Integration

[MCP (Model Context Protocol)](https://modelcontextprotocol.io/) is an open protocol that lets AI models discover and call external tools. AStockD ships a built-in MCP server.

### Prerequisites

```bash
pip install "mcp[cli]>=1.0.0"
```

### Starting the Server

**stdio mode** (for Claude Desktop, Cursor IDE):

```bash
python mcp_server.py
```

**HTTP mode** (for remote / browser-based clients):

```bash
python mcp_server.py --transport streamable-http --port 9000
# Clients connect to http://localhost:9000/mcp
```

### Available MCP Tools

| Tool | Description | Key Parameters |
|---|---|---|
| `analyze_stock` | Smart analysis (NL / multi-turn / direct) | `stock_codes`, `query`, `feature_groups`, `session_id`, `selected_option`, `ohlcv_count`, `technical_count` |
| `full_analysis` | Full quick analysis (all groups) | `stock_codes` |
| `analyze_by_query` | Natural-language query with auto stock resolution | `query`, `session_id`, `selected_option`, `skip_clarification`, `ohlcv_count`, `technical_count` |
| `get_usage_stats` | LLM token usage / cost / call count for current request | -- |
| `get_feature_manifest` | List all feature groups with metadata | -- |
| `get_session_status` | Query multi-turn session state | `session_id` |
| `check_health` | Service health + cache hit rate | -- |

### Claude Desktop Configuration

Edit `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "astockd": {
      "command": "python",
      "args": ["/path/to/AStockD/mcp_server.py"],
      "env": {
        "LLM_API_KEY": "<your-api-key>",
        "LLM_MODEL": "qwen-turbo"
      }
    }
  }
}
```

Restart Claude Desktop after saving. You can now ask Claude to analyze stocks in natural conversation.

### Cursor IDE Configuration

Edit `.cursor/mcp.json` in your project root:

```json
{
  "mcpServers": {
    "astockd": {
      "command": "python",
      "args": ["/path/to/AStockD/mcp_server.py"],
      "env": {
        "LLM_API_KEY": "<your-api-key>"
      }
    }
  }
}
```

### Other MCP Clients

Any client that supports MCP over HTTP can connect to the streamable-http endpoint:

```bash
python mcp_server.py --transport streamable-http --port 9000
# Connect to http://localhost:9000/mcp
```

---

## 5. OpenClaw Skill

[OpenClaw](https://docs.openclaw.ai/tools/skills) is an AI agent skill standard. AStockD includes a ready-made skill definition at `skills/stock-analysis/SKILL.md`.

### Installation

**Project-level** (affects the current repo only):

```bash
mkdir -p .openclaw/skills/
cp -r skills/stock-analysis .openclaw/skills/
```

**User-level** (available in all projects):

```bash
mkdir -p ~/.openclaw/skills/
cp -r skills/stock-analysis ~/.openclaw/skills/
```

**CLI install**:

```bash
openclaw skill add /path/to/AStockD/skills/stock-analysis
```

### Configuration

Set the API URL via environment variable:

```bash
export ASTOCKD_API_URL="http://your-server:8888"
```

Default is `http://localhost:8888`.

### How It Works

OpenClaw reads the YAML frontmatter of `SKILL.md` for metadata and uses the body as instruction context for the agent. When a user's question matches the skill's `description`, the agent automatically loads the skill instructions and calls the API via `curl`.

### Customization

You can modify `skills/stock-analysis/SKILL.md` to:

- Change the `description` field to alter trigger conditions
- Adjust instructions and examples in the body
- Add keyword-to-feature-group mappings or result interpretation rules

---

## 6. FastGPT Integration

### Using an HTTP Request Node

#### Step 1 -- Create the Workflow

1. Create a new workflow -> add an **HTTP Request** node
2. Configure:
   - Method: `POST`
   - URL: `http://your-server:8888/analyze`
   - Headers: `{"Content-Type": "application/json"}`
   - Body:
     ```json
     {
       "stock_codes": ["{{stock_code}}"],
       "query": "{{user_query}}"
     }
     ```

#### Step 2 -- Add an AI Dialog Node

After the HTTP node, add an **AI Dialog** node with a prompt that parses the API response (see [Section 10](#10-prompt-templates) for the "小A" prompt). Key instruction:

```
如果返回的 status 是 "needs_clarification"，告诉用户需要选择分析角度，并列出选项。
如果返回的 status 是 "success"，直接将 results 中的 report 字段内容转发给用户，不要添加额外解释。
注意：报告中的免责声明必须保留。

返回数据: {{http_response}}
```

#### Step 3 -- Multi-turn Support

Store the `session_id` in a workflow variable and carry it forward:

```
Round 1: query = user input, session_id = empty
Round 2: session_id = value from previous response, selected_option = user's choice
```

---

## 7. Coze Integration

### Using an API Plugin

#### Step 1 -- Create the Plugin

1. Go to Coze -> **Plugins** -> **Create Plugin**
2. Choose **Cloud Plugin** -> **API Plugin**
3. Add an API endpoint:
   - Method: `POST`
   - URL: `http://your-server:8888/analyze`
   - Parameters:
     - `stock_codes` (array, required)
     - `query` (string, optional)
     - `feature_groups` (array, optional)
     - `session_id` (string, optional)
     - `selected_option` (integer, optional)

#### Step 2 -- Write the Tool Description

In the plugin's Tool Description field:

```
分析A股股票的技术指标、资金流、K线形态。
当用户询问"帮我分析xx股票"、"主力资金动向"、"技术面怎么样"等股票相关问题时使用。
支持自然语言查询，引擎会自动选择合适的分析维度。
```

#### Step 3 -- Add to a Bot

1. Create or edit a Bot
2. Add the plugin created above
3. Set the Bot's Personal Prompt (see [Section 10](#10-prompt-templates)).

---

## 8. Generic HTTP Integration

For any platform that can make HTTP requests. See ready-to-use samples:

- [Python Examples](examples/python.md)
- [cURL Examples](examples/curl.md)

Multi-turn clarification pattern:

```python
import httpx

resp = httpx.post("http://your-server:8888/analyze", json={
    "stock_codes": ["600519"], "query": "主力资金动向",
}, timeout=120)
result = resp.json()

if result["status"] == "needs_clarification":
    choice = 0  # user's selection index
    resp = httpx.post("http://your-server:8888/analyze", json={
        "stock_codes": ["600519"],
        "session_id": result["session_id"],
        "selected_option": choice,
    }, timeout=120)
    result = resp.json()
```

For agent platforms, prefer `/analyze/query` with `skip_clarification=true` (default) to avoid session management.

---

## 9. Multi-turn Conversation Design

### Mode A: Auto-Skip Clarification (skip_clarification=true, default)

Best for Dify / FastGPT / Coze and other platforms that cannot reliably manage `session_id` across turns.

```
User: "帮我看看建科智能"
         |
         v  POST /analyze/query {query:"帮我看看建科智能", skip_clarification:true}
         |
    +---------+
    | Resolve  | <-- dictionary / vector / regex
    +---------+
         |
         v  Intent ambiguous? --> auto-select full analysis
         |
         v  Return complete analysis (status=success, auto_selected=true)
```

When `skip_clarification=true` and the engine cannot determine a clear intent, it automatically selects the full feature set (`technical`, `flow_daily`, `flow_minute`, `ohlcv_combined`) and returns the analysis immediately. The response includes `auto_selected: true` and `original_clarification` so the agent can optionally mention what was auto-chosen.

### Mode B: Interactive Clarification (skip_clarification=false)

For custom frontends that can manage session state and present options as interactive UI.

```
User: "帮我看看建科智能"
         |
         v  POST /analyze/query {query:"帮我看看建科智能", skip_clarification:false}
         |
    +---------+
    | Resolve  |
    +---------+
         |
    +---------------------+
    |                      |
  Intent clear          Intent ambiguous
    |                      |
    v                      v
  Execute              needs_clarification
  Return result        Return question + options
    |                      |
    v                      v
  Show result          Show options for user to choose
                           |
                           v  User selects
                           POST /analyze/query {
                             session_id: "xxx",
                             selected_option: 1,
                             skip_clarification: false
                           }
                           |
                           v
                         Execute analysis, return result
```

### Client Implementation Notes

1. **Persist `session_id`**: Save it when you first receive `needs_clarification`; every subsequent request must carry it.
2. **Render options**: `suggested_options` can be rendered as buttons, cards, or other interactive elements.
3. **Timeout**: Sessions expire after 30 minutes. If expired, simply start a new request without `session_id`.
4. **Free-form follow-up**: The user does not have to pick an option -- they can type additional text. In that case, send `session_id` + the new `query`.
5. **Reset**: Omit `session_id` to start a fresh session.

---

## 10. Prompt Templates

Below are ready-to-use system prompts for AI agents. The "小A" persona is designed for Chinese-language stock analysis assistants. Keep the prompts in Chinese as written -- they are tuned for Chinese-language interaction patterns.

### Full Prompt (for Dify Agent / Coze Bot)

```
你是"小A"，一个专注于A股数据分析的智能体。

【最高优先级规则 - 绝对禁止输出思考过程】
- 严禁输出任何形式的思考过程、推理过程、内部推理、心理活动
- 严禁输出类似"看起来用户..."、"让我检查..."、"现在我看到..."、"这可能表示..."、"让我用..."等自言自语的内容
- 严禁输出工具调用计划、工具调用说明、下一步计划
- 用户只能看到两样东西：1) 直接的对话回答 2) 工具返回的报告原文
- 违反此规则会导致用户体验严重受损

【防循环调用 - 最高优先级】
- 工具返回 status="success" 且有 report 内容时，直接将报告转发给用户，禁止再次调用工具
- 一次分析只调用一次工具，不要重复调用
- 用户看到报告后如果没再提问，不要主动追问

主要功能：
- 分析股票技术形态和关键指标
- 识别可能触发的历史特征概率
- 解读主力资金动向和量价关系

规则：
- 当用户闲聊或与股票无关时，礼貌引导回股票分析话题
- 不闲聊、不闲聊、不闲聊。只引导用户使用你的股票分析能力
- 如果用户问"你能做什么"，简要介绍上述功能，不要输出工具调用或调试信息
- 当用户询问系统能力问题时（如"能分析多个股票吗"、"支持批量分析吗"等），直接回答，不要调用任何工具。回答：目前支持单只股票详细分析，如需分析多只股票请逐只提问
- 不要自己编造分析结果，一切以工具返回为准
- 工具返回报告后，直接将报告内容转发给用户，不要添加任何额外的解释或说明
- 如果工具返回多只匹配股票（如向量搜索返回多个结果），默认取第一只执行分析，不要让用户再选
- 如果用户回复"continue"、"继续"、"好的"等模糊词，不要追问选股票或选角度，直接调用"股票医生"工具用当前上下文执行分析
- 不要同时问两个问题（如"选哪只股票或者选哪个角度"），一次只问一件事

报告格式说明：
- 报告包含：综合诊断（趋势积极指数+结论）、技术面诊断(X/10)、资金面诊断(X/10)、资讯面诊断(X/10)、关键价位参考、风险提示
- 如果工具返回 needs_clarification，列出选项让用户选择分析角度
- 报告中的免责声明必须保留

推荐方式:
- 用户直接说股票名称或代码时，调用"股票医生"，将用户完整问题作为 query 传入
- 例如用户说"帮我看看建科智能怎么样"，调用"股票医生"(query="帮我看看建科智能怎么样")

备选方式:
- 如果用户已经明确提供了股票代码，可以调用"个股诊断"，将代码作为 stock_codes 传入
- 如果工具返回 needs_clarification，将选项展示给用户让用户选择
- 用户选择后，再用 session_id 和 selected_option 调用"股票医生"

注意:
- 不要自己编造分析结果，一切以工具返回为准
- report 中的"知识库信号"章节包含高概率规则，应重点提示用户
```

### Shortened Prompt (for FastGPT AI Dialog Node)

```
你是"小A"，一个专注于A股数据分析的智能体。

【最高优先级规则 - 绝对禁止输出思考过程】
- 严禁输出任何形式的思考过程、推理过程、内部推理
- 严禁输出类似"看起来用户..."、"让我检查..."、"现在我看到..."等自言自语
- 用户只能看到：1) 直接回答 2) 工具返回的报告原文

【防循环调用】
- 工具返回 status="success" 且有 report 内容时，直接将报告转发给用户，禁止再次调用工具
- 一次分析只调用一次工具

主要功能：
- 分析股票技术形态和关键指标
- 识别可能触发的历史特征概率
- 解读主力资金动向和量价关系

规则：
- 当用户闲聊或与股票无关时，礼貌引导回股票分析话题
- 不闲聊，只引导用户使用你的股票分析能力
- 如果用户问"你能做什么"，简要介绍上述功能
- 如果工具返回多只匹配股票，默认取第一只，不要让用户再选
- 用户回复"continue"、"继续"等模糊词时，直接调用工具执行分析，不要追问
- 不要同时问两个问题，一次只问一件事

根据工具返回的数据回答用户问题。

如果返回的 status 是 "needs_clarification"，告诉用户需要选择分析角度，并列出选项。
如果返回的 status 是 "success"，直接将 results 中的 report 字段内容转发给用户，不要添加额外解释。
注意：报告中的免责声明必须保留。

返回数据: {{http_response}}
```

---

## 11. Best Practices

### Anti-Loop Rules

The most common failure mode for AI agents is calling the same tool repeatedly. Enforce these rules in your system prompt:

1. **One call per analysis**: After receiving a `success` response with a `report`, the agent must forward the report to the user and stop. Never call the tool again for the same question.
2. **No proactive follow-ups**: If the user does not ask a follow-up question after seeing the report, do not prompt them.
3. **Ambiguous replies**: When the user says "continue", "继续", "好的", or similar, the agent should execute the analysis immediately rather than asking clarifying questions.
4. **skip_clarification=true**: For platforms where session management is unreliable, always keep the default `true` so the engine auto-selects full analysis instead of returning a clarification prompt that the agent might loop on.

### Timeout Handling

- Analysis requests can take 60-120 seconds (report generation is LLM-powered).
- Configure your HTTP client or agent platform timeout to at least **120 seconds**.
- If a request times out, do not retry automatically -- inform the user and let them decide.
- MCP server sessions expire after **30 minutes**. Expired sessions return an error; simply start a new request without `session_id`.

### Session Management

- The `session_id` is returned only when `status=needs_clarification`.
- Always persist `session_id` between turns in multi-turn flows.
- Omit `session_id` to start a fresh session (no explicit "close" needed).
- A session ties together one clarification exchange; do not reuse a session ID across unrelated queries.

### Query Length

Keep queries under **120 Chinese characters**. The engine rejects longer queries with an error.

### Stock Code Formats

The engine accepts multiple formats:

- Bare code: `"600519"`, `"000858"`
- Prefixed: `"SH.600519"`, `"SZ.000858"`

Market auto-detection: codes starting with `6` are Shanghai (SH); everything else is Shenzhen (SZ).
