nose-cov
========

This plugin produces coverage reports.  It also supports coverage of subprocesses.

All features offered by the coverage package should be available, either through nose-cov or
through coverage's config file.


Installation
------------

Install with pip::

    pip install nose-cov

.. NOTE::

    Ensure you use pip instead of easy_install as the latter does not correctly install the
    init_cov_core.pth file needed for subprocess measurement.


Uninstallation
--------------

Uninstall with pip::

    pip uninstall nose-cov
    pip uninstall cov-core

.. NOTE::

    Ensure that you manually delete the init_cov_core.pth file in your site-packages directory.

    This file starts coverage collection of subprocesses if appropriate during site initialisation
    at python startup.


Usage
-----

The following will report on the combined coverage of the main process and all of it's subprocesses::

    nosetests --with-cov --cov myproj tests/

Shows a terminal report::

    ---------- coverage: platform linux2, python 2.6.5-final-0 -----------
    Name                 Stmts   Miss  Cover
    ----------------------------------------
    myproj/__init__          2      0   100%
    myproj/myproj          257     13    95%
    myproj/feature4286      94      9    90%
    ----------------------------------------
    TOTAL                  353     22    94%
    ----------------------------------------------------------------------


Reporting
---------

It is possible to generate any combination of the reports for a single test run.

The available reports are terminal (with or without missing line numbers shown), HTML, XML and
annotated source code.

The terminal report without line numbers (default)::

    nosetests --with-cov --cov-report term --cov myproj tests/

    ---------- coverage: platform linux2, python 2.6.5-final-0 -----------
    Name                 Stmts   Miss  Cover
    ----------------------------------------
    myproj/__init__          2      0   100%
    myproj/myproj          257     13    95%
    myproj/feature4286      94      9    90%
    ----------------------------------------
    TOTAL                  353     22    94%
    ----------------------------------------------------------------------


The terminal report with line numbers::

    nosetests --with-cov --cov-report term-missing --cov myproj tests/

    ---------- coverage: platform linux2, python 2.6.5-final-0 -----------
    Name                 Stmts   Miss  Cover   Missing
    --------------------------------------------------
    myproj/__init__          2      0   100%
    myproj/myproj          257     13    95%   24-26, 99, 149, 233-236, 297-298, 369-370
    myproj/feature4286      94      9    90%   37, 40, 183-188, 197
    --------------------------------------------------
    TOTAL                  353     22    94%
    ----------------------------------------------------------------------


The remaining three reports output to files without showing anything on the terminal (useful for
when the output is going to a continuous integration server)::

    nosetests --with-cov
              --cov-report html
              --cov-report xml
              --cov-report annotate
              --cov myproj tests/

Multiple reports can also be seperated by commas::
    nosetests --with-cov --cov-report html,xml --cov myproj tests/
    nosetests --with-cov --cov-report html,xml --cov myproj tests/

In the nose.cfg file, they can be seperated by whitespace or commas::
    [nosetests]
    with-cov=1
    cov-report=html,xml
    cov=myproj

    [nosetests]
    with-cov=1
    cov-report=html
        xml
        term-missing
    cov=myproj

Coverage Data File
------------------

The data file is erased at the beginning of testing to ensure clean data for each test run.

The data file is left at the end of testing so that it is possible to use normal coverage tools to
examine it.


Coverage Config File
--------------------

This plugin provides a clean minimal set of command line options that are added to nosetests.  For
further control of coverage use a coverage config file.

For example if tests are contained within the directory tree being measured the tests may be
excluded if desired by using a .coveragerc file with the omit option set::

    nosetests --cov-config .coveragerc
              --cov myproj
              myproj/tests/

Where the .coveragerc file contains file globs::

    [run]
    omit = tests/*

For full details refer to the `coverage config file`_ documentation.

.. _`coverage config file`: http://nedbatchelder.com/code/coverage/config.html

Note that this plugin controls some options and setting the option in the config file will have no
effect.  These include specifying source to be measured (source option) and all data file handling
(data_file and parallel options).


Limitations
-----------

For subprocess measurement environment variables must make it from the main process to the
subprocess.  The python used by the subprocess must have nose-cov installed.  The subprocess must
do normal site initialisation so that the environment variables can be detected and coverage
started.


Nose Multiprocess Plugin
------------------------

The nose cov plugin partially works with the nose multiprocess plugin.

The nose multiprocess plugin does not join with its child processes so nose cov plugin has coverage
measured but can't merge coverage results and report them.

Work around by using cov plugin and multiprocess plugin together to run tests, note coverage report
from this command will be incorrect::

    nosetests --with-cov --processes=4 tests/

After there will be coverage data files for the main nose process and each subprocess::

    .coverage
    .coverage.hostname.7323.198266
    .coverage.hostname.7339.177156
    .coverage.hostname.7358.543616
    .coverage.hostname.7393.997428

Tell coverage to merge coverage results into one coverage data file::

    coverage combine


Tell coverage to report::

    coverage report


Acknowledgements
----------------

Whilst this plugin has been built fresh from the ground up it has been influenced by the work done
on pytest-coverage (Ross Lawley, James Mills, Holger Krekel) and nose-cover (Jason Pellerin) which are
other coverage plugins.

Ned Batchelder for coverage and its ability to combine the coverage results of parallel runs.

Holger Krekel for pytest with its distributed testing support.

Jason Pellerin for nose.

Michael Foord for unittest2.

No doubt others have contributed to these tools as well.


SDH branch
----------

Added support for "null" reports; useful when all we want to do is generate a .coverage file.
