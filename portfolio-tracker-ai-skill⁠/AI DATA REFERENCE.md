# Portfolio Tracker — AI Data Reference

This document is the single source of truth for any AI agent reading or writing to the Supabase portfolio database.

-----

## Connection

```
URL:   https://brwkhnqnsoormvpjqcmd.supabase.co
Table: portfolio
Row:   id = 1  (always — there is only one row)
Auth:  Bearer JWT from active session
```

### Read

```http
GET /rest/v1/portfolio?id=eq.1&select=stocks,funds,snapshots
Authorization: Bearer <JWT>
apikey: <SB_KEY>
```

### Write

```http
PATCH /rest/v1/portfolio?id=eq.1
Authorization: Bearer <JWT>
apikey: <SB_KEY>
Content-Type: application/json

{ "stocks": [...], "funds": [...], "snapshots": {...}, "updated_at": "<ISO>" }
```

**Never use upsert. Always PATCH row id=1.**

-----

## Top-Level Shape

```json
{
  "stocks":    [ ...Stock ],
  "funds":     [ ...Fund ],
  "snapshots": { ...Snapshots }
}
```

-----

## Stock Object

```json
{
  "id":           "CRDB",
  "name":         "CRDB Bank",
  "type":         "bank",
  "color":        "#00C896",
  "currentPrice": 650,
  "buyZone":      "500 – 600",
  "signal":       "ACCUMULATE",
  "tranches": [
    {
      "type":       "buy",
      "date":       "15 Jan 2025",
      "shares":     1000,
      "price":      520,
      "commission": 10712
    },
    {
      "type":       "sell",
      "date":       "10 Mar 2025",
      "shares":     200,
      "price":      610,
      "commission": 2506,
      "profit":     16988
    }
  ],
  "fundamentals": {
    "navPerShare": 450,
    "raw": {
      "eps":          45.2,
      "bookValue":    450,
      "roe":          "18.2%",
      "roa":          "2.1%",
      "nim":          "7.4%",
      "npl":          "4.2%",
      "cir":          "52%",
      "divYield":     "4.5%",
      "fairValue":    700,
      "buyZoneLow":   420,
      "buyZoneHigh":  560,
      "avoidAbove":   630,
      "navPerShare":  450,
      "navDiscount":  "30.8%"
    }
  }
}
```

### Stock Types and Their Metrics

|type    |metrics used                                |
|--------|--------------------------------------------|
|bank    |P/E, P/B, ROE, ROA, NIM, NPL, CIR, Div Yield|
|holding |P/NAV, NAV Discount, ROE, D/E, Div Yield    |
|aviation|Altman Z, EV/EBITDA, D/E                    |
|etf     |Current NAV, P/NAV, vs Launch               |

### Signal Values

`STRONG BUY` `BUY` `ACCUMULATE` `HOLD & ADD` `STRONG HOLD` `HOLD` `WATCH` `SELL`

### Stock Tranche Fields

|field     |type  |notes                           |
|----------|------|--------------------------------|
|type      |string|`"buy"` or `"sell"`             |
|date      |string|`"15 Jan 2025"` format          |
|shares    |number|integer                         |
|price     |number|TSh per share                   |
|commission|number|TSh, calculated at DSE tiers    |
|profit    |number|sell only — net profit after WHT|

### DSE Commission Tiers

|trade value |rate |
|------------|-----|
|≤ 10,000,000|2.06%|
|10M – 50M   |1.86%|
|> 50M       |1.16%|

WHT on capital gains: **10% on profit only**

-----

## Fund Object

```json
{
  "id":          "igrowth",
  "name":        "iGrowth Fund",
  "manager":     "iTrust Finance",
  "color":       "#F4A623",
  "nav":         183.4521,
  "launchNav":   100,
  "launchDate":  "Jan 2020",
  "signal":      "ACCUMULATE",
  "purpose":     "Growth",
  "tranches": [
    {
      "type":        "buy",
      "date":        "10 Feb 2024",
      "units":       500,
      "nav":         165.2,
      "amount":      82600
    },
    {
      "type":        "sell",
      "date":        "5 May 2025",
      "units":       100,
      "nav":         180.1,
      "amount":      18010,
      "profit":      1490
    }
  ]
}
```

### Fund Tranche Fields

|field |type  |notes                     |
|------|------|--------------------------|
|type  |string|`"buy"` or `"sell"`       |
|date  |string|`"10 Feb 2024"` format    |
|units |number|fund units                |
|nav   |number|NAV at time of transaction|
|amount|number|TSh total                 |
|profit|number|sell only                 |

### Fund Calculations

```
current units  = sum(buy units) - sum(sell units)
current value  = current units × nav
total invested = sum(buy units × buy nav)
avg buy nav    = total invested / current units
gain/loss      = current value - total invested
gain %         = (current value / total invested - 1) × 100
```

-----

## Reserve Object

```json
{
  "id":    "rv_1",
  "name":  "M-Wekeza",
  "color": "#F59E0B",
  "rate":  13,
  "transactions": [
    {
      "type":   "deposit",
      "date":   "01 Jan 2025",
      "amount": 500000,
      "note":   "Initial deposit"
    },
    {
      "type":   "interest",
      "date":   "31 Jan 2025",
      "amount": 5417
    },
    {
      "type":   "withdraw",
      "date":   "15 Mar 2025",
      "amount": 100000,
      "note":   "Emergency"
    },
    {
      "type":      "buy_shares",
      "date":      "20 Apr 2025",
      "amount":    260000,
      "stockId":   "CRDB",
      "shares":    500,
      "price":     520,
      "commission": 10712,
      "note":      "500 shares @ 520"
    }
  ]
}
```

