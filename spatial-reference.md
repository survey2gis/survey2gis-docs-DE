---
title: 9. Spatial Reference Systems and Reprojections
layout: page
nav_order: 9
---

# Spatial Reference Systems and Reprojections

Support for spatial reference systems and reprojections has been introduced in version 1.5.0 of Survey2GIS. This necessitated some significant changes in the user interface and processing logics. Most importantly, geographic latitude/longitude data is now handled explicitly, leading to modified program behaviour in the areas of topological cleaning and output formats. Please note the relevant parts of sections 9 and 10, in addition to the information given in this section.

Survey2GIS allows the user to set spatial reference system (SRS) and reprojection information for only the input or both input and output data. Setting the input SRS correctly helps ensure correct data processing. Additionally setting the output SRS will cause Survey2GIS to perform output data reprojection.

SRS and further reprojection parameters (concerning datum transformation for higher accuracy in certain scenarios: see 8.2.1) can be set through a number of options with the common prefix "proj-" (note that there are no one-letter short names for these):

```
--proj-in
--proj-out
--proj-dx
--proj-dy
--proj-dz
--proj-rx
--proj-ry
--proj-rz
--proj-ds
--proj-grid
```

The first two options ("--proj-in" and "--proj-out") are used to set the SRS of the input and output data, respectively, while the remaining ones are only used in the context of a geodesic datum shift. More information on each option is given in the following subsections (note also the short summaries in 2.3).

Reprojection is performed after all other geometric processing is done. This means that it is possible to e. g. topologically clean UTM input data and reproject the output to a latitude/longitude system.

To compute reprojections, Survey2GIS uses a built-in copy of the PROJ.4 open source library. This means that all SRS definitions will be converted to PROJ.4 definitions internally, and that all reprojection capabilities are identical with those of the built-in version of PROJ.4. Read 8.2 for more information.

