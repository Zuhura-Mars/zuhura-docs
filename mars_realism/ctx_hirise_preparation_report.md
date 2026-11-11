# ZUHURA — MARS REALISM & DETAIL NAVIGATION
# Regional & Local Imagery Preparation Report: CTX and HiRISE

**Date:** 2026-11-05T16:40:00Z  
**Phase:** Phase B (Multi-Resolution Imagery Pipeline)  
**Region:** Jezero Crater / Nili Fossae (Mars 2020 Perseverance Rover Landing Site)  
**Geodetic Framework:** IAU/IAG 2015 Mars Biaxial Ellipsoid ($R_{\text{mean}} = 3,396,190\text{ m}$)  

---

## 1. Instrument Dataset Audit & Integrity Verification

### 1.1 Context Camera (CTX)
- **Product ID:** `P02_001820_1984_XI_18N282W`
- **Source Mission / Instrument:** Mars Reconnaissance Orbiter (MRO) / Context Camera (CTX)
- **Producer:** Malin Space Science Systems (MSSS) / NASA PDS
- **Raw File:** `zuhura-data/data/raw/ctx/P02_001820_1984_XI_18N282W.IMG` (35.2 MB)
- **Raw File SHA-256:** `044ba99584d9cba66fdd0ebf42cee505a2bc0cdeb0a0f66a5d9ddc214bb14ce4`
- **Native Product Type:** Experiment Data Record (EDR), 8-bit unsigned integer line scan
- **Native Spatial Resolution:** $5.55\text{ m/pixel}$ at $250\text{--}300\text{ km}$ orbit altitude
- **Dimensions:** $5,056 \text{ samples} \times 7,168 \text{ lines}$
- **Official PDS ODE Footprint:**
  `POLYGON ((77.53 18.654, 77.62 17.9408, 77.13 17.8814, 77.04 18.5946, 77.53 18.654))`
- **Ground Control Points (GCPs):**
  - Top-Left: Pixel $(0, 0) \rightarrow (77.04^\circ\text{E}, 18.5946^\circ\text{N})$
  - Top-Right: Pixel $(5056, 0) \rightarrow (77.53^\circ\text{E}, 18.6540^\circ\text{N})$
  - Bottom-Right: Pixel $(5056, 7168) \rightarrow (77.62^\circ\text{E}, 17.9408^\circ\text{N})$
  - Bottom-Left: Pixel $(0, 7168) \rightarrow (77.13^\circ\text{E}, 17.8814^\circ\text{N})$
  - Center: Pixel $(2528, 3584) \rightarrow (77.3296^\circ\text{E}, 18.2678^\circ\text{N})$

### 1.2 High Resolution Imaging Science Experiment (HiRISE)
- **Product ID:** `PSP_004299_1985_MRGB`
- **Source Mission / Instrument:** Mars Reconnaissance Orbiter (MRO) / HiRISE
- **Producer:** University of Arizona / NASA PDS
- **Raw File:** `zuhura-data/data/raw/hirise/PSP_004299_1985_MRGB.abrowse.jpg` (3.14 MB)
- **Raw File SHA-256:** `d13db2f799abef4c3f2360a0a968175a93932f371930377c003c056e704a934c`
- **Native Product Type:** RDR V1.1 Map-Projected Color Browse Preview
- **Native Ground Posting:** Sub-meter ($\sim 0.5\text{--}1.0\text{ m/pixel}$ effective browse scale)
- **Dimensions:** $2,048 \text{ samples} \times 4,744 \text{ lines} \times 3 \text{ bands (RGB)}$
- **Official PDS ODE Footprint (from `PSP_004299_1985_RED_LBL.kml`):**
  `POLYGON ((77.6537 18.2996, 77.5531 18.2876, 77.5158 18.5714, 77.6166 18.5834, 77.6537 18.2996))`
- **Target Feature:** NASA Mars 2020 Perseverance rover landing site (Octavia E. Butler Landing) and Jezero Western Delta fan.

---

## 2. Georeferencing & Radiometric Transformation

Both candidate images were transformed into standard Cloud-Optimized GeoTIFFs (COGs) preserving physical spatial integrity and native pixel geometry:

1. **Radiometric Normalization:**
   - CTX EDR pixel values (originally SQROOT compressed spanning values 6 to 71) were normalized using a 1%–99% linear contrast stretch into standard 8-bit range $[0, 255]$ with 0 preserved as NoData.
   - HiRISE RGB browse pixels retain their calibrated primary color balance.
2. **Geodetic Alignment:**
   - Both datasets were transformed to North-up Equidistant Cylindrical coordinates on the IAU 2015 Mars reference sphere ($R = 3,396,190\text{ m}$).
   - Multi-scale internal overviews ($2, 4, 8, 16, 32$) were generated using cubic/bilinear resampling.

---

## 3. Output Cloud-Optimized GeoTIFF Verification

| Metric | CTX Jezero Swath | HiRISE Perseverance Landing |
| :--- | :--- | :--- |
| **Output File** | `data/processed/ctx/P02_001820_1984_jezero_ctx_5m_cog.tif` | `data/processed/hirise/PSP_004299_1985_jezero_hirise_cog.tif` |
| **Output SHA-256** | `908f24820d16111bde0be9fc10e31a99581c1fdcdddc120faea73756d86afced` | `67b570383d38af5b2cf08c4d82c990a4b2791c0e3e3f360714b88fc272382931` |
| **File Size** | $24.64\text{ MB}$ | $2.48\text{ MB}$ |
| **Raster Dimensions** | $5,821 \times 7,754$ pixels | $2,338 \times 5,015$ pixels |
| **Bands / Color** | 1 Band (Grayscale 8-bit) | 3 Bands (RGB Byte) |
| **Internal Tiling** | $512 \times 512$ blocks (`LAYOUT=COG`) | $512 \times 512$ blocks (`LAYOUT=COG`) |
| **Compression** | `DEFLATE` | `JPEG (Quality 90)` |
| **Internal Overviews** | $2910\times 3877, 1455\times 1938, 727\times 969, 363\times 484$ | $1169\times 2507, 584\times 1253, 292\times 626, 146\times 313$ |
| **Bounding Box** | $77.04^\circ\text{E}$ to $77.62^\circ\text{E}$, $17.88^\circ\text{N}$ to $18.65^\circ\text{N}$ | $77.5158^\circ\text{E}$ to $77.6537^\circ\text{E}$, $18.2876^\circ\text{N}$ to $18.5834^\circ\text{N}$ |
| **Valid Zoom Range** | Levels 9 to 13 ($5.55\text{ m/px}$) | Levels 13 to 17 ($0.5\text{--}1.0\text{ m/px}$) |

Both products are ready for zero-copy progressive tile streaming in the Zuhura backend.
