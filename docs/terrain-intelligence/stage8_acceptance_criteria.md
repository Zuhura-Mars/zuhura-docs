# ZUHURA — STAGE 8: SCIENTIFIC TERRAIN INTELLIGENCE
# Acceptance Criteria and Verification Specifications

**Document Version:** 1.0.0  
**Phase:** Stage 8 Phase 1 (Acceptance Specifications)  
**Status:** PROPOSED / PENDING APPROVAL  
**Platform:** Zuhura Integrated Planetary Digital Twin  
**Verification Method:** Automated Pytest Integration + Headless Selenium Visual Acceptance  

---

## 1. Acceptance Framework Overview

Stage 8 acceptance requires passing all 10 verification test suites defined below. Testing encompasses mathematical correctness, geodetic precision, boundary safety, provenance transparency, performance latency, and interactive frontend visual rendering.

A test is classified as **PASSED** only when automated verification confirms numerical results within defined physical tolerances and visual evidence verifies distortion-free rendering.

---

## 2. Detailed Acceptance Test Suites

### Suite 1: Geodetic & Coordinate Transformations (AC-01)
- **Requirement:** Compliance with IAU/IAG 2015 Mars Biaxial Ellipsoid ($A = 3,396,190\text{ m}$, $C = 3,376,200\text{ m}$).
- **Test Cases:**
  1. **Planetographic to Planetocentric Latitude Conversion:**
     - At Equator ($0^\circ$): $\Delta \phi = 0.0^\circ$ ($< 10^{-6\circ}$).
     - At Poles ($\pm 90^\circ$): $\Delta \phi = 0.0^\circ$ ($< 10^{-6\circ}$).
     - At $\phi = 45.0^\circ$: Correctly calculates planetocentric latitude $\phi_c \approx 44.6626^\circ$ ($\Delta \phi \approx 0.3374^\circ$, corresponding to $\approx 20.4\text{ km}$ physical displacement).
  2. **Longitude Normalization:**
     - Negative longitudes (e.g. $-59.2^\circ$) normalize to $[0, 360)$ East ($300.8^\circ\text{E}$).
     - Values $\ge 360^\circ$ wrap correctly (e.g. $437.58^\circ \rightarrow 77.58^\circ\text{E}$).
  3. **Great-Circle Distance Verification:**
     - Distance between $(0^\circ\text{N}, 0^\circ\text{E})$ and $(0^\circ\text{N}, 90^\circ\text{E})$ on Mars equals $\frac{\pi}{2} \times 3,389.5\text{ km} \approx 5,324.22\text{ km}$ ($\pm 0.5\text{ km}$).

---

### Suite 2: Known Elevation Reference Points & Datum Accuracy (AC-02)
- **Requirement:** Point elevations query directly from MOLA GMM-3 areoid datum without synthetic interpolation bias.
- **Reference Landmark Verification Matrix:**
  | Landmark | Target Latitude | Target Longitude | Expected Elevation Range (Areoid) | Expected Result | Tolerance |
  | :--- | :---: | :---: | :---: | :---: | :---: |
  | **Olympus Mons Summit** | $18.65^\circ\text{N}$ | $226.20^\circ\text{E}$ | $+20,000\text{ m}$ to $+21,250\text{ m}$ | $+20,015\text{ m}$ to $+21,249\text{ m}$ | $\pm 10\text{ m}$ |
  | **Jezero Crater Floor** | $18.38^\circ\text{N}$ | $77.58^\circ\text{E}$ | $-2,700\text{ m}$ to $-2,550\text{ m}$ | $-2,648\text{ m}$ | $\pm 5\text{ m}$ |
  | **Gale Crater (Curiosity Landing)**| $-5.37^\circ\text{N}$ | $137.81^\circ\text{E}$ | $-4,550\text{ m}$ to $-4,400\text{ m}$ | $-4,503\text{ m}$ | $\pm 5\text{ m}$ |
  | **Valles Marineris Floor** | $-13.90^\circ\text{N}$ | $300.80^\circ\text{E}$ | $-5,600\text{ m}$ to $-5,400\text{ m}$ | $-5,511\text{ m}$ | $\pm 10\text{ m}$ |
  | **Hellas Planitia Basin Floor** | $-42.50^\circ\text{N}$ | $70.50^\circ\text{E}$ | $-8,250\text{ m}$ to $-8,000\text{ m}$ | $\le -8,100\text{ m}$ | $\pm 10\text{ m}$ |
  | **Areoid Zero-Datum Plain** | Variable | Variable | Near $0\text{ m}$ | Zero-crossing confirmed | $\pm 2\text{ m}$ |

---

