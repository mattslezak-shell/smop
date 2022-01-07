``SMOP`` is Small Matlab and Octave to Python compiler.
 ``SMOP`` translates matlab to python. Despite obvious similarities
 between matlab and numeric python, there are enough differences to
 make manual translation infeasible in real life.  ``SMOP`` generates
 human-readable python.

 Smop3 is the community maintained version of https://github.com/victorlei/smop/
 abandoned somewhere around 2018. SMOP is looking for maintainers and people
 with knowledge on lexers and parsers to help clean up the project!

Installation
============

It's best to use THIS forked version of the software if you're converting option code over, as that's how it's been modified.

In this case, you want to copy the zip file from the GitHub site to your local PC, and then unzip it.  From the unzipped directory, type:

python setup.py install

This will "almost" be complete.  Next you need to go to wherever the installation occurs.  In Anaconda3, it should be at C:/Users/FirstName.LastName/Anaconda3/Lib/site-packages/smop3-0.41b0-py3.x.egg, note that the py3.x will be whatever python version you actually have installed (3.5, 3.7, 3.9, whatever).

Now you need to unzip that smop3-0.41b0-py3.x.egg file in the same directory, which will create a /smop/ directory so that things will actually work.  Meaning, when you type: smop "filename.m", it can find everything it needs to complete the .py conversion, and the generated .py file can find what it needs to work.  Now, it's not perfect - some files will be created that have incorrect indentation, or other various issues.  Some math functions won't be ported over, so you'll need to manually edit the files.  The converter is kind of a "MATLAB-clone" representation in Python.  So it is not perfect, but generally works.  Good for hacking over MATLAB code that you aren't sure you want to port over to Python yet, so you can just do some minimal work to the auto-converted files and see if it does what you need or not.  If it is what you're wanting, then you should probably manually convert it over to pure Python... which will require some knowledge of both MATLAB and Python data structures.  You don't have to, but it's cleaner, and easier to distribute (this SMOP project is basically dead, so don't expect end users to get it working).  Just my opinion...

``smop3`` is available from PyPI, although I'd suggest using this version as it's actually been tested.

.. code-block:: bash

  pip install smop3

Developers can clone the GitHub repository and use editable installs:

.. code-block:: bash

  git clone git@github.com:smop3/smop
  cd smop3
  pip install -e .

Random remarks
==============

With less than five thousands lines of python code
``SMOP`` does not pretend to compete with such polished
products as matlab or octave.  Yet, it is not a toy.
There is an attempt to follow the original matlab
semantics as close as possible.  Matlab language
definition (never published afaik) is full of dark
corners, and ``SMOP`` tries to follow matlab as
precisely as possible.

There is a price, too.
The generated sources are
`matlabic`, rather than `pythonic`, which means that
library maintainers must be fluent in both languages,
and the old development environment must be kept around.

Should the generated program be `pythonic` or `matlabic`?
For example should array indexing start with zero
(`pythonic`) or with one (`matlabic`)?

I beleive now that some matlabic accent is unavoidable
in the generated python sources.  Imagine matlab program
is using regular expressions, matlab style.  We are not
going to translate them to python style, and that code
will remain forever as a reminder of the program's
matlab origin.

Another example.  Matlab code opens a file; fopen
returns -1 on error.  Pythonic code would raise
exception, but we are not going to do `that`.   Instead,
we will live with the accent, and smop takes this to the
extreme --- the matlab program remains mostly unchanged.

It turns out that generating `matlabic`` allows for
moving much of the project complexity out of the
compiler (which is already complicated enough) and into
the runtime library, where there is almost no
interaction between the library parts.

Which one is faster --- python or octave?  I don't know.
Doing reliable performance measurements is notoriously
hard, and is of low priority for me now.  Instead, I wrote
a simple driver ``go.m`` and ``go.py`` and rewrote `rand`
so that python and octave versions run the same code.
Then I ran the above example on my laptop.  The results
are twice as fast for the python version.   What does it
mean?  Probably nothing. YMMV.

Running the test suite
======================

The test suite uses Python's ``unittest`` module and can be executed with:

.. code-block:: bash

  python -m unittest

Command-line options
====================

The command line interface can be accessed using either the ``smop`` command,
or if wrappers are unavailable on your system ``python -m smop`` can be used:

..code-block:: bash

  python -m smop

.. code-block:: bash

  SMOP compiler version 0.25.1
  Usage: smop [options] file-list
      Options:
      -V --version
      -X --exclude=FILES      Ignore files listed in comma-separated list FILES
      -d --dot=REGEX          For functions whose names match REGEX, save debugging
                              information in "dot" format (see www.graphviz.org).
                              You need an installation of graphviz to use --dot
                              option.  Use "dot" utility to create a pdf file.
                              For example:
                                  $ python main.py fastsolver.m -d "solver|cbest"
                                  $ dot -Tpdf -o resolve_solver.pdf resolve_solver.dot
      -h --help
      -o --output=FILENAME    By default create file named a.py
      -o- --output=-          Use standard output
      -s --strict             Stop on the first error
      -v --verbose
