---
title: 5. The Parser Schema
layout: page
---

# The Parser Schema

The part of Survey2GIS that is responsible for making sense of the data in the input file (i. e. correctly interpret the file's contents) is called the "parser". The parser cannot automatically understand the structure of an input file. Instead, it must be supplied with a parser schema, which is a plain text file with a simple syntax that describes the structure of the input data. The user must create a valid parser schema file before running Survey2GIS on new input data. A given parser schema is then supplied to Survey2GIS using the -p (Parser) option (see 2.3). It can be used to process as many additional input files as required, so long as the input files all adhere to the structure specified in the schema file. This section explains the contents of a valid parser schema file.

## Syntax

To see the contents of this section in context, study the (non-working) illustrative parser description "parser.txt" in the subfolder "docs", as well as the (working) parser and data files in the subfolder "samples".

Lines that start with a "#" (hashmark) are comment lines. They will be ignored just like empty lines.

A valid parser schema must consist of two types of sections. Each section must in turn be declared using a key word in square brackets ("[]"). The two section types are called "[Parser]" and "[Field]". There must be exactly one section of type "[Parser]" that contains general settings for the parser. Following that, up to 251 sections of type "[Field]" may be added, each one describing the properties of one of the input data's elements/fields. The fields must be declared in the same order as they appear (from left to right) in the input data file(s).

Every section then contains further declarations that consist of "option name & value" pairs ("settings"), separated by an equal ("=") sign in the general form "option=value". Only one such pair is allowed per line of the parser schema, e. g.:

```
[Field]
Name=Index
```

The entire text to the right of "=" will be interpreted as the option's value. Empty values are not allowed. If an option is not to be set, then it should not be declared at all. Parser key words, option names and values within a schema are treated as not case sensitive. The names of options and their values may also be given using quotation marks. Additional whitespace characters will be ignored, so the following is just as valid as the above:

```
[Field]
"name" = "Index"
```

Option names must not contain any whitespace. Names that consist of more than one word may be spelled using the underscore ("_") character instead:

```
[Field]
Name = Index
empty_allowed = No
```

Some options, such as "empty allowed" in the example above, are boolean values or "switches" that can only be toggled on or off (boolean "true" or "false"). The following are all equivalent values for turning an option "on": "Y", "Yes", "On", "1", "Enable", or "True". For turning an option "off", the following may be used: "N", "No", "Off", "0", "Disable", or "False".

A parser schema may be validated by running Survey2GIS with the options "-v" and "-p", followed by the name of the schema file. If the schema contains any errors, an informative message will be generated. If there are no errors then a summary description of the schema will be displayed.

The next section contains a discussion of all options that can or must be declared within the sections "[Parser]" and "[Field]".

## Options for [Parser]

The following options (listed in alphabetical order) can ("optional") or must ("mandatory") be declared within the section "[Parser]". They control the general workings of the input data parser. The example file "parser.txt", contained in the subfolder "docs", contains a parser schema (not suitable for processing actual data) that illustrates all possible options with short comments.

### Option: comment_mark
**Meaning:** Special characters that may be used to introduce comments in the input data. All text preceded by a comment mark will be skipped when processing the data.

**Remarks:** Optional. One or more comment marks may be specified. However, these must not include characters that are also used as field separators or quotation marks (see "quotation" and "separator" in 4.3).

**Example:**
```
comment_mark = #
comment_mark = //
```

### Option: coor_x, coor_y, coor_z
**Meaning:** These three options specify the fields that hold the X, Y and Z coordinates of the measurements.

**Remarks:** The options "coor_x" and "coor_y" must always be specified and contain the names of two distinct, fully declared fields (see next sections. All fields must be numeric and of type "double". If "coor_z" is not specified, then Z coordinates will be assumed to be "0.0" for all measurements.

**Example:**
```
coor_x = xfield
coor_y = yfield
coor_z = zfield
```

### Option: geom_tag_point, geom_tag_line, geom_tag_poly
**Meaning:** Specification of geometry markers ("tags") for the three basic geometry types "point", "line" and "polygon". These markers allow the parser to multiplex simple point measurements into more complex geometries. They must be part of the input data.

**Remarks:** The details of how these markers are used by the parser depend on the parser mode chosen (see 4.4). Setting "geom_tag_point" is optional for some parser modes. If it is not set, then Survey2GIS will try to recognize simple point measurements automatically. However, this will only work reliably if certain requirements are met (see 12). For all parser modes except "None", both "geom_tag_line" and "geom_tag_poly" must be defined (and use different markers!) as part of the parser description. For parser mode "None", no geometry tag definitions need to be made; if any are found, they will be ignored. It is possible to declare more than one (up to 32) geometry tag for each geometry type. However, they must not have any characters (case matters) in common. There must also be no overlap in characters with field separators, quotation marks (see 4.3 for both) or comment marks (see "comment_mark", above). If the field used to store the tag is numeric, then all digits, plus ("+") and ("-") are also not allowed. Neither are the decimal point and thousands separator characters, if the field is of type "double".

**Example:**
```
geom_tag_point = "*"
geom_tag_line = "-"
geom_tag_poly = "@"
geom_tag_poly = "@ "
```

### Option: info
**Meaning:** A short description of the parser schema.

**Remarks:** Optional.

**Example:**
```
info = This parser has been designed to test Survey2GIS
```

### Option: key_field
**Meaning:** Name of the key field that contains the same value for all measurements that represent vertices of the same multi-vertex geometry (i. e. a line or polygon). If the parser is working in mode "End" or "Max", then all successive measurements with the same value in "key_field" will be merged into one geometry. Note that "successive" in this context means that measurements will be merged up to and including the next one that contains a line or polygon geometry marker (see "tag_field", below). The "key_unique" (see below) option can be used to merge multi-part geometries.

**Remarks:** Mandatory for parser modes "End" and "Max".

**Example:**
```
key_field = key
```

### Option: key_unique
**Meaning:** Specifies whether the key field (see above) is unique across disjunct (multi-part) geometries.

**Remarks:** If this option is turned "on", Survey2GIS will assume that every geometry has a globally unique key value. This can be used to correctly process geometries that consist of disjunct parts (e. g. a set of spatially separate areas that should be considered parts of one polygon). In this case, all geometries with the same value in "key_field" (see above) will be stored as one multi-part geometry in the output file, with only one row of attribute data. If this option is not specified, then Survey2GIS cannot produce multi-part geometries and the user must instead manually assemble them in the GIS.

**Example:**
```
key_unique = yes
```

### Option: name
**Meaning:** Parser name.

**Remarks:** Optional.

**Example:**
```
name = Survey2GIS test parser
```

### Option: no_data
**Meaning:** An integer number that represents attribute values which are "null" (="no data"). This should be set to a value that never occurs in valid data. The chosen value will be written into the output data whenever a field actually contains no data (i. e. is empty; see "empty_allowed" in 4.3).

**Remarks:** Optional. See descriptions of output formats in 10 for details on how "no data" is set when there is no user-defined value for it. See also "empty_allowed" (above).

**Example:**
```
no_data = -99999
```

### Option: tag_field
**Meaning:** Name of the field that contains the geometry markers (see 4.4). May be identical with "key_field" (see above), except in parser mode "Max". Must contain the name of field declared within a "[Field]" section (see 4.3).

**Remarks:** Must be specified for all parser modes except "None". To reduce typing work in the field, geometry markers may be part of any other attribute field (see 4.4 for details). In this case, however, the attribute values must not overlap with the characters used as geometry markers. Coordinate fields can also not be used for this purpose (see "coor_x", "coor_y" and "coor_z", above). Note that in parser mode "Min" it will usually make sense for "tag_field" to be a separate field, since there will be only one record (line) with complete attribute data.

**Example:**
```
tag_field = tag
```

### Option: tagging_mode
**Meaning:** Determines the principal working mode of the parser (parser mode). Possible settings are "min", "max", "end" or "none". Further explanations are given in 4.4.

**Remarks:** Mandatory.

**Example:**
```
tagging_mode = end
```

### Option: tag_strict
**Meaning:** If this option is "on", the parser will be strict about geometry markers. This means that every measurement (i. e. line in the input file) must contain a geometry marker. Measurements without one will be discarded.

**Remarks:** Optional. Turned "off" if not specified.

**Example:**
```
tag_strict = yes
```

## Options for [Field]

The general parser settings (see preceding section) must be followed by two or more (up to 251) field definitions, each one introduced by a

```
[Field]
```

line. In addition to coordinates, individual fields may represent any kind of attribute data recorded during the survey. For the automated reconstruction of complex line strings and polygons, the parser schema must contain at least one additional field that contains the geometry markers and key values (see descriptions of "key_field" and "tag_field" in preceding section).

All attribute data will be stored as part of the output file(s), provided that this is supported by the chosen output file format. In the case of Shapefiles, the attribute data will be stored as a DBase file with extension ".dbf". Further restrictions may apply to the definition of attribute fields, depending on the capabilities of specific output formats (see 10 for details).

### Option: change_case
**Meaning:** This option may be set to convert the contents of a text field to all upper or lower case characters after reading the input data. Possible values are "lower" (convert to lower case) and "upper" (convert to upper case) or "none" (no conversion; this is the default).

**Remarks:** Optional. Only applies to fields of type "text".

**Example:**
```
change_case = lower
```

### Option: empty_allowed
**Meaning:** Indicates whether a field is allowed to have empty content. E. g. this will be the case when two field separator characters succeed each other immediately or with only whitespace between them. In that case, a "no data" (see option "no_data", below) value will be stored if "empty_allowed" is on. Otherwise, the entire measurement (line of input data) will be considered invalid and will be discarded.

**Remarks:** Optional. This option is "off" by default. It cannot be turned "on" for fields that are declared to be "key_field" or "tag_field" or if "merge_separators" (see below) is "on".

**Important:** If the corresponding field's separator is set to a whitespace character ("tab" or "space"), then empty fields cannot be processed correctly: Since whitespace to the left or right of a field's contents ("padding") is automatically removed, the parser cannot distinguish between empty fields and field separators in this extreme case.

**Example:**
```
empty_allowed = yes
```

### Option: info
**Meaning:** One-line description of the field's content.

**Remarks:** Optional (but highly recommended). For purposes of documentation.

**Example:**
```
info = Field with unique object index.
```

### Option: merge_separators
**Meaning:** Specifies whether two adjacent field separators should be treated as one separator.

**Remarks:** Optional. If turned "off" (default) then two adjacent separators will be interpreted as empty field content (see "empty_allowed" above).

**Example:**
```
merge_separators = yes
```

### Option: name
**Meaning:** The name of the field, with a maximum length of 10 characters. Valid characters for field names are all letters of the Latin alphabet, the underscore ("_") and the digits "0" to "9". There is no distinction between upper and lower case spelling. All field names will be stored in all lower case automatically (note: correctly enforced since version 1.4.0).

**Remarks:** Mandatory. Every field must have a valid name.

**Example:**
```
name = index_fld
```

### Option: persistent
**Meaning:** This option is only meaningful in parser mode "Min" (s. 4.4). If it is turned "on" for a field, then the parser expects a field of this type to be present in every line of the input file, even though there may be no valid attribute data. This option can be useful when processing with superfluous data. E. g. some software writes additional data into each line when downloading data from a device.

**Remarks:** Optional. Coordinate fields are always assumed to be persistent in parser mode "Min".

**Example:**
```
persistent = yes
```

### Option: quotation
**Meaning:** If the content of a text field is surrounded by quotation marks and the marks themselves should not be saved as part of the field content, then the quotation mark character used must be specified with this option.

**Remarks:** Optional. Only one quotation mark character per field may be declared. This must not be a character that is also used as field separator (see "separator", below) or comment mark (see "comment_mark" in 4.2). Applies only to fields of type "text".

**Example:**
```
quotation = '
```

### Option: skip
**Meaning:** When "skip" is turned "on" for a field, then the field will be processed by the parser but it will not be stored as part of the output. This is useful for fields that are part of the input data structure but are of no importance for further data processing.

**Remarks:** Optional. The default is store every defined field in the output file(s).

**Example:**
```
skip = yes
```

### Option: separator
**Meaning:** It is possible to specify one or more separator characters that indicate the end of one field's content and the beginning of the next. Field separators may also include more than one character. The special declarations "tab" and "space" are used to represent tabulator and space characters, respectively.

**Remarks:** Must be specified for every field except the last one and "pseudo fields" (see parser option "value", below). Also note the option "merge_separators" (above).

**Example:**
```
separator = ;
separator = tab
separator = space
```

### Option: type
**Meaning:** This option defines the data type of the field. The available choices are "integer" (for whole numbers: -1, 0, 100, ...), "double" (for real numbers in floating point notation: -10,05, 0.0, 1200,12, ...) and "text" (for generic text). Note that some output format will severely limit the length of text fields (see corresponding entry in 10). Correct processing of floating point data requires correct settings for decimal point and thousands separator characters (cf. 2.2).

**Remarks:** Must be specified for every field.

**Example:**
```
type = integer
```

### Option: unique
**Meaning:** If this option is turned "on", then the parser will check whether the field's contents are unique per geometry. If that is not the case then Survey2GIS will issue a warning during processing. However, the affected geometries will not be modified or discarded. It is then up to the user to decide how to handle duplicate attribute values.

**Remarks:** Optional. Turned "off" by default. Uniqueness is checked across all input file(s).

**Example:**
```
unique = yes
```

### Option: value
**Meaning:** The "value" option allows the user to add a "pseudo field" to the output data that is not actually present in the input data. The field will have a constant, user-defined value for all attribute table records in the output data. The field's definition is a simplified one: Only the options "info", "name", "type" and "value" will be processed. Providing any other options will produce an error message.

**Remarks:** The field's data type (see option "type") must match the specified "value". In case of floating point values, make sure that the language settings (see 2.2) match the value's notation.

**Example (for a field of type "double"):**
```
value = 1000.05
```

### Option: @ (replace)
**Meaning:** The "@" character signifies replacement of text field contents. This option allows the user to replace repetitive, short text (codes, abbreviations) with longer, more descriptive text for storage in the attribute table. The syntax for the replacement operation is:

```
@old=new
```

This replaces the text "old" with "new", whenever the former appears in the content of the text field and constitutes the entire contents of the field. Partial replacements will not be performed. It is possible to specify more than one replacement pair per text field. There must be no whitespace or other characters between "@" and "old".

**Remarks:** This option is only available for fields of type "text". The comparison of old and new field content is not case sensitive. The new text must not be longer than 254 ASCII characters.

**Example (for a field of type "text"):**
```
@ABB = Abbreviation
@P1 = Point of type 1
...
```

## Geometry Markers and Parser Modes

In order to reconstruct complex geometries, such as line strings and polygons, from the survey data, the parser needs to understand how to interleave the individual measurements that represent the vertices of complex geometries.

There are some fundamental limitations to what can be achieved:

- Even though Survey2GIS is capable of processing any number of input data files, it is not possible to spread vertex measurements belonging to one geometry across more than one file. However, it is possible to store the vertices of separate parts of a multi-part geometry (s. description of parser option "key_unique") in separate files.
- The list of consecutive vertex measurements within one input file may only be interrupted by comment lines or empty line. Regarding the surveying work in the field, It is thus not possible to interrupt the surveying of one feature, start and complete another feature and later finish surveying the first. Where such a working mode is unavoidable, the resulting data file must be re-ordered manually before it can be processed by Survey2GIS.
- The only available geometry types are "point", "line" and "polygon". Rounded shapes cannot be approximated by Survey2GIS. This must be done in a separate post-processing step, using external software.

**Note:** Further hints on the processing of complex geometries, such as polygons with holes or multi-part geometries, can be found in the section on "topological cleaning" (9).

Every geometry type is indicated to the parser via a freely definable geometry marker (see 4.2). Candidate characters for markers are all characters that are not normally used in attribute table field content, such as "@" or "$". When surveying, these markers are keyed into the device by the surveyor and stored along with the associated coordinate measurements. They may be inserted as separate data fields or at the end of an existing field (depending on the parser mode chosen).

The field that contains the geometry markers is called the "tag_field" and must be declared as part of the parser schema (see option "tag_field" in 4.2). If there is no such declaration, then Survey2GIS will interpret every measurement/line of input data as an individual point measurement. In this case it will be impossible to build lines or polygons automatically from the input data.

In some parser modes, the value of a key field (option "key_field") is used to indicate to the parser which measurements to interleave into one geometry. The key field's content is read in a case-sensitive manner, so that "1a" and "1A" would represent two different keys. The field option "change_case" may be used to alter this behaviour.

The final measurement/vertex of a polygon does not have to be identical with the first one, i e. duplicate measurements need not be taken. Polygons will be closed automatically by Survey2GIS. In fact, such a duplicate vertex is considered a topological error that will be fixed automatically by Survey2GIS (see 9 for details).

Depending on the nature of field work, it may be useful to apply different strategies for marking geometries. For this reason, Survey2GIS provides alternative processing methods, called "parser modes". These can be set in the parser schema using the "tagging_mode" option:

```
[Parser]
...
tagging_mode = min
...
```

Only one mode may be specified per parser scheme. This means that all input data will be processed using the same mode. Possible modes are "Min", "Max", "End" and "None".

**Note:** While it is possible to use any text type attribute field for storing an additional geometry tag (and even to declare the same field as "tag_field" and "key_field" in some parser modes: see details below), this is not recommended practice. It is better to use a separate, dedicated geometry tag field. Nevertheless, if geometry markers are stored as part of other field content, then the geometry marker must appear either as the first or last character of the field content.

### "Min": Minimal Coding Work

This mode is selected by default if the user does not specify a different "tagging_mode" in the parser schema. It is useful for reducing the coding (attribute data entry) work during surveying. In this mode, the first measurement of a line or polygon must be tagged with the appropriate geometry marker.

The appearance of a geometry marker in the input data indicates the start of a list of vertex measurements. Only the first vertex measurement includes attribute data on the same line. All following measurements/lines are stored as reduced records that contain only coordinate values and unavoidable additional data that may have been inserted by the software responsible for producing the input data file(s). If such additional data exists, then the user must create matching field definitions and set the "persistent" option (see field options in 4.3), so that the parser will be able to separate actual coordinate data from "noise". It is not necessary to set a "key_field" in this mode, unless the data contains multi-part geometries.

This is an excerpt of input data fit for processing in mode "Min":

```
# Sample file structure for a polygon (square) in mode "Min".
# Hashmark introduces a comment line that will not be parsed.
# Fields: index, label, geometry tag (@=polygon).
1 polygon 1 @ 10.00 10.00 1.00
# The first line had all the attributes and the geometry tag.
# The following lines will only have the x/y/z vertex coordinates.
10.00 20.00 1.05
20.00 20.00 1.10
20.00 10.00 1.00
# The next occurence of a full record terminates the polygon.
2 polygon 2 @ 30.00 30.00 2.50
...
```

To maintain consistency, persistent and coordinate fields have to appear in the same sequence in which they were defined, both in the first (attributed) record and in the following (reduced records). E. g., assuming that the second field ("label") in the example above has been set to be "persistent", then a valid record structure might look like this:

```
1 polygon 1 @ 10.00 10.00 1.00
polygon 1 10.00 20.00 1.05
polygon 1 20.00 20.00 1.10
polygon 1 20.00 10.00 1.00
```

The minimum number of fields, Min(f), in a valid reduced record in mode "Min" is therefore:

Min(f) = number of coordinate fields (2 or 3) + number of persistent fields.

### "Max": Maximal Control

In mode "Max", each measurement must be tagged with a geometry marker (this also means that the parser schema must define a marker for each geometry type). This mode is intended to allow tightly controlled processing of fully edited data. Suitable data may be created using a text editor or other means and Survey2GIS may be used to generate consistent GIS data from it. This mode may also be useful for processing data records exported from a database management system.

A "tag_field" as well as a "key_field" must be defined as part of the parser schema. They must be separate fields. In principle, it is enough for the value of "key_field" to change between successive geometries. For the benefit of data integrity, however, "key_field" should be unique per geometry across all input data.

This is an excerpt of input data, suitable for processing in mode "Max":

```
# Sample file structure for a polygon (square) in mode "Max".
# Hashmark introduces a comment line that will not be parsed.
# Fields: index, label, geometry tag (@=polygon).
# The second field (label) is the key field.
1 polygon 1 @ 10.00 10.00 1.00
2 polygon 1 @ 10.00 20.00 1.05
3 polygon 1 @ 20.00 20.00 1.10
4 polygon 1 @ 20.00 10.00 1.00
# A change in the value of the key field terminates the polygon.
5 point 1 @ 35.00 32.00 2.60
# A change in the value of the key field terminates the first point.
6 point 2 @ 33.00 32.00 2.40
# etc.
...
```

**Note:** The vertices of polygons and lines encoded in mode "Max" must be recorded in direct succession. It is not possible to e. g. intersperse single point measurements between the vertices of a polygon!

### "End": Flexible Surveying

In this mode, every measurement of a line or polygon must be concluded with a geometry marker. The marker must be part of the final vertex measurement.

The advantage of mode "End" is that the decision whether to record a geometry as a polygon or line string must only be taken with the final measurement. In order to allow the parser to collect all vertices that belong to a geometry, working backward from the marked one, all related measurements must have an identical value in the "key_field". This may cause additional typing work during the survey.

Both a "key_field" and a "tag_field" must be defined as part of the parser schema in mode "End". However, both may be the same field. The geometry markers will not be stored as part of the output data. In principle, it is enough for the value of "key_field" to be identical within on geometry, as the geometry tag will reliably indicate the end of each geometry. For the benefit of data integrity, however, "key_field" should be unique per geometry across all input data.

This is an excerpt of input data, suitable for processing in mode "End":

```
# Sample file structure for a polygon (square) in mode "End".
# Hashmark introduces a comment line that will not be parsed.
# Fields: index, label with embedded geometry tag (@=polygon).
# The second field (label) is also the key field.
1 polygon 1 10.00 10.00 1.00
2 polygon 1 10.00 20.00 1.05
3 polygon 1 20.00 20.00 1.10
4 polygon 1@ 20.00 10.00 1.00
# After the occurence of "@" (geometry tag), another object follows.
5 polygon 2 30.00 30.00 2.50
...
```

### "None": Points-Only Surveys

In mode "None", there will be no marking of geometries. This mode is suitable for surveys that include only simple point measurements, such as the work done to survey an elevation model.

By contrast, in modes "Min" and "Max", the geometry markers may be left out (unless option "tag_strict" has been turned on), in which case measurements are automatically assumed to represent simple points.

Note that mode "None" also allows Survey2GIS to be used as a flexible tool for converting simple point lists to GIS datasets.

This is an excerpt of input data, suitable for processing in mode "None":

```
# Sample file structure for a polygon (square) in mode "None".
# Hashmark introduces a comment line that will not be parsed.
# Fields: index, X, Y, Z.
1 10.00 10.00 1.00
2 10.00 20.00 1.05
3 20.00 20.00 1.10
4 20.00 10.00 1.00
5 30.00 30.00 2.50
...
```