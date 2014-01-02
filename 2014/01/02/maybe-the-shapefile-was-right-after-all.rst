Maybe the Shapefile was right after all
=======================================

Sometimes I agree with Martin Daly's suggestion that the Shapefile was right
all along. Not about everything, of course, but about the lack of distinction
between the geometric objects that the OGC's `Simple Features
<http://en.wikipedia.org/wiki/Simple_Feature_Access>`__ model classifies as
polygons and multipolygons. What's the essential difference between a polygon
and a set of non-intersecting polygons with only one member? I'm not convinced
that there is one. Consider the features in the image below (from
http://education.usgs.gov/kids/tracks.html).

.. image:: http://farm3.staticflickr.com/2811/11631807246_b306a4a3bb_o_d.png

In the simple features model, you'd classify all of these nine-part prints as
multipolygon features. In the coyote's hind print, however, the slight merger
of the middle toe prints hints at what we might see if the prints were made in
a soft enough medium: single part contiguous prints.

Why would we want the representation in data of a print to change from
a multi-part multipolygon to a single polygon as we dilate it (by making it
deeper in this analogy)? If I was designing a format for the exchange of animal
print data, I'd like all prints to be of the same geometric type whether they
were shallow or deep. Wouldn't you? If not, why not? What would be the use of
having separate polygon and multi-polygon types and prints that flip-flop
between them depending on incidental conditions of their observation? And would
any use outweigh the cost of documenting and explaining the difference?

.. author:: default
.. categories:: Programming
.. tags:: geometry, ogc, nogc, geojson
.. comments::
