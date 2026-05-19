---
name: stock_analysis
description: A股智能分析 - 技术指标/资金流/K线形态/自然语言推理分析
metadata: {"openclaw": {"requires": {"bins": ["curl"], "env": ["ASTOCKD_API_URL"]}, "os": ["macos", "linux"]}}
---

# A股智能分析 Skill

分析A股股票的技术面、资金流、K线形态等特征，提供 300+ 特征的综合分析。支持自然语言查询和结构化指定。

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
- `k_patterns`: K线形态（18特征）—— 早晨之星、黄昏之星、阳包阴、阴包阳、锤头、吊颈、红三兵、黑三鸦等经典反转/持续形态
- `lifeline`: 生死线指标（7特征）—— 红蓝带、三色球、绝对买点、相对买点、自杀买点警告、日线卖出信号、蓝带卖出信号
- `experience`: 经验特征（4特征）—— 当前价格、涨跌幅、成交量趋势等基础行情数据
- `ohlcv_combined`: OHLCV综合（42特征）—— K线形态 + 生死线 + 经验特征的完整组合
- `technical`: 技术指标（212特征）—— RSI/MACD/KDJ/布林/CCI/ROC/ADX/OBV/斐波那契/FVG缺口等，包含60分钟和日线多时间周期指标
- `flow_minute`: 分钟资金流（16特征）—— 早盘30分净流入、尾盘30分净流入、日内吸筹、日内出货、资金流转正时刻、洗盘检测等
- `flow_daily`: 日线资金流（64特征）—— MFS主力流向、MFI资金强度、MFCS资金周期、主力净流入MA、资金流动量MACD、流入流出背离等
- `flow_combined`: 综合资金流（80特征）—— 分钟级 + 日线级资金流的完整组合分析

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

## 特征体系总览

AStockD 特征系统由 **8 大特征组** 组成，总计约 **339 个特征**：

| 特征组 | 特征数 | 数据源 | 核心功能 |
|--------|--------|--------|----------|
| `k_patterns` | 18 | 日线OHLCV | 经典K线形态识别（反转/持续形态） |
| `lifeline` | 7 | 日线OHLCV | 生死线买卖点系统（红蓝带/三色球） |
| `experience` | 4 | 日线OHLCV | 基础行情数据 |
| `ohlcv_combined` | 42 | 日线OHLCV | K线+生死线+经验完整组合 |
| `technical` | 212 | 60分钟OHLCV | 多周期技术指标（RSI/MACD/KDJ/布林/斐波那契等） |
| `flow_minute` | 16 | 分钟资金流 | 盘中资金流向（早盘/尾盘/吸筹/出货） |
| `flow_daily` | 64 | 日线资金流 | 主力资金分析（MFS/MFI/MFCS/背离） |
| `flow_combined` | 80 | 分钟+日线资金流 | 综合资金流分析 |

### 关键词 → 特征组映射

当需要自行判断特征组时，参考以下映射：

| 关键词 | 特征组 |
|--------|--------|
| 技术指标/RSI/MACD/KDJ/布林/CCI/ROC/动量/ADX | technical |
| 主力/大单/散户/资金流/MFS/MFI/MFCS/资金流动量 | flow_daily |
| K线/形态/吞没/十字星/锤头/反转/持续 | k_patterns 或 ohlcv_combined |
| 生死线/红蓝带/三色球/买卖点/金葫芦 | lifeline 或 ohlcv_combined |
| 分钟/早盘/尾盘/洗盘/吸筹/出货 | flow_minute |
| 斐波那契/FVG缺口/Z-score | technical |
| 背离/趋势/周期 | flow_daily 或 technical |
| 买卖/涨跌/看多看空/综合分析 | technical + flow_daily + ohlcv_combined |

## 结果解读

### 分析报告结构

分析结果中 `report` 包含结构化分析报告，包含以下章节：

