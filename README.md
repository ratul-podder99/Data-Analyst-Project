# Delivery Operations Analytics: Staffing & Hiring Recommendation

**An end-to-end analysis of a last-mile delivery network (6 hubs, 64 agents, 22,789 orders) that answers one business question: *should we approve a request to hire 8 new delivery agents?***

Using SQL, Excel and PowerPoint, I measured hub utilisation, diagnosed why holds and returns were driving up cost, forecast the agents needed for the next four weeks, and recommended a plan that costs **BDT 62,800 instead of BDT 175,200** while closing the capacity gaps.

---

## Business problem

The company ("On The Go") runs six delivery hubs grouped into three clusters. Operations asked for **8 new hires** to cope with rising volume. The task was to decide whether that was justified, using two weeks of operating data (13–26 Oct 2026), a 4-week order forecast, a fixed cost rate card and a **BDT 70,000 budget**.

## Recommendation

**Do not approve the 8 hires.** Approve a targeted plan instead:

| Hub | Decision | Cost (BDT) | Why |
|---|---|---:|---|
| H01 Greenfield | Move 2 agents in from H02 | 4,000 | 88.6% utilisation, short 2 agents in every forecast week, donor in same cluster |
| H02 Lakeview | Donate 2 agents | 0 | 56.2% utilisation, needs only 8 of its 11 agents |
| H03 Riverside | Hold-reduction programme (no hire) | 15,000 | 64% of its holds are agent-side; this is a dispatch problem, not a headcount problem |
| H04 Hillcrest | Hire 2 Fixed agents | 43,800 | 95.9% utilisation, no donor available in its cluster |
| H05 Brookside | No action; coach high-return agents | 0 | 71.5% utilisation, kept as reserve |
| H06 Westwood | No action; monitor weekly | 0 | 80.4% utilisation, needs 9, has 9 |
| **Total** | | **62,800** | **BDT 7,200 held as contingency; BDT 112,400 less than the 8-hire request** |

## Key findings

- **Three hubs ran above the 85% utilisation target in Week 2:** H04 (95.9%), H03 (90.8%) and H01 (88.6%). Two hubs sat well below it: H02 (56.2%) and H05 (71.5%).
- **Riverside (H03) is a process problem.** It logged **874 holds in Week 2** (other hubs: 123–186), **64.4% of them agent-side**, with Late Dispatch alone causing 312. It needs 1.39 attempts per order against 1.07–1.10 elsewhere and has the highest cost per delivered order (BDT 45.67).
- **Held orders at H03 end badly:** only 76.3% are eventually delivered and 13.9% are returned, the worst outcome of any hub.
- **Only H01 and H04 stay short of agents in every forecast week** (+2 each); H02 and H05 have a surplus.
- **Brookside (H05) has the highest return cost** (BDT 12,980 in Week 2). Three agents with more than 30 days' tenure return over 15% of their attempts and are flagged for coaching.
- **Fixed pay beats Variable pay above 25 deliveries per agent-day**, which is why the H04 hires are Fixed.

**Network headline numbers (13–26 Oct):** 21,078 orders delivered · 82.4% delivery success · BDT 731,881 total cost · BDT 34.72 per delivered order · 72.5% average utilisation · 43.1% agent-side hold share · 5.8% return ratio.

## Dashboard

`Dashboard_Final.xlsx` is an interactive four-page Excel dashboard built with formulas only (no macros). The yellow **Hub** and **Week** dropdowns on the Overview page filter the KPI cards and the daily trend chart.

**1. Overview:** KPI cards, a hub action board with signals and recommended actions, delivered orders by hub, and a daily trend.
![Overview dashboard](Dasboard%20Images/Dashboard_1_Overview.png)

**2. Utilisation & Staffing:** utilisation vs the 85% target, agents required vs active, and the staffing gap for each forecast week.
![Utilisation and staffing dashboard](Dasboard%20Images/Dashboard_2_Utilisation_Staffing.png)

**3. Holds & Returns:** agent-side vs customer-side holds, hold reasons, what happens to held orders, and return cost by hub.
![Holds and returns dashboard](Dasboard%20Images/Dashboard_3_Holds_Returns.png)

**4. Agents:** return ratio by agent, deliveries per agent-day vs the Fixed/Variable break-even, and a full flagged agent table.
![Agents dashboard](Dasboard%20Images/Dashboard_4_Agents.png)

## Deliverables

| File | What it is |
|---|---|
| [`Project Report.pdf`](Project%20Report.pdf) | Full write-up: SQL, outputs and assumptions for each question, the hiring recommendation, budget, expected return, risks and the dashboard pages |
| [`Staffing Recommendation.pptx`](Staffing%20Recommendation.pptx) | 8-slide executive deck: situation, root cause, forecast, decision by hub, budget, risks and next steps |
| [`Dashboard_Final.xlsx`](Dashboard_Final.xlsx) | Final interactive dashboard (4 report pages, helper `Calc` sheet and data tabs) |
| [`Dashboard.xlsx`](Dashboard.xlsx) | Earlier version holding only the prepared data tabs |
| [`Data/`](Data) | The six source CSV files |

