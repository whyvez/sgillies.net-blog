Python at FOSS4G 2014
=====================

There were plenty of other Python talks at FOSS4G and I plan to watch them when
the videos are online (**update**: talks are appearing now at
http://vimeo.com/foss4g). I haven't been aware of `ogrtools
<https://github.com/sourcepole/ogrtools>`__, which is unlucky because there's
plenty of functional overlap between it and Fiona. The designs seem rather
different because Fiona doesn't emulate XML tool chains (GDAL's VRTs are not
unlike XSLT) and is more modular. For example, where ogrtools has
a file-to-file ``ogr translate`` command, Fiona has a ``fio dump`` and ``fio
load`` pair connected by a stream of GeoJSON objects. The ogrtools talk is
right near the top of my list of talks to see.

I was very fortunate to go right after Mike Bostock's keynote. It got people
thinking about tools and design, and that's exactly the conversation that I'm
trying to engage developers in with Fiona and Rasterio, if with less insight
and perspective than Mike. I reminded attendees that the best features of our
day-to-day programming languages are sometimes disjoint and showed this diagram
(in which C is yellow, Javascript is magenta, and Python is blue. By "GC"
I mean garbage collection and by "{};" I mean extraneous syntax).

.. image:: http://sgillies.github.io/foss4g-2014-fiona-rasterio/img/py-js-c.png
   :width: 600

D3 embraces browser standards and all they entail (a world wide knowledge
base and continuous performance improvements) and Fiona and Rasterio embrace
the good parts of Python. Written as C, like we usually see in GDAL/OGR
examples on the web, Python is quite slow. Idiomatic Python, including the
good parts like list comprehensions, generators, and iterators, is dramatically
faster. While Fiona and Rasterio don't do particular operations faster than the
older GDAL and OGR bindings (because it's the same C library underneath), they
are designed from the bottom up for a good fit with more efficient idiomatic
Python code.

I plugged `Click <http://click.pocoo.org/3/>`__ and `Cython
<http://cython.org>`__ in my talk, too, and discussed them afterwards. I found
tons of interest in Python at FOSS4G and lots of good ideas about how to use
it.

I confess that I didn't pay a lot of attention to the talk schedule before the
conference. My summer was kind of nuts and I don't subscribe to any OSGeo
lists. When I did look closely I was surprised to find that many people were
giving two talks and some three. If any woman or first-timer didn't get
a chance to speak while some dude got three (and the multiple talkers were all
men and long time attendees as far as I can tell) – that's a bug in the talk
selection that needs to be fixed before the next edition.

Lastly, I think the views of Mount Hood you get when flying in and out of PDX to
destinations south and east are worth the airfare all by themselves.

.. image:: https://farm6.staticflickr.com/5587/15249959145_91e47b3444_c_d.jpg
   :target: https://www.flickr.com/photos/by-sgillies/15249959145/
   :width: 600
   :height: 800

.. author:: default
.. categories:: Programming
.. tags:: foss4g, conference, fiona, rasterio, python, design, gdal, ogr
.. comments::
