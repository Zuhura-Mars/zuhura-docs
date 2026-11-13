# ZUHURA MARS — NEXT-GENERATION SCIENTIFIC UPGRADE ROADMAP

**Document Identifier**: `ZUHURA_ROADMAP_2026_V1`  
**Execution Date**: 2026-11-13  
**Status**: APPROVED FOR IMMEDIATE EXECUTION  
**Authors**: Principal Software Architect, Senior Planetary Scientist, Engineering Lead  
**Scope**: 7-Repository Execution Matrix (Phases 2 through 12)

---

## 1. Upgrade Philosophy & Quality Gates

The upgrade follows this inviolable lifecycle:
$$\text{PRESERVE} \longrightarrow \text{AUDIT} \longrightarrow \text{VALIDATE} \longrightarrow \text{IMPROVE} \longrightarrow \text{EXTEND} \longrightarrow \text{INTEGRATE} \longrightarrow \text{TEST} \longrightarrow \text{DOCUMENT} \longrightarrow \text{DEPLOY}$$

### Strict Operational Directives:
1. **Zero Functionality Loss**: Never delete, disable, simplify, or overwrite existing working features.
2. **Backward Compatibility**: All existing API endpoints, routes, components, and schemas remain functional.
3. **No Synthetic Certainty**: Clearly distinguish direct observations from model-derived estimates and simulations.
4. **Verified Execution**: Every milestone requires concrete test passes before git commit.

---

## 2. Multi-Phase Execution Schedule

```
Phase 1: Architecture, Security & Technical Debt Audit [COMPLETED]
   |
   +--> Phase 2: Scientific Truth Engine & Provenance Architecture
   |       - Epistemic classification taxonomy (OBSERVED, MEASURED, DERIVED, etc.)
   |       - InSight interior model reconciliation (Stähler 2021 vs Samuel 2023)
   |       - Provenance tracking & uncertainty propagation
   |
   +--> Phase 3: Advanced Rover Mobility Simulation
   |       - Terrain hazard scoring, slope & roughness analysis
   |       - Wheel traction & Bekker soil slip estimation
   |       - Solar energy availability & communication visibility
   |       - Separation of geometric, engineering, energy, and mission feasibility
   |
   +--> Phase 4: Mars Visual Intelligence Engine
   |       - Modular CV pipeline (illumination normalization, ORB features)
   |       - Benchmark dataset with ground truth rover sites
   |       - Conclusive vs inconclusive confidence calibration
   |
   +--> Phase 5: Scientific AI Research Agent
   |       - Research question decomposition (e.g. Jezero vs Gale Crater)
   |       - Scientific paper retrieval & citation formatting
   |       - Reproducible analysis execution manifests
   |
   +--> Phase 6: Planetary Data Infrastructure
   |       - STAC metadata & COG support
   |       - Effective vs nominal camera resolution distinction
   |       - Checksums & automated missing-tile detection
   |
   +--> Phase 7: Researcher Workspace
   |       - Saved bookmarks, geological annotations, geodesic measurement tools
   |       - Layer comparison / synchronized visualization
   |       - Scientific exports (GeoJSON, CSV, JSON, NetCDF summaries)
   |
   +--> Phase 8: Production Engineering & Observability
   |       - Structured JSON logging, Prometheus metrics, Grafana configs
   |       - API rate limiting & security hardening
   |       - Non-root container builds & health check verification
   |
   +--> Phase 9: Scientific Performance Engineering
   |       - Latency benchmarking, spatial indexing budgets, tile throughput
   |
   +--> Phase 10: Frontend Experience
   |       - Charcoal/slate/rust/copper theme refinement
   |       - Uncertainty visualization, accessibility, responsive HUDs
   |
   +--> Phase 11: Automated Validation & Cross-Repository Testing
   |       - Unit, integration, contract, and regression test suites
   |
   +--> Phase 12: GitHub Synchronization & Release Management
           - Clean commits, documentation synchronization, release publishing
```

---

## 3. Phase Specifications & Deliverables

### Phase 2: Scientific Truth Engine
- **Files**: `zuhura-backend/src/zuhura/provenance/`, `zuhura-backend/src/zuhura/interior/model.py`, `zuhura-api/schemas/`
- **Deliverables**:
  - Centralized provenance model (`EpistemicClassification`: `OBSERVED`, `MEASURED`, `DERIVED`, `MODEL_ESTIMATED`, `SIMULATED`, `PROVISIONAL`, `UNVERIFIED`).
  - InSight multi-model selector (Stähler et al. 2021 1,830 km core vs Samuel et al. 2023 1,650 km core with 160 km basal silicate slag layer).
  - Uncertainty bounds $[min, max]$ on all physical properties.

