# Using GeoServer's REST Interface to Add Scanned Maps
Below are the steps to make a GeoTIFF available in GeoServer using [GeoServer's REST API](http://docs.geoserver.org/stable/en/user/rest/). The examples use the command line tool [cURL](https://en.wikipedia.org/wiki/CURL#Examples_of_cURL_use_from_command_line) . Go [here for  full explanation of cURL's options](https://curl.haxx.se/docs/manpage.html).

In the examples we use `https://mygoeserver.org`. Replace that with the url for your GeoServer. You should use `https` as you will be providing user credentials. In the examples we use the default credentials: username, `admin` and password `geoserver`. Obviously you should change those.

For readability, the examples are in multiple lines using a `\`  to split them. You can omit the `\` and run the command as one line. For example;

`curl -u admin:geoserver -v -XPOST -H 'Content-Type: application/xml -d /path/to/store.xml https://mygeoserver.org/geoserver/rest/workspaces/Code4Lib/coveragestores`

## On your GeoServer
The examples assume the following have been done.
### Create a Workspace
If you haven't already, create a [GeoServer workspace](http://docs.geoserver.org/stable/en/user/webadmin/data/workspaces.html) . In our example, we named our workspace `NameOfMyWorkspace`.
### Put your GeoTIFF on the Server
You need to put your GeoTIFF in [GeoServer's data directory](http://docs.geoserver.org/stable/en/user/datadirectory/setting.html#datadir-setting). You might have to work with your system's admin on this. In our examples the data directory is
`/path/to/workspaces/NameOfMyWorkspace` where `NameOfMyWorkspace` is just that; what I named my workspace.

## REST examples

### Create a store for the uploaded GeoTiff
Make a `POST` request and provide some basic data in an XML file.

cURL command
```
curl -u admin:geoserver -v -XPOST -H \
'Content-Type: application/xml'\
-d /path/to/store.xml \
 https://mygeoserver.org/geoserver/rest/workspaces/Code4Lib/coveragestores
```
Contents of XML file
```
<coverageStore>
    <title>Atlanta 1928 Sheet 45</title>
    <name>/path/to/geo.tif</name>
    <workspace>Code4Lib</workspace>
    <enabled>true</enabled>
    <type>GeoTIFF</type>
</coverageStore>
```

### Create a layer from the store
This creates the layer that will be accessible to the public. We need to tell GeoServer where the the GeoTIFF
```
curl –u admin:geoserver –v –XPUT –H ‘Content-type:text/plain’ \
–d ‘file:/path/to/workspaces/NameOfMyWorkspace/geo.tif’ \
https://mygeoserver.org/geoserver/rest/workspaces/Code4Lib/coveragestores/external.geotiffconfigure=first
```

### Update the store's metadata
This will fill in some metadata fields that we will supply in an XML

cURL command:
```
curl -u admin:geoserver -v -XPOST -H 'Content-Type: application/xml' \
    -d atlanta_1928_sheet45.xml \
    https://mygeoserver.org/geoserver/rest/workspaces/Code4Lib/coveragestores/atlanta_1928_sheet45.xml
```
XML file:
Note: The `<metadataLinks>` section is optional. We're providing it here as an example of how you can update the various attributes for a store.
```
<coverage>
	<name>Name of Map</name>
	<title>Title of Map</title>
	<abstract>Description of map</abstract>
	<enabled>'true'</enabled>
	<metadataLinks>
		<metadataLink>
			<type>'text/plain'</type>
			<metadataType>'ISO19115:2003'</metadataType>
			<content>"http://somehost.org/some-map.xml"</content>
		</metadataLink>
	</metadataLinks>
</coverage>


```
