# Mint CRM Admin Portal

## Overview
Internal admin dashboard for the Mint investment platform. Provides client management, KYC verification, investment strategy configuration, and order book reporting.

## Architecture
- **Backend**: Node.js with native `http` module (`server.js`)
- **Frontend**: Static HTML/JS with Tailwind CSS (served from `public/`)
- **Database**: Supabase (PostgreSQL)
- **Services**: Resend (email), Sumsub (KYC)
- **Deployment target**: Vercel (serverless functions in `api/`)

## Key Pages
- `/signin.html` - Admin login (Supabase Auth)
- `/index.html` - Client profiles / CRM
- `/dashboard.html` - Platform overview & stats
- `/strategies.html` - Strategy creation & management with holdings builder
- `/factsheet.html` - Strategy factsheet preview (mirrors Mint platform layout)
- `/orderbook.html` - Order book email runs

## Strategies Feature
- Strategies are created with holdings from the `securities` table
- **Auto-calculated fields**: target weight (based on market value), number of shares, minimum investment
- Strategies saved with `status: 'active'` to appear on the Mint client app's OpenStrategies page
- Holdings stored as JSON array: `[{symbol, ticker, name, shares, quantity, weight}]`
- Minimum investment = sum of (shares * last_price/100) for each holding
- Target weight = (holding_market_value / total_market_value) * 100
- Factsheet page shows: holdings with logos, daily change, performance summary, calendar returns, fees

## Database Tables Used
- `profiles` - Client profiles
- `strategies` - Investment strategies with holdings JSON column
- `strategy_analytics` - Performance analytics (summary, curves, calendar_returns)
- `strategy_metrics` - Daily metrics (last_close, change_pct, returns)
- `securities` - Securities with symbol, name, logo_url, last_price
- `stock_holdings` - Client stock holdings
- `orderbook_email_runs` - Email report history

## Environment Variables Required
- `SUPABASE_URL` / `SUPABASE_SERVICE_ROLE_KEY` - Database access
- `RESEND_API_KEY` - Email sending
- `SUMSUB_APP_TOKEN` / `SUMSUB_APP_SECRET` - KYC verification
- `ORDERBOOK_EMAIL_FROM` / `ORDERBOOK_EMAIL_TO` - Report emails
- `CRON_SECRET` - Cron endpoint protection
- `PORT` - Server port (set to 5000)

## Linked App
Connected to the Mint client-facing investment platform which shares the same Supabase backend. Strategies created here appear in Mint's OpenStrategies page when `status: 'active'` and `is_public: true`.
