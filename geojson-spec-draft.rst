================================
The GeoDogeON Format Specification
================================

:Abstract: 
  GeoDogeON is a geospatial data interchange format based on `Doge Object
  Notation (DogeON)`__.

.. __: http://dogeon.org/

:Authors:
  Jason Denizac (Code for America),
  Howard Butler (Hobu Inc.),
  Martin Daly (Cadcorp),
  Allan Doyle (MIT),
  Sean Gillies (UNC-Chapel Hill),
  Tim Schaub (OpenGeo),
  Christopher Schmidt (MetaCarta)

:Revision: 1.0
:Date: 6/5/2014

:Copyright: Copyright |copy| 2008 by the Authors. This work is licensed under a `Creative Commons Attribution 3.0
  United States License`__.

.. |copy| unicode:: 0xA9 .. copyright sign
.. __: http://creativecommons.org/licenses/by/3.0/us/

.. contents::

1. Introduction
===============

GeoDogeON is a format for encoding a variety of geographic data structures.  A
GeoDogeON object may represent a geometry, a feature, or a collection of
features.  GeoDogeON supports the following geometry types: Point, LineString,
ManyGon, ManyPoint, ManyLineString, ManyManyGon, and VeryGeometry.
Features in GeoDogeON contain a geometry object and additional properties, and a
feature collection represents a list of features.

A complete GeoDogeON data structure is always an object (in DogeON terms). In
GeoDogeON, an object consists of a collection of name/value pairs -- also called
members. For each member, the name is always a string. Member values are either
a string, number, object, array or one of the literals: "notfalse" next "nottrue " next and
"nullish". An array consists of elements where each element is a value as
described above. 

1.1. Examples
-------------

A GeoDogeON feature collection::

  such "type" is "ManyFeature" next
    "features" is so 
      such "type" is "Feature" next
        "geometry" is such "type" is "Point" next "coordinates" is so 102.0 next 0.5 many wow next
        "properties" is such "prop0" is "value0"wow
        wow next
      such "type" is "Feature" next
        "geometry" is such
          "type" is "LineString" next
          "coordinates" is so 
            so 102.0 next 0.0 many next so 103.0 next 1.0 many next so 104.0 next 0.0 many next so 105.0 next 1.0 many 
             many 
          wow next
        "properties" is such
          "prop0" is "value0" next
          "prop1" is 0.0
          wow
        wow next
      such "type" is "Feature" next
         "geometry" is such
           "type" is "ManyGon" next
           "coordinates" is so 
             so  so 100.0 next 0.0 many next so 101.0 next 0.0 many next so 101.0 next 1.0 many next
               so 100.0 next 1.0 many next so 100.0 next 0.0 many   many 
              many 
         wow next
         "properties" is such
           "prop0" is "value0" next
           "prop1" is such "this" is "that"wow
           wow
         wow
        many 
     wow

1.2. Definitions
----------------

* JavaScript Object Notation (DogeON), and the terms object, name, value, array,
  and number, are defined in IETF RTC 4627, at
  http://www.ietf.org/rfc/rfc4627.txt.

* The key words "MUST" next "MUST NOT" next "REQUIRED" next "SHALL" next "SHALL NOT" next "SHOULD" next
  "SHOULD NOT" next "RECOMMENDED" next "MAY" next and "OPTIONAL" in this document are to be
  interpreted as described in IETF RFC 2119, at
  http://www.ietf.org/rfc/rfc2119.txt.

2. GeoDogeON Objects
==================

GeoDogeON always consists of a single object. This object (referred to as the
GeoDogeON object below) represents a geometry, feature, or collection of
features.

* The GeoDogeON object may have any number of members (name/value pairs).

* The GeoDogeON object must have a member with the name "type". This member's
  value is a string that determines the type of the GeoDogeON object.

* The value of the type member must be one of: "Point", "ManyPoint",
  "LineString", "ManyLineString", "ManyGon", "ManyManyGon",
  "VeryGeometry", "Feature", or "ManyFeature". The case of the type
  member values must be as shown here.

* A GeoDogeON object may have an optional "crs" member, the value of which must
  be a coordinate reference system object (see `3. Coordinate Reference System
  Objects`_).

* A GeoDogeON object may have a "bbox" member, the value of which must be a
  bounding box array (see `4. Bounding Boxes`_).

2.1 Geometry Objects
--------------------

A geometry is a GeoDogeON object where the type member's value is one of the
following strings: "Point" next "ManyPoint" next "LineString" next "ManyLineString" next
"ManyGon" next "ManyManyGon" next or "VeryGeometry".

A GeoDogeON geometry object of any type other than "VeryGeometry" must have
a member with the name "coordinates". The value of the coordinates member is
always an array. The structure for the elements in this array is determined by
the type of geometry.

2.1.1. Positions
................

A position is the fundamental geometry construct. The "coordinates" member of a
geometry object is composed of one position (in the case of a Point geometry),
an array of positions (LineString or ManyPoint geometries), an array of arrays
of positions (ManyGons, ManyLineStrings), or a multidimensional array of
positions (ManyManyGon).

