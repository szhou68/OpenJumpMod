# OpenJumpMod

This repo contains a mod to enable loading Ordnance Survey MasterMap (OSMM) topographic layer GML file via OpenJump API.

OpenJump provides an mechnism to load gml via user-specified xml templates (http://ojwiki.soldin.de/index.php?title=Working_with_GML). 

In a template, a user can specify collection element, feature element, geometry element and define columns for various feature attributes. It seems, however, there are a few restrictions:
- Columns defined as feature element attributes are not loaded; 
- GML files without geometry elements can't be loaded

In case of OSMM, the attribute "fid" is defined as an attribute of feature element so it wouldn't be loaded by the original OpenJump GML reader. Also, the SRID in OSMM is "osgb:BNG" where OpenJump GML assumes a number for SRID.

To address the above issues, three OpenJump classes are modified:
-com.vividsolutions.jump.io.GMLReader
-com.vividsolutions.jump.io.ColumnDescription
-com.vividsolutions.jump.io.GMLInputTemplate

There are five feature categories in OSMM topographic layer:
-TopographicArea
-TopographicLine
-TopographicPoint
-CartographicPoint
-CartographicText

You will need a template for each category.

OSMM ITN contains non-geometric road to road_memeber mapping. I have a different handler for loading this mapping.
