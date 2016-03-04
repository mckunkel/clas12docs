
.. _clasio-raw:

******************************
JAVA EVIO Input/Output Library
******************************

The clas-io library provides utilities to read/write evio files consistent with CLAS12
conventions. It has classes to manipulate RAW (DAQ) EVIO files as well as GEMC output
EVIO files with dictionary based bank structures.

Reading Raw (DAQ) Files
=======================

Following are examples of groovy scripts used to read evio RAW data files. The examples 
can be found in coatjava package in directory scripts/evio.
To read raw files one must use:

.. code-block:: java

  import org.jlab.evio.clas12.*;
  import org.jlab.clas12.raw.*;
  import org.jlab.evio.decode.*;
  import org.jlab.clas.detector.*;

  outputFile = args[0];

  EvioSource  reader = new EvioSource();
  reader.open(outputFile);

  EvioEventDecoder decoder = new EvioEventDecoder();

  int counter = 0;

  while(reader.hasEvent()){

     EvioDataEvent event = reader.getNextEvent();
     List<DetectorRawData> rawData = decoder.getDataEntries(event);

     for(DetectorRawData data : rawData){
        System.out.println(data);
     }
     counter++;
  }

  reader.close();



This script will read each event and print out branches that correspond to crate numbers.
The printout looks like:

.. code-block:: bash

   [EVIO DATA EVENT LIST]
   EVIO Branch tag = 4 (4) num = d7 (215)
     evio leaf : tag = e10a (57610) num = 0 (0) length = 4
     evio leaf : tag = e111 (57617) num = 0 (0) length = 35
     evio leaf : tag = e10f (57615) num = 0 (0) length = 5

Where tag corresponds to CRATE number and num is changing from 0 to 255 depending on the event number 
to keep track of event sequence. The leafs have different structure and contain data read from the 
crate. The data is written in composite bank format, and it is the branch with tag=57617 in this example.
The tags can vary depending on the mode used to write data (raw ADC mode or Pulse ADC mode).
The EvioRawDataSource class will automatically recognize the tags and parse them individually depending 
on the data mode.

Raw Data Banks
==============

There is one class that handles bank structure for raw data and it initializes differently depending
on the mode of the bank (determined by the tag number). To read a specific crate information add these lines
to previous example:

.. code-block:: java

  ...
  EvioRawDataBank bank = reader.getDataBank(event,4);
  System.out.println(bank);
  ...

The raw data bank object will be created from the data for CRATE 4. The printout looks like:

.. code-block:: bash

   [RAW Data Bank] CRATE :    4 , SLOT =    3 , TRIGGER =    24022 , TIME =     28953094729186
   CHANNEL :  376
     -->   0 : PULSE (mode =  4) :      376      211        2
   CHANNEL :  377
     -->   0 : PULSE (mode =  4) :      377      210        7
   CHANNEL :  378
     -->   0 : PULSE (mode =  4) :      378      211        3
   CHANNEL :  867
     -->   0 : PULSE (mode =  4) :      867      211        0 


Depending on the content of the leafs in the EVIO node, the EvioRawDataBank will create objects
of RawData differently. Before accessing the data use has to check the mode of RawData object.
To read the data from EvioRawDataBank the list of channels has to be obtained first, then
for each channel, list of RawData objects can be obtained. Each channel in the crate can have multiple
hits.

.. code-block:: java

    if(bank!=null){
      List<Integer>  channels = bank.getChannelList(); // LIST of channels
      for(Integer chan : channels){
        List<RawData>  channelData = bank.getData(chan); // List of hits for given channel
        for(RawData data : channelData){
           System.out.println("channel = " + chan + " mode = " + data.mode());
        }
      }
    }

