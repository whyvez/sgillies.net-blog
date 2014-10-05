Unix style spatial ETL with fio cat, collect, and load
======================================================

In Fiona 1.4.0 I added a fio-cat command to the CLI which works much UNIX cat.
It opens one or more vector datastets, concatenating their features and
printing them to stdout as a sequence of GeoJSON features.

.. code-block:: console

    $ fio cat docs/data/test_uk.shp | head -n 2
    {"geometry": {"coordinates": [...], "type": "Polygon"}, "id": "0", "properties": {"AREA": 244820.0, "CAT": 232.0, "CNTRY_NAME": "United Kingdom", "FIPS_CNTRY": "UK", "POP_CNTRY": 60270708.0}, "type": "Feature"}
    {"geometry": {"coordinates": [...], "type": "Polygon"}, "id": "1", "properties": {"AREA": 244820.0, "CAT": 232.0, "CNTRY_NAME": "United Kingdom", "FIPS_CNTRY": "UK", "POP_CNTRY": 60270708.0}, "type": "Feature"}

I've replaced most of the coordinates with ellipses to save space in the code
block above, something I'll continue to do in examples below.

I said that fio-cat concatenates features of multiple files and you can see
this by using ``wc -l``.

.. code-block:: console

    $ fio cat docs/data/test_uk.shp | wc -l
          48
    $ fio cat docs/data/test_uk.shp docs/data/test_uk.shp | wc -l
          96

If you look closely at the output, you'll see that every GeoJSON feature is a
standalone text and each is preceded by an ASCII RS (0x1E) control character.
These allow you to cat pretty-printed GeoJSON (using the ``--indent`` option)
containing newlines that can still be understood as a sequence of texts by
other programs. Software like Python's json module and Node's underscore-cli 
will trip over unstripped RS, so you can disable the RS control characters and
emit CRLF delimited sequences of GeoJSON (with no option to pretty print, of
course) using ``--x-json-seq-no-rs``.

To complement fio-cat I've written fio-load and fio-collect. They
read features from a sequence (RS or CRLF delimited) and respectively write them
to a formatted vector file (such as a Shapefile) or print them as a GeoJSON 
feature collection.

Here's an example of using fio-cat and load together. You should tell fio-load
what coordinate reference system to use when writing the output file because
that information isn't carried in the GeoJSON features written by fio-cat.

.. code-block:: console

    $ fio cat docs/data/test_uk.shp \
    | fio load --driver Shapefile --dst_crs EPSG:4326 /tmp/test_uk.shp
    $ ls -l /tmp/test_uk.*
    -rw-r--r--  1 seang  wheel     10 Oct  5 10:09 /tmp/test_uk.cpg
    -rw-r--r--  1 seang  wheel  11377 Oct  5 10:09 /tmp/test_uk.dbf
    -rw-r--r--  1 seang  wheel    143 Oct  5 10:09 /tmp/test_uk.prj
    -rw-r--r--  1 seang  wheel  65156 Oct  5 10:09 /tmp/test_uk.shp
    -rw-r--r--  1 seang  wheel    484 Oct  5 10:09 /tmp/test_uk.shx

And here's one of fio-cat and collect.

.. code-block:: console

    $ fio cat docs/data/test_uk.shp | fio collect --indent 4 | head
    {
        "features": [
            {
                "geometry": {
                    "coordinates": [
                        [
                            [
                                0.899167,
                                51.357216
                            ],
    $ fio cat docs/data/test_uk.shp | fio collect --indent 4 | tail
                    "CAT": 232.0,
                    "CNTRY_NAME": "United Kingdom",
                    "FIPS_CNTRY": "UK",
                    "POP_CNTRY": 60270708.0
                },
                "type": "Feature"
            }
        ],
        "type": "FeatureCollection"
    }

Does it look like I've simply reinvented ogr2ogr? The difference is that with
fio-cat and fio-load there's space in between for programs that process
features.  The programs could be written in any language. They might use
Shapely, they might use Turf. The only requirement is that they read and write
sequences of GeoJSON features using stdin and stdout. A nice property of
programs like these is that you can sometimes parallelize them cheaply using
`GNU parallel
<http://www.gnu.org/software/parallel/parallel_tutorial.html#pipe>`__. 

The fio-buffer program (`unreleased
<https://github.com/Toblerity/Fiona/blob/sequence-processing/fiona/fio/ops.py>`__)
in the example below uses Shapely to calculate a 100 km buffer around features
(in Web Mercator, I know!). Parallel doesn't help in this example because the
sequence of features from fio-cat is fairly small, but I want to show you how
to tell parallel to watch for RS as a record separator.

.. code-block:: console

    $ fio cat docs/data/test_uk.shp --dst_crs EPSG:3857 \
    > | parallel --pipe --recstart '\x1E' fio buffer 1E+5 \
    > | fio collect --src_crs EPSG:3857 \
    > | geojsonio

Here's the result: http://bl.ocks.org/d/dcfe6ce4e7fa7b2e00e9. Unix pipelines,
still awesome at the age of `41
<http://en.wikipedia.org/wiki/Pipeline_(Unix)#History>`__!

The other point of this post is that, with the `JSON Text Sequence
<https://datatracker.ietf.org/doc/draft-ietf-json-text-sequence/>`__ draft
apparently going to publication, sequences of GeoJSON features not collected
into a GeoJSON feature collection are very close to being a real thing that
developers should be supporting.

.. author:: default
.. categories:: Programming
.. tags:: fiona, etl, unix, geojson, json, parallel, pipes, features
.. comments::
