# AStockD Feature Reference

## 1. Overview

AStockD's feature system is organized into **8 feature groups** totaling **300+ features**. Features are computed by independent pipelines and returned by the API in a structured `{value, label, description}` format.

**Architecture at a glance:**

| Pipeline | Feature Groups | Data Source |
|---|---|---|
| `ohlcv_pipeline` | k_patterns, lifeline, experience, ohlcv_combined | Daily OHLCV |
| `technical_pipeline` | technical | 60-minute OHLCV |
| `flow_minute_pipeline` | flow_minute | Real-time minute money flow |
| `flow_daily_pipeline` | flow_daily | Historical daily money flow |
| `flow_combined_pipeline` | flow_combined | Minute + daily money flow |
| `hot_sector_strategy` | hot_sector | Real-time market data (strategy, not features) |

---

## 2. Feature Groups Summary Table

| Group | Count | Description | Data Source |
|---|---|---|---|
| `k_patterns` | 18 | K-line candlestick patterns (morning star, engulfing, etc.) | Daily OHLCV |
| `lifeline` | 7 | Lifeline indicators (red/blue band, three-color ball, buy/sell signals) | Daily OHLCV |
| `experience` | 4 | Basic price and volume experience features | Daily OHLCV |
| `ohlcv_combined` | 42 | Combined OHLCV features (k_patterns + lifeline + experience) | Daily OHLCV |
| `technical` | 212 | Technical indicators (60m sessions, daily, derived, Z-score) | 60-minute OHLCV |
| `flow_minute` | 16 | Intraday money flow features (morning/closing session, wash trade, etc.) | Minute money flow |
| `flow_daily` | 64 | Daily money flow features (MFS, MFI, MFCS, divergence, etc.) | Daily money flow + OHLCV |
| `flow_combined` | 80 | Combined money flow (minute + daily) | Minute + daily money flow |
| `hot_sector` | 0 (strategy) | Hot sector limit-up tracking and follower selection strategy | Real-time market data |

**Total: ~339 features across 8 groups** (excluding hot_sector which is a strategy, not a feature group)

---

## 3. Technical Features (212 features)

Returned by `POST /features/technical` and included in `POST /features/all`.

### 3.1 Session 60m Indicators (32 features)

60-minute indicators extracted from specific trading sessions (morning start `mstart`, afternoon start `astart`, last bar `last`). Values are classified (`cls`) or raw.

