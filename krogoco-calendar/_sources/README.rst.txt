krogoco-calendar
================

|PyPI version| |PyPI pyversions| |PyPI license|

A Python library that scrapes `Krog&Co <https://krogoco.se/>`__ calendar
pages and converts them to iCalendar (ICS) files.

Full documentation: https://jonnybergdahl.github.io/krogoco-calendar/

Installation
------------

.. code:: bash

   pip install krogoco-calendar

Python 3.10 or newer is required.

For development, clone the repository and install locally:

.. code:: bash

   git clone https://github.com/jonnybergdahl/Python-krogoco-calendar.git
   cd Python-krogoco-calendar
   pip install -e ".[dev]"

Usage
-----

.. code:: python

   from krogoco_calendar import KrogocoIcs

   scraper = KrogocoIcs(
       url="https://krogoco.se/jonkoping/kalender/",
       months=2,
   )

   # Get ICS as a string
   ics_data = scraper.get_ics()

   # Or write directly to a file
   scraper.write_ics("krogoco.ics")

Blacklist
~~~~~~~~~

You can filter out events by providing a blacklist of keywords. Events
whose title contains any of the keywords (case-insensitive) will be
excluded:

.. code:: python

   scraper = KrogocoIcs(
       url="https://krogoco.se/jonkoping/kalender/",
       months=2,
       blacklist=["HV71", "SHL", "match"],
   )

Supported locations
~~~~~~~~~~~~~~~~~~~

The library works with any Krog & Co location that uses the same
calendar page structure:

- ``https://krogoco.se/jonkoping/kalender/``
- ``https://krogoco.se/halmstad/kalender/``

Time format handling
--------------------

Event times are extracted automatically from Swedish title formats:

+-----------------------+----------------------------+-----------------------+
| Format                | Example                    | Parsed as             |
+=======================+============================+=======================+
| Full range            | ``12.00-16.00``            | 12:00 - 16:00         |
+-----------------------+----------------------------+-----------------------+
| Short range           | ``Kl.12-17``               | 12:00 - 17:00         |
+-----------------------+----------------------------+-----------------------+
| Bare hour range       | ``Sliver nattklubb 22-03`` | 22:00 - 03:00 (next   |
|                       |                            | day)                  |
+-----------------------+----------------------------+-----------------------+
| Single time           | ``Kl.17.00`` or            | 17:00 / 21:00         |
|                       | ``Från 21:00``             |                       |
+-----------------------+----------------------------+-----------------------+
| Time with hint        | ``Kl.22.00, 23+``          | 22:00 - 23:00         |
+-----------------------+----------------------------+-----------------------+
| No time               | ``Studentfest``            | All-day event         |
+-----------------------+----------------------------+-----------------------+

Running tests
-------------

.. code:: bash

   pytest

Publishing
----------

``build.sh`` runs the tests, builds the docs and pushes them to
`jonnybergdahl.github.io <https://jonnybergdahl.github.io/krogoco-calendar/>`__,
then builds the package and uploads it to PyPI. Bump ``__version__`` in
``src/krogoco_calendar/__init__.py`` first.

License
-------

MIT

.. |PyPI version| image:: https://badge.fury.io/py/krogoco-calendar.svg
   :target: https://badge.fury.io/py/krogoco-calendar
.. |PyPI pyversions| image:: https://img.shields.io/pypi/pyversions/krogoco-calendar.svg
   :target: https://pypi.python.org/pypi/krogoco-calendar/
.. |PyPI license| image:: https://img.shields.io/pypi/l/krogoco-calendar.svg
   :target: https://pypi.python.org/pypi/krogoco-calendar/
