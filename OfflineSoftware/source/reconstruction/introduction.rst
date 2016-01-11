
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

     FLAGS : use -config SYSTEM::ITEM=value syntax to pass configuration.


   Available Configurations :

     -config CCDB::GEOMRUN=10        : set ccdb run number to 10 for loading geometry
     -config CCDB::GEOMVAR='custom'  : set ccdb variation to 'custom' for loading geometry
     -config CCDB::CALIBRUN=10       : set ccdb run number to 10 for calibration constant
     -config CCDB::CALIBVAR='custom' : set ccdb variation to 'custom' for calibration constants
     -config MAG::torus=0.75         : set scale for TORUS magnet to 3/4
     -config MAG::solenoid=0.5       : set scale for SOLENOID magnet to 1/2
     -config DCTB::kalman=true       : enable kalman filter in time based tracking




  DATABASE OPTIONS: 
     to use local sqlite database type : setenv CCDB_DATABASE etc/database/clas12database.db 



  SHOW AVAILABLE DETECTORS : 12


    *****************************************************************
    * MODULE       * AUTHOR                   *  VERSION * LANGUAGE *
    *****************************************************************
    * BST          * ziegler                  *      1.0 *     java *
    * CTOF         * kenjo                    *      1.0 *     java *
    * DCHB         * ziegler                  *      2.0 *     java *
    * DCTB         * ziegler                  *      2.0 *     java *
    * EB           * gavalian                 *      1.0 *     java *
    * EC           * gavalian                 *      1.0 *     java *
    * FMT          * ziegler                  *      1.0 *     java *
    * FTCAL        * devita                   *      1.0 *     java *
    * FTHODO       * devita                   *      1.0 *     java *
    * FTMATCH      * devita                   *      1.0 *     java *
    * FTOF         * gavalian                 *      1.0 *     java *
    * HTCC         * henkins                  *      1.0 *     java *
    *****************************************************************


Services are the detector reconstruction packages, each of them can be ran
individually or chained together. The order is important to get particles 
reconstructed in the output. The event builder service must be the last one
in the chain. 

The configuration flags can be passed trhough command line, and they are described 
in the printout. 

NOTE: One of the most important flags is "-config DATA::mc=true", indicates that 
the recnostruction is running on GEMC data, due to some inconsistencies in the
detector geometry, this flag is necessary.

Reconstruction program can be used offline (with no internet correction), one has 
to specify the relative path to the sqlite database table to be used instead of mysql
server. The local database is included in the coatjava package, and the relative
location and the environment variable to set are described in the help printout.

The printout also privides list of available plugins, the directory lib/plugins
is scanned for classes that extend DetectorReconstruction and all are available to 
run through reconstruction program. User developed jar files must be copied to 
the plugins directory in order to appear in the available module list.


To run the full chain of forward reconstruction use command:

.. code-block:: bash

   bin/clas12-reconstruction -s DCHB:DCTB:FTOF:EC:EB -i gemc_output.evio -o myoutput.evio

if no "-o" option is given the default file "rec_output.evio" will be created.

Additional parameters can be passed to each reconstruction service from command line using option
-config DETECTOR::option=value. Several parameters can be used in command line. Example :

.. code-block:: bash

   bin/clas12-reconstruction -s DCHB:DCTB:FTOF:EC:EB -i gemc_output.evio -o myoutput.evio \
    -config MAG::torus=0.75 -config MAG::solenoid=0.5 -config DCTB::kalman=true

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


