
Analyzing Reconstructed Data
****************************

This section describes analysis tools available within clara framework
to analize reconstructed data events.

Groovy Scripts
==============

The analysis can be run as groovy scripts, and all Java classes can be used
inside groovy script. This method ties well with the framework and provides
standard tools for reading EVIO files and reconstruction banks. To run groovy
scripts the groovy package has to be installed on the computer. On OSX machines
this can be done by:

.. code-block:: bash
   
   > sudo port install groovy

On Jlab farm machines one has to initialize groovy by:

.. code-block:: bash

   > use groovy

Once groovy is installed on the system any groovy script can be run using:

.. code-block:: bash

   coatjava> bin/run-groovy myscript.groovy

The example scripts are located in scripts folder in the distribution.



Reading EVIO files
==================

The output of reconstruction is an EVIO file with bank structures defined in XML 
files. The XML descriptors are located in etc/bankdef/clas12 directory. Following 
script will run through events in the file:

.. code-block:: java
    
       import org.jlab.evio.clas12.*;
       
       EvioSource reader = new EvioReader();
       reader.open("myfile.evio");
       while(reader.hasEvent()){
	  EvioDataEvent event = reader.getNextEvent();
	  event.show(); // print out all banks in the event
       }

The banks are reffered to with their name and the section separated by '::',
for example to read digitized bank from DC the following code can be used:

.. code-block:: java

   ...
   EvioDataEvent event = reader.getNextEvent();
   if(event.hasBank("DC::dgtz")){
      EvioDataBank dcBank = event.getBank("DC::dgtz");
      dcBank.show(); // printout the content of the bank
   }
   ...

The EvioDataSource object is derived from DataSource interface, and there are several implementations
of DataSource, one of them is the BOS (legacy CLAS6 format) reader. In the same manner the BOS files
can be read within our Java framework with no additional effort. To read bos files the bos reader 
interface can be used. There are slight differences in the bank description in BOS format, since the
BOS banks are numbered, the descriptor to reatrieve particular bank should contain the number of the 
bank within the name, if no number is present '0' will be assumed.


Reading Reconstruction Events
=============================

The EVIO structures produced by reconstruction program can be parsed to produce
events for physics analysis. There is a standard interface allowing to implement
different ways to identify particles. One simple implementation exists called
GenericKinematicFitter which reads generated particle bank and the reconstructed
particle bank with the PID assigned by standrard Event Builder. Example:


.. code-block:: java

   import org.jlab.evio.clas12.*;
   import org.jlab.clas.physics.*;
   import org.jlab.clas12.physics.*;

   EvioSource reader = new EvioSource();
   reader.open("myrec.evio");
   // create new kinematic fitter, with beam energy 11.0 GeV and electron filter
   GenericKinematicFitter fitter = new GenericKinematicFitter(11.0,"11:X+:X-:Xn");
   
   while(reader.hasEvent()==true){
	EvioDataEvent event = reader.getNextEvent();
        PhysicsEvent  recEvent  = fitter.getPhysicsEvent(event);
        PhysicsEvent  genEvent  = fitter.getGeneratedEvent(event);
	System.out.println(genEvent.toLundString());
	System.out.println(recEvent.toLundString());
   }
   
This will print out on the screen generated event and reconstructed event.

Working with Physics Events
===========================

Physics event object is a container for particles and has a beam particle and 
a target particle, there are few convenience methods that alow checking the 
final state of the event. Particle id's are used to require specific particle
to be in the final state, and "X" followed by the sign is used to require any 
number of particles with particular charge. For example "X+" means any number 
of positively charged particles, "X-" and "Xn" for negative and neutral particles
respectively. EventFilter object must be created to check the final state
of the event.

.. code-block:: java

   ...
   EventFilter  filter = new EventFilter("11:2212:-211:X+:X-:Xn");
   if(filter.isValid(recEvent)==true){
	Particle mx_eppi = recEvent.getParticle("[b]+[t]-[11]-[2212]-[-211]");
	double mass  = mx_eppi.mass();
	double mom   = mx_eppi.p();
	double theta = mx_eppi.theta();
	double phi   = mx_eppi.phi();
   }
   ...

The code above checks if the event has at least one electron, one proton and one 
negative pion and then constructs a missing mass of "e-,p,pi-". The symbol "[b]"
stands for beam particle and "[t]" stands for target particle. There can be multiple
particle of the same kind in the event, and the syntax allows picking up particles
by the order. For example:

.. code-block:: java
   
   ...
   EventFilter  filter = new EventFilter("11:2212:2212"); // exclusive e-,p,p
   if(filter.isValid(recEvent)==true){
	Particle mx_epp = recEvent.getParticle("[b]+[t]-[11]-[2212,0]-[2212,1]");
   }
   ...

Entry "[2212,1]" takes the second (skip=1) proton from the event, "[2212,0]" takes the first
event, if no skip parameter is mentioned first particle is assumed "[2212]" is same 
as "[2212,0]".
The following example loops through events and plots the missing mass of two pions.

.. code-block:: java

   import org.jlab.evio.clas12.*;
   import org.jlab.clas.physics.*;
   import org.jlab.clas12.physics.*;
   import org.root.histogram.*;
   import org.root.pad.*;

   EvioSource reader = new EvioSource();
   reader.open("myrec.evio");
   GenericKinematicFitter fitter = new GenericKinematicFitter(11.0,"11:X+:X-:Xn");
   EventFilter  filter = new EventFilter("11:211:-211:X+:X-:Xn");
   H1D MxPiPi = new H1D("MxPiPi",120,0.01,0.35);

   while(reader.hasEvent()==true){
        EvioDataEvent event = reader.getNextEvent();
        PhysicsEvent  recEvent  = fitter.getPhysicsEvent(event);

   	if(filter.isValid(recEvent)==true){
	   Particle mx_epipi = recEvent.getParticle("[b]+[t]-[11]-[211]-[-211]");
	   MxPiPi.fill(mx_epipi.mass());
   	}
   }

   TCanvas c1 = new TCanvas("c1","Physics Analysis",1000,800,2,3);
   c1.cd(0);
   c1.draw(MxPiPi);

The code loops through events and picks events corresponding to the given filter
then gets particle for given string syntax and fills the mass histogram.
