---
title: Handling Special (Accent) Characters
layout: page
nav_order: 12
---

# Handling Special (Accent) Characters

Support for the use of special or accented characters (i. e. those not included in the standard ASCII code table, such as German umlauts) for international text is limited to only the following contexts in Survey2GIS:

1. Names of files and folder,
2. program messages and user interface,
3. selection commands that include replacement text with special characters (cf. 6)
4. and a small subset of parser schema elements (cf. 4):
   a. the content of "info" in the "[Parser]" section,
   b. the content of "name" in the "[Parser]" section,
   c. the content of "info" in a "[Field]" section,
   d. and replacement text that follows an "@" in a "[Field]" section.

In all other contexts (e. g. option names passed on the command line, parser options, names of attribute fields, ...) the use of non-ASCII characters is not supported.

Internally, Survey2GIS handles all international character encoding as Unicode version UTF-8. All ASCII characters have identical codes in UTF-8, which means that a conversion is not necessary and that pure ASCII data will always be processed correctly by Survey2GIS.

UTF-8 ist the most widely used encoding on the Internet and the default for many operating systems (in the rare case that a Linux or Mac OS X system does not use UTF-8, it can be adjusted to do so). The most notable exception to this is the Windows operating system, which (due to historical reasons) still handles character encoding via a number of "code pages" (the Windows code page that is closest to UTF-8 has ID 65001). Starting with version 1.3.1, Survey2GIS is capable of correctly processing the multi-byte encoding used by Windows for file and folder names. To correctly display the UTF-8 encoded program messages issued when running Survey2GIS from the Windows command line (cmd.exe), set the font used by cmd.exe to one that covers the Unicode character range, such as "Lucida Console".

When using special characters in a parser schema (where supported: see above), make sure that the text file containing the schema is produced with a program (text editor) that generates UTF-8 encoded files.

All attribute table field contents produced by Survey2GIS will also be encoded in UTF-8. This means that e. g. special characters used in the DBF attribute table of a Shapefile will only be displayed correctly by a GIS that supports UTF-8.

The use of special characters (as well as whitespace) in file and folder names should always be avoided, as this is very likely to cause problems at some stage (e. g. when processing the files with another software or exchanging files across different operating systems).