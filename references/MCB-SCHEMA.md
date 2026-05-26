# Macro Context Base (MCB) — Schema 定义

> 宏观语境基座：为数字炼化提供跨项目通用的时代坐标系。

## 设计目标

1. **标准化**：所有炼化项目使用同一套宏观语境数据，确保跨人格可比性
2. **结构化**：每个字段有明确的取值类型和来源，杜绝自由文本作文
3. **可引用**：Phase 2 语境标注通过 `mcb_ref` 字段引用 MCB 月度 ID，避免重复手写宏观环境

## 覆盖范围

- **时间范围**：2005-01 至今（按需扩展）
- **时间粒度**：月度（YYYY-MM）
- **市场覆盖**：中国（CN）、美国（US）

## JSONL 文件格式

每行一条 JSON 记录，代表一个月的一个市场。

文件命名：`MCB_CN.jsonl`、`MCB_US.jsonl`

## 字段定义

### 中国市场 (MCB_CN.jsonl)

```jsonc
{
  // === 标识字段 ===
  "id": "CN-2015-06",                    // 唯一标识：{market}-{YYYY}-{MM}
  "market": "CN",
  "year": 2015,
  "month": 6,

  // === Layer 1: 宏观经济层 ===
  "macro": {
    "gdp_yoy": 7.0,                      // GDP 累计同比(%)，季度数据，非季月填 null
    "cpi_yoy": 1.4,                       // CPI 当月同比(%)
    "m2_yoy": 11.8,                       // M2 同比增速(%)
    "treasury_10y": 3.59,                 // 10Y 国债到期收益率(%)，月末值
    "usdcny": 6.1136                      // 美元兑人民币中间价，月末值
  },

  // === Layer 2: 股市层 ===
  "equity": {
    "sh_composite": {                     // 上证综指
      "close": 4277.22,                   // 月末收盘价
      "high": 5178.19,                    // 月最高价
      "low": 3847.88,                     // 月最低价
      "amplitude_pct": 25.72              // 月振幅(%) = (high-low)/上月close*100
    },
    "chinext": {                          // 创业板指（2010-06 起，之前为 null）
      "close": 3982.25,
      "high": 4037.96,
      "low": 2927.16,
      "amplitude_pct": null
    },
    "pe_ttm_median": 28.5,               // 全 A 股 PE(TTM) 中位数，月末值
    "pb_median": 3.2,                     // 全 A 股 PB 中位数，月末值
    "pe_percentile": 92,                  // PE 在 2005 年以来的历史百分位(0-100)，可选，无数据填 null
    "pb_percentile": 88,                  // PB 在 2005 年以来的历史百分位(0-100)，可选
    "margin_balance_bn": 2268.0,          // 两融余额(亿元)，月末值（2010-03 起）
    "financing_buy_bn": null,             // 融资买入额(亿元)，月度合计
    "avg_daily_turnover_bn": 1980.0,      // 日均成交额(亿元)
    "new_investors_k": 1642               // 当月新开户投资者数(千人)，月度（2007 起）
  },

  // === Layer 3: 行业周期层 ===
  "industry": {
    "top3": [                             // 申万一级行业月涨幅 Top3
      {"name": "计算机", "return_pct": 18.5},
      {"name": "传媒", "return_pct": 15.2},
      {"name": "电子", "return_pct": 12.8}
    ],
    "bottom3": [                          // 申万一级行业月涨幅 Bottom3
      {"name": "银行", "return_pct": -5.2},
      {"name": "采掘", "return_pct": -4.8},
      {"name": "钢铁", "return_pct": -3.5}
    ]
  },

  // === Layer 4: 政策事件层 ===
  "policy_events": [                      // 当月发生的重大政策事件，无则为空数组
    {
      "date": "2015-06-12",
      "category": "regulatory",           // monetary | fiscal | regulatory | market_structure
      "event": "证监会要求清理场外配资",
      "impact": "negative"                // positive | negative | neutral | mixed
    }
  ],

  // === Layer 5: 市场情绪层 ===
  "sentiment": {
    "equity_bond_spread": 3.15,           // 股债收益差 = 1/PE(全A) - 10Y国债收益率(%)
    "northbound_net_bn": -28.5,           // 北向资金当月净买入(亿元)，2014-11 起
    "margin_sentiment": "overheated",     // 两融情绪定性标签：frozen | cold | neutral | warm | hot | overheated
    "notable_events": [                   // 重大破发/破净/极端事件，无则空数组
      "千股跌停首现"
    ]
  },

  // === Layer 6: 叙事层 ===
  "narrative": "5178见顶，配资清查引发踩踏",  // ≤30字市场主线
  "keywords": ["杠杆牛", "配资清查", "千股跌停"]
}
```

### 美国市场 (MCB_US.jsonl)

