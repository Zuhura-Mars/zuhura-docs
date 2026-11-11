# Zuhura: Mars Digital Twin & Scientific Intelligence

Welcome to the technical knowledge base for **Zuhura**, an interactive 3D digital representation of Mars and planetary research intelligence platform designed for the **NASA Space Apps Challenge 2026**.

## Mission & Vision
Unlike conventional web maps, Zuhura models the physical reality of planetary exploration:
- **Mars-Specific Geodesy**: Strict IAU/IAG 2015 WGCCRE ellipsoidal references and Areoid elevation datums.
- **Traceable Scientific Data**: Deep integration with NASA Planetary Data System (PDS) and USGS Astrogeology.
- **Grounded AI Intelligence**: A domain-specific research assistant grounded in indexed observation footprints, peer-reviewed literature, and validated spatial tools.

## Platform Modules
1. **Planetary Visualization Engine (`zuhura-frontend`)**: Built on CesiumJS, customized with Martian atmospheric scattering and Quantized Mesh terrain streaming.
2. **Backend & Spatial Services (`zuhura-backend`)**: Asynchronous FastAPI service orchestrating spatial queries, coordinate conversions, and AI pipelines.
3. **Contracts & Specifications (`zuhura-api`)**: OpenAPI 3.1 definitions, JSON schemas, and STAC extensions.
4. **Data Pipelines (`zuhura-data`)**: Automated PDS harvesters, COG pyramid converters, and coordinate validators.
