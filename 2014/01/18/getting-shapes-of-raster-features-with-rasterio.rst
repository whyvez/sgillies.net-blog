Getting shapes of raster features with rasterio
===============================================

I've just added a new function to rasterio that generates the shapes of all
features in an image. The region labeling algorithm is provided by libgdal's
GDALPolygonize() and I'm using GDAL and OGR in-memory raster and vector
datastores to avoid disk I/O. Look, Ma, no temporary files.

In the interpreter session shown below, I made a 12 x 12 image with three different
features: the background with value 1, a 4 x 4 square in the upper left corner with
value 2, and a 2 x 2 square with value 5 on the left edge. Then I copied an
affine transformation matrix from the rasterio test suite and passed it and the
image to rasterio.features.shapes(). The result: the polygon boundaries of those
three features in "world", not image, coordinates.

.. code-block:: pycon

    >>> import pprint
    >>> import numpy
    >>> from rasterio import features
    >>> image = numpy.ones((12, 12), dtype=numpy.uint8)
    >>> image[0:4,0:4] = 2
    >>> image[4:6,0:2] = 5
    >>> image
    array([[2, 2, 2, 2, 1, 1, 1, 1, 1, 1, 1, 1],
           [2, 2, 2, 2, 1, 1, 1, 1, 1, 1, 1, 1],
           [2, 2, 2, 2, 1, 1, 1, 1, 1, 1, 1, 1],
           [2, 2, 2, 2, 1, 1, 1, 1, 1, 1, 1, 1],
           [5, 5, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
           [5, 5, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
           [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
           [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
           [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
           [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
           [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
           [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]], dtype=uint8)
    >>> t = [101985.0, 300.0379266750948, 0.0, 
    ...      2826915.0, 0.0, -300.041782729805]
    >>> for shape, value in features.shapes(image, transform=t):
    ...     print("Image value:")
    ...     print(value)
    ...     print("Geometry:")
    ...     pprint.pprint(shape)
    ...
    Image value:
    2
    Geometry:
    {'coordinates': [[(101985.0, 2826915.0),
                      (101985.0, 2825714.832869081),
                      (103185.15170670037, 2825714.832869081),
                      (103185.15170670037, 2826915.0),
                      (101985.0, 2826915.0)]],
     'type': 'Polygon'}
    Image value:
    5
    Geometry:
    {'coordinates': [[(101985.0, 2825714.832869081),
                      (101985.0, 2825114.7493036212),
                      (102585.0758533502, 2825114.7493036212),
                      (102585.0758533502, 2825714.832869081),
                      (101985.0, 2825714.832869081)]],
     'type': 'Polygon'}
    Image value:
    1
    Geometry:
    {'coordinates': [[(103185.15170670037, 2826915.0),
                      (103185.15170670037, 2825714.832869081),
                      (102885.11378002529, 2825714.832869081),
                      (102585.0758533502, 2825714.832869081),
                      (102585.0758533502, 2825114.7493036212),
                      (102285.0379266751, 2825114.7493036212),
                      (101985.0, 2825114.7493036212),
                      (101985.0, 2823314.4986072425),
                      (105285.41719342604, 2823314.4986072425),
                      (105585.45512010113, 2823314.4986072425),
                      (105585.45512010113, 2826915.0),
                      (103185.15170670037, 2826915.0)]],
     'type': 'Polygon'}

In its initial form, the function generated GeoJSON-ish feature dicts, but
I decided these were too heavy and have switched to a pair of objects: one
a GeoJSON style geometry dict and the other the labeled feature's
characteristic image value.

This example doesn't read or write because I want to show how the new function
doesn't require I/O. But of course you can always get the numpy array and
affine transformation matrix from a raster on disk using rasterio.open() and
read_band(), and the generated shapes can be written to a vector file on disk
using fiona.open() and writerecords(). 

.. author:: default
.. categories:: Programming
.. tags:: rasterio, python, pixels, geojson, gdal, numpy
.. comments::
