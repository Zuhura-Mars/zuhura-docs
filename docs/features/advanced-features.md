# Advanced Scientific Exploration Features

**Platform:** Zuhura Mars Digital Twin  
**Version:** 1.0.0  
**Updated:** 2026-11-13  

---

## 1. X-Ray Mars — Interactive Planetary Interior

### 1.1 Geophysical Grounding & NASA InSight Constraints
Mars interior structure is derived from the NASA InSight seismological mission (SEIS instrument, Station `XB.ELYSE` in Elysium Planitia, 2018–2022) combined with MRO radio science gravity fields.

| Structural Layer | Depth Horizon | Thickness | Density | Temperature | State of Matter | Seismic Evidence |
|---|---|---|---|---|---|---|
| **Upper Fractured Crust** | 0 – 10 km | 10 ± 2 km | 2,550 kg/m³ | 220 K | Solid (Porous) | P-to-s receiver functions |
| **Middle Crystalline Crust** | 10 – 24 km | 14 ± 4 km | 2,850 kg/m³ | 350 K | Solid | P-to-s conversions |
| **Lower Crust & Moho** | 24 – 42 km | 18 ± 8 km | 3,100 kg/m³ | 550 K | Solid | 2-layer vs 3-layer Moho models |
| **Upper Mantle Lithosphere** | 42 – 450 km | 408 ± 50 km | 3,450 kg/m³ | 1,150 K | Solid Rigid Lid | Triplicated P & S arrivals |
| **Convecting Silicate Mantle**| 450 – 1,520 km | 1,070 ± 60 km| 3,750 kg/m³ | 1,750 K | Solid (Convecting) | Thermodynamic geotherms |
| **Basal Molten Silicate Slag**| 1,520 – 1,680 km| 160 ± 40 km | 4,100 kg/m³ | 2,050 K | Partially Molten | Diffracted SKS & Pdiff waves |
| **Liquid Metallic Core** | 1,680 – 3,389.5 km| 1,709.5 ± 50 km| 6,100 kg/m³ | 2,300 K | Liquid Metallic | ScS core reflections ($V_s = 0$) |

### 1.2 Interactive Cutaway Capabilities
- Adjustable cutaway slice angles (0° to 180°).
- Layer highlight, visibility toggle, and geomechanical telemetry inspection.
- Hemispheric crustal dichotomy explanation (Northern Lowlands ~30 km vs Southern Highlands 55–75 km).

---

## 2. Optimize Navigation — Terrain-Aware Traverse Planning

### 2.1 Multi-Objective Cost Model
Traverses are evaluated using an A* pathfinding algorithm over the MOLA DEM grid. Edge weights account for:
$$\text{Cost} = \Delta s \times \left(1 + k_{\text{slope}} \cdot \left(\frac{\theta}{\theta_{\text{ref}}}\right)^2\right) \times \left(1 + k_{\text{roughness}} \cdot \frac{\text{TRI}}{\text{TRI}_{\text{ref}}}\right)$$

If local slope $\theta > \theta_{\text{max}}$, the segment is marked as an impassable obstacle or high-risk hazard.

### 2.2 Vehicle Mobility Profiles
- **Perseverance Class:** Max climbable slope 18°, critical slope 25°, step obstacle 0.40 m.
- **Curiosity Class:** Max climbable slope 20°, critical slope 28°, step obstacle 0.50 m.
- **Crew Traverser:** Max climbable slope 14°, critical slope 20°, step obstacle 0.30 m.
- **Scout Hexacopter:** Max climbable slope 45°, critical slope 60°, obstacle 5.0 m.

---

## 3. Multiresolution Mars Map & High-Zoom Clarity

- **Tier 1 (Global):** USGS Viking MDIM 2.1 Natural Color Mosaic at 232 m/px (Levels 0–10).
- **Tier 2 (Regional):** MRO Context Camera (CTX) Jezero Mosaic at 5.55 m/px (Levels 9–15).
- **Tier 3 (Local Target):** MRO HiRISE Orthorectified DTM over Perseverance ellipse at 0.50 m/px (Levels 13–18).
- **Dynamic LOD Telemetry:** Header and bottom overlay dynamically compute instantaneous ground resolution based on camera altitude.

---

## 4. Hover Intelligence

- Continuous mouse-move and mobile touch telemetry.
- 200ms debounce with in-memory LRU spatial caching.
- Reports IAU feature name, distance, diameter, MOLA Areoid elevation, geological era, and available imagery footprints.

---

## 5. Location Pinpoint — Image Localization

- Upload format validation: JPEG, PNG, WebP up to 10 MB.
- Morphological signature matching against cataloged ground truth sites (Jezero Western Delta, Gale Mount Sharp, Vera Rubin Ridge, Olympus Mons, Valles Marineris).
- Ranked candidate sites with coordinates, similarity scores (0.0 to 1.0), and uncertainty bounds.
- Seamless "Fly to Location" integration with the Cesium 3D camera.

---

## 6. Mars Region Discovery — Geological Segmentation

- Topographic clustering engine grouping terrain cells by elevation relief, slope variance, and roughness.
- Produces valid, closed polygon rings formatted as GeoJSON FeatureCollections.
- Distinguishes **Official IAU Nomenclature** from **Provisional Platform-Generated Names** (`PROVISIONAL_PLATFORM_GENERATED`).
- GeoJSON export download.
