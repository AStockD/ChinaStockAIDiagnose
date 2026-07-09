## 支持的全部 API

### 系统（0 Credit）

| API | 说明 |
|-----|------|
| `system.status` | 服务状态 |
| `system.health` | 健康检查 |

### 资金流-原始

| API | 说明 | 配额 |
|-----|------|------|
| `flow.minute` | 分钟级资金流 | 0.1 |
| `flow.daily` | 日级资金流 | 0.1 |
| `flow.combined` | 合并资金流 | 0.2 |

### 资金流-特征

| API | 说明 | 配额 |
|-----|------|------|
| `flow_features.minute` | 分钟级资金流特征 | 0.2 |
| `flow_features.daily` | 日级资金流特征 | 0.2 |
| `flow_features.combined` | 合并资金流特征 | 0.4 |

### 特征计算

| API | 说明 | 配额 |
|-----|------|------|
| `features.k_patterns` | K 线形态（18 种） | 0.2 |
| `features.lifeline` | 生死线（7 个） | 0.2 |
| `features.experience` | 经验特征 | 0.2 |
| `features.combined` | 合并特征 | 0.6 |
| `features.technical` | 技术特征（212 个） | 0.6 |
| `features.all` | 全量特征 | 1.2 |

### AI 分析

| API | 说明 | 配额 |
|-----|------|------|
| `analyze` | AI 原生智能分析 | 5 |
| `analyze.full` | 全量分析 | 3 |
| `analyze.query` | 自然语言查询 | 3 |
| `analyze.manifest` | 特征清单 | 0 |

### 评分

| API | 说明 | 配额 |
|-----|------|------|
| `score` | 单股五维评分 | 1.5 |
| `score.batch` | 批量评分 | 1.5 × 股票数 |
| `scores.recent` | 近期高分 | 3 |

### 盘中 / 策略 / 搜索

| API | 说明 | 配额 |
|-----|------|------|
| `analyze.tick_intraday` | 逐笔分时 | 1 |
| `analyze.hot_sector` | 热门板块 | 3 |
| `analyze.market_review` | 大盘复盘 | 3 |
| `analyze.portfolio` | 组合持仓 | 3 |
| `analyze.stock_track` | 收益跟踪 | 0 |
| `strategy.screener` | 策略选股 | 3 |
| `search.tavily` | Tavily 搜索 | 1 |
