#GDAL Commands
The following is the three step process to get a GeoTiff
##gdalwarp
In this step, you need to reproject your data from any other coordinate system into WGS 1984 (EPSG 3857).
GeoServer can reproject on-the-fly, but you lose some image quality and use server resources for no real reason.
* `-s_srs` is the EPSG code of the original GeoTIFF.
* `-t_srs` is the desired (target) EPSG code.
* `-r` is the resampling method. We've seen the best results using `average`. Run `man gdalwarp` to see the other options.
* Lastly provide the paths to your original GeoTIFF and the path and name of your newly warped GeoTIFF.

###Syntax
```
gdalwarp -s_srs <source ESPG> -t_srs <target EPSG> -r average </path/to/source/geo.tif> </path/to/new/geo.tif>
```
###Example
```
gdalwarp -s_srs EPSG:2240 -t_srs EPSG:3857 -r average /data/atlanta_1928_sheet45.tif /data/tmp/atlanta_1928_sheet45.tif
```
##gdal_translate
GeoTIFF files are organized in 1 pixel strips by default. We want to retile the internal structure of the GeoTIFF to the dimensions of the tiles we need GeoServer to render (256x256).
This improves load time and matches the tile size used by OpenStreetMap and Google.
* `-co` takes a `NAME=VALUE`
* `TILED=YES/NO` the main point of this step is to tile the GeoTIFF
* `BLOCKXSIZE=XXX` the width of the tile
* `BLOCKYSIZE=XXX` the height of the tile
* Provide the path of your temporary file (made in the previous step) and the path and name of what will be your fully processed GeoTIFF.
-- The `-co` referrers to 'creation options'. A list can be found in the 'creation options' section [here](http://www.gdal.org/frmt_gtiff.html)

###Syntax
*Note: we could reproject the map here but we would be overriding our original file.*
```
gdal_translate -co 'TILED=YES/NO' -co 'BLOCKXSIZE=XXX' -co 'BLOCKYSIZE=XXX' </path/to/tmp.tif> </path/to/new.tif>
```
###Example
```
gdal_translate -co 'TILED=YES' -co 'BLOCKXSIZE=256' -co 'BLOCKYSIZE=256' /data/tmp/atlanta_1928_sheet45.tif /data/processed/atlanta_1928_sheet45.tif
```
##gdaladdo
Overviews allow only the needed resolution to be displayed at different zoom levels.
The GeoTIFF format is capable of storing overviews internally (rather than externally like are generated in ArcGIS).
Overviews are lost in reprojection, which is why this step needs to be last.
This step increases file size by about 25%.

* `-r` is the resampling method. We'll use `average` again as we did in step one.
* The list of integral overview levels. Selecting a level value like 2 causes an overview level that is 1/2 the resolution (in each dimension) of the base layer to be computed (see note below).
* The path to the the GeoTIFF created in the previous step

###Syntax
```
gdaladdo -r average </path/to/new.tif> levels
```
###Example
```
gdaladdo -r average /data/processed/atlanta_1928_sheet45.tif 2 4 8 16 32
```
If you run `gdalingo </path/to/new.tif>` after this step you wll see somenting like:

```
Band 1 Block=256x256 Type=Byte, ColorInterp=Red
  Overviews: 2960x2916, 1480x1458, 740x729, 370x365, 185x183
Band 2 Block=256x256 Type=Byte, ColorInterp=Green
  Overviews: 2960x2916, 1480x1458, 740x729, 370x365, 185x183
Band 3 Block=256x256 Type=Byte, ColorInterp=Blue
  Overviews: 2960x2916, 1480x1458, 740x729, 370x365, 185x183
```
The `Overviews` show how many pixels 256x256 represent in the original GeoTIFF.
Our original GeoTIFF was 5920x5831 so the
