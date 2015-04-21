
Getting Started
***************

Required Software
=================

CLAS12 software package is written in JAVA and to run the codes and scripts Java>1.7 is required.
To run example scripts groovy package is required. To run on local machines user must install JDK 7
package and groovy.

Running On Jlab CUE 
===================

To use latest version of coatjava (CLAS reconstruction package) on CUE environment must be initialized
to use required software do:

.. code-block:: bash

   ifarm> module load java_1.7
   ifarm> use groovy

Then downloaded coatjava package is ready to use. To use up to date development version installed on CUE 
machines include following in command in cshrc file.

.. code-block:: bash

   /group/clas12/environment_java.csh

Downloading coatjava
====================

The packaged tarball of complete package with neccessary software and files can be downloaded from:

.. code-block:: bash

   wget https://userweb.jlab.org/~gavalian/software/coatjava/coatjava-1.0.tar.gz

Development environment
=======================

To start developing for CLAS12 framework refer to section Development of Code. The library needed
to start development is located on clas maven repository:

.. code-block:: bash
   
   http://clasweb.jlab.org/clas12maven/org/jlab/coat/coat-libs/1.0-SNAPSHOT/

Packages in CoatJava
====================

Here is the list of packages that coatjava distribution contains with links to their javadoc page:

  +-----------------+-----------------------------------------+-------------------------------------------------------------------------------------+
  | Package         |   Description                           |                  Documentation Link                                                 |
  +=================+=========================================+=====================================================================================+
  |  clas-geometry  |  CLAS geometry Package                  |   <http://clasweb.jlab.org/clas12offline/docs/javadocs/clas-geometry/>              |
  +-----------------+-----------------------------------------+-------------------------------------------------------------------------------------+
  |  clas-io        |  CLAS EVIO I/O Package                  |   <http://clasweb.jlab.org/clas12offline/docs/javadocs/clas-io/>                    |
  +-----------------+-----------------------------------------+-------------------------------------------------------------------------------------+
  | clas-physics    | Physics Toolkit Library                 |   <http://clasweb.jlab.org/clas12offline/docs/javadocs/clas-physics/>               |
  +-----------------+-----------------------------------------+-------------------------------------------------------------------------------------+
  | jroot           | Java Plotting Library                   |   <http://clasweb.jlab.org/clas12offline/docs/javadocs/clas-physics/>               |
  +-----------------+-----------------------------------------+-------------------------------------------------------------------------------------+