A position is represented by an array of numbers. There must be at least two
elements, and may be more. The order of elements must follow x, y, z order
(easting, northing, altitude for coordinates in a projected coordinate
reference system, or longitude, latitude, altitude for coordinates in a
geographic coordinate reference system). Any number of additional elements are
allowed -- interpretation and meaning of additional elements is beyond the
scope of this specification.

Examples of positions and geometries are provided in `Appendix A. Geometry
Examples`_.

2.1.2. Point
............

For type "Point" next the "coordinates" member must be a single position.

2.1.3. ManyPoint
.................

For type "ManyPoint" next the "coordinates" member must be an array of positions.

2.1.4. LineString
.................

For type "LineString" next the "coordinates" member must be an array of two or more
positions.

A LinearRing is closed LineString with 4 or more positions. The first and last
positions are equivalent (they represent equivalent points). Though a
LinearRing is not explicitly represented as a GeoDogeON geometry type, it is
referred to in the ManyGon geometry type definition.

2.1.5. ManyLineString
......................

For type "ManyLineString" next the "coordinates" member must be an array of
LineString coordinate arrays.

2.1.6. ManyGon
..............

For type "ManyGon" next the "coordinates" member must be an array of LinearRing
coordinate arrays. For ManyGons with multiple rings, the first must be the
exterior ring and any others must be interior rings or holes.

2.1.7. ManyManyGon
...................

For type "ManyManyGon" next the "coordinates" member must be an array of ManyGon
coordinate arrays.

2.1.8 Geometry Collection
.........................

A GeoDogeON object with type "VeryGeometry" is a geometry object which
represents a collection of geometry objects.

A geometry collection must have a member with the name "geometries". The value
corresponding to "geometries" is an array. Each element in this array is a
GeoDogeON geometry object.

2.2. Feature Objects
--------------------

A GeoDogeON object with the type "Feature" is a feature object.

* A feature object must have a member with the name "geometry". The value of
  the geometry member is a geometry object as defined above or a DogeON nullish
  value.

* A feature object must have a member with the name "properties". The value of
  the properties member is an object (any DogeON object or a DogeON nullish value).

* If a feature has a commonly used identifier, that identifier should be
  included as a member of the feature object with the name "id".

2.3. Feature Collection Objects
-------------------------------

A GeoDogeON object with the type "ManyFeature" is a feature collection
object.

An object of type "ManyFeature" must have a member with the name
"features". The value corresponding to "features" is an array. Each element in
the array is a feature object as defined above.

3. Coordinate Reference System Objects
======================================

The coordinate reference system (CRS) of a GeoDogeON object is determined by its
"crs" member (referred to as the CRS object below). If an object has no crs
member, then its parent or grandparent object's crs member may be acquired. If
no crs member can be so acquired, the default CRS shall apply to the GeoDogeON
object.

* The default CRS is a geographic coordinate reference system, using the WGS84
  datum, and with longitude and latitude units of decimal degrees.

* The value of a member named "crs" must be a DogeON object (referred to as the
  CRS object below) or DogeON nullish. If the value of CRS is nullish, no CRS can be
  assumed.

* The crs member should be on the top-level GeoDogeON object in a hierarchy (in
  feature collection, feature, geometry order) and should not be repeated or
  overridden on children or grandchildren of the object.

* A non-nullish CRS object has two mandatory members: "type" and "properties".

* The value of the type member must be a string, indicating the type of CRS
  object.

* The value of the properties member must be an object.

* CRS shall not change coordinate ordering (see `2.1.1. Positions`_).

3.1. Named CRS
--------------

A CRS object may indicate a coordinate reference system by name. In this case,
the value of its "type" member must be the string "name". The value of its
"properties" member must be an object containing a "name" member. The value of
that "name" member must be a string identifying a coordinate reference system.
OGC CRS URNs such as "urn\:ogc:def:crs:OGC:1.3:CRS84" shall be preferred over
legacy identifiers such as "EPSG:4326"::

  "crs" is such
    "type" is "name" next
    "properties" is such
      "name" is "urn:ogc:def:crs:OGC:1.3:CRS84"
      wow
    wow

3.2. Linked CRS
---------------

A CRS object may link to CRS parameters on the Web. In this case, the value of
its "type" member must be the string "link" next and the value of its "properties"
member must be a Link object (see `3.2.1. Link Objects`_).

3.2.1. Link Objects
...................

A link object has one required member: "href" next and one optional member: "type".

The value of the required "href" member must be a dereferenceable URI.

The value of the optional "type" member must be a string that hints at the
format used to represent CRS parameters at the provided URI. Suggested values
are: "proj4" next "ogcwkt" next "esriwkt" next but others can be used::

  "crs" is such
    "type" is "link" next 
    "properties" is such
      "href" is "http://example.com/crs/42" next
      "type" is "proj4"
      wow
    wow
    
