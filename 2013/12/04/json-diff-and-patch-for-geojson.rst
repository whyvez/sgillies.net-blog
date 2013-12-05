JSON diff and patch for GeoJSON
===============================

We diff and patch source code all the time, but doing the same thing for JSON
data isn't as straightforward. More often than seeing the differences between JSON texts, we're interested in seeing (and sharing
and reapplying) the differences between the objects encoded in the text. We
want to gloss over differences in whitespace and order of object members and
see the real difference in the data. Unix patch and diff operate only on text,
and best on text of consistent and short line length, and aren't very useful
for parsing out differences between objects encoded in JSON. If diff and patch
aren't going to work, what will?

RFC 6902, `JavaScript Object Notation (JSON) Patch
<http://tools.ietf.org/html/rfc6902>`__ is one attempt at standardizing JSON
patches. From the introduction:

   JavaScript Object Notation (JSON) [`RFC4627 <http://tools.ietf.org/html/rfc4627>`__] is a common format for
   the exchange and storage of structured data.  HTTP PATCH [`RFC5789 <http://tools.ietf.org/html/rfc5789>`__]
   extends the Hypertext Transfer Protocol (HTTP) [`RFC2616 <http://tools.ietf.org/html/rfc2616>`__] with a
   method to perform partial modifications to resources.

   JSON Patch is a format (identified by the media type "application/
   json-patch+json") for expressing a sequence of operations to apply to
   a target JSON document; it is suitable for use with the HTTP PATCH
   method.

   This format is also potentially useful in other cases in which it is
   necessary to make partial updates to a JSON document or to a data
   structure that has similar constraints (i.e., they can be serialized
   as an object or an array using the JSON grammar).

RFC 6902 comes from developers interested in REST-style interaction with 
JSON representations of resources but might be more generally useful. How does
it work? If you're familiar with XPath and XPointer, you might see some
similarities. A JSON patch doc contains an array of path, value, and operation
tuples. The path tells you how to traverse into the JSON document and the
operations specify what is done to objects using the values.

For an example, I'm going to take GeoJSON from Lyzi Diamond's `Learn GeoJSON
<https://github.com/lyzidiamond/learn-geojson>`__ project and make diffs using
Stefan Kögl's `python-json-patch
<https://github.com/stefankoegl/python-json-patch>`__ package. RFC 6902 has
implementations in other languages, too, such as `jsonpatch-js
<https://github.com/bruth/jsonpatch-js>`__ (npm).

Here's a `script <https://gist.github.com/sgillies/7797688#file-json-diff-py>`__ that prints out the RFC 6902 diff between commits `44de76ef53
<https://github.com/lyzidiamond/learn-geojson/blob/44de76ef53b20bdaf51e0cde4aa634df210cd9d4/geojson/hackspots.geojson>`__
and `e9514f5c31
<https://github.com/lyzidiamond/learn-geojson/blob/e9514f5c317ee980b94ed6580950cfd9fbde53db/geojson/hackspots.geojson>`__
of `hackspots.geojson
<https://github.com/lyzidiamond/learn-geojson/blob/master/geojson/hackspots.geojson>`__:

.. code-block:: python

    import json
    import jsonpatch
    import os.path
    import requests
    import subprocess
     
    # original
    if not os.path.exists('hackspots-44de76ef53.json'):
        r = requests.get('https://github.com/lyzidiamond/learn-geojson/raw/44de76ef53b20bdaf51e0cde4aa634df210cd9d4/geojson/hackspots.geojson')
        data = r.content
        with open('hackspots-44de76ef53.json', 'wb') as f:
            f.write(data)
     
    # next commit
    if not os.path.exists('hackspots-e9514f5c31.json'):
        r = requests.get('https://github.com/lyzidiamond/learn-geojson/raw/e9514f5c317ee980b94ed6580950cfd9fbde53db/geojson/hackspots.geojson')
        data = r.content
        with open('hackspots-e9514f5c31.json', 'wb') as f:
            f.write(data)
     
    diff_text = subprocess.check_output([
                    'jsondiff',
                    'hackspots-44de76ef53.json',
                    'hackspots-e9514f5c31.json'])
    diff = json.loads(diff_text)
    print(json.dumps(diff, indent=2))

The result looks, to me, like a very reasonable representation of an operation
that adds one feature to a GeoJSON feature collection.

.. code-block:: javascript

    [
      {
        "path": "/features/2",
        "value": {
          "geometry": {
            "type": "Point",
            "coordinates": [
              -122.65496134757996,
              45.5212590460849
            ]
          },
          "type": "Feature",
          "properties": {
            "Name": "Three Friends Coffeehouse",
            "Address": "201 SE 12th Ave, Portland, OR 97214"
          }
        },
        "op": "add"
      }
    ]

A hypothetical change of this same feature's coordinates from `e9514f5c31
<https://github.com/lyzidiamond/learn-geojson/blob/e9514f5c317ee980b94ed6580950cfd9fbde53db/geojson/hackspots.geojson>`__
to https://gist.github.com/sgillies/7797688#file-foo-geojson looks like this:

.. code-block:: javascript

    [
      {
        "path": "/features/2/geometry/coordinates/0",
        "value": -122.655,
        "op": "replace"
      },
      {
        "path": "/features/2/geometry/coordinates/1",
        "value": 45.522,
        "op": "replace"
      }
    ]

There's a potential for RFC 6902 style GeoJSON diffs to become very large if
highly detailed lines and polygons are being modified (via simplication or
reprojection). But GeoJSON itself is quite verbose already and a verbose patch
representation feels not surprising. If you already think GeoJSON sucks, JSON
patch for GeoJSON is going to look exta sucky. I don't think there's any way
around that. Just having started to look into this, I'm not sure how it will
play out at scale, but I think it's worth considering.

.. author:: default
.. categories:: Programming
.. tags:: json, patch, diff, github, http, geojson, python, 
.. comments::
