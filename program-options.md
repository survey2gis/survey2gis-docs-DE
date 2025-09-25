---
title: 2. Program Startup and Options
layout: page
---

# Program Startup and Options

The software is controlled by a number of options that can either be stated on the command line (started via cmd.exe on Windows systems), as described in this section, or through a graphical user interface (GUI), as described in section 3. Both interfaces offer the same level of control.

On the command line, the name of the executable program ("survey2gis") is followed by the names (or abbreviations) of one or more options, each of them in turn followed by user-specified option values (=settings). Finally, a list of input files is supplied on the same line. All option and input file specifications must be separated from each other by a space character.

Starting the software with the option "-h" (for "help", note the space preceding the option name):

```
survey2gis -h
```

...will display a summary description of the software and its options on the screen (much shortened here; actual display may differ):

```
Usage: survey2gis -p FILE -o DIR -n NAME [OPTION]... [FILE]...
Read geometry and attribute descriptions from a survey protocol file
in ASCII format and convert them to common GIS and CAD output formats.

Possible OPTIONs are:
-p, --parser= name of file with parsing schema (required)
-o, --output= directory name for output file(s) (required)
-n, --name= base name for output file(s) (required)
-f, --format= output format (see list below; default: "shp")
"shp" (Esri Shapefile)
"dxf" (AutoCAD Drawing Exchange Format)
"geojson" (GeoJSON Object)
"kml" (Keyhole Markup Language)
-L, --label= choose field for labels (see manual for details)
--label-mode-point= label mode for points (default: "center")
--label-mode-line= label mode for lines (default: "center")
--label-mode-poly= label mode for polygons (default: "center")
"center" (Place at center of geometry)
"first" (Place on first vertex)
"last" (Place on last vertex)
"none" (Do not label)
...
```

A minimal command line for running Survey2GIS to process some actual data must include:

- The specification of a parser schema file (see 4) with option "-p",
- the name of an existing folder in the file system, where the output data is to be stored (option "-o"),
- the base name for naming all output files, using option "-n",
- and one or more input file names.

The parser schema describes the structure of the input data in detail. It must be supplied as a structured text file. Its syntax is described in detail in section 4.

Most option names have short and long spelling variants, which are equivalent. E. g. it makes no difference whether the parser file is specified using:

```
-p parser.txt
```

or using the long option name:

```
--parser=parser.txt
```

Long option names are separated from option values by an equal sign ("=") and generally improve the legibility of command line statements.

The order in which options are given is of no importance, but input files should always be listed as the last items of the command line.

Option values and file names that include spaces (a practice that should generally be avoided), must be written in quotation marks. Otherwise they will be interpreted as separate tokens on the command line.

As an alternative, the graphical user interface (GUI: see 3) can be started by calling Survey2GIS without any options, or by adding the special option "--show-gui":

```
survey2gis --show-gui
```

## Input and Output Files

All items specified on the command line, that follow the list of options and option values, will be interpreted as the names of input data files. All input data files will be interpreted according to the parser schema file (specified with option "-p"). The output data produced will be stored as files in the folder specified by the "-o" option. The output folder must exist and must allow write access. The current folder can be used as output folder by specifying the single dot (".") as folder name.

The output file format (see details in 10) is selected using option "-f". By default, this option's value is "shp", which causes Survey2GIS to produce GIS Shapefiles (see 10.1). All input data will be merged into a common output dataset. The actual names and number of output files depends on the chosen format. For this reason, the option "-n" specifies a base name only, without any file extension. The base name will be automatically extended and file type extensions added as required.

**Important:** This also means that an existing file will be overwritten automatically, if its name clashes with one of those produced by Survey2GIS!

The minus ("-") character has a special purpose. If it is given instead of a file name, then Survey2GIS will wait for input data to arrive through the operating system's standard input stream. On Mac OS X (macOS) and Linux systems, this stream is known as stdin. By default, it is linked with the keyboard input stream, so that the user may directly type input data to be forwarded to Survey2GIS. To complete keyboard input, the user has to type the special sequence "ˆZ" on Windows systems (press keys "CTRL" and "Z" simultaneously), and "ˆC" ("CTRL" and "C") on Mac OS X and Linux, on an otherwise blank line.

## Language Settings

All messages and GUI elements of Survey2GIS are "internationalized". On startup, Survey2GIS will check the language settings of the operating system on which it is running and display messages in the system's language, provided that a matching translation is available. If no translation exists, then Survey2GIS will fall back to English. In any case, option "-e" will force Survey2GIS to run in English mode, disregarding any operating system settings.

Internationalization also includes different numerical notations. E. g. the number "tenthousandpointzero" would be written like this in a German or Dutch environment:

```
10.000,00
```

Whereas in an English speaking location, it would be:

```
10,000.00
```

The numerical notation, including representations of the decimal point and grouping character, will be set to the English standard by default, as this is how most hardware produces output. However, Survey2GIS offers settings for the specification of decimal point and grouping characters (using options "-i" and "-g", respectively).

