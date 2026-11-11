# Scientific Data Catalog Guide

Zuhura indexes authoritative planetary datasets from NASA Planetary Data System (PDS) nodes, the PDS Orbital Data Explorer (ODE), USGS Astrogeology Science Center, and ESA Planetary Science Archive (PSA).

## Canonical Data Processing Tiers

To ensure scientific honesty and prevent simulations or models from being confused with direct orbital telemetry, every dataset in Zuhura is cataloged under a strict derivation tier:

1. **Level 1 (Raw Observation)**: Uncalibrated instrument telemetry (e.g. raw PDS EDRs).
2. **Level 2 (Calibrated)**: Radiometrically calibrated data products (e.g. MRO CRISM radiance spectra, HiRISE RDR).
3. **Level 3 (Derived Product)**: Georeferenced, map-projected orthomosaics and digital elevation models (e.g. MOLA MEGDR, Viking MDIM 2.1, Jezero HiRISE mosaic).
4. **Level 4 (Interpretation)**: Peer-reviewed geological maps, fault trace databases, and stratigraphical classifications (e.g. USGS SIM 3292).
5. **Level 5 (Simulation)**: Planetary atmospheric general circulation models (GCMs) and hypothetical thermal/climate simulations.

## Supported Formats
- **Cloud Optimized GeoTIFF (COG)**: Standard for gridded rasters. Enables HTTP range requests from browser clients.
- **Quantized Mesh 1.0**: Hierarchical 3D terrain streaming format used by CesiumJS for Mars topography.
- **GeoParquet**: Columnar spatial storage for planetary feature catalogs (craters, landing sites).
- **STAC 1.0.0**: SpatioTemporal Asset Catalog metadata specification with the Zuhura Mars Extension.
