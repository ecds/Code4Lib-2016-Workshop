#GDAL Commands
##gdalwarp
```
gdalwarp -s_srs EPSG:2240 -t_srs EPSG:3857 -r average </path/to/source/geo.tif> </path/to/new/geo.tif>
```
```
gdalwarp -s_srs EPSG:2240 -t_srs EPSG:3857 -r average atlanta_1928_sheet45.tif tmp/atlanta_1928_sheet45.tif
```
##gdal_translate
```
gdal_translate -co 'TILED=YES' -co 'BLOCKXSIZE=256' -co 'BLOCKYSIZE=256' </path/to/tmp.tif> </path/to/new.tif>
```
```
gdal_translate -co 'TILED=YES' -co 'BLOCKXSIZE=256' -co 'BLOCKYSIZE=256' tmp/atlanta_1928_sheet45.tif processed/atlanta_1928_sheet45.tif
```

##gdaladdo
```
gdaladdo -r average </path/to/new.tif> 2 4 8 16 32
```
```
gdaladdo -r average processed/atlanta_1928_sheet45.tif 2 4 8 16 32
```