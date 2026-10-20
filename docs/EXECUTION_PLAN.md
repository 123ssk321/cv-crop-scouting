# Crop Scouting execution plan

## Product boundary

Deliver a scouting-priority map for one crop context: corn-like crop regions in a small Iowa study area. The supported question is where a human should inspect unusual vegetation change. The output describes deviations, usable observation counts and reasons to withhold a ranking. It does not infer disease, drought cause, nutrient deficiency, yield loss or treatment. A declining index is an observation, not a diagnosis.

Use public crop masks to construct analysis regions, not private farm polygons. Label them “crop-mask regions”; they are neither verified fields nor ownership boundaries. Technical completion requires no farmer, drone, field visit or agronomy partner. Optional practitioner feedback can inform workflow fit, but paid adoption and agronomic utility remain conditional on later external evidence.

## Time and budget

Only one portfolio project and one implementation issue are active. Its 16-hour weekly allocation starts at activation. Twenty packages total 640 hours, with another 160 hours reserved: 800 total, including 20% reserve. Four near-term tasks take at most twelve hours each. Sixteen epics become four bounded child slices each, replacing their parent estimates.

| Milestone | Work hours | Reserve | Cumulative | Target |
| --- | ---: | ---: | ---: | --- |
| M1 scope and contracts | 40 | 10 | 50 | Month 1 |
| M2 temporal baseline | 160 | 40 | 250 | Month 4 |
| M3 functional public result | 160 | 40 | 450 | Month 7 |
| M4 robustness and deployment | 160 | 40 | 650 | Month 10 |
| M5 maintenance and handoff | 120 | 30 | 800 | By month 12 |

Including reserve, M3 requires 28.1 weeks, M4 40.6 weeks and completion 50 weeks. Monthly reviews reconcile hours, evidence and remaining uncertainty. Reduce region size or optional model comparisons before cutting evaluation and release tasks. Reserve cannot fund extra crops, mobile applications or purchased imagery.

## Public data and provenance

Use Sentinel-2 surface reflectance accessed through STAC, with NASS crop masks as contextual data. The implementation records exact collection identifiers, license snapshots, access conditions, band scaling, CRS, acquisition time, availability time, source hashes and attribution. Pin completed seasons and cached examples so upstream availability cannot block the demo. Agriculture-Vision is excluded from product development because of the restrictions documented in the source report; PlantVillage and new manual labels are unnecessary.

Cap the study at 100 square kilometers across three spatially separated county areas, with up to six completed seasons and a 30 GB cache. Harmonize all inputs onto a declared 30-meter analysis grid, recording resampling choices instead of assuming annual mask resolutions match. Candidate tiles are selected through fixed coverage rules before inspecting anomaly results. If public access fails, a fully generated raster fixture keeps the workflow runnable, with public real-data evidence marked incomplete.

## Contracts and baseline stack

Use Python, pystac-client, rasterio, xarray, NumPy, SciPy, GeoPandas, FastAPI, SQLite and a Leaflet client. Pin versions and exact STAC endpoints at implementation. Avoid a GPU dependency. Ingestion outputs versioned raster manifests; preprocessing outputs cloud-screened reflectance and NDVI; scoring outputs region-level deviations; reporting produces GeoJSON, CSV and trend evidence. Modules communicate through stable schemas rather than provider-specific objects.

Each observation has item_id, acquired_at, available_at, footprint, CRS, resolution, band_scale, valid_mask and provenance_ref. Each region has region_id, geometry, mask_year and boundary_kind. Each score records as_of, baseline_version, deviation, valid_fraction, sample_count, uncertainty_reason, rank_status and source observation IDs. A ranking is relative to the current eligible cohort, not a probability of disease.

The baseline is a robust seasonal NDVI median and median absolute deviation, stratified by day-of-year windows and region context. Compare recent valid observations against historical seasons using only data available at the requested as_of time. Require at least three recent valid observations and two prior usable seasons; otherwise abstain. Rank negative deviations using magnitude, persistence and usable area, publishing the formula and all components. Compare against a last-observation-minus-previous-observation strawman.

## Leakage and uncertainty controls

