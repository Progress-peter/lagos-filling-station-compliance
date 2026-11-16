# Lagos Filling Station Siting Compliance

> A spatial compliance audit of 71 filling stations in Ikeja, Kosofe and Eti-Osa LGAs, Lagos, against two siting rules (400 m station spacing on the same side of the road, and a 15 m road setback). Delivered as an ArcGIS Dashboard and a Leaflet.js web map with a live checker for proposed sites.

- **Live Demo (web map):** https://progress-akpan-portfolio.vercel.app/projects/lagos-filling-station-compliance/map/ (mirror on [GitHub Pages](https://progress-peter.github.io/lagos-filling-station-compliance/))
- **Case study:** https://progress-akpan-portfolio.vercel.app/projects/lagos-filling-station-compliance/
- **ArcGIS Dashboard:** https://sambusgeospatial.maps.arcgis.com/apps/dashboards/2626992a813443dda0c586c0152673d2

**Status:** Professional demonstrator. The analysis and dashboard were built at Sambus Geospatial Nigeria Limited as a demonstration for Lagos State urban-planning stakeholders, then rebuilt for the open web with Leaflet. Results are a screening exercise, not a regulatory determination.

## Overview

A filling station stores tens of thousands of litres of petrol, diesel and often cooking gas under one forecourt. When stations are packed close together, a fire or explosion at one can spread to the next. In Port Harcourt, a fire on Eleme Road, Elelenwo, razed two filling stations standing side by side; three people died, 15 others were burned, and 14 cars and two nearby houses were destroyed.

Nigeria's petroleum regulator (formerly the DPR, now the NMDPRA) sets minimum distances a station must keep from other stations, roads and sensitive land uses. This project tests existing stations in Lagos against two of those rules:

- **Station spacing:** at least **400 m** from the nearest station on the **same side of the road**.
- **Road setback:** at least **15 m** from the nearest road.

## Problem statement

Lagos road demand drives a constant stream of new filling stations competing for the same high-traffic frontages. The result is clustering: several stations within a few hundred metres on the same stretch of road, and forecourts pushed right up to the kerb. Siting checks are usually done case by case, on paper, with no shared picture of how close existing stations already are. That makes it hard to see proliferation along a corridor, apply the rules consistently, or show an applicant exactly why a site fails.

## Objectives

1. Map every filling station in Ikeja, Kosofe and Eti-Osa and attach it to its ward and LGA.
2. Measure each station's distance to the nearest station on the same side of the road, and to the nearest road.
3. Classify each station as fully compliant, single violation or double violation, and quantify proliferation (stations within 400 m).
4. Publish the results as an ArcGIS Dashboard and an open web map with a checker for proposed sites.

## Study area

| LGA | Why it was selected | Stations |
|---|---|---|
| **Ikeja** | State capital and administrative centre (Alausa, state secretariat); heavy commuter and civil-service traffic | 17 |
| **Kosofe** | Borders Ikeja; one of the most densely settled parts of Lagos, with residential demand along Ikorodu Road | 15 |
| **Eti-Osa** | Commercial and corporate hub (Victoria Island, Ikoyi, Lekki corridor); rapid growth along the Lekki–Epe Expressway | 39 |

Together they cover an administrative core, a dense residential belt and a fast-growing business corridor.

## Data & sources

| Dataset | Source | Date | Resolution | Format |
|---|---|---|---|---|
| Filling stations (71 points) | Station POI dataset with ward and LGA names, cleaned in ArcGIS Pro | 2026 | Point | Feature class → GeoJSON |
| Road network (~19,500 segments) | OpenStreetMap, clipped to the three LGAs | 2026 extract | Vector | Feature class |
| LGA and ward boundaries | Lagos State administrative boundaries | — | Vector | Feature class |
| Siting thresholds | DPR / NMDPRA filling-station siting guidelines | — | — | 400 m spacing, 15 m setback |
| Live roads (web checker) | OpenStreetMap via the Overpass API | Live | Vector | JSON |
| Basemaps | Esri Light Gray Canvas, World Street Map, World Imagery | Current | Tiles | XYZ |

Distances were measured in a projected coordinate system in ArcGIS Pro; layers were exported to WGS 84 GeoJSON for the web (`docs/data.js`).

## Methodology

