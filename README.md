# `greener_pastures_worlds` Package

The `greener_pasture_worlds` package provides Gazebo simulations of various Virginia Tech Farms.  These virutal environments are intended to support the develop of the Pasture Sanitation Robots (PSRs) which are an element of the Greener Pastures NSF/USDA funded project.  The terrain was generated from data provided from the USGS.  It is Georeference, so simulated GPS coordinates are similar to those expected at the actual VT Farms.

## Launch

There are currently two VT Farms that are included in this project.   They may both be launch using the same launch file in this package, `vt_farm.launch`.  To launch the simulations, use the following command:
>`roslaunch greener_pastures_worlds vt_farm.launch vt_world:=blacksburg`

- `vt_world` -- The specific VT Farm to launch [*`blacksburg`*`|middleburg|shenandoah`]
