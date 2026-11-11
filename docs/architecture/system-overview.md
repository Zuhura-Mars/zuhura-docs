# Zuhura System Architecture

Zuhura is structured as a **Modular Monolith for Application Services** paired with **High-Performance Asynchronous Data Engines**.

## Architecture Components

1. **Planetary Rendering Engine (`zuhura-frontend`)**:
   - WebGL2 CesiumJS client customized with the IAU 2015 Mars Biaxial Ellipsoid.
   - Level-of-Detail (LOD) progressive terrain and imagery streaming via Quantized Mesh and Cloud Optimized GeoTIFF (COG) HTTP range requests.
   - Zustand-driven global state synchronizing camera telemetry, active layers, and feature inspections.

2. **Backend & AI Orchestrator (`zuhura-backend`)**:
   - Python FastAPI modular monolith.
   - Geodesy transformation engine converting between planetographic and planetocentric coordinates.
   - Grounded AI assistant utilizing map viewport context, parameterized spatial tools, and strict provenance tracking.

3. **Storage & Database Tier**:
   - **PostgreSQL 16 + PostGIS 3.4**: High-performance spatial indexing of planetary features, craters, and observation footprints.
   - **pgvector**: Dense vector similarity search for scientific literature and PDS dataset metadata.
   - **MinIO / S3**: Object storage serving COGs and Quantized Mesh terrain tiles.
   - **Redis 7.2**: Caching for tile responses and distributed task broker.