| Feature | Chinese Label | Description |
|---|---|---|
| `boll_width_60m_astart_cls` | 布林带宽度(午盘)分类 | Bollinger Band width classification at afternoon session |
| `atr_14_60m_astart_cls` | ATR(午盘)分类 | 14-period ATR classification at afternoon session |
| `kdj_d_60m_astart_cls` | KDJ-D(午盘)分类 | KDJ-D value classification at afternoon session |
| `kdj_k_60m_mstart_cls` | KDJ-K(早盘)分类 | KDJ-K value classification at morning session |
| `ma5_ma20_margin_ratio_60m_last` | MA5/MA20间距比(60m) | MA5 vs MA20 margin ratio, last 60m bar |
| `roc_8_60m_last` | ROC-8(60m尾盘) | 8-period Rate of Change, last 60m bar |
| `rsi_14_60m_last` | RSI-14(60m尾盘) | 14-period RSI, last 60m bar |
| `boll_position_60m_mstart_cls` | BOLL位置(早盘)分类 | Bollinger Band position classification, morning session |
| `roc_6_60m_mstart` | ROC-6(60m早盘) | 6-period Rate of Change, morning session |
| `momentum_8_60m_last` | 动量-8(60m尾盘) | 8-period momentum, last 60m bar |
| `macd_hist_60m_last` | MACD柱(60m尾盘) | MACD histogram, last 60m bar |
| `momentum_6_60m_mstart` | 动量-6(60m早盘) | 6-period momentum, morning session |
| `pdi_60m_astart_cls` | PDI(午盘)分类 | +DI classification at afternoon session |
| `rsi_7_60m_mstart` | RSI-7(60m早盘) | 7-period RSI, morning session |
| `mdi_60m_astart_cls` | MDI(午盘)分类 | -DI classification at afternoon session |
| `rsi_21_60m_last` | RSI-21(60m尾盘) | 21-period RSI, last 60m bar |
| `roc_4_60m_last` | ROC-4(60m尾盘) | 4-period Rate of Change, last 60m bar |
| `cci_60m_mstart_cls` | CCI(早盘)分类 | CCI classification, morning session |
| `kdj_j_60m_mstart` | KDJ-J(60m早盘) | KDJ-J value, morning session |
| `macd_60m_last` | MACD(60m尾盘) | MACD DIF value, last 60m bar |
| `momentum_4_60m_last` | 动量-4(60m尾盘) | 4-period momentum, last 60m bar |
| `roc_2_60m_mstart` | ROC-2(60m早盘) | 2-period Rate of Change, morning session |
| `momentum_2_60m_mstart` | 动量-2(60m早盘) | 2-period momentum, morning session |
| `kdj_oversold_60m_astart` | KDJ超卖(60m午盘) | KDJ oversold signal at afternoon session |
| `kdj_cci_overbought_60m_mstart` | KDJ+CCI超买(60m早盘) | KDJ and CCI both overbought, morning session |
| `vol_ma5_60m_last` | 成交量MA5(60m尾盘) | 5-period volume MA, last 60m bar |
| `adx_60m_astart_cls` | ADX(午盘)分类 | ADX classification at afternoon session |
| `kdj_cci_oversold_60m_mstart` | KDJ+CCI超卖(60m早盘) | KDJ and CCI both oversold, morning session |
| `macd_signal_60m_last` | MACD信号线(60m尾盘) | MACD signal line, last 60m bar |
| `cci_oversold_60m_last` | CCI超卖(60m尾盘) | CCI oversold signal, last 60m bar |
| `macd_red_strong_60m_last` | MACD红柱增强(60m尾盘) | MACD red bar strengthening, last 60m bar |
| `obv_change_60m_mstart_cls` | OBV变化(早盘)分类 | OBV change rate classification, morning session |

### 3.2 Base 60m Indicators (27 features)

Aggregated 60-minute indicators (not session-specific), focusing on MA relationships, RSI levels, and trend detection.

| Feature | Chinese Label | Description |
|---|---|---|
| `close_vs_ma20_pct_last` | 收盘vs MA20% | Close price deviation from MA20 (%) |
| `ma5_vs_ma20_pct_last` | MA5 vs MA20% | MA5 deviation from MA20 (%) |
| `rsi_14` | RSI-14 | 14-period RSI |
| `atr_percentile_last` | ATR百分位 | ATR percentile in 60-day window |
| `rsi_14_within3_dw` | RSI-14 3日内下跌 | RSI-14 declined within 3 days |
| `rsi_21` | RSI-21 | 21-period RSI |
| `rsi_7` | RSI-7 | 7-period RSI |
| `rsi_14_within3_up` | RSI-14 3日内上涨 | RSI-14 rose within 3 days |
| `rsi_7_within3_dw` | RSI-7 3日内下跌 | RSI-7 declined within 3 days |
| `atr_ratio_last_cls` | ATR比率分类 | Afternoon/morning ATR ratio classification |
| `close_vs_ma50_pct_last` | 收盘vs MA50% | Close price deviation from MA50 (%) |
| `rsi_21_within3_dw` | RSI-21 3日内下跌 | RSI-21 declined within 3 days |
| `rsi_7_within3_up` | RSI-7 3日内上涨 | RSI-7 rose within 3 days |
| `ma20_up_trend` | MA20上升趋势 | MA20 is rising |
| `rsi_21_within3_up` | RSI-21 3日内上涨 | RSI-21 rose within 3 days |
| `price_vs_vwap_mstart_cls` | 价格vs VWAP(早盘)分类 | Price vs VWAP deviation classification, morning |
| `ma20_vs_ma50_pct_last` | MA20 vs MA50% | MA20 deviation from MA50 (%) |
| `ma50_vs_ma200_pct_last` | MA50 vs MA200% | MA50 deviation from MA200 (%) |
| `ma_gap_trend_1` | MA间距趋势1 | MA50 rising for 3 consecutive bars |
| `ma50_up_trend` | MA50上升趋势 | MA50 is rising |
| `ma_gap_trend_2` | MA间距趋势2 | MA50 falling for 3 consecutive bars |
| `ma200_up_trend` | MA200上升趋势 | MA200 is rising |
| `obv_acceleration_last_cls` | OBV加速度分类 | OBV acceleration ratio classification |
| `macd_weak_strong` | MACD弱转强 | MACD histogram transition from weak to strong |
| `is_ma50_up_cross_ma200` | MA50上穿MA200 | MA50 crossed above MA200 |
| `macd_strong_weak` | MACD强转弱 | MACD histogram transition from strong to weak |
| `is_ma50_up_cross_ma200_in5` | MA50上穿MA200(5日内) | MA50 crossed above MA200 within 5 bars |