```jsonc
{
  "id": "US-2008-09",
  "market": "US",
  "year": 2008,
  "month": 9,

  // === Layer 1: 宏观经济层 ===
  "macro": {
    "gdp_yoy": -2.8,                     // GDP 同比(%)，季度数据，非季月填 null
    "cpi_yoy": 4.9,                       // CPI 同比(%)
    "m2_yoy": 8.1,                        // M2 同比增速(%)
    "treasury_10y": 3.83,                 // 10Y US Treasury Yield(%)，月末值
    "dxy": 78.95                          // 美元指数(DXY)，月末值
  },

  // === Layer 2: 股市层 ===
  "equity": {
    "sp500": {                            // S&P 500
      "close": 1166.36,
      "high": 1305.32,
      "low": 1106.42,
      "amplitude_pct": 15.33
    },
    "nasdaq": {                           // NASDAQ Composite
      "close": 2091.88,
      "high": 2318.44,
      "low": 1983.73,
      "amplitude_pct": 15.23
    },
    "pe_sp500": 18.5,                     // S&P 500 PE(TTM)，月末值
    "cape_shiller": 20.1,                 // Shiller CAPE，月末值（可选）
    "vix_close": 46.72,                   // VIX 月末收盘价
    "avg_daily_volume_bn_usd": null       // 日均成交额(十亿美元)
  },

  // === Layer 3: 行业周期层 ===
  "industry": {
    "top3": [                             // S&P 500 GICS 一级行业月涨幅 Top3
      {"name": "Utilities", "return_pct": 1.2},
      {"name": "Consumer Staples", "return_pct": -2.1},
      {"name": "Healthcare", "return_pct": -3.5}
    ],
    "bottom3": [
      {"name": "Financials", "return_pct": -22.8},
      {"name": "Energy", "return_pct": -18.5},
      {"name": "Materials", "return_pct": -15.2}
    ]
  },

  // === Layer 4: 政策事件层 ===
  "policy_events": [
    {
      "date": "2008-09-15",
      "category": "regulatory",
      "event": "Lehman Brothers files for bankruptcy",
      "impact": "negative"
    },
    {
      "date": "2008-09-16",
      "category": "monetary",
      "event": "Fed bails out AIG with $85B loan",
      "impact": "mixed"
    }
  ],

  // === Layer 5: 市场情绪层 ===
  "sentiment": {
    "equity_bond_spread": null,
    "ted_spread": 3.14,                   // TED Spread(%)，美国特有，衡量信用恐慌
    "vix_avg": 35.8,                      // VIX 月均值
    "notable_events": [
      "Lehman bankruptcy triggers global panic",
      "Money market fund breaks the buck"
    ]
  },

  // === Layer 6: 叙事层 ===
  "narrative": "Lehman collapses, global financial panic",
  "keywords": ["Lehman", "AIG bailout", "credit freeze"]
}
```

## 数据来源优先级

| 层级 | 中国数据源 | 美国数据源 |
|------|-----------|-----------|
| 宏观经济层 | Wind 终端 > 国家统计局 > CEIC | Wind 终端 > FRED > BLS |
| 股市层 | Wind 终端 > 东方财富 > CSMAR | Wind 终端 > Yahoo Finance > FRED |
| 行业周期层 | Wind（申万行业指数） | Wind > S&P Global |
| 政策事件层 | Web search + 央行公告 | Web search + Fed公告 |
| 市场情绪层 | Wind + 计算（股债收益差） | FRED(TED/VIX) + 计算 |
| 叙事层 | LLM + Web search 综合 | LLM + Web search 综合 |

## 数据质量标注

每个字段如果数据不可得，填 `null`，不填 0 或估计值。

在 MCB 的元信息文件 `MCB_META.json` 中记录每个层级的数据完整度：

```json
{
  "market": "CN",
  "date_range": "2005-01 to 2025-12",
  "total_months": 252,
  "completeness": {
    "macro": {"complete": 240, "partial": 8, "missing": 4},
    "equity": {"complete": 230, "partial": 15, "missing": 7},
    "industry": {"complete": 220, "partial": 20, "missing": 12},
    "policy_events": {"complete": 252, "partial": 0, "missing": 0},
    "sentiment": {"complete": 150, "partial": 60, "missing": 42},
    "narrative": {"complete": 252, "partial": 0, "missing": 0}
  },
  "data_sources": ["Wind终端导出", "国家统计局", "Web search"]
}
```

## Phase 2 中的引用方式

Phase 2 语境标注通过 `mcb_ref` 引用 MCB：

```json
{
  "statement_id": "S001",
  "statement": "现在应该满仓干",
  "date": "2015-06-05",
  "mcb_ref": "CN-2015-06",               // string | string[]，引用 MCB 月度切片 ID
  "personal_context": "刚从实地调研回来，对创业板科技股信心爆棚",  // ≤50字个人体感
  "context_dependency": "high"
}
```

**mcb_ref 类型说明**：通常为单个字符串（如 `"CN-2015-06"`）。当言论横跨多月时，使用字符串数组（如 `["CN-2015-05", "CN-2015-06"]`）。

这样语境标注从"作文"变成了"结构化引用 + 简短补充"，跨炼化可复用、可比较。
