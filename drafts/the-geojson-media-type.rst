The GeoJSON media type
======================

It's official: `application/vnd.geo+json
<http://www.iana.org/assignments/media-types/application/vnd.geo+json>`__ is
the media type for GeoJSON registered with the IANA. I had been holding out for
a `profile <https://www.mnot.net/blog/2012/04/17/profiles>`__ of
application/json, but it seems that the internet has resigned itself to
a Cambrian explosion of JSON media types.

Start serving your GeoJSON up with the new content type

::

    HTTP/1.1 200 OK
    Content-Type: application/vnd.geo+json

and start asking for it by name.

::

    Accept: application/vnd.geo+json; q=1.0, application/json; q=0.5

.. author:: default
.. categories:: Data
.. tags:: geojson, iana, web, http, media types
.. comments::
