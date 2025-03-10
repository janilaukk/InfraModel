{{schemafile ../schema/inframodel-raw.xsd}}
# File headers

## XML file header

Inframodel XML files shall use UTF-8 character encoding, and encoder attribute shall be set on XML header.

Example:

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

NB: even if it is possible to use scandic characters in Inframodel XML files, they should not be used in **name**-attribute values, e.g. "väylä 2" should be spelled "vayla 2".


## LandXML container

The namespaces in Inframodel file shall be the following:

- The default namespace to be used without prefix for all LandXML elements specialized for Inframodel in schema inframodel.xsd shall be set in the root element.
- The namespace for elements in Inframodel extension schema im.xsd (if used in the file) to be used with prefix "im" shall be set in the root element
- The types in Inframodel enumerations schema inframodelEnumerations.xsd are included in the default namespace

**Note: The namespace URI is not meant to be used to look up information. Its sole purpose is to give the namespace a unique name.**


The schema locations may be set in an Inframodel transfer file, in which case XML Schema Instance namespace shall be set in the root element: xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

The following schema locations can be set to access online:

- The schema location for the default namespace
- If elements from im namespace are used in the file, the "im.xsd" schema location

The Inframodel enumerations schema inframodelEnumerations.xsd location is set automatically by being included by the default schema


The root element (\<LandXML>) of the transfer file is used by software to check the validity of the file structure.

{{xtabulate4 LandXML}}


## Units

The units used in the file are defined by the \<Units> element. Only certain metric SI system units are allowed, and those are defined under the sub-element \<Units>\<Metric>.

{{xtabulate Units}}

{{xtabulate Metric}}

## Coordinate and height systems

The height and coordinate system information is defined in the element \<CoordinateSystem>. 
Exactly one coordinate system shall be defined:

- By defining the \<CoordinateSystem>.epsgCode using the European Petrol Survey Group (EPSG) naming system without a prefix.
- Or by defining local system(s) with \<CoordinateSystem>.horizontalCoordinateSystemName (and optionally the \<CoordinateSystem>.verticalCoordinateSystemName)  

\<CoordinateSystem>.desc attribute may provide informal information about the system used).

It is also possible to set a rotationAngle for the coordinate system.


{{xtabulate CoordinateSystem}}



### Local coordinate transformation by point pairs

Local coordinate system may be defined as set of control points sourceCRS-targetCRS point pairs under "IM_coordTransformation" \<Feature> extension. 
See {{refto IM_coordTransformation-feature}} for detailed information about "IM_coordTransformation" \<Feature> extension.

### Local coordinate transformation by transformation parameters

The exact parameters of a particular local coordinate transformation may be given using \<im:LocalCoordinateTransformation> element in im-extension schema as \<any> element under \<LandXML>. 
The im namespace xml schema (im.xsd) for the extension schema elements is available at Inframodel schema page.

{{xtabulate LocalCoordinateTransformation ../schema/im.xsd}}

Coordinate systems use a reference ellipsoid, defined by the semi-major and semi-minor axis, to approximate the shape of the Earth. The datum is then defined by the ellipsoid and its location and orientation, i.e. different datums can use the same ellipsoid but its position varies.

For example, the WGS84 system uses a reference ellipsoid with a semi-major axis of 6 378 137m and a semi-minor axis of 6 356 752m. The ellipsoid center is located at the Earth's center of mass.

1. SourceCRS

{{xtabulate SourceCRS ../schema/im.xsd}}

where

{{xtabulate Ellipsoid ../schema/im.xsd}}

{{xtabulate PrimeMeridian ../schema/im.xsd}}

2. TargetCRS

{{xtabulate TargetCRS ../schema/im.xsd}}

3. DatumTransformation

Helmert3D \<im:DatumTransformation>.\<im:Helmert3D> performs a coordinate transformation from one datum to another.

{{xtabulate Helmert3D ../schema/im.xsd}}

4. Projection

Transverse Mercator Map projection \<im:Projection>.\<im:TransverseMercator> transforms geographical coordinates (latitude, longitude, altitude) to a plane (x, ,y, z). The grid origin is taken on the central latitude and longitude, and false easting and northing is then applied to prevent negative coordinates west or south of the origin.

{{xtabulate TransverseMercator ../schema/im.xsd}}

5. Local transformation

In LocalTransformation \<im:LocalTransformation>, Helmert2D \<im:Helmert2D> transforms the projected (x,y,z) coordinates to the local coordinate system. FittedPlane \<im:FittedPlane> corrects height values using a plane as the geoid model. The corrected height at point (x,y,z) is z_corrected = z + (a*x + b*y +c).

