---
title: Installation and Basic Usage
layout: page
nav_order: 1
---

# Installation and Basic Usage

## Installation

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