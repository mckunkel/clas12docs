
.. _clasio-intro:

**********************************
JAVA (jython) Input/Output Library
**********************************

CLAS/IO library is a java package developed to handle input/output of the 
common data types used in CLAS. The interface is implemented to make codes
trasparent to the different types of data used. Implementations exist for
formats BOS/FPACK, EVIO (clas12), and iG5 (data mining format). The document 
describes usage of the package with Jython.

Getting Started
===============

The package coatjava contains all implementations of the standard formats. 
The clas-io package can be downloaded from github:

.. code-block:: bash

   >git clone https://github.com/gavalian/clas-io
   >cd clas-io
   >ant

This will compile the code, and produce a dist/clas-io.jar file. To use 
clas-io package within a project following files need to be copied and included
in the project.

.. code-block:: bash

   >dist/clas-io.jar
   >lib/jevio-4.1.jar


CLAS12 Evio
===========

Working with Dictionary
-----------------------

The bank definitions for CLAS12 are stored in XML files and are dynamically loaded
at run time using the environmental variable CLAS12DIR or CLARA_SERVICES, if either
of these variables are set the program will look for directory lib/bankdefs/clas12
relative path for XML files to load.

Each XML file contains several bank definitions for one detector including raw generated
banks and reconstructed bank structures. Each bank is divided into sections, and each section
has variables with same "tag" number and changing "num".
Here is a sample XML describing a bank with two sections:

.. code-block:: python

   <evio_dictionary>
     <bank name="CUSTOM" tag="400" info="Example Bank">
       <section name="true" tag="401" num="0" info="True information">
         <column name="hitn"    type="int32"   num="1"   info="Hit Number"/>
         <column name="pid"     type="int32"   num="2"   info="Particle ID"/>
         <column name="energy"  type="float64" num="3"   info="Particle Energy"/>
       </section>
       <section name="dgtz" tag="402" num="0" info="True values">
         <column name="nhit"    type="int32"   num="1"  info="Hit Number" />
         <column name="ADC"     type="int32"   num="2"  info="ADC value" />
         <column name="TDC"     type="int32"   num="3"  info="TDC value" />
       </section>
    </bank>
  </evio_dictionary>   

Each section of the bank will be presented as separate bank descriptor. This file will be parsed to have
two descriptors: "CUSTOM::true" and "CUSTOM::dgtz". Here is an example jython script showing parsing of the
file and displaying bank information.

.. code-block:: python

   from   org.jlab.evio.clas12  import EvioDataDictionary, EvioFactory

   EvioFactory.loadDictionary('.')
   EvioFactory.getDictionary().show()

The factory is initialized with XML files found in current directory and the content of 
the dictionary is displayed. The output is:

.. code-block:: bash
   
        +------------------------------------------+--------+--------+--------+
        |                                      Bank| Columns|     Tag|  Number|
        +------------------------------------------+--------+--------+--------+
        |                              CUSTOM::dgtz|       3|     401|       0|
        |                              CUSTOM::true|       3|     402|       0|
        +------------------------------------------+--------+--------+--------+

Two bank descriptors were initialized, each with three columns and 401 and 402 tags respectively.
Initialization of the dictionary can be done in several ways. The method loadDictionary() with
no arguments will search for XML files in directory $CLAS12DIR/lib/bankdefs/clas12, and will
parse all XML files in that directory into descriptors. The method loadDictionary(String dirpath),
will search for XML filed in given directory path.

Show method prints out dictionary descriptors information, for more detailed information about 
particular bank descriptor, use:

.. code-block:: python

   from   org.jlab.evio.clas12  import EvioDataDictionary, EvioFactory

   EvioFactory.loadDictionary('.')
   EvioFactory.getDictionary().getDescriptor('CUSTOM::dgtz').show()

Output:

.. code-block:: bash

   >>> BANK name = CUSTOM::dgtz tag = 400

        +------------------------+--------+--------+------------+
        |                  Column|     Tag|  Number|        Type|
        +------------------------+--------+--------+------------+
        |                    nhit|     402|       1|       int32|
        |                     ADC|     402|       2|       int32|
        |                     TDC|     402|       3|       int32|
        +------------------------+--------+--------+------------+

This will print out detailed information for given descriptor. The first line
gives the descriptor name and the parent container tag (400 in this case), and the
table describes each column with name, tag, number and data type.

Creating Banks
--------------

Once the dictionary has been initialized user can create banks for given structures. Created bank will be 
initialized with given size. Example:

.. code-block:: python

   from   org.jlab.evio.clas12  import  EvioFactory
   from   org.jlab.evio.clas12  import  EvioDataBank
   from   org.jlab.evio.clas12  import  EvioDataDescriptor

   EvioFactory.loadDictionary('.')
   bankDGTZ = EvioFactory.createBank('CUSTOM::dgtz',5)
   bankDGTZ.show()

This will create an instance of "CUSTOM:dgtz" bank and will allocate 5 rows for each column. And the show()
method of the bank will printout the content of the bank with column names. Output looks like:

.. code-block:: bash
   
   *****>>>>> BANK CUSTOM::dgtz  >>>> SIZE = 3
          nhit :             0              0              0              0              0  
           TDC :             0              0              0              0              0  
           ADC :             0              0              0              0              0 

Newly initialized bank has all entries equal to zero. To modify the entries set<type>() functionas are used.
setFloat(name, row, value) or setInt(name, row, value). Here is an example:

.. code-block::	    python

   bankDGTZ.setInt('nhit',0,1)
   bankDGTZ.setInt('nhit',1,2)
   bankDGTZ.setInt('nhit',2,3)

   bankDGTZ.setInt('ADC',0,1200)
   bankDGTZ.setInt('ADC',1,1400)
   bankDGTZ.setInt('ADC',2,1500)

   bankDGTZ.setInt('TDC',0,4600)
   bankDGTZ.setInt('TDC',1,6700)
   bankDGTZ.setInt('TDC',2,4600)

   bankDGTZ.show()

Output:

.. code-block:: bash
   
   *****>>>>> BANK CUSTOM::dgtz  >>>> SIZE = 3
          nhit :             1              2              3              0              0  
           TDC :          4600           6700           4600              0              0  
           ADC :          1200           1400           1500              0              0


Reading Events From a file
--------------------------

For now the evio files do not contain the dictionary object, in the future the dictionary XML
file will be enbedded in the file so user does not have to worry about having the XML descriptors 
locally to be able to access the banks. For now, the EvioSource object has to know where the 
XML files with bank descriptions are located. The dafault directory is $CLAS12DIR/lib/bankdefs/clas12.
Reading events from the file is done using EvioSource object, as in given example:

.. code-block:: python
   
   reader = EvioSource()
   reader.open('../../lib/data/ftof_1Kevents.evio')

   icounter = 0
   while(reader.hasEvent()):
	 event = reader.getNextEvent()
    	 icounter = icounter + 1

   print 'Events analyzed from File = ', icounter

This code initilizes a reader object and loops through events while reader.hasEvent() returns "ture".
To view the content of the event  event.show() function can be used which displays the banks and sections
present in the current event. here is an example output:

.. code-block::	bash

        +------------------------+------------+------------+
        |                    bank|       nrows|       ncols|
        +------------------------+------------+------------+
        |            FTOF1A::true|           1|          23|
        |            FTOF1A::dgtz|           1|           7|
        |            FTOF1B::true|           2|          23|
        |            FTOF1B::dgtz|           2|           7|
        +------------------------+------------+------------+

The banks in the event can be read using:

.. code-block::	 python

   while(reader.hasEvent()):
         event = reader.getNextEvent()
	 bank  = event.getBank('FTOF1A::dgtz')
	 print 'BANK rows = ', bank.rows()
	 bank.show()

