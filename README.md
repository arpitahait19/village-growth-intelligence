# Village Economic Growth Intelligence

Finding the top 100 villages in India with the highest economic growth over the last 5 years, using satellite data and public datasets.

**Deliverables**

| What | Where |
|---|---|
| Top 100 villages (overall) | [`outputs/top100_villages.csv`](outputs/top100_villages.csv) |
| Top 100 villages (rural, > 10 km from a 100k+ city) | [`outputs/top100_rural_villages.csv`](outputs/top100_rural_villages.csv) |
| Interactive map (download and open in a browser) | [`outputs/maps/top100_map.html`](outputs/maps/top100_map.html) |
| Charts and static map | [`outputs/figures/`](outputs/figures/) |
| Presentation (7 slides) | [`presentation/`](presentation/) |

## Definition of economic growth

Economic growth is the spatially measurable expansion of local productive capacity, wealth, and infrastructure over time within rural boundaries

## Method in brief

Every Census 2011 village is measured with satellite signals over **2020 → 2025**:

| Signal | Dataset | Measures |
|---|---|---|
| Night lights | NOAA VIIRS DNB monthly (~500 m) | Economic activity |
| Built-up share | Google/WRI Dynamic World (10 m) | Physical expansion |
| Building count | Google Open Buildings Temporal (4 m, 2018 → 2023) | Independent check |

**Two-stage ranking**

1. **Stage 1 – all India (591,717 villages):** villages with 500+ people get a score = 50% night-light growth percentile + 50% built-up growth percentile. Light spikes with no construction (likely gas flares or single facilities) are excluded.
2. **Stage 2 – shortlist (388 villages):** the best 250 overall and best 250 rural are checked with building counts. Final score = 70% Stage 1 + 30% building-growth percentile.

The method was first tested on Haryana (6,681 villages): the Stage 1 top 100, chosen without building data, ranked at the 90th percentile of building growth.

Full reasoning for every choice is in the notebooks (markdown cells marked ✍️).

## Repository layout

```
notebooks/
  01_prepare_villages.ipynb   clean SHRUG village boundaries, build Earth Engine upload files
  02_extract_signals.ipynb    Google Earth Engine: night lights, built-up, buildings (pilot + national)
  03_score_rank.ipynb         features, distance to cities, Stage 1 + Stage 2 scoring, top-100 lists
  04_visualise.ipynb          interactive map, charts, static map
data/raw/                   SHRUG downloads (not in the repo – see below)
data/processed/             extracted signal CSVs (in the repo); large boundary files (rebuilt by notebook 01)
outputs/                    final tables, map, figures
presentation/               slides
SOURCES.md                  all data sources with links
```

## How to run

**1. Environment** (Python 3.11; conda shown, pip also works)

```bash
conda create -n village python=3.11 -y
conda activate village
pip install -r requirements.txt
earthengine authenticate          # only needed to re-run the Earth Engine exports
```

**2. Raw data** – place these two files in `data/raw/` (too large for GitHub):
- PC11 village polygons (GeoPackage) → `village_modified.gpkg` (~900 MB)
- SHRID population and land area weights (CSV) → `shrid_pop_area_key.csv` (~57 MB)

Original source: [SHRUG, Development Data Lab](https://www.devdatalab.org/shrug) – please cite SHRUG when using the data.

Mirror for convenience (same files, no sign-up): [Google Drive folder](https://drive.google.com/drive/folders/1DwlKo1bvEVOCz1N2ioFekk7b0x7XeATz)

**3. Run the notebooks in `notebooks/` in order** (start `jupyter notebook` from the repository folder, then *Kernel → Restart & Run All* on each):

| Notebook | Run time | Notes |
|---|---|---|
| `01_prepare_villages` | ~10 min | Rebuilds `villages_india.gpkg` and the Earth Engine upload zips |
| `02_extract_signals` | ~1 min | Exports are **off by default** – the extracted CSVs are already in `data/processed/`. To re-extract, upload the zips as Earth Engine assets, set your `PROJECT_ID` and the `RUN_*_EXPORT` switches to `True`, then download the CSVs from Google Drive |
| `03_score_rank` | ~3 min | All method settings are in one cell at the top |
| `04_visualise` | ~3 min | Writes the map and figures |

## Limitations

- Village boundaries and population are from Census 2011; Telangana appears under Andhra Pradesh.
- 2020 was a COVID year, which may inflate night-light growth.
- National extraction at 30 m (pilot 10 m) to fit the free Earth Engine compute quota.
- Open Buildings ends in 2023, so its window is 2018 → 2023.
- Night lights (~500 m) are coarse for small villages, and light is not income.
- Satellite signals detect land conversion (airports, plants), which is not always local prosperity.

## Next steps

- Verify the leading villages with high-resolution before/after imagery.
- Add Overture Places and Roads (businesses, road access) as validation.
- Test sensitivity to the weights, the 10 km threshold and the minimum population.
- Run building counts for every village with more compute quota.
- Bring in ground data: GST registrations, bank branches, electricity connections.

## Data sources

See [`SOURCES.md`](SOURCES.md).
