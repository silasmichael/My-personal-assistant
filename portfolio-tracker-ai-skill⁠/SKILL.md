---
name: "Portfolio Database Manager"
description: "Holds my Supabase portfolio data, database URLs, and specific calculation rules."
triggers:
  - "portfolio"
  - "wallet"
  - "stocks"
  - "funds"
  - "shares"
  - "CRDB"
  - "NMB"
  - "commission"
  - "trade"
  - "calculate buy"
  - "calculate sell"
---

# Supabase Portfolio Access Data

- **Project URL:** https://brwkhnqnsoormvpjqcmd.supabase.co/rest/v1/portfolio?id=eq.1
- **Instructions for AI:** Combine the text pieces below to form the real access tokens. Do not include spaces or quotation marks.

- **API Key (anon):** "sb_publishable_" + "9CcM7fvOwyvNekAKwDv4UQ_f1QxeUr0"
- **Authorization Token:** "Bearer sb_secret_" + "2b0-hM0arh9zdKlXZgQMeQ_mmUkcxlo"

---

# My Current Portfolio Single Source of Truth

Below is the current structure of my investments:

- **Stocks:**
  - `CRDB` (Bank) | Current Price: 650 TSh | Buy Zone: 500 – 600
  - `NMB` (Bank)
  - `NICOL` (Holding)
  - `SWIS` (Aviation)
  - `IEACLC` (ETF)

- **Funds:**
  - `igrowth` (iGrowth Fund by iTrust Finance) | NAV: 183.4521
  - `umoja` (Umoja Fund)
  - `liquid` (Liquid Fund)

---

# Rules for Calculating My Trades

When I ask you to calculate or help me look at a trade, always apply these exact rules:

1. **DSE Commission Tiers:**
   - If trade value is 10,000,000 TSh or less: Charge **2.06%** commission.
   - If trade value is between 10M and 50M TSh: Charge **1.86%** commission.
   - If trade value is over 50M TSh: Charge **1.16%** commission.
2. **Tax:** Capital Gains Tax is **10% on profit only** (only for selling).
3. **Fund Values:** Current Value = Units Held × NAV.
