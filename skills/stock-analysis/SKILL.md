---
name: stock_analysis
description: A股智能分析 - 技术指标/资金流/K线形态/自然语言推理分析
metadata: {"openclaw": {"requires": {"bins": ["curl"], "env": ["ASTOCKD_API_URL"]}, "os": ["macos", "linux"]}}
---

# A股智能分析 Skill

分析A股股票的技术面、资金流、K线形态等特征。支持自然语言查询和结构化指定。

## 使用场景

- 用户询问某只股票的技术面、资金流向、K线形态
- 用户说"帮我分析一下茅台"、"这只股票主力资金动向"
- 用户需要综合技术指标判断买卖信号

## 工具调用

### 基础配置

API 地址由环境变量 `ASTOCKD_API_URL` 指定，默认 `http://localhost:8888`。

### 1. 自然语言查询 (推荐)

当用户用自然语言描述分析需求且未提供股票代码时，调用 `/analyze/query` 端点，系统自动识别股票：

```bash
curl -s -X POST "${ASTOCKD_API_URL}/analyze/query" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "帮我看看比亚迪怎么样"
  }'
```

**多轮对话处理**：
- 默认 `skip_clarification=true`：意图模糊时自动选择全面分析，不会返回追问（推荐AI Agent使用）
- 如果需要交互式追问：设置 `skip_clarification=false`，返回 `status == "needs_clarification"` 时展示选项
- 用户选择后，携带 `session_id` + `selected_option` + `skip_clarification=false` 再次调用

### 2. 指定股票代码分析

当已知股票代码时，调用 `/analyze` 端点：

```bash
curl -s -X POST "${ASTOCKD_API_URL}/analyze" \
  -H "Content-Type: application/json" \
  -d '{
    "stock_codes": ["600519"],
    "query": "用户的具体问题"
  }'
```

**完整请求体参数**:
- `stock_codes` (必填): 股票代码列表
- `query` (可选): 自然语言需求描述
- `feature_groups` (可选): 直接指定特征组，跳过推理
- `session_id` (可选): 多轮对话会话ID
- `selected_option` (可选): 用户选择的选项索引（回应追问时使用）
- `ohlcv_count` (可选): 日线数据条数，默认300
- `technical_count` (可选): 60分钟数据条数，默认800

**多轮对话处理**：同上

### 3. 直接指定特征组

当用户需求明确或你自己判断出特征组时，跳过推理直接调用：

```bash
curl -s -X POST "${ASTOCKD_API_URL}/analyze" \
  -H "Content-Type: application/json" \
  -d '{
    "stock_codes": ["600519"],
    "feature_groups": ["technical", "flow_daily"]
  }'
```

**可用特征组**:
- `k_patterns`: K线形态特征（吞没/十字星/锤子线等20+形态）
- `lifeline`: 生死线指标（红蓝带/买卖点信号）
- `experience`: 经验特征（价格变动/成交量趋势）
- `ohlcv_combined`: OHLCV综合（K形态+生死线+经验，42特征）
- `technical`: 技术指标（RSI/MACD/KDJ/布林等212特征）
- `flow_minute`: 分钟资金流（早盘/尾盘/洗盘等16特征）
- `flow_daily`: 日线资金流（MFS/MFI/主力等64特征）
- `flow_combined`: 综合资金流（分钟+日线80特征）

### 4. 全量快捷分析

一次性获取技术+资金流(日线)+形态全部特征：

```bash
curl -s -X POST "${ASTOCKD_API_URL}/analyze/full" \
  -H "Content-Type: application/json" \
  -d '{"stock_codes": ["600519"]}'
```

返回: technical + flow_daily + ohlcv_combined 全部特征

### 5. 查看特征清单

```bash
curl -s "${ASTOCKD_API_URL}/analyze/manifest"
```

## 关键词 → 特征组映射

当需要自行判断特征组时，参考以下映射：

| 关键词 | 特征组 |
|--------|--------|
| 技术指标/RSI/MACD/KDJ/布林/动量 | technical |
| 主力/大单/散户/资金流/MFS/MFI | flow_daily |
| K线/形态/吞没/十字星/反转 | k_patterns 或 ohlcv_combined |
| 生死线/红蓝带/买卖点 | lifeline 或 ohlcv_combined |
| 分钟/早盘/尾盘/洗盘 | flow_minute |
| 买卖/涨跌/看多看空 | technical + flow_daily |

## 结果解读

分析结果中 `report` 包含结构化分析报告，包含以下章节：
- 综合诊断：趋势方向 + 情绪指标(-10~+10可视化条) + 关键支撑/阻力 + 风险收益比 + 资金面 + 技术面 + K线形态 + 资讯面 + 积极信号/消极信号
- 趋势信号解读：按技术指标/资金流/生死线分类展示看涨看跌信号
- 知识库信号：看涨/看跌各top3高概率规则及指标条件
- 风险提示

`usage` 字段记录 LLM 用量：
- `actual_tokens`: 实际消耗的 token 总数
- `platform_tokens`: 对外展示的处理后 token 数（含系数调整）
- `platform_prompt_tokens`: 处理后的 prompt tokens
- `platform_completion_tokens`: 处理后的 completion tokens
- `total_cost_cny`: 费用（人民币）
- `details`: 每次LLM调用明细（model/purpose/tokens/cost）

**Platform 计费规则**:
- 股票报告: actual < 5000 取 5000~6000 区间，再 × 1.25
- 其他: actual × 1.3（最低100）

`disclaimer` 为顶层字段，仅在响应中出现一次。

## 注意事项

- 查询字符数限制: 最长120个中文字符
- 股票代码支持格式: "600519"、"SH.600519"、"000858"、"SZ.000858"
- 市场自动识别: 6开头为沪市(sh)，其他为深市(sz)
- 盘中数据实时更新，盘后数据缓存时间更长
