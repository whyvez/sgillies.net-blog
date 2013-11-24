Introducing rasterio
====================

Ever since I wrote `Fiona <https://github.com/Toblerity/Fiona>`__, I've been
asked if I have plans to do something similar for geospatial raster data.
Having been out of the raster business for a few years, I always said "no
serious plans, just blue-sky ideas." Today, I'm back in satellite image
processing and very much want and need something like Fiona-for-rasters.
`Rasterio <https://github.com/sgillies/rasterio>`__ is my attempt to write such
a Python package.

Whereas Fiona is about reading and writing GeoJSON-like objects from and to
vector data files, rasterio exists to let Python programmers read and write
Numpy arrays (or other array-like objects providing the `PEP 3118 buffer
interface <http://www.python.org/dev/peps/pep-3118/>`__) from and to raster
data files. I intend rasterio to be fast and gotcha-free and let programmers
do the things they do now with osgeo.gdal, but with familiar Python idioms and
much less code.

I've just uploaded to PyPI `rasterio 0.2
<https://pypi.python.org/pypi/rasterio/0.2>`__. Share and enjoy.

Briefly, here's how you use rasterio to get the dimensions, number of bands,
indexes of the bands, coordinate reference system, and coordinate transform
matrix from a GeoTIFF.

.. code-block:: pycon

    >>> import rasterio
    >>> with rasterio.open('rasterio/tests/data/RGB.byte.tif') as src:
    ...     print(src.width, src.height)
    ...     print(src.count)
    ...     print(src.indexes)
    ...     print(src.dtypes)
    ...     print(src.crs)
    ...     print(src.transform)
    ...
    (791, 718)
    3
    [1, 2, 3]
    [<type 'numpy.uint8'>, <type 'numpy.uint8'>, <type 'numpy.uint8'>]
    {u'units': u'm', u'no_defs': True, u'ellps': u'WGS84', u'proj': u'utm', u'zone': 18}
    [101985.0, 300.0379266750948, 0.0, 2826915.0, 0.0, -300.041782729805]

Rasterio subscribes to PROJ.4 style dicts for coordinate reference systems, as
Fiona does. The indexes attribute contains the indexes of all image bands, and
those are used to access bands and analyze them like so.

.. code-block:: pycon

    >>> with rasterio.open('rasterio/tests/data/RGB.byte.tif') as src:
    ...     npixels = src.width * src.height
    ...     for i in src.indexes:
    ...         band = src.read_band(i)
    ...         print(i, band.min(), band.max(), band.sum()/npixels)
    ...
    (1, 0, 255, 29.94772668847656)
    (2, 0, 255, 44.516147889382289)
    (3, 0, 255, 48.113056354742945)

Rasterio is written using Cython, so it's fast and has Python 2/3
compatibility. I'm testing with Python 2.7 and Python 3.3 and have it deployed
on Python 2.7 at work (where the benchmarks are very positive).  I hope you'll
find rasterio interesting and `follow or fork
<https://github.com/sgillies/rasterio>`__.

.. author:: default
.. categories:: none
.. tags:: none
.. comments::