The bank is initialized according to the bank descriptor and the content is filled from the event if the 
bank is present, otherwise an empty bank is returned. The rows of the bank for each variable (column) will be
set to zero. Sample output:

.. code-block:: bash

   BANK rows =  0
   *****>>>>> BANK FTOF1A::dgtz  >>>> SIZE = 7
        sector : 
          ADCL : 
          TDCL : 
          TDCR : 
          ADCR : 
        paddle : 
          hitn : 

   BANK rows =  2
   *****>>>>> BANK FTOF1A::dgtz  >>>> SIZE = 7
        sector :             6              6  
          ADCL :            29             29  
          TDCL :          2566           2585  
          TDCR :          2502           2520  
          ADCR :            35             35  
        paddle :             5              6  
          hitn :             1              2  

First printout indicates that the bank is empty (rows = 0), the second printout shows bank with two rows.
Entries in the bank can be accessed using:

.. code-block:: python

   if(bank.rows()>0):
        for i in range(0,bank.rows()):
            print bank.getInt('ADCL')[i]

Java event programming
======================

Reconstruction components for CLAS12 detector that are implemented in Java are extensions of a class
that implements an interface where an EvioDataEvent is passed to processEvent(event) method and all input/output
is handled in the event object. The classes API is the same as for jython scripts. The processEvent(..) method
reads the banks from the event and after reconstruction writes (appends) newly created banks to the EvioDataEvent
object. By design, reconstruction banks for each detector are groupped together in a evio container bank. Sections
below give an overview on how to read banks, construct new ones and append to the event.

Reading event banks
-------------------

Simple implementation of a service will have one method that needs to be implemented which will take an EvioDataEvent
as an argument:

.. code-block:: java

   public class HTCCReconstruction extends Reconstruction {
   	  @Override
   	  public void processEvent(EvioDataEvent event){
	  	 EvioDataBank bankDGTZ = (EvioDataBank) event.getBank("HTCC::dgtz");
		 bankDGTZ.show();
		 int[] sector = bankDGTZ.getInt("sector");
		 System.err.println("rows = " + sector.length);
	  }
   }

This class contained in the jar file can be processed for given file. Each event of the file will be passed through
processEvent() method and the event object (with appended banks) will be written into output file. User needs to read 
the banks used for reconstruction and create (and append) reconstructed banks to the event. Each column of the bank is
accessed through get<type>(column name) methods. for each column appropriate get call should be used.

Creating new banks is also handled by event object. Banks are created using the bank descriptors and each event object has
descriptor dictionary. For creating output banks use:

.. code-block::	       java

   public void processEvent(EvioDataEvent event){
         EvioDataBank bankDGTZ = (EvioDataBank) event.getBank("FTOF::dgtz");
	 ...
	 CLASDetectorGeometry FTOFGeom = getGeometry();
	 double  length = FTOFGeom.getLength(0,0,0,12); // sector=0, suplayer=0 (1a), layer=0, paddle=12
	 // mid point of the detector component, mid.x(),mid.y(),mid.z()
	 Point3D mid    = FTOFGeom.getMidpoint(0,0,0,12); // sector=0, suplayer=0 (1a), layer=0, paddle=12
	 ...
	 double  veff   = getCalibration().constant("VEff",0,0,0,12); // calibration constant veffective 
	 ...
   	 EvioDataBank bankhits  = (EvioDataBank) event.getDictionary().createBank("FTOFRec::rawhits",6);
	 EvioDataBank bankclust = (EvioDataBank) event.getDictionary().createBank("FTOFRec::clusters",3);
	 //.... fill banks	 
	 bankhits.setInt("hid",0,1);
	 bankhits.setInt("hid",1,2);
	 .......
	 event.appendBanks(bankhits,bankclust);
   }

This code will create a bank section "HTCCRec::hits" with 6 rows. To change the values of the columnt in the banks
use functions set<type>(column_name,row,value), interface is the same as in jython case. So setInt("nphe",0,25) will 
set the number of photo-electrons for row 0 to 25.



 

