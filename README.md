# Trading Dashboard

A static, single-file trading dashboard built for GitHub Pages. Connects live to a Supabase backend to visualize and analyze an automated trading system.

**Live site:** [https://thearchery.github.io/trades-test-followup](https://thearchery.github.io/trades-test-followup)

---

## Features

- **KPI cards** — Win Rate, Profit Factor, Total Trades, Avg R-Multiple with color-coded thresholds
- **Interactive filters** — Filter by source, status, date range, and minimum score; applied client-side across all views
- **4 charts** — Status donut, win rate evolution, win rate by source, score vs R-multiple scatter
- **Full trades table** — Sortable columns, status badges, score progress bars, color-coded R-multiples
- **Expandable rows** — Click any trade to see its `daily_checks` history as a mini price chart with Entry / SL / TP reference lines
- **No build step** — Single `index.html` with CSS and JS inline; deploy directly to GitHub Pages

## Stack

| Layer | Technology |
|---|---|
| Backend | [Supabase](https://supabase.com) (PostgreSQL) |
| Charts | [Chart.js 4](https://www.chartjs.org) via CDN |
| Client | [Supabase JS v2](https://supabase.com/docs/reference/javascript) via CDN |
| Icons | [Lucide](https://lucide.dev) (inline SVG) |
| Hosting | GitHub Pages |

## Database Schema

### `trades`
| Column | Type | Description |
|---|---|---|
| `id` | uuid PK | Unique ID |
| `ticker` | text | Symbol (AAPL, NVDA…) |
| `source` | text | `CANSLIM` · `VCP` · `DIVIDEND` · `EARNINGS` |
| `score` | numeric | Composite screener score (0–100) |
| `entry_price` | numeric | Recommended entry price |
| `stop_loss` | numeric | Stop loss |
| `take_profit` | numeric | Take profit target |
| `status` | text | `OPEN` · `GREEN` · `RED` · `EXPIRED` |
| `recommended_at` | timestamptz | Recommendation timestamp |
| `closed_at` | timestamptz | Close timestamp (null if open) |
| `close_price` | numeric | Price at close |
| `r_multiple` | numeric | `(close − entry) / (entry − stop)` |
| `notes` | text | Additional notes |

### `daily_checks`
| Column | Type | Description |
|---|---|---|
| `id` | uuid PK | Unique ID |
| `trade_id` | uuid FK | Reference to `trades` |
| `checked_at` | timestamptz | Check timestamp |
| `day_high` | numeric | Day high |
| `day_low` | numeric | Day low |
| `day_close` | numeric | Day close |
| `hit_tp` | boolean | Take profit hit? |
| `hit_sl` | boolean | Stop loss hit? |

### `portfolio_stats`
| Column | Type | Description |
|---|---|---|
| `id` | uuid PK | Unique ID |
| `calculated_at` | timestamptz | Calculation timestamp |
| `total_trades` | int | Total trades |
| `open_trades` | int | Open trades |
| `green_trades` | int | Winning trades |
| `red_trades` | int | Losing trades |
| `win_rate` | numeric | Win percentage |
| `avg_r_multiple` | numeric | Average R-multiple |
| `profit_factor` | numeric | Gross profit / Gross loss |

## Supabase Row Level Security

Enable RLS and create SELECT-only policies for the `anon` role so the public key stays safe:

```sql
ALTER TABLE trades          ENABLE ROW LEVEL SECURITY;
ALTER TABLE daily_checks    ENABLE ROW LEVEL SECURITY;
ALTER TABLE portfolio_stats ENABLE ROW LEVEL SECURITY;

CREATE POLICY "public_read_trades"
  ON trades FOR SELECT TO anon USING (true);
CREATE POLICY "public_read_checks"
  ON daily_checks FOR SELECT TO anon USING (true);
CREATE POLICY "public_read_stats"
  ON portfolio_stats FOR SELECT TO anon USING (true);
```

## Deploy to GitHub Pages

1. Push this repo to GitHub
2. Go to **Settings → Pages → Source** and select your branch (`main`)
3. GitHub Pages will serve `index.html` at `https://thearchery.github.io/trades-test-followup`

## Configuration

The Supabase credentials are declared at the top of the `<script>` block in `index.html`:

```js
const SUPABASE_URL      = 'https://your-project.supabase.co';
const SUPABASE_ANON_KEY = 'your-anon-key';
```

---

Built with [Claude Code](https://claude.ai/claude-code) + Supabase
