# AI Scientific Intelligence Methodology

The **Ask Zuhura** assistant is an evidence-grounded scientific intelligence system designed specifically for planetary researchers, educators, and mission planners.

## Core Principles
1. **Zero Raw SQL Execution**: The assistant interacts with databases exclusively through strictly validated, parameterized Pydantic tool schemas.
2. **Context-Aware Spatial Ingestion**: The system continuously ingests camera position (lat, lon, altitude) and active layer IDs from the Cesium globe.
3. **Mandatory Citations & Provenance**: Every scientific claim must be supported by an indexed dataset ID or peer-reviewed DOI.
4. **Transparent Data Gaps**: The assistant explicitly acknowledges observation limits (e.g. indicating when a location only has 100 m/px THEMIS data rather than 25 cm/px HiRISE coverage).

## Controlled Spatial Tools
- `get_elevation_at_point(lat, lon)`: Samples surface elevation relative to MOLA Areoid.
- `get_elevation_profile(start_point, end_point, samples)`: Calculates transect cross-sections.
- `search_features(bbox, min_diameter_km)`: Retrieves verified IAU craters and formations.
- `query_dataset_catalog(bbox, category)`: Lists available observation footprints.
- `search_mission_traverses(lat, lon)`: Locates nearby surface landers or rover traverses.