If the input data consists of geographic lat/lon coordinates, it will be assumed that these are provided as simple decimal degrees (see https://en.wikipedia.org/wiki/Decimal_degrees). Survey2GIS does not include any support for correctly parsing lat/lon data that uses "E/W" prefixes and/or "degrees, minutes, seconds" notations.

## Setting Input and Output SRS

The user can specify the (given) SRS of the input data and the (intended) SRS of the output data. This is done via the options "--proj-in" and "--proj-out", respectively. Only one SRS can be set for all input files. It is not possible to mix input data with different SRS in one program run.

Setting only an input SRS allows Survey2GIS to adjust processing if needed. This is especially important in the case of latitude/longitude coordinate data which requires different mathematical treatment. The program will issue a warning or abort operations in situations where lat/lon data cannot be processed (correctly).

If both input and output SRS are set and are different from each other, then Survey2GIS will reproject the data (unless the input data's SRS is "local": see below) from the input to the output reference system. Reprojection generally involves a loss of accuracy. To minimize the latter, additional datum transformation options can be supplied by the user (see 8.2.1) for details.

Setting only an output SRS will result in a processing error.

SRS can be specified using EPSG codes, PROJ.4 strings or, in a few common cases, via convenient shorthands (read on for details).

### Internal SRS Abbreviations

For convenience, Survey2GIS understands a small number of shorthand names that can be used to set the SRS (capitalization is ignored):

#### local
Setting the SRS to "local" tells Survey2GIS that the data uses a local, cartesian reference system with metric units (meters). It is not possible to reproject such data. The "local" abbreviation exists mainly for the sake of consistency and completeness. If the input SRS is set to "local" and the output SRS is set to any other value, the program will issue an error message and abort.

#### wgs84
Setting the SRS to "wgs84" is identical to setting it to "EPSG:4326" (latitude and longitude values with WGS 84 datum). Note that Survey2GIS assumes that all input data uses decimal notation (see 8.3).

#### web
This abbreviation is used to specify the SRS used by Google Maps and similar online mapping tools. Please see 8.1.4 for details. It is identical with "EPSG:3857".

#### utmXXN/S
It is possible to specify a UTM zone (WGS 84 datum is always assumed) by replacing "XX" with a one or two-digit zone number and "N"(orth) or "S"(outh) designation. E. g. specify "utm32s" for "UTM with WGS 84 datum, zone 32, north". The EPSG equivalents are codes "32601" through "32760".

#### dhdnX
This abbreviation is used for the Gauß-Krüger national grid of Germany. Replace "X" with zone identifiers "2" to "5". The EPSG equivalents are codes "31466" through (including) "31469".

#### osgb
This is the traditional (still in use) grid system of the British Ordnance Survey (OSGB). It is identical with "EPSG:27700".

### Using EPSG Codes

If no internal abbreviation (see 8.1.1) is available, the second most simple way to specify an SRS is to pass its EPSG code (in fact, all SRS abbreviations are automatically mapped to their EPSG equivalents by Survey2GIS).

The full EPSG SRS database is available for reference online at http://www.epsg-registry.org/.

Setting an EPSG code as input or output SRS is done by prefixing the EPSG code with "epsg:" (capitalization is ignored).

Valid examples:
```
--proj-in=EPSG:27700
--proj-out=epsg:4326
```

Note that Survey2GIS only supports a subset of EPSG definitions, namely those that are also supported by the included version of PROJ.4 (see 8.1.3). Also note that PROJ.4 uses a simple, potentially imperfect, translation from EPSG codes to its own format, which may incur some loss of information/accuracy (see: http://proj4.org/faq.html#how-do-i-use-epsg-coordinate-system-codes-with-proj). If this is an issue, consider specifying PROJ.4 format parameters directly (see 8.1.3).

### Using PROJ.4 Strings

In some scenarios, neither the use of an internal SRS abbreviation nor the specification of an EPSG code (see 8.1.1 and 8.1.2, respectively) may be sufficient to obtain accurate reprojection results.

In such cases, full PROJ.4 SRS definitions ("PROJ.4 strings") can be provided as input and/or output SRS definitions.

PROJ.4 strings consists of several tokens, each one prefixed with a "+" and separated by a space from the next one. Capitalization of token names does not matter to Survey2GIS, as it automatically takes care of converting all token names to lowercase text. The token's name is followed by its value, separated from the name using an equal sign ("=") without further whitespace.

For example, the PROJ.4 version of "EPSG:4326" (lat/lon data with WGS 84 datum) is defined as follows:
```
+proj=longlat +ellps=WGS84 +datum=WGS84
```

And "EPSG:32601" (UTM zone 1, north) is defined as:
```
+proj=utm +zone=1 +ellps=WGS84 +datum=WGS84 +units=m
```

Please consult the PROJ.4 online documentation at http://proj4.org/ for full details, and also mind the information in 8.2.

In addition, the following may be helpful when trying to compose valid PROJ.4 strings:

- Survey2GIS prints out the full PROJ.4 definitions for all user-provided SRS as part of its standard logging output.
- The website at http://www.spatialreference.org/ can be used to convert between different SRS representations, including EPGS and PROJ.4.

An additional "+no_defs" token is always appended to the PROJ.4 string by Survey2GIS. This prevents any (potentially interfering) defaults that might be provided by PROJ.4 from being applied.

Datum transformation parameters and/or a datum shift grid file (see 8.2.1) can be appended using the tokens "+towgs84" and "+nadgrids", respectively. Note that these tokens will automatically be discarded and replaced by the corresponding Survey2GIS option values, if the latter are provided (see also 8.2.1).

**Important:** PROJ.4 definition strings are case-sensitive! E. g. specifying "+ellps=wgs84" will produce an error (the correct form being "+ellps=WGS84").

**Important:** PROJ.4 does not understand international numeric locales! This means that it is not possible to use decimal separators other than "." (period) in PROJ.4 token values!

### Web Mercator

Popular Internet navigation and visualization tools, such as Google Earth/Maps, OpenStreetMap, etc., use a perfect sphere as Earth model (i. e. the length of the equator is assumed to be exactly the same as the length of any meridian arc). This is not a realistic geodetic assumption, but it increases 3D performance. This perfectly spherical Earth model is called "Web Mercator" (see https://en.wikipedia.org/wiki/Web_Mercator). The tricky thing is that these tools will pretend that the coordinates displayed on a Web Mercator "ellipsoid" are identical with those on a geodetic WGS 84 ellipsoid (an effect of this is that length and area measurements using a 3D tool with a Web Mercator sphere will be wrong compared to the real, geodetic measurements on a WGS 84 Earth model): the coordinates seen on the 3D display are meant to be WGS 84.

Paradoxically, when performing a geodetically correct reprojection with a datum conversion between the two (see 8.2.1) wrong Y coordinates will result! This is due to PROJ.4 automatically compensating for the difference in N-S radii between the two systems' Earth models.

According to http://spatialreference.org/ref/sr-org/6864/, the error can be quite significant:

> "Relative to an ellipsoidal development errors of up to 800 meters in position and 0.7 percent in scale may arise."

According to EPSG entry:

> "Relative to WGS 84 / World Mercator (CRS code 3395) errors of 0.7 percent in scale and differences in northing of up to 43km in the map (equivalent to 21km on the ground) may arise."

To circumvent this problem and maintain the intuitive assumption that e. g. the coordinates of locations identified on a Google Earth display are the same as their counterparts on a "proper" map with WGS 84 datum, Survey2GIS will perform a two-stage reprojection whenever Web Mercator data is involved:

1. The Web Mercator-based coordinates are redeclared as WGS 84 without any reprojection, pretending that they are identical.
2. The "converted" WGS 84 coordinates will then be reprojected "properly" to the desired output SRS (unless "WGS 84" is the desired target system, in which case no further action is required).

Be advised however, that this is a work-around trick and that 3D tools with Web Mercator models are not suitable for applications that require geodetic correctness and accurate measurements.

This mechanism will only work if either "EPSG:3857" or the abbreviation "web" is used to specify the input/output SRS. If a PROJ.4 string is used, then it must include the necessary additions to handle the Y coordinates compensation (see http://proj4.org/faq.html#changing-ellipsoid-why-can-t-i-convert-from-wgs8 for details).

Providing any datum transformation parameters or a grid shift file (see 8.2.1) will lead to a program error if Web Mercator is the source or target SRS!

## Reprojection Using PROJ.4

**Important:** This version of Survey2GIS uses PROJ.4, version 4. At the time of writing, a major revision of PROJ.4, leading to version 5 and involving numerous changes in the user interface and underlying reprojection engine, was in progress. Please make sure to refer to the correct PROJ.4 version when looking for further information online.

If both input and output SRS are provided, are valid (see 8.1), and neither is a "local" system (see 8.1.1), then Survey2GIS will reproject all coordinate data from the input SRS to the output SRS.

Reprojection includes all point/vertex coordinates as well as (if present) label coordinates (see 5).

Survey2GIS relies on the open source library PROJ.4 (http://proj4.org/) for SRS and reprojection support. Therefore, its capabilities for data reprojection, geodesic datum support, etc. are identical with those provided by PROJ.4. Essentially, reprojection functionality is limited to 2D geographic (lat/lon) and 2D planimetric (X/Y) SRS.

Survey2GIS ships with a bundled copy of PROJ.4. The exact version number of the copy used by Survey2GIS is reported in the processing log. The bundled subfolder "proj" contains a database file with EPSG codes (called "epsg": see 8.1.2) for use by PROJ.4 and some grid files (you can add your own: see 8.2.2). This folder is essential for PROJ.4 to work correctly, and it should reside within the folder from which Survey2GIS is launched (in the case of the Windows version, this is the location of "survey2gis.exe", not "survey2gis.bat"):

The "proj" folder and the "epsg" file residing inside of it are part of the default distribution of Survey2GIS and thus PROJ.4 should work without problems.

Alternatively, the environment variable "PROJ_LIB" can be set to contain the full path to a copy of the "proj" folder. And as a third fallback option, PROJ.4 will look for "proj" in a default, system-wide location (the latter will vary, depending on the operating system).

If the "proj" folder does not exist (in any of the locations referenced above) or is not readable, Survey2GIS will still run, but reprojection functionality will be limited, and attempting to perform certain reprojections (e. g. involving EPSG codes) will result in an error.

### Datum Transformations

Where SRS with different datums (such as "WGS 84" vs. "ED50") are involved, the key to minimizing loss of accuracy during reprojection is to specify the best available datum transformation.

A geodetic/map datum is essential information that describes the "anchor point" and orientation of an SRS. In practice, the Earth model (such as a geodetic ellipsoid) is also considered part of the (extended) map datum.

Historically, many different datums have been used for mapping around the globe. In more recent times, mapping practice has converged on a smaller number of datums used for large areas of the world. The most common map datum, which also underlies the modern UTM system, is the "World Geodetic System" datum of 1984 (WGS 84), but other datums, such as ETRS89 also remain in use.

When computing reprojections, Survey2GIS (more accurately: the PROJ.4 library on which it relies) uses WGS 84 as a common "pivotal datum": if datums other than WGS 84 are involved, then coordinate transformations pass from the input SRS datum through WGS 84 and into the output SRS datum. Consequently, it is possible to specify datum transformation options to WGS 84 from the input SRS.

When using SRS abbreviations or EPSG codes for specify input/output SRS in Survey2GIS (see 8.1), commonly applied transformations parameters to WGS 84 are already included in the SRS definition. However, there is no guarantee that the default transformation achieves the best possible results. Therefore, datum transformations can also be provided by the user.

There are two basic datum transformation types: Three-parameter transformations provide translation (shift) factors towards the WGS 84 datum along the geographic X, Y and Z axes, while seven-parameter transformations also specify three rotational factors (again, around the geographic axes) and a scale factor. Transformation parameters can be set using the respective options provided by Survey2GIS:

```
--proj-dx
--proj-dy
--proj-dz
--proj-rx
--proj-ry
--proj-rz
--proj-ds
```

The first three options ("dx", "dy" and "dz") listed above are the translations factors of a three-parameter transformation. The following three ("rx", "ry" and "rz") and the final factor ("ds") are the rotational and scale factors, respectively of a seven-parameter transformation.

Note that these options can also be specified as part of a PROJ.4 string (token "towgs84": see 8.1.3), but options passed to Survey2GIS will replace/overwrite those in the PROJ.4 string (if present).

Note also that it is possible to specify only some of the options above (i. e. less than would be needed for a complete three or seven parameter transformation). In such case, Survey2GIS will automatically complete the datum transformation parameters using default parameters where needed. The default value for the scale factor ("--proj-ds") is "1", all other parameters (translation and rotation factors) default to "0".

User-defined transformation parameters will only applied by Survey2GIS if at least one parameter value deviates from its default value.

**Important:** Not supplying any transformation options to Survey2GIS does not mean that reprojection will not include a datum transformation. E. g. most EPSG SRS definitions (see 8.1.2) include their own transformation parameters, which will be applied if there is no user-specified transformation. Survey2GIS will report any applied transformation parameters as part of its log output.

The following conditions will raise an error and abort the current processing job:

- Both numeric datum transformation parameters and a transformation grid file (see 8.2.2) have been specified (only one of the two can be applied).
- Numeric datum transformation parameters or a transformation grid file have been specified, but "--proj-in" or "--proj-out" are missing (the latter must both be specified to trigger a reprojection).

If the output SRS has no datum transformation (to WGS 84) definition, then a synthetic three-parameter transformation with all parameters (i. e. X, Y and Z axis shifts) set to "0" will automatically be added to it. The reason for this is that transformation parameters must be present in both input and output SRS if a datum transformation is required (see: http://proj4.org/faq.html#why-do-i-get-different-results-with-4-5-).

**Important:** For the reason given in 8.1.3, it is not possible to use decimal separators other than "." (period) in the specification of datum transformation parameters!

### Grid-based Reprojection

Some application cases, such as high-precision engineering and cadastral mapping, may require a reprojection accuracy that cannot be achieved with even the best available parametric datum transformations (see 8.2.1) in all scenarios. In such cases, the alternative is to use a dense set ("grid") of local correction vectors, provided specifically for reprojection to the target SRS.

A file ("grid file") containing such correction data can be passed to Survey2GIS, which will in turn pass the data through to the underlying PROJ.4 library. The grid file's data coverage must match the extents of the (reprojected) data in the target SRS, otherwise it will simply have no effect.

The full path and name of a grid file can be supplied to Survey2GIS via the corresponding option:

```
--proj-grid=
```

For more on using grid files read section "nadgrids - Grid Based Datum Adjustments" at http://proj.maptools.org/gen_parms.html.

Note that PROJ.4 allows more than one grid file to be used, but only one can be specified using the dedicated option of Survey2GIS. Alternatively, by specifying the PROJ.4 string directly (see 8.1.3) and using the "+nadgrids=" token, several grid shift files can be supplied (separated by commas). In the latter case, the first grid file whose extents match the input data will be used for the reprojection. When using the "+nadgrids" token in a PROJ.4 string, do not use the "--proj-grid=" option, as the latter will automatically replace the former.

Note: any relative path given in the value of the "--proj-grid=" option will be resolved to a full, absolute path automatically by Survey2GIS, so that PROJ.4 can correctly locate the file. When instead using the "+nadgrids" token in a PROJ.4 string, users must take care of providing complete paths themselves.

## Notes on SRS and Reprojection Issues

Reprojection is a complex process. This section details some of the trickier issues that are involved in the process and gives advice on how to obtain the best possible results.

**Shape preservation in large-area surveys:** Some reprojection types (notable those involving a transformation from planimetric X/Y to geographic lat/lon data) can result in a significant loss of geometric accuracy. In such cases, better reprojection results may be obtained by adding more vertices to lines and polygon boundaries during surveying.

**Datum transformation quality:** Using the best possible datum transformation is key to reprojection accuracy where systems of different natures are involved. The default transformation parameters contained in EPSG codes are not always the best choice. Grid files should be used where available; Search the EPSG database at http://www.epsg-registry.org/ for alternative transformation parameters and accuracy assessments and read section 8.2.1 of this manual.

**Converting SRS representations:** The website at http://www.spatialreference.org/ provides a very helpful database of global SRS in a variety of common formats, including EPSG, PROJ.4 and WKT (Well Known Text). It is also possible to download ".prj" auxiliary files for adding SRS info to existing Shapefiles.

**Lat/lon data:** Survey2GIS expects all lat/lon data to use decimal degrees. Sufficient locational accuracy in lat/lon coordinates may require many decimal places. Helpful information and guidance on this topic is available at https://en.wikipedia.org/wiki/Decimal_degrees.

**Vertical (Z) coordinate units:** PROJ.4 automatically detects the correct Z units from the SRS definition before doing the reprojection. However, Survey2GIS always assumes that Z is in meters when re-orienting coordinates (such as "Local X-Z": 7.2) or performing any other 3D operations. These two assumptions may collide and produce unexpected results in rare circumstances.