### 3.3 Daily Indicators (110 features)

Daily-frequency technical indicators covering MA crossovers, RSI/MACD/KDJ/CCI/ROC signals, Bollinger Band analysis, Fibonacci levels, and FVG (Fair Value Gap) detection.

Key categories within daily indicators:
- **MA crossover signals**: `is_ma5_up_cross_ma10_day`, `is_ma10_up_cross_ma20_day`, `is_ma10_up_cross_ma36_day`, and their 5-day variants
- **RSI signals**: `rsi_7_buy_day` (<30), `rsi_14_sell_day` (>70), `rsi_overbought_reversal_day`
- **MACD signals**: `macd_up_day` (golden cross), `macd_dw_day` (death cross), `macd_red_strong_day`, `macd_watter_day`
- **KDJ signals**: `kdj_oversold_day`, `kdj_golden_cross_day`, `kdj_overbought_reversal_day`
- **CCI signals**: `cci_oversold_day`, `cci_overbought_day`, `cci_7_oversold_day`
- **Bollinger Band**: `boll_squeeze_day`, `boll_break_upper_day`, `boll_position_day_cls`
- **Momentum/ROC**: `momentum_3_day`, `momentum_5_day`, `roc_3_day`, `roc_5_day`, `roc_decay_day`
- **FVG gaps**: `is_up_fvg_day` (bullish), `is_dw_fvg_day` (bearish)
- **Fibonacci**: `is_fbnq_break_up_24/48/96_day`, `is_fbnq_break_down_24/48/96_day`
- **Volume**: `volume_ratio_day`, `vol_ma5_day`, `volume_decline_day`

### 3.4 Derived Indicators (4 features)

| Feature | Chinese Label | Description |
|---|---|---|
| `close` | 收盘价 | Daily close price |
| `body_ratio` | 实体比 | Candle body / full range ratio |
| `lower_shadow_ratio` | 下影线比 | Lower shadow / full range ratio |
| `volume_ratio_ma5_ma20` | 量比MA5/MA20 | 5-day avg volume / 20-day avg volume |

### 3.5 Z-score Features (39 total)

Z-score features measure how far current values deviate from their rolling mean, computed over 10/20/60-day windows. Values are clipped to [-3, 3].

**Z-score Technical Features (21)**: 7 base features x 3 windows

| Base Feature | Windows | Chinese Label Pattern |
|---|---|---|
| `vol_ma5_60m_last` | zscore_10, zscore_20, zscore_60 | 成交量MA5(60m尾盘) Z-{N} |
| `vol_ma5_60m_mstart` | zscore_10, zscore_20, zscore_60 | 成交量MA5(60m早盘) Z-{N} |
| `vol_ma5_60m_astart` | zscore_10, zscore_20, zscore_60 | 成交量MA5(60m午盘) Z-{N} |
| `obv_acceleration_last` | zscore_10, zscore_20, zscore_60 | OBV加速度 Z-{N} |
| `obv_day` | zscore_10, zscore_20, zscore_60 | OBV(日线) Z-{N} |
| `vol_ma5_day` | zscore_10, zscore_20, zscore_60 | 成交量MA5(日线) Z-{N} |
| `daily_volume` | zscore_10, zscore_20, zscore_60 | 日成交量 Z-{N} |