## Dataset

| File | Rows | Contents |
|---|---:|---|
| `orders.csv` | 22,789 | One row per order: hub, created time, final status, COD amount, delivery charge |
| `order_logs.csv` | 51,190 | Event log per order: Assigned, Delivered, Hold, Returned, with hold reason and attempt number |
| `agents.csv` | 64 | Agent, hub, salary type (Fixed / Variable), joining date, active status (62 active) |
| `hubs.csv` | 6 | Hub name and cluster (A, B, C) |
| `volume_forecast.csv` | 24 | Expected weekly orders per hub for four weeks from 27 Oct 2026 |
| `costs.csv` | 5 | Cost rates: BDT 30 per hold, 55 per return, 5,000 per hire, 2,000 per move, 15,000 per hold programme |

## Approach

1. **Agent productivity (SQL).** Built a daily agent table from the order log (assigned, delivered, hold, returned) and rolled it up by hub.
2. **Utilisation.** Measured Week 1 vs Week 2 per hub as `assigned attempts ÷ (available agent-days × 40)` and flagged hubs above the 85% target.
3. **Hold diagnosis.** Ranked hold reasons per hub, split them into agent-side vs customer-side, and tracked the final outcome of held orders.
4. **Agent quality.** Flagged agents with more than 30 days' tenure and a return ratio above 15%.
5. **Cost per delivered order.** Combined agent pay (Fixed vs Variable pay rules) with hold and return charges, by hub and week.
6. **Staffing forecast.** Converted forecast orders into required attempts using each hub's Week 2 attempts per order, then into required agents and a gap against active headcount.
7. **Decision and budget.** Chose the cheapest fix first (move, then fix the process, then hire), costed each option against the BDT 70,000 budget, estimated payback, and listed risks with numeric triggers and a day-14 go/stop gate.
8. **Dashboard and deck.** Packaged the findings into an interactive Excel dashboard and an executive presentation.

## Key assumptions

- Utilisation = assigned attempts ÷ (available agent-days × 40). Every active agent counts as available every day. Target = 85%.
- Capacity per agent at target = 7 × 40 × 0.85 = **238 attempts per week**.
- Required agents = `forecast orders × Week 2 attempts per order ÷ 238`, rounded up. Week 2 attempts per order is assumed to continue.
- Agents move only **within a cluster** and only out of hubs below 75% Week 2 utilisation.
- The hold programme removes 50% of agent-side holds, ramping up over 14 days.
- New agents work at 60% speed for their first 7 days. A Fixed hire costs BDT 21,900 in month 1 (5,000 one-time plus 16,900 salary).
- "Revenue protected" is an **upper bound**: extra deliveries × an average delivery charge of about BDT 65, at Week 2 delivery rates.
- Agent-side hold reasons are Late Dispatch, Not Attempted and Agent Time Shortage.

## Repository structure

```
.
├── Data/                          # source CSVs (orders, order_logs, agents, hubs, volume_forecast, costs)
├── Dasboard Images/               # dashboard page screenshots used in this README
├── Dashboard_Final.xlsx           # interactive 4-page Excel dashboard
├── Dashboard.xlsx                 # data-only workbook (earlier version)
├── Project Report.pdf             # SQL, results, recommendation, budget, risks
├── Staffing Recommendation.pptx   # executive presentation
└── README.md
```

## How to explore

1. Open **`Project Report.pdf`** for the full reasoning, or **`Staffing Recommendation.pptx`** for the 8-slide summary.
2. Open **`Dashboard_Final.xlsx`** in Excel or Google Sheets, go to the **Overview** sheet, and use the yellow **Hub** and **Week** dropdowns. The `Calc` sheet holds the formulas that feed each page.
3. The SQL queries are written in MySQL-style syntax and are included in the report; the CSVs in `Data/` can be loaded into any SQL database to rerun them.

## Skills demonstrated

SQL (CTEs, window functions, joins, conditional aggregation) · Excel (SUMIFS, dropdown-driven dashboards, conditional formatting, charts) · KPI design · root-cause analysis · capacity forecasting · cost and payback modelling · risk planning · executive presentation and data storytelling

## Limitations

- Only 14 days of history, so seasonality is not captured.
- Forecast figures assume Week 2 attempts per order and delivery rates continue.
- The 50% hold-reduction effect and the 60% new-agent ramp are planning assumptions, not measured results; the risk register defines how to check them within 14 days.

## Author

**Ratul Podder** · CSE graduate, East West University
GitHub: [ratul-podder99](https://github.com/ratul-podder99) · Email: ratulpodder99@gmail.com
