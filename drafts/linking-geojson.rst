Linking GeoJSON
===============

There are different ways to hook javascript web maps (e.g. Leaflet or
OpenLayers) up to feature data. Categorizing them properly isn't what I'm
trying to do in this post. I am going to examine one of them more closely, in
light of some recent discussion in other blogs and Twitter, and explain my
motivation for using it. With examples.

Write your feature data into script elements in the web page.
http://leafletjs.com/examples/quick-start-example.html

.. sourcecode:: javascript

   L.polygon([
        [51.509, -0.08],
        [51.503, -0.06],
        [51.51, -0.047]
    ]).addTo(map).bindPopup("I am a polygon.");

This might be dynamically written JS or a script that loads dynamic JS.

Pass features in the page URL via query string or window.location.hash (like
geojson.io).

.. sourcecode:: console

  $ geojsonio map.geojson --print
  http://geojson.io/#data=data:application/json,%7B%22type%22%3A%22FeatureCollecti
  on%22%2C%22features%22%3A%5B%7B%22type%22%3A%22Feature%22%2C%22properties%22%3A%
  7B%22title%22%3A%22Null%20Island%22%7D%2C%22geometry%22%3A%7B%22type%22%3A%22Poi
  nt%22%2C%22coordinates%22%3A%5B0%2C0%5D%7D%7D%5D%7D


Use external JSON resource, write the service or socket connection parameters
or HTTP URI into script elements in the web page.

Use external JSON resource, but write the service endpoint or resource URI into a non-script HTML element. This is one I like and have written about a few times 
http://sgillies.net/blog/888/openlayers-constrained-by-hypertext
http://sgillies.net/blog/958/geojson-data-uris/

I'm thrilled that it's catching on a bit. 




Links are not really for my javascript (I'd embed the GeoJSON in my javascript
if that was the only consideration). They are for others on the web. Old
fashioned HTTP clients that understand HTML.

  >>> from bs4 import BeautifulSoup
  >>> import requests

  >>> r = requests.get('http://sgillies.github.io/syriaca/')
  >>> soup = BeautifulSoup(r.content)
  >>> points = [t for t in soup.find_all('link') if 'location' in t['rel']]
  >>> print points
  [<link href="http://sgillies.net/syriaca.json" rel="location"/>]
  >>> for p in points:
  ...     s = requests.get(r.url + p['href'])
  ...     json = s.json()
  ...     print json['type'], len(json['features'])
  ...
  FeatureCollection 955

I might be able to use a regex to parse "syriaca.json" from a <script> tag,
but the solution would be more brittle.

.. author:: default
.. categories:: Programming
.. tags:: python, javascript, geojson, linking, maps, web, http, html
.. comments::


