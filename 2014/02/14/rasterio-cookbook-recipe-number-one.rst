Rasterio cookbook recipe number one
===================================

I got a nice message today from a developer who found rasterio to be the right
tool for scaling the values of a 15 GB LiDAR DEM. Here's the code.

.. code-block:: python

    import numpy as np
    import rasterio

    """
    2014-02-13
    Bryan Luman
    Use it however you like at your own risk

    Problem:
    I have a huge DEM converted from LiDAR LAS points. I'd like to make
    it slightly less massive for further processing.

    All my z values are between 682 and 869 feet stored as float32. If
    I convert them to centimeters and they will fit well within a 16 bit
    unsigned integer.

    This saves a significant amount of storage space and greatly reduces
    any type of future analysis.

    """

    CM_IN_FOOT = 30.48

    with rasterio.drivers():
        with rasterio.open('massive_dem_as_float') as src:
            kwargs = src.meta
            kwargs.update(
                driver='GTiff',
                dtype=rasterio.uint16,
                count=1,
                compress='lzw',
                nodata=0,
                bigtiff='YES' # Output will be larger than 4GB
            )
        
            windows = src.block_windows(1)

            with rasterio.open(
                    'less_massive_dem_with_as_int.tif', 
                    'w', 
                    **kwargs) as dst:
                for idx, window in windows:
                    src_data = src.read_band(1, window=window)

                    # Source nodata value is a very small negative number
                    # Converting in to zero for the output raster
                    np.putmask(src_data, src_data < 0, 0)

                    dst_data = (src_data * CM_IN_FOOT).astype(rasterio.uint16)
                    dst.write_band(1, dst_data, window=window)

This uses the windowed read and write and windows iterator features of
rasterio that I worked on with `Asger Skovbo Petersen
<https://github.com/AsgerPetersen>`__.

Do you have any more short rasterio code examples? I'd love to start collecting
these into a cookbook of sorts.

.. author:: default
.. categories:: Programming
.. tags:: python, rasterio, pixels, lidar
.. comments::
