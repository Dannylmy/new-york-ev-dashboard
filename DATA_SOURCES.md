# Data sources and methodology

Source verification date: **2026-09-20**. The bundled CSV and dashboard are historical local files, not a fresh online export.

## 1. Original database

- Provider: **NYS DMV**.
- Dataset: **Vehicle, Snowmobile, and Boat Registrations**.
- Dataset ID: `w4pv-hbkt`.
- [Official dataset page](https://data.ny.gov/Transportation/Vehicle-Snowmobile-and-Boat-Registrations/w4pv-hbkt)
- [Machine-readable metadata](https://data.ny.gov/api/views/w4pv-hbkt.json)
- [Full current CSV export](https://data.ny.gov/api/views/w4pv-hbkt/rows.csv?accessType=DOWNLOAD) — this is the full, much larger DMV database, not the bundled EV-only snapshot.

The current provider description says registrations expired more than two years are excluded, while records with scofflaw, suspension or revocation indicators remain included. This description is current-source context; the exact historical extraction date of the local CSV is unknown.

## 2. Matching EV view

- Title: **Electric Vehicle Registrations**.
- View ID: `x9ct-hwyv`.
- [View page](https://data.ny.gov/Transportation/Electric-Vehicle-Registrations/x9ct-hwyv)
- [View metadata](https://data.ny.gov/api/views/x9ct-hwyv.json)
- [Current EV-view CSV export](https://data.ny.gov/api/views/x9ct-hwyv/rows.csv?accessType=DOWNLOAD)

The view metadata identifies `modifyingViewUid = w4pv-hbkt`, `assetType = filter`, and `provenance = community`. Its query selects the same 20 columns, in the same order, as the bundled CSV and filters:

```sql
WHERE upper(record_type) = 'VEH'
  AND upper(fuel_type) = 'ELECTRIC'
```

All 216,228 rows in the local CSV match those two conditions. The filename, schema and filter match provide strong evidence that this is the corresponding source view. The file contains no saved download-origin URL or snapshot identifier, so a byte-for-byte match to a historical online export cannot be established. The underlying DMV database is confirmed by the view metadata.

这里区分了“DMV 官方底层数据库”和“公众创建的 EV 筛选视图”。原 CSV 的文件名、20 个字段及筛选条件与该视图一致，但缺少历史下载记录，因此不把来源匹配说成已验证的历史导出校验。

## 3. Original files and integrity

The HTML is an exact copy of the original `ny_ev_dashboard_strict.html`, renamed to `index.html`. The CSV keeps its original name and bytes. `CHECKSUMS.sha256` records both hashes. No source record was edited, dropped or refreshed in the bundled CSV.

Original CSV: **216,228 rows × 20 columns**, excluding the header. Dashboard payload: **15,817 grouped map records** representing **205,846 registration records**.

## 4. Processing used by the existing dashboard

1. Normalize county, city, make, body, class, fuel and state strings; extract a five-digit ZIP.
2. Keep `Fuel Type = ELECTRIC`, exclude `County = OUT-OF-STATE`, and require a ZIP: **209,457 records**.
3. Join ZIPs to the original project's cached pgeocode US lookup, based on GeoNames postal data. Require geographic `state_code = NY`, latitude 40.45–45.05 and longitude −79.9 to −71.7.
4. Compare county names after uppercasing and removing non-alphanumeric characters. Keep only county matches: **205,846 records**, **62 counties**, **1,735 ZIPs**.
5. Aggregate map counts by ZIP, county, Tesla / Non-Tesla, grouped body type and grouped registration class. ZIP counts and inclusion-stage counts were independently reconciled for this package.

[GeoNames postal data](https://download.geonames.org/export/zip/) and [pgeocode](https://github.com/symerio/pgeocode) provide the geographic lookup. The original lookup cache and build environment are not bundled; this is a preserved dashboard/data sharing package, not a fully pinned rebuild environment.

The original processing does not deduplicate by VIN, apply a separate expiration-date test, or remove flagged records. `State` in the CSV is a mailing-state field; geographic NY eligibility is determined through the ZIP lookup and county check.

## 5. Opportunity score

For each ZIP under the current dropdown filters:

```text
score = 45 × ln(1 + registrations) / ln(1 + largest ZIP registration count)
      + 25 × share with model year >= 2023
      + 18 × share with Registration Class != PAS
      + 12 × share with Body Type in {SUBN, PICK, VAN, BUS}
```

Shares are fractions between zero and one. The largest ZIP count is recomputed within the current filter, so scores are relative to that selection and should not be compared as absolute measures across filters. The dashboard shows the top eight ZIPs. Weights are heuristics in the original code, not statistically estimated demand parameters.

## 6. CSV field definitions

Descriptions below come from the matching view's public metadata. Preserve ZIPs and VINs as text when opening the CSV in spreadsheet software.

| Field | Source definition |
|---|---|
| Record Type | The type of vehicle. BOAT = Boat; SNOW = Snowmobile; TRL = Trailer; VEH = All other vehicles |
| VIN | The Vehicles Identification Number as assigned by the vehicle manufacturer or by NYS DMV if the original VIN was compromised. |
| Registration Class | An alpha code that is used to classify the registration. (see complete list of registration classes at the end of the data dictionary) |
| City | A city name for mailing purposes; appears in the last line of an address on a mail piece. |
| State | The official USPS abbreviation for the name of a mailing state, U.S. territory, Canadian Province, Commonwealth or armed forces ZIP Code designation. If APO/FPO/DPO, then the state abbreviation will be “AA,” “AE,” or “AP.” |
| Zip | A code that identifies a specific geographic mail delivery area. ZIP Codes can represent an area within a foreign country, a state, or a single building or company that has a very high mail volume. The zip code appears in the last line of the address on a mail piece. Example: 12345 |
| County | The New York State county name that is the county of residence or use. |
| Model Year | The manufacturer model year of vehicle. |
| Make | The DMV code for the make of a vehicle that appears on the registration. The DMV make code is the first 5 letters of the vehicle’s make name. If the vehicle make is more than one word, the make code is the first 2 letters of the first two words with a slash in between. (Examples, CHEVR, HA/DA) |
| Body Type | The body type of a vehicle. (see the complete list of body types at the end of the data dictionary) |
| Fuel Type | The fuel type of a vehicle. |
| Unladen Weight | The weight of a passenger vehicle without any load. |
| Maximum Gross Weight | The maximum weight that a commercial vehicle is permitted to carry. |
| Passengers | The number of passengers that a for-hire vehicle can transport, excluding the driver. |
| Reg Valid Date | The date DMV issued the most recent registration document. |
| Reg Expiration Date | The date when the registration expires. |
| Color | The color of the vehicle. This is the DMV code not the NCIC code. (see the color codes at the end of the data dictionary) |
| Scofflaw Indicator | Indicates if a parking or red light scofflaw stop is in effect. (Y/N). |
| Suspension Indicator | Indicates if a suspension is in effect. (Y/N). |
| Revocation Indicator | Indicates if a revocation is in effect. (Y/N). |
