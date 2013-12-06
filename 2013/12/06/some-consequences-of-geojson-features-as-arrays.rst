Some consequences of GeoJSON features as arrays
===============================================

Don't mind me, I'm just thinking out loud about the GeoJSON format.

A GeoJSON Feature Collection is an object with a "features" key and the value
for the key is specified to be an array of Feature objects. Is the order of
Features significant? In the context of my `previous post
<http://sgillies.net/blog/2013/12/04/json-diff-and-patch-for-geojson.html>`__,
should a zero or empty RFC 6902 diff between this

.. code-block:: javascript

    { "type": "FeatureCollection",
      "features": [
        { "type": "Feature",
          "id": "foo",
          ... },
        { "type": "Feature",
          "id": "bar",
          ... } ] }

and this, where the feature array has been reversed,

.. code-block:: javascript

    { "type": "FeatureCollection",
      "features": [
        { "type": "Feature",
          "id": "bar",
          ... },
        { "type": "Feature",
          "id": "foo",
          ... } ] }

be expected? Strictly speaking, no, because in JSON (see `json.org
<http://www.json.org>`__ or `ECMA 404
<http://www.ecma-international.org/publications/standards/Ecma-404.htm>`__)

  An array is an ordered collection of values.

whereas

  An object is an unordered set of name/value pairs.

My question is related to `one asked on the IETF's apps-discuss
list
<http://www.ietf.org/mail-archive/web/apps-discuss/current/msg08749.html>`__ in
January. Should we expect to be able to patch GeoJSON documents using the ids
of individual features? Being able to do so is more or less the reason why
GeoJSON Features have unique ids, but it's clear to me that a generic RFC 6902
won't do. As James Snell points out in that thread, more expressive paths will
be required.

In my previous post on this topic (see link at the top of this one) I looked at
the addition of a new feature to the tail of the features array. The JSON patch
in this case was very simple. But what if that feature had been inserted at the
head of the features array? Is the resulting patch going to be something that
reads like "push back existing items and add new item to the head?" The result
from python-json-patch isn't pretty.

.. code-block:: javascript

    [
      {
        "path": "/features/0/geometry/coordinates/0",
        "value": -122.65496134757996,
        "op": "replace"
      },
      {
        "path": "/features/0/geometry/coordinates/1",
        "value": 45.5212590460849,
        "op": "replace"
      },
      {
        "path": "/features/0/properties/Notes",
        "op": "remove"
      },
      {
        "path": "/features/0/properties/Name",
        "value": "Three Friends Coffeehouse",
        "op": "replace"
      },
      {
        "path": "/features/0/properties/Address",
        "value": "201 SE 12th Ave, Portland, OR 97214",
        "op": "replace"
      },
      {
        "path": "/features/1/geometry/coordinates/0",
        "value": -122.67516911029816,
        "op": "replace"
      },
      {
        "path": "/features/1/geometry/coordinates/1",
        "value": 45.55673233031101,
        "op": "replace"
      },
      {
        "path": "/features/1/properties/Notes",
        "value": "usually busy, outlets on side wall only",
        "op": "replace"
      },
      {
        "path": "/features/1/properties/Name",
        "value": "Albina Press",
        "op": "replace"
      },
      {
        "path": "/features/1/properties/Address",
        "value": "4637 N Albina Ave  Portland, OR 97217",
        "op": "replace"
      },
      {
        "path": "/features/2",
        "value": {
          "geometry": {
            "type": "Point",
            "coordinates": [
              -122.68242716789246,
              45.56997505986905
            ]
          },
          "type": "Feature",
          "properties": {
            "Notes": "",
            "Name": "Arbor Lodge",
            "Address": "1507 N Rosa Parks Way  Portland, OR 97217"
          }
        },
        "op": "add"
      }
    ]

Unless I misunderstand RFC 6901 and RFC 6902, the following ought to be
possible.

.. code-block:: javascript

   [
     { "op": "move", "from": "/foo/1", "path": "/foo/2" },
     { "op": "move", "from": "/foo/0", "path": "/foo/1" },
     { "path": "/features/0`",
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
       "op": "add" }
   ]

Making concise patches won't be trivial, that seems clear, and the ideal patch
style for GeoJSON could differ from the ideal style for other applications.
I'm certain that we'd need to get to patches like the latter to make GeoJSON
patching something that developers would consider adopting. JSON PATCH provides
a useful grammar but by itself isn't enough. There's more work to be done.

Did we ever consider making FeatureCollection's features key have an object
value? I'll look in the GeoJSON list archive when I have time.

.. author:: default
.. categories:: Programming
.. tags:: json, patch, diff, http, ietf, ecma, geojson, python
.. comments::

