docstrfmt: a formatter for Sphinx flavored reStructuredText
===========================================================

.. image:: https://img.shields.io/pypi/v/docstrfmt.svg
    :alt: Latest docstrfmt Version
    :target: https://pypi.python.org/pypi/docstrfmt

.. image:: https://img.shields.io/pypi/pyversions/docstrfmt
    :alt: Supported Python Versions
    :target: https://pypi.python.org/pypi/docstrfmt

.. image:: https://img.shields.io/pypi/dm/docstrfmt
    :alt: PyPI - Downloads - Monthly
    :target: https://pypi.python.org/pypi/docstrfmt

.. image:: https://coveralls.io/repos/github/LilSpazJoekp/docstrfmt/badge.svg?branch=master
    :alt: Coveralls Coverage
    :target: https://coveralls.io/github/LilSpazJoekp/docstrfmt?branch=master

.. image:: https://github.com/LilSpazJoekp/docstrfmt/workflows/CI/badge.svg
    :alt: Github Actions Coverage
    :target: https://github.com/LilSpazJoekp/docstrfmt/actions?query=branch%3Amaster

.. image:: https://img.shields.io/badge/Contributor%20Covenant-v2.0%20adopted-ff69b4.svg
    :alt: Contributor Covenant
    :target: https://github.com/LilSpazJoekp/docstrfmt/blob/master/CODE_OF_CONDUCT.md

*Strongly inspired by* rstfmt_ and rustfmt_.

*This is considered to be in beta however it is mostly stable.*

Description
-----------

docstrfmt is a tool for automatically formatting reStructuredText_ in files and Python
docstrings in a consistent way.

Like Black_ and rstfmt_, the motivation is to provide a format that is reasonable and
minimally configurable to prevent teams from wasting time on style discussions (or
individuals on manually doing formatting, for that matter).

Currently, docstrfmt is in an early stage of development. The most common reST
constructs are covered but there are some that are not. If there is a construct missing
and would like to add it, feel free to open a PR to add it or open an issue and I'll see
what I can do.

To get a feel for the output of docstrfmt, see `the sample file <sample.rst>`__.

Differences between docstrfmt and rstfmt_
-----------------------------------------

The main difference between rstfmt_ and docstrfmt is the ability to format Python
docstrings. I am open to merging this project with rstfmt_, however, there as several
differences in formatting conventions between the two (hence the separate fork).

Usage
-----

.. code-block:: sh

    # Install.
    pip install docstrfmt

    # Install the development version.
    pip install https://github.com/LilSpazJoekp/docstrfmt/archive/master.zip

    # Read a file from stdin and write the formatted version to stdout.
    docstrfmt

    # Format the given file(s) in place.
    docstrfmt <file>...

    # Format the given files, printing all output to stdout.
    docstrfmt -o <file>...

    # Wrap paragraphs to the given line length where possible (default to whatever is
    # set for black).

    docstrfmt -l <length>

.. note::

    Line length is resolved in the following order:

    1. The length specified with the ``-l`` flag.
    2. The ``line-length`` specified in the ``tool.docstrfmt`` section in the file
       specified with ``--pyproject-config``.
    3. The ``line-length`` specified in the ``tool.black`` section in the file specified
       with ``--pyproject-config``.
    4. The ``line-length`` specified in the ``tool.docstrfmt`` section in a
       ``pyproject.toml`` autodetected `like Black`_.
    5. The ``line-length`` specified in the ``tool.black`` section in a
       ``pyproject.toml`` autodetected `like Black`_.
    6. `Black's default line length`_ (88 at the time of this writing).

Like Black's blackd_, there is also a daemon that provides formatting via HTTP requests
to avoid the cost of starting and importing everything on every run.