{{xtabulate Helmert2D ../schema/im.xsd}}

{{xtabulate FittedPlane ../schema/im.xsd}}

## Project

\<Project> element defines base data of the project, including it's name description and classification system definitions. 

The description may contain ie. the project long name or code. 

The state attribute may be used to describe the state of the project and its content. Sub-elements of the file however may override the state value defined here by setting their own state attribute.

{{xtabulate5 Project}}

where:

{{xtabulate stateType}}

Detailed information about "IM_codings", "IM_proprietaryCodings" and "IM_userDefinedProperties" \<Feature> extensions can be found from {{refto IMFeatureEXT}}

- "IM_codings" \<Feature> extension,{{refto IM_codings-feature}}
- "IM_proprietaryCodings" \<Feature> extension,{{refto IM_proprietaryCodings-feature}}
- "IM_userDefinedProperties" \<Feature> extension,{{refto IM_userDefinedProperties-feature}}  


## Type coding systems

The meaning (semantic) of the points, lines and surfaces is defined in the file. The parties of a project agree on type coding systems that are used in the data transfer.

The main coding systems are set in the "IM_codings" extension *(exactly one in each file)* using \<Feature> element under \<Project>, defining:

1. The terrain description coding system (source data points and breaklines)(*terrainCoding*)
2. The surface/category description coding system (*surfaceCoding*)
3. The coding system for infrastructure objects (including alignments and breaklines, pipe netweorks, plan features) (*infraCoding*)

The existing terrain description contains source data points and breaklines. The surface description consists of the individual surfaces of the base data (terrain and ground layers) or the planned route or areal structures as TIN surface model or string line model. In addtion to surfaces, planned objects may be described as alignment geometry, line strings or points. It is possible to set the same type coding system for more than one of these.

In addition to the main coding systems, it is also possible to define additional or alternative type coding systems *(none, one or more e.g. InfraRAK2.3, Vesilaitos X, Kaupunki Y etc.)*, using "IM_proprietaryCodings" extension (one instance per coding system) under \<Project>. When a code from a proprietary system is used for an element, each "IM_proprietaryCoding" \<Feature> instance placed under the element being coded shall identify the coding system by its property proprietaryInfraCodingSource, having the same value as the system name set in "IM_proprietaryCodings" property proprietaryInfraCoding.


## Application

The \<Application> element describes what software was used to create the file. If the file has been created using several different applications, all are described by their own \<Application> element.

{{xtabulate4 Application}}

## Authors

Information of the author of the file is recorded in the sub-element \<Application>.\<Author>. It is possible to define several authors as separate \<Author>-elements.

{{xtabulate4 Author}}

## Feature dictionary

The \<FeatureDictionary> identifies the specification source of extensions used in the file, and the point of access to their documentation.

The contents of \<Feature> elements shall follow the source specification. LandXML-files in general may contain extensions from several different sources. In Inframodel file transfer, proper recognition and interpretation is required only for the extensions documented in this specification ( e.g. for the type coding systems used in an Inframodel file).
  
The dictionary for Inframodel extensions shall be specified using \<FeatureDictionary> element as shown in the table below. 

The name attribute shall be unique, and always 'inframodel' for the dictionary of Inframodel extensions, and exactly the same value shall be set in every Inframodel \<Feature> for attribute source (the \<Feature> attribute code being labeled with IM_ -prefix).

The \<version> should match the version number of the Inframodel schema. 
  
Optional \<DocFileRef> element can be used to provide the URI link to named external documentation where applicable feature code and property type values are described ({{refto IMExtensions}} in the case of Inframodel feature dictionary).

{{xtabulate4 FeatureDictionary}}

Proprietary extensions can be included in addition to Inframodel extensions, as "IM_userDefinedProperties" (generic extension specified in Inframodel feature dictionary), with mandatory propertyLabel (name of the property from proprietary source) and propertyValue (value set for the property in this instance), and optional propertyDescription (description or definition of the property from proprietary source) and propertySource (identification of or reference to the property definition source).

## Metadata
  
Metadata is described with the **\<im:Metadata>** element (specified im-extension schema) as **\<any>** element under **\<LandXML>**. The im namespace xml schema (im.xsd) for the extension schema elements is available at Inframodel schema page.

Metadata is optional and enables the following features shown below.
  
{{xtabulate IM-Metadata}}
