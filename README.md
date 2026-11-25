# Journal of Geography Atlas (1902–2026)

Two maps of the complete *Journal of Geography* corpus (10,202 articles, Zotero collection `Journal_of_Geography_v3-v125_complete`).

## 1. Topic landscape — *what the journal thought about* (`index.html`)
10,151 articles (title + first ~1,000 words) embedded with `all-mpnet-base-v2`, projected with UMAP (cosine, n_neighbors=25, seed 42), clustered with HDBSCAN (55 regions, 9 continents). The 2-D projection is mapped to a synthetic lon/lat box (-60…60, -40…40) so standard GIS tools apply. Kernel density of articles = elevation.

| File | Use |
|---|---|
| `terrain/terrain_all.tif`, `terrain/terrain_<decade>s.tif` | Cloud-optimized GeoTIFFs; load in GeoLibre as raster, or set one as **Terrain** source for 3-D (Controls → Terrain → double-click → choose layer). Decade files are scaled comparably. |
| `data/jog_articles_topicspace.parquet` | GeoParquet of all articles: year, decade, cluster/continent names, definition counts, game flag, `zotero_select` link, DOI. Query in GeoLibre via DuckDB. |
| `data/jog_articles_topicspace.pmtiles` | Same points as vector tiles (layer `articles`). |
| `data/jog_labels.pmtiles` | Label points (layers `continents`, `regions`). |
| `data/definitions_trail_points.geojson` | 220 high-confidence "defines geography" articles, chronological. |
| `data/cluster_summary.csv` | Region names, sizes, median year, distinctive terms, sample titles. |

**GeoLibre recipe:** Add Data → URL → `terrain/terrain_all.tif` (style: Terrain colormap) → Controls → Terrain → double-click, select this layer as elevation source, exaggeration ~1.5 → Add Data → `data/jog_articles_topicspace.pmtiles` → Style: categorized on `macro_name` → bind to Time Slider on `year` (Cumulative on) → add `data/jog_labels.pmtiles`.

## 2. Institution map — *where the journal was written* (`institutions.html`)
Author affiliations extracted from article headers/bios (6,201 author-attributed articles; 5,018 of 5,125 stated affiliations geocoded with OpenStreetMap Nominatim), aggregated to H3 resolution-4 hexagons.

| File | Use |
|---|---|
| `data/jog_institutions_hex_total.parquet` / `data/institutions_hex.geojson` | One row per hexagon: `articles_total`, `first_year`, `last_year`, `top_institution`, `institutions`, and cumulative columns `c1902…c2026`. **Extrude on `articles_total`** (or a `cYYYY` column); colour graduated on `first_year`. |
| `data/jog_institutions_hex_years.parquet` | Hexagon × year rows with `articles`, `cumulative_articles` — bind to Time Slider on `year`, Cumulative on, extrude on `cumulative_articles`. |
| `data/jog_institutions_points.parquet` / `.geojson` | 1,796 institution points with article counts and active years. |

**GeoLibre recipe:** Add Data → URL → `data/jog_institutions_hex_years.parquet` → Style → 3D extrusion, field `cumulative_articles`, scale ×5000 → Graduated colour on `year` → Layer menu → Bind to Time Slider → field `year`, Cumulative → play. Keep Terrain off while extruding.

## Provenance and caveats
- Full text covers 10,157 of 10,202 articles (99.6%); 45 have no PDF.
- Front matter (indexes, ads, subscription notices, NCGE business) was kept in the landscape — it forms its own "offshore" islands — and is flagged `is_front_matter`.
- Cluster names were assigned by hand from distinctive-term lists; `cluster_summary.csv` has the terms so they can be revised.
- 44% of articles were HDBSCAN outliers and were assigned to the nearest cluster (`was_noise` flag, `dist_to_centroid` in the parquet).
- Affiliation extraction used a language model over OCR text; institutions without a stated city were snapped to that institution's modal location; 107 author rows (2%) could not be geocoded.
- Terrain is a density surface in an abstract space: distance is semantic similarity, not geography. North/south/east/west have no meaning.
