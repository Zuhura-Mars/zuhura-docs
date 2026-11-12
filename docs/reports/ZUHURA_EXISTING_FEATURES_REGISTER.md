# ZUHURA MARS — EXISTING FEATURE PRESERVATION REGISTER

**Document Version:** 1.0.0  
**Classification:** Baseline Technical Audit & Preservation Ledger  
**Date:** 2026-11-13  
**Status:** ACTIVE & BINDING  

---

## 1. Executive Summary & Preservation Mandate

In accordance with Section 0 of the Master Autonomous Development Directive, this document forms the canonical preservation register for the **Zuhura Mars Digital Twin & Scientific Intelligence Platform**.

**Preservation Principle:**  
`Preserve → Understand → Extend → Improve → Integrate → Test`

Every existing capability documented below must remain functional and verified through automated regression suites and interactive validation throughout all development milestones.

---

## 2. Feature Inventory & Preservation Matrix

| ID | Existing Feature | Current Location | Current Behavior | Dependencies | Current Status | Preservation Test |
|---|---|---|---|---|---|---|
| **FEAT-001** | Global Mars 3D Viewer & Biaxial Ellipsoid | `zuhura-frontend/src/components/MarsGlobe.tsx`, `lib/cesium/marsEllipsoid.ts` | Renders interactive Mars 3D globe with IAU/IAG 2015 WGCCRE biaxial datum ($a=3396190$ m, $b=3376200$ m) | CesiumJS 1.121, `vite-plugin-cesium`, WebGL | **Working** | `npm run test:run` (`geodesy.test.ts`), visual rendering check |
| **FEAT-002** | Viking MDIM 2.1 Basemap Layer | `zuhura-frontend/src/lib/cesium/imageryProviders.ts`, `zuhura-backend/src/zuhura/api/v1/tiles.py` | Serves orthorectified true-color optical tiles at 232 m/px (levels 0–10) | Local Viking tile cache / COG, FastAPI | **Working** | `GET /api/v1/tiles/viking/0/0/0.jpg`, `imageryProviders.test.ts` |
| **FEAT-003** | Jezero Crater CTX Context Mosaic Layer | `zuhura-frontend/src/lib/cesium/imageryProviders.ts`, `zuhura-backend/src/zuhura/api/v1/tiles.py` | Overlays calibrated MRO Context Camera imagery at 5.55 m/px (levels 9–15) | Local CTX COG & tile cache, FastAPI | **Working** | `GET /api/v1/tiles/ctx/9/365/229.png` |
| **FEAT-004** | Jezero HiRISE Targeted High-Res Layer | `zuhura-frontend/src/lib/cesium/imageryProviders.ts`, `zuhura-backend/src/zuhura/api/v1/tiles.py` | Overlays sub-meter MRO HiRISE imagery at 0.50 m/px (levels 13–18) over Perseverance ellipse | Local HiRISE COG & tile cache, FastAPI | **Working** | `GET /api/v1/tiles/hirise/13/5852/3673.png` |
| **FEAT-005** | MOLA MEG128 Scientific Topographic Colormap | `zuhura-frontend/src/lib/cesium/imageryProviders.ts`, `zuhura-backend/src/zuhura/api/v1/tiles.py` | Dynamic Cloud-Optimized GeoTIFF streaming with color ramp referenced to Mars Areoid | Local MOLA COG, GDAL, FastAPI | **Working** | `GET /api/v1/tiles/cog/0/0/0.png` |
| **FEAT-006** | Cesium Quantized-Mesh Terrain Engine | `zuhura-backend/src/zuhura/api/v1/tiles.py` | Streams Cesium quantized-mesh 1.0 binary terrain tiles for Jezero, Gale, Valles Marineris, Olympus Mons | Local quantized-mesh tile pyramids | **Working** | `GET /api/v1/tiles/terrain/jezero/layer.json` |
| **FEAT-007** | Live Telemetry & Geodetic Header | `zuhura-frontend/src/components/Header.tsx`, `store/globeStore.ts` | Displays live planetocentric coordinates (POS), MOLA Areoid elevation (AREOID), camera altitude (ALT) | Zustand `globeStore`, Cesium mouse move event | **Working** | UI verification on cursor movement over globe |
| **FEAT-008** | Scientific Layer Management Panel | `zuhura-frontend/src/components/LayerPanel.tsx`, `store/layerStore.ts` | Allows toggling layer visibility, adjusting opacity sliders, and inspecting metadata | Zustand `layerStore`, React Lucide icons | **Working** | Toggle layers, adjust opacity slider, verify Cesium layer updates |
| **FEAT-009** | Terrain Preset Camera Navigation | `zuhura-frontend/src/components/LayerPanel.tsx`, `MarsGlobe.tsx` | Smooth flight to preset locations: Global Mars, Jezero Crater, Gale Crater, Valles Marineris, Olympus Mons | Cesium camera flyTo | **Working** | Select terrain preset, verify camera flyTo coordinate & altitude |
| **FEAT-010** | Dynamic Solar Illumination Controls | `zuhura-frontend/src/components/LayerPanel.tsx`, `MarsGlobe.tsx`, `zuhura-backend/src/zuhura/api/v1/illumination.py` | Simulates solar shadows, terminator line, azimuth and elevation sun angles | Cesium directional light, backend solar-position API | **Working** | Toggle solar lighting in LayerPanel, test `/api/v1/illumination/solar-position` |
| **FEAT-011** | Authoritative Feature Inspector | `zuhura-frontend/src/components/FeatureInspector.tsx`, `store/globeStore.ts` | Displays details for selected landmark (name, IAU type, elevation, coordinates, description) | Zustand store, `spatial/features` | **Working** | Click landmark pin (Jezero, Olympus Mons), verify inspector popup |
| **FEAT-012** | Great-Circle Geodesic Transect Profiler | `zuhura-frontend/src/components/TransectProfileDrawer.tsx`, `store/terrainAnalysisStore.ts` | Calculates spherical linear interpolation (Slerp) elevation profile along great circle between two stations | FastAPI `/api/v1/spatial/terrain/profile`, MOLA DEM | **Working** | `pytest test_stage8_terrain_api.py`, execute transect query in UI |
| **FEAT-013** | Point Terrain Morphometry Analysis | `zuhura-frontend/src/components/FeatureInspector.tsx`, `zuhura-backend/src/zuhura/terrain_intelligence/` | Computes metric latitude-scaled Horn slope, aspect azimuth, TRI roughness, TPI index | GDAL, numpy, FastAPI `/api/v1/spatial/terrain/point-analysis` | **Working** | `pytest test_stage8_terrain_intelligence_gates.py` |
| **FEAT-014** | Head-to-Head Terrain Site Comparison | `zuhura-backend/src/zuhura/api/v1/terrain.py` | Compares two landing sites for separation distance, elevation delta, slope percent, morphometry | FastAPI `/api/v1/spatial/terrain/compare` | **Working** | `POST /api/v1/spatial/terrain/compare` unit test |
| **FEAT-015** | Regional Morphometric Statistics | `zuhura-backend/src/zuhura/api/v1/terrain.py` | Computes statistical distributions (min, max, mean, relief, roughness) across circular footprint | FastAPI `/api/v1/spatial/terrain/statistics` | **Working** | `POST /api/v1/spatial/terrain/statistics` unit test |
| **FEAT-016** | Mars AI Scientist Conversational Assistant | `zuhura-frontend/src/components/AIPanel.tsx`, `zuhura-backend/src/zuhura/ai/` | Grounded conversational AI for planetary science, with citations, executed tools, streaming | Google Gemini API / fallback, SessionManager | **Working** | `pytest test_mars_ai_scientist.py`, `aiScientist.test.ts` |
| **FEAT-017** | AI Grounding & Scientific Tool Dispatcher | `zuhura-backend/src/zuhura/ai/tools.py` | 18+ strict Pydantic scientific tools (elevation, morphometry, catalog search, solar angle, etc.) | Pydantic v2, Python 3.14, GDAL | **Working** | `test_mars_ai_scientist.py` tool dispatch checks |
| **FEAT-018** | Coordinate Conversion Engine | `zuhura-backend/src/zuhura/geodesy/transformations.py`, `zuhura-frontend/src/lib/geodesy/` | Bi-directional conversion between Planetocentric, Planetographic, and Cartesian MCMF coordinates | PyProj, IAU 2015 constants | **Working** | `pytest test_transformations.py`, `geodesy.test.ts` |
| **FEAT-019** | Scientific Data Catalog & STAC APIs | `zuhura-backend/src/zuhura/api/v1/catalog.py` | Unified catalog search across MGS MOLA, MRO CTX, MRO HiRISE, Mars Odyssey THEMIS | STAC 1.0 JSON, Pydantic schemas | **Working** | `GET /api/v1/catalog/datasets`, `GET /api/v1/catalog/search` |
| **FEAT-020** | Mars Missions & Landing Sites Telemetry | `zuhura-backend/src/zuhura/api/v1/missions.py` | Returns landing coordinates, science objectives, instruments for Viking 1/2, Pathfinder, MER, MSL, M2020 | Static mission registry | **Working** | `GET /api/v1/missions` |
| **FEAT-021** | Raster Technical Metadata & Streaming | `zuhura-backend/src/zuhura/api/v1/rasters.py` | Exposes raster CRS, resolution, nodata value, band count, and byte-range streaming | GDAL, rasterio | **Working** | `GET /api/v1/rasters/{product_id}/metadata` |
| **FEAT-022** | System Health & Infrastructure Telemetry | `zuhura-backend/src/zuhura/api/v1/health.py` | Reports service status, geodetic datum parameters, and connected infrastructure components | FastAPI endpoint | **Working** | `GET /api/v1/health` |
| **FEAT-023** | Planetary Data Pipeline & Validation | `zuhura-data/src/zuhura_data/pipelines/` | Validates PDS EDR/RDR metadata, spatial extent, nodata consistency for planetary rasters | Pydantic, GDAL | **Working** | `pytest zuhura-data/tests` (33 passed) |
| **FEAT-024** | Docker & Deployment Orchestration | `zuhura/docker/docker-compose.yml`, `init-db.sql` | Orchestrates PostgreSQL + PostGIS 16-3.4, Redis 7.2, Backend, Frontend | Docker Compose, PostGIS IAU:49902 CRS | **Working** | Syntax and service definition validation |
| **FEAT-025** | Visual Acceptance Automation Scripts | `scripts/run_phase_c_visual_acceptance.py`, `scripts/benchmark_stage8_terrain_api.py` | Automated Selenium headless screenshots and API latency percentiles (P50/P90/P99) | Python, Selenium, Requests, NumPy | **Working** | Run benchmark scripts against live or mock services |
| **FEAT-026** | X-Ray Mars Planetary Interior 3D Cutaway | `zuhura-frontend/src/components/XRayMarsModal.tsx`, `zuhura-backend/src/zuhura/interior/` | Interactive 3D cutaway and radial cross-section of crust, lithosphere, mantle, CMB slag layer, and liquid core with InSight seismic data | FastAPI `/api/v1/spatial/interior/`, InSight SEIS citations | **Working** | `pytest test_advanced_features.py`, `advancedFeatures.test.ts` |
| **FEAT-027** | Optimize Navigation Terrain-Aware Traverse Router | `zuhura-frontend/src/components/RoutePlanningDrawer.tsx`, `zuhura-backend/src/zuhura/navigation/` | A* pathfinding over MOLA DEM with metric slope cost, roughness, rover mobility constraints, and GeoJSON export | FastAPI `/api/v1/spatial/navigation/optimize-route` | **Working** | `pytest test_advanced_features.py`, Cesium 3D route rendering |
| **FEAT-028** | Hover Intelligence Contextual Mars Inspector | `zuhura-frontend/src/components/HoverIntelligenceTooltip.tsx`, `zuhura-backend/src/zuhura/api/v1/hover.py` | High-speed cached geographic inspection on hover/tap (elevation, IAU features, geological era, imagery tier) | FastAPI `/api/v1/spatial/hover/inspect`, LRU cache | **Working** | `pytest test_advanced_features.py`, `advancedFeatures.test.ts` |
| **FEAT-029** | Location Pinpoint Landscape Image Localization | `zuhura-frontend/src/components/LocationPinpointModal.tsx`, `zuhura-backend/src/zuhura/pinpoint/` | Secure upload and morphological matching of Mars landscape photos against ground truth rover stations | FastAPI `/api/v1/spatial/pinpoint/analyze-image` | **Working** | `pytest test_advanced_features.py`, upload verification |
| **FEAT-030** | Mars Region Discovery Geological Segmentation | `zuhura-frontend/src/components/RegionDiscoveryPanel.tsx`, `zuhura-backend/src/zuhura/segmentation/` | Topographic clustering into candidate geological units with closed polygon boundaries and provisional names | FastAPI `/api/v1/spatial/regions/discover` | **Working** | `pytest test_advanced_features.py`, Cesium 3D polygon overlay |
| **FEAT-031** | Multiresolution Dynamic LOD Resolution Badge | `zuhura-frontend/src/components/MarsGlobe.tsx` | Telemetry HUD calculating dynamic ground resolution in m/px and reporting active imagery tier | Cesium camera height listener | **Working** | Camera altitude change check in UI |

---

## 3. Maintenance Protocol

1. Before any code is committed, every feature listed above must be validated against its respective preservation test.
2. If any test fails, work on new features must halt immediately until the regression is diagnosed and fixed.
3. No existing endpoint or interface may be renamed or removed.
