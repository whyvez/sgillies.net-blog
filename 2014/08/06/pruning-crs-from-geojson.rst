Pruning CRS from GeoJSON
========================

I uploaded `version 4 of the GeoJSON I-D
<http://tools.ietf.org/html/draft-butler-geojson-04>`__ to the IETF's tracker
yesterday. It contains a `major change to section
3 <http://tools.ietf.org/rfcdiff?url2=draft-butler-geojson-04.txt>`__. In
version 3, the draft contained more or less the same text as in
http://geojson.org/geojson-spec.html, but version 4 declares that coordinate
reference systems other than the default are not recommended and that means of
describing them, *including the CRS object of the original 2008 spec*, are now
application specific concerns. In other words, if you want projected
coordinates in the GeoJSON that travels between the front and back ends of your
web app you're on your own. Furthermore, you're doing it wrong if you
publish this projected GeoJSON to the open web and expect processors to have
access to an EPSG database.

I've been watching the IETF JSON Working Group's JSON, I-JSON, and JSON
Sequence discussions closely while revising the GeoJSON I-D. Version 4 treats
CRS like RFC 7159 treats `character encoding
<http://tools.ietf.org/html/rfc7159#section-8>`__, acknowledging other
coordinate reference systems while making a very strong recommendation for
using the default CRS. You could say CRS84 is our UTF-8. Version 4 also requires
that coordinates not be ordered latitude, longitude. Lat/lng is like our byte
order mark.

Removing the CRS object description from the draft has been a goal of mine from
the start. Its poor design has been a distraction and it never was as useful to
developers as we intended.  The GeoJSON draft is better without it. I get the
impression that some standards people will see its removal from the draft as
a void to be filled.  CRS wonks gotta wonk, I suppose, but do developers care
very much that there is no JSON equivalent of ``<gml:ProjectedCRS>``? I don't
think so.

.. author:: default
.. categories:: Web
.. tags:: geojson, ietf, standards, web
.. comments::
