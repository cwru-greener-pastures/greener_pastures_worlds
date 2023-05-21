# Greener Pastures World Terrain

The Gazebo environments in this package were derived from data available from the USGS.  The heightmaps are generated from GeoTIFF files and the textures are PNG files.  (GeoTIFF files are TIFFs with extended tags including GIS data for locating the data on a Coordinate Representation System [CRS].)  Data from the USGS comes in predefined blocks that must be manipulated.  They can be difficult to manipulate.

The files generated for this package use WGS 84, however, the USGS source data files do use another format.

## QGIS

The QGIS progam is free software that is explicitly for the manipulation of GIS data for the creation of maps.  That is the program used to create the worlds in this package.

This package includes the files from QGIS used to generate the texture PNG and the heightmap GeoTIFF.  It may take a YouTube tutorial to be able to manipulate the QGIS program to update and produce new world files.

### Aquisition

QGIS is available from the standard Ubuntu repository.  It seems to be very far out of date, version 2.18 is on the Ubuntu repo, and the LTS version is 3.16 and the bleeding edge is 3.20.  This utility works well in Windows, though, and the 3.16 install is straightforward from:
>[https://qgis.org/en/site/forusers/download.html](https://qgis.org/en/site/forusers/download.html)

## GDAL

Some basic alteration of the GeoTIFF files produced by QGIS was necessary to get them into the form Gazebo can use.  Gazebo requires heightmap files to be square and of 2n+1 in resolution.  There are layouts in the QGIS files that generate square files of the appropriate sizes.

For example, the Shenandoah farm heightmap is 257x257.  The GeoTIFF produced by QGIS is 1028x1028.  This resizes without interpolation to 257x257.  To accomplish this using the GDAL `gdalwarp` tool, use the following command:
>`gdalwarp -ts 257 257 heightmap_output.tif heightmap_257.tif`

There is one more step, Gazebo requires heightmaps to have one band and the QGIS files have four (even though they are greyscale).  The red band in the file (band 1) is used to create a one band file also using GDAL, though the green and the blue band could be used (band 2 & 3, respectively).  The GDAL tool, `gdal_translate`, is used in the following way:
>`gdal_translate -b 1 -colorinterp gray heightmap_257.tif heightmap.tif`

This can now be accomplished with one command:
>`gdal_translate -outsize 257 257 -b 1 -colorinterp gray heightmap_output.tif heightmap.tif`

In order to shift the heightmap down to the origin, it is necessary to know what the elevation of the heightmap will be at the origint.  This will be at 128,128 in the `heightmap.tif` file.  This can be found using GDAL as well.
>`gdallocationinfo heightmap.tif 128 128`

Use the value from this to shift the `heightmap` `collision` and `visual` entities down that far.  This is in the `<pos>` element of the `.world` file. 

### Acquisition

GDAL is available for the standard Ubuntu repositories.  Like many of the utilities from the Ubuntu repositories, they are a little bit behind the current releases.
>`sudo apt-get install gdal-bin gdal-data`

The tools are automatically installed using `rosdep` for the `greener_pastures_worlds` ROS package.

GDAL is available for Windows (and is included in the the QGIS system). 

## Gazebo

As mentioned, Gazebo requires heightmap files have one band and are of size 2n+1 square.  The GeoTIFF heightmap files contain coordinate information that is used by Gazebo so that GPS simulation will return correct location.

The heightmap files contain all the information to be rendered at the correct scale in Gazebo.  The texture files also contain all the same information, however, Gazebo does not use that information.  The `.world` must include a `size` element for the texture in order for it map the texture over the `heightmap` correctly.  See the GDAL section on how to accomplish this

Another significant issue that seems to have plagued Gazebo for a long time is that the `collision` and the `visual` elements generated from the heightmap images are not alway identical.  If an object sits below the surface or floats in the air, then the glitch is being observed.  

So far, higher resolution data seems to minimize this issue.

## Dataservers

The data for these maps can be served to QGIS.  Below are the server and server types.  These data servers are public and require not credentials to use.

### USGS 1M 3DEP Digital Elevation Maps

Server URL: ```https://elevation.nationalmap.gov/arcgis/services/3DEPElevation/ImageServer/WMSServer```

Layer(s) Used:
- `3DEPElevation`:  The grey scale Digital Elevation Map used to generate the heightmap for Gazebo.
- `3DEPElevation:Aspect Map`: The multi-colored image that indicate the slope and direction at a location. 

### USGS NAIP Plus

Server URL: ```https://imagery.nationalmap.gov/arcgis/services/USGSNAIPPlus/ImageServer/WMSServer```

Layer(s) Used:
- `USGSNAIPPLUS`:  Current highest resolution satellite images.

### USGS Transportation

Server URL: ```https://carto.nationalmap.gov/arcgis/services/transportation/MapServer/WMSServer```

Layer(s) Used:
- `Large and Medium-Scaled-Features`: 
  - All the available layers are included, but not always used.

### USGS Wetlands

Server URL: ```https://fwspublicservices.wim.usgs.gov/wetlandsmapservice/services/WetlandsTopo/WetlandsTopoService/MapServer/WMSServer```

Layer(s) Used: 
- `Inland Waters Topo Service`:  Lakes, rivers, ponds, etc.

### USGS Imperviousness

Server URL: ```https://www.mrlc.gov/geoserver/NLCD_Impervious/wms```

Layer(s) Used:  
- `NLCD_2019_Impervious_L48`: Measurements of ground imperviouness.


## Static Data Sources

The source images can be downloaded and statically added to a project.  These files are large and the process to find and download them can be a bit tedious.  The project was upgraded to use servers to load the most recent data.  This documentation is being kept for completeness.

Most of the source data for the Virginia Tech farm pasture virtual environments came from the USGS.  There is a "Data Download" interface that allows one to search for geographic locations.
>[https://apps.nationalmap.gov/downloader/](https://apps.nationalmap.gov/downloader/)

The topography data is from the "Elevation Products (3DEP)" and the "Elevation Source Data (3DEP) - LIDAR, lfSAR".  The former are typical Digital Elevation Maps (DEMs), the latter can include raw LIDAR source data and DEM images that are generated from the higher resolution source data.

Blacksburg and Shenandoah maps are at approximately 1 m resolution, the highest resolution.  The LIDAR data is ostensibly at higher resolution, but does not actually appear to be that much better.

This data is in the GeoTIFF format that embeds GIS data.

The source imagery is from the Nation Agriculure Imagery Project (NAIP).  It is in the JPEG2000 format that include GIS data for the location of the image.

### Blacksburg

-80.435°, 37.221°

#### 3DEP Data

1m

https://www.sciencebase.gov/catalog/item/5ed2f65482ce2832f0478529

#### 3DEP LIDAR

https://www.sciencebase.gov/catalog/item/60ddf580d34e3a6dca28a0f7
https://www.sciencebase.gov/catalog/item/60ddf57fd34e3a6dca28a0f4
https://www.sciencebase.gov/catalog/item/60ddf57ad34e3a6dca28a0d7
https://www.sciencebase.gov/catalog/item/60ddf57bd34e3a6dca28a0db

#### Imagery

https://www.sciencebase.gov/catalog/item/59eb3810e4b0026a55ff6198
https://www.sciencebase.gov/catalog/item/59eb3810e4b0026a55ff619e


### Middleburg

-77.74°, 38.95°

#### 3DEP

1/3 arcsec

https://www.sciencebase.gov/catalog/item/60c44002d34e86b93898fb5b

#### Imagery

https://www.sciencebase.gov/catalog/item/59eb385be4b0026a55ff6952

### Shenandoah

-79.21°, 37.93°

#### 3DEP

1m

https://www.sciencebase.gov/catalog/item/6067a2e1d34edc0435c0ba20
https://www.sciencebase.gov/catalog/item/6067a2e1d34edc0435c0ba20

#### Imagery

https://www.sciencebase.gov/catalog/item/59eb37a7e4b0026a55ff569c
https://www.sciencebase.gov/catalog/item/59eb37a7e4b0026a55ff5698



# Summary

The workflow through QGIS is functional, but could be improved.  It would be ideal to use GDAL to extract the desired geographic extent for the maps directly.  GDAL also works on JPEG2000 files, which are the files that are used for the texture, or surface coloring, of the heightmaps.  It is likely, though, that QGIS will continue to be useful to find the map extents necessary for finding the map extents.
