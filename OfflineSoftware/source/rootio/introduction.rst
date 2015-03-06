
.. _clasrootio-intro:

******************************** 
ROOT (CINT) Input/Output Library
********************************

This package is a C++ I/O library for reading CLAS12 EVIO files from C++.
The library also contains wrappers to read CLAS12 events from ROOT/CINT.

Getting Started
===============

The clas-root libraries are compiled and are part of the CLAS12 environment 
on the CUE machines. to use the library on CUE machines you will need to source
the file /group/clas12/environment.csh. The scripts provided in this document will
work on CUE machines. 
To run on local machine the library must be downloaded and 
compiled. The repository is located on github, to get it use command:

.. code-block:: python

   mymachine> git clone https://github.com/gavalian/clas-root
   mymachine> cd clas-root
   mymachine> setenv CLASROOT `pwd`
   mymachine> scons

The setup of enviroment variabe CLASROOT is necessary for the package to locate
the dictionaries for bank neccessary to construct ROOT tree for evio data files.
The examples folder contains sample codes that run in ROOT(CINT).

Example Session
===============

The wrapper for EVIO ROOT library overrides the TTree ROOT object and presents the
EVIO data file in a ROOT Tree form. This makes it easy to browse the tree and make
plots right from the browser.
To starts a session with ROOT/CINT, user needs to set up few environment variables.
CLASROOT should point to the clas-root distribution directory for dictionary files,
and LD_LIBRARY_PATH should be updated to include lib directory from CLASROOT

.. code-block:: python

  mymachine> setenv LD_LIBRARY_PATH ${CLASROOT}/lib:${LD_LIBRARY_PATH}

Once all this is set the ROOT/CINT session can load the libraries and dictionaries.

.. code-block:: cpp

  mymachine> root -l
  root[0] gSystem->Load("libEvioRoot.so");
  root[1] TTreeEvio tree("mysample.evio");
  root[2] TBrowser b("b",&tree);

At this point you'll be presented with a ROOT Browser with Tree directory, which will 
contain all branches and leafs defined in the dictionaries.

.. image:: images/clasroot-treewindow.png



Example Scripts
===============

Reading Evio From a Script
--------------------------

The TTreeEvio object can also be used in a CINT script to read the branches and leafs.
Here is an example script demonstrating how to read FTOF1B paddles and ADC values from
the tree object:

.. code-block:: cpp

  {
    // loading library
    gSystem->Load("../lib/libEvioRoot.so");
    // Open a file with TTreeEvio
    TTreeEvio tree("input.evio");

    int nentries = tree.GetEntries();

    TH1D *H1 = new TH1D("H1","Ftof 1B Paddles",70,0.0,70.0);
    // Loop over all events in the tree
    for(int loop = 0; loop < nentries; loop++){

      // reading next event into the tree
      tree.LoadTree(loop);
      // get number of rows for FTOF1B digitized bank
      Int_t nrows = tree.GetNRows("FTOF1B::dgtz");
      cout << " NRows =  " << nrows << endl;
      // Loop over rows and get values for paddle and ADCL
      for(int row = 0; row < nrows; row++){
        // Use GetValueF - for fload columns and GetValueD - for double columns.
        cout << row << "  paddle = " << tree.GetValueI(row,"FTOF1B::dgtz","paddle") 
             << "  ADC = " << tree.GetValueI(row,"FTOF1B::dgtz","ADCL") << endl; 
        H1->Fill(tree.GetValueI(row,"FTOF1B::dgtz","paddle"));
      }
    }

    H1->Draw();
  }

For differnet types of variables different get functions have to be used. GetValueI - for 
integer, GetValueF - for floats and GetValueD - for doubles. 
