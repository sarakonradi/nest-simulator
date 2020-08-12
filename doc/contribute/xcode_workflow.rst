Xcode workflow
==============

This article contains instructions on how to develop NEST on a Mac (OSX 10.10.3 as of this writing) using Xcode
(Version 6.3.2). As the shipped gcc, aka clang (based on LLVM 3.6.0svn), does not support OpenMP and there is
no MPI shipped by default, this also explains, how to get a proper gcc (with OpenMP and MPI enabled) installed on Mac.

Setup infrastructure
--------------------

We need several packages installed, before we can become productive with NEST:

* gcc
* openmpi 1.6 (or later)
* gsl
* cmake
* libtool
* ipython, python, cython, ... The best way to install all the python requirements is to use
  `Anaconda <https://store.continuum.io/cshop/anaconda/>`_.

We present two ways to install the rest: MacPorts and Homebrew. For both versions you need to have Xcode and Xcode
command line tools installed:

1. Install Xcode from the AppStore.
2. Install the Xcode command line tools by executing the following line in the Terminal and following the
   instructions in the windows that will pop up

   .. code::

      xcode-select --install

Homebrew
~~~~~~~~

1. Follow the install instructions for Homebrew (`short <http://brew.sh/>`_ or
   `long <https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/Installation.md#installation>`_)
2. Open up the Terminal and execute the following lines:

   .. code::

      brew install gcc gsl cmake open-mpi libtool

MacPorts
~~~~~~~~

We recommend using the Homebrew workflow, since there you can use a more current openmpi version for NEST, but
we leave the MacPorts instructions for legacy purposes.

1. Follow the install instructions for `MacPorts <https://www.macports.org/install.php>`_.
2. Open up the Terminal and execute the following lines:

   .. code::

      sudo port install gcc48
      sudo port select gcc mp-gcc48 # make gcc-48 the default compiler
      sudo port install gsl +gcc48
      sudo port install cmake       # build tools

3. NEST on Mac requires OpenMPI 1.6 from MacPorts to work properly, so we have to get this older version for MacPort.
   Download the portsfile
   `Portfile-openmpi-1.6.4.txt <http://www.nest-simulator.org/wp-content/uploads/2014/12/Portfile-openmpi-1.6.4.txt>`_
   and save it under the name ``Portfile`` in an arbitraty directory.
4. In Terminal, move to the directory containing Portfile and run

   .. code::

      sudo port install +gcc48 +threads configure.compiler=macports-gcc-4.8

Install NEST
------------

Please refer to our :doc:`../installation/index`.

Get Xcode working with NEST
---------------------------

1. Create a new project, which we will call ``NEST-fork`` in this article. In the menu
   select File -> New -> Project... . Then select OS X -> Other -> External Build System
   (with build tool ``/usr/bin/make``).
2. Add the NEST sources to the project. There is a `+` in the left-bottom corner. Click
   ``Add Files to "NEST-fork"...``. Then select the ``<somebase>/NEST/src/`` folder (do not copy items and use groups).
   Also add the generated files:

   .. code::

      <somebase>/NEST/build/libnestutil/config.h
      <somebase>/NEST/build/libnestutil/sliconfig.h
      <somebase>/NEST/build/nest/static_modules.h
3. On the left panel select the newly created project ``NEST-fork``, then select the created target/.
   Here you set set Directory to ``<somebase>/NEST/build``. This will be the directory, in which the ``make`` command
   is executed. Also check ``Pass build settings in environment``.
4. Select the ``Build Settings`` panel.
   Here you ``Add User-Defined Setting`` and name it ``PATH``. In the ``NEST-fork`` column (the second) you copy the
   content of your ``PATH`` variable (do ``echo $PATH`` in the Terminal).
5. The build system (CMD+B) should work from now on.

Running NEST from Xcode
~~~~~~~~~~~~~~~~~~~~~~~

We have to edit the Targets Scheme:

1. In the menu select: Product -> Scheme -> Manage Schemes...
2. Select the ``NEST-fork`` target and hit ``Edit...``
3. Select the ``Run`` option on the left and then on the right select ``Info``.
4. As ``Executable`` select ``<somebase>/NEST/install/bin/nest``.
5. You can specify arguments in the ``Arguments`` panel.

.. note::

   The executable ``<somebase>/NEST/install/bin/nest`` will only be updated, if you execute ``make install`` in
   the terminal.

Code completion in Xcode
~~~~~~~~~~~~~~~~~~~~~~~~

We have to create a new target and configure it appropriately:

1. In the menu select: File -> New -> Target...
2. Make the target a OS X -> Command Line Tool (Next), of type C++ in your project (preselected). We call it
   ``completion``.
3. Remove all files and folders that are created with the new target.
4. In the tab "Build Phase" of the new target, under "Compile Sources" add all ``*.h``, ``*.hpp``, ``*.c``, ``*.cc``,
   ``*.cpp`` files from the list (you can use CMD+a)
5. Now Xcode generates its index and after that code completion should work.