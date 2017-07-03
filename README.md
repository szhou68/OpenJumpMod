# OpenJumpMod

This repo contains a mod to enable loading Ordnance Survey MasterMap (OSMM) topographic layer GML file via OpenJump API.

OpenJump provides an mechnism to load gml via user-specified xml templates (http://ojwiki.soldin.de/index.php?title=Working_with_GML). 

In a OpenJump GML template, a user can specify collection element, feature element, geometry element and define columns for various feature attributes. It seems, however, there are a few restrictions:
- Columns defined as feature element attributes are not loaded; 
- Data without geometry elements can't be loaded

In case of OSMM, the attribute "fid" is defined as an attribute of feature element so it wouldn't be loaded by the original OpenJump GML reader. Also, the SRID in OSMM is "osgb:BNG" where OpenJump GML assumes a number for SRID.

To address the above issues, three OpenJump classes are modified:

-com.vividsolutions.jump.io.GMLReader

-com.vividsolutions.jump.io.ColumnDescription

-com.vividsolutions.jump.io.GMLInputTemplate

There are six feature categories in OSMM topographic layer:

-TopographicArea

-TopographicLine

-TopographicPoint

-CartographicPoint

-CartographicText

-BoundaryLine

You will need a template for each category. Sixe sample template files for OSMM topographic layer features are provided in templates folder. 

It should be noted that for feature attributes that allow multiple values, the returned value by OpenJUMP API has type Object, which may either be a simple data value (double, String), or a list of values.

An unaddressed issue is the textRendering attribute for CartographicText features. This attribute contains child attributes whicn can't be loaded properly by current OpenJump GML loader. Below is an example of textRendering attribute value:

<osgb:textRendering>
<osgb:anchorPosition>2</osgb:anchorPosition>
<osgb:font>1</osgb:font>
<osgb:height>3.000</osgb:height>
<osgb:orientation>0</osgb:orientation>
</osgb:textRendering>

OSMM ITN contains non-geometric road to road_memeber mapping. I have a different handler for loading this mapping, which will be uploaded to this repo soon.

To use this mod, please use one of the two jar files in JAR folder in place of the original OpenJump jar file. Normally a feature category in an OSMM topographic layer GML file may be read into an OpenJump FeatureCollection in the following manner:

		DriverProperties dp = new DriverProperties();
		dp.setProperty("TemplateFile", "your_template.xml");
		dp.setProperty("DefaultValue", "your_GML.gml");
		
		FeatureCollection fc = null;
		GMLReader gmlReader = new GMLReader();
		gmlReader.acceptSRID(true);
		gmlReader.processMultiItems(true);
		try {
			fc = gmlReader.read(dp);
		} catch (Exception e) {
			e.printStackTrace();
		}
