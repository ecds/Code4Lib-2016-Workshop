# Create a store for the uploaded GeoTiff

```
curl -u admin:geoserver -v -XPOST -H 'Content-Type: application/xml'\
     -d <coverageStore> \
			<title>Atlanta 1928 Sheet 45</title> \
            <name>atlanta_1928_sheet45.tif</name> \
            <workspace>Code4Lib</workspace> \
            <enabled>true</enabled> \
            <type>GeoTIFF</type> \
    </coverageStore>' \
    http://52.87.207.128:8080/geoserver/rest/workspaces/Code4Lib/coveragestores
```

# Create a layer from the store
```
curl –u admin:geoserver –v –XPUT –H ‘Content-type:text/plain’ \
	–d ‘file:/data/geoserver_data/workspaces/Code4Lib/atlanta_1928_sheet45.tif’ \
   	 http://52.87.207.128:8080/geoserver/rest/workspaces/Code4Lib/coveragestores/external.geotiffconfigure=first
```

# Update the store with the metadata file
```
curl -u admin:geoserver -v -XPOST -H 'Content-Type: application/xml' \
    -d atlanta_1928_sheet45.xml \
    http://52.87.207.128:8080/geoserver/rest/workspaces/Code4Lib/coveragestores/atlanta_1928_sheet45.xml
```
