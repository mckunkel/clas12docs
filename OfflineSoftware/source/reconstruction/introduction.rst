
Running CLAS-12 Reconstruction
******************************

This section describes reconstruction tools for CLAS12 detector.
The reconstruction package is distributed as a tar.gz package and
contains all neccessary files to run reconstruction.

Event Simulation 
================

For sumulating CLAS12 detector the GEMC package is used. The input
to GEMC is an event file in LUND format. The full documentation
on how to run GEMC can be found at: https://gemc.jlab.org

Running Reconstruction
======================

The software for reconstruction can be downloaded from https://userweb.jlab.org/~gavalian/software/coatjava/

After unpacking the coatjava CLAS12 reconsturction package one can 
find all exacuatebles for reconstruction in bin/ directory. The reconstruction
program is called clas12-reconstruction and it accepts following arguments.

.. code-block:: bash

  Usage : generic  [options]

    Options: 
    -i          :   input file name
    -l          :   number of events to skip
    -n          :   number of events to run
    -o          :   output file name
    -s          :   service list to run (e.g. BST:FTOF:EB )

Services are the detector reconstruction packages, each of them can be ran
individually or chained together. The order is important to get particles 
reconstructed in the output. The event builder service must be the last one
in the chain. Here is the list of available services:

 +--------------+-----------------------------------------+
 | Service Name |   Description                           |
 +==============+=========================================+
 |    DCHB      | Drift Chamber Hit  Based tracking       |
 +--------------+-----------------------------------------+
 |    DCTB      | Drift Chamber Time Based tracking       |
 +--------------+-----------------------------------------+
 |    TFOT      | Time Of Flight Reconstruction (1A,1B,2) |
 +--------------+-----------------------------------------+
 |    BST       | SVT Reconstruction package              |
 +--------------+-----------------------------------------+
 |    EC        | EC/PCAL reconstruction                  |
 +--------------+-----------------------------------------+
 |    EB        | Generic Event Builder package           |
 +--------------+-----------------------------------------+

To run the full chain of forward reconstruction use command:

.. code-block:: bash

   bin/clas12-reconstruction -s DCHB:DCTB:FTOF:EC:EB -i gemc_output.evio -o myoutput.evio

if no "-o" option is given the default file "rec_output.evio" will be created.

Additional parameters can be passed to each reconstruction service from command line using option
-config DETECTOR::option=value. Several parameters can be used in command line. Example :

.. code-block:: bash

   bin/clas12-reconstruction -s DCHB:DCTB:FTOF:EC:EB -i gemc_output.evio -o myoutput.evio \
    -config DCHB::torus=0.75 -config DCHB::solenoid=0.5 -config DCTB::kalman=true

This command line will run reconstruction with torus field scaled with 0.75 and solenoid field scaled
with 0.5, and will run with Kalman filter. use option kalman=false to switch off Kalman filter.

Check Reconstruction Results
============================

After running reconstruction one could use standard tools to check the 
quality of reconstruction, such as particle resolutions particle identification
and event builder hit matching quality. To run the tool do:

.. code-block:: bash

   bin/clas12-monitor myoutput.evio

The graphical interface show several plot canvases for reconstruction quality plots 
for all detector services.


Kinematics Monitoring
=====================

To view kinematics of reconstructed event one could use the standard tool
for kinematics monitoring. It displays plots for vaious final states inclusing
double pion production and DVCS (more channels will be added). To run kinematics
monitoring tool use:

.. code-block:: bash

   bin/clas12-kinematics myoutput.evio