It is of crucial importance that the numerical notation is correctly set to that of the input data. Many devices and programs use English notation by default. Typical symptoms of a mismatch include wrongly placed geometries in the output data, as well as stepped and deformed line segments and polygon boundaries.

## Complete List of Options

The following is a complete but terse description of all options accepted by Survey2GIS. Links to more details on how these options influence processing are provided where appropriate. Options are listed in the same order in which they appear in the command line usage summary (invoked by running "survey2gis --help").

### Option: -p, --parser=
**Meaning:** Complete path and name of the file that contains the parser schema for processing all input data (see 4).

**Example:**
```
-p C:\Users\Me\parser.txt
```

### Option: -o, --output=
**Meaning:** Complete path and name of the folder in which to store all output data.

**Example:**
```
-o c:\Data
```

### Option: -n, --name=
**Meaning:** Base name (no extension) for all output file(s). The files will be stored in the folder specified by "-o" (see above).

**Example:**
```
-n output
```

### Option: -f, --format=
**Meaning:** Name of the format in which to store the output data (default: "shp").

**Remarks:** The help text displayed via option "-h" contains the complete list of possible formats (see also 10).

**Example:**
```
-f shp
```

### Option: -L, --label=
**Meaning:** Specify the name of a field (see 4.3) to use for creating a special labels layer. See section 7 for details.

**Example:**
```
-L level
```

### Option: --label-mode-point=
**Meaning:** Specify where to place labels for point geometries. Valid choices are: "center" (default), "first", "last" and "none".

**Remarks:** Only "none" will have a real effect in the case of point geometries. See section 5 for details.

**Example:**
```
--label-mode-point=none
```

### Option: --label-mode-line=
**Meaning:** Specify where to place labels for line geometries. Valid choices are: "center" (default), "first", "last" and "none". See section 5 for details.

**Example:**
```
--label-mode-line=center
```

### Option: --label-mode-poly=
**Meaning:** Specify where to place labels for polygon geometries. Valid choices are: "center" (default), "first", "last" and "none". See section 5 for details.

**Example:**
```
--label-mode-poly=center
```

### Option: -O, --orientation=
**Meaning:** Sets the orientation of the output data's coordinate axes. The default orientation mode is "world" which does not modify the coordinate data. See section 7 for details.

**Example:**
```
-O localxz
```

### Option: -S, --selection=
**Meaning:** Specify a selection command to reduce the input data to only those records that pass the selection criteria. It is possible to specify one or more selections, each one via a separate "--selection=" option. The selections will be applied in the same order in which they are specified. See section 6 for details.

**Remarks:** Note that this option's shortcut is a capital "-S", while "-s" is the shortcut for the "--snapping=" option (see below).

**Example:**
```
-S objdesc:all:EQ:Boundary
```

### Option: -T, --topology=
**Meaning:** Determines which topological cleaning functions to run on the input data (see 9). Possible choices are "none", "basic" and "full" (default). An explanation of these choices is given in section 9.1.

**Remarks:** Note that this option's shortcut is a capital "-T", while "-t" is the shortcut for the "--tolerance=" option (see below).

**Example:**
```
-T basic
```

### Option: -l, --log=
**Meaning:** Stores all processing messages displayed by survey2gis in the specified file.

**Example:**
```
-l C:\Users\Me\protocol.txt
```

### Option: -t, --tolerance=
**Meaning:** Sets the threshold for the distance of individual point measurements. Measurements that are spaced below this distance from each other will be regarded as duplicate (see 9.1.1).

**Example:**
```
-t 0.05
```

### Option: -s, --snapping=
**Meaning:** Sets the threshold for snapping of vertices that constitute polygon boundaries. Snapped vertices will be moved so that they are placed exactly on nearby vertices off neighbouring polygon boundaries. The maximum distance up to which snapping is performed is specified by this option (see 9.1.5 for details). Setting this option to "0" (default) will disable boundary vertex snapping.

**Example:**
```
-s 0.05
```

### Option: -D, --dangling=
**Meaning:** Sets the threshold for snapping of vertices that constitute the first and last vertices (also called "nodes") of lines. Snapped line nodes will be moved to the closest vertex or segment on a nearby line or polygon boundary. This process is also known as overshoot (node is located behind the segment on which it should lie) and undershoot (node is located before the segment on which it should lie) correction of so-called "dangles" (see 9.1.8 for details). The maximum distance up to which node snapping happens is specified by this option. Setting this option to "0" (default) will disable line node snapping.

**Remarks:** Note that this option's shortcut is a capital "-D", while "-d" is the shortcut for the "--decimal-places=" option (see below).

**Example:**
```
-D 0.05
```

### Option: -x, --x-offset=
**Meaning:** Specifies a constant offset to be added to all X coordinates.

**Remarks:** This may be useful if e. g. UTM coordinates have been recorded without the first two digits. Please note that the attribute table of the output data will always store the original values, without the offset! If the transformed values should also be stored in the attribute table, then the appropriate GIS tools ("field calculator" or similar) must be used directly after data import to add the offset to the field values.

**Example:**
```
-x 100000
```

