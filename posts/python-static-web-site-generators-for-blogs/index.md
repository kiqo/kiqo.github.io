<!--
.. title: Python static web site generators for blogs
.. slug: python-static-web-site-generators-for-blogs
.. date: 2024-10-13 12:52:28 UTC+02:00
.. tags: python web github
.. category: engineering
.. link: 
.. description: Learnings when setting up this Blog web site with Python 
.. type: text
-->

There is a really awesome comparison over **all** static web site generators [here](https://github.com/myles/awesome-static-generators).
Not only does it list what type of web site the library/tool is aiming for, but it also lists the supported languages.

## What am I looking for? 

* Simple to use tool, not too complex for somebody with little frontend experience.
* Nice design available, so that no CSS code needs to be touched
* Nice-to-have: Possibility to extend it easily (e.g. create custom theme)
* Nice-to-have: Switch to another tool

## Python Blog Generators

When looking at "Blog" section and the ones for Python, that still seem to be maintained (with >50 Github Stars and recent commits):   

* [Blag](https://github.com/venthur/blag) ([docs](https://blag.readthedocs.io/en/latest/))
    * Design: Nice look and simple
    * Featuers: Jinja2, live reload
* [Hyde](https://github.com/hyde/hyde) ([demos](https://github.com/hyde/hyde/wiki/Hyde-Powered))
    * Design: Very customisable, some very nice and some very ugly
* [Pelican](https://github.com/getpelican/pelican) ([docs](https://docs.getpelican.com/en/latest/quickstart.html))
    * Featuers: Jinja2, live reload, Code Syntax Highlighting, Linking to internal content, Importing Tool, Chronological content as well as static pages
* [Nikola](https://github.com/getnikola/nikola) ([docs](https://getnikola.com/handbook.html#why-static), [demos](https://themes.getnikola.com/))
    * Design: Ugly by default, several themes as plugins, with some of themes (e.g. [hack](https://themes.getnikola.com/v8/hack/demo/)) looking nice
    * Featuers: Mako or Jinja2, many input formats incl. rst, Markdown, IPython Notebooks and HTML, live reload, multilingual support, Deployment support


The following libraries are left out in this comparison: 

* [aurora](https://github.com/capjamesg/aurora) - not having a nice-looking demo
* [makesite](https://github.com/sunainapai/makesite) - not maintained anymore

## Decision 

In the end, the winner for me was Nikola due to its many out-of-the box features, including the IPython Support available by default.  