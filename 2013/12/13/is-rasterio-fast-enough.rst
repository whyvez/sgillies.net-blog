Is rasterio fast enough?
========================

I've been working on a Python package named
`rasterio <https://github.com/sgillies/rasterio>`__ and have
`blogged <http://sgillies.net/blog/2013/11/24/introducing-rasterio.html>`__
about my goals: more Python idioms, no gotchas, and speed. I'm doing well on
the first two, but now let's look at the last of these. Is rasterio fast
enough?

Here is a raster band read to Numpy ndarray benchmark with a comparison to 
osgeo.gdal. The RGB.byte.tif file is a small (about 800 x 800 pixel) GeoTIFF
file. I'm going to open the file, read the entirety of the first band to a
ndarray, and then close the file. The gdal Dataset class has no explicit
closing method, so I'm assigning the variable to None and indirectly freeing
up the GeoTIFF file. In rasterio, you should call an opened file's close()
method when done, or use a `with` statement.

.. code-block:: python

    import timeit
    
    import rasterio
    from osgeo import gdal

    n = 100

    # GDAL read as array
    s = """
    src = gdal.Open('rasterio/tests/data/RGB.byte.tif')
    arr = src.GetRasterBand(1).ReadAsArray()
    src = None
    """
    
    t = timeit.timeit(s, setup='from osgeo import gdal', number=n)
    print("GDAL:")
    print("%f usec\n" % (t/n))
    
    # Rasterio read band
    s = """
    with rasterio.open('rasterio/tests/data/RGB.byte.tif') as src:
        arr = src.read_band(1)
    """
    
    t = timeit.timeit(s, setup='import rasterio', number=n)
    print("Rasterio:")
    print("%f usec\n" % (t/n))

Here are the results on my 1.7 GHz Intel Core i7 MacBook Air:

.. code-block:: console

    $ python benchmarks/ndarray.py
    GDAL:
    0.002212 usec
    
    Rasterio:
    0.002637 usec

I'm pleased that Rasterio is only about 15% slower for this benchmark. And 
the difference between the two will diminish as files grow larger. Rasterio
does considerably more validation of input and logs quite a bit. Also, rasterio
could be a little more lazy about getting and formatting a file's coordinate
referencing system. Less code and fewer bugs is the main goal of rasterio, so
getting close to gdal.py performance-wise is icing on the cake.

.. author:: default
.. categories:: Programming
.. tags:: rasterio, python, gdal, numpy, pixels, cython, benchmark
.. comments::
