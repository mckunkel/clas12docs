
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
