JSON-LD and GeoJSON
===================

JSON-LD, a a JSON-based serialization for Linked Data, is finally a
`W3C Recommendation <http://www.w3.org/TR/2014/REC-json-ld-20140116/>`__. I'd like
to remind readers that dumpgj, the any-vectors-to-GeoJSON program that is distributed
with `Fiona <https://pypi.python.org/pypi/Fiona>`__, will 
`optionally add JSON-LD contexts to GeoJSON files
<http://sgillies.net/blog/1179/dumpgj-json-ld-and-crs/>`__.

.. code-block:: console

    $ dumpgj --help
    usage: dumpgj [-h] [-d] [-n N] [--compact] [--encoding ENC]
                  [--record-buffered] [--ignore-errors] [--use-ld-context]
                  [--add-ld-context-item TERM=URI]
                  infile [outfile]

    Serialize a file's records or description to GeoJSON

    positional arguments:
      infile                input file name
      outfile               output file name, defaults to stdout if omitted

    optional arguments:
      -h, --help            show this help message and exit
      -d, --description     serialize file's data description (schema) only
      -n N, --indent N      indentation level in N number of chars
      --compact             use compact separators (',', ':')
      --encoding ENC        Specify encoding of the input file
      --record-buffered     Economical buffering of writes at record, not
                            collection (default), level
      --ignore-errors       log errors but do not stop serialization
      --use-ld-context      add a JSON-LD context to JSON output
      --add-ld-context-item TERM=URI
                            map a term to a URI and add it to the output's JSON LD
                            context

.. author:: default
.. categories:: Programming
.. tags:: python, fiona, geojson, json-ld, dumpgj, w3c, linked data
.. comments::
