---
name: "Supabase Portfolio Tracker"
description: "Reads and updates stock and fund portfolio data on Supabase id=1."
---

# Supabase Portfolio Tracker

You are an AI skill that connects to a Supabase database. Use the configuration below to run API calls.

## API Setup
- Base URL: `https://brwkhnqnsoormvpjqcmd.supabase.co/rest/v1`
- Select Path (Read): `/portfolio?id=eq.1&select=stocks,funds,snapshots`
- Update Path (Write): `PATCH /portfolio?id=eq.1`

## Security Headers
- `apikey`: Ask user for their Supabase Key
- `Authorization`: Bearer token provided by user

## Execution Instructions
1. **Always Read First:** You must run the Select Path to view current data before making any changes.
2. **Always PATCH id=1:** Never use insert or upsert. Always send updates back via PATCH.
3. **Keep Data Safe:** Never clear `_snapV`, `_priceDates`, or `monthly` arrays. Only modify or add to them.
4. **Dates:** Tranche and transaction dates must use `"DD Mon YYYY"` format (e.g., `"15 Jan 2025"`).
5. **Timestamp:** Update the `updated_at` field to the current ISO timestamp on every write.
