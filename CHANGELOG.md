# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.0] - 2026-05-14

### Added

- `/features/all` endpoint returning all feature categories in a single call
- Feature descriptions system: all feature APIs return `{value, label, description}` structured format with 300+ features covered
- Z-score features: 39 total (21 technical + 18 money flow), computed over multi-window periods (10/20/60)
- Usage tracking with `platform_tokens` billing and per-call LLM cost details
- Platform billing multipliers configurable in `config.yaml` (stock_report: 1.25, hot_sector: 1.8, default: 1.3, cache_hit_multiplier: 4.5)
- L1 cache per-item TTL differentiated by data category and trading vs. after-hours status
- Sentiment bar visualization (-10 to +10 scale) replacing numeric trend index
- MCP server additions: `get_usage_stats`
- Concurrency configuration unification with 5 worker settings, env var override support, and config.yaml integration

### Changed

- Report structure simplified: consolidated five standalone sections into summary lines under a unified diagnosis section, reducing LLM prompt tokens by ~40%
- Disclaimer moved from per-stock to top-level response field (appears once)
- L1 cache TTL from fixed 300s to per-item TTL by category and trading hours
- Feature API response format from raw values to `{value, label, description}` structured format
- `/flow/combined` fields from raw values to `{value, label, description, unit}` structured format
- Concurrency settings from hardcoded `max_workers` across 10 files to centralized config
- Candlestick feature cleanup: removed 7 redundant columns, bound slope signals to their flags

### Fixed

- numpy bool serialization in feature responses (auto-convert to Python native types)
- Outlier cleaning for `ma50_ma200_margin_ratio`, `ma5_ma20_margin_ratio`, `cci_decline_magnitude_day`, `rsi_decline_magnitude_day` (6,751 values corrected)

### Deprecated

- `config.yaml` `news.max_workers` setting (migrated to unified `concurrency.news_workers`)

### Uncategorized

- Query validation: 120 character limit enforced on query input
- Session optimization: auto-reset context on stock change, message pruning when exceeding 8 entries
- BuildKit image size reduced from 579MB to 62MB
