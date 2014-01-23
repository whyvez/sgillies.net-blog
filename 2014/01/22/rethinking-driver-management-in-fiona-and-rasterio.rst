Rethinking driver management in fiona and rasterio
==================================================

In a C program using the GDAL (or OGR) API, you manage the global format
driver registry like gdalinfo does:

.. code-block:: c
    
    #include "gdal.h"

    int main( int argc, char ** argv )
    {
        
        /* Register all drivers. */
        GDALAllRegister();

        ...

        /* De-register all drivers. */
        GDALDestroyDriverManager();

        exit( 1 );
    }

I used to try to hide this in Python bindings by calling
GDALAllRegister() in a module and registering a function that called
GDALDestroyDriverManager() with the atexit module.

.. code-block:: python

    """ gdal.py """

    import atexit

    def cleanup():
        GDALDestroyDriverManager()

    atexit.register(cleanup)

    GDALAllRegister()

Importing that gdal module registers all drivers and the drivers are
de-registered when the module is torn down. I don't like this approach
anymore. I prefer that driver registration to not be a side effect of module
import. And in testing, I'm finding that it's a drag to write the code
needed to keep the cleanup function around until the atexit handlers
execute.

Instead of going back to the C style, I'm going to go forward with
context managers and a ``with`` statement to manage the GDAL and OGR
global driver registries. The ``with rasterio.drivers()`` expression creates
a GDAL driver environment.

.. code-block:: python

    import rasterio

    try:

        # Use a with statement and context manager to register all the
        # GDAL/OGR drivers.
        with rasterio.drivers():

            # Count the manager's registered drivers (156 in this case).
            print rasterio.driver_count()

            # Open a file and get to work.
            with rasterio.open('rasterio/tests/data/RGB.byte.tif') as f:

                # Oops, an exception.
                raise Exception("Oops")

            # When this block ends, even via an exception, the drivers are
            # de-registered.

    except Exception, e:
        print "Caught exception: %s" % e

    # Drivers were de-registered and this will print 0.
    print rasterio.driver_count()

    # Output:
    # 156
    # Caught exception: Oops
    # 0

The nice thing about this approach is that the registered drivers get
de-registered and the memory deallocated no matter what happens in the
``with`` block. And it's less code than my old approach using atexit.

So that programs written for older versions of Fiona and rasterio don't break, I'm
also letting opened collections and datasets manage the driver registry if no other
manager is already present. So this code below works as before.

.. code-block:: python

    import rasterio
    
    try:

        with rasterio.open('rasterio/tests/data/RGB.byte.tif') as f:
            print rasterio.driver_count()
            raise Exception("Oops")

    except Exception, e:
        print "Caught exception: %s" % e

    print rasterio.driver_count()

    # Output:
    # 156
    # Caught exception: Oops
    # 0

It's more efficient to register drivers once and only once in your program, so 
use ``with fiona.drivers()`` or ``with rasterio.drivers()`` in new programs using 
`Fiona 1.1 <https://pypi.python.org/pypi/Fiona>`__ and 
`rasterio 0.5 <https://pypi.python.org/pypi/rasterio/0.5>`__ or newer. 

.. author:: default
.. categories:: Programming
.. tags:: python, c, gdal, ogr, fiona, rasterio, with, context manager
.. comments::
