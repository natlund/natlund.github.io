.. title: Nikola usage
.. slug: nikola-usage
.. date: 2016-04-22 13:20:11 UTC+01:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

Nikola is a static site generator geared towards making blogs.  To make a standard website, a few tweaks are required.

First, note that the front page of a website has the name index.html
It seems that when you point a browser at a folder, if the folder contains a file called ``index.html``, the browser automatically loads ``index.html``.

When build is run, Nikola creates an ``index.html`` in the root of the output directory.  This ``index.html`` is designed to be a generic front page for a blog website.  So it shows the first 500 words from the newest 10 blog posts, or whatever, as well as all the navigation bars, etc.  Nikola actually puts ``index.html`` in the root of the output directory, because that is the default location.  We need to change this by uncommenting and altering the INDEX_PATH variable in ``conf.py``

Uncomment and change to::

    INDEX_PATH = "blog"

Now, the Nikola build command creates a blog sub-directory in output, creates the blog-centric ``index.html`` file, and dumps it in the ``output/blog`` directory.

But we still need an ``index.html`` in the root of output to be the front page of our website.  There are various ways to do this, but:

- I want the home page .rst file to be easy to find: ideally sitting by itself in a ``/stories/home`` directory.
- I want to keep the root of output clean.  I don't want *all* of my web pages there, only the ``index.html`` page.  All the rest should be tucked away in ``/output/stories``.

It seems that one way to do this is to add a tuple to the ``PAGES`` variable in ``conf.py``.  By default, ``PAGES`` looks like this::

    PAGES = (
        ("stories/*.rst", "stories", "story.tmpl"),
        ("stories/*.txt", "stories", "story.tmpl"),
        ("stories/*.html", "stories", "story.tmpl"),
    )

The **first** tuple in ``PAGES`` seems to have the following significance:

* The command ``nikola build`` parses the tuple ``("stories/*.rst", "stories", "story.tmpl")`` like so: 'Look for files matching ``"stories/*.rst"``, process them using the ``"story.tmpl"`` template, and dump the output in the ``"stories"`` subdirectory of ``output``.

* The command ``nikola new_page`` will create a new ``.rst`` file in the directory specified in the first tuple member: ``"stories/*.rst"``, that is, in ``/stories/``.

This gives us two options, each with drawbacks.

* Add ``("stories/home/*.rst", "", "story.tmpl")``, to the **end** of the list of other tuples.  This **should** tell Nikola to process files in ``stories/home/`` and put the result in the "" subdirectory of ``/output/``.  BUT, instead it puts it in the subdirectory ``/output/stories/home``.

* Add ``("stories/home/*.rst", "", "story.tmpl")``, to the **start** of the list of other tuples.  This works well.  The drawback is that the command ``nikola new_page`` puts new files in ``/stories/home`` instead of just ``/stories``.

The latter option is better, so we will go with that.  ``PAGES`` now looks like::

    PAGES = (
        ("stories/home/*.rst", "", "story.tmpl"),
        ("stories/*.rst", "stories", "story.tmpl"),
        ("stories/*.txt", "stories", "story.tmpl"),
        ("stories/*.html", "stories", "story.tmpl"),   
    )

