# New York EV Charging Opportunity Dashboard

An interactive dashboard by Mingyu Liang for exploring electric-vehicle registrations across New York State and screening ZIP-level charging opportunities.

纽约州电动车注册与充电机会看板。可按县、Tesla / Non-Tesla、车型和注册类别筛选，查看 ZIP 热力图、市场结构和机会排名。

## Open and share / 打开与分享

**[Open the live dashboard / 在线打开看板](https://dannylmy.github.io/new-york-ev-dashboard/)**

**[Download all files as ZIP / 下载完整项目](https://github.com/Dannylmy/new-york-ev-dashboard/archive/refs/heads/main.zip)**

1. On GitHub, choose **Code → Download ZIP**, then extract the archive.
2. Open **[index.html](index.html)** in Chrome, Edge, Safari, or Firefox. No installation or Python environment is required.
3. Keep an internet connection for the Leaflet map libraries and CARTO / OpenStreetMap basemap. Registration aggregates are embedded in the HTML.

GitHub's repository preview displays HTML source rather than running the dashboard. Use the live link above, or download and open the HTML locally. GitHub Pages publishes from **main → / (root)**. The live map, county filter and reset action were verified after deployment on 2026-09-20.

下载 ZIP 并解压后，双击 `index.html` 即可打开。图表数据已内嵌；地图底图和地图组件需要联网。直接分享完整 ZIP 可同时提供看板、Markdown 说明和原始 CSV。

## Included files / 文件

| File | Purpose |
|---|---|
| [index.html](index.html) | Original strict-validation dashboard, preserved byte for byte under a portable entry-point name. |
| [Electric_Vehicle_Registrations.csv](Electric_Vehicle_Registrations.csv) | Original CSV, preserved byte for byte; 216,228 data rows and 20 fields. |
| [DATA_SOURCES.md](DATA_SOURCES.md) | Original database and view links, field definitions, snapshot provenance and processing method. |
| [CHECKSUMS.sha256](CHECKSUMS.sha256) | SHA-256 checksums for the dashboard and CSV. |

## Snapshot coverage / 数据范围

| Stage | Registration records |
|---|---:|
| Original CSV | 216,228 |
| Electric records with a five-digit ZIP, excluding `OUT-OF-STATE` county | 209,457 |
| Valid NY ZIP coordinates with matching county | 205,846 |

The mapped data covers **62 counties and 1,735 ZIP codes**. Counts were independently checked against the original CSV and the locally cached ZIP lookup used by the original project. Every ZIP total matches the dashboard's embedded data.

This is the original saved snapshot, packaged on 2026-09-20. The CSV's exact original download date and the source's snapshot date are not recorded. The online source continues to update; this package does not claim to represent September 2026 registrations.

本项目保留原来的数据快照，没有用现在的在线数据替换 CSV。记录数是注册记录数，不应直接理解为去重后的车辆数或新增销量。

## Interpretation / 使用边界

- Four dropdowns filter county, brand group, body type, and registration class. Model-year and registration-date charts respond to those filters; they are not date-range controls.
- Map locations are ZIP centroids, not vehicle addresses or charging-station coordinates. Strict county matching can exclude valid registrations in ZIPs spanning counties.
- **Reg Valid Date** means the latest registration-document issue date. Its monthly chart is not a historical sales series or a complete adoption-growth series.
- The opportunity score combines registration scale, model year, non-private registration share and body-type mix. It is an exploratory ranking, not a validated charging-demand or investment model. Charging supply, power availability, land access, utilization and economics are not included.
- `2023+` is a fixed model-year threshold. Timeline processing retains model years 2012–2027 and registration-document dates in 2024–2026; the dashboard displays the last 14 available time bins after filtering.

## Data attribution

Underlying records: **New York State Department of Motor Vehicles (NYS DMV)** through [New York Open Data](https://data.ny.gov/Transportation/Vehicle-Snowmobile-and-Boat-Registrations/w4pv-hbkt).

Matching source view: [Electric Vehicle Registrations — x9ct-hwyv](https://data.ny.gov/Transportation/Electric-Vehicle-Registrations/x9ct-hwyv), a community-created filtered view of the DMV database. See [DATA_SOURCES.md](DATA_SOURCES.md) for the evidence and limitations of this source match.

Geographic lookup: pgeocode / GeoNames. Map rendering: Leaflet and Leaflet.heat. Basemap: CARTO with OpenStreetMap attribution retained in the dashboard. Third-party data and libraries retain their respective terms; this repository does not assign a new license to the DMV records.
