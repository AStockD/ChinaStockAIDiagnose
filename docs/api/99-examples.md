# Stock Diagnosis API 调用文档

> 基于实际测试结果自动生成，所有请求/响应均为真实示例。
>
> **Base URL**: `https://astockd.com/api/v1/open`
>
> **认证**: `Authorization: Bearer <你的密钥>`

## 目录

### 系统

- [system.status](#system-status) — 服务状态
- [system.health](#system-health) — 健康检查
### AI 原生分析

- [analyze](#analyze) — AI原生智能分析
### AI 分析

- [analyze.full](#analyze-full) — 全量分析
- [analyze.query](#analyze-query) — 自然语言查询
- [analyze.manifest](#analyze-manifest) — 特征清单
### 评分

- [score](#score) — 单股五维评分
- [score.batch](#score-batch) — 批量五维评分
- [scores.recent](#scores-recent) — 近期高分评分
### 资金流

- [flow.minute](#flow-minute) — 分钟级资金流
- [flow.daily](#flow-daily) — 日级资金流
- [flow.combined](#flow-combined) — 合并资金流
### 资金流特征

- [flow_features.minute](#flow_features-minute) — 分钟级资金流特征
- [flow_features.daily](#flow_features-daily) — 日级资金流特征
- [flow_features.combined](#flow_features-combined) — 合并资金流特征
### 特征计算

- [features.k_patterns](#features-k_patterns) — K线形态特征(18种)
- [features.lifeline](#features-lifeline) — 生死线指标(7个)
- [features.experience](#features-experience) — 经验特征
- [features.combined](#features-combined) — 合并特征
- [features.technical](#features-technical) — 技术特征集(212个)
- [features.all](#features-all) — 全量特征
### AI 原生分析

- [analyze.tick_intraday](#analyze-tick_intraday) — 逐笔分时特征
- [analyze.hot_sector](#analyze-hot_sector) — 热门板块联动跟风
- [analyze.market_review](#analyze-market_review) — 大盘复盘报告
- [analyze.portfolio](#analyze-portfolio) — 组合持仓分析
- [analyze.stock_track](#analyze-stock_track) — 股票收益跟踪
### 策略选股

- [strategy.screener](#strategy-screener) — 策略选股
### 搜索

- [search.tavily](#search-tavily) — Tavily搜索

---

## 通用说明

### 统一网关 `/invoke`

所有 API 均可通过统一网关调用：

```
POST /invoke
Authorization: Bearer <你的密钥>
Content-Type: application/json

{
  "api": "<api_code>",
  "stock_codes": ["SZ.000001"],    // 可选，部分 API 需要
  "query": "...",                   // 可选，自然语言查询
  "params": { ... }                 // 可选，API 专属参数
}
```

> 说明：下文示例中的 `<token>` 均表示“你的密钥”。

Python 请求示例：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {
    "Authorization": "Bearer <你的密钥>",
    "Content-Type": "application/json",
}
payload = {
    "api": "analyze.query",
    "query": "帮我看看贵州茅台最新走势",
}

resp = requests.post(url, headers=headers, json=payload, timeout=30)
print(resp.json())
```

### 统一响应格式

```json
{
  "code": 200,
  "message": "ok",
  "data": { ... },
  "usage": { "cost": 3.0 }
}
```

---

## 系统

### system.status

**服务状态**

- 耗时: 0.63s
- 响应大小: 465 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "system.status"
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "system.status"}

resp = requests.post(url, headers=headers, json=payload, timeout=30)
print(resp.json())
```

请求体：

```json
{
  "api": "system.status"
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "service": "统一股票分析API",
    "version": "0.10.39",
    "status": "running",
    "services": [
      "分钟级资金流服务",
      "日级资金流服务",
      "合并资金流服务",
      "分钟级资金流特征服务",
      "日级资金流特征服务",
      "合并资金流特征服务",
      "K形态特征服务",
      "生死线特征服务",
      "经验特征服务",
      "合并特征服务",
      "技术特征集服务"
    ]
  },
  "usage": {
    "cost": 0.0,
    "balance": 13516.8,
    "balance_after": 13516.8
  }
}
```

---

### system.health

**健康检查**

- 耗时: 0.34s
- 响应大小: 290 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "system.health"
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "system.health"}

resp = requests.post(url, headers=headers, json=payload, timeout=30)
print(resp.json())
```

请求体：

```json
{
  "api": "system.health"
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "status": "healthy",
    "version": "0.10.39",
    "trading_hours": false,
    "after_close": true,
    "cache": {
      "hits": 0,
      "misses": 0,
      "hit_rate": 0.0,
      "l1_size": 0
    },
    "sessions": 0,
    "timestamp": "2026-07-06T21:35:11.853141"
  },
  "usage": {
    "cost": 0.0,
    "balance": 13516.8,
    "balance_after": 13516.8
  }
}
```

---

## AI 原生分析

### analyze

**AI原生智能分析**

- 耗时: 3.58s
- 响应大小: 4,503 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "analyze",
  "stock_codes": [
    "SZ.000001"
  ],
  "query": "主力资金动向"
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "analyze", "stock_codes": ["SZ.000001"], "query": "主力资金动向"}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "analyze",
  "stock_codes": [
    "SZ.000001"
  ],
  "query": "主力资金动向"
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "strategy": "stock_diagnosis",
    "disclaimer": "本报告基于股票当前数据和历史数据统计分析，所有提及的概率均基于历史回测数据，不构成任何投资建议。股市有风险，投资需谨慎。",
    "status": "success",
    "session_id": "8a931637",
    "feature_groups": [
      "flow_daily",
      "flow_minute",
      "flow_combined"
    ],
    "reasoning": "用户明确关注'主力资金动向'，应优先选择资金流相关特征组：flow_daily（日线资金流）、flow_minute（分钟级资金流）和flow_combined（综合资金流），覆盖日线与分钟级主力资金行为分析。 (报告缓存命中)",
    "results": {
      "SZ.000001": {
        "status": "success",
        "_report": "## 快速结论\n\n**建议观望**（信心：弱 | 情绪 -0）\n\n## 综合评分：54分（中性）\n\n| 维度 | 得分 | 权重 | 说明 |\n|------|------|------|------|\n| 综合情绪 | 50/100 | 15% | 中性 |\n| 分时强度 | 78/100 | 25% | 强势 |\n| 资金面 | 44/100 | 40% | 偏弱 |\n| 技术面 | 50/100 | 10% | 中性 |\n| K线形态 | 50/100 | 10% | 中性 |\n\n\n\n---\n\n免责申明：本报告仅供参考，不构成投资建议。股市有风险，投资需谨慎。\n\n趋势方向偏中性，信心中等。\n\n历史数据显示，该股当前多空力量相对均衡，既没有明显一边倒的上涨动力，也没有持续走弱的迹象。资金面上，今天主力资金大幅流入2.42亿元，早盘和尾盘都有明显资金介入，且主力占全部成交的七成以上，说明大资金在积极运作；技术面上，股价目前稳稳站在分时均线之上，均线本身也在缓慢抬升，盘中强度表现较好，整体呈现“弱转强”的节奏；不过也要注意，主力和散户同步流入，这种一致性反而可能隐藏诱多意图，加上资金流向略显杂乱，说明场内分歧仍在。\n\n风险提示：虽然短期资金活跃、价格稳定，但综合评分仅54分，尚未达到趋势明确的临界点。若后续资金流入不能持续、或价格无法有效突破关键位置，仍可能重回震荡甚至小幅回调。请关注后续几个交易日主力动作是否连贯、市场情绪是否真正转暖。\n\n## 量价趋势\n\n分时图价格运行均线上方(+0.91%)，买方占优，分时趋势弱转强，分时均线上升中。\n\n## 实时资金流\n\n主力今日净买入2.42亿，呈主动买入态势。\n主力占比18.2%，机构资金主导盘面。\n主力-散户差为正(+0.1854)，主力资金占主导，散户被动跟涨。",
        "simple_verdict": {
          "verdict": "建议观望",
          "confidence": "弱",
          "sentiment": "-0",
          "overall_score": 54.4,
          "sentiment_norm": 50.0,
          "tick_norm": 77.5,
          "flow_norm": 43.9,
          "tech_norm": 50.0,
          "kline_norm": 50.0
        }
      }
    },
    "summary": "## SZ.000001 - 2026/07/06 20:17:57\n\n免责申明：本报告仅供参考，不构成投资建议。股市有风险，投资需谨慎。\n\n趋势方向偏中性，信心中等。\n\n历史数据显示，该股当前多空力量相对均衡，既没有明显一边倒的上涨动力，也没有持续走弱的迹象。资金面上，今天主力资金大幅流入2.42亿元，早盘和尾盘都有明显资金介入，且主力占全部成交的七成以上，说明大资金在积极运作；技术面上，股价目前稳稳站在分时均线之上，均线本身也在缓慢抬升，盘中强度表现较好，整体呈现“弱转强”的节奏；不过也要注意，主力和散户同步流入，这种一致性反而可能隐藏诱多意图，加上资金流向略显杂乱，说明场内分歧仍在。\n\n风险提示：虽然短期资金活跃、价格稳定，但综合评分仅54分，尚未达到趋势明确的临界点。若后续资金流入不能持续、或价格无法有效突破关键位置，仍可能重回震荡甚至小幅回调。请关注后续几个交易日主力动作是否连贯、市场情绪是否真正转暖。\n\n## 量价趋势\n\n分时图价格运行均线上方(+0.91%)，买方占优，分时趋势弱转强，分时均线上升中。\n\n## 实时资金流\n\n主力今日净买入2.42亿，呈主动买入态势。\n主力占比18.2%，机构资金主导盘面。\n主力-散户差为正(+0.1854)，主力资金占主导，散户被动跟涨。",
    "timers": {
      "data_fetch": 0.02,
      "feature_compute": 0.0,
      "report_gen": 0,
      "total": 0.02
    },
    "cache_stats": {
      "hits": 9,
      "misses": 0,
      "hit_rate": 1.0,
      "l1_size": 7
    },
    "elapsed_seconds": 0.02,
    "timestamp": "2026-07-06T21:35:15"
  },
  "usage": {
    "cost": 5.0,
    "balance": 13516.8,
    "balance_after": 13511.8
  }
}
```

---

## AI 分析

### analyze.full

**全量分析**

- 耗时: 15.05s
- 响应大小: 6,816 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "analyze.full",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "analyze.full", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=120)
print(resp.json())
```

请求体：

```json
{
  "api": "analyze.full",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "strategy": "stock_diagnosis",
    "disclaimer": "本报告由 AStockD 特征库及历史趋势表现生成，不构成任何投资建议。股市有风险，投资需谨慎!",
    "status": "success",
    "session_id": "05c15717",
    "feature_groups": [
      "technical",
      "flow_daily",
      "flow_minute",
      "ohlcv_combined"
    ],
    "reasoning": "直接指定: technical, flow_daily, flow_minute, ohlcv_combined",
    "results": {
      "SZ.000001": {
        "status": "success",
        "ohlcv": {
          "date": "2026-07-06 00:00:00",
          "open": 10.25,
          "close": 10.5,
          "high": 10.52,
          "low": 10.22,
          "volume": 1061049.0
        },
        "_report": "## 快速结论\n\n**建议观望**（信心：中等 | 情绪 -2）\n\n## 综合评分：55分（中性）\n\n| 维度 | 得分 | 权重 | 说明 |\n|------|------|------|------|\n| 综合情绪 | 40/100 | 15% | 偏弱 |\n| 分时强度 | 78/100 | 25% | 强势 |\n| 资金面 | 44/100 | 40% | 偏弱 |\n| 技术面 | 72/100 | 10% | 强势 |\n| K线形态 | 49/100 | 10% | 中性 |\n\n- 分时强度多头强势，盘中买方主导\n- 资金持续流入，主力资金在买入\n- 多条历史规律(4条)提示风险\n\n> 注意：收盘价远高于MA20(深度超买)。多项信号预示下跌风险。\n\n---\n\n免责申明：本报告仅供参考，不构成投资建议。股市有风险，投资需谨慎。\n\n趋势方向偏中性略偏消极，信心一般。\n\n历史数据显示，当前价格明显高于20日和5日均线，类似情况在过去210次中，有94%和93%的概率随后出现回调；同时RSI指标处于高位，也与多数历史回调前的状态吻合。不过资金面表现亮眼：主力全天大幅流入超2.4亿元，早盘和尾盘均有明显抢筹，且主力占比高达71.7%，主导意愿强；技术面上，价格稳站在VWAP和分时均线之上，均线本身还在上行，短周期动能正由弱转强，盘中强度也处于较高水平。但需注意，资金一致性高、主力与散户同步流入，历史上曾多次出现在诱多阶段，加上K线虽出现低位十字星等温和趋势积极形态，但“蓝带”信号又提示空头氛围尚未完全消退。\n\n综合评分55分，处于中间区域，信号存在明显分歧。一方面主力动作积极，另一方面超买压力突出，历史规律显示此类状态易引发震荡或回调。投资者应重点关注后续几个交易日价格能否站稳智能参考阻力（10.62元）并有效消化超买压力，若出现放量滞涨或快速回落至参考支撑10.47元附近，则需警惕趋势进一步转弱的可能。\n\n## 量价趋势\n\n价格站上5日线(+2.4%)。\n量能维持均量水平(0.83倍)。\n分时图价格运行均线上方(+0.91%)，买方占优，分时趋势弱转强，分时均线上升中。\n5日累计上涨2.5%，短期动能偏强。\n\n## 斐波那契位置与策略\n\n操作剧本（参考）\n\n| 场景 | 怎么看 | 应对 |\n|------|--------|------|\n| 放量上行 | 资金态度积极，突破确认 | 可轻仓跟随，注意止损 |\n| 回踩确认 | 回踩10.47支撑位 | 若不破可加仓 |\n| 假突破回落 | 突破失败，重回区间 | 跌破10.47果断止损 |\n| 横盘消化 | 多空拉锯整理 | 持有观望，等方向选择 |\n\n## 实时资金流\n\n主力今日净买入2.42亿，呈主动买入态势。\n主力占比18.2%，机构资金主导盘面。\n主力-散户差为正(+0.1854)，主力资金占主导，散户被动跟涨。",
        "simple_verdict": {
          "verdict": "建议观望",
          "confidence": "中等",
          "sentiment": "-2",
          "overall_score": 54.9,
          "sentiment_norm": 39.5,
          "tick_norm": 77.5,
          "flow_norm": 43.9,
          "tech_norm": 71.5,
          "kline_norm": 49.0
        }
      }
    },
    "summary": "## SZ.000001 - 2026/07/06 21:35:30\n\n## 快速结论\n\n**建议观望**（信心：中等 | 情绪 -2）\n\n## 综合评分：55分（中性）\n\n| 维度 | 得分 | 权重 | 说明 |\n|------|------|------|------|\n| 综合情绪 | 40/100 | 15% | 偏弱 |\n| 分时强度 | 78/100 | 25% | 强势 |\n| 资金面 | 44/100 | 40% | 偏弱 |\n| 技术面 | 72/100 | 10% | 强势 |\n| K线形态 | 49/100 | 10% | 中性 |\n\n- 分时强度多头强势，盘中买方主导\n- 资金持续流入，主力资金在买入\n- 多条历史规律(4条)提示风险\n\n> 注意：收盘价远高于MA20(深度超买)。多项信号预示下跌风险。\n\n---\n\n免责申明：本报告仅供参考，不构成投资建议。股市有风险，投资需谨慎。\n\n趋势方向偏中性略偏消极，信心一般。\n\n历史数据显示，当前价格明显高于20日和5日均线，类似情况在过去210次中，有94%和93%的概率随后出现回调；同时RSI指标处于高位，也与多数历史回调前的状态吻合。不过资金面表现亮眼：主力全天大幅流入超2.4亿元，早盘和尾盘均有明显抢筹，且主力占比高达71.7%，主导意愿强；技术面上，价格稳站在VWAP和分时均线之上，均线本身还在上行，短周期动能正由弱转强，盘中强度也处于较高水平。但需注意，资金一致性高、主力与散户同步流入，历史上曾多次出现在诱多阶段，加上K线虽出现低位十字星等温和趋势积极形态，但“蓝带”信号又提示空头氛围尚未完全消退。\n\n综合评分55分，处于中间区域，信号存在明显分歧。一方面主力动作积极，另一方面超买压力突出，历史规律显示此类状态易引发震荡或回调。投资者应重点关注后续几个交易日价格能否站稳智能参考阻力（10.62元）并有效消化超买压力，若出现放量滞涨或快速回落至参考支撑10.47元附近，则需警惕趋势进一步转弱的可能。\n\n## 量价趋势\n\n价格站上5日线(+2.4%)。\n量能维持均量水平(0.83倍)。\n分时图价格运行均线上方(+0.91%)，买方占优，分时趋势弱转强，分时均线上升中。\n5日累计上涨2.5%，短期动能偏强。\n\n## 斐波那契位置与策略\n\n操作剧本（参考）\n\n| 场景 | 怎么看 | 应对 |\n|------|--------|------|\n| 放量上行 | 资金态度积极，突破确认 | 可轻仓跟随，注意止损 |\n| 回踩确认 | 回踩10.47支撑位 | 若不破可加仓 |\n| 假突破回落 | 突破失败，重回区间 | 跌破10.47果断止损 |\n| 横盘消化 | 多空拉锯整理 | 持有观望，等方向选择 |\n\n## 实时资金流\n\n主力今日净买入2.42亿，呈主动买入态势。\n主力占比18.2%，机构资金主导盘面。\n主力-散户差为正(+0.1854)，主力资金占主导，散户被动跟涨。",
    "timers": {
      "data_fetch": 0.02,
      "feature_compute": 3.37,
      "rule_match": 0.25,
      "trend_signal": 0.02,
      "news_fetch": 0.0,
      "report_gen": 7.0,
      "total": 14.06
    },
    "cache_stats": {
      "hits": 7,
      "misses": 2,
      "hit_rate": 0.778,
      "l1_size": 10
    },
    "elapsed_seconds": 14.06,
    "timestamp": "2026-07-06T21:35:30"
  },
  "usage": {
    "cost": 3.0,
    "balance": 13511.8,
    "balance_after": 13508.8
  }
}
```

---

### analyze.query

**自然语言查询**

- 耗时: 3.94s
- 响应大小: 6,181 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "analyze.query",
  "query": "帮我看看平安银行怎么样"
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "analyze.query", "query": "帮我看看平安银行怎么样"}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "analyze.query",
  "query": "帮我看看平安银行怎么样"
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "strategy": "stock_diagnosis",
    "disclaimer": "本报告基于股票当前数据和历史数据统计分析，所有提及的概率均基于历史回测数据，不构成任何投资建议。股市有风险，投资需谨慎。",
    "status": "success",
    "session_id": "ad12e16f",
    "feature_groups": [
      "technical",
      "flow_daily",
      "flow_minute",
      "ohlcv_combined"
    ],
    "reasoning": "用户使用轻量动词'帮我看看'，明确指向个股分析（平安银行），且未限定单一维度，因此采用默认完整特征组合，覆盖技术面、资金流（日线+分钟级）及OHLCV综合信号，确保分析全面性。 (报告缓存命中)",
    "results": {
      "SZ.000001": {
        "status": "success",
        "ohlcv": {
          "date": "2026-07-06 00:00:00",
          "open": 10.25,
          "close": 10.5,
          "high": 10.52,
          "low": 10.22,
          "volume": 1061049.0
        },
        "_report": "## 快速结论\n\n**建议观望**（信心：中等 | 情绪 -2）\n\n## 综合评分：55分（中性）\n\n| 维度 | 得分 | 权重 | 说明 |\n|------|------|------|------|\n| 综合情绪 | 40/100 | 15% | 偏弱 |\n| 分时强度 | 78/100 | 25% | 强势 |\n| 资金面 | 44/100 | 40% | 偏弱 |\n| 技术面 | 72/100 | 10% | 强势 |\n| K线形态 | 49/100 | 10% | 中性 |\n\n\n\n---\n\n免责申明：本报告仅供参考，不构成投资建议。股市有风险，投资需谨慎。\n\n趋势方向偏中性略偏消极，信心一般。\n\n历史数据显示，当前价格明显高于20日和5日均线，类似情况在过去210次中，有94%和93%的概率随后出现回调；同时RSI指标处于高位，也与多数历史回调前的状态吻合。不过资金面表现亮眼：主力全天大幅流入超2.4亿元，早盘和尾盘均有明显抢筹，且主力占比高达71.7%，主导意愿强；技术面上，价格稳站在VWAP和分时均线之上，均线本身还在上行，短周期动能正由弱转强，盘中强度也处于较高水平。但需注意，资金一致性高、主力与散户同步流入，历史上曾多次出现在诱多阶段，加上K线虽出现低位十字星等温和趋势积极形态，但“蓝带”信号又提示空头氛围尚未完全消退。\n\n综合评分55分，处于中间区域，信号存在明显分歧。一方面主力动作积极，另一方面超买压力突出，历史规律显示此类状态易引发震荡或回调。投资者应重点关注后续几个交易日价格能否站稳智能参考阻力（10.62元）并有效消化超买压力，若出现放量滞涨或快速回落至参考支撑10.47元附近，则需警惕趋势进一步转弱的可能。\n\n## 量价趋势\n\n价格站上5日线(+2.4%)。\n量能维持均量水平(0.83倍)。\n分时图价格运行均线上方(+0.91%)，买方占优，分时趋势弱转强，分时均线上升中。\n5日累计上涨2.5%，短期动能偏强。\n\n## 斐波那契位置与策略\n\n操作剧本（参考）\n\n| 场景 | 怎么看 | 应对 |\n|------|--------|------|\n| 放量上行 | 资金态度积极，突破确认 | 可轻仓跟随，注意止损 |\n| 回踩确认 | 回踩10.47支撑位 | 若不破可加仓 |\n| 假突破回落 | 突破失败，重回区间 | 跌破10.47果断止损 |\n| 横盘消化 | 多空拉锯整理 | 持有观望，等方向选择 |\n\n## 实时资金流\n\n主力今日净买入2.42亿，呈主动买入态势。\n主力占比18.2%，机构资金主导盘面。\n主力-散户差为正(+0.1854)，主力资金占主导，散户被动跟涨。",
        "simple_verdict": {
          "verdict": "建议观望",
          "confidence": "中等",
          "sentiment": "-2",
          "overall_score": 54.9,
          "sentiment_norm": 39.5,
          "tick_norm": 77.5,
          "flow_norm": 43.9,
          "tech_norm": 71.5,
          "kline_norm": 49.0
        }
      }
    },
    "summary": "## SZ.000001 - 2026/07/06 21:35:29\n\n免责申明：本报告仅供参考，不构成投资建议。股市有风险，投资需谨慎。\n\n趋势方向偏中性略偏消极，信心一般。\n\n历史数据显示，当前价格明显高于20日和5日均线，类似情况在过去210次中，有94%和93%的概率随后出现回调；同时RSI指标处于高位，也与多数历史回调前的状态吻合。不过资金面表现亮眼：主力全天大幅流入超2.4亿元，早盘和尾盘均有明显抢筹，且主力占比高达71.7%，主导意愿强；技术面上，价格稳站在VWAP和分时均线之上，均线本身还在上行，短周期动能正由弱转强，盘中强度也处于较高水平。但需注意，资金一致性高、主力与散户同步流入，历史上曾多次出现在诱多阶段，加上K线虽出现低位十字星等温和趋势积极形态，但“蓝带”信号又提示空头氛围尚未完全消退。\n\n综合评分55分，处于中间区域，信号存在明显分歧。一方面主力动作积极，另一方面超买压力突出，历史规律显示此类状态易引发震荡或回调。投资者应重点关注后续几个交易日价格能否站稳智能参考阻力（10.62元）并有效消化超买压力，若出现放量滞涨或快速回落至参考支撑10.47元附近，则需警惕趋势进一步转弱的可能。\n\n## 量价趋势\n\n价格站上5日线(+2.4%)。\n量能维持均量水平(0.83倍)。\n分时图价格运行均线上方(+0.91%)，买方占优，分时趋势弱转强，分时均线上升中。\n5日累计上涨2.5%，短期动能偏强。\n\n## 斐波那契位置与策略\n\n操作剧本（参考）\n\n| 场景 | 怎么看 | 应对 |\n|------|--------|------|\n| 放量上行 | 资金态度积极，突破确认 | 可轻仓跟随，注意止损 |\n| 回踩确认 | 回踩10.47支撑位 | 若不破可加仓 |\n| 假突破回落 | 突破失败，重回区间 | 跌破10.47果断止损 |\n| 横盘消化 | 多空拉锯整理 | 持有观望，等方向选择 |\n\n## 实时资金流\n\n主力今日净买入2.42亿，呈主动买入态势。\n主力占比18.2%，机构资金主导盘面。\n主力-散户差为正(+0.1854)，主力资金占主导，散户被动跟涨。",
    "timers": {
      "data_fetch": 0.01,
      "feature_compute": 0.81,
      "report_gen": 0,
      "total": 0.82
    },
    "cache_stats": {
      "hits": 18,
      "misses": 0,
      "hit_rate": 1.0,
      "l1_size": 11
    },
    "elapsed_seconds": 0.82,
    "timestamp": "2026-07-06T21:35:34"
  },
  "usage": {
    "cost": 3.0,
    "balance": 13508.8,
    "balance_after": 13505.8
  }
}
```

---

### analyze.manifest

**特征清单**

- 耗时: 0.34s
- 响应大小: 6,540 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "analyze.manifest"
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "analyze.manifest"}

resp = requests.post(url, headers=headers, json=payload, timeout=30)
print(resp.json())
```

请求体：

```json
{
  "api": "analyze.manifest"
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "feature_groups": {
      "k_patterns": {
        "description": "K线形态特征：吞没、十字星、锤子线等20+形态",
        "data_dependencies": [
          "ohlcv_daily"
        ],
        "feature_count": 20,
        "computation": "ohlcv_pipeline",
        "keywords": [
          "K线",
          "形态",
          "吞没",
          "十字星",
          "锤子",
          "反转形态"
        ]
      },
      "lifeline": {
        "description": "生死线指标：红蓝带、三色球、买卖点信号",
        "data_dependencies": [
          "ohlcv_daily"
        ],
        "feature_count": 7,
        "computation": "ohlcv_pipeline",
        "keywords": [
          "生死线",
          "红蓝带",
          "买卖点",
          "趋势线"
        ]
      },
      "experience": {
        "description": "经验特征：价格变动、成交量趋势、Fisher/Calmar信号",
        "data_dependencies": [
          "ohlcv_daily"
        ],
        "feature_count": 15,
        "computation": "ohlcv_pipeline",
        "keywords": [
          "经验",
          "量价",
          "成交量",
          "Fisher",
          "Calmar"
        ]
      },
      "ohlcv_combined": {
        "description": "OHLCV综合特征：K形态+生死线+经验+均线，一次获取",
        "data_dependencies": [
          "ohlcv_daily"
        ],
        "feature_count": 77,
        "computation": "ohlcv_pipeline",
        "keywords": [
          "OHLCV",
          "量价",
          "综合",
          "K线+生死线+均线+V型"
        ]
      },
      "volume_price_base": {
        "description": "量价基础特征：均线偏离/排列/交叉/斜率/位置编码/连涨连跌/放量缩量/量价配合/V型底/倒V顶等35特征",
        "data_dependencies": [
          "ohlcv_daily"
        ],
        "feature_count": 35,
        "computation": "ohlcv_pipeline",
        "keywords": [
          "均线",
          "MA",
          "偏离度",
          "多头排列",
          "金叉",
          "死叉",
          "斜率",
          "均线位置",
          "量价",
          "连涨",
          "连跌",
          "放量",
          "缩量",
          "V型",
          "倒V",
          "趋势反转"
        ]
      },
      "technical": {
        "description": "技术指标特征集：212个特征，含60m时段/日线指标/Z-score多窗口",
        "data_dependencies": [
          "ohlcv_60m"
        ],
        "feature_count": 212,
        "computation": "technical_pipeline",
        "keywords": [
          "技术指标",
          "RSI",
          "MACD",
          "KDJ",
          "布林",
          "BOLL",
          "Z-score",
          "动量",
          "ATR",
          "CCI",
          "FVG"
        ]
      },
      "flow_minute": {
        "description": "分钟级资金流特征：早盘/尾盘资金、洗盘检测、量偏斜等16特征",
        "data_dependencies": [
          "flow_minute"
        ],
        "feature_count": 16,
        "computation": "flow_minute_pipeline",
        "keywords": [
          "分钟资金流",
          "早盘",
          "尾盘",
          "洗盘",
          "日内",
          "量偏斜"
        ]
      },
      "flow_daily": {
        "description": "日线资金流特征：MFS/MFI/MFCS等64特征，含Z-score",
        "data_dependencies": [
          "flow_daily",
          "ohlcv_daily"
        ],
        "feature_count": 64,
        "computation": "flow_daily_pipeline",
        "keywords": [
          "资金流",
          "大单",
          "MFS",
          "MFI",
          "MFCS",
          "主力",
          "散户",
          "资金集中度"
        ]
      },
      "flow_combined": {
        "description": "综合资金流特征：分钟+日线共80特征",
        "data_dependencies": [
          "flow_minute",
          "flow_daily",
          "ohlcv_daily"
        ],
        "feature_count": 80,
        "computation": "flow_combined_pipeline",
        "keywords": [
          "资金流综合",
          "分钟+日线",
          "全天资金流"
        ]
      },
      "hot_sector": {
        "description": "热门板块动态：当日涨停联动分析，发现市场热点与跟风股票（独立策略，不走特征计算流水线）",
        "data_dependencies": [],
        "feature_count": 0,
        "computation": "hot_sector_strategy",
        "keywords": [
          "热门股票",
          "热门股",
          "跟风股",
          "联动",
          "热门板块",
          "热门咨询",
          "关注度",
          "人气股",
          "近期热门"
        ]
      },
      "market_review": {
        "description": "大盘复盘：全市场总结，包括指数表现、涨跌停统计、市场广度、板块行情等（独立策略，不走特征计算流水线）",
        "data_dependencies": [],
        "feature_count": 0,
        "computation": "market_review_strategy",
        "keywords": [
          "大盘",
          "复盘",
          "市场总结",
          "市场行情",
          "今天市场",
          "今日市场",
          "大盘怎么样",
          "市场表现"
        ]
      },
      "sentiment_scan": {
        "description": "舆情观点扫描：从雪球、东方财富论坛等社区搜索当日热门讨论、大V观点、题材方向，LLM总结市场情绪和关注方向（独立策略，不走特征计算流水线）",
        "data_dependencies": [],
        "feature_count": 0,
        "computation": "sentiment_scan_strategy",
        "keywords": [
          "舆情",
          "观点",
          "大V",
          "达人",
          "市场情绪",
          "市场趋势",
          "社区讨论",
          "雪球",
          "东方财富",
          "股吧",
          "热门话题",
          "舆论",
          "看法",
          "怎么看市场"
        ]
      },
      "tick_intraday": {
        "description": "逐笔分时特征：分时均线穿越(V/倒V)、弱转强/强转弱信号、综合强度评分等14特征",
        "data_dependencies": [
          "tick_data"
... (truncated)
```

---

## 评分

### score

**单股五维评分**

- 耗时: 2.24s
- 响应大小: 683 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "score",
  "params": {
    "stock_code": "SZ.000001"
  }
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "score", "params": {"stock_code": "SZ.000001"}}

resp = requests.post(url, headers=headers, json=payload, timeout=30)
print(resp.json())
```

请求体：

```json
{
  "api": "score",
  "params": {
    "stock_code": "SZ.000001"
  }
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_code": "SZ.000001",
    "verdict": "建议观望",
    "confidence": "中等",
    "overall_score": 54.0,
    "dimensions": {
      "综合情绪": {
        "score": 33.5,
        "weight": "30%"
      },
      "分时强度": {
        "score": 77.5,
        "weight": "25%"
      },
      "资金面": {
        "score": 43.9,
        "weight": "20%"
      },
      "技术面": {
        "score": 71.5,
        "weight": "15%"
      },
      "K线形态": {
        "score": 49.0,
        "weight": "10%"
      }
    },
    "reasons": [
      "分时强度多头强势，盘中买方主导",
      "资金持续流入，主力资金在买入",
      "多条历史规律(4条)提示风险"
    ],
    "risk_hint": "注意：收盘价远高于MA20(深度超买)。多项信号预示下跌风险。",
    "data_warnings": []
  },
  "usage": {
    "cost": 1.5,
    "balance": 13505.8,
    "balance_after": 13504.3
  }
}
```

---

### score.batch

**批量五维评分**

- 耗时: 5.61s
- 响应大小: 1,329 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "score.batch",
  "stock_codes": [
    "SZ.000001",
    "SH.600036"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "score.batch", "stock_codes": ["SZ.000001", "SH.600036"]}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "score.batch",
  "stock_codes": [
    "SZ.000001",
    "SH.600036"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "results": [
      {
        "stock_code": "SH.600036",
        "verdict": "建议观望",
        "confidence": "中等",
        "overall_score": 57.7,
        "dimensions": {
          "综合情绪": {
            "score": 33.5,
            "weight": "30%"
          },
          "分时强度": {
            "score": 78.0,
            "weight": "25%"
          },
          "资金面": {
            "score": 52.2,
            "weight": "20%"
          },
          "技术面": {
            "score": 74.0,
            "weight": "15%"
          },
          "K线形态": {
            "score": 49.0,
            "weight": "10%"
          }
        },
        "reasons": [
          "分时强度多头强势，盘中买方主导",
          "资金持续流入，主力资金在买入",
          "多条历史规律(10条)提示风险"
        ],
        "risk_hint": "注意：收盘价远高于MA20(深度超买)。多项信号预示下跌风险。",
        "data_warnings": []
      },
      {
        "stock_code": "SZ.000001",
        "verdict": "建议观望",
        "confidence": "中等",
        "overall_score": 54.0,
        "dimensions": {
          "综合情绪": {
            "score": 33.5,
            "weight": "30%"
          },
          "分时强度": {
            "score": 77.5,
            "weight": "25%"
          },
          "资金面": {
            "score": 43.9,
            "weight": "20%"
          },
          "技术面": {
            "score": 71.5,
            "weight": "15%"
          },
          "K线形态": {
            "score": 49.0,
            "weight": "10%"
          }
        },
        "reasons": [
          "分时强度多头强势，盘中买方主导",
          "资金持续流入，主力资金在买入",
          "多条历史规律(4条)提示风险"
        ],
        "risk_hint": "注意：收盘价远高于MA20(深度超买)。多项信号预示下跌风险。",
        "data_warnings": []
      }
    ],
    "errors": [],
    "total": 2,
    "success": 2,
    "failed": 0
  },
  "usage": {
    "cost": 3.0,
    "balance": 13504.3,
    "balance_after": 13501.3
  }
}
```

---

### scores.recent

**近期高分评分**

- 耗时: 0.48s
- 响应大小: 2,586 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "scores.recent",
  "params": {
    "min_score": 60,
    "hours": 48
  }
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "scores.recent", "params": {"min_score": 60, "hours": 48}}

resp = requests.post(url, headers=headers, json=payload, timeout=30)
print(resp.json())
```

请求体：

```json
{
  "api": "scores.recent",
  "params": {
    "min_score": 60,
    "hours": 48
  }
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "total": 8,
    "min_score": 60.0,
    "hours": 48,
    "scores": [
      {
        "code": "SZ.300759",
        "name": "康龙化成",
        "sector": "",
        "score_date": "2026-07-06",
        "timestamp": "2026-07-06T13:35",
        "source": "limitup_review",
        "sub_sources": [],
        "overall_score": 60.5,
        "sentiment_norm": 33.5,
        "tick_norm": 88.5,
        "flow_norm": 50.8,
        "tech_norm": 82.0,
        "kline_norm": 48.0,
        "price": 31.14,
        "pct_change": 5.31
      },
      {
        "code": "SH.605305",
        "name": "中际联合",
        "sector": "专用设备",
        "score_date": "2026-07-06",
        "timestamp": "2026-07-06T15:40",
        "source": "benchmark",
        "sub_sources": [],
        "overall_score": 60.5,
        "sentiment_norm": 41.0,
        "tick_norm": 76.5,
        "flow_norm": 54.6,
        "tech_norm": 81.5,
        "kline_norm": 52.0,
        "price": 47.57,
        "pct_change": 3.46
      },
      {
        "code": "SH.600276",
        "name": "恒瑞医药",
        "sector": "",
        "score_date": "2026-07-06",
        "timestamp": "2026-07-06T13:34",
        "source": "limitup_review",
        "sub_sources": [],
        "overall_score": 60.3,
        "sentiment_norm": 33.5,
        "tick_norm": 84.5,
        "flow_norm": 50.9,
        "tech_norm": 84.5,
        "kline_norm": 53.0,
        "price": 56.74,
        "pct_change": 4.21
      },
      {
        "code": "SZ.300684",
        "name": "中石科技",
        "sector": "新材料",
        "score_date": "2026-07-06",
        "timestamp": "2026-07-06T10:58",
        "source": "benchmark",
        "sub_sources": [],
        "overall_score": 60.3,
        "sentiment_norm": 41.0,
        "tick_norm": 85.0,
        "flow_norm": 46.3,
        "tech_norm": 97.0,
        "kline_norm": 47.0,
        "price": 74.03,
        "pct_change": 19.38
      },
      {
        "code": "SZ.301165",
        "name": "锐捷网络",
        "sector": "5G通信",
        "score_date": "2026-07-06",
        "timestamp": "2026-07-06T15:40",
        "source": "benchmark",
        "sub_sources": [],
        "overall_score": 60.3,
        "sentiment_norm": 41.0,
        "tick_norm": 73.5,
        "flow_norm": 57.0,
        "tech_norm": 78.5,
        "kline_norm": 51.0,
        "price": 102.33,
        "pct_change": 7.84
      },
      {
        "code": "SZ.000566",
        "name": "海南海药",
        "sector": "化学制药",
        "score_date": "2026-07-06",
        "timestamp": "2026-07-06T15:40",
        "source": "benchmark",
        "sub_sources": [],
        "overall_score": 60.2,
        "sentiment_norm": 41.0,
        "tick_norm": 82.5,
        "flow_norm": 51.4,
        "tech_norm": 78.0,
        "kline_norm": 51.0,
        "price": 6.5,
        "pct_change": 5.86
      },
      {
        "code": "SZ.002793",
        "name": "罗欣药业",
        "sector": "化学制药",
        "score_date": "2026-07-06",
        "timestamp": "2026-07-06T13:50",
        "source": "benchmark",
        "sub_sources": [],
        "overall_score": 60.1,
        "sentiment_norm": 43.5,
        "tick_norm": 91.0,
        "flow_norm": 53.0,
        "tech_norm": 47.5,
        "kline_norm": 49.0,
        "price": 4.69,
        "pct_change": 8.82
      },
      {
        "code": "SH.688192",
        "name": "迪哲医药",
        "sector": "",
        "score_date": "2026-07-05",
        "timestamp": "2026-07-05T17:59",
        "source": "strategy_screener",
        "sub_sources": [
          "高低回撤"
        ],
        "overall_score": 62.5,
        "sentiment_norm": 49.0,
        "tick_norm": 87.5,
        "flow_norm": 45.7,
        "tech_norm": 100.0,
        "kline_norm": 50.0,
        "price": 48.48,
        "pct_change": 11.12
      }
    ]
  },
  "usage": {
    "cost": 3.0,
    "balance": 13501.3,
    "balance_after": 13498.3
  }
}
```

---

## 资金流

### flow.minute

**分钟级资金流**

- 耗时: 3.34s
- 响应大小: 2,486 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "flow.minute",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "flow.minute", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "flow.minute",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "results": {
      "SZ.000001": {
        "status": "success",
        "data": {
          "stock_code": {
            "value": "sz000001",
            "label": "股票代码",
            "description": "带市场前缀的股票代码"
          },
          "stock_name": {
            "value": "平安银行",
            "label": "股票名称",
            "description": "股票中文名称"
          },
          "date": {
            "label": "date",
            "description": ""
          },
          "price": {
            "value": 10.5,
            "label": "最新价",
            "description": "当前最新成交价格(元)"
          },
          "change_pct": {
            "value": 2.04082,
            "label": "涨跌幅",
            "description": "当日价格变动百分比(%)"
          },
          "netamount": {
            "value": 241845029.07,
            "label": "主力净流入",
            "description": "超大单+大单净流入金额(元)"
          },
          "主力净_flow": {
            "value": 241845029.07,
            "label": "主力净流入",
            "description": "超大单+大单净流入金额(元)"
          },
          "主力罗盘度": {
            "value": 85.0451,
            "label": "主力罗盘度",
            "description": "主力资金方向集中度指标，值越大方向越一致"
          },
          "特大单流入": {
            "value": 419889777,
            "label": "特大单流入",
            "description": "单笔>100万的买入金额(元)"
          },
          "特大单流出": {
            "value": 161917753,
            "label": "特大单流出",
            "description": "单笔>100万的卖出金额(元)"
          },
          "特大单净额": {
            "value": 257972023,
            "label": "特大单净额",
            "description": "特大单流入-特大单流出(元)"
          },
          "大单流入": {
            "value": 153736914,
            "label": "大单流入",
            "description": "单笔20-100万的买入金额(元)"
          },
          "小单流入": {
            "value": 75724857,
            "label": "中单流入",
            "description": "单笔4-20万的买入金额(元)"
          },
          "散单流入": {
            "value": 19032576,
            "label": "小单流入",
            "description": "单笔<4万的买入金额(元)"
          },
          "r0_in": {
            "value": 419889777,
            "label": "超大单买入(r0)",
            "description": "单笔>100万的买入金额(元)"
          },
          "r0_out": {
            "value": 161917753,
            "label": "超大单卖出(r0)",
            "description": "单笔>100万的卖出金额(元)"
          },
          "r1_in": {
            "value": 153736914,
            "label": "大单买入(r1)",
            "description": "单笔20-100万的买入金额(元)"
          },
          "r2_in": {
            "value": 75724857,
            "label": "中单买入(r2)",
            "description": "单笔4-20万的买入金额(元)"
          },
          "r3_in": {
            "value": 19032576,
            "label": "小单买入(r3)",
            "description": "单笔<4万的买入金额(元)"
          },
          "volume": {
            "value": 105341463,
            "label": "成交量",
            "description": "当日总成交股数"
          },
          "turnover": {
            "value": 54.284,
            "label": "成交额",
            "description": "当日总成交金额(万元)"
          }
        },
        "timestamp": "2026-07-06T21:35:46.443843"
      }
    },
    "summary": {
      "total": 1,
      "success": 1,
      "failed": 0
    },
    "timestamp": "2026-07-06T21:35:46.443909"
  },
  "usage": {
    "cost": 0.1,
    "balance": 13498.3,
    "balance_after": 13498.2
  }
}
```

---

### flow.daily

**日级资金流**

- 耗时: 1.07s
- 响应大小: 764 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "flow.daily",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "flow.daily", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "flow.daily",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "market": "sz",
    "results": {
      "SZ.000001": {
        "status": "success",
        "data": {
          "data_count": {
            "value": 30,
            "label": "数据条数",
            "description": "历史资金流数据记录数"
          },
          "latest_date": {
            "value": "2026-07-06",
            "label": "最新日期",
            "description": "最近一条资金流记录的日期"
          },
          "mean": {
            "value": 15705361.524666658,
            "label": "主力净流入均值",
            "description": "历史主力净流入平均值(元)"
          },
          "latest": {
            "value": 241845029.07,
            "label": "最新主力净流入",
            "description": "最近一日主力净流入(元)"
          }
        },
        "timestamp": "2026-07-06T21:35:47.517902"
      }
    },
    "summary": {
      "total": 1,
      "success": 1,
      "failed": 0
    },
    "timestamp": "2026-07-06T21:35:47.518947"
  },
  "usage": {
    "cost": 0.1,
    "balance": 13498.2,
    "balance_after": 13498.1
  }
}
```

---

### flow.combined

**合并资金流**

- 耗时: 10.47s
- 响应大小: 65,151 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "flow.combined",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "flow.combined", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=120)
print(resp.json())
```

请求体：

```json
{
  "api": "flow.combined",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "market": "sz",
    "results": {
      "SZ.000001": {
        "status": "success",
        "minute_flow": {
          "stock_code": {
            "value": "sz000001",
            "label": "股票代码",
            "description": "带市场前缀的股票代码",
            "unit": ""
          },
          "stock_name": {
            "value": "平安银行",
            "label": "股票名称",
            "description": "股票中文名称",
            "unit": ""
          },
          "date": {
            "label": "date",
            "description": "",
            "unit": ""
          },
          "price": {
            "value": 10.5,
            "label": "最新价",
            "description": "当前最新成交价格",
            "unit": "元"
          },
          "change_pct": {
            "value": 2.04082,
            "label": "涨跌幅",
            "description": "当日价格变动百分比",
            "unit": "%"
          },
          "netamount": {
            "value": 241845029.07,
            "label": "主力净流入",
            "description": "超大单+大单净流入金额",
            "unit": "元"
          },
          "主力净_flow": {
            "value": 241845029.07,
            "label": "主力净流入",
            "description": "超大单+大单净流入金额(中文键)",
            "unit": "元"
          },
          "主力罗盘度": {
            "value": 85.0451,
            "label": "主力罗盘度",
            "description": "主力资金方向集中度指标，值越大方向越一致",
            "unit": ""
          },
          "特大单流入": {
            "value": 419889777,
            "label": "特大单流入",
            "description": "单笔>100万的买入金额",
            "unit": "元"
          },
          "特大单流出": {
            "value": 161917753,
            "label": "特大单流出",
            "description": "单笔>100万的卖出金额",
            "unit": "元"
          },
          "特大单净额": {
            "value": 257972023,
            "label": "特大单净额",
            "description": "特大单流入-特大单流出",
            "unit": "元"
          },
          "大单流入": {
            "value": 153736914,
            "label": "大单流入",
            "description": "单笔20-100万的买入金额",
            "unit": "元"
          },
          "小单流入": {
            "value": 75724857,
            "label": "中单流入",
            "description": "单笔4-20万的买入金额",
            "unit": "元"
          },
          "散单流入": {
            "value": 19032576,
            "label": "小单流入",
            "description": "单笔<4万的买入金额",
            "unit": "元"
          },
          "r0_in": {
            "value": 419889777,
            "label": "超大单买入(r0)",
            "description": "单笔>100万的买入金额",
            "unit": "元"
          },
          "r0_out": {
            "value": 161917753,
            "label": "超大单卖出(r0)",
            "description": "单笔>100万的卖出金额",
            "unit": "元"
          },
          "r1_in": {
            "value": 153736914,
            "label": "大单买入(r1)",
            "description": "单笔20-100万的买入金额",
            "unit": "元"
          },
          "r2_in": {
            "value": 75724857,
            "label": "中单买入(r2)",
            "description": "单笔4-20万的买入金额",
            "unit": "元"
          },
          "r3_in": {
            "value": 19032576,
            "label": "小单买入(r3)",
            "description": "单笔<4万的买入金额",
            "unit": "元"
          },
          "volume": {
            "value": 105341463,
            "label": "成交量",
            "description": "当日总成交股数",
            "unit": "股"
          },
          "turnover": {
            "value": 54.284,
            "label": "成交额",
            "description": "当日总成交金额",
            "unit": "万元"
          }
        },
        "daily_flow": [
          {
            "trade_date": {
              "value": "2026-07-06",
              "label": "交易日期",
              "description": "该条记录对应的交易日",
              "unit": ""
            },
            "stock_code": {
              "value": "sz000001",
              "label": "股票代码",
              "description": "带市场前缀的股票代码",
              "unit": ""
            },
            "stock_name": {
              "label": "stock_name",
              "description": "",
              "unit": ""
            },
            "netamount": {
              "value": 241845029.07,
              "label": "主力净流入",
              "description": "超大单+大单净流入金额",
              "unit": "元"
            },
            "r0_in": {
              "value": 419889777.25,
              "label": "超大单买入(r0)",
              "description": "单笔>100万的买入金额",
              "unit": "元"
            },
            "r0_out": {
              "value": 161917753.60000002,
              "label": "超大单卖出(r0)",
              "description": "单笔>100万的卖出金额",
              "unit": "元"
            },
            "r0_net": {
              "value": 257972023.65,
              "label": "超大单净额(r0)",
              "description": "超大单买入-超大单卖出",
              "unit": "元"
            },
            "r0_ratio": {
              "value": 0.44339753263955195,
              "label": "超大单占比",
              "description": "超大单净流入占总成交额比例
... (truncated)
```

---

## 资金流特征

### flow_features.minute

**分钟级资金流特征**

- 耗时: 1.57s
- 响应大小: 3,196 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "flow_features.minute",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "flow_features.minute", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "flow_features.minute",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "market": "sz",
    "results": {
      "SZ.000001": {
        "status": "success",
        "trade_date": "2026-07-06",
        "features": {
          "morning_30m_net": {
            "value": 10386667.85,
            "description": "早盘30分钟累积净流入(元), >0开盘资金看多, <0开盘资金看空, |值|>5000万=强信号",
            "signal": "：早盘资金积极流入"
          },
          "morning_30m_r0": {
            "value": 0.01296462766666667,
            "description": "早盘30分钟主力占比均值(-1~1), >0.1主力主导买入, <-0.1主力主导卖出",
            "signal": ""
          },
          "closing_30m_net": {
            "value": 2006431.919999987,
            "description": "尾盘30分钟净流入增量(元), >0尾盘资金回流(博弈次日溢价), <0尾盘撤离",
            "signal": "：尾盘资金回流"
          },
          "closing_30m_surge": {
            "value": -0.4452,
            "description": "尾盘放量程度=尾盘均量/全天均量-1, >0.5尾盘明显放量, <-0.3尾盘缩量",
            "signal": "：尾盘缩量"
          },
          "intraday_accumulate": {
            "value": 0.0,
            "description": "日内吸筹, 1=价格波动<2%且净流入>0(横盘吸筹)",
            "signal": ""
          },
          "intraday_distribute": {
            "value": 0.0,
            "description": "日内出货, 1=价格涨幅<1%且净流入<0(滞涨出货)",
            "signal": ""
          },
          "flow_turn_positive_pct": {
            "value": 0.0126,
            "description": "资金转正位置(0~1或-1), 0=开盘即正(最强), <0.3=早盘转正(较强), >0.7=尾盘才转正(弱), -1=全天未转正",
            "signal": "：早盘转正，资金较早确认方向"
          },
          "sell_exhaust": {
            "value": 0.0,
            "description": "卖单衰竭, 1=后半段流出量<前半段60%且资金开始回流(日内低吸信号)",
            "signal": ""
          },
          "mf_intraday_std": {
            "value": 5424074.988247766,
            "description": "日内资金流波动率(元), 越大博弈越激烈, 结合方向判断是争夺还是单边",
            "signal": ""
          },
          "r0_r3_spread": {
            "value": 0.185432,
            "description": "主力vs散户日内占比差(-1~1), >0.15主力明显主导, <-0.15散户主导主力缺席",
            "signal": "：主力明显主导"
          },
          "wash_trade_ratio": {
            "value": 0.105,
            "description": "疑似对倒分钟占比(0~1), >0.2对倒嫌疑高, <0.05正常",
            "signal": ""
          },
          "wash_trade_signal": {
            "value": 0.0,
            "description": "对倒信号, 1=超过20%分钟流入≈流出且成交活跃(自买自卖)",
            "signal": ""
          },
          "vol_skew": {
            "value": 4.1225,
            "description": "日内成交量偏度, >1.5正偏=尾盘抢筹, <-1.5负偏=早盘集中出货, -1~1正常",
            "signal": "：尾盘抢筹明显"
          },
          "vol_pulse_regularity": {
            "value": 0.1198,
            "description": "量能脉冲规律性=1/(放量间隔标准差+1), >0.3规律脉冲=程序化拆单吸筹嫌疑, <0.1无规律",
            "signal": ""
          },
          "price_mf_efficiency": {
            "value": 0.004128,
            "description": "资金推动效率=(收盘-开盘)/开盘÷(总资金流/亿), >0.05高效推动, <0低效=对倒或阻力大",
            "signal": ""
          },
          "intraday_drawdown_ctrl": {
            "value": 0.9952,
            "description": "日内回撤控制力=(1-最大回撤)×sign(净流入), >0.9回撤极小+流入=强控盘, <-0.5回撤大+流出=失控",
            "signal": "：回撤极小+资金流入，主力强控盘"
          },
          "intraday_net_amount": {
            "value": 241845029.07,
            "description": "",
            "signal": ""
          },
          "intraday_r0_avg": {
            "value": 0.182281,
            "description": "",
            "signal": ""
          }
        },
        "feature_count": 18
      }
    },
    "summary": {
      "total": 1,
      "success": 1,
      "failed": 0
    },
    "timestamp": "2026-07-06T21:35:59.557731"
  },
  "usage": {
    "cost": 0.2,
    "balance": 13497.9,
    "balance_after": 13497.7
  }
}
```

---

### flow_features.daily

**日级资金流特征**

- 耗时: 6.37s
- 响应大小: 8,839 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "flow_features.daily",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "flow_features.daily", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=120)
print(resp.json())
```

请求体：

```json
{
  "api": "flow_features.daily",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "market": "sz",
    "results": {
      "SZ.000001": {
        "status": "success",
        "trade_date": "2026-07-06 00:00:00",
        "data_count": 200,
        "features": {
          "mfs": {
            "value": 0.434671967160764,
            "description": "MFS资金流强度=(主力净流入-散户净流入)/成交量, >0.3强势, <-0.3弱势",
            "signal": ""
          },
          "mfs_ma5": {
            "value": -0.5227618736511531,
            "description": "MFS 5日均值, >0资金趋势偏多, <0偏空",
            "signal": ""
          },
          "mfs_ma10": {
            "value": -0.7036859091335943,
            "description": "MFS 10日均值, >0中期资金偏多, <0偏空",
            "signal": ""
          },
          "mf_ma5": {
            "value": 26837906.918000013,
            "description": "主力净流入5日均线(元), >0短期资金流入, <0流出",
            "signal": ""
          },
          "mf_ma10": {
            "value": -12945392.968999997,
            "description": "主力净流入10日均线(元), >0中期流入, <0流出",
            "signal": ""
          },
          "mf_ma20": {
            "value": 24153057.893999998,
            "description": "主力净流入20日均线(元), >0长期流入, <0流出",
            "signal": ""
          },
          "mf_ma_cross": {
            "value": 1.0,
            "description": "资金均线交叉, +1=5日上穿20日(金叉看多), -1=下穿(死叉看空)",
            "signal": "：短期均线上穿长期，资金趋势转多"
          },
          "mf_bias": {
            "value": 5.0,
            "description": "净流入偏离度=(当日-MA20)/|MA20|, >2异常偏高, <-2异常偏低",
            "signal": ""
          },
          "mfd": {
            "value": 0.0,
            "description": "MFD背离率=(价格变化率-净流入变化率)/价格变化率, |值|>1背离明显",
            "signal": ""
          },
          "mfd_ma5": {
            "value": 0.0,
            "description": "背离率5日均值, 持续>0.5价格虚涨, 持续<-0.5资金暗流入",
            "signal": ""
          },
          "inst_share": {
            "value": 0.717335983580382,
            "description": "主力资金占比=主力净流入/(|主力|+|散户|), >0.6主力主导, <-0.6散户主导",
            "signal": ""
          },
          "inst_share_ma5": {
            "value": 0.0839918199623332,
            "description": "主力占比5日均值, >0.3持续主力主导, <-0.3持续散户主导",
            "signal": ""
          },
          "mfc_10": {
            "value": 0.5930159137288775,
            "description": "资金流集中度10日=1/std(净流入), 归一化0~1, >0.5资金稳定集中, <0.2分散",
            "signal": ""
          },
          "mfc_20": {
            "value": 0.5367274876739292,
            "description": "资金流集中度20日, >0.5长期稳定, <0.2长期分散",
            "signal": ""
          },
          "bofi": {
            "value": 0.44339753263955195,
            "description": "BOFI大单流强度=特大单净额/(流入+流出), >0.6强势攻击, <-0.3大单撤退",
            "signal": ""
          },
          "bofi_ma5": {
            "value": 0.058831934336160296,
            "description": "BOFI 5日均值, >0.3持续大单买入, <-0.2持续大单卖出",
            "signal": ""
          },
          "mfcs": {
            "value": 1.3366514942690706,
            "description": "MFCS综合评分=0.5×z(主力)+0.3×z(大单)-0.2×z(散户), >1.5强势, <-1.5弱势",
            "signal": ""
          },
          "mfcs_ma5": {
            "value": 0.2042709564501081,
            "description": "综合评分5日均值, >1持续强势, <-1持续弱势",
            "signal": ""
          },
          "mfai": {
            "value": 1.340145649755896,
            "description": "MFAI异常Z值=(当日净流入-均值)/标准差, |值|>2.5异常, >2.5异常流入, <-2.5异常流出",
            "signal": ""
          },
          "mfmo": {
            "value": 16389699.23772493,
            "description": "MFMO动量=EMA12-EMA26, >0资金动量向上, <0向下",
            "signal": ""
          },
          "mfmo_signal": {
            "value": 399749.3662229264,
            "description": "MFMO信号线=EMA9, MFMO上穿信号线=金叉",
            "signal": ""
          },
          "mfmo_hist": {
            "value": 15989949.871502005,
            "description": "MFMO柱状图=MFMO-Signal, >0且递增=加速流入, <0且递减=加速流出",
            "signal": "：资金动量加速"
          },
          "mfi_14": {
            "description": "MFI 14日(0~100), >80超买警惕回调, <20超卖关注反弹, 40~60中性",
            "signal": ""
          },
          "mfi_9": {
            "description": "MFI 9日(0~100), 更灵敏, >80超买, <20超卖",
            "signal": ""
          },
          "mfi_overbought": {
            "value": 0.0,
            "description": "MFI超买标记, 1=MFI>80进入超买区",
            "signal": ""
          },
          "mfi_oversold": {
            "value": 0.0,
            "description": "MFI超卖标记, 1=MFI<20进入超卖区",
            "signal": ""
          },
          "mf_streak": {
            "value": 1.0,
            "description": "连续净流入天数, >=3天持续流入趋势确认, <=-3天持续流出趋势确认",
            "signal": ""
          },
          "mf_price_corr_10": {
            "description": "价格-资金流10日相关性(-1~1), >0.5同步性强, <-0.3背离",
            "signal": ""
          },
          "mf_price_corr_20": {
            "description": "价格-资金流20日相关性(-1~1), >0.5中期同步, <-0.3中期背离",
            "signal": ""
          },
          "mfi_trap_bull": {
            "value": 0.0,
   
... (truncated)
```

---

### flow_features.combined

**合并资金流特征**

- 耗时: 2.54s
- 响应大小: 11,910 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "flow_features.combined",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "flow_features.combined", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=120)
print(resp.json())
```

请求体：

```json
{
  "api": "flow_features.combined",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "market": "sz",
    "results": {
      "SZ.000001": {
        "status": "success",
        "stock_code": "SZ.000001",
        "minute_features": {
          "trade_date": "2026-07-06",
          "data": {
            "morning_30m_net": {
              "value": 10386667.85,
              "description": "早盘30分钟累积净流入(元), >0开盘资金看多, <0开盘资金看空, |值|>5000万=强信号",
              "signal": "：早盘资金积极流入"
            },
            "morning_30m_r0": {
              "value": 0.01296462766666667,
              "description": "早盘30分钟主力占比均值(-1~1), >0.1主力主导买入, <-0.1主力主导卖出",
              "signal": ""
            },
            "closing_30m_net": {
              "value": 2006431.919999987,
              "description": "尾盘30分钟净流入增量(元), >0尾盘资金回流(博弈次日溢价), <0尾盘撤离",
              "signal": "：尾盘资金回流"
            },
            "closing_30m_surge": {
              "value": -0.4452,
              "description": "尾盘放量程度=尾盘均量/全天均量-1, >0.5尾盘明显放量, <-0.3尾盘缩量",
              "signal": "：尾盘缩量"
            },
            "intraday_accumulate": {
              "value": 0.0,
              "description": "日内吸筹, 1=价格波动<2%且净流入>0(横盘吸筹)",
              "signal": ""
            },
            "intraday_distribute": {
              "value": 0.0,
              "description": "日内出货, 1=价格涨幅<1%且净流入<0(滞涨出货)",
              "signal": ""
            },
            "flow_turn_positive_pct": {
              "value": 0.0126,
              "description": "资金转正位置(0~1或-1), 0=开盘即正(最强), <0.3=早盘转正(较强), >0.7=尾盘才转正(弱), -1=全天未转正",
              "signal": "：早盘转正，资金较早确认方向"
            },
            "sell_exhaust": {
              "value": 0.0,
              "description": "卖单衰竭, 1=后半段流出量<前半段60%且资金开始回流(日内低吸信号)",
              "signal": ""
            },
            "mf_intraday_std": {
              "value": 5424074.988247766,
              "description": "日内资金流波动率(元), 越大博弈越激烈, 结合方向判断是争夺还是单边",
              "signal": ""
            },
            "r0_r3_spread": {
              "value": 0.185432,
              "description": "主力vs散户日内占比差(-1~1), >0.15主力明显主导, <-0.15散户主导主力缺席",
              "signal": "：主力明显主导"
            },
            "wash_trade_ratio": {
              "value": 0.105,
              "description": "疑似对倒分钟占比(0~1), >0.2对倒嫌疑高, <0.05正常",
              "signal": ""
            },
            "wash_trade_signal": {
              "value": 0.0,
              "description": "对倒信号, 1=超过20%分钟流入≈流出且成交活跃(自买自卖)",
              "signal": ""
            },
            "vol_skew": {
              "value": 4.1225,
              "description": "日内成交量偏度, >1.5正偏=尾盘抢筹, <-1.5负偏=早盘集中出货, -1~1正常",
              "signal": "：尾盘抢筹明显"
            },
            "vol_pulse_regularity": {
              "value": 0.1198,
              "description": "量能脉冲规律性=1/(放量间隔标准差+1), >0.3规律脉冲=程序化拆单吸筹嫌疑, <0.1无规律",
              "signal": ""
            },
            "price_mf_efficiency": {
              "value": 0.004128,
              "description": "资金推动效率=(收盘-开盘)/开盘÷(总资金流/亿), >0.05高效推动, <0低效=对倒或阻力大",
              "signal": ""
            },
            "intraday_drawdown_ctrl": {
              "value": 0.9952,
              "description": "日内回撤控制力=(1-最大回撤)×sign(净流入), >0.9回撤极小+流入=强控盘, <-0.5回撤大+流出=失控",
              "signal": "：回撤极小+资金流入，主力强控盘"
            },
            "intraday_net_amount": {
              "value": 241845029.07,
              "description": "",
              "signal": ""
            },
            "intraday_r0_avg": {
              "value": 0.182281,
              "description": "",
              "signal": ""
            }
          }
        },
        "daily_features": {
          "trade_date": "2026-07-06",
          "data": {
            "mfs": {
              "value": 0.0,
              "description": "MFS资金流强度=(主力净流入-散户净流入)/成交量, >0.3强势, <-0.3弱势",
              "signal": ""
            },
            "mfs_ma5": {
              "value": 0.0,
              "description": "MFS 5日均值, >0资金趋势偏多, <0偏空",
              "signal": ""
            },
            "mfs_ma10": {
              "value": 0.0,
              "description": "MFS 10日均值, >0中期资金偏多, <0偏空",
              "signal": ""
            },
            "mf_ma5": {
              "value": 26837906.918000013,
              "description": "主力净流入5日均线(元), >0短期资金流入, <0流出",
              "signal": ""
            },
            "mf_ma10": {
              "value": -12945392.968999997,
              "description": "主力净流入10日均线(元), >0中期流入, <0流出",
              "signal": ""
            },
            "mf_ma20": {
              "value": 24153057.89399999,
              "description": "主力净流入20日均线(元), >0长期流入, <0流出",
              "signal": ""
            },
            "mf_ma_cross": {
              "value": 1.0,
              "description": "资金均线交叉, +1=5日上穿20日(金叉看多), -1=下穿(死叉看空)",
              "signal": "：短期均线上穿长期，资金趋势转多"
            },
            "mf_bias": {
              "value": 5.0,
              "description": "净流入偏离度=(当日-MA20)/|MA20|, >2异
... (truncated)
```

---

## 特征计算

### features.k_patterns

**K线形态特征(18种)**

- 耗时: 2.45s
- 响应大小: 4,878 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "features.k_patterns",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "features.k_patterns", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "features.k_patterns",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "market": "sz",
    "frequency": "1d",
    "results": {
      "SZ.000001": {
        "status": "success",
        "data_count": 301,
        "k_patterns": {},
        "latest_features": {
          "date": "2026-07-06T00:00:00",
          "open": 10.25,
          "close": 10.5,
          "high": 10.52,
          "low": 10.22,
          "volume": 1061049.0,
          "pattern_hammer": 0,
          "pattern_hanging_man": 0,
          "pattern_morning_star": 0,
          "pattern_evening_star": 0,
          "pattern_bullish_engulf": 0,
          "pattern_bearish_engulf": 0,
          "pattern_bullish_harami": 0,
          "pattern_bearish_harami": 0,
          "pattern_piercing": 0,
          "pattern_dark_cloud": 0,
          "pattern_three_soldiers": 0,
          "pattern_three_crows": 0,
          "pattern_rising_three": 0,
          "pattern_falling_three": 0,
          "pattern_side_by_side_bull": 0,
          "pattern_side_by_side_bear": 0,
          "pattern_bull_count": 0,
          "pattern_bear_count": 0,
          "vol_sell_signal": 0,
          "vol_ratio_vs_peak": 0.0,
          "pattern_score": 0,
          "lifeline_score": 0,
          "total_score": 0,
          "fairy_guide_signal": 0,
          "fairy_guide_strict": 0,
          "fairy_guide_buy": 0,
          "low_cross_star": 0,
          "low_cross_star_buy": 1,
          "three_bear_hold_yang": 0,
          "three_bear_hold_yang_buy": 0,
          "limit_up_first_bear": 0,
          "limit_up_first_bear_buy": 0,
          "fisher_value": -0.12495256848836869,
          "fisher_buy": 0,
          "fisher_sell": 0,
          "calmar_ratio": -0.32859745665986334,
          "calmar_buy": 0,
          "calmar_strong_buy": 0,
          "calmar_sell": 1,
          "guppy_break_price": 10.74,
          "guppy_countdown_buy": 0,
          "alligator_sleep": 0,
          "alligator_bull": 0,
          "alligator_bear": 0,
          "keltner_bull": 0,
          "keltner_bear": 0,
          "hurst_oversold": 0,
          "hurst_buy": 0,
          "hurst_overbought": 0,
          "obv_bull_divergence": 0,
          "obv_divergence_buy": 0,
          "obv_bear_divergence": 0,
          "four_price_bull": 0,
          "four_price_bear": 0,
          "v2560_bull": 0,
          "v2560_pullback_buy": 0,
          "rsi_oversold_buy": 0,
          "rsi_overbought_sell": 0,
          "boll_squeeze": 0,
          "boll_squeeze_bull": 0,
          "boll_squeeze_bear": 0,
          "macd_moat_buy": 0,
          "macd_moat_rebound": 0,
          "combo_bull": 0,
          "combo_bear": 0,
          "limit_double_cannon": 0,
          "triple_gap_top": 0,
          "triple_gap_bottom": 0,
          "gap_fill_sell": 0,
          "gap_fill_buy": 0,
          "inverted_hammer": 0,
          "macd_buddha_hand": 0,
          "td9_buy": 0,
          "td9_sell": 0,
          "fib_reversal_buy": 0,
          "fib_reversal_sell": 0,
          "cup_handle_buy": 0,
          "triple_bottom_buy": 0,
          "island_reversal_buy": 0,
          "island_reversal_sell": 0,
          "harami_bear": 0,
          "harami_bull": 0,
          "single_gun": 0,
          "single_gun_buy": 0,
          "golden_ratio_buy": 0,
          "golden_ratio_zone": 0,
          "golden_ratio_resonance": 0,
          "golden_ratio_stop": 0,
          "golden_ratio_retracement": 0.5952380952380953,
          "golden_ratio_target": 12.02868,
          "golden_ratio_support_618": 10.47132,
          "golden_ratio_support_500": 10.620000000000001,
          "golden_ratio_support_809": 10.23066,
          "golden_ratio_resistance_high": 11.25,
          "golden_ratio_resistance_236": 10.95264,
          "golden_ratio_resistance_382": 10.76868,
          "golden_ratio_swing_low": 9.99,
          "golden_ratio_risk_reward": 5.68,
          "air_refuel_buy": 0,
          "platform_breakout": 0,
          "double_yang_bottom": 0,
          "old_duck_head": 0,
          "old_duck_head_buy": 0,
          "smart_fib_support": 10.47,
          "smart_fib_resistance": 10.62,
          "smart_fib_stop_loss": 10.29,
          "smart_fib_risk_reward": 0.6,
          "smart_fib_swing_high": 11.25,
          "smart_fib_swing_low": 9.99,
          "smart_fib_position_in_range": 0.4,
          "smart_fib_risk": 0.21,
          "smart_fib_reward": 0.12,
          "smart_fib_type": "extension",
          "smart_fib_closer_to_low": 1,
          "smart_fib_closer_to_high": 0,
          "trade_date": "2026-07-06",
          "sector": "银行",
          "industry": "银行",
          "ma55": 10.652545454545454,
          "ma5": 10.256,
          "dif": -0.10914552999946281,
          "dea": -0.08310936671765085,
          "macd_hist": -0.026036163281811964,
          "red_band": 0,
          "blue_band": 1,
          "three_color_ball": 1,
          "absolute_buy_point": 0,
          "relative_buy_point": 0,
          "suicide_buy_
... (truncated)
```

---

### features.lifeline

**生死线指标(7个)**

- 耗时: 2.35s
- 响应大小: 5,485 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "features.lifeline",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "features.lifeline", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "features.lifeline",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "market": "sz",
    "frequency": "1d",
    "results": {
      "SZ.000001": {
        "status": "success",
        "data_count": 301,
        "lifeline": {
          "red_band": {
            "value": 0,
            "label": "红带",
            "description": "收盘价在5日线上方，且5日线在10日线上方"
          },
          "blue_band": {
            "value": 1,
            "label": "蓝带",
            "description": "收盘价在5日线下方，且5日线在10日线下方"
          },
          "three_color_ball": {
            "value": 1,
            "label": "三色球",
            "description": "红带+蓝带+红带或蓝带+红带+蓝带的三日组合"
          },
          "absolute_buy_point": {
            "value": 0,
            "label": "绝对买点",
            "description": "红带第一日，且前一日为蓝带"
          },
          "relative_buy_point": {
            "value": 0,
            "label": "相对买点",
            "description": "红带第二日或第三日"
          }
        },
        "signals": {
          "trend": "下降趋势（蓝带）"
        },
        "latest_features": {
          "date": "2026-07-06T00:00:00",
          "open": 10.25,
          "close": 10.5,
          "high": 10.52,
          "low": 10.22,
          "volume": 1061049.0,
          "pattern_hammer": 0,
          "pattern_hanging_man": 0,
          "pattern_morning_star": 0,
          "pattern_evening_star": 0,
          "pattern_bullish_engulf": 0,
          "pattern_bearish_engulf": 0,
          "pattern_bullish_harami": 0,
          "pattern_bearish_harami": 0,
          "pattern_piercing": 0,
          "pattern_dark_cloud": 0,
          "pattern_three_soldiers": 0,
          "pattern_three_crows": 0,
          "pattern_rising_three": 0,
          "pattern_falling_three": 0,
          "pattern_side_by_side_bull": 0,
          "pattern_side_by_side_bear": 0,
          "pattern_bull_count": 0,
          "pattern_bear_count": 0,
          "vol_sell_signal": 0,
          "vol_ratio_vs_peak": 0.0,
          "pattern_score": 0,
          "lifeline_score": 0,
          "total_score": 0,
          "fairy_guide_signal": 0,
          "fairy_guide_strict": 0,
          "fairy_guide_buy": 0,
          "low_cross_star": 0,
          "low_cross_star_buy": 1,
          "three_bear_hold_yang": 0,
          "three_bear_hold_yang_buy": 0,
          "limit_up_first_bear": 0,
          "limit_up_first_bear_buy": 0,
          "fisher_value": -0.12495256848836869,
          "fisher_buy": 0,
          "fisher_sell": 0,
          "calmar_ratio": -0.32859745665986334,
          "calmar_buy": 0,
          "calmar_strong_buy": 0,
          "calmar_sell": 1,
          "guppy_break_price": 10.74,
          "guppy_countdown_buy": 0,
          "alligator_sleep": 0,
          "alligator_bull": 0,
          "alligator_bear": 0,
          "keltner_bull": 0,
          "keltner_bear": 0,
          "hurst_oversold": 0,
          "hurst_buy": 0,
          "hurst_overbought": 0,
          "obv_bull_divergence": 0,
          "obv_divergence_buy": 0,
          "obv_bear_divergence": 0,
          "four_price_bull": 0,
          "four_price_bear": 0,
          "v2560_bull": 0,
          "v2560_pullback_buy": 0,
          "rsi_oversold_buy": 0,
          "rsi_overbought_sell": 0,
          "boll_squeeze": 0,
          "boll_squeeze_bull": 0,
          "boll_squeeze_bear": 0,
          "macd_moat_buy": 0,
          "macd_moat_rebound": 0,
          "combo_bull": 0,
          "combo_bear": 0,
          "limit_double_cannon": 0,
          "triple_gap_top": 0,
          "triple_gap_bottom": 0,
          "gap_fill_sell": 0,
          "gap_fill_buy": 0,
          "inverted_hammer": 0,
          "macd_buddha_hand": 0,
          "td9_buy": 0,
          "td9_sell": 0,
          "fib_reversal_buy": 0,
          "fib_reversal_sell": 0,
          "cup_handle_buy": 0,
          "triple_bottom_buy": 0,
          "island_reversal_buy": 0,
          "island_reversal_sell": 0,
          "harami_bear": 0,
          "harami_bull": 0,
          "single_gun": 0,
          "single_gun_buy": 0,
          "golden_ratio_buy": 0,
          "golden_ratio_zone": 0,
          "golden_ratio_resonance": 0,
          "golden_ratio_stop": 0,
          "golden_ratio_retracement": 0.5952380952380953,
          "golden_ratio_target": 12.02868,
          "golden_ratio_support_618": 10.47132,
          "golden_ratio_support_500": 10.620000000000001,
          "golden_ratio_support_809": 10.23066,
          "golden_ratio_resistance_high": 11.25,
          "golden_ratio_resistance_236": 10.95264,
          "golden_ratio_resistance_382": 10.76868,
          "golden_ratio_swing_low": 9.99,
          "golden_ratio_risk_reward": 5.68,
          "air_refuel_buy": 0,
          "platform_breakout": 0,
          "double_yang_bottom": 0,
          "old_duck_head": 0,
          "old_duck_head_buy": 0,
          "smart_fib_support": 10.47,
          "smart_fib_resistance": 10.62,
          "smart_fib_stop_loss": 10.29,
          "smart_fib_risk_reward": 0
... (truncated)
```

---

### features.experience

**经验特征**

- 耗时: 2.40s
- 响应大小: 5,325 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "features.experience",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "features.experience", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "features.experience",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "market": "sz",
    "frequency": "1d",
    "results": {
      "SZ.000001": {
        "status": "success",
        "data_count": 301,
        "experience_features": {
          "data_points": {
            "value": 301,
            "label": "数据点数",
            "description": "用于计算特征的历史数据条数"
          },
          "current_price": {
            "value": 0,
            "label": "最新价",
            "description": "当前最新成交价格"
          },
          "price_change": {
            "value": 0,
            "label": "价格变动",
            "description": "当日价格变动百分比(%)"
          },
          "volume_trend": {
            "value": "unknown",
            "label": "量能趋势",
            "description": "成交量趋势判断：increase=放量, decrease=缩量, stable=稳定"
          }
        },
        "latest_features": {
          "date": "2026-07-06T00:00:00",
          "open": 10.25,
          "close": 10.5,
          "high": 10.52,
          "low": 10.22,
          "volume": 1061049.0,
          "pattern_hammer": 0,
          "pattern_hanging_man": 0,
          "pattern_morning_star": 0,
          "pattern_evening_star": 0,
          "pattern_bullish_engulf": 0,
          "pattern_bearish_engulf": 0,
          "pattern_bullish_harami": 0,
          "pattern_bearish_harami": 0,
          "pattern_piercing": 0,
          "pattern_dark_cloud": 0,
          "pattern_three_soldiers": 0,
          "pattern_three_crows": 0,
          "pattern_rising_three": 0,
          "pattern_falling_three": 0,
          "pattern_side_by_side_bull": 0,
          "pattern_side_by_side_bear": 0,
          "pattern_bull_count": 0,
          "pattern_bear_count": 0,
          "vol_sell_signal": 0,
          "vol_ratio_vs_peak": 0.0,
          "pattern_score": 0,
          "lifeline_score": 0,
          "total_score": 0,
          "fairy_guide_signal": 0,
          "fairy_guide_strict": 0,
          "fairy_guide_buy": 0,
          "low_cross_star": 0,
          "low_cross_star_buy": 1,
          "three_bear_hold_yang": 0,
          "three_bear_hold_yang_buy": 0,
          "limit_up_first_bear": 0,
          "limit_up_first_bear_buy": 0,
          "fisher_value": -0.12495256848836869,
          "fisher_buy": 0,
          "fisher_sell": 0,
          "calmar_ratio": -0.32859745665986334,
          "calmar_buy": 0,
          "calmar_strong_buy": 0,
          "calmar_sell": 1,
          "guppy_break_price": 10.74,
          "guppy_countdown_buy": 0,
          "alligator_sleep": 0,
          "alligator_bull": 0,
          "alligator_bear": 0,
          "keltner_bull": 0,
          "keltner_bear": 0,
          "hurst_oversold": 0,
          "hurst_buy": 0,
          "hurst_overbought": 0,
          "obv_bull_divergence": 0,
          "obv_divergence_buy": 0,
          "obv_bear_divergence": 0,
          "four_price_bull": 0,
          "four_price_bear": 0,
          "v2560_bull": 0,
          "v2560_pullback_buy": 0,
          "rsi_oversold_buy": 0,
          "rsi_overbought_sell": 0,
          "boll_squeeze": 0,
          "boll_squeeze_bull": 0,
          "boll_squeeze_bear": 0,
          "macd_moat_buy": 0,
          "macd_moat_rebound": 0,
          "combo_bull": 0,
          "combo_bear": 0,
          "limit_double_cannon": 0,
          "triple_gap_top": 0,
          "triple_gap_bottom": 0,
          "gap_fill_sell": 0,
          "gap_fill_buy": 0,
          "inverted_hammer": 0,
          "macd_buddha_hand": 0,
          "td9_buy": 0,
          "td9_sell": 0,
          "fib_reversal_buy": 0,
          "fib_reversal_sell": 0,
          "cup_handle_buy": 0,
          "triple_bottom_buy": 0,
          "island_reversal_buy": 0,
          "island_reversal_sell": 0,
          "harami_bear": 0,
          "harami_bull": 0,
          "single_gun": 0,
          "single_gun_buy": 0,
          "golden_ratio_buy": 0,
          "golden_ratio_zone": 0,
          "golden_ratio_resonance": 0,
          "golden_ratio_stop": 0,
          "golden_ratio_retracement": 0.5952380952380953,
          "golden_ratio_target": 12.02868,
          "golden_ratio_support_618": 10.47132,
          "golden_ratio_support_500": 10.620000000000001,
          "golden_ratio_support_809": 10.23066,
          "golden_ratio_resistance_high": 11.25,
          "golden_ratio_resistance_236": 10.95264,
          "golden_ratio_resistance_382": 10.76868,
          "golden_ratio_swing_low": 9.99,
          "golden_ratio_risk_reward": 5.68,
          "air_refuel_buy": 0,
          "platform_breakout": 0,
          "double_yang_bottom": 0,
          "old_duck_head": 0,
          "old_duck_head_buy": 0,
          "smart_fib_support": 10.47,
          "smart_fib_resistance": 10.62,
          "smart_fib_stop_loss": 10.29,
          "smart_fib_risk_reward": 0.6,
          "smart_fib_swing_high": 11.25,
          "smart_fib_swing_low": 9.99,
          "smart_fib_position_in_range": 0.4,
          "smart_fib_risk": 0.21,
          "smart
... (truncated)
```

---

### features.combined

**合并特征**

- 耗时: 2.36s
- 响应大小: 5,470 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "features.combined",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "features.combined", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "features.combined",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "market": "sz",
    "frequency": "1d",
    "results": {
      "SZ.000001": {
        "status": "success",
        "data_count": 301,
        "k_patterns": {},
        "lifeline": {
          "red_band": {
            "value": 0,
            "label": "红带",
            "description": "收盘价在5日线上方，且5日线在10日线上方"
          },
          "blue_band": {
            "value": 1,
            "label": "蓝带",
            "description": "收盘价在5日线下方，且5日线在10日线下方"
          },
          "three_color_ball": {
            "value": 1,
            "label": "三色球",
            "description": "红带+蓝带+红带或蓝带+红带+蓝带的三日组合"
          },
          "absolute_buy_point": {
            "value": 0,
            "label": "绝对买点",
            "description": "红带第一日，且前一日为蓝带"
          },
          "relative_buy_point": {
            "value": 0,
            "label": "相对买点",
            "description": "红带第二日或第三日"
          }
        },
        "experience": {},
        "latest_features": {
          "date": "2026-07-06T00:00:00",
          "open": 10.25,
          "close": 10.5,
          "high": 10.52,
          "low": 10.22,
          "volume": 1061049.0,
          "pattern_hammer": 0,
          "pattern_hanging_man": 0,
          "pattern_morning_star": 0,
          "pattern_evening_star": 0,
          "pattern_bullish_engulf": 0,
          "pattern_bearish_engulf": 0,
          "pattern_bullish_harami": 0,
          "pattern_bearish_harami": 0,
          "pattern_piercing": 0,
          "pattern_dark_cloud": 0,
          "pattern_three_soldiers": 0,
          "pattern_three_crows": 0,
          "pattern_rising_three": 0,
          "pattern_falling_three": 0,
          "pattern_side_by_side_bull": 0,
          "pattern_side_by_side_bear": 0,
          "pattern_bull_count": 0,
          "pattern_bear_count": 0,
          "vol_sell_signal": 0,
          "vol_ratio_vs_peak": 0.0,
          "pattern_score": 0,
          "lifeline_score": 0,
          "total_score": 0,
          "fairy_guide_signal": 0,
          "fairy_guide_strict": 0,
          "fairy_guide_buy": 0,
          "low_cross_star": 0,
          "low_cross_star_buy": 1,
          "three_bear_hold_yang": 0,
          "three_bear_hold_yang_buy": 0,
          "limit_up_first_bear": 0,
          "limit_up_first_bear_buy": 0,
          "fisher_value": -0.12495256848836869,
          "fisher_buy": 0,
          "fisher_sell": 0,
          "calmar_ratio": -0.32859745665986334,
          "calmar_buy": 0,
          "calmar_strong_buy": 0,
          "calmar_sell": 1,
          "guppy_break_price": 10.74,
          "guppy_countdown_buy": 0,
          "alligator_sleep": 0,
          "alligator_bull": 0,
          "alligator_bear": 0,
          "keltner_bull": 0,
          "keltner_bear": 0,
          "hurst_oversold": 0,
          "hurst_buy": 0,
          "hurst_overbought": 0,
          "obv_bull_divergence": 0,
          "obv_divergence_buy": 0,
          "obv_bear_divergence": 0,
          "four_price_bull": 0,
          "four_price_bear": 0,
          "v2560_bull": 0,
          "v2560_pullback_buy": 0,
          "rsi_oversold_buy": 0,
          "rsi_overbought_sell": 0,
          "boll_squeeze": 0,
          "boll_squeeze_bull": 0,
          "boll_squeeze_bear": 0,
          "macd_moat_buy": 0,
          "macd_moat_rebound": 0,
          "combo_bull": 0,
          "combo_bear": 0,
          "limit_double_cannon": 0,
          "triple_gap_top": 0,
          "triple_gap_bottom": 0,
          "gap_fill_sell": 0,
          "gap_fill_buy": 0,
          "inverted_hammer": 0,
          "macd_buddha_hand": 0,
          "td9_buy": 0,
          "td9_sell": 0,
          "fib_reversal_buy": 0,
          "fib_reversal_sell": 0,
          "cup_handle_buy": 0,
          "triple_bottom_buy": 0,
          "island_reversal_buy": 0,
          "island_reversal_sell": 0,
          "harami_bear": 0,
          "harami_bull": 0,
          "single_gun": 0,
          "single_gun_buy": 0,
          "golden_ratio_buy": 0,
          "golden_ratio_zone": 0,
          "golden_ratio_resonance": 0,
          "golden_ratio_stop": 0,
          "golden_ratio_retracement": 0.5952380952380953,
          "golden_ratio_target": 12.02868,
          "golden_ratio_support_618": 10.47132,
          "golden_ratio_support_500": 10.620000000000001,
          "golden_ratio_support_809": 10.23066,
          "golden_ratio_resistance_high": 11.25,
          "golden_ratio_resistance_236": 10.95264,
          "golden_ratio_resistance_382": 10.76868,
          "golden_ratio_swing_low": 9.99,
          "golden_ratio_risk_reward": 5.68,
          "air_refuel_buy": 0,
          "platform_breakout": 0,
          "double_yang_bottom": 0,
          "old_duck_head": 0,
          "old_duck_head_buy": 0,
          "smart_fib_support": 10.47,
          "smart_fib_resistance": 10.62,
          "smart_fib_stop_loss": 10.29,
          "smart_fib_risk_reward": 0.6,
      
... (truncated)
```

---

### features.technical

**技术特征集(212个)**

- 耗时: 1.83s
- 响应大小: 43,687 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "features.technical",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "features.technical", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=120)
print(resp.json())
```

请求体：

```json
{
  "api": "features.technical",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "market": "sz",
    "results": {
      "SZ.000001": {
        "status": "success",
        "data_count": 200,
        "feature_count": 197,
        "session_features": {
          "boll_width_60m_astart_cls": {
            "value": 3.0,
            "label": "布林带宽度(午盘)分类",
            "description": "午盘布林带宽度相对中轨的分类值"
          },
          "atr_14_60m_astart_cls": {
            "value": 2.0,
            "label": "ATR(午盘)分类",
            "description": "午盘14周期ATR相对收盘价百分比的分类值"
          },
          "kdj_d_60m_astart_cls": {
            "value": 5.0,
            "label": "KDJ-D(午盘)分类",
            "description": "午盘KDJ-D值分类"
          },
          "kdj_k_60m_mstart_cls": {
            "value": 4.0,
            "label": "KDJ-K(早盘)分类",
            "description": "早盘KDJ-K值分类"
          },
          "ma5_ma20_margin_ratio_60m_last": {
            "value": 0.017296980340763195,
            "label": "MA5/MA20间距比(60m)",
            "description": "60m MA5与MA20间距比率"
          },
          "roc_8_60m_last": {
            "value": 2.1400778210116793,
            "label": "ROC-8(60m尾盘)",
            "description": "60m 8周期变动率"
          },
          "rsi_14_60m_last": {
            "value": 77.41933735692376,
            "label": "RSI-14(60m尾盘)",
            "description": "60m 14周期RSI"
          },
          "boll_position_60m_mstart_cls": {
            "value": 7.0,
            "label": "BOLL位置(早盘)分类",
            "description": "早盘布林带位置分类"
          },
          "roc_6_60m_mstart": {
            "value": 1.1673151750972859,
            "label": "ROC-6(60m早盘)",
            "description": "60m 6周期变动率"
          },
          "momentum_8_60m_last": {
            "value": 0.22000000000000064,
            "label": "动量-8(60m尾盘)",
            "description": "60m 8周期动量"
          },
          "macd_hist_60m_last": {
            "value": 0.0484428278671241,
            "label": "MACD柱(60m尾盘)",
            "description": "60m MACD柱状图"
          },
          "momentum_6_60m_mstart": {
            "value": 0.120000000000001,
            "label": "动量-6(60m早盘)",
            "description": "60m 6周期动量"
          },
          "pdi_60m_astart_cls": {
            "value": 4.0,
            "label": "PDI(午盘)分类",
            "description": "午盘+DI分类"
          },
          "rsi_7_60m_mstart": {
            "value": 67.64704489619679,
            "label": "RSI-7(60m早盘)",
            "description": "60m 7周期RSI"
          },
          "mdi_60m_astart_cls": {
            "value": 2.0,
            "label": "MDI(午盘)分类",
            "description": "午盘-DI分类"
          },
          "rsi_21_60m_last": {
            "value": 64.13042014414322,
            "label": "RSI-21(60m尾盘)",
            "description": "60m 21周期RSI"
          },
          "roc_4_60m_last": {
            "value": 2.0408163265306207,
            "label": "ROC-4(60m尾盘)",
            "description": "60m 4周期变动率"
          },
          "cci_60m_mstart_cls": {
            "value": 5.0,
            "label": "CCI(早盘)分类",
            "description": "早盘CCI分类"
          },
          "kdj_j_60m_mstart": {
            "value": 90.70337300174816,
            "label": "KDJ-J(60m早盘)",
            "description": "60m KDJ-J值"
          },
          "macd_60m_last": {
            "value": 0.01362234275055485,
            "label": "MACD(60m尾盘)",
            "description": "60m MACD DIF值"
          },
          "momentum_4_60m_last": {
            "value": 0.21000000000000085,
            "label": "动量-4(60m尾盘)",
            "description": "60m 4周期动量"
          },
          "roc_2_60m_mstart": {
            "value": 1.0689990281827135,
            "label": "ROC-2(60m早盘)",
            "description": "60m 2周期变动率"
          },
          "momentum_2_60m_mstart": {
            "value": 0.11000000000000121,
            "label": "动量-2(60m早盘)",
            "description": "60m 2周期动量"
          },
          "kdj_oversold_60m_astart": {
            "value": 0,
            "label": "KDJ超卖(60m午盘)",
            "description": "午盘KDJ超卖信号"
          },
          "kdj_cci_overbought_60m_mstart": {
            "value": 0,
            "label": "KDJ+CCI超买(60m早盘)",
            "description": "早盘KDJ与CCI同时超买"
          },
          "vol_ma5_60m_last": {
            "value": 24208561.0,
            "label": "成交量MA5(60m尾盘)",
            "description": "60m 5周期成交量均线"
          },
          "adx_60m_astart_cls": {
            "value": 5.0,
            "label": "ADX(午盘)分类",
            "description": "午盘ADX分类"
          },
          "kdj_cci_oversold_60m_mstart": {
            "value": 0,
            "label": "KDJ+CCI超卖(60m早盘)",
            "description": "早盘KDJ与CCI同时超卖"
          },
          "macd_signal_60m_last": {
            "value": -0.03482048511656925,
            "label": "MACD信号线(60m尾盘)",
            "description": "60m MACD信号线"
          },
          "cci_oversold_60m_last": {
            "value": 0,
            "label": "
... (truncated)
```

---

### features.all

**全量特征**

- 耗时: 7.27s
- 响应大小: 33,444 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "features.all",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "features.all", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=120)
print(resp.json())
```

请求体：

```json
{
  "api": "features.all",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "stock_codes": [
      "SZ.000001"
    ],
    "market": "sz",
    "results": {
      "SZ.000001": {
        "status": "success",
        "technical": {
          "trade_date": {
            "value": "2026-07-06",
            "label": "trade_date",
            "description": ""
          },
          "boll_width_60m_astart_cls": {
            "value": 3.0,
            "label": "布林带宽度(午盘)分类",
            "description": "午盘布林带宽度相对中轨的分类值"
          },
          "atr_14_60m_astart_cls": {
            "value": 2.0,
            "label": "ATR(午盘)分类",
            "description": "午盘14周期ATR相对收盘价百分比的分类值"
          },
          "kdj_d_60m_astart_cls": {
            "value": 5.0,
            "label": "KDJ-D(午盘)分类",
            "description": "午盘KDJ-D值分类"
          },
          "kdj_k_60m_mstart_cls": {
            "value": 4.0,
            "label": "KDJ-K(早盘)分类",
            "description": "早盘KDJ-K值分类"
          },
          "ma5_ma20_margin_ratio_60m_last": {
            "value": 0.017296980340763195,
            "label": "MA5/MA20间距比(60m)",
            "description": "60m MA5与MA20间距比率"
          },
          "roc_8_60m_last": {
            "value": 2.1400778210116793,
            "label": "ROC-8(60m尾盘)",
            "description": "60m 8周期变动率"
          },
          "rsi_14_60m_last": {
            "value": 77.41933735692376,
            "label": "RSI-14(60m尾盘)",
            "description": "60m 14周期RSI"
          },
          "boll_position_60m_mstart_cls": {
            "value": 7.0,
            "label": "BOLL位置(早盘)分类",
            "description": "早盘布林带位置分类"
          },
          "roc_6_60m_mstart": {
            "value": 1.1673151750972859,
            "label": "ROC-6(60m早盘)",
            "description": "60m 6周期变动率"
          },
          "momentum_8_60m_last": {
            "value": 0.22000000000000064,
            "label": "动量-8(60m尾盘)",
            "description": "60m 8周期动量"
          },
          "macd_hist_60m_last": {
            "value": 0.0484428278671241,
            "label": "MACD柱(60m尾盘)",
            "description": "60m MACD柱状图"
          },
          "momentum_6_60m_mstart": {
            "value": 0.120000000000001,
            "label": "动量-6(60m早盘)",
            "description": "60m 6周期动量"
          },
          "pdi_60m_astart_cls": {
            "value": 4.0,
            "label": "PDI(午盘)分类",
            "description": "午盘+DI分类"
          },
          "rsi_7_60m_mstart": {
            "value": 67.64704489619679,
            "label": "RSI-7(60m早盘)",
            "description": "60m 7周期RSI"
          },
          "mdi_60m_astart_cls": {
            "value": 2.0,
            "label": "MDI(午盘)分类",
            "description": "午盘-DI分类"
          },
          "rsi_21_60m_last": {
            "value": 64.13042014414322,
            "label": "RSI-21(60m尾盘)",
            "description": "60m 21周期RSI"
          },
          "roc_4_60m_last": {
            "value": 2.0408163265306207,
            "label": "ROC-4(60m尾盘)",
            "description": "60m 4周期变动率"
          },
          "cci_60m_mstart_cls": {
            "value": 5.0,
            "label": "CCI(早盘)分类",
            "description": "早盘CCI分类"
          },
          "kdj_j_60m_mstart": {
            "value": 90.70337300174816,
            "label": "KDJ-J(60m早盘)",
            "description": "60m KDJ-J值"
          },
          "macd_60m_last": {
            "value": 0.01362234275055485,
            "label": "MACD(60m尾盘)",
            "description": "60m MACD DIF值"
          },
          "momentum_4_60m_last": {
            "value": 0.21000000000000085,
            "label": "动量-4(60m尾盘)",
            "description": "60m 4周期动量"
          },
          "roc_2_60m_mstart": {
            "value": 1.0689990281827135,
            "label": "ROC-2(60m早盘)",
            "description": "60m 2周期变动率"
          },
          "momentum_2_60m_mstart": {
            "value": 0.11000000000000121,
            "label": "动量-2(60m早盘)",
            "description": "60m 2周期动量"
          },
          "kdj_oversold_60m_astart": {
            "value": 0,
            "label": "KDJ超卖(60m午盘)",
            "description": "午盘KDJ超卖信号"
          },
          "kdj_cci_overbought_60m_mstart": {
            "value": 0,
            "label": "KDJ+CCI超买(60m早盘)",
            "description": "早盘KDJ与CCI同时超买"
          },
          "vol_ma5_60m_last": {
            "value": 24208561.0,
            "label": "成交量MA5(60m尾盘)",
            "description": "60m 5周期成交量均线"
          },
          "adx_60m_astart_cls": {
            "value": 5.0,
            "label": "ADX(午盘)分类",
            "description": "午盘ADX分类"
          },
          "kdj_cci_oversold_60m_mstart": {
            "value": 0,
            "label": "KDJ+CCI超卖(60m早盘)",
            "description": "早盘KDJ与CCI同时超卖"
          },
          "macd_signal_60m_last": {
            "value": -0.03482048511656925,
            "label": "MACD信号线(60m尾盘)",
            "description": "60m MACD信号线"
          },
        
... (truncated)
```

---

## AI 原生分析

### analyze.tick_intraday

**逐笔分时特征**

- 耗时: 2.03s
- 响应大小: 2,378 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "analyze.tick_intraday",
  "stock_codes": [
    "SZ.000001"
  ]
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "analyze.tick_intraday", "stock_codes": ["SZ.000001"]}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "analyze.tick_intraday",
  "stock_codes": [
    "SZ.000001"
  ]
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "summary": {
      "total": 1,
      "success": 1,
      "failed": 0,
      "timestamp": "2026-07-06T21:36:29.176525",
      "trend_labels": {
        "0": "中性",
        "1": "弱转强",
        "2": "强转弱",
        "3": "强势上涨",
        "4": "弱势下跌",
        "5": "企稳回升",
        "6": "上涨乏力"
      },
      "feature_count": 14
    },
    "results": {
      "SZ.000001": {
        "status": "success",
        "features": {
          "tick_price_vs_avg_pct": {
            "value": 0.91,
            "name": "价格vs分时均线%",
            "description": "最新成交价相对分时均线(VWAP)偏离百分比"
          },
          "tick_avg_slope": {
            "value": 0.02,
            "name": "分时均线斜率",
            "description": "分时均线从开盘到当前的变化率，正值表示均线上升"
          },
          "tick_avg_current": {
            "value": 10.405,
            "name": "分时均线当前值",
            "description": "当前分时均线(VWAP)价格"
          },
          "tick_last_cross_type": {
            "value": "V",
            "name": "最近穿越类型",
            "description": "V=价格从下穿上均线(弱转强)，INV=从上穿下(强转弱)"
          },
          "tick_cross_count": {
            "value": 33,
            "name": "穿越次数",
            "description": "当天价格穿越分时均线的总次数"
          },
          "tick_minutes_since_cross": {
            "value": 304,
            "name": "距穿越分钟数",
            "description": "距最近一次价格穿越均线的分钟数"
          },
          "tick_consecutive_side": {
            "value": 3953,
            "name": "连续站稳笔数",
            "description": "价格连续在均线同一侧的成交笔数"
          },
          "tick_distance_from_avg": {
            "value": 0.095,
            "name": "距均线绝对距离",
            "description": "最新价与分时均线的绝对差值"
          },
          "tick_distance_trend": {
            "value": "stable",
            "name": "距离变化趋势",
            "description": "expanding=远离均线，shrinking=靠近均线，stable=稳定"
          },
          "tick_outer_inner_ratio": {
            "value": 1.58,
            "name": "外内盘比",
            "description": "主动买入量/主动卖出量，>1表示买方活跃"
          },
          "tick_big_trade_pct": {
            "value": 52.25,
            "name": "大单占比%",
            "description": ">=1000手大单成交量占总成交量的百分比"
          },
          "tick_volume_acceleration": {
            "value": -54.14,
            "name": "量能加速度",
            "description": "最近5分钟成交量/之前5分钟成交量的比值"
          },
          "tick_strength_score": {
            "value": 55,
            "name": "综合强度评分",
            "description": "-100到+100，正值表示多头强势，负值表示空头强势"
          },
          "tick_trend_status": {
            "value": 1,
            "name": "趋势状态码",
            "description": "0=中性,1=弱转强,2=强转弱,3=强势上涨,4=弱势下跌,5=企稳回升,6=上涨乏力"
          }
        },
        "trend_label": "弱转强",
        "data_complete": false,
        "tick_count": 4466
      }
    }
  },
  "usage": {
    "cost": 1.0,
    "balance": 13494.1,
    "balance_after": 13493.1
  }
}
```

---

### analyze.hot_sector

**热门板块联动跟风**

- 耗时: 0.34s
- 响应大小: 66,360 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "analyze.hot_sector"
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "analyze.hot_sector"}

resp = requests.post(url, headers=headers, json=payload, timeout=120)
print(resp.json())
```

请求体：

```json
{
  "api": "analyze.hot_sector"
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "strategy": "hot_sector",
    "status": "cache_hit_off_hours",
    "disclaimer": "免责声明：热门板块分析结果基于历史联动数据和实时技术面筛选，仅供参考，不构成投资建议。股市有风险，投资需谨慎。",
    "reasoning": "/hot-sector API调用",
    "session_id": "",
    "result": {
      "status": "cache_hit_off_hours",
      "message": "当前为非交易时段，返回最近缓存报告",
      "top3_selected": [
        {
          "code": "SZ.300308",
          "name": "中际旭创",
          "overall_score": 51.6,
          "relevance_score": 92,
          "combined_score": 75.84,
          "pct": -1.53,
          "reason": "全球光模块龙头，直接受益AI算力基建与5G-A商用试点，虽短期回调但资金面持续净流入",
          "timestamp": "2026-07-06T20:09:02.470429",
          "diagnosis": {
            "overall_score": 47.4,
            "verdict": "建议观望",
            "confidence": "弱",
            "sentiment": "-2",
            "scores": {
              "sentiment": 42.5,
              "tick": 36.0,
              "flow": 50.0,
              "tech": 70.0,
              "kline": 50.0
            }
          }
        },
        {
          "code": "SZ.002705",
          "name": "新宝股份",
          "overall_score": 52.6,
          "relevance_score": 85,
          "combined_score": 72.04,
          "pct": 0.34,
          "reason": "AI终端+家电出海双主线，受益AI五年计划对智能硬件政策支持，研报维持买入评级",
          "timestamp": "2026-07-06T20:09:02.470410",
          "diagnosis": {
            "overall_score": 49.4,
            "verdict": "建议观望",
            "confidence": "中等",
            "sentiment": "-3",
            "scores": {
              "sentiment": 33.5,
              "tick": 56.5,
              "flow": 50.0,
              "tech": 52.0,
              "kline": 50.0
            }
          }
        },
        {
          "code": "SH.600283",
          "name": "钱江水利",
          "overall_score": 52.6,
          "relevance_score": 78,
          "combined_score": 67.84,
          "pct": 3.19,
          "reason": "唯一涨幅超3%的候选股，业绩高增（Q3营收+15.88%），契合国家设备更新+水利基建政策催化",
          "timestamp": "2026-07-06T20:09:02.470426",
          "diagnosis": {
            "overall_score": 56.1,
            "verdict": "建议观望",
            "confidence": "中等",
            "sentiment": "-3",
            "scores": {
              "sentiment": 33.5,
              "tick": 74.5,
              "flow": 50.0,
              "tech": 74.5,
              "kline": 50.0
            }
          }
        }
      ],
      "top10_scored": [
        {
          "code": "SH.600894",
          "name": "广日股份",
          "overall_score": 52.7,
          "diagnosis_score": 52.7,
          "base_score": 0,
          "pct": 0.76,
          "timestamp": "2026-07-06T16:36:32.311275"
        },
        {
          "code": "SZ.002705",
          "name": "新宝股份",
          "overall_score": 52.6,
          "diagnosis_score": 52.6,
          "base_score": 0,
          "pct": 0.34,
          "timestamp": "2026-07-06T16:35:20.822634"
        },
        {
          "code": "SH.600283",
          "name": "钱江水利",
          "overall_score": 52.6,
          "diagnosis_score": 52.6,
          "base_score": 0,
          "pct": 3.19,
          "timestamp": "2026-07-06T16:36:54.195675"
        },
        {
          "code": "SZ.000554",
          "name": "泰山石油",
          "overall_score": 52.6,
          "diagnosis_score": 52.6,
          "base_score": 0,
          "pct": -0.51,
          "timestamp": "2026-07-06T16:37:03.441375"
        },
        {
          "code": "SH.601965",
          "name": "中国汽研",
          "overall_score": 52.6,
          "diagnosis_score": 52.6,
          "base_score": 0,
          "pct": 0.0,
          "timestamp": "2026-07-06T16:37:07.650575"
        },
        {
          "code": "SZ.300198",
          "name": "*ST纳川",
          "overall_score": 52.6,
          "diagnosis_score": 52.6,
          "base_score": 0,
          "pct": 1.34,
          "timestamp": "2026-07-06T16:37:23.843248"
        },
        {
          "code": "SH.600295",
          "name": "鄂尔多斯",
          "overall_score": 52.6,
          "diagnosis_score": 52.6,
          "base_score": 0,
          "pct": 1.46,
          "timestamp": "2026-07-06T16:37:43.795589"
        },
        {
          "code": "SZ.300308",
          "name": "中际旭创",
          "overall_score": 51.6,
          "diagnosis_score": 51.6,
          "base_score": 0,
          "pct": -1.53,
          "timestamp": "2026-07-06T16:29:56.770352"
        },
        {
          "code": "SZ.000725",
          "name": "京东方Ａ",
          "overall_score": 51.6,
          "diagnosis_score": 51.6,
          "base_score": 0,
          "pct": -7.4,
          "timestamp": "2026-07-06T16:30:02.841292"
        },
        {
          "code": "SZ.002050",
          "name": "三花智控",
          "overall_score": 51.6,
          "diagnosis_score": 51.6,
          "base_score": 0,
          "pct": -5.8,
          "timestamp": "2026-07-06T16:33:12.005819"
        }
      ],
      "stock_list": [
        {
          "code": "SH.600894",
          "name": "广日股份",
       
... (truncated)
```

---

### analyze.market_review

**大盘复盘报告**

- 耗时: 0.33s
- 响应大小: 8,182 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "analyze.market_review",
  "params": {
    "skip_llm": true,
    "use_cache": true
  }
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "analyze.market_review", "params": {"skip_llm": True, "use_cache": True}}

resp = requests.post(url, headers=headers, json=payload, timeout=120)
print(resp.json())
```

请求体：

```json
{
  "api": "analyze.market_review",
  "params": {
    "skip_llm": true,
    "use_cache": true
  }
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "status": "success",
    "strategy": "market_review",
    "period": "full",
    "summary": "📅 报告生成时间：2026-07-06 09:36:52\n\n【2026/07/06 早盘复盘报告】\n\n1. 指数概览  \n今天早盘，A股三大指数整体呈现“红盘报收、但涨得不猛”的温和态势。上证指数站稳4049点，小幅上涨约0.15%；深证成指微涨0.05%，创业板指则略显疲弱，下跌0.44%，是唯一收绿的主要指数。值得注意的是，所有主要宽基指数——包括沪深300、中证500和科创50——全部缩量运行，说明市场交投偏谨慎，资金没有大规模进场扫货，更多是存量资金在局部板块间调仓换仓。其中，科创50逆势大涨0.76%，成为全场最亮眼的指数，但其5日累计跌幅仍高达6.37%，说明短期虽有技术性反抽，尚未扭转中期弱势趋势。\n\n2. 市场情绪  \n情绪面整体偏暖，但热度有限。全市场共30家个股涨停，仅2家跌停，涨跌家数比为3183:1793，超六成个股上涨（上涨占比64.0%），说明多数股票都在跟随指数小幅修复。不过，涨停数量处于近期中等偏低水平（近10个交易日平均约42家），且无跌停潮、无大面积恐慌抛压，属于典型的“小阳线+低波动”格局——既没有明显亢奋，也没有明显退缩，更像是在等待方向确认前的蓄势整理。\n\n3. 板块行情  \n今日板块轮动特征鲜明：资金明显从前期过热或调整中的科技细分领域撤出，转向具备政策预期支撑、估值相对合理、且近期有事件催化的方向。涨幅居前的五大板块依次为：工业机器人（+3.1%）、军工电子（+2.3%）、工程机械（+2.1%）、汽车零部件（+2.0%）、创新药（+1.9%）。这些板块有一个共同点：多与“高端制造升级”“设备更新”“国产替代深化”等政策主线挂钩，且部分行业已出现订单回暖或招标提速的早期信号。  \n相反，跌幅靠前的板块集中在上游原材料和部分半导体配套环节：基础化工（-1.4%）、半导体材料（-1.2%）、钢铁（-1.2%）、半导体封测（-1.1%）、被动元件（-0.8%）。这些板块过去一两个月涨幅较大，当前进入阶段性休整，叠加部分品种库存压力显现、价格承压，资金选择获利了结。历史数据显示，这类“高位滞涨+量能萎缩”的板块，往往需要更长时间消化浮筹，短期难有强势反包。\n\n4. 龙头股分析  \n今日连板梯队较短，仅有3只连板股，反映出市场短线接力意愿尚处试探阶段。其中，恒尚节能（SH.603137）以4连板领跑，所属板块为环保工程；另两只为*ST艾艾（SH.603580）和*ST金科（SZ.000656），均为2连板，分别归属专用设备和房地产板块。值得注意的是，三只连板股分属三个不同行业，且除恒尚节能外，其余两家均为ST类个股——这说明当前市场缺乏清晰统一的主线共识，资金更多是在“分散试错”，而非集中抱团某一主线。恒尚节能作为唯一4连板标的，已初步确立为新周期潜在风向标，其持续走强将对环保工程及泛基建相关题材形成带动效应；但目前尚未看到同板块其他个股形成有效梯队呼应，暂未构成真正意义上的“板块性龙头”。\n\n5.5 涨停个股分析  \n虽然未提供具体涨停个股精选评分数据，但结合板块涨幅与连板结构可推断：今日涨停核心催化方向集中在“设备更新+自主可控”双主线。工业机器人、军工电子、工程机械等领涨板块均受益于近期密集出台的“大规模设备更新”政策落地节奏加快，以及部分军工订单加速释放的消息；而创新药的异动，则与医保谈判节奏预期提前、部分重磅品种临床数据披露等消息有关。由于原始数据中未给出涨停个股评分及关联逻辑明细，本报告不作主观推测或补充个股名称及代码，严格遵循“无数据不列示”原则。\n\n6. 阶段判断  \n当前市场处于“启动初期向发酵过渡”的关键节点。理由有三：第一，指数层面虽未放量突破，但中证500、科创50等中小盘指数率先企稳反弹，显示资金开始关注估值更具弹性的方向；第二，板块轮动有序，领涨板块具备政策背书与基本面改善迹象，非纯情绪炒作；第三，连板梯队虽短，但出现4连板新面孔，且未见老龙头大幅杀跌（如前期高标股未批量跌停），说明亏钱效应收敛，短线生态正在修复。但需注意，成交量持续萎缩仍是制约进一步走强的关键瓶颈，若后续不能有效补量，可能延长震荡筑底时间。\n\n7. 风险提示  \n需警惕三类风险：一是外部宏观环境不确定性仍存，美联储政策路径、地缘局势变化可能扰动风险偏好；二是部分前期热门赛道（如AI应用、算力硬件）调整尚未充分，存在反复震荡可能；三是ST类个股连续涨停背后隐含较高合规与退市风险，普通投资者不宜盲目跟风。此外，当前市场缩量运行，一旦突发利空或外围市场大幅波动，容易引发快速踩踏，需保持仓位灵活性与纪律性。\n\n> 免责申明：本报告由 AStockD 市场数据生成，不构成任何投资建议。股市有风险，投资需谨慎!",
    "data_summary": {
      "indices": [
        {
          "name": "上证指数",
          "close": 4049.55,
          "pct": 0.15,
          "pct_5d": -0.6,
          "volume": 59283440.0,
          "vol_ratio": 0.1,
          "open": 4059.19,
          "high": 4060.07,
          "low": 4046.83
        },
        {
          "name": "深证成指",
          "close": 15605.96,
          "pct": 0.05,
          "pct_5d": -1.31,
          "volume": 96989313.0,
          "vol_ratio": 0.12,
          "open": 15677.93,
          "high": 15690.18,
          "low": 15597.7
        },
        {
          "name": "创业板指",
          "close": 4002.42,
          "pct": -0.44,
          "pct_5d": -5.08,
          "volume": 24400530.0,
          "vol_ratio": 0.11,
          "open": 4050.88,
          "high": 4050.91,
          "low": 4000.96
        },
        {
          "name": "沪深300",
          "close": 4849.15,
          "pct": 0.14,
          "pct_5d": -1.58,
          "volume": 29590786.0,
          "vol_ratio": 0.1,
          "open": 4868.54,
          "high": 4870.91,
          "low": 4848.74
        },
        {
          "name": "中证500",
          "close": 8767.8,
          "pct": 0.26,
          "pct_5d": -0.6,
          "volume": 24789536.0,
          "vol_ratio": 0.11,
          "open": 8790.97,
          "high": 8795.89,
          "low": 8750.87
        },
        {
          "name": "科创50",
          "close": 1990.56,
          "pct": 0.76,
          "pct_5d": -6.37,
          "volume": 1392161.0,
          "vol_ratio": 0.08,
          "open": 2015.18,
          "high": 2015.18,
          "low": 1989.74
        }
      ],
      "limitup": {
        "count": 30,
        "codes": [
          "SH.603033",
          "SH.603067",
          "SH.603137",
          "SH.603203",
          "SH.603466",
          "SH.603580",
          "SH.603722",
          "SH.603789",
          "SH.603828",
          "SH.603928",
          "SH.688039",
          "SH.688071",
          "SH.688206",
          "SH.688238",
          "SH.688820",
          "SZ.000656",
          "SZ.001270",
          "SZ.001287",
          "SZ.002042",
          "SZ.002141"
        ]
      },
      "limitup_detail": {
        "dragon_stocks": [
          {
            "code": "SH.603137",
            "name": "恒尚节能",
            "consecutive_boards": 4,
            "sector": "环保工程",
            "industry": "环保工程",
            "category": "4连板"
          },
          {
            "code": "SH.603580",
            "name": "*ST艾艾",
            "consecutive_boards": 2,
            "sector": "专用设备",
            "industry": "专用设备",
            "category": "2连板"
          },
          {
            "code": "SZ.000656",
            "name": "*ST金科",
            "consecutive_boards": 2,
            "sector": "房地产",
            "industry": "房地产",
            "category": "2连板"
          }
    
... (truncated)
```

---

### analyze.portfolio

**组合持仓分析**

- 耗时: 47.65s
- 响应大小: 31,671 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "analyze.portfolio",
  "params": {
    "holdings": [
      {
        "code": "SZ.000001",
        "cost_price": 12.5,
        "quantity": 1000
      },
      {
        "code": "SH.600036",
        "cost_price": 35.0,
        "quantity": 500
      },
      {
        "code": "SZ.000858",
        "cost_price": 150.0,
        "quantity": 200
      }
    ]
  }
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {
    "api": "analyze.portfolio",
    "params": {
        "holdings": [
            {"code": "SZ.000001", "cost_price": 12.5, "quantity": 1000},
            {"code": "SH.600036", "cost_price": 35.0, "quantity": 500},
            {"code": "SZ.000858", "cost_price": 150.0, "quantity": 200},
        ]
    },
}

resp = requests.post(url, headers=headers, json=payload, timeout=180)
print(resp.json())
```

请求体：

```json
{
  "api": "analyze.portfolio",
  "params": {
    "holdings": [
      {
        "code": "SZ.000001",
        "cost_price": 12.5,
        "quantity": 1000
      },
      {
        "code": "SH.600036",
        "cost_price": 35.0,
        "quantity": 500
      },
      {
        "code": "SZ.000858",
        "cost_price": 150.0,
        "quantity": 200
      }
    ]
  }
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "disclaimer": "免责声明：本组合分析报告基于股票当前数据和历史数据统计分析，所有提及的概率均基于历史回测数据，不构成任何投资建议。股市有风险，投资需谨慎。本报告仅供参考，不构成买卖依据，投资者应独立判断并承担投资风险。",
    "status": "success",
    "strategy": "portfolio",
    "portfolio_summary": {
      "total_market_value": 44117.0,
      "total_cost": 60000.0,
      "total_pnl": -15883.0,
      "total_pnl_pct": -26.47,
      "holding_count": 3,
      "max_weight_pct": 42.76,
      "concentration_risk": "高",
      "details": [
        {
          "code": "SZ.000001",
          "cost_price": 12.5,
          "current_price": 10.5,
          "quantity": 1000,
          "market_value": 10500.0,
          "pnl": -2000.0,
          "pnl_pct": -16.0,
          "weight_pct": 23.8,
          "fibonacci": {
            "当前价格": "10.50",
            "波段区间": "9.99~11.25",
            "计算类型": "上涨扩展",
            "位置": "40.0%(中位)",
            "智能支撑": "10.47",
            "智能阻力": "10.62",
            "智能止损": "10.29",
            "智能风险收益比": "0.60:1 (较差(<1:1))",
            "波段高点(0%回撤)": "11.25 (距当前+7.1%)",
            "0.382阻力位": "10.77 (距当前+2.6%)",
            "0.236阻力位": "10.95 (距当前+4.3%)",
            "0.500支撑位": "10.62",
            "0.618支撑位": "10.47 (距当前-0.3%)",
            "0.809止损位": "10.23 (距当前-2.6%)",
            "波段低点": "9.99 (距当前-4.9%)",
            "1.618目标价": "12.03 (距当前+14.6%)",
            "回撤位置": "黄金回撤区，重要支撑 （波段9.99→11.25，已回撤59.5%）",
            "风险收益比(观望)": "4.18:1 — 目标智能阻力(10.62) / 参考支撑0.618(10.47) — 极优(>3:1)",
            "斐波那契突破信号": "24日斐波那契位上突破↑、48日斐波那契位上突破↑、96日斐波那契位上突破↑"
          },
          "flow": {
            "5日均主力净流入": "2684万",
            "10日均主力净流入": "-1295万",
            "20日均主力净流入": "2415万",
            "连续净流入(截至昨日)": "1天",
            "资金流强度": "+0.435(强流入)",
            "5日均资金流强度": "-0.523",
            "大单强度": "+0.443(大单大幅净流入)",
            "5日均大单强度": "+0.059",
            "主力占比": "+0.72(主力主导)",
            "资金一致性": "诱多嫌疑",
            "资金均线": "金叉(趋势转多)",
            "资金动量": "资金动量指标=16389699、柱=15989950(多头)",
            "资金综合评分": "+1.34(强)",
            "大单-散户分歧": "+0.460(大单远强于散户)",
            "价格位置": "0.50(中位区)",
            "今日净流入(实时)": "2.42亿(大幅流入)",
            "今日主力占比(实时)": "0.1823(主力主导)",
            "早盘30分钟净流入": "1039万",
            "主力-散户占比差": "+0.1854 (主力主导)",
            "分时强度": "55.0(多头强势)",
            "分时趋势": "弱转强",
            "价格vs分时均线": "+0.91%",
            "外内盘比": "1.58(买方活跃)",
            "大单占比": "52.2%"
          },
          "sentiment": "[===-------] -3.3 轻微偏空"
        },
        {
          "code": "SH.600036",
          "cost_price": 35.0,
          "current_price": 37.73,
          "quantity": 500,
          "market_value": 18865.0,
          "pnl": 1365.0,
          "pnl_pct": 7.8,
          "weight_pct": 42.76,
          "fibonacci": {
            "当前价格": "37.73",
            "波段区间": "35.28~40.36",
            "计算类型": "上涨扩展",
            "位置": "48.0%(中位)",
            "智能支撑": "37.22",
            "智能阻力": "37.82",
            "智能止损": "36.48",
            "智能风险收益比": "0.10:1 (较差(<1:1))",
            "波段高点(0%回撤)": "40.15 (距当前+6.4%)",
            "0.382阻力位": "38.29 (距当前+1.5%)",
            "0.236阻力位": "39.00 (距当前+3.4%)",
            "0.500支撑位": "37.72 (距当前-0.0%)",
            "0.618支撑位": "37.14 (距当前-1.6%)",
            "0.809止损位": "36.21 (距当前-4.0%)",
            "波段低点": "35.28 (距当前-6.5%)",
            "1.618目标价": "43.16 (距当前+14.4%)",
            "回撤位置": "中位回撤，关键位 （波段35.28→40.15，已回撤49.7%）",
            "风险收益比(做多)": "6.00:1 — 目标智能阻力(37.82) / 参考支撑0.500(37.72) — 极优(>3:1)",
            "斐波那契突破信号": "24日斐波那契位上突破↑、48日斐波那契位上突破↑、96日斐波那契位上突破↑"
          },
          "flow": {
            "5日均主力净流入": "2.45亿",
            "10日均主力净流入": "6357万",
            "20日均主力净流入": "3922万",
            "连续净流入(截至昨日)": "4天",
            "资金流强度": "+0.858(强流入)",
            "5日均资金流强度": "+0.085",
            "大单强度": "+0.403(大单大幅净流入)",
            "5日均大单强度": "+0.082",
            "主力占比": "+0.93(主力主导)",
            "资金一致性": "诱多嫌疑",
            "资金均线": "金叉(趋势转多)",
            "资金动量": "资金动量指标=84655769、柱=76423859(多头)",
            "资金综合评分": "+2.27(极强)",
            "主力意图": "资金突增",
            "大单-散户分歧": "+0.866(大单远强于散户)",
            "价格位置": "0.50(中位区)",
            "今日净流入(实时)": "8.89亿(大幅流入)",
            "今日主力占比(实时)": "0.2033(主力主导)",
            "早盘30分钟净流入": "4949万",
            "主力-散户占比差": "+0.2033 (主力主导)",
            "分时强度": "56.0(多头强势)",
            "分时趋势": "弱转强",
            "价格vs分时均线": "+1.10%",
            "外内盘比": "1.68(买方活跃)",
            "大单占比": "29.9%"
          },
          "sentiment": "[===-------] -3.3 轻微偏空"
        },
        {
          "code": "SZ.000858",
          "cost_price": 150.0,
          "current_price": 73.76,
          "quantity": 200,
          "market_value": 14752.0,
          "pnl": -15248.0,
          "pnl_pct": -50.83,
          "weight_pct": 33.44,
          "fibonacci": {
            "当前价格": "73.76",
            "波段区间": "71.75~109.19",
            "计算类型": "上涨扩展",
            "位置": "5.0%(接近波段低点)",
            
... (truncated)
```

---

### analyze.stock_track

**股票收益跟踪**

- 耗时: 0.37s
- 响应大小: 100,705 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "analyze.stock_track",
  "params": {
    "days": 4
  }
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "analyze.stock_track", "params": {"days": 4}}

resp = requests.post(url, headers=headers, json=payload, timeout=60)
print(resp.json())
```

请求体：

```json
{
  "api": "analyze.stock_track",
  "params": {
    "days": 4
  }
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "status": "success",
    "days": 4,
    "count": 262,
    "records": [
      {
        "code": "SZ.301520",
        "name": "万邦医药",
        "track_date": "2026-07-06",
        "score_record_id": 83081,
        "source": "subscription_model",
        "sub_sources": "[\"cyb_3d\"]",
        "base_price": 31.4,
        "base_pct_change": 1.29,
        "open_price": 38.0,
        "high_price": 38.03,
        "low_price": 38.0,
        "close_price": 38.03,
        "max_return": 21.11,
        "min_return": 21.02,
        "close_return": 21.11,
        "open_return": 21.02,
        "updated_at": "2026-07-06T15:30:12"
      },
      {
        "code": "SZ.301345",
        "name": "涛涛车业",
        "track_date": "2026-07-06",
        "score_record_id": 91344,
        "source": "subscription_model",
        "sub_sources": "[\"cyb_3d\"]",
        "base_price": 231.01,
        "base_pct_change": 3.45,
        "open_price": 257.94,
        "high_price": 258.02,
        "low_price": 257.94,
        "close_price": 258.02,
        "max_return": 11.69,
        "min_return": 11.66,
        "close_return": 11.69,
        "open_return": 11.66,
        "updated_at": "2026-07-06T15:30:11"
      },
      {
        "code": "SZ.002025",
        "name": "航天电器",
        "track_date": "2026-07-06",
        "score_record_id": 83098,
        "source": "subscription_model",
        "sub_sources": "[\"sz_main_3d\"]",
        "base_price": 83.1,
        "base_pct_change": 3.62,
        "open_price": 92.07,
        "high_price": 92.07,
        "low_price": 92.07,
        "close_price": 92.07,
        "max_return": 10.79,
        "min_return": 10.79,
        "close_return": 10.79,
        "open_return": 10.79,
        "updated_at": "2026-07-06T15:30:12"
      },
      {
        "code": "SH.688702",
        "name": "盛科通信",
        "track_date": "2026-07-06",
        "score_record_id": 91351,
        "source": "subscription_model",
        "sub_sources": "[\"kcb_3d\"]",
        "base_price": 323.64,
        "base_pct_change": -8.33,
        "open_price": 357.62,
        "high_price": 357.62,
        "low_price": 357.0,
        "close_price": 357.0,
        "max_return": 10.5,
        "min_return": 10.31,
        "close_return": 10.31,
        "open_return": 10.5,
        "updated_at": "2026-07-06T15:30:11"
      },
      {
        "code": "SZ.000938",
        "name": "紫光股份",
        "track_date": "2026-07-06",
        "score_record_id": 91339,
        "source": "subscription_model",
        "sub_sources": "[\"sz_main_1d\"]",
        "base_price": 30.28,
        "base_pct_change": 4.31,
        "open_price": 33.31,
        "high_price": 33.31,
        "low_price": 33.31,
        "close_price": 33.31,
        "max_return": 10.01,
        "min_return": 10.01,
        "close_return": 10.01,
        "open_return": 10.01,
        "updated_at": "2026-07-06T15:30:11"
      },
      {
        "code": "SZ.002025",
        "name": "航天电器",
        "track_date": "2026-07-06",
        "score_record_id": 91361,
        "source": "subscription_model",
        "sub_sources": "[\"sz_main_3d\"]",
        "base_price": 83.7,
        "base_pct_change": 0.72,
        "open_price": 92.07,
        "high_price": 92.07,
        "low_price": 92.07,
        "close_price": 92.07,
        "max_return": 10.0,
        "min_return": 10.0,
        "close_return": 10.0,
        "open_return": 10.0,
        "updated_at": "2026-07-06T15:30:11"
      },
      {
        "code": "SZ.002245",
        "name": "蔚蓝锂芯",
        "track_date": "2026-07-06",
        "score_record_id": 91340,
        "source": "subscription_model",
        "sub_sources": "[\"sz_main_1d\"]",
        "base_price": 20.34,
        "base_pct_change": 5.12,
        "open_price": 22.37,
        "high_price": 22.37,
        "low_price": 22.37,
        "close_price": 22.37,
        "max_return": 9.98,
        "min_return": 9.98,
        "close_return": 9.98,
        "open_return": 9.98,
        "updated_at": "2026-07-06T15:30:11"
      },
      {
        "code": "SH.688347",
        "name": "华虹公司",
        "track_date": "2026-07-06",
        "score_record_id": 91330,
        "source": "subscription_model",
        "sub_sources": "[\"kcb_1d\"]",
        "base_price": 288.87,
        "base_pct_change": 1.54,
        "open_price": 309.7,
        "high_price": 309.7,
        "low_price": 309.68,
        "close_price": 309.68,
        "max_return": 7.21,
        "min_return": 7.2,
        "close_return": 7.2,
        "open_return": 7.21,
        "updated_at": "2026-07-06T15:30:11"
      },
      {
        "code": "SZ.300363",
        "name": "博腾股份",
        "track_date": "2026-07-06",
        "score_record_id": 91345,
        "source": "subscription_model",
        "sub_sources": "[\"cyb_3d\"]",
        "base_price": 16.9,
        "base_pct_change": 2.99,
        "open_price": 17.97,
        "high_price": 17.97,
        "low_price": 17.97,
        "close_price": 17.97,
   
... (truncated)
```

---

## 策略选股

### strategy.screener

**策略选股**

- 耗时: 0.32s
- 响应大小: 20,074 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "strategy.screener",
  "params": {
    "top_n": 3
  }
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "strategy.screener", "params": {"top_n": 3}}

resp = requests.post(url, headers=headers, json=payload, timeout=120)
print(resp.json())
```

请求体：

```json
{
  "api": "strategy.screener",
  "params": {
    "top_n": 3
  }
}
```

#### 响应

```json
{
  "code": 204,
  "message": "无结果",
  "data": {
    "strategy": "strategy_screener",
    "status": "success",
    "disclaimer": "免责声明：策略选股结果基于多策略筛选和5维评分，仅供参考，不构成投资建议。股市有风险，投资需谨慎。",
    "reasoning": "策略选股API调用 (评分缓存命中)",
    "session_id": "",
    "result": {
      "status": "success",
      "top3_selected": [
        {
          "code": "SH.688400",
          "name": "凌云光",
          "overall_score": 57.9,
          "verdict": "建议观望",
          "confidence": "中等",
          "sentiment_norm": 43.5,
          "tick_norm": 82.5,
          "flow_norm": 43.2,
          "tech_norm": 85.0,
          "kline_norm": 50.0,
          "source": "strategy_top3",
          "strategy_sources": [
            "高低回撤"
          ],
          "timestamp": "2026-06-30T14:32:06.008640"
        },
        {
          "code": "SH.603050",
          "name": "科林电气",
          "overall_score": 57.1,
          "verdict": "参考benchmark评分",
          "confidence": 0.5,
          "sentiment_norm": 41.0,
          "tick_norm": 79.0,
          "flow_norm": 52.4,
          "tech_norm": 57.5,
          "kline_norm": 45.0,
          "source": "strategy_top3",
          "strategy_sources": [
            "高低回撤"
          ],
          "timestamp": "2026-07-02T14:37:52.876723"
        },
        {
          "code": "SH.603893",
          "name": "瑞芯微",
          "overall_score": 56.8,
          "verdict": "参考benchmark评分",
          "confidence": 0.5,
          "sentiment_norm": 41.0,
          "tick_norm": 71.0,
          "flow_norm": 49.1,
          "tech_norm": 82.5,
          "kline_norm": 50.0,
          "source": "strategy_top10",
          "strategy_sources": [
            "高低回撤"
          ],
          "timestamp": "2026-07-02T14:37:52.876723"
        }
      ],
      "llm_report": "## 策略选股报告 [2026-07-06]\n\n以下股票通过多策略筛选（超跌反弹/高低回撤/高位回落/振幅回撤），经5维综合评分后精选：\n\n### 今日盘面催化\n\n今日涨停核心驱动力来自三大政策事件：工信部等四部门发布AI赋能新型工业化三年行动，引爆人工智能与CPO芯片；国家药监局同步出台AI辅助诊断审评新规及化学药一致性评价绿色通道，驱动创新药、医疗器械、化学制药集体走强；国家能源局、工信部分别启动新型电力系统输变电装备升级与车路云一体化试点扩容，带动输变电设备、汽车零部件板块涨停。此外，多家龙头公司发布重磅产品进展或订单公告，形成政策与产业双轮共振。\n\n### Top3 概览\n\n**1. 迪普科技(SZ.300768)** 评分51 | 参考benchmark评分（0.5）\n   板块:信息安全 | 策略来源:超跌反弹, 高低回撤\n   参考benchmark评分\n\n**2. 恒勃股份(SZ.301225)** 评分50 | 参考benchmark评分（0.5）\n   板块:汽车零部件 | 策略来源:高低回撤\n   参考benchmark评分\n\n**3. 德才股份(SH.605287)** 评分50 | 参考benchmark评分（0.5）\n   板块:建筑装饰 | 策略来源:高低回撤\n   参考benchmark评分\n\n==================================================\n\n### 详细分析\n\n#### 第1名：迪普科技(SZ.300768)\n综合评分：51分\n策略来源：超跌反弹, 高低回撤\n结论：参考benchmark评分（信心0.5）\n\n## 快速结论\n\n**参考benchmark评分**（信心：0.5 | 情绪 建议观望）\n\n## 综合评分：51分（中性）\n\n| 维度 | 得分 | 权重 | 说明 |\n|------|------|------|------|\n| 综合情绪 | 41/100 | 15% | 偏弱 |\n| 分时强度 | 50/100 | 25% | 中性 |\n| 资金面 | 55/100 | 40% | 中性 |\n| 技术面 | 58/100 | 10% | 偏强 |\n| K线形态 | 47/100 | 10% | 中性 |\n\n> ⚠️ 评分来自benchmark缓存，非实时计算\n\n- 复用近期benchmark评分\n\n\n---\n\n免责申明：本报告仅供参考，不构成投资建议。股市有风险，投资需谨慎。\n\n当前趋势略偏积极，但信心不足，信号存在明显分歧。\n\n一方面，资金面显示主力连续5天净流入，今天又主动买入1060万元，盘中大单活跃，分时走势强劲（强度82分），价格稳稳站在日线均价和成交均价上方，均线也呈现向上发散状态；另一方面，技术面上虽出现多个“金叉”和突破信号，但历史数据显示，当收盘价大幅高于短期均线（如MA5）和中期均线（MA20）时，后续出现回调的概率很高——类似情况在过去210次中，有93%~94%最终发生了价格回落。此外，当前价格已逼近智能参考阻力14.62元，距离波段高点19.54元仍有较大空间，但短期上行动力正面临考验。\n\n需特别注意：当前处于深度超买区域，价格明显脱离均线运行，历史上这类状态多数伴随快速回调。虽然盘中热度较高，但日线级别趋势尚未真正转强，若主力资金节奏变化或市场情绪进一步走弱，短期波动可能加大。\n\n## 量价趋势\n\n价格站上5日线(+8.2%)，短期均线多头排列。\n今日放量至均量的1.53倍，交投活跃。\n分时图价格运行均线上方(+5.13%)，买方占优，分时趋势弱转强，分时均线上升中。\n5日累计上涨19.1%，短期动能偏强。\n\n## 斐波那契位置与策略\n\n操作剧本（参考）\n\n| 场景 | 怎么看 | 应对 |\n|------|--------|------|\n| 放量上行 | 资金态度积极，突破确认 | 可轻仓跟随，注意止损 |\n| 回踩确认 | 回踩13.10支撑位 | 若不破可加仓 |\n| 假突破回落 | 突破失败，重回区间 | 跌破13.10果断止损 |\n| 横盘消化 | 多空拉锯整理 | 持有观望，等方向选择 |\n\n## 实时资金流\n\n主力今日小幅净流入1060万。\n主力占比73.6%，机构资金主导盘面。\n\n==================================================\n\n#### 第2名：恒勃股份(SZ.301225)\n综合评分：50分\n策略来源：高低回撤\n结论：参考benchmark评分（信心0.5）\n\n## 快速结论\n\n**参考benchmark评分**（信心：0.5 | 情绪 建议观望）\n\n## 综合评分：50分（中性）\n\n| 维度 | 得分 | 权重 | 说明 |\n|------|------|------|------|\n| 综合情绪 | 41/100 | 15% | 偏弱 |\n| 分时强度 | 50/100 | 25% | 中性 |\n| 资金面 | 55/100 | 40% | 中性 |\n| 技术面 | 52/100 | 10% | 中性 |\n| K线形态 | 42/100 | 10% | 偏弱 |\n\n> ⚠️ 评分来自benchmark缓存，非实时计算\n\n- 复用近期benchmark评分\n\n\n---\n\n免责申明：本报告仅供参考，不构成投资建议。股市有风险，投资需谨慎。\n\n趋势消极，但信心偏弱，当前信号分歧较大，整体偏向谨慎观望。\n\n历史数据显示，近5日趋势消极信号出现频率极高，且历史概率显著高于正常水平，这类情况过去几乎每次都伴随短期回调；技术面上虽有MA5上穿MA20的积极迹象，但价格处于缩量下跌状态，布林带收窄也提示变盘临近，方向尚不明朗；资金面虽今日小幅流入，但连续两日净流入后出现“诱多嫌疑”，叠加实时交易由散户主导，说明主力态度犹豫，难成持续推动力。\n\n技术面承压明显，尤其在关键参考阻力105.02附近压力较大，而情绪分仅34分、分时趋势已由强转弱，叠加多项趋势消极信号反复确认，短期下行风险不容忽视。需特别注意午后是否出现冲高回落或放量下跌，一旦跌破分时均线，可能加速调整。\n\n## 量价趋势\n\n价格站上5日线(+10.9%)，短期均线多头排列。\n今日放量至均量的1.37倍，交投活跃。\n缩量下跌，卖压减轻但买盘也不足。\n分时图价格运行均线下方(-1.22%)，卖方占优，分时趋势强转弱，分时均线下降中。\n5日累计上涨29.9%，短期动能偏强。\n\n## 斐波那契位置与策略\n\n操作剧本（参考）\n\n| 场景 | 怎么看 | 应对 |\n|------|--------|------|\n| 上行测试 | 冲击105.02阻力 | 不追，观察量能和资金态度 |\n| 下探支撑 | 回踩97.02支撑 | 若不破可轻仓低吸 |\n| 横盘震荡 | 多空拉锯 | 不参与，等方向选择 |\n| 放量突破 | 突破105.02 | 资金态度转强，可考虑轻仓跟随 |\n\n## 实时资金流\n\n主力今日小幅净流入2753万。\n主力占比仅3.4%，散户主导，缺乏机构参与。\n\n==================================================\n\n#### 第3名：德才股份(SH.605287)\n综合评分：50分\n策略来源：高低回撤\n结论：参考benchmark评分（
... (truncated)
```

---

## 搜索

### search.tavily

**Tavily搜索**

- 耗时: 3.19s
- 响应大小: 16,387 bytes

#### 请求

```bash
curl -X POST https://astockd.com/api/v1/open/invoke \
  -H "Authorization: Bearer <你的密钥>" \
  -H "Content-Type: application/json" \
  -d '{
  "api": "search.tavily",
  "query": "A股 今日 行情",
  "params": {
    "max_results": 3
  }
}'
```

Python：

```python
import requests

url = "https://astockd.com/api/v1/open/invoke"
headers = {"Authorization": "Bearer 你的密钥", "Content-Type": "application/json"}
payload = {"api": "search.tavily", "query": "A股 今日 行情", "params": {"max_results": 3}}

resp = requests.post(url, headers=headers, json=payload, timeout=120)
print(resp.json())
```

请求体：

```json
{
  "api": "search.tavily",
  "query": "A股 今日 行情",
  "params": {
    "max_results": 3
  }
}
```

#### 响应

```json
{
  "code": 200,
  "message": "ok",
  "data": {
    "status": "success",
    "query": "A股 今日 行情",
    "answer": "Today's Shanghai Composite Index (A-shares) closed at 4041.24, down 0.06%. The index had a trading volume of 143.21 billion yuan. The market showed minor fluctuations throughout the day.",
    "results": [
      {
        "url": "https://xueqiu.com/S/SH000001",
        "title": "上证指数(SH000001)股票股价_股价行情_财报_数据报告 - 雪球",
        "content": "[![Image 1: 雪球](https://xqimg.imedao.com/18cc98da6c19cdc03fdb7c76.png)首页](https://xueqiu.com/). [股票 __](https://xueqiu.com/k?q=#/stock). [查看「」的全部搜索结果](https://xueqiu.com/k?q=). ![Image 2](https://xqimg.imedao.com/18bf0aab29a1c37c3f92e18f.png). ![Image 3](https://xqimg.imedao.com/18bf0aab267182c13fe89a54.png). *   [登录](https://xueqiu.com/S/SH000001). ![Image 4](https://xueqiu.com/S/SH000001). [加自选](https://xueqiu.com/S/SH000001#). 最低：4005.41 昨收：4043.64 成交额：1.43万亿 量比：0.93. 52周最低：3446.97 下跌：927 总市值：67.69万亿. [股票对比](https://xueqiu.com/S/SH000001). [](https://xueqiu.com/S/SH000001). [](https://xueqiu.com/S/SH000001). [去问答广场看看 >](https://xueqiu.com/ask/square). [__](https://xueqiu.com/S/SH000001). [__](javascript:;)[__](https://xueqiu.com/S/SH000001)[__](https://xueqiu.com/S/SH000001)[__](https://xueqiu.com/S/SH000001)[![Image 10](https://assets.imedao.com/ugc/images/editor/award-f2d0f7dfda.png)悬赏](https://xueqiu.com/S/SH000001)[__ 长文](https://mp.xueqiu.com/write/?position=pc_stock_post). [新帖](https://xueqiu.com/S/SH000001)[热帖](https://xueqiu.com/S/SH000001). [![Image 11](https://xavatar.imedao.com/community/202311/1703222177039-1703222179320.jpeg!240x240.jpg)](https://xueqiu.com/1650699212). [收起 __](https://xueqiu.com/S/SH000001). [$上证指数(SH000001)$](https://xueqiu.com/S/SH000001) 100个到1000个要多少年啊， 今天亏损2.6. [展开 __](https://xueqiu.com/S/SH000001). [__ 投诉](https://xueqiu.com/S/SH000001). [![Image 13](https://xavatar.imedao.com/community/20160/1453610621472-1453610623073.jpg!240x240.jpg)](https://xueqiu.com/aguilmkz). [收起 __](https://xueqiu.com/S/SH000001). [](https://xueqiu.com/8524534519/398645956). [__ 投诉](https://xueqiu.com/S/SH000001). [![Image 14](https://xavatar.imedao.com/community/20265/1781862630707-1781862631368.jpeg!240x240.jpg)](https://xueqiu.com/5431263675). [$上证指数(SH000001)$](https://xueqiu.com/S/SH000001) 2014年-2019年-2024年-各位下一轮2029年见![Image 15: [笑]](https://assets.imedao.com/ugc/images/face_regular/v1/emoji_01_smile.png?v=1). [__ 投诉](https://xueqiu.com/S/SH000001). [![Image 16](https://xavatar.imedao.com/community/20224/1652064543134-1652064543370.jpeg!240x240.jpg)](https://xueqiu.com/9733048547). [$上证指数(SH000001)$](https://xueqiu.com/S/SH000001) 不要羡慕哪些躲过下跌的人，因为大概率上涨的时候他们也没有在场....投资是买和卖的艺术，不是买点好就是对的。俗话说会买是徒弟，会卖才是师傅。. [__ 投诉](https://xueqiu.com/S/SH000001). [![Image 17](https://xavatar.imedao.com/community/20265/1781335877878-1781335878188.jpg!240x240.jpg)](https://xueqiu.com/2201128278). [收起 __](https://xueqiu.com/S/SH000001). [](https://xueqiu.com/2201128278/398645632). [$掌趣科技(SZ300315)$](https://xueqiu.com/S/SZ300315). [__ 投诉](https://xueqiu.com/S/SH000001). [![Image 19](https://xavatar.imedao.com/community/202310/1699236416866-1699236417091.png!240x240.jpg)](https://xueqiu.com/2029621325). [收起 __](https://xueqiu.com/S/SH000001). [展开 __](https://xueqiu.com/S/SH000001). [__ 投诉](https://xueqiu.com/S/SH000001). [![Image 20](https://xavatar.imedao.com/community/20263/1777372394787-1777372394976.jpg!240x240.jpg)](https://xueqiu.com/4328965805). [收起 __](https://xueqiu.com/S/SH000001). [](https://xueqiu.com/4328965805/398645540). [__ 投诉](https://xueqiu.com/S/SH000001). [![Image 22](https://xavatar.imedao.com/community/20263/1775066327865-1775066328165.jpeg!240x240.jpg)](https://xueqiu.com/3001715520). [收起 __](https://xueqiu.com/S/SH000001). [$上证指数(SH000001)$](https://xueqiu.com/S/SH000001)[$长飞光纤(SH601869)$](https://xueqiu.com/S/SH601869) 事以至此，擅长的领域来了，回本！！！. [展开 __](https://xueqiu.com/S/SH000001). [__ 投诉](https://xueqiu.com/S/SH000001). [![Image 23](https://xavatar.imedao.com/community/20240/1704242135932-1704242137120.jpeg!240x240.jpg)](https://xueqiu.com/5281611609). [$上证指数(SH000001)$](https://xueqiu.com/S/SH000001) 怕高的都是苦命人，现在怎么分析这句话？？？. [__ 投诉](https://xueqiu.com/S/SH000001). [![Image 24](https://xavatar.imedao.com/community/202310/1699236528702-1699236528846.png!240x240.jpg)](https://xueqiu.com/9092514787). [__ 投诉](https://xueqiu.com/S/SH000001). *   [![Image 27](https://xavatar.imedao.com/community/20239/1697532787814-1697532788415.jpeg!240x240.jpg)](https://xueqiu.com/1952051658). *   [![Image 28](https://xavatar.imedao.com/community/20180/1516791929344-1516791929523.jpg!240x240.jpg)](https://xueqiu.com/6078717678). *   [![Image 29](https://xavatar.imedao.com/community/20250/1737611441081-1737611441269.jpeg!240x240.jpg)](https://xueqiu.com/9038481061). *   [![Image 30](https://xavatar.imedao.com/community/20213/1617369939276-1617369939669.jpg!240x240.jpg)](https://xueqiu.com/7978056001). *   [![Image 31](https://xavatar.imedao.com
... (truncated)
```

---
