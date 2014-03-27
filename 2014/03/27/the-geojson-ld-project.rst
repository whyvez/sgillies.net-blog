The GeoJSON-LD project
======================

JSON-LD is JSON for linking data and it's a web standard: http://json-ld.org/.
It offers a smooth, even zero-edit in some cases, path for upgrading existing
GeoJSON data and makes web-friendly extension of GeoJSON possible.

Previously, if you added a ``"timestamp": "2014-03-27"`` to a GeoJSON feature
object's "properties", a user has to guess what you mean. Playing guessing
games with my kids is fun. Playing pointless guessing games with data sucks.
One of the most important things JSON-LD offers is a framework in which you can
unambiguously identify "timestamp" as "http://purl.org/dc/terms/created". Now
users of your data have a solid start toward understanding what you mean by
"timestamp".

There's more to JSON-LD than defining your feature properties. You can also
bring in terms from other vocabularies to express that your features are, say,
schema.org `AdministrativeAreas <http://schema.org/AdministrativeArea>`__ or
`Landforms <http://schema.org/Landforms>`__.

If you'd like to help shape the way we use GeoJSON in the future, please follow
https://github.com/geojson/geojson-ld and jump into discussion on the open 
vocabulary and context issues.

.. author:: default
.. categories:: Data
.. tags:: geojson, json-ld, web
.. comments::
