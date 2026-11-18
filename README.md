# U.S. residential electricity rates and bills by state

Average residential electricity price, monthly use and monthly bill for all 50 states, D.C. and the U.S. as a whole, from January 2001 to the latest month published by the U.S. Energy Information Administration (EIA).

The files are refreshed twice a month, a few days after EIA publishes a new month.

Maintained by [Average Electric Bill](https://averageelectricbill.com), a free tool that checks whether an electric bill is normal for your state, home size and month.

## Files

| File | Rows | What it holds |
| --- | --- | --- |
| [`data/electricity-rates-by-state.csv`](data/electricity-rates-by-state.csv) | 52 | One row per state: the latest 12-month average rate, use and bill, with 1-year and 5-year price changes |
| [`data/electricity-monthly-by-state.csv`](data/electricity-monthly-by-state.csv) | about 16,000 | One row per state per month since 2001: price, use per customer, average bill, customers |

Raw links, for scripts:

```
https://raw.githubusercontent.com/lyudolf/us-electricity-rates/main/data/electricity-rates-by-state.csv
https://raw.githubusercontent.com/lyudolf/us-electricity-rates/main/data/electricity-monthly-by-state.csv
```

## Columns

### electricity-rates-by-state.csv

| Column | Meaning |
| --- | --- |
| `state`, `state_code` | State name and postal code. `US` is the national figure. |
| `avg_rate_cents_per_kwh` | Residential revenue ÷ kWh sold over the latest 12 months, in cents |
| `avg_monthly_kwh` | Average kWh per residential customer per month over the same 12 months |
| `avg_monthly_bill_usd` | Average monthly bill over the same 12 months, in dollars |
| `rate_change_1y_pct`, `rate_change_5y_pct` | Change in the 12-month average rate compared with one and five years earlier, in percent |
| `period_end` | Last month included (YYYY-MM) |
| `source` | Data source |

### electricity-monthly-by-state.csv

| Column | Meaning |
| --- | --- |
| `state`, `state_code` | State name and postal code. `US` is the national figure. |
| `period` | Month (YYYY-MM) |
| `price_cents_per_kwh` | Residential revenue ÷ kWh sold in that month, in cents |
| `kwh_per_customer` | kWh sold ÷ residential customers in that month |
| `avg_bill_usd` | Residential revenue ÷ residential customers in that month, in dollars |
| `customers` | Residential customer accounts |

## Things to know before you use it

- **Prices are what customers paid on average, not a tariff.** Revenue ÷ kWh includes fixed charges and the taxes utilities report as revenue, so it is higher than the per-kWh energy charge on a rate sheet.
- **Dollars are nominal.** Nothing is adjusted for inflation.
- **Customer counts start in January 2008.** Before that, `kwh_per_customer`, `avg_bill_usd` and `customers` are empty. Prices go back to 2001.
- **Recent months are preliminary.** EIA estimates the latest months from a sample of utilities (Form EIA-861M) and revises them when the annual census (Form EIA-861) is final.
- **A monthly bill follows the weather.** Compare a month with the same month in other years, or use the 12-month file.

## Example

```python
import pandas as pd

url = "https://raw.githubusercontent.com/lyudolf/us-electricity-rates/main/data/electricity-monthly-by-state.csv"
df = pd.read_csv(url, parse_dates=["period"])

# average winter (Dec-Feb) bill in Texas, by winter
tx = df[(df.state_code == "TX") & df.period.dt.month.isin([12, 1, 2])].copy()
tx["winter"] = tx.period.dt.year + (tx.period.dt.month == 12)
print(tx.groupby("winter").avg_bill_usd.mean().round(0).tail())
```

## The same data for AI assistants

- MCP server (Streamable HTTP, no key): `https://averageelectricbill.com/mcp`
  Tools: `check_electric_bill`, `get_state_electricity_stats`, `find_utility_rate`.
- JSON API (no key): [`https://averageelectricbill.com/openapi.json`](https://averageelectricbill.com/openapi.json)

## Source and method

All figures come from the EIA open data API, [electricity retail sales](https://www.eia.gov/opendata/browser/electricity/retail-sales), residential sector (Forms EIA-861M and EIA-861). How the numbers are built is described on the [methodology page](https://averageelectricbill.com/methodology).

Pages that use this data:

- [Average electric bill by state](https://averageelectricbill.com/electricity)
- [Winter electric bills by state](https://averageelectricbill.com/guides/winter-electric-bills-by-state)
- [Monthly electricity price reports](https://averageelectricbill.com/reports)
- [Is my electric bill normal?](https://averageelectricbill.com/)

## License and citation

The underlying EIA data is in the public domain. The compiled files in this repository are released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): use them for anything, with credit.

Suggested credit: *Average Electric Bill (averageelectricbill.com), from U.S. Energy Information Administration data.*

This project is not affiliated with EIA or any utility.