### Suite 3: Slope and Aspect Derivative Calculations (AC-03)
- **Requirement:** Slope and aspect correctly computed using the Metric Latitude-Scaled Horn $3\times 3$ operator.
- **Test Cases:**
  1. **Synthetic Planar Ramp Test:**
     - Feed a synthetic $3\times 3$ grid with a known physical gradient (e.g. $10.0^\circ, 30.0^\circ, 45.0^\circ$).
     - Calculated slope must match true slope to within $\pm 0.01^\circ$.
  2. **Aspect Directionality Test:**
     - North-dipping ramp: Aspect $= 0.0^\circ$ ($\pm 0.1^\circ$).
     - East-dipping ramp: Aspect $= 90.0^\circ$ ($\pm 0.1^\circ$).
     - South-dipping ramp: Aspect $= 180.0^\circ$ ($\pm 0.1^\circ$).
     - West-dipping ramp: Aspect $= 270.0^\circ$ ($\pm 0.1^\circ$).
  3. **Latitudinal Metric Tensor Scaling Test:**
     - Compute slope on an east-west gradient at Equator ($0^\circ\text{N}$) and at high latitude ($75^\circ\text{N}$).
     - The calculation must account for $\Delta x = \Delta x_0 \cos(75^\circ) \approx 0.2588 \times \Delta x_0$.
     - Unscaled algorithm will underestimate slope by $386\%$; scaled algorithm must produce the correct physical gradient within $\pm 0.1\%$.
  4. **Flat Surface Handling:**
     - Surface with zero elevation gradient returns `slope_deg: 0.0`, `aspect_deg: null`, `is_flat: true`.

---

### Suite 4: Terrain Transect & Geodesic Profiling (AC-04)
- **Requirement:** Elevation profile sampled along great-circle arc with monotonic distance accumulation.
- **Test Cases:**
  1. **Distance Monotonicity:**
     - For all samples $i \in [1, N-1]$: $d_i > d_{i-1}$ and $d_{N-1} = D_{\text{total}} \pm 0.001\text{ km}$.
  2. **Endpoint Elevation Fidelity:**
     - $z(d_0)$ matches point elevation query at $P_1$ ($\pm 0.1\text{ m}$).
     - $z(d_{N-1})$ matches point elevation query at $P_2$ ($\pm 0.1\text{ m}$).
  3. **Real Planetary Transect Verification:**
     - Transect across Jezero Western Delta fan (length $\approx 30\text{ km}$):
       * Resolves delta front scarp drop $> 100\text{ m}$.
       * Peak slope along scarp $> 10^\circ$.
     - Transect across Valles Marineris canyon chasma (length $\approx 200\text{ km}$):
       * Total relief $> 5,000\text{ m}$.
       * Wall slopes $> 20^\circ$.

---

### Suite 5: Local Morphometry & Kernel Statistics (AC-05)
- **Requirement:** Focal kernel metrics accurately differentiate smooth plains from rugged crater terrain.
- **Test Cases:**
  1. **Isidis Planitia (Smooth Plain):**
     - TRI $< 10.0\text{ m}$.
     - Local relief $< 25.0\text{ m}$.
     - TPI class: `"flat_plain"`.
  2. **Jezero Crater Rim (Rugged Scarp):**
     - TRI $> 40.0\text{ m}$.
     - Local relief $> 200.0\text{ m}$.
     - TPI class: `"ridge"` or `"upper_slope"`.
  3. **Kernel Size Scaling:**
     - Supported kernel sizes: $3\times 3$, $5\times 5$, $7\times 7$. Non-supported sizes (e.g. 4) return HTTP 400.

---

### Suite 6: Spatial Comparison Endpoint (AC-06)
- **Requirement:** Accurate differential analysis between two distinct planetary sites.
- **Test Cases:**
  1. **Separation Distance:** Great-circle distance between Jezero ($18.38^\circ\text{N}, 77.58^\circ\text{E}$) and Gale ($-5.37^\circ\text{N}, 137.81^\circ\text{E}$) equals $3,842.15\text{ km} \pm 10\text{ km}$.
  2. **Elevation Delta:** $\Delta z = z_{\text{Gale}} - z_{\text{Jezero}} = -4,503 - (-2,648) = -1,855\text{ m}$ ($\pm 5\text{ m}$).
  3. **Comparative Output:** Response returns side-by-side table of elevation, slope, aspect, and roughness.

---

