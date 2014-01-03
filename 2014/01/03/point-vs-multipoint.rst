Point vs MultiPoint
===================

I got some `feedback <https://twitter.com/sgillies/status/418836645438160896>`__
about yesterdays blog post. To be clear, I'm not
suggesting that multipart geometry types are not needed. Rather, I think it's the
single part types that may be superfluous.

A couple of you suggest that single points and sets of points are different
from the higher dimensional (curve and surface) cases. What exactly sets these
geometry types apart and is the argument for setting them apart a geometrical
one? Consider the two WKT strings below and the geometric point sets they
describe.

.. code-block:: text

    POINT(0 0)
    MULTIPOINT((0 0))

You're banking on the distances from either of these objects to any other
object, or a 50-meter buffer zone around either object, being the same. In
a Simple Features world, you're also counting on these objects being *equal*
(See section 2.1.13.3, Named Spatial Relationship predicates based on the
DE-9IM, in OGC 99-049). Applications using Shapely, to name one category of
users, are certainly banking on it.

.. code-block:: pycon

    >>> from shapely.geometry import Point, MultiPoint
    >>> coords = (0.0, 0.0)
    >>> Point(coords).equals(MultiPoint([coords]))
    True

If a single point with coordinates ``x,y`` is equivalent to a set of points
with a single member (same coordinates ``x,y``), why do we trouble ourselves
with different representations for these things? I'm sure historical reasons
are a big part of the answer. Not just "Feature B followed Feature A"
historical reasons, but point-in-time historical reasons. I've a half-baked
thesis that the late 90s and early 00s are partly characterized by information
modeling practices that tended to produce the greatest possible number of
classes rather than the least sufficient number of classes.

Just thinking out loud here. And GeoJSON is the context for my thinking: how it
came to be and how things might have been done differently. And how things
might be done differently the next time around.

.. author:: default
.. categories:: Programming
.. tags:: geometry, ogc, nogc, geojson
.. comments::
