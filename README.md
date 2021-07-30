# Well Known Text

This document provides a standalone reference describing the Well-known Representation of Geometry, which is fully specified in the 
[Simple Features Access](file:///Users/cholmes/Downloads/06-103r4_Implementation_Specification_for_Geographic_Information_-_Simple_feature_access_-_Part_1_Common_Architecture_v1.2.1.pdf)
standard, section 7 (TODO: provide a link directly to that section when there is an html version online).

**WARNING: This is a work in progress, and should not yet be considered definitive in any way.** But we hope to have a finished version relatively soon.

## About

TODO: Describe what WKT is and what it's used for.

### Contributing

This repository aims to be a definitive reference for Well Known Text. If you've ever been frustrated trying to find the official reference please help out! 
Pull Requests are encouraged. We'll need some official review before this becomes a definitive reference, but we should iterate to make it more useful. Lots
of todos throughout the text, and we'll likely evolve to using github issues. But we're also hoping to make this a 'little' thing, not a big deal, just get
to an iterative improvement and 'ship it'.

## Using Well Known Text

Well Known Text is formally defined below, but its easier to get a sense of it by starting with some examples.
| **Geometry Type** | **Text Literal Representation**                                                                                                                                                                                                                           | **Comment**                                                                             |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| Point             | POINT (10 10)                                                                                                                                                                                                                                             | a Point                                                                                 |
| LineString        | LINESTRING (10 10, 20 20, 30 40)                                                                                                                                                                                                                          | a LineString with 3 points                                                              |
| Polygon           | POLYGON ((10 10, 10 20, 20 20, 20 15, 10 10))                                                                                                                                                                                                             | a Polygon with 1 exteriorRing and 0 interiorRings                                       |
| Multipoint        | MULTIPOINT ((10 10), (20 20))                                                                                                                                                                                                                             | a MultiPoint with 2 points                                                              |
| MultiLineString   | MULTILINESTRING ((10 10, 20 20), (15 15, 30 15))                                                                                                                                                                                                          | a MultiLineString with 2 linestrings                                                    |
| MultiPolygon      | MULTIPOLYGON ( ((10 10, 10 20, 20 20, 20 15, 10 10)), ((60 60, 70 70, 80 60, 60 60 )) )                                                                                                                                                                   | a MultiPolygon with 2 polygons                                                          |
| GeomCollection    | GeometryCollection ( POINT (10 10), POINT (30 30), LINESTRING (15 15, 20 20) )                                                                                                                                                                            | a GeometryCollection consisting of 2 Point values and a LineString value                |
| Polyhedron        | Polyhedron Z ( ((0 0 0, 0 0 1, 0 1 1, 0 1 0, 0 0 0)), ((0 0 0, 0 1 0, 1 1 0, 1 0 0, 0 0 0)), ((0 0 0, 1 0 0, 1 0 1, 0 0 1, 0 0 0)), ((1 1 0, 1 1 1, 1 0 1, 1 0 0, 1 1 0)), ((0 1 0, 0 1 1, 1 1 1, 1 1 0, 0 1 0)), ((0 0 1, 1 0 1, 1 1 1, 0 1 1. 0 0 1)) ) | A polyhedron cube, corner at the origin and opposite corner at (1, 1, 1).               |
| Tin               | Tin Z ( ((0 0 0, 0 0 1, 0 1 0, 0 0 0)), ((0 0 0, 0 1 0, 1 0 0, 0 0 0)), ((0 0 0, 1 0 0, 0 0 1, 0 0 0)), ((1 0 0, 0 1 0, 0 0 1, 1 0 0)), )                                                                                                                 | A tetrahedron (4 triangular faces), corner at the origin and each unit coordinatedigit. |
| Point             | Point Z (10 10 5)                                                                                                                                                                                                                                         | A 3D Point                                                                              |
| Point             | Point ZM (10 10 5 40)                                                                                                                                                                                                                                     | the same 3D Point with M value of 40                                                    |
| Point             | Point M (10 10 40)                                                                                                                                                                                                                                        | a 2D Point with M value of 40                                                           |
|                   |                                                                                                                                                                                                                                                           |                                                                                         |

TODO: Revisit these examples, these are straight from the spec, but likely better to start with the core. Wikipedia does it much better, perhaps just copy 
theirs, with the nice pictures as well: https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry

## Definition

Note: The core spec has 4 BNF definitions, for 2d, 3d, and 'measured' versions of each. For now we'll just include the 2d one (non-measured)

### BNF Introduction

The Well-known Text Representation of Geometry is defined below using [BNF](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form).

 * The notation “{}” denotes an optional token within the braces; the braces do not appear in the output token
list.
* The notation "( )" groups a sequence of tokens into a single token; the parentheses do not appear in the
output token list.
* The notation "*" after a token denotes the optional use of multiple instances of that token.
* A character string without any modifying symbols denotes an instance of that character string as a single
token.
* The notation "|" denotes a choice of two tokens, and do not appear in the output token list,
* The notation "< >" denotes a production defined elsewhere in the list or a basic type.
* The notation ":=" is a production and the grammar on the left may be replaced with the grammar on the
right of this symbol. Production is terminated when no undefined production equations are left unresolved.

The text representation of the instantiable Geometry Types implemented shall conform to this grammar. Well
known text is case insensitive. 

*Note: All productions are segregated by coordinate type. This means that any two subelements of any element
will always have the same coordinate type, which will be the coordinate type of the larger containing
element.* 

The grammar in this and the following clause has been designed to support a compact and readable textual
representation of geometric objects. The representation of a geometric object that consists of a set of
homogeneous components does not include the tags for each embedded component. This first set of productions
is to define a double precision literal. 

```
<x> ::= <signed numeric literal>
<y> ::= <signed numeric literal>
<z> ::= <signed numeric literal>
<m> ::= <signed numeric literal>
<quoted name> ::= <double quote> <name> <double quote>
<name> ::= <letters>
<letters> ::= (<letter>)*
<letter> ::= <simple Latin letter>|<digit>|<special>
<simple Latin letter> ::= <simple Latin upper case letter>|<simple Latin lower case letter>
<signed numeric literal> ::= {<sign>}<unsigned numeric literal>
<unsigned numeric literal> ::= <exact numeric literal>|<approximate numeric literal> 
<approximate numeric
literal> ::=
<mantissa>E<exponent>
<mantissa> ::= <exact numeric literal>
<exponent> ::= <signed integer>
<exact numeric literal> ::= <unsigned integer>{<decimal point>{<unsigned integer>}}|<decimal point><unsigned integer>
<signed integer> ::= {<sign>}<unsigned integer>
<unsigned integer> ::= (<digit>)*
<left delimiter> ::= <left paren>|<left bracket> // must match balancing right delimiter
<right delimiter> ::= <right paren>|<right bracket> // must match balancing left delimiter
<special> ::= <right paren>|<left paren>|<minus sign>|<underscore>|<period>|<quote>|<space>
<sign> ::= <plus sign> | <minus sign>
<decimal point> ::= <period> | <comma>
<empty set> ::= EMPTY
<minus sign> ::= -
<left paren> ::= (
<right paren> ::= )
<left bracket> ::= [
<right bracket> ::= ]
<period> ::= .
<plus sign> ::= +
<double quote> ::= "
<quote> ::= '
<comma> ,
<underscore> ::= _ 
<digit> ::= 0|1|2|3|4|5|6|7|8|9
<simple Latin lower caseletter> ::= a|b|c|d|e|f|g|h|i|j|k|l|m|n|o|p|q|r|s|t|u|v|w|x|y|z
<simple Latin upper case letter> ::= A|B|C|D|E|F|G|H|I|J|K|L|M|N|O|P|Q|R|S|T|U|V|W|X|Y|Z
<space>= " "// unicode "U+0020" (space) 
```
TODO: Get some better formatting here. The [CQL.bnf](https://github.com/opengeospatial/ogcapi-features/blob/e4912fa8e6f285acbbc03b5595dbbab29659f4b2/extensions/cql/standard/schema/cql.bnf) 
feels a lot cleaner, but it seems to use different BNF, so need to figure out how that all works. These could probably go in separate documents, and keep the main
document cleaner - just talk through how it works and give examples

## BNF Productions for Two-Dimension Geometry WKT 

The following BNF defines two-dimensional geometries in (x, y) coordinate spaces. With the exception of the
addition of polyhedral surfaces, these structures are unchanged from earlier editions of this standard. 

```
<point> ::= <x> <y>
<geometry tagged text> ::= <point tagged text> | <linestring tagged text> | <polygon tagged text> | <triangle tagged text> | <polyhedralsurface tagged text> | <tin tagged text> | <multipoint tagged text> | <multilinestring tagged text> | <multipolygon tagged text>| <geometrycollection tagged text>
<point tagged text> ::= point <point text>
<linestring tagged text> ::= linestring <linestring text>
<polygon tagged text> ::= polygon <polygon text>
<polyhedralsurface tagged text> ::= polyhedralsurface <polyhedralsurface text>
<triangle tagged text> ::= triangle <polygon text>
<tin tagged text> tin <polyhedralsurface text>
<multipoint tagged text> ::= multipoint <multipoint text>
<multilinestring tagged text> ::= multilinestring <multilinestring text>
<multipolygon tagged text> ::= multipolygon <multipolygon text>
<geometrycollection tagged text> ::= geometrycollection
<geometrycollection text>
<point text> ::= <empty set> | <left paren> <point> <right paren> 
<linestring text> ::= <empty set> | <left paren> <point> {<comma> <point>}* <right paren>
<polygon text> ::= <empty set> | <left paren> <linestring text> {<comma> <linestring text>}* <right paren>
<polyhedralsurface text> ::= <empty set> | <left paren> <polygon text> {<comma> <polygon text>}* <right paren>
<multipoint text> ::= <empty set> | <left paren> <point text> {<comma> <point text>}* <right paren>
<multilinestring text> ::= <empty set> | <left paren> <linestring text> {<comma> <linestring text>}* <right paren>
<multipolygon text> ::= <empty set> | <left paren> <polygon text> {<comma> <polygon text>}* <right paren>
<geometrycollection text> ::= <empty set> | <left paren> <geometry tagged text> {<comma> <geometry tagged text>}* <right paren> 
```