### Option: -y, --y-offset=
**Meaning:** see above (-x, --x-offset=).

**Example:**
```
-y 100000
```

### Option: -z, --z-offset=
**Meaning:** see above (-x, --x-offset=).

**Example:**
```
-z 100000
```

### Option: -d, --decimal-places=
**Meaning:** Specifies the number of decimal places (precision) with which numerical values will be stored in the attribute fields of the output data. For survey data in meters, a setting of "2" means centimeter precision. The precision of coordinate storage is not influenced by this. The default is "3".

**Example:**
```
-d 3
```

### Option: -i, --decimal-point=
**Meaning:** Specifies the character used for representing the decimal point in the input data (see 2.2).

**Example:**
```
-i .
```

### Option: -g, --decimal-group=
**Meaning:** Specifies the grouping character used for formatting large numbers in the input data (see 2.2).

**Example:**
```
-g ,
```

### Option: -r, --raw-data
**Meaning:** If this option is given, then survey2gis will produce an additional output dataset that preserves all individual point measurements of the original survey (i. e. before the data was multiplexed into lines and polygons).

**Note:** The "raw" output will not include vertices that have been removed during topological cleaning, because they are spaced too closely to another vertex (see 9). To also preserve such vertices, set the option "--tolerance=" (see above) to a negative value. In parser mode "None" (see 4.4), the "raw" output will be identical to the regular output.

**Example:**
```
-r
```

### Option: -2, --force-2d
**Meaning:** Force all output data to be 2D, even if the input data is 3D. This setting only affects the storage of the coordinate values in the output file(s). Internal processing is not affected by this setting.

**Example:**
```
-2
```

### Option: -c, --strict
**Meaning:** Switches the parser into a "strict" mode in which it will discard records without a geometry marker instead of interpreting them as single point measurements (see 4.4).

**Example:**
```
-c
```

### Option: -v, --validate
**Meaning:** Performs a validation of the content of the parser schema (specified with option "-p"). The schema file's content will be checked for syntactical correctness and completeness, and any errors will be reported.

**Example:**
```
-p parser.txt -v
```

### Option: -e, --english
**Meaning:** Sets all program messages and the input data's assumed numerical notation to English.

**Example:**
```
-e
```

### Option: -h, --help
**Meaning:** Displays a help text with a synopsis of program usage and a list of options to the screen.

**Example:**
```
-h
```

### Option: --proj-in=
**Meaning:** Specify given spatial reference system of input data (internal abbreviation, EPSG code or full PROJ.4 string). Note that PROJ.4 format specifications are case-sensitive. See section 8 for details.

**Example:**
```
--proj-in=epsg:31467
```

### Option: --proj-out=
**Meaning:** Specify intended spatial reference system of output data (internal abbreviation, EPSG code or full PROJ.4 string). Note that PROJ.4 format specifications are case-sensitive. See section 8 for details.

**Example:**
```
--proj-out=epsg:wgs84
```

### Option: --proj-dx=
**Meaning:** Set the X shift factor for a datum transformation to WGS 84 from the intended spatial reference system of the output data. See section 8.2.1 for details. The default is "0" which means "no X shift".

**Example:**
```
--proj-dx=598.1
```

### Option: --proj-dy=
**Meaning:** Set the Y shift factor for a datum transformation to WGS 84 from the intended spatial reference system of the output data. See section 8.2.1 for details. The default is "0" which means "no Y shift".

**Example:**
```
--proj-dy=73.7
```

### Option: --proj-dz=
**Meaning:** Set the Z shift factor for a datum transformation to WGS 84 from the intended spatial reference system of the output data. See section 8.2.1 for details. The default is "0" which means "no Y shift".

**Example:**
```
--proj-dz=418.2
```

### Option: --proj-rx=
**Meaning:** Set the X rotation factor for a datum transformation to WGS 84 from the intended spatial reference system of the output data. See section 8.2.1 for details. The default is "0" which means "no X rotation".

**Example:**
```
--proj-rx=0.202
```

### Option: --proj-ry=
**Meaning:** Set the Y rotation factor for a datum transformation to WGS 84 from the intended spatial reference system of the output data. See section 8.2.1 for details. The default is "0" which means "no Y rotation".

**Example:**
```
--proj-ry=0.045
```

### Option: --proj-rz=
**Meaning:** Set the Z rotation factor for a datum transformation to WGS 84 from the intended spatial reference system of the output data. See section 8.2.1 for details. The default is "0" which means "no Z rotation".

**Example:**
```
--proj-rz=-2
```

### Option: --proj-ds=
**Meaning:** Set the scaling factor for a datum transformation to WGS 84 from the intended spatial reference system of the output data. See section 8.2.1 for details. The default is "1" which means "no change in scale".

**Example:**
```
--proj-ds=6.7
```

### Option: --proj-grid=
**Meaning:** Set the grid file to be used for a grid-based datum transformation to WGS 84 from the intended spatial reference system of the output data. See section 8.2.1 for details. The default is to not use any grid file.

**Example:**
```
--proj-grid=BETA2007.gsb
```