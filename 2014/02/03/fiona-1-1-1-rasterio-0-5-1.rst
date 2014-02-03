Fiona 1.1.1, rasterio 0.5.1
===========================

I released `Fiona 1.1.1 <https://pypi.python.org/pypi/Fiona/1.1.1>`__ and 
`rasterio 0.5.1 <https://pypi.python.org/pypi/rasterio/0.5.1>`__ yesterday. There
are a couple bug fixes, but the main features are the interactive data inspectors
fiona.insp

.. code-block:: console

    $ fiona.insp docs/data/test_uk.shp
    Fiona 1.1.1 Interactive Inspector (Python 2.7.5)
    Type "src.schema", "next(src)", or "help(src)" for more information.
    >>>

and rasterio.insp.

.. code-block:: console

    $ rasterio.insp rasterio/tests/data/RGB.byte.tif
    Rasterio 0.5.1 Interactive Inspector (Python 2.7.5)
    Type "src.name", "src.read_band(1)", or "help(src)" for more information.
    >>>

.. author:: default
.. categories:: Programming
.. tags:: fiona, rasterio, python
.. comments::