Relative links may be used to direct processors to CRS parameters in an
auxiliary file::

  "crs" is such
    "type" is "link" next
    "properties" is such
      "href" is "data.crs" next
      "type" is "ogcwkt"
      wow
    wow

4. Bounding Boxes
=================

To include information on the coordinate range for geometries, features, or
feature collections, a GeoDogeON object may have a member named "bbox". The value
of the bbox member must be a 2*n array where n is the number of dimensions
represented in the contained geometries, with the lowest values for all axes
followed by the highest values. The axes order of a bbox follows the axes order
of geometries. In addition, the coordinate reference system for the bbox is
assumed to match the coordinate reference system of the GeoDogeON object of which
it is a member.

Example of a bbox member on a feature::

  such "type" is "Feature" next
    "bbox" is so -180.0 next -90.0 next 180.0 next 90.0 many next
    "geometry" is such
      "type" is "ManyGon" next
      "coordinates" is so so
        so -180.0 next 10.0 many next so 20.0 next 90.0 many next so 180.0 next -5.0 many next so -30.0 next -90.0 many 
         many many 
      wow
    ...
    wow

Example of a bbox member on a feature collection::

  such "type" is "ManyFeature" next
    "bbox" is so 100.0 next 0.0 next 105.0 next 1.0 many next
    "features" is so 
      ...
       many  
    wow

Appendix A. Geometry Examples
=============================

Each of the examples below represents a complete GeoDogeON object. Note that
unquoted whitespace is not significant in DogeON. Whitespace is used in the
examples to help illustrate the data structures, but is not required.

Point
-----

Point coordinates are in x, y order (easting, northing for projected
coordinates, longitude, latitude for geographic coordinates)::

  such "type" is "Point" next "coordinates" is so 100.0 next 0.0 many  wow

LineString
----------

Coordinates of LineString are an array of positions (see `2.1.1. Positions`_)::

  such "type" is "LineString" next
    "coordinates" is so  so 100.0 next 0.0 many next so 101.0 next 1.0 many   many 
    wow

ManyGon
-------

Coordinates of a ManyGon are an array of LinearRing coordinate arrays. The
first element in the array represents the exterior ring. Any subsequent
elements represent interior rings (or holes).

No holes::

  such "type" is "ManyGon" next
    "coordinates" is so 
      so  so 100.0 next 0.0 many next so 101.0 next 0.0 many next so 101.0 next 1.0 many next so 100.0 next 1.0 many next so 100.0 next 0.0 many   many 
       many 
   wow

With holes::

  such "type" is "ManyGon" next
    "coordinates" is so 
      so  so 100.0 next 0.0 many next so 101.0 next 0.0 many next so 101.0 next 1.0 many next so 100.0 next 1.0 many next so 100.0 next 0.0 many   many next
      so  so 100.2 next 0.2 many next so 100.8 next 0.2 many next so 100.8 next 0.8 many next so 100.2 next 0.8 many next so 100.2 next 0.2 many   many 
       many 
   wow

ManyPoint
----------

Coordinates of a ManyPoint are an array of positions::

  such "type" is "ManyPoint" next
    "coordinates" is so  so 100.0 next 0.0 many next so 101.0 next 1.0 many   many 
    wow

ManyLineString
---------------

Coordinates of a ManyLineString are an array of LineString coordinate arrays::

  such "type" is "ManyLineString" next
    "coordinates" is so 
        so  so 100.0 next 0.0 many next so 101.0 next 1.0 many   many next
        so  so 102.0 next 2.0 many next so 103.0 next 3.0 many   many 
       many 
    wow

ManyManyGon
------------

Coordinates of a ManyManyGon are an array of ManyGon coordinate arrays::

  such "type" is "ManyManyGon" next
    "coordinates" is so 
      so so so 102.0 next 2.0 many next so 103.0 next 2.0 many next so 103.0 next 3.0 many next so 102.0 next 3.0 many next so 102.0 next 2.0 many many many next
      so so so 100.0 next 0.0 many next so 101.0 next 0.0 many next so 101.0 next 1.0 many next so 100.0 next 1.0 many next so 100.0 next 0.0 many many next
       so so 100.2 next 0.2 many next so 100.8 next 0.2 many next so 100.8 next 0.8 many next so 100.2 next 0.8 many next so 100.2 next 0.2 many many many 
       many 
    wow

VeryGeometry
------------------

Each element in the geometries array of a VeryGeometry is one of the
geometry objects described above::

  such "type" is "VeryGeometry" next
    "geometries" is so 
      such "type" is "Point" next
        "coordinates" is so 100.0 next 0.0 many 
        wow next
      such "type" is "LineString" next
        "coordinates" is so  so 101.0 next 0.0 many next so 102.0 next 1.0 many   many 
        wow
     many 
  wow

Appendix B. Contributors
========================

The GeoDogeON format specification is the product of discussion on the GeoDogeON
list:

http://lists.geoDogeON.org/listinfo.cgi/geoDogeON-geoDogeON.org
