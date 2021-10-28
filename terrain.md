# Greener Pastures World Terrain

The Gazebo environments in this package were derived from data available from the USGS.  The heightmaps are generated from GeoTIFF files and the textures are PNG files.  (GeoTIFF files are TIFFs with extended tags including GIS data for locating the data on a Coordinate Representation System [CRS].)  Data from the USGS comes in predefined blocks that must be manipulated.  They can be difficult to manipulate.

The files generated for this package use WGS 84, however, the USGS source data files do use another format.

## QGIS

The QGIS progam is free software that is explicitly for the manipulation of GIS data for the creation of maps.  That is the program used to create the worlds in this package.

This package includes the files from QGIS used to generate the texture PNG and the heightmap GeoTIFF.  It may take a YouTube tutorial to be able to manipulate the QGIS program to update and produce new world files.

## GDAL

Some basic alteration of the GeoTIFF files produced by QGIS was necessary to get them into the form Gazebo can use.  Gazebo requires heightmap files to be square and of 2n+1 in resolution.  There are layouts in the QGIS files that generate square files of the appropriate sizes.

For example, the Shenandoah farm heightmap is 257x257.  The GeoTIFF produced by QGIS is 1028x1028.  This resizes without interpolation to 257x257.  To accomplish this:
>`gdal_warp -ts 257 256 heightmap_1028.tiff heightmap_257.tiff`

There is one more step, Gazebo requires heightmaps to have one band and the QGIS files have four (even though they are greyscale).  The first band in the file (for red) is used to create a one band file also using GDAL.
>`gdal_translate -b 1 heightmap_257.tiff heightmap_257b.tiff`

## Gazebo

As mentioned, Gazebo requires heightmap files have one band.  The GeoTIFF files contain coordinate information that is used by Gazebo so that GPS simulation will return correct location.

The files are scaled in Gazebo to be accurately represented.  For instance, in the Blacksburg world, the custom file for the environment is 2056 pixels square.  This The source data is of approximately one meter square resolution.  This is approximate, and the actual resolution is 2138x2138 meters.  This means that the heightmap and texture files must be scaled to 2138.  The Z-axis in the heighmap file, however, is different.  

The height of the heightmap is scaled from minimum to maximum of the source data.  It appears that the heightmap in the file is 0 to 1.  Typically, GeoTIFF files also include metadata about the maximum and minimum to allso the height to be reproduced, however, that data is committed when saving the files in QGIS.  The difference between the maximum and minimum height in the generated heightmap for Blacksburg is 64 m.  That is why the 64 is in the Z scaling location for the heightmaps.  

There does seem to be an issue with elevation that is derived from this.  Elevation seems to be based on the adjusted information in the new heightmap.  It may not be accurate for the absolute elevation of the location.  This may need to be addressed in the future for GPS simulation, though, a constant offset should not actually cause issues.

# Summary

The workflow through QGIS is functional, but could be improved.  It would be ideal to use GDAL to extract the desired geographic extent for the maps directly.  GDAL also works on JPEG2000 files, which are the files that are used for the texture, or surface coloring, of the heightmaps.  It is likely, though, that QGIS will continue to be useful to find the map extents necessary for finding the map extents.
