# ZUHURA — STAGE 8: SCIENTIFIC TERRAIN INTELLIGENCE
# API Design and Interface Specifications

**Document Version:** 1.0.0  
**Phase:** Stage 8 Phase 1 (API Design Specification)  
**Status:** PROPOSED / PENDING APPROVAL  
**Platform:** `zuhura-backend` (FastAPI / ASGI)  
**OpenAPI Specification:** 3.1.0  

---

## 1. Design Philosophy & Endpoint Architecture

The Stage 8 Scientific Terrain Intelligence API is designed to provide high-performance, geodetically sound, and reproducible terrain analysis endpoints. It extends the existing `/api/v1/spatial/` namespace with dedicated `/terrain/` routes while preserving complete backward compatibility with existing Stage 1–7 endpoints (`/api/v1/elevation`, `/api/v1/spatial/elevation-profile`, `/api/v1/tiles/*`).

### Core Design Principles:
1. **Zero-Copy & Zero-Spike Memory Footprint:** Endpoints query localized raster blocks directly from Cloud-Optimized GeoTIFFs (COGs) via GDAL window reads, preventing multi-gigabyte memory allocations.
2. **Geodetic Rigor:** All slope, aspect, and profile distance calculations are mathematically scaled to the local metric tensor on the IAU 2015 Mars biaxial ellipsoid.
3. **Transparent Scientific Provenance:** Every response payload provides metadata declaring the source dataset ID, nominal and metric grid posting, vertical datum, calculation method, and uncertainty bounds.
4. **Pydantic v2 Schema Enforcement:** Strict request validation prevents out-of-bounds coordinates, polar singularity errors, and denial-of-service sampling counts.
5. **Sub-100ms Latency Targets:** Point analysis responds in $< 30\text{ ms}$; 50-point transect profiles resolve in $< 50\text{ ms}$.

---

## 2. API Endpoint Directory

| HTTP Method | Route | Description | Expected Latency |
| :--- | :--- | :--- | :---: |
| `POST` | `/api/v1/spatial/terrain/point-analysis` | Computes elevation, slope (degrees), aspect, TRI, and TPI at a point. | $< 25\text{ ms}$ |
| `POST` | `/api/v1/spatial/terrain/profile` | Generates a great-circle transect profile with elevations, gradients, and relief summary. | $< 60\text{ ms}$ |
| `POST` | `/api/v1/spatial/terrain/statistics` | Computes focal morphometric kernel statistics over a localized area. | $< 50\text{ ms}$ |
| `POST` | `/api/v1/spatial/terrain/compare` | Compares two distinct surface locations (elevation delta, distance, gradient, morphometry). | $< 35\text{ ms}$ |

---

## 3. Detailed Endpoint Contracts

### 3.1 Point Terrain Analysis (`POST /api/v1/spatial/terrain/point-analysis`)

Computes instantaneous terrain derivatives at a specified Martian coordinate using a $3\times 3$ to $7\times 7$ window from the MOLA global COG with latitude-dependent metric tensor scaling.

#### Request Schema (`PointAnalysisRequest`):
```json
{
  "latitude": 18.38,
  "longitude": 77.58,
  "include_morphometry": true,
  "kernel_size": 3
}
```

- `latitude` (float, required): Planetocentric latitude ($-90.0 \le \phi \le +90.0$).
- `longitude` (float, required): East-positive longitude ($-180.0 \le \lambda \le +360.0$).
- `include_morphometry` (bool, optional, default `true`): If `true`, returns TRI, TPI, and local relief.
- `kernel_size` (int, optional, default `3`): Odd integer kernel size (`3`, `5`, or `7`).

#### Response Schema (`PointAnalysisResponse`):
```json
{
  "coordinate": {
    "latitude": 18.38,
    "longitude": 77.58,
    "latitude_type": "planetocentric",
    "longitude_convention": "east_positive"
  },
  "elevation": {
    "elevation_m": -2648.0,
    "vertical_datum": "Mars Areoid (MOLA GMM-3)",
    "vertical_accuracy_m": 1.0,
    "nodata_status": false
  },
  "derivatives": {
    "slope_deg": 4.12,
    "aspect_deg": 134.8,
    "aspect_cardinal": "SE",
    "is_flat": false,
    "calculation_method": "Horn (1981) 3x3 finite-difference with metric latitude scaling",
    "metric_cell_size_m": {
      "dx_m": 439.42,
      "dy_m": 463.06
    }
  },
  "morphometry": {
    "kernel_size": 3,
    "tri_m": 18.4,
    "tpi_m": -2.6,
    "tpi_class": "flat_plain",
    "local_relief_m": 58.0
  },
  "provenance": {
    "source_dataset": "zuhura-mola-global-v3-cog",
    "product_id": "mola_global_meg128_cog",
    "source_resolution_m": 463.0577,
    "native_crs": "Mars_simpCyl_c180 (IAU:49900 Sphere R=3396km)",
    "query_timestamp": "2026-11-05T15:30:00Z",
    "validation_status": "scientifically_validated"
  }
}
```

