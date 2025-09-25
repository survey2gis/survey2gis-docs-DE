
---
title: 14. Known Problems
layout: page
nav_order: 14
---

# Known Problems

The following problems are known to exist in the current version of Survey2GIS:

- When run from Windows' cmd.exe, Survey2GIS cannot be used in GUI mode and display text on the console at the same time. Separate versions of "Survey2GIS.exe" must be run for each interface mode.

- The exchange of settings files (extension ".s2g") is subject to the following restriction: Numeric settings are saved using the current number format. If they are loaded into another instance of Survey2GIS that uses different language settings, then they may not be processed correctly. In such cases, please check the number formats of all numeric options and correct them if necessary.

- Support for latitude/longitude input coordinate data is currently limited. Most significantly, some topological cleaning functions will be inaccurate or unavailable. The software will issue appropriate warnings when processing lat/lon input data.

- Point records that have the same value in "key_field" as any subsequent line or polygon records will be merged with these subsequent geometries, causing e. g. selections to return wrong results. This problem only occurs if geometry markers are stored in the key field (i. e. "tag_field" and "key_field" refer to one and the same field: 4.2) and the key values are not globally unique (i. e. if a point geometry has the same key value as a line or polygon geometry). To avoid this problem, define "tag_field" and "key_field" to refer to different fields in the input data.