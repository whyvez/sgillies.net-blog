Warping images with rasterio
============================

Warping imagery from one spatial basis to another is a crucial function. I've
been determined that rasterio be able to do this. Scipy provides
a `geometric_transform()
<http://docs.scipy.org/doc/scipy/reference/generated/scipy.ndimage.interpolation.geometric_transform.html#scipy.ndimage.interpolation.geometric_transform>`__
function that I like, but I'd like rasterio to operate at a higher level and
take advantage of GDAL's fast transformers. GDAL's gdalwarp program only
operates on files, and I'm aiming lower than this (so rasterio can let us build
better scripts). The ``ReprojectImage()`` function in osgeo.gdal, as pointed
out in http://jgomezdans.github.io/gdal_notes/reprojection.html, only works on
GDAL dataset objects. And the code to make them gets messy.

My requirements for a rasterio warper are:

* Speed approaching gdalwarp.
* Same results (same algorithms) as gdalwarp.
* Sources and destinations can be ndarrays as well as bands in files.
* Simplicity and clarity.

I think I've managed to satisfy these requirements. The warp module will be the
key feature of the next rasterio release.

Here's an example within the interactive dataset inspector.

.. code-block:: console

    $ rasterio.insp rasterio/tests/data/RGB.byte.tif
    Rasterio 0.7 Interactive Inspector (Python 2.7.5)
    Type "src.name", "src.read_band(1)", or "help(src)" for more information.

The ``reproject()`` function takes source and destination ndarrays and an
affine transformation vector and coordinate reference system for each. In
this example, I'm warping the UTM source to a Web Mercator destination of slightly
smaller extent. I computed the extents of the destination using pyplot, rasterio
has yet no method to come up with an optimal destination extent (to do).

.. code-block:: pycon

    >>> source = src.read_band(1)
    >>> dst_transform = [
    ...     -8789636.708, 
    ...     300.0, 
    ...     0.0, 
    ...     2943560.235, 
    ...     0.0, 
    ...     -300.0]
    >>> dst_crs = {'init': 'EPSG:3857'}
    >>> destin = np.empty(src.shape, dtype=np.uint8)
    >>> from rasterio import _warp
    >>> _warp.reproject(
    ...     source, destin,
    ...     src.transform, src.crs,
    ...     dst_transform, dst_crs)
    >>> import matplotlib.pyplot as plt
    >>> plt.subplot(121)
    <matplotlib.axes.AxesSubplot object at 0x1075362d0>
    >>> plt.imshow(source)
    <matplotlib.image.AxesImage object at 0x1078b1450>
    >>> plt.gray()
    >>> plt.subplot(122)
    <matplotlib.axes.AxesSubplot object at 0x1075ffe50>
    >>> plt.imshow(destin)
    <matplotlib.image.AxesImage object at 0x1078b19d0>
    >>> plt.gray()
    >>> plt.savefig('/tmp/warp.png')

It's pretty much just three statements. One to get a source ndarray from a 
dataset. One to create a destination ndarray. One to call ``reproject()``.

I'm out of time to properly transform the axes in the plots to show coordinates
in their proper CRS, but they're warped correctly.

.. image:: http://farm4.staticflickr.com/3776/12773733534_c51761076e_z_d.jpg

.. author:: default
.. categories:: Programming
.. tags:: rasterio, gdal, python, pixels, warp, crs, projection
.. comments::
