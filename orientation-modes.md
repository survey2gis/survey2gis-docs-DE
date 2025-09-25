---
title: 8. Orientation Modes
layout: page
nav_order: 8
---

# Orientation Modes

Starting with version 1.5.0, Survey2GIS provides the concept of "orientation modes" as a work-around for handling vertical data ("catenas" in soil science, "profiles" in archaeology, etc.) in a traditional 2D/pseudo 3D GIS environment.

Processing 3D data is more complex than just passing through additional Z coordinate data. GIS concepts and operations, such as topology and georeferencing, do not easily translate into the third dimension. Thus, survey data that include a strong vertical component (cross-sections of soil volumes, standing walls, etc.) are not easy to integrate into a 2D GIS workflow. Survey2GIS addresses this problem by providing options to re-orientate the input data into a new, synthetic 2D coordinate system. The resulting data can be loaded into a 2D GIS and treated as though it were regular X-Y planar 2D data. Such a transformation from 3D to 2D always incurs a loss of information.

By default, the orientation of the output data produced by Survey2GIS is identical to that of the input data (setting option "--orientation" to "world"), i. e. no re-orientation is performed (7.1).

If requested by the user, re-orientation of axes is performed immediately after an input file has been read and before further geometry processing, in particular topological cleaning, is applied (see 9). This allows operations such as snapping to work on vertical data as expected. The coordinates of label points (if present: see 5) will also be transformed.

The program option "--orientation=" (see 2.3) can be used to re-orientate the data as described below. Note that not all orientation modes may be available for all types of input data.

**Warning:** Data produced in an orientation mode other than "world" will be subject to transformations with no guarantee of reversibility. Therefore, all survey raw data should be archived and re-orientated data should never be considered the primary or authoritative processing result.

## World Orientation (Default)

Orientation mode "world" is the default mode of operation in Survey2GIS. It does not modify the input data in any way. The corresponding option value exists merely to keep the user interface consistent.

## Local X-Z Orientation

The "local X-Z" orientation mode (option value "localxz") is a simple means of transforming 3D survey data to 2D cross-sections ("profiles") that can be visualized in a 2D GIS using a synthetic coordinate system. It is not suitable for large areas with many disjunct geometries, but intended to visualize a single "profile" (alternative terms: "cut", "trench wall", "catena", etc.):

The re-orientated data will have a new, synthetic coordinate system with swapped Z and Y axes and newly computed X coordinates. The fact that the transformed data's X coordinates will always start at "0" while the Z coordinates are preserved, makes it relatively easy to compare different datasets side-by-side (provided that the GIS can display several data views at once).

This transformation method is non-parametric and fully automatic. It does not require the definition of a projection plane.

The transformed data will have the following properties:

- The new Y coordinates will be the same as the original Z data.
- The coordinates of the synthetic X axis will no longer represent any original measurements.
- The smallest X coordinate value found in the input data will become the origin (X = 0) of the new, synthetic X axis.
- The largest X coordinate on the synthetic X axis will be identical with the greatest difference in X values between the input measurements.
- All Y coordinate data are discarded: user-defined offsets on any axis are also discarded.

This transformation method has a number of limitations:

- Input data must be 3D cartesian data. Lat/lon and 2D data cannot be transformed using this method.
- Input data should only cover a single profile's extent.
- All input data must represent points that were measured from just one location. By extension, this means that complex 3D structures with strong concavity ("recesses") will not produce satisfactory results.
- Data with strong variation along the profile's major axis (i. e. on the X-Y plane) will be strongly distorted
- Data that has a significantly larger extent on the Z axis than on the X-Y plane will more likely produce wrongly orientated results (see description of process, below).
- It is not possible to transform data from more than one data source (input file) in one program run.
- Data with "local X-Z" orientation cannot be reprojected to another spatial reference system (see 8).

Results will be as accurate as possible if all points/vertices between the first and the last measurement lie on a straight line when viewed in the X-Y plane. The more they deviate from this ideal assumption, the more distorted the result will be. Likewise, transformed profiles will be easier to interpret if the original Z coordinates are "reasonably orthogonal" to the X-Y plane (because the new Y axis retains the geographic alignment of the original Z axis).

The transformation works as follows:

1. Set reference point RP and determine profile orientation (see below).
2. For all input points/vertices (pi=1,2,...,n) in input dataset P:
   a. Compute X coordinate for transformed version of pi as straight-line distance between X/Y location of pi and RP.
   b. Set Y for transformed pi to value of original Z coordinate.
   c. Set new Z coordinate to be "0".

In order to produce usable results, two key parameters will be derived automatically from the data:

1. The overall W-E orientation of the profile, i. e. the question of where the left edge of the profile will be in the X-Z coordinate system. If this fails, then the transformed data will appear "mirrored".
2. The identity of the most "extreme" surveyed point with the "left-most" coordinate in the synthetic X-Z coordinate system. If the wrong point is identified, the left edge of the transformed profile will appear distorted.

The first parameter, "profile orientation" is determined by computing the weighted sum of differences between the X coordinate of a reference point and those of all other points. All X coordinate differences are inversely weighted by the difference in Z between them and the reference point. The effect of this is that measurements further removed on the Z axis from the reference points have less of an effect on the result, thereby compensating for the practical problem that the true "left" edge of the surveyed profile might receed significantly in comparison to the first measured point. If the weighted sum of X differences is positive, then the profile is determined to have "eastward" orientation, "westward" otherwise. Note that the latter are not accurate descriptions of the profile's orientation in the geographic X-Y plane. They merely serve to determine where the "left" and "right" edges of the transformed data will be in the synthetic X-Z coordinate system.

In the extreme case that all X coordinate differences sum to exactly "0" (e. g. this can theoretically happen if all measured points lie on a straight line parallel to the S-N geographic axis), profile orientation cannot be decided automatically and will be assumed to be "eastward orientation".

In practice, then, everything hinges on choosing a suitable reference point, which the program assumes to be the first point of the input data. This is more likely to produce accurate results if one keeps the following in mind when conducting the profile survey:

1. Measurements should start at or near the upper-left corner (as viewed from the surveying station) of the profile.
2. Successive measurements should generally proceed toward the right and bottom, ending at or near the lower-right corner of the profile.

The above assumptions do not have to be met fully. Given a sufficient number of measurements, the program will be able to compensate for e. g. a reference point that is not identical with the true top-left point in the input data.

Survey2GIS will provide information on the chosen reference point, profile orientation and other key parameters as part of its logging output.