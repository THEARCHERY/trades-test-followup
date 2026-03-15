# Trading Dashboard

Dashboard interactivo para seguimiento de operaciones de trading, construido con HTML/JS puro + Supabase como backend.

## Demo

El dashboard se sirve directamente desde `index.html` — ábrelo en el navegador o despliégalo en GitHub Pages / cualquier hosting estático.

## Funcionalidades

- **KPIs globales**: Win Rate, Profit Factor, Total Trades, Avg R-Multiple, Near Miss Rate
- **Equity Curve** con 4 líneas:
  - 🟠 Capital Invertido (acumulado desde `capital_invertido`)
  - 🔵 Valor del Portfolio (efectivo + posiciones abiertas en tiempo real)
  - 🟢 Efectivo / Presupuesto (cash disponible tras P&L realizado)
  - 🟣 Posiciones Abiertas (valor de mercado actual de trades OPEN)
  - Sombreado verde/rojo entre portfolio y capital; sombreado rojo cuando el efectivo cae a negativo
- **KPI cards de la curva**: Retorno total, Max Drawdown, Mejor mes, Peor mes
- **Filtros de tiempo**: 5D / 15D / 30D / 60D / All (KPIs se recalculan con el rango)
- **Gráficos de análisis**: distribución de operaciones, evolución del Win Rate, Win Rate por fuente, Score vs R-Multiple
- **Tabla de trades** con paginación, ordenación por columna y fila expandible con mini-chart por operación
- **Filtros**: por fuente, estado, fechas y score mínimo

## Tablas Supabase

### `trades`
| columna | tipo |
|---|---|
| id | uuid PK |
| ticker | text |
| source | text (CANSLIM / VCP / DIVIDEND / EARNINGS) |
| status | text (OPEN / GREEN / RED / EXPIRED) |
| entry_price | numeric |
| stop_loss | numeric |
| take_profit | numeric |
| close_price | numeric |
| invested_amount | numeric |
| r_multiple | numeric |
| score | numeric |
| notes | text |
| recommended_at | timestamptz |
| closed_at | timestamptz |

### `daily_checks`
| columna | tipo |
|---|---|
| id | uuid PK |
| trade_id | uuid FK → trades |
| checked_at | timestamptz |
| day_close | numeric |
| day_high | numeric |
| day_low | numeric |

### `portfolio_stats`
| columna | tipo |
|---|---|
| id | uuid PK |
| calculated_at | timestamptz |
| win_rate | numeric |

### `capital_invertido`
| columna | tipo |
|---|---|
| id | uuid PK |
| fecha | date |
| monto | numeric |
| notas | text |

## Configuración

Edita las dos constantes al inicio del `<script>` en `index.html`:

```js
const SUPABASE_URL      = 'tu-url';
const SUPABASE_ANON_KEY = 'tu-anon-key';
```

### Row Level Security (recomendado)

```sql
ALTER TABLE trades            ENABLE ROW LEVEL SECURITY;
ALTER TABLE daily_checks      ENABLE ROW LEVEL SECURITY;
ALTER TABLE portfolio_stats   ENABLE ROW LEVEL SECURITY;
ALTER TABLE capital_invertido ENABLE ROW LEVEL SECURITY;

CREATE POLICY "public_read" ON trades            FOR SELECT TO anon USING (true);
CREATE POLICY "public_read" ON daily_checks      FOR SELECT TO anon USING (true);
CREATE POLICY "public_read" ON portfolio_stats   FOR SELECT TO anon USING (true);
CREATE POLICY "public_read" ON capital_invertido FOR SELECT TO anon USING (true);
```

## Stack

- HTML / CSS / JavaScript (vanilla, sin frameworks)
- [Chart.js 4](https://www.chartjs.org/)
- [Supabase JS v2](https://supabase.com/docs/reference/javascript)
