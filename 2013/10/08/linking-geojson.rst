Linking GeoJSON
===============

I've blogged a few times (
`here <http://sgillies.net/blog/888/openlayers-constrained-by-hypertext/>`__ 
and `here <http://sgillies.net/blog/958/geojson-data-uris/>`__) about a pattern
I've developed for mapping feature data associated with a web page.
Lyzi Diamond wrote a great post about this pattern a few weeks ago:
http://lyzidiamond.com/posts/osgeo-august-meeting/. I'm thrilled that it's
catching on a bit. The pattern is super simple, only three short paragraphs are
needed to describe it.

Let's say I have a collection of web pages. Each is about a particular batch of
features. http://sgillies.github.io/syriaca/, for example, is a demo of a page
about Syriac places in antiquity. It bears a map in which the places are
rendered.

The features to be rendered in the map are obtained by making an HTTP request
for a GeoJSON resource. In my case
http://sgillies.github.io/syriaca/syriaca.json is fetched using
jQuery.getJSON(). 

.. sourcecode:: javascript

  var map = L.map('map');
  
  $.getJSON(geojson_uri, function (data) {
    var geojson = L.geoJson(data);
  });
  
  map.addLayer(geojson);

The feature data is not written into a script in the page as
they are in http://leafletjs.com/examples/quick-start-example.html. Yes,
this means an extra HTTP request, but one that can be ansynchronous and
cacheable.

Here's the crux of the pattern: the URI of that GeoJSON resource bound to the
geojson_uri variable is specified not in the map script, but in a link in the
head of the page.

.. sourcecode:: html

  <link rel="location" type="application/json" href="syriaca.json"/>

That URI is found by its "location" relation type.

.. sourcecode:: javascript

  var geojson_uri = $('link[rel="location"]').attr("href");

What's gained by having my Javascript follow a link to get GeoJSON features?
Why not just write the features into a script?  I've developed this pattern
because it decouples the map from the data and lets me generalize the mapping
script so I can reuse it across many pages, and also because I'm not thinking
just about the slippy maps in the page. I'm keeping the bigger web in mind.

If the Javascript in my page was my only consideration I'd just embed the
features in a script and maybe not use GeoJSON at all. But a resource like
http://sgillies.github.io/syriaca/ isn't just a page with a slippy map, it's
a starting point for other web map making software. The links are also for web
clients written by people other than me.

Given the page's URL, a Python web client can get the same GeoJSON in the
same way.

.. sourcecode:: python

  from bs4 import BeautifulSoup
  import requests
  
  r = requests.get('http://sgillies.github.io/syriaca/')
  soup = BeautifulSoup(r.content)
  points = [t for t in soup.find_all('link') if 'location' in t['rel']]
  print points
  
  # Output: 
  # [<link href="http://sgillies.github.io/syriaca/syriaca.json" rel="location"/>]
  
  for p in points:
      s = requests.get(r.url + p['href'])
      json = s.json()
      print json['type'], len(json['features'])
  
  # Output: 
  # FeatureCollection 955

The GeoJSON URI is much easier to find in a link by its relation type than
by scraping it from a script (were I to use a literal in my first code
example above instead of a geojson_uri variable). There's a IETF draft
written by James Snell proposing standardization of the "location" relation: 
http://tools.ietf.org/html/draft-snell-more-link-relations-01. My pattern
becomes much more useful with such a standard link relation.

Does this pattern avoid the browser's same-origin policy as Lyzi suggests in
http://lyzidiamond.com/posts/external-geojson-and-leaflet-the-other-way/? As
far as I know, links with rel="stylesheet" are the only ones that a browser
fetches unprompted. The GeoJSON fetched by XHR is still subject to same-origin
policy.

.. author:: default
.. categories:: Programming
.. tags:: python, javascript, geojson, link, leaflet, map, web, http, html, uri
.. comments::