### Suite 7: Boundary & NoData Handling (AC-07)
- **Requirement:** Safe, graceful handling of coordinate boundaries and edge cases.
- **Test Cases:**
  1. **Equatorial Coverage Boundary ($|\phi| \le 88.0^\circ$):** Served via global MOLA COG v3 (`nodata_status: false`).
  2. **Polar Boundary ($|\phi| > 88.0^\circ$):** Automatically routes to Polar Stereographic DEM (`megt_n_128_1.tif` or `megt_s_128_1.tif`) with explicit CRS metadata in response.
  3. **Antimeridian Crossing ($180^\circ\text{E}$):** Transect profiles traversing $180^\circ\text{E}$ sample smoothly without index out-of-range exceptions.
  4. **Prime Meridian Crossing ($0^\circ / 360^\circ\text{E}$):** Profile traversing from $359^\circ\text{E}$ to $1^\circ\text{E}$ computes shortest distance arc ($2^\circ$), not $358^\circ$ retrograde arc.
  5. **Invalid Inputs:** Latitude $> 90^\circ$ or $<-90^\circ$ returns HTTP 422 / 400.

---

### Suite 8: Scientific Provenance & Uncertainty Reporting (AC-08)
- **Requirement:** Complete metadata transparency in every response payload.
- **Verification Criteria:**
  - `source_dataset`: Declares exact dataset identifier (`zuhura-mola-global-v3-cog`).
  - `source_resolution_m`: Declares nominal posting ($463.0577\text{ m}$).
  - `metric_cell_size_m`: Declares physical ground grid size at query latitude ($\Delta x = 463.06 \cos\phi$, $\Delta y = 463.06$).
  - `vertical_datum`: Declares `"Mars Areoid (MOLA GMM-3)"`.
  - `vertical_accuracy_m`: Declares nominal uncertainty bounds ($\pm 1.0\text{ m}$ on flat plains, $\pm 10-30\text{ m}$ on steep slopes).
  - `validation_status`: Set to `"scientifically_validated"`.

---

### Suite 9: API Contract & Performance Benchmarks (AC-09)
- **Requirement:** High-throughput, low-latency execution under concurrent load.
- **Performance Thresholds:**
  | Endpoint | Metric | Acceptance Threshold | Target Performance |
  | :--- | :--- | :---: | :---: |
  | `POST /point-analysis` | 95th Percentile Response Time | $< 50\text{ ms}$ | $< 25\text{ ms}$ |
  | `POST /profile` (50 samples) | 95th Percentile Response Time | $< 120\text{ ms}$ | $< 60\text{ ms}$ |
  | `POST /profile` (200 samples)| 95th Percentile Response Time | $< 250\text{ ms}$ | $< 120\text{ ms}$ |
  | `POST /statistics` | 95th Percentile Response Time | $< 100\text{ ms}$ | $< 45\text{ ms}$ |
  | Memory RSS Growth | Peak memory increase during 100 queries | $< 50\text{ MB}$ | $< 15\text{ MB}$ |

---

### Suite 10: Frontend Visualization & Visual Acceptance (AC-10)
- **Requirement:** Visual rendering of terrain intelligence tools in Cesium globe.
- **Verification Criteria:**
  1. **Transect Polyline:** Polyline renders cleanly on 3D terrain globe using Cesium `clampToGround: true`.
  2. **Interactive Chart Drawer (`TransectProfileDrawer.tsx`):**
     - Cross-section curve renders elevation (Y-axis) vs distance (X-axis).
     - Hovering over chart displays point elevation, distance, and slope tooltip.
     - Hover pin updates camera position / surface marker on 3D globe.
  3. **Feature Inspector Morphometry Card (`FeatureInspector.tsx`):**
     - Displays live Slope, Aspect, TRI Roughness, and TPI classification.
  4. **Layer Panel Tool Toggle (`LayerPanel.tsx`):**
     - Provides intuitive buttons to activate/deactivate Terrain Analysis mode.
  5. **Visual Capture Evidence:** Headless Selenium test suite captures screenshots demonstrating all 4 UI features.

---

## 3. Pass / Fail Acceptance Gate Matrix

| Test Suite ID | Test Suite Name | Execution Phase | Gate Requirement |
| :---: | :--- | :---: | :---: |
| **AC-01** | Geodetic & Coordinate Transformations | Phase 2 | **100% Pass Required** |
| **AC-02** | Known Elevation Reference Points | Phase 2 | **100% Pass Required** |
| **AC-03** | Slope and Aspect Derivatives | Phase 2 | **100% Pass Required** |
| **AC-04** | Terrain Transect & Geodesic Profiling | Phase 2 | **100% Pass Required** |
| **AC-05** | Local Morphometry & Kernels | Phase 2 | **100% Pass Required** |
| **AC-06** | Spatial Comparison Endpoint | Phase 3 | **100% Pass Required** |
| **AC-07** | Boundary & NoData Handling | Phase 3 | **100% Pass Required** |
| **AC-08** | Scientific Provenance Reporting | Phase 3 | **100% Pass Required** |
| **AC-09** | API Performance Benchmarks | Phase 3 | **100% Pass Required** |
| **AC-10** | Frontend Visualization Acceptance | Phase 5 | **100% Pass Required** |
