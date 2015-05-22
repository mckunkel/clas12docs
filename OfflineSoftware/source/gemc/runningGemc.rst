
Running `gemc <https://gemc.jlab.org/gemc/Home.html>`_ at JLab
**************************************************************

gemc is installed on the JLab interactive ifarm65 and jlabl4 CUE machines and on the JLab farm nodes.
This section describes how to run gemc to simulate events the CLAS12 detectors.


Sourcing the environment
========================

The environment is necessary to load the libraries needed to run gemc.

.. code-block:: bash

	source /site/12gev_phys/production.csh

csh is supported, and bash is in the works.


Running gemc
============

To run 10 events with the CLAS12 detectors:


.. code-block:: bash

	gemc /group/clas12/clas12.gcard -USE_GUI=0 -N=10


The **USE_GUI** option tells gemc to run in batch mode.

The output format (default: evio) is specified in the gcard:

.. code-block:: bash

	<option name="OUTPUT" value="evio, out.ev"/>

A text format is also available.

To modify the default clas12 gcard located in /group, you can copy it and use it from anywhere.


gemc/gcard options
==================

The gcards and/or command line set the simulation conditions.
The command line always overwrites the gcard.

The gemc command line options are in the format:

.. code-block:: bash

 -OPTION="argument"

For example, to set the primary beam particle type, momentum, theta and phi:

.. code-block:: bash

 -BEAM_P="e-, 6*GeV, 15*deg, 20*deg"

The corresponding option in the gcard has a **name** **value** syntax like this:

.. code-block:: bash

 <option name="BEAM_P" value="e-, 6*GeV, 15*deg, 20*deg"/>


.. note::

 All command line options can be used in the gcard and viceversa.


Loading / Unloading detectors
=============================


A detector can be loaded with several variations (a variation could have a different material, or dimensions, or positioning).

To load a detector in the gcard:

.. code-block:: bash

	<detector name="<path>/bst" factory="TEXT" variation="original"/>

Where **name** points to the name (with path) of the detector system and **variation** points to its variation.
To remove a detector simply remove or comment its corresponding line.


Using the internal generator
============================

A primary particle 4-momentum and vertex ranges can be set with the directives:

.. code-block:: bash

 <option name="BEAM_P"   value="proton, 4.0*GeV, 20.0*deg, 10*deg"/>
 <option name="SPREAD_P" value="1*GeV, 10*deg, 180*deg"/>
 <option name="BEAM_V"   value="(0, 0, -5)cm"/>
 <option name="SPREAD_V" value="(0.1, 10)cm"/>

The above will generate a proton with:
 * :math:`p` between 3 and 5 GeV.
 * :math:`\theta` between 10 and 30 degrees.
 * :math:`\phi` between 0 and 360 degrees.
 * vertex z between -5 and 5 cm.
 * vertex radius between 0 and 0.1 cm.


Using a custom generator
========================

gemc support the  `LUND format <https://gemc.jlab.org/gemc/Documentation/Entries/2011/3/18_The_LUND_Format.html>`_ .
To generate events using a LUND file:

.. code-block:: bash

 -INPUT_GEN_FILE="LUND, filename"


Generating Background
======================

To add background coming from the beam the following quantities must be defined

 1. a time window: the total time of one event
 2. the number of beam particles for each event
 3. the number of beam bunches

These quantities are defined with the **LUMI_EVENT** option.
For example for clas12 :math:`10^{35}` luminosity on 5cm LH2 target:

.. code-block:: bash

 <option name="LUMI_EVENT"     value="124000, 250*ns, 2*ns" />
 <option name="LUMI_P"         value="e-, 11*GeV, 0*deg, 0*deg" />
 <option name="LUMI_V"         value="(0.,0.,-10.)cm" />
 <option name="LUMI_SPREAD_V"  value="(0.01, 0.01)cm" />

Adds 124000 e- in 250 ns time window, grouped in 2 ns bunches. That would produce 125 bunches with 992 particles each bunch.
The beam is 100 micron wide and starts 10 cm upstream of the center of the target.


Scaling Magnetic Fields
=======================

There are two magnetic fields: torus (*clas12-torus-big*)  and solenoid (*clas12-solenoid*).


They both can be scaled with the **SCALE_FIELD** option. For example:

.. code-block:: bash

 <option name="SCALE_FIELD" value="clas12-torus-big, -0.8"/>
 <option name="SCALE_FIELD" value="clas12-solenoid, 0.5"/>

will invert and scale the torus, and halve the solenoid.


.. note::

 By default the torus map has e- outbending. So in order to have e- in-bending the torus field has to be
 scaled by -1 (done in the clas12 gcard).

gemc options
============

The gemc command line options are in the format:

.. code-block:: bash

 -OPTION="argument"

For example, to set the primary beam particle

Typing gemc -help will show the help sub-categories:

.. code-block:: bash

 Help Options:

 >  -help-all:  all available options.

 >  -help-control             control options.
 >  -help-general             general options.
 >  -help-generator           generator options.
 >  -help-luminosity          luminosity options.
 >  -help-mysql               mysql options.
 >  -help-output              output options.
 >  -help-physics             physics options.
 >  -help-verbosity           verbosity options.

You can access to a specific subcategory like this:

.. code-block:: bash

 gemc -help-control


Running clas12 simulation on a personal computer
================================================

gemc can be installed on apple computers using the dmg found `here <https://gemc.jlab.org/gemc/Downloads.html>`_.

For linux OSes a installation from source is required, that involves installing all the dependency libraries.
Here are the `installation instructions <https://www.jlab.org/12gev_phys/packages/sources/ceInstall/1.2_install.html>`_


The CLAS12 detectors geometry, materials, fields etc can be downloaded from the `gemc download page <https://gemc.jlab.org/gemc/Downloads.html>`_.


