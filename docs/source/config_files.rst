.. _config-files:

===================
Configuration Files
===================

``repo2docker`` looks for configuration files in the repository being built
to determine how to build it. In general, ``repo2docker`` uses the same
configuration files as other software installation tools,
rather than creating new custom configuration files.

A number of ``repo2docker`` configuration files can be combined to compose more
complex setups.

The `binder examples <https://github.com/binder-examples>`_ organization on
GitHub contains a list of sample repositories for common configurations
that ``repo2docker`` can build with various configuration files such as
Python and R installation in a repository.

Below is a list of supported configuration files (roughly in the order of build priority):

.. contents::
   :local:
   :depth: 1

.. _environment-yml:

``environment.yml`` - Install a Python environment
==================================================

``environment.yml`` is the standard configuration file used by Anaconda, conda,
and miniconda that lets you install packages in the data analytics stack (it
primarily installs Python packages, though can be used to install a range of
non-Python packages as well).

.. note::

   You can install files from pip in your ``environment.yml`` as well.
   For example, see the `binder-examples environment.yml
   <https://github.com/binder-examples/python-conda_pip/blob/master/environment.yml>`_
   file.

You can also specify which Python version to install in your built environment
with ``environment.yml``. By default, ``repo2docker`` **installs
Python 3.6** with your ``environment.yml`` unless you include the version of
Python in the file.  ``conda`` supports Python versions 3.6, 3.5, 3.4, and 2.7.
``repo2docker`` support is best with Python 3.6, 3.5, and 2.7.

.. warning::
   If you include a Python version in a ``runtime.txt`` file in addition to your
   ``environment.yml``, your ``runtime.txt`` will be ignored.


``requirements.txt`` - Install a Python environment
===================================================

This specifies a list of Python packages that should be installed in your
environment. Our
`requirements.txt example <https://github.com/binder-examples/requirements/blob/master/requirements.txt>`_
on GitHub shows a typical requirements file.


``setup.py`` - Install Python packages
======================================

To install your repository like a Python package, you may include a
``setup.py`` file. repo2docker installs ``setup.py`` files by running
``pip install -e .``.


``REQUIRE`` - Install a Julia environment
=========================================

This specifies a list of Julia packages. Repositories with a  ``REQUIRE`` file
**must also contain an** ``environment.yml`` **file**.  To see an example of a
Julia repository with ``REQUIRE`` and ``environment.yml``,
visit `binder-examples/julia-python <https://github.com/binder-examples/julia-python>`_.


``install.R`` - Install an R/RStudio environment
================================================

This is used to install R libraries pinned to a specific snapshot on
`MRAN <https://mran.microsoft.com/documents/rro/reproducibility>`_.
To set the date of the snapshot add a runtime.txt_.
For an example ``install.R`` file, visit our `example install.R file <https://github.com/binder-examples/r/blob/master/install.R>`_.


``apt.txt`` - Install packages with apt-get
===========================================

A list of Debian packages that should be installed. The base image used is usually the latest released
version of Ubuntu.

We use ``apt.txt``, for example, to install LaTeX in our
`example apt.txt for LaTeX <https://github.com/binder-examples/latex/blob/master/apt.txt>`_.


``DESCRIPTION`` - Install an R package
======================================

To install your repository like an R package, you may include a
``DESCRIPTION`` file. repo2docker installs the package and dependencies
from the ``DESCRIPTION`` by running ``devtools:install_git(".")``.

You also need to have a ``runtime.txt`` file that is formatted as
``r-<YYYY>-<MM>-<DD>``, where YYYY-MM-DD is a snapshot of MRAN that will be
used for your R installation.

.. _postBuild:

``postBuild`` - Run code after installing the environment
=========================================================

A script that can contain arbitrary commands to be run after the whole repository has been built. If you
want this to be a shell script, make sure the first line is ``#!/bin/bash``.

An example use-case of ``postBuild`` file is JupyterLab's demo on mybinder.org.
It uses a ``postBuild`` file in a folder called ``binder`` to `prepare
their demo for binder <https://github.com/jupyterlab/jupyterlab-demo/blob/master/binder/postBuild>`_.


.. _start:

``start`` - Run code before the user sessions starts
====================================================

A script that can contain simple commands to be run at runtime (as an
`ENTRYPOINT <https://docs.docker.com/engine/reference/builder/#entrypoint>`
to the docker container). If you want this to be a shell script, make sure the
first line is ```#!/bin/bash``. The last line must be ```exec "$@"```
equivalent.

Use this to set environment variables that software installed in your container
expects to be set. This script is executed each time your binder is started and
should at most take a few seconds to run.

If you only need to run things once during the build phase use :ref:`postBuild`.


.. TODO: Discuss runtime limits, best practices, etc.
   Also, point to an example.

.. _runtime.txt:

``runtime.txt`` - Specifying runtimes
=====================================

This allows you to control the runtime of Python or R.

To use python-2.7: add ``python-2.7`` in runtime.txt file.
The repository will run in a virtualenv with
Python 2 installed. To see a full example repository, visit our
`Python2 example <https://github.com/binder-examples/python2_runtime/blob/master/runtime.txt>`_.
**Python versions in** ``runtime.txt`` **are ignored when** ``environment.yml`` **is
present in the same folder**.

repo2docker uses R libraries pinned to a specific snapshot on
`MRAN <https://mran.microsoft.com/documents/rro/reproducibility>`_.
You need to have a ``runtime.txt`` file that is formatted as
``r-<YYYY>-<MM>-<DD>``, where YYYY-MM-DD is a snapshot at MRAN that will be
used for installing libraries.

To see an example R repository, visit our `R
example in binder-examples <https://github.com/binder-examples/r/blob/master/runtime.txt>`_.


``Dockerfile`` - Advanced environments
======================================

In the majority of cases, providing your own Dockerfile is not necessary as the base
images provide core functionality, compact image sizes, and efficient builds. We recommend
trying the other configuration files before deciding to use your own Dockerfile.

With Dockerfiles, a regular Docker build will be performed.

.. note::
    If a Dockerfile is present, all other configuration files will be ignored.

See the `Advanced Binder Documentation <https://mybinder.readthedocs.io/en/latest/dockerfile.html>`_ for
best-practices with Dockerfiles.
