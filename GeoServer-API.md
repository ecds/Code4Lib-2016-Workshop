# Using GeoServer's REST Interface to Add Scanned Maps
Below are the steps to make a GeoTIFF available in GeoServer using [GeoServer's REST API](http://docs.geoserver.org/stable/en/user/rest/). The examples use the command line tool [cURL](https://en.wikipedia.org/wiki/CURL#Examples_of_cURL_use_from_command_line) . Go [here for  full explanation of cURL's options](https://curl.haxx.se/docs/manpage.html).

## On your GeoServer
The examples assume the following have been done.
### Create a Workspace
If you haven't already, create a [GeoServer workspace](http://docs.geoserver.org/stable/en/user/webadmin/data/workspaces.html) . In our example, we named our workspace `NameOfMyWorkspace`.
### Put your GeoTIFF on the Server
You need to put your GeoTIFF in [GeoServer's data directory](http://docs.geoserver.org/stable/en/user/datadirectory/setting.html#datadir-setting). You might have to work with your system's admin on this. In our examples the data directory is
`/path/to/workspaces/NameOfMyWorkspace` where `NameOfMyWorkspace` is just that; what I named my workspace.

## REST examples
In the examples:

* `https://mygoeserver.org` is the domain for the GeoServer. Replace that with the url for your GeoServer. You should use `https` as you will be providing user credentials.
* `admin:geoserver` is the username and password. This is the default for GeoServer. You should change it.
* `geo.tif` is the file name of your GeoTIFF
* `Code4Lib` is our made up workspace. Replace this with your workspace.
* `/path/to/..` Replace all these references to actual file paths.
* `<somexml>` We're providing examples of the XML data to send. It is easier to just have it one long string after the `-d` . You can pass it a file path but you might run into whitespace issues.

For readability, the examples are in multiple lines using a `\`  to split them. You can omit the `\` and run the command as one line. For example;

`curl -u admin:geoserver -v -XPOST -H 'Content-Type: application/xml -d '<coverageStore>...</coverageStore> https://mygeoserver.org/geoserver/rest/workspaces/Code4Lib/coveragestores`

### Create a store for the uploaded GeoTiff
Make a `POST` request and provide some basic data in an XML file.

cURL command

```
curl -u admin:geoserver -v -XPOST \
-H 'Content-Type: application/xml' -d '<somexml>' \
https://mygeoserver.org/geoserver/rest/workspaces/Code4Lib/coveragestores
```

Example XML

```
<coverageStore>
    <title>My Map</title>
    <name>mymapname</name>
    <workspace>Code4Lib</workspace>
    <enabled>true</enabled>
    <type>GeoTIFF</type>
    <url>file:test/geo.tif</url>
    <description>About my map</description>
    <advertised>true</advertised>
</coverageStore>
```

### Create a layer from the store
This creates the layer that will be accessible to the public. We need to tell GeoServer where the the GeoTIFF
```
curl -u admin:geoserver -v -XPUT =h 'Content-type: text/plain' \
-d 'file:/path/to/data_dir/geo.tif' \
https://mygeoserver.org/geoserver/rest/workspaces/test/coveragestores/mymapname/external.geotiff?configure=first
```
**Note:** `external.geotiff` is literally what it is. Don't replace this with the name of your GeoTIFF.

### Update the store's metadata
This will fill in some metadata fields that we will supply in an XML

cURL command:

```
curl -u admin:geoserver -v -XPOST -H 'Content-Type: application/xml' \
-d <somexml> \
https://mygeoserver.org/geoserver/rest/workspaces/Code4Lib/coveragestores/<tiff file name but replace .tif with .xml>.xml
```
XML:
Note: The `<metadataLinks>` section is optional. We're providing it here as an example of how you can update the various attributes for a store.
```
<coverage>
	<title>Title of Map</title>
	<name>mymapname</name>
	<abstract>Description of map</abstract>
	<enabled>'true'</enabled>
	<metadataLinks>
		<metadataLink>
			<type>'text/plain'</type>
			<metadataType>'ISO19115:2003'</metadataType>
			<content>"http://somehost.org/some-metadata.xml"</content>
		</metadataLink>
	</metadataLinks>
</coverage>


```
