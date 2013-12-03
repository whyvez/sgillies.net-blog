Atom extension for Tinkerer
===========================

`Tinkerer <http://tinkerer.me/>`__, the software I'm using for my blog, lacks
a good Atom feed, so last night I wrote my first Sphinx extension to provide
one. `Rutherford <https://github.com/sgillies/rutherford>`__ adds a couple new
config items and a full content Atom feed. It exposes the Tinker categories and
tags and tracks the modification times of posted .rst files so that feeds and
their entries get updated accordingly. My own feed is
http://sgillies.net/blog/feed.atom.

I'm becoming a fan of the Sphinx `extension story
<http://sphinx-doc.org/extensions.html>`__, at least for simple cases like
mine. Python's packaging situation continues to `get better
<https://python-packaging-user-guide.readthedocs.org/en/latest/>`__ and there
are all kinds of wins for insisting that extensions (think `QGIS plugins
<http://www.qgis.org/en/docs/pyqgis_developer_cookbook/plugins.html>`__) be Python
packages: support from pip and setuptools, version pinning, package dependency
resolution, etc.

.. author:: default
.. categories:: Programming
.. tags:: atom, rss, tinkerer, sphinx, extension, plugin, packaging, python
.. comments::
.. summary:: I'm becoming a fan of the Sphinx extension story
