Rasterio windows and masks
==========================

Rasterio 0.4 is up on PyPI: https://pypi.python.org/pypi/rasterio/0.4. The new
features since the last time I blogged about rasterio are `windowed
<https://github.com/sgillies/rasterio/blob/master/docs/windowed-rw.rst>`__ and
`masked
<https://github.com/sgillies/rasterio/blob/master/docs/nodata-masks.rst>`__
reads and writes. Here's an example that combines both features and demonstrates
a good pattern for reading raster data.

.. code-block:: python

    import numpy
    import rasterio
    
    with rasterio.open('rasterio/tests/data/RGB.byte.tif') as src:
        
        # The following asserts that all bands have the same block/stripe structure.
        assert len(set(src.block_shapes)) == 1
        
        # As they do, we can iterate over the first band's block windows
        # and apply to all other bands.
        for ji, window in src.block_windows(1):
    
            # Get data in this window for each band as a Numpy masked array.
            bands = [
                numpy.ma.masked_equal(
                    src.read_band(src.indexes[i]),
                    src.nodatavals[i])
                for i in range(src.count)]
    
            # Break out after the first block in this example.
            break
    
    # Let's look at the first, blue band.
    b = bands[0]
    print(b)
    print(b.mask)
    print(b.fill_value)
    print(b.min(), b.max(), b.mean())

The output:

.. code-block:: console

    [[-- -- -- ..., -- -- --]
     [-- -- -- ..., -- -- --]
     [-- -- -- ..., -- -- --]
     ...,
     [-- -- -- ..., -- -- --]
     [-- -- -- ..., -- -- --]
     [-- -- -- ..., -- -- --]]
    [[ True  True  True ...,  True  True  True]
     [ True  True  True ...,  True  True  True]
     [ True  True  True ...,  True  True  True]
     ...,
     [ True  True  True ...,  True  True  True]
     [ True  True  True ...,  True  True  True]
     [ True  True  True ...,  True  True  True]]
    0
    (1, 255, 44.434478650699106)

You can see that all four corners of the test image are masked.

.. author:: default
.. categories:: Programming
.. tags:: rasterio, python, gdal, numpy, pixels, cython
.. comments::