```
Station POIs → left/right road-side split → Near (station ↔ station) → Near (station ↔ road)
   → rule flags → overall status → ArcGIS Dashboard + GeoJSON → Leaflet
```

1. **Road-side grouping:** stations in each LGA were split into left and right of the main road, so spacing is only measured between stations a motorist would pass on the same side.
2. **Spacing test:** *Near* within each side group gives the distance to the nearest station; `≥ 400 m` is compliant.
3. **Setback test:** *Near* against the OSM road network gives the distance to the nearest road centreline; `≥ 15 m` is compliant.
4. **Classification:** both rules met = fully compliant; one broken = single violation; both broken = double violation. A 400 m buffer count gives the proliferation score.
5. **Publishing:** the three LGA layers were merged, published to ArcGIS Online and built into a dashboard, then exported to GeoJSON for the web map.
6. **Web checker:** the browser re-implements both tests in plain JavaScript. For a proposed site it fetches roads within 700 m from OpenStreetMap, finds the nearest major road to decide which side the site is on, and measures spacing to stations on that side only. Run against all 71 stations with the project road data, it reproduces the ArcGIS road distances to within 0.2 m and every compliance class.

## Results

| LGA | Stations | Fully compliant | Single violation | Double violation | Fail setback | Fail spacing |
|---|---|---|---|---|---|---|
| Eti-Osa | 39 | 7 | 28 | 4 | 31 | 5 |
| Ikeja | 17 | 0 | 15 | 2 | 17 | 2 |
| Kosofe | 15 | 2 | 11 | 2 | 13 | 2 |
| **Total** | **71** | **9** | **54** | **8** | **61** | **9** |

- Only **9 of 71 stations (13%)** meet both rules; no station in Ikeja does.
- **Road setback is the dominant failure** (61 stations); spacing fails at 9.
- The 8 double violations sit where stations cluster on the same side of busy corridors, which is where proliferation control matters most.

## Limitations

- **Point position drives the setback result.** Many station points sit 0–10 m from the road centreline, likely because POIs are placed at the entrance rather than the forecourt. Digitising forecourt positions from imagery is the main next step and would likely lower the setback failure rate.
- **Centreline, not road edge:** setback is measured to the OSM centreline, without carriageway width or right-of-way.
- **Road-side split is an approximation.** The web checker uses the nearest major road locally, which is less reliable at junctions and roundabouts.
- **Live OSM roads** in the checker may differ slightly from the road extract used in ArcGIS.
- **Two rules only.** Real approvals also consider distance to schools, hospitals and other sensitive uses, plot size, and environmental requirements.

## Technologies

ArcGIS Pro (Near, Buffer, Spatial Join) · ArcGIS Online · ArcGIS Dashboards · ArcPy (GeoJSON export) · Leaflet · JavaScript · OpenStreetMap / Overpass API · Esri basemaps · Vercel · GitHub Pages

## Reproducibility

1. In ArcGIS Pro, load the station points and the OSM road network for the three LGAs in a projected CRS (e.g. WGS 84 / UTM zone 31N).
2. Split stations by side of the main road, run *Near* within each side group (spacing) and against the roads (setback), then flag `< 400 m` and `< 15 m`.
3. Count stations within a 400 m *Buffer* of each station (proliferation), classify the overall status, merge the LGAs and publish to ArcGIS Online for the dashboard.
4. Export the station layer to GeoJSON (WGS 84) and save it as `docs/data.js`.
5. Web map: `docs/` is a static site. Preview with `python -m http.server -d docs 8000`, or publish with GitHub Pages (Settings → Pages → `main`, `/docs`).

```
lagos-filling-station-compliance/
├── README.md
└── docs/
    ├── index.html         # Leaflet web map + proposed-site checker
    ├── data.js            # stations with distances and compliance results (GeoJSON)
    └── vendor/leaflet/    # Leaflet 1.9.4 (BSD-2-Clause)
```

## Author

**Progress Peter Akpan**, GIS & remote sensing analyst, Lagos, Nigeria · [Portfolio](https://progress-akpan-portfolio.vercel.app)

**CV summary:** Audited 71 Lagos filling stations against NMDPRA spacing and setback rules using ArcGIS Pro proximity analysis and OpenStreetMap roads, finding only 13% fully compliant; delivered the results as an ArcGIS Dashboard and a Leaflet web map whose in-browser checker reproduces the ArcGIS road distances to within 0.2 m.
