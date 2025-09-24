---
title: Survey2GIS
layout: home
---

# Survey2GIS Quick Start Guide

## What is Survey2GIS?
Survey2GIS converts survey data from plain text files into GIS formats (Shapefile, DXF, GeoJSON, KML). It takes point measurements and reconstructs them into points, lines, and polygons for use in GIS software.

## Installation

### Option 1: Standalone Application
1. Download Survey2GIS for your operating system
2. Extract to a folder (no installation needed)
3. Run the executable from the command line or use the GUI

### Option 2: QGIS Plugin (Recommended for QGIS users)
Install the "S2G Data Processor" plugin directly in QGIS:
1. In QGIS, go to Plugins → Manage and Install Plugins
2. Search for "S2G Data Processor" 
3. Install and use Survey2GIS functionality within QGIS

Plugin URL: https://plugins.qgis.org/plugins/s2g_data_processor/

## Basic Workflow
1. **Prepare your data** - Survey data in plain text format, one point per line
2. **Create a parser file** - Describes how to read your data structure  
3. **Run Survey2GIS** - Converts data to GIS format

## Quick Test Run

### Step 1: Try the Sample Data

**Windows:**
```cmd
# Open Command Prompt (cmd.exe) and navigate to Survey2GIS folder
cd /D C:\path\to\survey2gis

# Create output folder
mkdir output

# Copy sample files to main folder  
copy samples\parser_desc_end.txt .
copy samples\sample_data_end.dat .

# Run Survey2GIS
survey2gis -p parser_desc_end.txt -o output -n test_data sample_data_end.dat
```

**Mac/Linux:**
```bash
# Navigate to Survey2GIS folder
cd /path/to/survey2gis

# Create output folder
mkdir output

# Copy sample files to main folder
cp samples/parser_desc_end.txt .
cp samples/sample_data_end.dat .

# Run Survey2GIS
survey2gis -p parser_desc_end.txt -o output -n test_data sample_data_end.dat
```

This creates Shapefiles in the `output` folder that you can open in any GIS software.

### Step 2: Use the GUI (Alternative)
**All platforms:**
```bash
survey2gis --show-gui
```
Fill in the forms instead of using command line options.

## Understanding Your Data Structure

Your survey data should look like this:
```
# Comment lines start with #
1 boundary 1 @ 10.00 10.00 1.00
2 boundary 1 @ 10.00 20.00 1.05  
3 boundary 1 @ 20.00 20.00 1.10
4 boundary 1@ 20.00 10.00 1.00
```

Each line contains:
- Point ID
- Description/label
- Geometry marker (@ = polygon, - = line, * = point)
- X, Y, Z coordinates

## Creating Your Parser File

The parser file tells Survey2GIS how to read your data. Create a text file with these sections:

```ini
[Parser]
name = My Survey Parser
tagging mode = end
tag field = description
key field = description  
coor x = x
coor y = y
coor z = z
geom tag point = *
geom tag line = -
geom tag poly = @

[Field]
name = id
type = integer
separator = space

[Field] 
name = description
type = text
separator = space

[Field]
name = x
type = double
separator = space

[Field]
name = y  
type = double
separator = space

[Field]
name = z
type = double
```

## Essential Command Line Options

**Required options:**
- `-p parser.txt` - Parser file
- `-o output_folder` - Where to save results  
- `-n base_name` - Base name for output files

**Useful options:**
- `-f shp` - Output format (shp, dxf, geojson, kml)
- `-h` - Show help
- `-v -p parser.txt` - Validate parser file
- `--show-gui` - Open graphical interface

## Common Data Formats

### Points Only
```
1 10.00 10.00 1.00
2 10.00 20.00 1.05
3 20.00 20.00 1.10
```
Use `tagging mode = none` in parser.

### Lines with Markers  
```
1 fence * 10.00 10.00 1.00
2 fence - 15.00 15.00 1.50
3 fence - 20.00 20.00 2.00
```
First point marked with `*`, line segments with `-`.

### Polygons
```
1 building @ 10.00 10.00 1.00
2 building @ 10.00 20.00 1.05  
3 building @ 20.00 20.00 1.10
4 building @ 20.00 10.00 1.00
```
All vertices marked with `@`, automatically closed.

## Troubleshooting

**"Parser validation failed"**
- Check your parser syntax
- Ensure field names match your data
- Validate with: `survey2gis -v -p your_parser.txt`

**"No output files created"**
- Check that output folder exists and is writable
- Verify your data has correct geometry markers
- Look at console messages for errors

**Geometries look wrong**
- Check coordinate field assignments in parser
- Verify geometry markers are correct
- Consider data order (larger objects first)

## Next Steps

Once you have basic conversion working:
- Read Chapter 4 for advanced parser options
- Chapter 6 for data selections and filtering  
- Chapter 8 for coordinate system reprojection
- Chapter 9 for topological cleaning options

## File Formats Supported

**Input:** Plain text files with survey measurements
**Output:** 
- Shapefile (.shp) - Default, works with most GIS
- DXF (.dxf) - CAD format  
- GeoJSON (.geojson) - Web/database format
- KML (.kml) - Google Earth format

## Quick Reference Commands

```bash
# Basic conversion
survey2gis -p parser.txt -o output -n mydata data.txt

# Multiple input files  
survey2gis -p parser.txt -o output -n mydata file1.txt file2.txt

# Different output format
survey2gis -p parser.txt -o output -n mydata -f dxf data.txt

# With coordinate reprojection
survey2gis -p parser.txt -o output -n mydata --proj-in=utm32n --proj-out=wgs84 data.txt
```

This quick start should get you converting survey data in minutes. Refer to the full manual for advanced features and troubleshooting.