**Z-score Money Flow Features (18)**: 6 base features x 3 windows

| Base Feature | Windows | Chinese Label Pattern |
|---|---|---|
| `mf_ma5` | zscore_10, zscore_20, zscore_60 | 主力净流入MA5 Z-{N} |
| `mf_ma10` | zscore_10, zscore_20, zscore_60 | 主力净流入MA10 Z-{N} |
| `mf_ma20` | zscore_10, zscore_20, zscore_60 | 主力净流入MA20 Z-{N} |
| `mfmo` | zscore_10, zscore_20, zscore_60 | 资金流动量 Z-{N} |
| `mfmo_signal` | zscore_10, zscore_20, zscore_60 | 资金流动量信号 Z-{N} |
| `mfmo_hist` | zscore_10, zscore_20, zscore_60 | 资金流动量柱 Z-{N} |

---

## 4. K-Line Candlestick Patterns (18 patterns)

Returned by `POST /features/k-patterns` and included in `ohlcv_combined`.

| Feature | Chinese Name | Signal | Description |
|---|---|---|---|
| `pattern_morning_star` | 早晨之星 | +1 | Bullish reversal: large bearish + small body + large bullish candle |
| `pattern_evening_star` | 黄昏之星 | -1 | Bearish reversal: large bullish + small body + large bearish candle |
| `pattern_hammer` | 锤头 | +1 | Bullish reversal at downtrend end: small body + long lower shadow |
| `pattern_hanging_man` | 吊颈 | +1 | Reversal bullish at uptrend end: small body + long lower shadow |
| `pattern_bullish_engulf` | 阳包阴 | +1 | Bullish: bearish followed by bullish candle that fully engulfs it |
| `pattern_bearish_engulf` | 阴包阳 | -1 | Bearish: bullish followed by bearish candle that fully engulfs it |
| `pattern_bullish_harami` | 看涨孕线 | +1 | Bullish: large bearish then small candle inside its body |
| `pattern_bearish_harami` | 看跌孕线 | -1 | Bearish: large bullish then small candle inside its body |
| `pattern_piercing` | 刺透 | +1 | Bullish: large bearish then bullish opens lower but closes above midpoint |
| `pattern_dark_cloud` | 乌云盖顶 | -1 | Bearish: large bullish then bearish opens higher but closes below midpoint |
| `pattern_three_soldiers` | 红三兵 | +1 | Three bullish candles, each opening inside prior body, closing higher |
| `pattern_three_crows` | 黑三鸦 | -1 | Three bearish candles, each opening inside prior body, closing lower |
| `pattern_rising_three` | 上升三法 | +1 | Bullish continuation: large bullish + 3 small bearish + bullish breakout |
| `pattern_falling_three` | 下降三法 | -1 | Bearish continuation: large bearish + 3 small bullish + bearish breakout |
| `pattern_side_by_side_bull` | 并列阳线 | +1 | Two adjacent bullish candles, similar opens, new high close |
| `pattern_side_by_side_bear` | 并列阴线 | -1 | Two adjacent bearish candles, similar opens, new low close |
| `pattern_bull_count` | 看涨信号数 | count | Number of bullish patterns detected |
| `pattern_bear_count` | 看跌信号数 | count | Number of bearish patterns detected |

---

## 5. Lifeline Indicators (7 indicators)

Returned by `POST /features/lifeline` and included in `ohlcv_combined`.

| Feature | Chinese Name | Values | Description |
|---|---|---|---|
| `red_band` | 红带 | 0/1 | Bullish operable zone: close > MA55 and DIF > 0 |
| `blue_band` | 蓝带 | 0/1 | Bearish no-trade zone: close < MA55 or DIF < 0 |
| `three_color_ball` | 三色球 | 0/1 | Buy signal: bullish candle + volume surge + above MA5 |
| `absolute_buy_point` | 绝对买点 | 0/1 | Safest entry: first three-color ball after blue-to-red transition |
| `relative_buy_point` | 相对买点 | 0/1 | Regular three-color ball within red band (not the first) |
| `suicide_buy_warning` | 自杀买点警告 | 0/1 | Dangerous: three-color ball within blue band (high crash probability) |
| `daily_sell_signal` | 日线卖出信号 | 0/1 | Sell signal: gain >= 20% while in red band |
| `blue_band_sell_signal` | 蓝带卖出信号 | 0/1 | Permanent exit signal: stock enters blue band |