---

### 3.2 Geodesic Transect Profile (`POST /api/v1/spatial/terrain/profile`)

Extracts an elevation and gradient cross-section along the true great-circle geodesic between two coordinates using spherical linear interpolation (Slerp) and bilinear sub-pixel elevation sampling.

#### Request Schema (`TerrainProfileRequest`):
```json
{
  "start_point": {
    "latitude": 18.25,
    "longitude": 77.35
  },
  "end_point": {
    "latitude": 18.50,
    "longitude": 77.80
  },
  "num_samples": 50,
  "include_derivatives": true,
  "interpolation_method": "bilinear"
}
```

- `start_point`, `end_point` (Point, required): Transect endpoints.
- `num_samples` (int, optional, default `50`): Number of stations ($5 \le N \le 500$).
- `include_derivatives` (bool, optional, default `true`): Include pointwise slope and aspect.
- `interpolation_method` (string, optional, default `"bilinear"`): `"bilinear"` or `"nearest"`.

#### Response Schema (`TerrainProfileResponse`):
```json
{
  "transect_summary": {
    "total_distance_km": 30.45,
    "num_samples": 50,
    "sample_step_m": 621.43,
    "min_elevation_m": -2890.0,
    "max_elevation_m": -2110.0,
    "elevation_range_relief_m": 780.0,
    "mean_elevation_m": -2580.4,
    "cumulative_elevation_gain_m": 124.0,
    "cumulative_elevation_loss_m": 656.0,
    "mean_slope_deg": 2.45,
    "max_slope_deg": 14.82
  },
  "samples": [
    {
      "step": 0,
      "distance_km": 0.0,
      "latitude": 18.25,
      "longitude": 77.35,
      "elevation_m": -2110.0,
      "slope_deg": 12.3,
      "aspect_deg": 48.2
    },
    {
      "step": 1,
      "distance_km": 0.62,
      "latitude": 18.2551,
      "longitude": 77.3592,
      "elevation_m": -2185.2,
      "slope_deg": 8.1,
      "aspect_deg": 52.0
    }
  ],
  "provenance": {
    "source_dataset": "zuhura-mola-global-v3-cog",
    "vertical_datum": "Mars Areoid (MOLA GMM-3)",
    "geodetic_reference": "IAU/IAG 2015 Mars Biaxial Ellipsoid",
    "validation_status": "scientifically_validated"
  }
}
```

---

### 3.3 Local Morphometric Statistics (`POST /api/v1/spatial/terrain/statistics`)

Extracts statistical distributions of elevation, slopes, and roughness across a circular geographic footprint or bounding box.

#### Request Schema (`TerrainStatisticsRequest`):
```json
{
  "center_point": {
    "latitude": 18.38,
    "longitude": 77.58
  },
  "radius_km": 15.0
}
```

#### Response Schema (`TerrainStatisticsResponse`):
```json
{
  "query_region": {
    "type": "circle",
    "center": {"latitude": 18.38, "longitude": 77.58},
    "radius_km": 15.0,
    "area_km2": 706.86
  },
  "elevation_statistics": {
    "cell_count": 3312,
    "min_m": -2742.0,
    "max_m": -2410.0,
    "mean_m": -2634.8,
    "median_m": -2648.0,
    "std_dev_m": 64.2,
    "relief_m": 332.0
  },
  "slope_statistics": {
    "mean_deg": 2.1,
    "median_deg": 1.6,
    "std_dev_deg": 1.9,
    "max_deg": 18.4,
    "trafficability_grade": "low_slope_favorable"
  },
  "roughness_indices": {
    "mean_tri_m": 12.3,
    "max_tri_m": 48.7,
    "vector_ruggedness_measure": 0.0018
  },
  "provenance": {
    "source_dataset": "zuhura-mola-global-v3-cog",
    "source_resolution_m": 463.0577,
    "vertical_datum": "MOLA GMM-3 Areoid"
  }
}
```

---

### 3.4 Spatial Terrain Comparison (`POST /api/v1/spatial/terrain/compare`)

