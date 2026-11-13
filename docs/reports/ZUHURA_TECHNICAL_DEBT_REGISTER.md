# ZUHURA MARS — TECHNICAL DEBT & ARCHITECTURAL DEFECT REGISTER

**Document Identifier**: `ZUHURA_TECH_DEBT_2026_V1`  
**Execution Date**: 2026-11-13  
**Status**: ACTIVE TRACKING & REMEDIATION  
**Authors**: Principal Software Architect, Senior QA Engineer, Lead Systems Engineer  
**Scope**: All 7 Repositories in the `Zuhura-Mars` Ecosystem

---

## 1. Technical Debt Inventory Summary

This register catalogs technical debt, architectural shortcuts, scientific ambiguities, missing test coverage, and incomplete integrations discovered across the seven repositories. Every entry is prioritized by severity and paired with an actionable remediation strategy and validation method.

---

## 2. Technical Debt Register

| ID | Repository | Affected Files | Category | Severity | Description | Scientific / Engineering Implications | Recommended Correction | Validation Method |
|---|---|---|---|---|---|---|---|---|
| **TD-01** | `zuhura-backend` | `src/zuhura/api/v1/*.py`, `src/zuhura/interior/model.py` | Scientific Accuracy | **HIGH** | Absence of standardized epistemic tags on API responses. | Endpoints return numbers without indicating whether they are direct measurements, model estimates, or simulations. | Implement central provenance schema with `OBSERVED`, `MEASURED`, `DERIVED`, `MODEL_ESTIMATED`, `SIMULATED`, `PROVISIONAL`, `UNVERIFIED`. | Unit tests asserting provenance schema on every endpoint. |
| **TD-02** | `zuhura-backend` | `src/zuhura/interior/model.py` | Scientific Geophysics | **HIGH** | Mars interior model exposes only one default profile without comparing published literature models. | Users cannot compare the 2021 large-core model ($R=1,830\text{ km}$) with the 2023 basal molten silicate layer model ($R=1,650\text{ km}$). | Add multi-paper model selector exposing Stähler et al. (2021), Samuel et al. (2023), and Khan et al. (2023). | Integration test verifying both models with exact parameter outputs. |
| **TD-03** | `zuhura-backend` | `src/zuhura/navigation/router.py` | Planetary Robotics | **HIGH** | Rover mobility cost model uses simplified slope penalties without Bekker soil mechanics or solar energy models. | Routes may recommend paths across high-slip sand ripples or underestimate battery consumption during low sun angles. | Implement modular mobility simulation: wheel traction, slip risk, solar energy availability, and mission feasibility. | Rover route test verifying energy demand, slip score, and terrain hazard count. |
| **TD-04** | `zuhura-backend` | `src/zuhura/pinpoint/engine.py` | Computer Vision | **MEDIUM** | Pinpoint engine uses basic edge/color histograms without modular feature extraction or inconclusive fallback. | Risk of low-confidence false positives when presented with generic dust plains. | Implement multi-stage CV pipeline: illumination normalization, ORB feature extraction, multi-scale similarity, and explicit inconclusive handling. | Benchmark test with synthetic and rover images testing top-1/top-5 accuracy and conclusive thresholds. |
| **TD-05** | `zuhura-backend` | `src/zuhura/ai/tools.py`, `scientist_engine.py` | AI Intelligence | **MEDIUM** | AI Scientist lacks deep research question decomposition and automated scientific paper retrieval. | AI responses require manual prompt engineering for complex comparative studies (e.g., Jezero vs Gale Crater). | Add research decomposition tool, literature retrieval tool, citation formatter, and reproducible manifest generator. | AI scientist integration test comparing two Martian craters. |
| **TD-06** | `zuhura-data` | `src/zuhura_data/catalog/builder.py` | Data Infrastructure | **MEDIUM** | STAC metadata does not clearly differentiate nominal camera ground sample distance (GSD) from effective optical resolution. | Can give false impression that high-resolution data exists where tiles are empty or heavily degraded by dust storms. | Add explicit `effective_resolution_m` vs `nominal_sensor_gsd_m` fields in STAC item properties. | Data pipeline validation test checking STAC item schema. |
| **TD-07** | `zuhura-frontend` | `src/components/`, `src/store/` | Researcher UX | **MEDIUM** | Lack of persistent researcher workspace (saved bookmarks, geodesic measurements, layer comparison). | Researchers cannot save analysis sessions or export scientific findings across reloads. | Build Researcher Workspace: bookmarks, geodesic ruler, annotations, layer split-view, and multi-format exports (GeoJSON/CSV). | Frontend vitest component test verifying bookmark and export actions. |
| **TD-08** | `zuhura-backend` | `src/zuhura/core/logging.py`, `src/zuhura/api/main.py` | Observability | **MEDIUM** | Missing Prometheus metrics endpoint (`/metrics`) and structured JSON logging. | Production operations cannot monitor request latencies, tile throughput, or error rates in Grafana. | Add `prometheus-client` middleware exposing request counters, latency histograms, and tile cache hit rates. | Prometheus endpoint scrape test returning HTTP 200 with standard metric lines. |
| **TD-09** | `zuhura-frontend` | `Dockerfile` | Security / DevSecOps | **LOW** | Frontend Dockerfile runs `npm run dev` in production image instead of optimized Nginx static server. | Increased container attack surface and suboptimal static file delivery performance. | Refactor Dockerfile to multi-stage build serving `/dist` via `nginx:alpine` running as unprivileged user. | Docker build and curl test verifying static asset delivery. |
| **TD-10** | `zuhura-api` | `openapi/zuhura-v1.yaml` | API Governance | **LOW** | Missing automated live contract validation in CI pipeline. | Risk of subtle contract drift when backend endpoints evolve without matching YAML updates. | Add schema validation and route coverage check in CI test suite. | Automated CI step verifying live endpoint parity with OpenAPI spec. |

---

## 3. Debt Retirement Strategy & Phasing

* **Phase 2 (Immediate)**: Remediate **TD-01** (Scientific Truth Engine) & **TD-02** (InSight geophysics model reconciliation).
* **Phase 3 (Immediate)**: Remediate **TD-03** (Advanced Rover Mobility Simulation & Bekker slip model).
* **Phase 4 (Immediate)**: Remediate **TD-04** (Modular Visual Intelligence Engine & conclusive calibration).
* **Phase 5 (Immediate)**: Remediate **TD-05** (Scientific AI Research Agent & research decomposition).
* **Phase 6 & 7 (Immediate)**: Remediate **TD-06** (Planetary Data Infrastructure) & **TD-07** (Researcher Workspace).
* **Phase 8 & 9 (Immediate)**: Remediate **TD-08** (Prometheus & Observability), **TD-09** (Container Hardening), and **TD-10** (Contract Testing).

*Certified by the Lead Systems Engineer, Zuhura Mars.*
