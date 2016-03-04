
Histogram Plotting
==================

Histogram Drawing
-----------------

.. code-block:: java

   import org.root.pad.*;
   import org.root.histogram.*;
   import org.root.func.*;
   import java.lang.Math;

   // Define two histograms with Title string
   H1D h1 = new H1D("h1","Random Function (Gaus + POL2)",200,0.0,14.0);
   H1D h2 = new H1D("h2","Second Random Function",200,0.0,14.0);


   // Set X and Y titles for both histograms
   h1.setXTitle("Random Generated Function");
   h1.setYTitle("Counts");

   h2.setXTitle("Random Generated Function");
   h2.setYTitle("Counts");

   // Define a function with gaus and polynomial and set parameters
   F1D f1 = new F1D("gaus+p2",0.0,14.0);

   f1.setParameter(0,120.0);
   f1.setParameter(1,  8.2);
   f1.setParameter(2,  1.2);
   f1.setParameter(3, 24.0);
   f1.setParameter(4,  7.0);

   // Declare a random number generator based on the function
   // and fill the histograms with randomly generated numbers

   RandomFunc rndm = new RandomFunc(f1);

   for(int i = 0; i < 34000; i++){
      h1.fill(rndm.random());
      if(i%2==0) h2.fill(rndm.random());
   }

   // Define a canvas with 1 column and two rows
   TGCanvas c1 = new TGCanvas("c1","JROOT Demo",900,800,1,2);

   // Set up the drawing properties for histograms
   // Color scheme is similar to ROOT:
   // 1 - black, 2 - red, 3 - green, 4 -blue .... 
   h1.setLineWidth(2);
   h1.setFillColor(4);

   h2.setLineWidth(2);
   h2.setLineColor(4);
   h2.setFillColor(3);

   // Go to first pad and draw histogram with default
   // axis divisions for X and Y axis.
   c1.cd(0);
   c1.draw(h1);

   // Go to second pad, change the axis divisions for 
   // the pad and draw the histogram.

   c1.cd(1);
   c1.getCanvas().setDivisionsX(8);
   c1.getCanvas().setDivisionsY(5);
   c1.draw(h2);

   
.. image:: images/jroot-new-histogram-axis.png


2D Histograms
-------------


.. code-block:: java

   import org.root.pad.*;
   import org.root.histogram.*;
   import org.root.func.*;
   import java.lang.Math;

   TGCanvas c1 = new TGCanvas("c1","JROOT Demo",900,800,2,3);
   //c1.setFontSize(14);

   H1D h1 = new H1D("h1","Random Function (Gaus + POL2)",200,0.0,14.0);
   H2D h2 = new H2D("h2",14,0.0,14.0,120,0.0,14.0);

   h1.setXTitle("Random Generated Function");
   h1.setYTitle("Counts");

   h2.setTitle("SECTOR 1 OCCUPANCY");
   h2.setXTitle("TIME-OF-FLIGHT");
   h2.setYTitle("PADDLES");

   F1D f1 = new F1D("gaus+p2",0.0,14.0);
   f1.setParameter(0,120.0);
   f1.setParameter(1,  8.2);
   f1.setParameter(2,  1.2);
   f1.setParameter(3, 24.0);
   f1.setParameter(4,  7.0);

   RandomFunc rndm = new RandomFunc(f1);

   for(int i = 0; i < 840000; i++){
      h2.fill(rndm.random(),rndm.random());
   }


   for(int p = 0; p < 6; p++){
     c1.cd(p);
     if(p<3) c1.setLogZ();
     c1.draw(h2);
   }

.. image:: images/jroot-new-histogram2d.png