.. code-block:: sh

    # Install.
    pip install "docstrfmt[d]"

    # Install the development version.
    pip install "https://github.com/LilSpazJoekp/docstrfmt/archive/master.zip#egg=docstrfmt[d]"

    # Start the daemon (binds to localhost:5219 by default).
    docstrfmtd --bind-host=<host> --bind-port=<port>

    # Print the formatted version of a file.
    curl http://locahost:5219 --data-binary @<file>

    # Specify the line length (default to whatever is set for black).
    curl -H 'X-Line-Length: 72' http://locahost:5219 --data-binary @<file>

    # Mimic the standalone tool: read from stdin, write to stdout, exit with
    # a nonzero status code if there are errors.
    curl -fsS http://locahost:5219 --data-binary @/dev/stdin

With Editors
~~~~~~~~~~~~

PyCharm
+++++++

Instructions derived from `black documentation
<https://black.readthedocs.io/en/stable/editor_integration.html#pycharm-intellij-idea>`_

1. Install.

   .. code-block:: sh

       pip install "docstrfmt[d]"

2. Locate where `docstrfmt` is installed.

   - On macOS / Linux / BSD:

     .. code-block:: sh

         which docstrfmt
         # /usr/local/bin/docstrfmt  # possible location

   - On Windows:

     .. code-block:: shell

         where docstrfmt
         # C:\Program Files\Python39\Scripts\docstrfmt.exe

.. note::

    Note that if you are using a virtual environment detected by PyCharm, this is an
    unneeded step. In this case the path to `docstrfmt` is
    ``$PyInterpreterDirectory$/docstrfmt``.

3. Open External tools in PyCharm.

   - On macOS:

     `PyCharm -> Preferences -> Tools -> External Tools`

   - On Windows / Linux / BSD:

     `File -> Settings -> Tools -> External Tools`

4. Click the + icon to add a new external tool with the following values:

   - Name: docstrfmt
   - Description:
   - Program: <install_location_from_step_2>
   - Arguments: ``"$FilePath$"``

5. Format the currently opened file by selecting `Tools -> External Tools -> docstrfmt`.

   - Alternatively, you can set a keyboard shortcut by navigating to `Preferences or
     Settings -> Keymap -> External Tools -> External Tools - docstrfmt`.

6. Optionally, run `docstrfmt` on every file save:

   1. Make sure you have the `File Watchers
      <https://plugins.jetbrains.com/plugin/7177-file-watchers>`_ plugin installed.
   2. Go to `Preferences or Settings -> Tools -> File Watchers` and click `+` to add a
      new watcher:

      - Name: docstrfmt
      - File type: Python
      - Scope: Project Files
      - Program: <install_location_from_step_2>
      - Arguments: ``$FilePath$``
      - Output paths to refresh: ``$FilePath$``
      - Working directory: ``$ProjectFileDir$``

   3. Uncheck "Auto-save edited files to trigger the watcher" in Advanced Options

With pre-commit
~~~~~~~~~~~~~~~

.. code-block:: yaml

    repos:
      - repo: https://github.com/LilSpazJoekp/docstrfmt
        rev: stable # Replace by any tag/version: https://github.com/LilSpazJoekp/docstrfmt/tags
        hooks:
          - id: docstrfmt
            language_version: python3
            types_or: [python, rst, txt] # only needed if you want to include txt files.

.. _black: https://github.com/psf/black

.. _black's default line length: https://black.readthedocs.io/en/stable/the_black_code_style/current_style.html#line-length

.. _blackd: https://github.com/psf/black#blackd

.. _docutils: https://docutils.sourceforge.io/

.. _like black: https://ichard26-testblackdocs.readthedocs.io/en/refactor_docs/pyproject_toml.html#where-black-looks-for-the-file

.. _pandoc: https://pandoc.org/

.. _reformatter.el: https://github.com/purcell/reformatter.el

.. _restructuredtext: https://docutils.sourceforge.io/docs/user/rst/quickstart.html

.. _rstfmt: https://github.com/dzhu/rstfmt

.. _rustfmt: https://github.com/rust-lang/rustfmt
