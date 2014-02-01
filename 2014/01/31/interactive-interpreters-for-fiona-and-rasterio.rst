Interactive interpreters for Fiona and Rasterio
===============================================

I'm not sure why I didn't think of this before, but last night after the nth time
launching python in interactive mode, importing rasterio, and typing in a filename
with no tab completion, I made myself an interactive raster dataset inspector for
Rasterio. Oh, and yes, I made it for you, too. Then I made one for Fiona.

You've used Python's interpreter interactively before, yes? By which I mean typing
"python" at a shell prompt to get the banner and triple right-angle bracket prompt.

.. code-block:: console

    $ python
    Python 2.7.5 (default, Oct  3 2013, 20:27:52)
    [GCC 4.2.1 Compatible Apple LLVM 5.0 (clang-500.2.76)] on darwin
    Type "help", "copyright", "credits" or "license" for more information.
    >>>

I've used ``code.interact()`` to give you the same kind of prompt right after calling
``rasterio.open()`` on a filename you provide to the main function in ``rasterio.tool()``.
The code is `ridiculously simple <https://github.com/mapbox/rasterio/blob/master/rasterio/tool.py>`__.

.. code-block:: console

    $ python -m rasterio.tool rasterio/tests/data/RGB.byte.tif
    Rasterio 0.5.1 Interactive Interpreter
    Type "src.name", "src.read_band(1)", or "help(src)" for more information.
    >>>

Typing in the first two suggested expressions gives you useful entry points.

.. code-block:: pycon

    >>> src.name
    'rasterio/tests/data/RGB.byte.tif'
    >>> src.read_band(1)
    array([[0, 0, 0, ..., 0, 0, 0],
           [0, 0, 0, ..., 0, 0, 0],
           [0, 0, 0, ..., 0, 0, 0],
           ...,
           [0, 0, 0, ..., 0, 0, 0],
           [0, 0, 0, ..., 0, 0, 0],
           [0, 0, 0, ..., 0, 0, 0]], dtype=uint8)

it's a bit like gdalinfo on steroids. You can get statistics by using Numpy ndarray
methods, because rasterio has ndarrays instead of band objects.

.. code-block:: pycon

    >>> red = src.read_band(1)
    >>> red.min(), red.max(), red.mean()
    (0, 255, 29.94772668847656)

Fiona's inspector is in the ``fiona.inspector`` module.

.. code-block:: console

    $ python -m fiona.inspector docs/data/test_uk.shp
    Fiona 1.1.1 Interactive Inspector
    Type "src.name", "src.schema", "next(src)", or "help(src)" for more information.
    >>>

Its suggested expressions give you (with the help of ``pprint.pprint()``):

.. code-block:: pycon

    >>> src.name
    u'test_uk'
    >>> import pprint
    >>> pprint.pprint(src.schema)
    {'geometry': 'Polygon',
     'properties': OrderedDict([(u'CAT', 'float:16'), (u'FIPS_CNTRY', 'str'), (u'CNTRY_NAME', 'str'), (u'AREA', 'float:15.2'), (u'POP_CNTRY', 'float:15.2')])}
    >>> pprint.pprint(next(src))
    {'geometry': {'coordinates': [[(0.899167, 51.357216),
                                   (0.885278, 51.35833),
                                   (0.7875, 51.369438),
                                   (0.781111, 51.370552),
                                   (0.766111, 51.375832),
                                   (0.759444, 51.380829),
                                   (0.745278, 51.39444),
                                   (0.740833, 51.400276),
                                   (0.735, 51.408333),
                                   (0.740556, 51.429718),
                                   (0.748889, 51.443604),
                                   (0.760278, 51.444717),
                                   (0.791111, 51.439995),
                                   (0.892222, 51.421387),
                                   (0.904167, 51.418884),
                                   (0.908889, 51.416939),
                                   (0.930555, 51.398888),
                                   (0.936667, 51.393608),
                                   (0.943889, 51.384995),
                                   (0.9475, 51.378609),
                                   (0.947778, 51.374718),
                                   (0.946944, 51.371109),
                                   (0.9425, 51.369164),
                                   (0.904722, 51.358055),
                                   (0.899167, 51.357216)]],
                  'type': 'Polygon'},
     'id': '0',
     'properties': OrderedDict([(u'CAT', 232.0), (u'FIPS_CNTRY', u'UK'), (u'CNTRY_NAME', u'United Kingdom'), (u'AREA', 244820.0), (u'POP_CNTRY', 60270708.0)]),
     'type': 'Feature'}

I'd like to have proper program names for these soon instead of "python -m rasterio.tool",
but haven't thought of any yet. Also, I will gladly accept patches that would use your
own favorite other interpreters (bpython, IPython) if available.

.. author:: default
.. categories:: Programming
.. tags:: python, rasterio, fiona
.. comments::