### Phase 3: Advanced Rover Mobility Simulation
- **Files**: `zuhura-backend/src/zuhura/navigation/mobility_model.py`, `router.py`, `zuhura-frontend/src/components/PinpointRouteHUD.tsx`
- **Deliverables**:
  - Modular mobility assessment: slope gradient, roughness TRI, wheel traction, slip risk, energy consumption (Wh/km), solar insolation cost, communication line-of-sight.
  - Feasibility breakdown: Geometric feasibility, Engineering mobility feasibility, Energy feasibility, Mission operational feasibility.
  - NASA rover profiles: Perseverance, Curiosity, Dune Buggy, Aerial Scout.

### Phase 4: Mars Visual Intelligence Engine
- **Files**: `zuhura-backend/src/zuhura/pinpoint/cv_pipeline.py`, `engine.py`, `tests/`
- **Deliverables**:
  - Multi-stage CV pipeline: illumination normalization, ORB feature descriptors, multi-scale pyramid matching, confidence calibration.
  - Conclusive evidence threshold: returns explicit `INCONCLUSIVE_EVIDENCE` status when similarity is insufficient.
  - Ground-truth benchmark dataset with Perseverance (Jezero) and Curiosity (Gale) reference panoramas.

### Phase 5: Scientific AI Research Agent
- **Files**: `zuhura-backend/src/zuhura/ai/research_agent.py`, `tools.py`, `scientist_engine.py`
- **Deliverables**:
  - Research question decomposition engine (sub-question breakdown, dataset requirements, methodology, calculations, synthesis).
  - Scientific paper retrieval & citation generator with DOI references.
  - Reproducible scientific execution manifest.

### Phase 6: Planetary Data Infrastructure
- **Files**: `zuhura-data/src/zuhura_data/catalog/builder.py`, `models/scientific_product.py`, `specs/`
- **Deliverables**:
  - STAC Item & Collection enhancements with explicit `effective_resolution_m` vs `nominal_sensor_gsd_m`.
  - Cloud Optimized GeoTIFF (COG) validation and missing-tile detection.
  - Data integrity SHA-256 verification.

### Phase 7: Researcher Workspace
- **Files**: `zuhura-frontend/src/components/ResearcherWorkspace.tsx`, `MeasurementTools.tsx`, `store/workspaceStore.ts`
- **Deliverables**:
  - Interactive geodesic measurement tool (ruler, surface distance, polygon area).
  - Geological annotations and bookmarks saved to localStorage.
  - Multi-format exporter: GeoJSON, CSV, JSON scientific reports.
  - Split-screen / layer comparison toggle.

### Phase 8: Production Engineering & Observability
- **Files**: `zuhura-backend/src/zuhura/core/metrics.py`, `logging.py`, `main.py`, `zuhura-frontend/Dockerfile`
- **Deliverables**:
  - Prometheus `/metrics` endpoint with request counters and latency histograms.
  - Structured JSON logging.
  - API rate limiting via slowapi.
  - Hardened Dockerfile configurations.

### Phase 9: Scientific Performance Engineering
- **Files**: `zuhura-backend/tests/performance/`, `zuhura-frontend/src/lib/`
- **Deliverables**:
  - Measured latencies across Cesium rendering, hover telemetry (<10ms), routing (<200ms), and spatial queries.
  - Verification against strict performance budgets.

### Phase 10: Frontend Experience Refinement
- **Files**: `zuhura-frontend/src/`
- **Deliverables**:
  - Retain Charcoal (`#0a0d12`), Slate (`#161b22`), Rust (`#c1440e`), Copper (`#d97706`).
  - Error boundary, empty states, uncertainty indicators, accessible aria labels.

### Phase 11 & 12: Automated Validation & Git Management
- **Files**: All 7 repositories
- **Deliverables**:
  - Full test suites execution across backend (pytest), frontend (vitest), data (pytest).
  - Production build clean execution.
  - Non-destructive git commits and pushes to `https://github.com/Zuhura-Mars`.

---

*Certified autonomously by the Lead Engineering Team, Zuhura Mars.*
