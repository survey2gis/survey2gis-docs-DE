---
title: 5. Labels
layout: page
nav_order: 6
---

# 5. Labels

In addition to producing output geometries for the measured geometries, it is also possible to output additional point data with labels (also called "annotations"). Label points are 2D points (Z data is not considered when computing positions of label points) with special attribute fields. The actual data format and attribute table schema used by the labels depends on the chosen output format. See section 10 for details.

> **Warning:** File formats that produce additional files for labels (e. g. Shapefiles: see 10.1) will overwrite existing data. Since they also use auto-naming, this may result in unexpected consequences. Auto-naming will add " labels" to the user-specified output base name. Check to make sure that this does not collide with any existing files that you wish to keep!

Producing a labels layer requires the specification of a field in the input data. The contents of this field will be used as label text for the generated label points. Note that it is possible to choose a label text field that has been marked "skip=true" in the parser (see 4.3). The label field can be of type text, integer or double. Its contents will be copied verbatim into the label texts (except that geometry markers will be removed, if present).

In addition, the user has control over where the label points are placed on the associated geometries: The possible placing modes are: "center" (default), "first", "last" and "none". The effect of these modes depends on the type of geometry:

- **Point geometries:** Placement mode is ignored, one label point is placed exactly on each point. No label points will ever be produced for raw vertex data (see option "--raw-data" in section 2).

- **Line geometries:** In mode "center", one label point will be placed exactly halfway along the path of each line (string); in mode "first", the label point will be placed on the first vertex of each line and in mode "last" it will be placed on the last vertex. In all three modes, label points are guaranteed to lie on the line geometries.

- **Polygon geometries:** In mode "center", one label point will be placed in the geometric center of each polygon; in mode "first", the label point will be placed on the first vertex of each polygon and in mode "last" it will be placed on the last vertex. When using mode "center" with polygons that contain holes or are strongly concave, it cannot be guaranteed that label points will actually lie within the polygon areas.

Mode "none" has the same effect on all geometry types and will suppress the generation of label points for the respective type.

In the case of multi-part geometries, one label point will be generated for each part.

> **Note:** If the label field contains geometry tags in addition to other content, then Survey2GIS will attempt to remove all geometry tags from the output labels. This can fail in some scenarios. To ensure that labels are free of geometry tags, use a separate, dedicated field to store them (see parser definition of "tag_field" in 4.2).