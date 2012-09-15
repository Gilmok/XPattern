XPattern 1.0
==================================
XPattern is an alternative to XSLT and uses regular expression style syntax to find and replace elements, attributes, and text in XML documents.
==================================
Verion notes:
1.0: Initial upload
==================================
Quick Reference:

FIND
================================
x: find Element
@x: find Attribute
=: find Attribute with value
": find Text
CDATA: find CDATA section
!--: find Comment
!: Negate search
-: Without search
/: Turn on regular expression matching in text, CDATA, or attribute values
*: Wildcard
---------------------
/: Go up the tree
\: Go down the tree
>: Next sibling
<: Previous sibling
---------------------
*: Find zero or more elements or attributes
+: Find one or more elements or attributes
?: Find zero or one elements or attributes
---------------------
(: Begin group selection
): End group selection
---------------------
#n: Find the nth paramaterized item


REPLACE
================================
x: insert Element
@: insert Attribute
": insert Text
CDATA: insert CDATA section
------------------------------
/: Go up the tree
\: Go down the tree
>: Go to the next sibling
<: Go to the previous sibling
------------------------------
$n: Insert the nth group, without preserving children
$`n and $': Insert the text found before the nth group and after the nth group (text and regexes only)
&n: Insert the nth group, preserving children
%n: Insert only the children of the nth group
^n: Move the insertion point to the nth group
------------------------------
#n: Insert the nth paramaterized item



XPATTERN GUIDE
===================================
XPattern is used to find and replace elements and attributes in XML documents.
To use XPattern, simply make an XPattern object with the Xml node or document you wish to perform find and/or replacement operations on.  See the code in XPatternTest to see how this is done.
===================================

FINDING ELEMENTS

To find elements, simply put the name of the element you wish to find.
Example:  "syntax" finds all the syntax elements in the document.

You can find elements that exist only on the specified xpath as well.  To specify an xpath, use the tokens / for up, \ for down, < for previous, and > for next.
Examples:
"param\desc" finds all the desc elements underneath param elements
"method\param>param" finds a param element next to a param element underneath a method element

Use a * to specify a wildcard find.
"method\*" finds all the children of the method node


FINDING ATTRIBUTES

To find attributes, simply put @ followed by the attribute you wish to find.
Example: "@name" finds all the name attributes

You can use attributes to filter element finding.
Exapmle: "method@name" finds all the method elements with a name attribute.

You can specify attributes with certain values using the = sign.
Example: "method@name=find" finds method elements with a name attribute whose value is "find".

You can specify multiple attributes by chaining them together.
Example: "method@name=find@paramCount=3" finds method elements with a name attribute whose value is find and a paramCount attribute whose value is 3.

Use a * to specify a wildcared find.
Example: "method@*" finds a method element with any attribute
Example: "method@*=replace" finds a method element with any attribute whose value is "replace".

For values, use a / to specify a regular expression search
Example: "method@name=/find" finds a method element with a name attribute whose value matches "find" according to a regular expression


FINDING TEXT, CDATA, AND COMMENTS

To find a CDATA node, specify CDATA in a search.
Example: "sample\CDATA" finds the CDATA node underneath the sample element

To find a comment node, use !--.
Example: "sample\!--" finds the comment node underneath the sample element

To find a text node, use the quote mark (").  Put a * after the text node to specify that you want to match any text.
Example: "sample\desc\"*" finds a text node underneath a desc element underneath a sample element

You can specify the text you wish to match.
Example: "name\"replaceAll" finds the text node whose text is "replaceAll" underneath a name element

Use / to specify a regex search for the text you wish to match.
Example: "name\"/All" finds the text node whose text matches "All" underneath a name element


NEGATED SEARCHES

Use the ! to negate any search for elements, attributes, attribute values, or text.
Example: "method\!param" finds any node that is not a param underneath a method element
Example: "method@!name*" finds all attributes that are not name in the method element
Example: "desc\"!/class" finds all text nodes with text that does not match "class" according to a regular expression underneath a desc element


WITHOUT SEARCH

Use the - to specify finding elements without certain properties.
Example: "method\-desc" finds method elements with no desc elements underneath them
Example: "method@-param" finds method elements with no param attributes
Example: "param<-param" finds a param element with no param elements before it
Example: "param>-param" finds a param element with no param elemetns after it


QUANTIFIERS

Use ? to specify zero or 1, * to specify zero or more, and + to specify one or more.
Example: "param*" matches 0 or more param elements
Example: "method\desc+@name" matches 1 or more desc nodes with a name attribute underneath a method element 
Example: "method@rtnType?" mathes a method that might have a rtnType attribute


GROUPING

Use the ( and ) to begin and end groups, respectively.
Example: "(var)(@**)\(desc)\"/(no)"
In this example, group 1 contains the var element, group 2 contains each attribute of the var element, group 3 contains the desc element, and group 4 contains the text "no" which matches text in the desc element
Example: "method@name=(*)"  group 1 contains the value of the name attribute in the method element


PARAMETERIZATION

Use the #n to insert the nth parameterized value in your search.
Example: "var@name=#0" finds the var whose value is specified in the first passed in parameter.
Example: "var@#0=#1" finds the var with an attribute specified in the first passed in parameter and whose value is the second passed in parameter


REPLACEMENT

When doing replacement, XPattern uses a found pattern, notes where that pattern is, and removes all nodes found in the find pattern.  It then uses the supplied replacement pattern to insert nodes where the found pattern was found.

INSERTING ELEMENTS

To insert elements, simply input the name of the element you wish to find.
Example: "var", "variable"  replaces each var element with a variable element

You can insert more than one element by chaining them together with a directional token.  / moves up the tree, \ moves down the tree, > goes to the next node, and < goes to the previous node.
Example: "method", "method\name>returnType>params\param/>desc" replaces a method element with a method element, a name element, returnType elment, and params element underneath that, and a param element underneath the params element, and next to the param element a desc element
*Note the use of /> when moving back up the tree; you need to specify a direction to go after moving up the tree (simply putting a / may result in an error)


INSERTING ATTRIBUTES

To insert attributes, simply put the name of the attribute you want to insert
Example: "var", "variable@name" inserts a name attribute into the variable element

To insert attribute values, put the value you want to insert after an = sign after the attribute
Example: "var", "variable@name=myName" inserts a name attribute with a value of "myName" into the variable element


INSERTING TEXT AND CDATA

To insert a CDATA section, simply put CDATA.
Example: "snippet", "snippet\CDATA" inserts a CDATA section under the found snippet element.

To insert text, use the quote sign (").  You can specify what text is inserted after the " sign.
Example: "desc", "desc\"Description here" inserts a text node stating "description here" underneath the inserted desc element.


WORKING WITH GROUPS

XPattern supports several operations when working with groups captured in a find operation.  How XPattern treats the group depends on the contents of the group:
Group contains elements or attributes: group treated as nodes
Group contains text: group treated as a value

$n: Copy the nth group
$n copies the contents of the nth group.  If the group is a group of nodes, no children of those nodes are copied.
You can make elements and attributes with values, but you cannot make values out of nodes
Example: "(var)", "$1" copies the captured var element and puts it back in, without children
Example: "(var)\"(*)", "$1@$2=on"  copies the captured var element without children and inserts an attribute whose name is the captured text underneath the var element and sets that attribute's value to "on"

If multiple nodes are captured, they are replaced consecutively on the same level.  It is not currently recommended that you group multiple nodes on different levels into the same group.
Example: "method\(var+)", "method\vars\$1"  puts a var element under a method element and copies all var elements found underneath the vars element.  

For regular expression captures, you can specify $`n to grab the text found before the regex match and $' to specify text found after the regex match.
Example: ""/(var)", ""$`1>ref\"$1/>$'1" grabs the var text in each text node and wraps it in a ref element

 
&n: Copy the nth group and its children
&n copies the contents of the nth group along with its children.  It is undefined for values.
Example: "(method)\(var)", "&2\$1" flips the location of the var and method elements on the tree.  (Note that "&2\&1" is likely to end up in an infinite loop)
Example: "(var+)", "&1" essentially does nothing, as you are replacing the found var elements with themselves.

Like $n, if multiple groups are captured, they are placed consecutively on the same level.


%n: Copy the nth group's children
%n copies only the children of the nodes found in the nth group.  It is undefined for values.
This is a useful operation for renaming nodes.
Example: "(var)(@**)" "variable@$2%1" renames each var element to a variable element while preserving the attributes and children of each old var element.

This operation is only defined for the first node; it is not defined for groups containing more than one node.


^n: Move the insertion point
^n moves the insertion point to a captured group.  If ^n is found at the start of a replacement pattern, only nodes found after the group will be removed before initial insertion.
Example: "class\method\(var)", "^1variable" replaces var elements with variable elements underneath method elements in class elements


PARAMETERIZATION

Like find opearations, use the # to insert a parameterized value.  These are treated as non-interpolated values.


