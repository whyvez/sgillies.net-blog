Rasterio 0.5
============

.. code-block:: console

    $ pip install 'rasterio>=0.5'

Which is to say, share and enjoy.

Here's a script that shows off everything new in rasterio 0.5: `GDAL driver
environments
<http://sgillies.net/blog/2014/01/22/rethinking-driver-management-in-fiona-and-rasterio.html>`__,
raster feature sieving, and a generator of raster feature shapes.

.. code-block:: python

    #!/usr/bin/env python
    #
    # sieve: demonstrate sieving and polygonizing of raster features.

    import subprocess

    import numpy
    import rasterio
    from rasterio.features import sieve, shapes

    # Register GDAL and OGR drivers.
    with rasterio.drivers():
        
        # Read a raster to be sieved.
        with rasterio.open('rasterio/tests/data/shade.tif') as src:
            shade = src.read_band(1)
        
        # Print the number of shapes in the source raster.
        print "Slope shapes: %d" % len(list(shapes(shade)))
        
        # Sieve out features 13 pixels or smaller.
        sieved = sieve(shade, 13)

        # Print the number of shapes in the sieved raster.
        print "Sieved (13) shapes: %d" % len(list(shapes(sieved)))

        # Write out the sieved raster.
        with rasterio.open('example-sieved.tif', 'w', **src.meta) as dst:
            dst.write_band(1, sieved)

    # Dump out gdalinfo's report card and open (or "eog") the TIFF.
    print subprocess.check_output(
        ['gdalinfo', '-stats', 'example-sieved.tif'])
    subprocess.call(['open', 'example-sieved.tif'])

The images that you can operate on with the new functions in rasterio.features
don't have to be read from GeoTIFFs or other image files. They could be purely
numpy-based spatial models or slices of multidimensional image data arrays
produced with scipy.ndimage.

For fun and benchmarking purposes I've written a program that uses both Fiona and rasterio to emulate GDAL's
gdal_polygonize.py: `rasterio_polygonize.py <https://github.com/sgillies/rasterio/blob/master/examples/rasterio_polygonize.py>`__. If you're interested in integrating these modules, it's a good starting point.

.. author:: default
.. categories:: Programming
.. tags:: python, rasterio, fiona, pixels, gdal, numpy, features
.. comments::