Annual crop masks may be released after their represented growing season. Store availability timestamps and forbid future masks in operational replay. A retrospective view may use same-year masks only with a visible hindsight label; it must not be presented as historical forecasting. Operational replay uses masks available by as_of and states that current crop identity is unverified. Never infer planting dates, disease labels or field ownership from crop-mask geometry.

Clouds, shadows, snow, saturation and insufficient coverage produce quality flags. Temporal interpolation is bounded and never bridges large gaps silently. Mask mixed pixels and document edge erosion. Missing dates, low valid fraction and unstable historical baselines produce “insufficient evidence.” Preserve original observations alongside filtered outputs so apparent anomalies can be traced to their source.

## Evaluation and held-out evidence

Freeze spatial blocks and seasons before tuning. Use earlier seasons for historical baselines, 2023 for development and 2024 as a sealed temporal test, subject to data availability verified in M1. Keep one county area entirely out of threshold development. Exclude overlapping tiles and apply a one-kilometer spatial buffer across partitions. Historical observations from a test region may initialize its unsupervised baseline, but its anomalies cannot tune thresholds; document that distinction.

Generate automatically labeled raster sequences with known vegetation drops, gradual shifts, cloud contamination, seasonal offsets and missing acquisitions. Hold out entire perturbation families and magnitude ranges, not just seeds. Test at least 300 region-season sequences. Targets are 85% recall for predefined sustained NDVI drops of at least 0.15 lasting two usable acquisitions, a false-alert rate below 5% on unchanged synthetic controls, and correct abstention in every insufficient-data fixture. Report performance by effect size, cloud fraction and history length with region-bootstrap intervals.

Public real imagery has no assumed stress ground truth. Evaluate acquisition completeness, provenance, cloud-filter effects, sensitivity to resampling, ranking stability under small sensor perturbations, and agreement between independently repeated runs. Report historical rank turnover without calling it agronomic accuracy. Failed synthetic targets trigger a narrower supported anomaly regime; no manual labels or inferred diagnoses are added to rescue performance.

## Public result and deployment

M3 delivers a functioning local map, ranked queue, trends, evidence downloads, generated fixtures, an evaluation card and a reproducible public-data recipe. A user can select an as_of date, inspect why a region ranks highly, see missing-data reasons and export the scouting list. The offline generated example works without credentials; a small licensed real-data cache is distributed only if exact terms allow it.

Package as a localhost container with bounded raster jobs and resumable downloads. Target four CPU cores, 16 GB RAM, a 30 GB cache, and a five-minute processing budget for the documented cached study subset. Cap initial data retrieval at 20 GB and preprocessing experiments at 24 compute hours. Measure peak memory, runtime and egress; reduce the area before considering paid compute. Paid infrastructure budget is zero. Hosted production scheduling and private farm uploads are outside scope.

## Validation, operations and sources

M4 tests three workflow jobs: explain a high rank, recognize an abstention, and export a traceable scouting route list without prescribing treatment. Measure completion times and misinterpretations. Optional agronomist feedback uses public examples; absent external feedback, report usability as a scripted technical walkthrough and demand as unvalidated. No feedback session becomes a labeling exercise.

M5 covers malformed rasters, source outages, cache corruption, migrations, backup restore and release rollback. A four-week maintenance rehearsal measures dependency updates and data-source drift within the budget. Document two hours monthly as estimated ongoing upkeep after handoff. Continue commercially only if subsequent evidence establishes agronomic value and willingness to pay.

This plan follows section 7 of “Research report 2: high-effort computer-vision projects for a solo builder” (2026-10-16) and the published repository `AGENTS.md` protocol. Recorded primary sources include [NASS developer resources](https://data.nass.usda.gov/developer/index.php), [2024 CDL metadata](https://data.nass.usda.gov/Research_and_Science/Cropland/metadata/metadata_Cropland-Data-Layer-2024.htm), [Copernicus STAC](https://documentation.dataspace.copernicus.eu/APIs/STAC.html) and [Agriculture-Vision terms](https://www.agriculture-vision.com/dataset-terms). Verify exact terms and availability during implementation; scope, rights and evaluation changes require Astra review.