Performs a head-to-head morphometric comparison between two candidate sites (e.g. comparing Jezero Delta vs Gale Crater floor).

#### Request Schema (`TerrainCompareRequest`):
```json
{
  "site_a": {
    "name": "Jezero Western Delta",
    "latitude": 18.38,
    "longitude": 77.58
  },
  "site_b": {
    "name": "Gale Mount Sharp Base",
    "latitude": -5.37,
    "longitude": 137.81
  }
}
```

#### Response Schema (`TerrainCompareResponse`):
```json
{
  "separation": {
    "great_circle_distance_km": 3842.15,
    "elevation_delta_m": 2366.0,
    "net_gradient_percent": 0.062,
    "site_b_relative_to_a": "lower"
  },
  "comparison_matrix": {
    "site_a": {
      "name": "Jezero Western Delta",
      "elevation_m": -2648.0,
      "slope_deg": 4.12,
      "aspect_deg": 134.8,
      "tri_roughness_m": 18.4,
      "tpi_class": "flat_plain"
    },
    "site_b": {
      "name": "Gale Mount Sharp Base",
      "elevation_m": -282.0,
      "slope_deg": 11.45,
      "aspect_deg": 312.0,
      "tri_roughness_m": 42.1,
      "tpi_class": "lower_slope"
    }
  },
  "provenance": {
    "source_dataset": "zuhura-mola-global-v3-cog",
    "vertical_datum": "MOLA GMM-3 Areoid"
  }
}
```

---

## 4. Python Pydantic Models Implementation

```python
from typing import List, Optional, Literal, Dict, Any
from pydantic import BaseModel, Field, field_validator


class Coordinate(BaseModel):
    latitude: float = Field(..., ge=-90.0, le=90.0, description="Planetocentric latitude in degrees")
    longitude: float = Field(..., description="East-positive longitude in degrees (-180 to 360)")

    @field_validator("longitude")
    @classmethod
    def normalize_longitude(cls, v: float) -> float:
        norm = v % 360.0
        return norm if norm >= 0.0 else norm + 360.0


class PointAnalysisRequest(BaseModel):
    latitude: float = Field(..., ge=-90.0, le=90.0)
    longitude: float = Field(...)
    include_morphometry: bool = Field(default=True)
    kernel_size: int = Field(default=3, description="Kernel window size: 3, 5, or 7")

    @field_validator("kernel_size")
    @classmethod
    def validate_kernel(cls, v: int) -> int:
        if v not in [3, 5, 7]:
            raise ValueError("kernel_size must be 3, 5, or 7")
        return v


class TerrainProfileRequest(BaseModel):
    start_point: Coordinate
    end_point: Coordinate
    num_samples: int = Field(default=50, ge=5, le=500, description="Sample count (5 to 500)")
    include_derivatives: bool = Field(default=True)
    interpolation_method: Literal["bilinear", "nearest"] = Field(default="bilinear")


class TerrainStatisticsRequest(BaseModel):
    center_point: Coordinate
    radius_km: float = Field(..., gt=0.1, le=250.0, description="Sampling radius in km (0.1 to 250 km)")


class TerrainCompareRequest(BaseModel):
    site_a: Coordinate
    site_b: Coordinate
    name_a: Optional[str] = "Site A"
    name_b: Optional[str] = "Site B"
```

---

## 5. Error Handling & HTTP Status Codes

| Code | Condition | Response Payload Example |
| :---: | :--- | :--- |
| **`200 OK`** | Successful calculation. | Full JSON response matching schema. |
| **`400 Bad Request`** | Invalid parameter (e.g. `kernel_size=4`, `num_samples=1000`, `radius_km=0`). | `{"detail": "kernel_size must be 3, 5, or 7"}` |
| **`422 Unprocessable`** | Missing required coordinate fields or data type mismatch. | Standard FastAPI field validation details. |
| **`503 Service Unavailable`** | Underlying COG file unreadable or disk I/O failure. | `{"detail": "Global MOLA COG dataset temporarily unavailable"}` |

### Boundary Condition Rules:
- **Polar Latitudes ($|\phi| > 88.0^\circ$):** The endpoint automatically switches internal GDAL reads to the polar stereographic DEMs (`megt_n_128_1.tif` or `megt_s_128_1.tif`) and reports the polar CRS in the provenance metadata block.
- **Antimeridian Crossing ($180^\circ\text{E}$ or $0^\circ/360^\circ\text{E}$):** The longitudinal sampling engine seamlessly modulo-wraps index columns, eliminating negative index crashes.