1. **综合诊断**：
   - 趋势方向（上升/下降/震荡）
   - 情绪指标（-10~+10 可视化条形图）
   - 关键支撑位/阻力位
   - 风险收益比
   - 资金面分析（主力/散户行为）
   - 技术面分析（多周期指标状态）
   - K线形态特征
   - 资讯面（可选）
   - 积极信号/消极信号汇总

2. **趋势信号解读**：
   - 按技术指标分类（RSI/MACD/KDJ/布林带等）
   - 按资金流分类（主力流向/散户情绪）
   - 按生死线分类（红蓝带/买卖点）
   - 展示看涨/看跌信号及强度

3. **知识库信号**：
   - 看涨信号 Top 3（高概率规则 + 指标条件）
   - 看跌信号 Top 3（高概率规则 + 指标条件）
   - 历史成功率参考

4. **风险提示**：
   - 市场风险警示
   - 技术面风险点
   - 资金面风险点

### 情绪指标 (sentiment)

```json
{
  "score": 5,           // -10 到 +10 的情绪评分
  "label": "偏多",      // 文字标签
  "bar": "[-----=====]" // 可视化条形图
}
```

**评分解读**：
- **+7 ~ +10**：极度看涨
- **+4 ~ +6**：偏多
- **-3 ~ +3**：中性震荡
- **-6 ~ -4**：偏空
- **-10 ~ -7**：极度看跌

### 用量统计 (usage)

`usage` 字段记录 LLM token 消耗和费用：

```json
{
  "llm": {
    "actual_tokens": 6200,              // 实际消耗的 token 总数
    "platform_tokens": 7750,            // 对外展示的处理后 token 数（含系数调整）
    "platform_prompt_tokens": 3375,     // 处理后的 prompt tokens
    "platform_completion_tokens": 4375, // 处理后的 completion tokens
    "total_cost_cny": 0.05,             // 费用（人民币）
    "details": [                        // 每次 LLM 调用明细
      {
        "model": "deepseek-chat",
        "purpose": "stock_report",
        "tokens": 6200,
        "cost_cny": 0.05
      }
    ]
  }
}
```

**Platform 计费规则**：
- **股票报告**：actual_tokens < 5000 时取 5000~6000 区间，再 × 1.25
- **其他类型**：actual_tokens × 1.3（最低 100 tokens）
- **费用计算**：基于 DeepSeek API 定价标准

### 免责声明 (disclaimer)

`disclaimer` 为顶层字段，仅在响应中出现一次，提醒用户投资风险。

## 注意事项

### 输入限制
- **查询字符数限制**：最长 120 个中文字符
- **股票代码支持格式**：
  - 纯数字：`"600519"`、`"000858"`
  - 带市场前缀：`"SH.600519"`、`"SZ.000858"`
- **市场自动识别**：6开头为沪市(SH)，其他为深市(SZ)

### 数据更新
- **盘中数据**：实时更新（分钟级资金流、60分钟技术指标）
- **盘后数据**：缓存时间更长，依赖交易所收盘结算
- **日线数据**：默认获取 300 条（可通过 `ohlcv_count` 调整）
- **60分钟数据**：默认获取 800 条（可通过 `technical_count` 调整）

### 多轮对话
- **session_id**：用于多轮对话上下文保持
- **skip_clarification**：默认 `true`（AI Agent 推荐），意图模糊时自动选择全面分析
- **selected_option**：用户选择的选项索引，配合 `session_id` 使用

### 性能建议
- **全量分析**：使用 `/analyze/full` 端点，一次性获取 technical + flow_daily + ohlcv_combined
- **指定特征组**：明确需求时直接指定 `feature_groups`，跳过推理步骤
- **自然语言查询**：最灵活，适合用户不确定需求或需要智能识别股票

## 相关文档

- [API Reference](../../docs/api-reference.md) — 完整端点说明
- [Feature Reference](../../docs/features.md) — 300+ 特征详解
