---
title: 13. Hints on Survey Practice
layout: page
nav_order: 13
---

# Hints on Survey Practice

The quality of the data produced by Survey2GIS is in large part determined by the design of the survey workflow. The tools for error correction built into Survey2GIS can only fix some of the problems that arise during field work. This section contains some important hints for a field workflow that ensures good quality result data.

The order of measurements, i. e. the order in which the records will be stored in the input data files, is of great importance:

1. The directions of lines are determined by the order of their vertices.
2. The the outer boundaries of polygons should be recorded in clockwise direction, inner boundaries counter-clockwise, (use the south to north axis of the spatial reference system for orientation). See 9.1.9 for details.
3. Input files should be forwarded to Survey2GIS in the same order in which they were produced during the survey.
4. Topological cleaning functions such as snapping and "hole punching" (9.1) will work best if larger "base objects" are recorded before smaller objects.

The most important principle is that of hierarchical surveying: first the base lines and largest objects, then adjacent smaller objects, embedded objects etc.

When recording polygons, it is not necessary to record the first vertice again to close the geometry. In so far as the output formats demands this, Survey2GIS will take care of closing polygons by exactly duplicating the first vertex.

An important consideration regards the encoding of point measurements. In parser modes "End" and "Min", geometry tags for points may be left out (no declaration of "geom_tag_point" has to be made in the parser schema). In this case, Survey2GIS will attempt to separate single point measurements automatically from vertices of lines and polygons. However, this will only work reliably if the primary key values (i. e. the contents of the field "key_field") are unique across all geometries. If this is not the case, the following problems may occur:

- **Parser mode "End":** If a point occurs directly before a line or polygon record, and if that point has the same key value as the vertices of the following line or polygon object, then it will be erroneously merged as a vertex into that object.
- **Parser mode "Min":** If a point follows directly after a line or polygon object and if that point has the same key value as the vertices of the preceding line or polygon object, then it will be erroneously merged as a vertex into that object.

In order to avoid these problems, the following alternative solutions are recommended:

1. The "geom_tag_point" option is declared in the parser schema and all point measurements are explicitly tagged as such during recording.
2. Separate, non-overlapping key value ranges are used for the different geometry types, e. g. 0-9999 for points, 10000-19999 for lines, 20000-29999 for polygons.