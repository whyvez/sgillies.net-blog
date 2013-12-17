Teaching Python GIS users to be more rational
=============================================

We, developers and documenters of GIS software for Python, have been teaching
users to code irrationally. I'm certain that there's a cost to this and that we
need to try to undo the damage as much as we can. Irrational coding happens in
the real world. I'm largely self-taught as a programmer and have a long history
of using and misusing software that I don't fully understand, cargo-culting
code, etc. I've been there. This is part of the reason we write software, to
put power in the hands of less expert computer users, and it seems to
necessarily invite the problem of irrational programming. When software is
baffling rather than enlightening, it can make the problem worse. Software
ought to instead teach its users to think more rationally and reward them for
doing so. In theory, there ought to be a virtuous circle here for open source
software: rational users providing better bug reports and more effective
community support, and becoming code contributors at a higher rate. Teaching
users to be irrational, on the other hand, guarantees a steady and soul-sucking
stream of frequently asked low grade support questions.

I'm thinking about two things going on in Python GIS software in particular.
Exhibit A is the practice of teaching users to use ``del`` to close or finalize
externalize resources like database connections and cursors. See
http://gis.stackexchange.com/search?q=%5Bpython%5D+%22+del+%22. Think hard
about this when you're developing, documenting, and supporting Python APIs. Do
you really want your users to have to know so much about the details of ``del``
or what happens to Python objects after they employ ``del``? And whether it's
dependent on their interpreter's garbage collection? Do you really want to
teach them to rely on side-effects of ``del``?

Python's ``del`` statement is all about `namespace and container management
<http://docs.python.org/2/reference/simple_stmts.html#the-del-statement>`__ and
has nothing directly to do with finalizing external resources:

    Deletion of a name removes the binding of that name from the local or
    global namespace, depending on whether the name occurs in a global
    statement in the same code block. If the name is unbound, a NameError
    exception will be raised.

    It is illegal to delete a name from the local namespace if it occurs as
    a free variable in a nested block.

    Deletion of attribute references, subscriptions and slicings is passed to
    the primary object involved; deletion of a slicing is in general equivalent
    to assignment of an empty slice of the right type (but even this is
    determined by the sliced object).

Think about this too: will ``del`` work to close and finalize external stuff if
the name you're deleting is not in a local or global namespace, but is a list
or dict item? Or if you've bound two names to the same object do you have to
``del`` both names or just one?

Any closing of external resources is a side effect, and side effects in
software are almost always less than deterministic in nature. Teaching users to
rely on side effects is, in effect, teaching them to program irrationally.
Here's a user (not a GIS user, either) who has been particularly harmed, IMO:
http://stackoverflow.com/a/12626643/159235.

The reason that ``del`` works at all when it does is that the object to which
the name was bound has a __del__() method that calls another method that
finalizes.  Just make that other method part of your API, document it well, and
you have users programming rationally and getting rational results like
external files and resources closed and freed precisely when they should be.

To their credit, the ArcPy developers have corrected this in recent versions,
but any visit to GIS StackExchange shows that the damage caused to programmers
continues.

Exhibit B is the practice of teaching users to rebind names to ``None`` to
finalize, as in `GDAL/OGR Python bindings
<http://trac.osgeo.org/gdal/wiki/PythonGotchas#CertainobjectscontainaDestroymethodbutyoushouldneveruseit>`__.
Before you write an API like this or write a tutorial or book chapter that
advocates this, ask yourself what is so special about ``None`` in this case?
Will rebinding that name to ``0``, or ``True``, or ``"abracadabra!"`` work just
as well? Why or why not? A library developer or teacher or writer should know
the answer to this and be able to explain it.

``None`` truly `is special
<http://python-history.blogspot.com/2013/11/story-of-none-true-false.html>`__
in Python (just compare the results of ``None = 1`` and ``False = 1`` at your
interpreter prompt to see), but is its special nature the thing that causes
finalization, or is it the binding, or what?  What is actually going on in the
software and how do we teach users to use it rationally?

In fact, the reason rebinding to ``None`` works when it does is the same as
above: some finalizing method is called by the interpreter when the object is
no longer referenced by anything else. Just expose that method to programmers
and you have a less baffling API.

In other words, `explicit is better than implicit
<http://www.python.org/dev/peps/pep-0020>`__. Teach users to close and finalize
external resources explicitly instead of teaching them to do something else
that has finalization as a side effect, and let's see if we all profit from more
rational Python GIS programmers.


.. author:: default
.. categories:: Programming
.. tags:: python, zen, pedagogy, design, arcpy, gdal, ogr, stack exchange
.. comments::