### Reserve Transaction Types

|type      |effect on balance|notes                |
|----------|-----------------|---------------------|
|deposit   |+amount          |cash in              |
|interest  |+amount          |earned interest      |
|withdraw  |-amount          |cash out             |
|buy_shares|-amount          |used to buy DSE stock|

### Reserve Balance Calculation

```
balance = sum(deposit + interest) - sum(withdraw + buy_shares)
```

-----

## Bond Object

```json
{
  "id":         "bond_1",
  "name":       "GOT 2027",
  "issuer":     "Government of Tanzania",
  "faceValue":  1000000,
  "unitsHeld":  5,
  "couponRate": 12.5,
  "maturity":   "2027",
  "purchaseDate": "01 Jun 2024"
}
```

Bonds are stored in `snapshots._bonds` as an array.

-----

## Dividend Object

```json
{
  "stockId":        "CRDB",
  "date":           "15 Jun 2025",
  "amountPerShare": 25,
  "shares":         1000,
  "total":          25000
}
```

Dividends are stored in `snapshots._dividends` as an array.

-----

## Snapshots Object

```json
{
  "_snapV":        3,
  "_priceDates":   { "CRDB": "2026-05-23T14:00:00.000Z", "NMB": "2026-05-23T14:00:00.000Z" },
  "_lastPriceTime": "2026-05-23T14:00:00.000Z",
  "_dividends":    [ ...Dividend ],
  "_reserves":     [ ...Reserve ],
  "_bonds":        [ ...Bond ],
  "monthly":       { "2025-01": 42000000, "2025-02": 45000000 },
  "projYear":      2028,
  "goals": {
    "2026": 38000000,
    "2027": 80000000
  },
  "plans": {
    "2026": { "Jan": 2000000, "Feb": 2000000, "Mar": 2000000, "Apr": 2000000, "May": 2000000, "Jun": 2000000, "Jul": 2000000, "Aug": 1000000, "Sep": 1000000, "Oct": 1000000, "Nov": 1000000, "Dec": 1000000 },
    "2027": { "Jan": 1000000, ... }
  }
}
```

### Snapshots Field Reference

|field           |description                                                            |
|----------------|-----------------------------------------------------------------------|
|`_snapV`        |Migration version. Currently 3. Never change.                          |
|`_priceDates`   |ISO timestamp of last price update per asset ID                        |
|`_lastPriceTime`|ISO timestamp of the last full price fetch run                         |
|`_dividends`    |Array of dividend records                                              |
|`_reserves`     |Array of reserve account objects                                       |
|`_bonds`        |Array of bond objects                                                  |
|`monthly`       |Monthly portfolio value snapshots. Key = `"YYYY-MM"`, value = TSh total|
|`projYear`      |Target projection end year (int)                                       |
|`goals`         |Year → TSh portfolio goal                                              |
|`plans`         |Year → month → TSh monthly investment plan                             |

-----

## Portfolio Value Calculation

```
total = stocks value + funds value + reserves value + bonds value

stocks value  = sum over stocks: remaining_shares × currentPrice
funds value   = sum over funds:  current_units × nav
reserves value = sum over reserves: balance(r)
bonds value   = sum over bonds: faceValue × unitsHeld
```

-----

## Critical Rules for AI Writes

1. **Always PATCH id=1. Never insert, never upsert.**
1. **Never overwrite `_snapV`** — leave it as 3.
1. **Never clear `_priceDates`** — only add or update keys.
1. **Never clear `monthly` snapshots** — only append new months.
1. **Dates in tranches and transactions** must use `"DD Mon YYYY"` format (e.g. `"15 Jan 2025"`), not ISO format.
1. **After any write**, update `updated_at` to current ISO timestamp.
1. **Read before write** — always fetch the current row first, merge changes, then PATCH. Never construct the full object from scratch.
1. **`_dataReady` guard** — the app won’t write to Supabase until it confirms data has loaded. If your AI writes while the app is open, the app may overwrite your changes on its next persist. Safe window: write when the app is closed or not syncing.

-----

## Existing Asset IDs

### Stocks

|id    |name              |type    |
|------|------------------|--------|
|CRDB  |CRDB Bank         |bank    |
|NMB   |NMB Bank          |bank    |
|NICOL |NICOL Holdings    |holding |
|SWIS  |Swissport Tanzania|aviation|
|IEACLC|IEACLC ETF        |etf     |

### Funds

|id     |name        |manager       |
|-------|------------|--------------|
|igrowth|iGrowth Fund|iTrust Finance|
|umoja  |Umoja Fund  |UTT AMIS      |
|liquid |Liquid Fund |UTT AMIS      |

-----

## Example: AI Updating a Stock Price

```json
// 1. Read current row
GET /rest/v1/portfolio?id=eq.1&select=stocks,funds,snapshots

// 2. Modify only what you need
stocks[0].currentPrice = 680;  // CRDB
snapshots._priceDates["CRDB"] = "2026-05-23T14:30:00.000Z";
snapshots._lastPriceTime = "2026-05-23T14:30:00.000Z";

// 3. Write back
PATCH /rest/v1/portfolio?id=eq.1
{ "stocks": [...modified], "snapshots": {...modified}, "updated_at": "2026-05-23T14:30:00.000Z" }
```

## Example: AI Adding a Buy Tranche

```json
// Find the stock, push to its tranches array
stocks.find(s => s.id === "NMB").tranches.push({
  "type":       "buy",
  "date":       "23 May 2026",
  "shares":     500,
  "price":      4200,
  "commission": 43260
});
// Then PATCH the full stocks array back
```