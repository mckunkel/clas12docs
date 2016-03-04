
.. _clasio-bosio:

Reading BOS files with Java I/O package
***************************************

The design of CLAS I/O library allows implementation of different 
formats with same interface. And there is a BOS reader implemented
that can read BOS files directly from JAVA. There are also tools
included in COATJAVA package to convert legacy BOS files to EVIO 
format consistent with CLAS12 data format. The advantage of this
is that all standard tools for event manipulation and cuts and corrections
can be used with old data sets.


Preparing to convert
====================

The BOS files created by RECSIS (CLAS-6 event reconstruction program) sometimes
have split event buffers and discontinuities of BANKs within an event record.
Java software can deal with some of the fragmentation in the FPACK format, but
not all of them sometimes resulting in missing banks. In order to avoid this issue
it is recommended to pass BOS file through filter which creates clean continues
records out of BOS file. The command to filter BOS is:

.. code-block:: bash

   /group/clas12/packages/bos/bosutility -filter -b "HEADHEVTEVNTECPBCCPBSCPBLCPB" filtered_run_28456_A00.bos run_28456_A00.bos

The "-b" option specifies which banks should be in the final filtered BOS file, 
if needed other banks can be added as well. Note ! Banks with names shorter than 
4 character have to be completed to 4 characters with " " (space).


Converting BOS to EVIO
======================

The convertor program is called bos2evio and it is included in the coatjava
package. The program will convert BOS file into EVIO, and there are options
to use the SEB scheme or A1C bank scheme.

For SEB scheme use:

.. code-block:: bash

   >bin/bos2evio -seb myoutput.evio run018567.A00.B00

For A1C scheme (PART, TBID and TBER banks) use:

.. code-block:: bash

   >bin/bos2evio -a1c myoutput.evio run018567.A00.B00 run018567.A00.B01

NOTE. Multiple files can be combined into one run file.


Analyzing Data
==============

Once converted to EVIO format legacy CLAS6 data can be analyzed similar to CLAS12 
data. Here is and example script that plots invariant mass of two pions from a data 
run.

.. code-block:: java

   import org.jlab.evio.clas12.*;
   import org.jlab.clas.physics.*;
   import org.root.histogram.*;
   import org.root.pad.*;
   import org.jlab.clas12.physics.*;

   filename = args[0];

   EventFilter  filter = new EventFilter("11:211:-211:X+:X-:Xn");
   EvioSource reader = new EvioSource();
   reader.open(filename);

   H1D H100 = new H1D("H100","M [GeV]","",60,0.25,0.8);

   GenericKinematicFitter fitter = new GenericKinematicFitter(11.0,"11:X+:X-:Xn");
   int counter = 0;
   while(reader.hasEvent()==true){
        counter++;
        EvioDataEvent event = reader.getNextEvent();
        PhysicsEvent  physEvent = fitter.getPhysicsEvent(event);
        if(filter.isValid(physEvent)==true){
            Particle pipi = physEvent.getParticle("[211]+[-211]");
            H100.fill(pipi.mass());
        }
  }

  TCanvas c1 = new TCanvas("c1","Physics Analysis",800,600,1,1);
  c1.setFontSize(18);
  c1.cd(0);
  H100.setTitle("Two Pion Invariant Mass");
  H100.setLineWidth(2);
  H100.setLineColor(2);
  c1.draw(H100);


For more detailed documentation on how to use CLAS12 physics classes see "Analyzing Reconstructed Data".