> **Note**: The API returns 7 indicators, but the underlying system computes additional lifeline-related features (gourd patterns, sector three-color ball, etc.) that are accessible via the full OHLCV combined endpoint.

### Extended Lifeline System (via ohlcv_combined)

The full OHLCV pipeline also computes:

- **Golden Gourd (金葫芦)**: `gourd_body`, `gourd_head`, `golden_gourd_bull`, `golden_gourd_bear` -- Identifies major volume base (gourd body) followed by shrinking-volume breakout (gourd head)
- **Double Gourd Bull (双葫大牛)**: `double_gourd_bull` -- Bear gourd base followed by bull gourd breakout (multi-bagger structure)
- **Golden Gourd Buy Point**: `golden_gourd_buy_point` -- Gourd head + pullback to red band upper edge + three-color ball
- **Sector Three-Color Ball**: `sector_three_color_ball`, `industry_three_color_ball`, `both_three_color_ball`, `only_stock_three_color_ball`, `only_sector_three_color_ball`

---

## 6. Experience Features (4 features)

Returned by `POST /features/experience` and included in `ohlcv_combined`.

| Feature | Chinese Name | Description |
|---|---|---|
| `current_price` | 当前价格 | Latest trade price |
| `price_change` | 涨跌幅 | Price change percentage |
| `volume_trend` | 成交量趋势 | Volume trend indicator |

> **Note**: The full OHLCV pipeline computes 130+ additional experience signals (Fisher Transform, Calmar Ratio, Guppy countdown, Alligator, Keltner Channel, OBV divergence, TD9 sequence, Fibonacci reversal, cup-and-handle, island reversal, etc.). These are available in the `ohlcv_combined` response but are not individually listed in the experience feature group.

---

## 7. Money Flow - Minute Level (16 features)

Returned by `POST /flow_features/minute` and included in `POST /flow_features/combined`.

| Feature | Chinese Name | Description | Signal Interpretation |
|---|---|---|---|
| `morning_30m_net` | 早盘30分净流入 | Cumulative net inflow in first 30 minutes | Positive = morning capital inflow |
| `morning_30m_r0` | 早盘30分主力占比 | Average super-large order ratio in first 30 min | Higher = stronger institutional morning activity |
| `closing_30m_net` | 尾盘30分净流入 | Net inflow increment in last 30 minutes | Positive = late-day capital entry |
| `closing_30m_surge` | 尾盘资金突增 | Tail session avg volume vs full-day avg volume ratio - 1 | >0 = late-day volume surge |
| `intraday_accumulate` | 日内吸筹 | Price range <2% + net inflow >0 | 1 = accumulation detected (sideways + inflow) |
| `intraday_distribute` | 日内出货 | Price change <1% + net outflow | 1 = distribution detected (stagnant + outflow) |
| `flow_turn_positive_pct` | 资金流转正时刻 | Position ratio when cumulative flow turns positive | 0 = positive from open; -1 = never turned positive; lower = stronger |
| `sell_exhaust` | 卖单衰竭 | Second-half selling exhaustion signal | 1 = selling volume declining + net flow improving |
| `mf_intraday_std` | 日内资金流波动率 | Standard deviation of per-minute net flow delta | Higher = more volatile intraday flow |
| `r0_r3_spread` | 主力散户占比差 | Mean(super-large order ratio) - Mean(small order ratio) | Positive = institutional dominance |
| `wash_trade_ratio` | 对倒比例 | Fraction of minutes suspected as wash trades | >0.2 suggests possible wash trading |
| `wash_trade_signal` | 对倒信号 | Binary: 1 if wash_trade_ratio > 0.2 | 1 = suspicious wash trading activity |
| `vol_skew` | 量能偏度 | Skewness of intraday minute volume distribution | Positive = late-day rush; Negative = early-day exit |
| `vol_pulse_regularity` | 量能脉冲规律性 | Inverse of pulse interval std (programmatic order detection) | Higher = more regular (likely algorithmic) |
| `price_mf_efficiency` | 价格资金效率 | (Close - Open) / total flow amount | Higher = capital more efficiently moves price |
| `intraday_drawdown_ctrl` | 日内回撤控制 | (1 - max drawdown) * sign(net flow) | Higher = stronger control by main force |

