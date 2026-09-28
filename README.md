# Brazil container flows 2025

**Who carries Brazil's deep-sea containers, how long ships wait at the terminals, and where the empties and transhipments go — built from ANTAQ's 2025 port data and a vessel-by-vessel carrier mapping.**

I spent 17 years in freight forwarding negotiating ocean freight on Brazil trade lanes. Most of the numbers the market uses for Brazil (carrier shares, port congestion, empty repositioning) come from paid databases or from anecdote. This project rebuilds them from public data, and is explicit about where the data stops.

---

## Key findings

| # | Finding | Figure |
|---|---|---|
| 1 | Median pre-berthing wait ranges from **2 h to 22 h** across the 11 main container terminals | [fig01](#1-waiting-time-varies-tenfold-between-terminals) |
| 2 | **54%** of the time container ships spend at those terminals is spent waiting to berth, not working | [fig02](#2-ships-spend-more-time-waiting-than-working) |
| 3 | Waiting got worse in the second half of 2025: median **+8.2 h** (10.5 h → 18.7 h) | [fig03](#3-waiting-got-worse-in-h2-2025) |
| 4 | **Maersk leads** Brazil's deep-sea container volume under every counting rule and scenario tested; the narrowest margin over MSC is 0.4 pp | [fig04](#4-maersk-leads-msc-is-close-behind) |
| 5 | At least **16%** of deep-sea TEU moved on vessels confirmed as chartered-in | [fig05](#5-at-least-16-moved-on-chartered-in-vessels) |
| 6 | Brazil ships out **empty dry** boxes (33% of dry exports are empty) and brings in **empty reefers** (54% of reefer imports) | [fig06](#6-empty-dry-boxes-go-out-empty-reefers-come-in) |
| 7 | About **24%** of international container volume moves via transhipment hubs — **34.5%** of exports vs 14.2% of imports; Paranaguá is the most hub-dependent large port (41%) | [fig07](#7-a-quarter-of-the-trade-goes-through-hubs) |

---

## Data

**Source:** ANTAQ — Estatístico Aquaviário, calendar year 2025. Five extracts:

| File | Content |
|---|---|
| `antaq_2025_cargo.xlsx` | Cargo by port call (TEU, navigation type, vessel IMO) |
| `antaq_2025_cargo_ports.xlsx` | Cargo by foreign port of origin/destination, direction, full/empty, box type and size |
| `antaq_2025_port_calls.xlsx` | Port calls with arrival, berthing and departure times |
| `antaq_2025_stoppages.xlsx` | Stoppages during the call, by cause |
| `antaq_2025_panel_gr23.xlsx` | ANTAQ's own panel totals, used to reconcile the extracts |

**Scope:** deep-sea calls (*Longo Curso*) with a vessel IMO — **11.09 million TEU**. The raw files are not in this repository; see [Reproducing the analysis](#reproducing-the-analysis).

**What the data does not have:** no AIS positions, no bookings, no freight rates, no carrier field. ANTAQ records the vessel, not the carrier. The carrier for each vessel had to be researched separately (next section).

---

## The carrier mapping

ANTAQ gives the vessel's IMO number but not who operates it. I built the carrier table by hand, starting with the vessels that carried the most TEU:

- **324 vessels mapped**, covering **90.5%** of deep-sea TEU with IMO.
- Operator identified from carrier schedules and vessel histories; for vessels that changed service during 2025, the service that carried most of their Brazilian TEU was used.
- Registered owner checked for **181 vessels** (MagicPort, September 2026) to separate carrier-owned from chartered-in tonnage.
- The remaining **349 vessels (9.1% of TEU)** are small: the largest single one is 0.13% of the total.
- One vessel was dropped (a general-cargo ship recorded with container TEU) and one could not be traced for 2025. Both are listed and quantified in notebook 03.

Brands are grouped by parent company: Hamburg Süd and Aliança under Maersk, Log-In under MSC, Mercosul Line and CNC under CMA CGM, OOCL under COSCO, Gold Star under ZIM.

The table is in [`data/processed/tabela_completa.csv`](data/processed/tabela_completa.csv) (column names are in Portuguese).

---

## Findings in detail

### 1. Waiting time varies tenfold between terminals

![Median pre-berthing wait by terminal](outputs/figures/fig01_wait_by_terminal.png)

Only terminals with comparable records were ranked: at least 100 deep-sea calls, mainly container traffic, and arrival times that look like real arrivals rather than the berthing time copied over. That leaves **11 terminals, covering 73% of deep-sea calls and 84% of deep-sea TEU**. The confidence intervals come from 2,000 bootstrap resamples.

**Reading it:** where the intervals overlap (BTP and both Paranaguá terminals, around 19 h), the ranking between them is not meaningful.

**Caveat:** Salvador's 2 h is an outlier. It most likely reflects fixed berthing windows rather than a faster terminal, so it is not directly comparable with the others.

### 2. Ships spend more time waiting than working

![Vessel-days waiting vs at berth](outputs/figures/fig02_vessel_days.png)

Across the 11 terminals: **5,348 vessel-days waiting** and **4,488 at berth**, over 4,800 calls. Pecém and Navegantes lose the largest share of their time to waiting (67%); Itapoá the lowest among the large terminals (41%).

**Caveat:** ANTAQ's waiting time includes congestion *and* ships that arrived early by choice (waiting for their window or for cargo cut-off). The data cannot separate the two. 65 calls have no recorded waiting time and are left out, so waiting is slightly understated.

### 3. Waiting got worse in H2 2025

![Wait H1 vs H2 by terminal](outputs/figures/fig03_wait_h1_vs_h2.png)

Median wait across the 11 terminals went from **10.5 h in H1 to 18.7 h in H2** (95% CI of the change: +6.7 to +9.5 h). The increase is statistically clear at Paranaguá (both terminals), Rio Grande, Santos Brasil and Itapoá. DP World Santos is the only terminal that improved (−4.2 h), but that change is not significant. Terminal-months with incomplete ANTAQ records were excluded (16 calls).

**Caveat:** this is one year of data. The H2 deterioration may be seasonal rather than a trend.

### 4. Maersk leads, MSC is close behind

![Carrier shares](outputs/figures/fig04_carrier_share.png)

Many vessels on Brazil services are shared between carriers (vessel-sharing agreements), so market share depends on how a shared vessel's TEU is counted. Two rules were used:

- **Primary carrier:** the whole vessel counts for the carrier that runs the service.
- **Split:** the vessel's TEU is divided equally among all carriers on board.

| Carrier group | Primary carrier | Split |
|---|---|---|
| Maersk | 23.9% | 23.7% |
| MSC | 21.5% | 23.1% |
| CMA CGM | 12.8% | 13.6% |
| Hapag-Lloyd | 7.2% | 8.4% |
| COSCO | 6.1% | 3.8% |
| ONE | 1.9% | 4.3% |

**Is the Maersk lead robust?** Three vessels had a debatable carrier assignment, so I re-ran the shares with each one moved:

| Scenario | Maersk − MSC, split rule |
|---|---|
| Table as published | +0.58 pp |
| LOG-IN EVOLUTION reassigned to Log-In (MSC group) — its operator is documented for only part of 2025 | +0.37 pp |
| SEASPAN EMPIRE reassigned to Hapag-Lloyd + Maersk | +0.72 pp |
| MSC MICHELA with its 2026 partners (MSC + Hapag-Lloyd + ZIM) | +0.73 pp |

Maersk leads in every scenario, and by 2.3–2.5 pp under the primary-carrier rule. The unmapped tail (9.5% of TEU) is the other risk: under the split rule, MSC would need to take 6.2% more of that tail than Maersk, or the four largest unmapped vessels would all have to be MSC-only. Those four were checked, and none was.

**What the two rules reveal:** ONE more than doubles under the split rule (1.9% → 4.3%). In Brazil it mostly buys space on other carriers' ships. COSCO does the opposite (6.1% → 3.8%): it is often the carrier running the service.

### 5. At least 16% moved on chartered-in vessels

![Vessel ownership](outputs/figures/fig05_ownership.png)

Of all deep-sea TEU, **16% moved on vessels confirmed as chartered-in**, 20% on vessels confirmed as carrier-owned, and 55% on vessels whose owner was not verified.

**The number to use is 16%, not 44%.** Among verified vessels, 44% of TEU was on chartered tonnage. But verification was not random: the largest and most ambiguous vessels were checked first. If unverified vessels followed the same split, the chartered share would be about 39%; the upper bound is 70%. Ownership reflects the registered owner in September 2026, not necessarily during 2025.

### 6. Empty dry boxes go out, empty reefers come in

![Full vs empty](outputs/figures/fig06_full_vs_empty.png)

International flows (Brazil–Brazil excluded), by direction and equipment:

| | Full (k TEU) | Empty (k TEU) | % empty |
|---|---|---|---|
| Imports — dry | 3,715 | 613 | 14% |
| Imports — reefer | 370 | 430 | 54% |
| Exports — dry | 2,689 | 1,312 | 33% |
| Exports — reefer | 906 | 55 | 6% |

Brazil imports more dry cargo than it exports, so dry boxes leave empty. It exports far more refrigerated cargo (meat, fruit) than it imports, so reefers arrive empty to be loaded.

**How much could be reused locally?** Only **17%** of empty dry and reefer TEU (415k) matches by Brazilian port, equipment type and box size in both directions. That is the ceiling for reusing import empties for local exports; the rest has to be repositioned.

**Classification choice:** ANTAQ's *Ventilado High Cube* category (28% of all TEU) was counted as dry. It behaves like a port-level recording convention rather than genuinely ventilated cargo (tested in notebook 02, Bloco 21). Treating it differently would change the dry figures.

### 7. A quarter of the trade goes through hubs

![Transhipment hubs](outputs/figures/fig07_transhipment_hubs.png)

When the foreign port recorded by ANTAQ is one of 11 major transhipment hubs, the cargo is counted as "via hub". On that basis, **24.2%** of international TEU moves via a hub: **34.5% of exports** and **14.2% of imports**. Singapore (864k TEU), Tanger Med (555k) and Cartagena (415k) are the largest.

Among the eight largest Brazilian ports, **Paranaguá (41%)** and **São Francisco do Sul (33%)** depend most on hubs; Santos sits near the average (23%).

**Caveat:** ANTAQ records where the box was loaded or discharged from that vessel, not its final origin or destination. Hub volume therefore includes some local cargo (Colombian or Moroccan imports, for example) and misses relays at ports outside the list (Busan or Valencia, for example). Treat 24% as an indicator, not an exact transhipment rate.

---

## Data quality notes

Two checks worth knowing about if you use ANTAQ data yourself:

- **Reefer capacity estimates are not reliable at vessel level.** For the 11 vessels where reefer plugs could be observed, the estimate was too high for 9 of them (median observed/estimated = 0.63). Small feeders were underestimated and Panamax/Neo-Panamax vessels overestimated. No finding in this project depends on that estimate.
- **"Ventilado High Cube" is a recording convention**, not a cargo type (see finding 6).

---

## Limitations

- **One year only.** Nothing here separates seasonality from trend.
- **No AIS data.** Waiting time is ANTAQ's recorded arrival-to-berthing time, not a measured anchorage time.
- **Carrier mapping is manual.** It covers 90.5% of TEU; the remaining 9.5% could, in theory, shift the smaller carriers' shares. The Maersk–MSC ranking was tested against it (finding 4).
- **Ownership is verified for 181 of 324 vessels**, and verification was prioritised, not random (finding 5).
- **Registered owner ≠ beneficial control.** Some owners are single-ship companies whose ties to a carrier are not public. Borderline cases are explained in the `Observacoes_Tecnicas` column of the carrier table.

---

## Repository structure

```
├── README.md
├── notebooks/
│   ├── 02_pipeline_antaq.ipynb           # the analysis: ingestion, validation, findings, figures
│   └── 03_edicao_tabela_armadores.ipynb  # how the carrier table was built, vessel by vessel
├── data/
│   ├── raw/                              # empty — ANTAQ files go here
│   └── processed/
│       ├── tabela_completa.csv           # carrier and ownership table (324 vessels)
│       ├── navios_descartados.csv        # vessels removed, with the reason
│       ├── imos_prioritarios.csv         # vessel priority list used for the research
│       └── tabela_referencia_262.csv     # earlier snapshot, used for a consistency check
└── outputs/
    └── figures/                          # the 7 figures above
```

Code comments and printed outputs in the notebooks are in Portuguese; figures and this README are in English.

---

## Reproducing the analysis

1. Export the 2025 data from ANTAQ's [Painel do Estatístico Aquaviário](https://aquarela.antaq.gov.br/single/?appid=2b370bbc-6a27-4e2e-8c43-56f1732c19f8&sheet=816b5cf4-46df-407d-b1e8-85f24d1c3015&opt=currsel%2Cctxmenu) and save the five extracts in `data/raw/` with the file names listed in [Data](#data). See [`data/raw/README.md`](data/raw/README.md) for how to export them.
2. Install the dependencies: `pip install pandas numpy matplotlib openpyxl`
3. Open `notebooks/02_pipeline_antaq.ipynb` and run all cells. The notebook finds the project folder on its own; no paths to edit.

Notebook 03 documents how the carrier table was built. Every block that writes to the table checks first and does nothing if its vessels are already there, so running it on the published table changes nothing.

---

## Related work

- [brazilian-maritime-analysis-2025](https://github.com/hugopedro-ds/brazilian-maritime-analysis-2025) — Brazil container market, port performance and carrier concentration (16 notebooks)
- [brazil-westafrica-container-corridor](https://github.com/hugopedro-ds/brazil-westafrica-container-corridor) — Brazil → West and Central Africa container trade

## Author

**Hugo Pedro** — São Paulo, Brazil. 17+ years in international freight forwarding and ocean freight procurement; now working on trade-lane analytics.
[LinkedIn](https://www.linkedin.com/in/hugopedro/)
