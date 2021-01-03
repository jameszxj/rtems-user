.. SPDX-License-Identifier: CC-BY-SA-4.0

.. Copyright (C) 2019 embedded brains GmbH
.. Copyright (C) 2019 Sebastian Huber
.. Copyright (C) 2016 Chris Johns <chrisj@rtems.org>

.. _QuickStartPrefixes:

Choose an Installation Prefix
=============================

.. index:: prefix

You will see the term :ref:term:`prefix` referred to throughout this
documentation and in a wide number of software packages you can download from
the internet.  It is also used in the
`GNU Coding Standard <https://www.gnu.org/prep/standards/html_node/Directory-Variables.html>`_.
A *prefix* is the path on your host computer a software package is installed
under.  Packages that have a prefix will place all parts under the prefix
path.  Packages for your host computer typically use a default prefix of
:file:`/usr/local` on FreeBSD and Linux.

You have to select a prefix for your installation. You will build and install
the RTEMS tool suite, an RTEMS kernel for a BSP, and you may build and install
third party libraries. You can build all the parts as a stack with a single
prefix or you can separate various parts by providing different prefixes to
each part as it is built. Using separate prefixes is for experienced RTEMS
users.

Do not select a prefix that is under the top of any of the source trees. The
prefix collects the install output of the various build steps you take in this
guide and need to be kept separate from the sources used.

The RTEMS tool suite consists of a cross tool chain (Binutils, GCC, GDB,
Newlib, etc.)  for your target architecture and :ref:`RTEMS tools <HostTools>`
provided by the RTEMS Project. The RTEMS Tools are a toolkit that help create
the RTEMS ecosystem and help support the building of embedded real-time
applications and systems.

You build and install the tool suite with the :ref:`RTEMS Source Builder (RSB)
<RSB>`.  By default, the RSB will start the prefix path with a host operating
system specific path plus :file:`rtems`, and the RTEMS version, e.g.
:file:`/opt/rtems/5` on Linux, and :file:`/usr/local/rtems/5` on FreeBSD and
macOS. Placing the RTEMS version number in the path lets you manage and
migrate RTEMS versions as they are released.

It is strongly recommended to run the RSB as a *normal user* and not with
*root* privileges (also known as *super user* or *Administrator*).  You have to
make sure that your normal user has sufficient privileges to create files and
directories under the prefix.  For example, you can create a directory
:file:`/opt/rtems` and give it to a developer group with read, write, and
execute permissions.  Alternatively, you can choose a prefix in your home
directory, e.g. :file:`$HOME/rtems/5` or with a project-specific component
:file:`$HOME/project-x/rtems/5`.  For more ideas, see the :ref:`project
sandboxing <ProjectSandboxing>` section.  In this quick start chapter, we will
choose :file:`$HOME/quick-start/rtems/5` for the RTEMS tool suite prefix.

.. warning::

    The prefix must not contain space characters.
