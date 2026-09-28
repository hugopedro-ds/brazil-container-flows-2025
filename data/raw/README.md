# Raw data (not included)

The five ANTAQ extracts used in this project are not stored in the repository: they are large and publicly available.

**Source:** ANTAQ — Painel do Estatístico Aquaviário, calendar year 2025. The custom reports sheet ("8. Relatórios Personalizados – Movimentação") lets you choose the columns, filter the year and export:
https://aquarela.antaq.gov.br/single/?appid=2b370bbc-6a27-4e2e-8c43-56f1732c19f8&sheet=816b5cf4-46df-407d-b1e8-85f24d1c3015&opt=currsel%2Cctxmenu

The link opens the panel with whatever filters are active — set the year to 2025 before exporting. The original ANTAQ column names used for each file are listed in notebook 02, Bloco 2 (the renaming map from Portuguese to English).

Save the exports in this folder with exactly these names:

- antaq_2025_cargo.xlsx
- antaq_2025_cargo_ports.xlsx
- antaq_2025_port_calls.xlsx
- antaq_2025_stoppages.xlsx
- antaq_2025_panel_gr23.xlsx

ANTAQ updates its database monthly, so an export made later may differ slightly from the figures in this project. Notebook 02 checks that all five files are present before running and lists any that are missing.