---

## 8. Money Flow - Daily Level (64 features)

Returned by `POST /flow_features/daily` and included in `POST /flow_features/combined`.

### 8.1 Strength and Trend (10 features)

| Feature | Chinese Name | Description |
|---|---|---|
| `mfs` | MFS资金流强度 | (Main net inflow - Retail net inflow) / Volume |
| `mfs_ma5` | MFS MA5 | 5-day moving average of MFS |
| `mfs_ma10` | MFS MA10 | 10-day moving average of MFS |
| `mf_ma5` | 主力净流入MA5 | 5-day MA of main force net inflow |
| `mf_ma10` | 主力净流入MA10 | 10-day MA of main force net inflow |
| `mf_ma20` | 主力净流入MA20 | 20-day MA of main force net inflow |
| `mf_ma_cross` | 资金流金叉死叉 | +1 = MA5 > MA20 (golden cross), -1 = death cross |
| `mf_bias` | 资金流乖离率 | (Current net - MA20) / |MA20| |
| `mfd` | 资金流背离 | (Price change rate - Main flow change rate) / Price change rate |
| `mfd_ma5` | 资金流背离MA5 | 5-day MA of MFD |

### 8.2 Distribution and Concentration (6 features)

| Feature | Chinese Name | Description |
|---|---|---|
| `inst_share` | 主力资金占比 | Main net inflow / (|Main| + |Retail|) |
| `inst_share_ma5` | 主力占比MA5 | 5-day MA of institutional share |
| `mfc_10` | 资金集中度10日 | 1 / std(10-day main net inflow), normalized [0,1] |
| `mfc_20` | 资金集中度20日 | 1 / std(20-day main net inflow), normalized [0,1] |
| `bofi` | 大单资金流强度 | Super-large net inflow / (super-large in + out) |
| `bofi_ma5` | 大单强度MA5 | 5-day MA of BOFI |

### 8.3 Composite Score and Anomaly (3 features)

| Feature | Chinese Name | Description |
|---|---|---|
| `mfcs` | 资金流综合评分 | 0.5*z(main) + 0.3*z(super-large) - 0.2*z(retail) |
| `mfcs_ma5` | 综合评分MA5 | 5-day MA of MFCS |
| `mfai` | 资金流异常指数 | Z-score of current main net inflow vs 20-day window |

### 8.4 Momentum and Oscillator (7 features)

| Feature | Chinese Name | Description |
|---|---|---|
| `mfmo` | 资金流动量 | EMA(12) - EMA(26) of main net inflow (MACD-like) |
| `mfmo_signal` | 资金流动量信号 | 9-day EMA of MFMO |
| `mfmo_hist` | 资金流动量柱 | MFMO - MFMO signal |
| `mfi_14` | MFI 14日 | Money Flow Index (14-period) |
| `mfi_9` | MFI 9日 | Money Flow Index (9-period) |
| `mfi_overbought` | MFI超买 | 1 if MFI_14 > 80 |
| `mfi_oversold` | MFI超卖 | 1 if MFI_14 < 20 |

### 8.5 MFI x Large-Order Cross-Validation (5 features)

| Feature | Chinese Name | Description |
|---|---|---|
| `mfi_trap_bull` | 诱多信号 | MFI > 50 but super-large net outflow (fake breakout) |
| `mfi_wash_buy` | 洗盘吸筹信号 | MFI < 50 but super-large net inflow (panic absorption) |
| `mfi_confirmed_bull` | 量价资金共振确认(多) | MFI > 50 and super-large net inflow (confirmed bullish) |
| `mfi_confirmed_bear` | 量价资金共振确认(空) | MFI < 50 and super-large net outflow (confirmed bearish) |
| `mfi_verify_score` | 综合验证得分 | confirmed_bull - confirmed_bear - trap_bull + wash_buy |

