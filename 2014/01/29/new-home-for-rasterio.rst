New home for rasterio
=====================

As it's getting more use at work, I've `moved rasterio
<https://github.com/mapbox/rasterio/issues/25>`__ to Mapbox's GitHub
organization and wrote about it on the `Mapbox blog
<https://www.mapbox.com/blog/rasterio-announce/>`__. Mapbox is probably known more
for Javascript, but has a lot of Python programs running at any moment and more in
development.

What do I mean when I write that rasterio is Python software, not GIS software?
There is GIS software under the hood, obviously, and rasterio would be nowhere
without the excellent `GDAL <http://www.gdal.org>`__ library. What I mean is
that whenever faced with a design choice between the way something would be
done in a name brand GIS program and the way it would be done in Python,
I always choose the Python way. That means I/O is done via file-like objects.
That means no reliance on `side effects
<http://sgillies.net/blog/2013/12/17/teaching-python-gis-users-to-be-more-rational.html>`__.
Python dicts instead of GIS-specific types or microformatted strings. It means
embracing Python idioms and protocols. Making things iterable and zip-able. It
means `PEP 8 <http://www.python.org/dev/peps/pep-0008/>`__ and `PEP 20
<http://www.python.org/dev/peps/pep-0020/>`__.

This is not to say that I think Python is the best language for every
application or that Python language design is always right. I'm increasingly
a polyglot and I expect that you are, too, or will be soon. It seems clear to
me that one of the main keys to successful polyglot programming isn't using
interfaces that stick to the common features of languages, it's using the best
parts of various languages to their fullest. I believe this means idiomatic
Javascript in your Javascript programs (for example) and idiomatic Python in
your Python programs. Celebrating Python is what I'm doing to make rasterio
a better tool for polyglots.

Bill Dollins `recently wrote about some of the characteristics of GIS software 
<http://blog.geomusings.com/2013/11/27/consider-the-alternative/>`__, and
here is the crux of it:

  So I have come to realize that the mainstream GIS community has become very
  much like my professor’s theoretical cat; conditioned to take the long way to
  the end result when more direct paths are clearly available. What’s more,
  they have become conditioned to think of such approaches as normal.

Python, on the other hand, is supposed to be direct, highly productive, and fun.
I've chosen to make rasterio less like the GIS software Bill wrote about and
more like ElementTree, simplejson, Requests, pytest, and Numpy – packages that
make the most of Python's strengths.

By the way, I wrote to the Python Software Foundation's Trademark Committee to
make extra sure that my derived work (the GeoJSON file at
https://gist.github.com/sgillies/8655640#file-python-powered-json) was legit,
and received a very clear and friendly response from David Mertz in the
affirmative. I've been a reader of his articles for years and it was a pleasure
to correspond with him at last. Python isn't just a great language, it's great
people.

.. author:: default
.. categories:: Work
.. tags:: python, rasterio, gis, nogis, zen
.. comments::
