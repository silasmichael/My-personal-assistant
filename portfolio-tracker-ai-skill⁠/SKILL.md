# Skill Name: Supabase Portfolio Tracker
Description: Reads and updates stock and fund portfolio data on Supabase row id=1.

## Security Credentials
- SB_KEY: Paste your Supabase key here
- JWT_TOKEN: Paste your user login token here

## Database Connection
- Base API URL: https://brwkhnqnsoormvpjqcmd.supabase.co/rest/v1
- Select Query: /portfolio?id=eq.1&select=stocks,funds,snapshots
- Update Query: PATCH /portfolio?id=eq.1

## Strict Rules for the AI
1. Always read data with the Select Query first before changing anything.
2. Always write back data using PATCH on id=1. Never insert or upsert.
3. Keep _snapV as 3. Never clear price dates or monthly tracking.
4. Tranche dates must use "DD Mon YYYY" format (e.g., 15 Jan 2025).
5. Change the updated_at timestamp to the current time whenever you save.
