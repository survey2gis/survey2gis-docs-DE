---
title: 1. Installation and Basic Usage
layout: page
nav_order: 1
---

# 1. Installation and Basic Usage

## 1.1 Provided functionality

The following functionality is currently provided by Survey2GIS:
- Line-wise reading of point coordinate measurements and encoded attribute data from one or more
input text files.
- Multiplexing of point readings into geometric objects (line strings and polygons).
- Freely definable fields (schema) for storing attribute data in the GIS output data.
- Detailed validation and recording (protocol) of all measurements and their processing, including to-
pological cleaning.
- Merging of all input files and export of geometry and attribute data into standard GIS file formats.
- Re-orientation and reprojection of coordinate data.
Some of the software’s noteworthy features are:
- Open source and freely available. No proprietary technology, no annoying copy protection or usage
restrictions.
- Runs under Linux, Mac OS X (macOS) and Windows operating systems.
- Minimal hardware requirements; ideal for use on small field laptops.
- Can be used through the command line or a graphical user interface (GUI).
- Processes and produces 2D or 3D data.
- Simple to translate into any language, including different numeric notations (decimal point and group-
ing characters). The software can always be switched back to “English” for use in multi-lingual
environments.
- Freely definable parser schema allows to adjust the processing for any input data field structure.
5
- Processing of several input data files at the same time and merging of their data into a single output
file.
- Complete recording of all hints, warnings and error message issued during processing; on screen and
into a text file.
- Invalid input data will be discarded from the output, but processing will continue in most cases.
- Handling of missing and “no data” records.
- Topological data validation and cleaning to produce high quality GIS data suitable for spatial analysis.

## 1.2 Structure of input data

Input data may be supplied as one or several text files (alternatively from the operating system’s standard
“in” stream: see 2.1). These text files contain at least one line (record) of data, with each line storing
the coordinates of exactly one point measurement, along with additional attribute data (identifiers, custom
object codes, etc.). Attribute data may be preserved or discarded in the output, according to the needs of
the end user. A minimal record does, however, need at least an X and a Y coordinate value.
There is no provision in Survey2GIS for handling various legacy text encodings, such as DOS and Windows
“codepages” or extended ASCII tables. This software assumes that input text is either plain ASCII or
UTF-8 encoded. The user must take care that any software used to further process the da

## 1.3 Installation

There is no installation routine in the form of a "setup" program. Simply locate the version for your operating system in the "bin" folder and run it. There is a "README" file for each operating system that contains further details.

Users who do not wish to use proprietary software to download data from their survey devices may find TotalOpenStation ([http://tops.iosa.it](http://tops.iosa.it)), a free tool for serial communication with selected total stations and GPS models, a useful companion to Survey2GIS.

Windows users should also consider installing a capable text editor (such as NotePad++: [http://notepad-plus-plus.org](http://notepad-plus-plus.org)), to facilitate the manual editing and cleaning of large data and log files.

## 1.4 Basic Usage

This software may be used from the command line or through a graphical user interface (GUI). The same capabilities are available in both modes. The GUI is described further in section 3. To use Survey2GIS from the Windows command line, start cmd.exe first, then change into the folder where the software resides (replace all items in pointed brackets "<>" with the actual path entries), by typing:

```
cd /D <drive>:\<survey2gis folder>
```

For a first test run, create a folder named "output" within the the program's folder (or another convenient location with write access). The data produced by Survey2GIS will be written into this folder. In addition, copy the files "parser desc end.txt" and "sample data end.dat" from the "samples" subfolder (shipped as part of the Survey2GIS package) into the installation folder. The software can then be run by issuing the following command:

```
survey2gis -p parser desc end.txt -o output -n test data sample data end.dat
```

Details of the above command line:

- Option -p (parser) specifies the file that has the information about the input data's structure (schema). Details about the syntax of this file can be found in section 4.2.
- Option -o (output) specifies the folder into which all output files will be written. In this example, its name happens to be "output", as well. The default for Survey2GIS is to write the data in Shapefile format (see 10 for choices).
- Option -n (name) specifies the user-definable (base) part of the output files' names. E. g. in the case of Shapefiles, there will be several output files, with the extensions ".shp", ".shx" and ".dbf" added to the base name (see 10.1).
- The final item specified on the command line is not an option, but the name of an input file. The file "sample data end.dat" contains one simple survey dataset with point, line and polygon records. This is a plain text file that can be viewed and edited using any text editor. It would be possible to specify further input file names on the command lines. The contents of all input files would then be merged by Survey2GIS and written to just one output dataset.

The result of the above command line should be three Shapefile sets containing the point, line and polygon records from "sample data end.dat" respectively.

A summary of all command line options is available via the option -h (help):

```
survey2gis -h
```

A detailed discussion of all options is available in section 2.