# Additional doc for the QGIS Atlas Print Plugin

This explains how to plugin interrogates the QGIS service to print pages.

The plugin calls these methods one after another : `GetProjectSettings` -> `GetFeature` -> `GetPrint`

Get the definition of Atlas : `http://127.0.0.1:8888/qgis-server?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetProjectSettings`

The coverage layer can be found with this Xpath : ` /WMS_Capabilities/Capability/ComposerTemplates/ComposerTemplate[@name='myplan']/@atlasCoverageLayer`

We can list the features of the coverage layer that intersect an area:
`POST http://127.0.0.1:8888/qgis-server?`

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<wfs:GetFeature maxFeatures="5000" version="1.1.0" service="WFS" xsi:schemaLocation="http://www.opengis.net/wfs http://schemas.opengis.net/wfs/1.1.0/wfs.xsd" xmlns:wfs="http://www.opengis.net/wfs" xmlns:gml="http://www.opengis.net/gml" xmlns:ogc="http://www.opengis.net/ogc" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <wfs:Query typeName="GeoCoverDivision">
    <ogc:Filter>
      <ogc:Intersects>
        <ogc:PropertyName>the_geom</ogc:PropertyName>
          <Polygon xmlns="http://www.opengis.net/gml" srsName="EPSG:4326">
            <exterior>
              <LinearRing>
                <posList srsDimension="2">6.904809316884131 46.445147801931604 6.904511260615304 46.44536023617469 6.905209363082197 46.44564162252219 6.905951438924574 46.445877208874904 6.906586641229295 46.44633044643151 6.907835703094311 46.44689045221654 6.908457065788995 46.44696353109655 6.908648245758518 46.44684388210399 6.907904997673016 46.446725253908994 6.906745292077869 46.446141820152185 6.906105531914959 46.445749169409744 6.905239061797321 46.44545902316597 6.904809316884131 46.445147801931604</posList>
              </LinearRing>
            </exterior>
          </Polygon>
      </ogc:Intersects>
    </ogc:Filter>
  </wfs:Query>
</wfs:GetFeature>
```

You can extract the IDs of the coverage layer features (xpath below):
`/wfs:FeatureCollection/gml:featureMember/qgs:MY_COVERAGE_LAYER/@gml:id` (structure : `<featureType>.<id>`)

With the returned IDs, we can run a print of one or several pages (separated by `,`) :
`http://127.0.0.1:8888/qgis-server?SERVICE=WMS&REQUEST=GetPrint&CRS=EPSG:2056&TEMPLATE=mybeautifullayout&FORMAT=pdf&ATLAS_PK=124,125`