### 8.6 Sliding Window Resonance (3 features)

| Feature | Chinese Name | Description |
|---|---|---|
| `wash_end_confirm` | 洗盘结束确认 | MFI bottom divergence + 3-day large-order inflow + declining retail |
| `mfi_cumsum_bull` | MFI累计净流入看多 | MFI crosses above 50 + 5-day cumulative inflow > 0 |
| `mfi_cumsum_bear` | MFI累计净流入看空 | MFI crosses below 50 + 5-day cumulative outflow < 0 |

### 8.7 Consensus, Position, and Dynamic Threshold (5 features)

| Feature | Chinese Name | Description |
|---|---|---|
| `flow_consensus` | 资金流向一致性 | +2: strong accumulation, +1: possible wash, -1: main exit, -2: full sell-off |
| `price_position` | 价格位置 | Close position in 20-day range [0,1] |
| `low_accumulate` | 低位吸筹 | Price position < 0.3 + main inflow + price change < 1% |
| `high_distribute` | 高位出货 | Price position > 0.7 + main outflow + price stagnant |
| `mf_surge` | 资金流突增 | Main inflow > mean + 2*std and volume > 1.5x 5-day avg |

### 8.8 Quality and Time-Series Dependence (4 features)

| Feature | Chinese Name | Description |
|---|---|---|
| `mf_entropy` | 资金流方向熵 | 20-day directional entropy (0 = unidirectional, 1 = alternating) |
| `big_small_diverge` | 大小单分歧 | (Super-large net - Retail net) / Volume |
| `mf_autocorr` | 资金流自相关 | Lag-1 autocorrelation of main net inflow (20-day rolling) |
| `mf_persist_prob` | 资金流持续概率 | P(today positive | yesterday positive), 20-day window |

### 8.9 Auxiliary (3 features)

| Feature | Chinese Name | Description |
|---|---|---|
| `mf_streak` | 连续净流入天数 | Consecutive days of net inflow (+) or outflow (-) |
| `mf_price_corr_10` | 资金流价格相关10日 | 10-day rolling correlation of close and main net inflow |
| `mf_price_corr_20` | 资金流价格相关20日 | 20-day rolling correlation |

### 8.10 Money Flow Z-score Features (18 features)

6 base features x 3 windows (10/20/60 day), clipped to [-3, 3]:

| Base Feature | Z-score Windows |
|---|---|
| `mf_ma5` | zscore_10, zscore_20, zscore_60 |
| `mf_ma10` | zscore_10, zscore_20, zscore_60 |
| `mf_ma20` | zscore_10, zscore_20, zscore_60 |
| `mfmo` | zscore_10, zscore_20, zscore_60 |
| `mfmo_signal` | zscore_10, zscore_20, zscore_60 |
| `mfmo_hist` | zscore_10, zscore_20, zscore_60 |

---

## 9. Hot Sector Strategy

The `hot_sector` group is **not a feature group** but a standalone strategy. It discovers market hotspots by tracking limit-up stocks and identifying follower candidates.

### Strategy Pipeline

```
Limit-up Detection --> Co-movement Lookup --> 15m Technical Filter --> Scoring Top10 --> LLM Select Top3
```

**Step 1 - Limit-up Detection**: Automatically detect today's limit-up stocks (or accept manual list via `limitup_codes`)

**Step 2 - Co-movement Lookup**: Query historical co-movement data to find follower stocks that historically moved in sync with the limit-up stocks

**Step 3 - Technical Filter**: Apply 15-minute technical screening to filter out technically weak candidates

**Step 4 - Scoring**: Rank remaining candidates by composite score considering:
- Day percentage change (`day_pct`)
- Volume ratio (`vol_ratio`)
- Historical excess return correlation (`excess_rate_1d_w`)
- Turnover rate
- Market environment factor (bull/bear adjustment)

