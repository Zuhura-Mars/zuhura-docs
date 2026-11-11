# Mars Coordinate Reference Systems & Geodesy

Planetary bodies do not follow Earth terrestrial conventions. Earth GIS tools assume a WGS84 ellipsoid ($a = 6378137\text{ m}$) with mean sea level datums. Mars requires explicit celestial geodetic formulations.

## 1. Reference Ellipsoid (IAU/IAG 2015 WGCCRE)
Zuhura adopts the official IAU/IAG Working Group on Cartographic Coordinates and Rotational Elements (2015) standard:

| Parameter | Value | Definition |
|---|---|---|
| Semi-Major Equatorial Axis ($A$) | $3,396,190.0\text{ m}$ | Equatorial radius |
| Semi-Minor Polar Axis ($C$) | $3,376,200.0\text{ m}$ | Polar radius |
| Flattening ($f$) | $\approx 0.005886$ | $f = (A - C) / A$ |
| Inverse Flattening ($1/f$) | $\approx 169.81$ | Reciprocal flattening |
| Volumetric Mean Radius | $3,389,500.0\text{ m}$ | Spherical approximation |

## 2. Planetocentric vs Planetographic Latitude
- **Planetocentric Latitude ($\phi_c$)**: Angle between the equatorial plane and a line drawn from the center of the planet. Used by modern IAU standards, MRO instruments, and Zuhura.
- **Planetographic Latitude ($\phi_g$)**: Angle between the equatorial plane and the normal vector to the reference surface. Used in historical Viking and Mariner maps.

$$\tan(\phi_c) = \left( \frac{C}{A} \right)^2 \tan(\phi_g) = (1 - f)^2 \tan(\phi_g)$$

At mid-latitudes ($45^\circ$), confusing these two conventions results in a **$\sim 20.4\text{ km}$ spatial offset** on the Martian surface! Zuhura mathematically converts all legacy products during ingestion.

## 3. Longitude Convention: East-Positive
Modern cartography mandates **Planetocentric East-positive longitude ($0^\circ \text{ to } 360^\circ$)**, centered at the Airy-0 crater in Sinus Meridiani ($0.0^\circ$ longitude). Historical West-positive coordinates are normalized using:

$$\lambda_{\text{east}} = (360^\circ - \lambda_{\text{west}}) \pmod{360^\circ}$$

## 4. Topography Reference Datum: Mars Areoid
Mars has no oceans. Elevation ($z = 0$) is defined relative to the **Mars Areoid** (MOLA GMM-2B gravitational equipotential surface) calculated from Mars Global Surveyor Doppler tracking and laser altimeter observations.
