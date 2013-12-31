Shapely 1.2.19 and 1.3.0
========================

`Shapely 1.2.19 <https://pypi.python.org/pypi/Shapely/1.2.19>`__ and `Shapely
1.3.0 <https://pypi.python.org/pypi/Shapely>`__ have been tagged and uploaded
to PyPI.

1.2.19 adds buffer styling, work (see `buffer.hires.png <http://toblerity.org/shapely/code/buffer.hires.png>`__) that was done by `Johannes Schönberger
<https://github.com/ahojnnes>`__. 1.3.0 has no new features other than
support for Python 3.x, for which we traded in support for Python 2.5 and
lower. The force behind the Python 3 work is `Mike Toews
<https://github.com/mwtoews>`__. Johannes and Mike aren't the only
contributors to these releases. We're up to `19 contributors
<https://github.com/Toblerity/Shapely/graphs/contributors>`__ since moving to
GitHub, not counting another 6-7 contributors from the olden days.

If you're still on Python 2.5 (or 2.4), the 1.3.0 release is inconvenient: pip
and easy_install are going to grab it by default and it won't work for you.
I'm sorry, but you'll have to specify Shapely 1.2.x releases explicitly like
``pip install Shapely==1.2.19``. If you're using a more modern Python, ``pip
install Shapely`` will fetch 1.3.x from now on.

The Shapely development plan for 2014: bug fixes and new GEOS version support
in 1.3.x (and 1.2.x when possible) and architectural changes for more speed and
other features for future Shapely versions in the master branch.

.. author:: default
.. categories:: Programming
.. tags:: shapely, python, geometry
.. comments::