**Step 5 - LLM Selection**: LLM compares Top10 candidates and selects Top3 with detailed reasoning report

### Market Environment Detection

The strategy automatically detects bull/bear conditions and adjusts the `env_factor`:
- **Bull market** (env_factor = 1.0): More aggressive follower selection
- **Bear market** (env_factor = 0.5): Stricter filtering requirements

### Response Fields

| Field | Description |
|---|---|
| `limitup_count` | Number of limit-up stocks detected |
| `candidate_count` | Total follower candidates from co-movement |
| `filtered_count` | Candidates passing technical filter |
| `top10_scored` | Top 10 candidates with scores |
| `top3_selected` | LLM-selected Top 3 with bullish_index |
| `llm_report` | Detailed LLM analysis report |
| `market_env` | Bull/bear detection result |

---

## 10. Feature Response Format

All feature API endpoints return features in a structured format with Chinese labels.

### Standard Feature Format (features/* endpoints)

```json
{
  "pattern_morning_star": {
    "value": 1,
    "label": "早晨之星",
    "description": "下跌趋势末端，大阴+小实体+大阳，看涨反转"
  },
  "red_band": {
    "value": 1,
    "label": "红带",
    "description": "多头可操作区域：收盘价>MA55且DIF>0"
  },
  "rsi_14_60m_last": {
    "value": 45.2,
    "label": "RSI-14(60m尾盘)",
    "description": "60m 14周期RSI"
  }
}
```

### Money Flow Feature Format (flow_features/* endpoints)

```json
{
  "morning_30m_net": {
    "value": 14154022.82,
    "description": "早盘30分钟累积净流入...",
    "signal": "：早盘资金积极流入"
  },
  "mfs": {
    "value": 0.16,
    "description": "MFS资金流强度...",
    "signal": ""
  }
}
```

### Money Flow Raw Data Format (flow/* endpoints)

```json
{
  "netamount": {
    "value": 515975417.62,
    "label": "主力净流入",
    "description": "超大单+大单净流入金额",
    "unit": "元"
  }
}
```

### Full Feature Response Structure (POST /features/all)

```json
{
  "stock_codes": ["000001"],
  "market": "sz",
  "results": {
    "000001": {
      "status": "success",
      "technical": {
        "close_60m_zscore": {"value": 0.85, "label": "...", "description": "..."},
        "...": "(212 technical features)"
      },
      "ohlcv_combined": {
        "k_patterns": {
          "pattern_morning_star": {"value": 1, "label": "早晨之星", "description": "..."},
          "...": "(18 K-line patterns)"
        },
        "lifeline": {
          "red_band": {"value": 1, "label": "红带", "description": "..."},
          "...": "(7 lifeline indicators)"
        },
        "experience": {
          "current_price": {"value": 12.5, "label": "当前价格", "description": "..."},
          "...": "(4 experience features)"
        }
      },
      "flow_daily": {
        "mfs": {"value": 0.16, "label": "MFS资金流强度", "description": "..."},
        "...": "(64 daily money flow features)"
      },
      "flow_minute": {
        "morning_30m_net": {"value": 14154022.82, "label": "早盘30分净流入", "description": "..."},
        "...": "(16 minute money flow features)"
      }
    }
  },
  "summary": {"total": 1, "success": 1}
}
```

---

## Appendix: Knowledge Base Rules

The system includes a **Feature Rules Knowledge Base** (4.4MB) containing backtested high-confidence feature combination rules for bullish/bearish prediction. This knowledge base is used by the LLM engine for report generation and includes:

- **Bullish rules**: Feature combinations predicting 1d/3d/5d price gains (verified win rates 65-80%)
- **Bearish rules**: Feature combinations predicting downside risks
- **Rule metadata**: Training/validation win rates, decay metrics, daily occurrence counts, average returns
- **Machine-readable conditions**: e.g., `cs_uptrend_warning == 2 AND enh_is_up_fvg_day == 1`

Rules are derived from historical backtesting with 70/30 train/validation split and include win-rate decay tracking to filter out overfitted patterns.
