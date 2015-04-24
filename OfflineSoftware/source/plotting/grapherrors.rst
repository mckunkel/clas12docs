
Graphs with Errors
==================

.. code-block:: java

   import org.root.pad.*;
   
   TCanvas c1 = new TCanvas("c1","My Plots",600,600);
   c1.divide(2,2);

The division of canvas can also be specified in the constructor after size parameters.
Once the canvas is created histograms and graphs can be plotted on the canvas. The data
objects in the Java library are named according to ROOT conventions. To create and draw
a GraphErrors object use:

.. code-block::	     java

   import org.root.pad.*;
   import org.root.data.*;

   TCanvas c1 =	new TCanvas("c1","My Plots",600,600);
   c1.divide(1,1);
   int[] x = [ 1.0, 2.0, 3.0, 4.0];
   int[] y = [ 0.5, 0.7, 0.6, 0.4];

   GraphErrors  graph = new GraphErrors(x,y);
   graph.setTitle("Example Graph");
   graph.setXTitle("Energy [GeV]");
   graph.setYTitle("Efficiency");
   c1.cd(0);
   c1.draw(graph);

The resulting plot looks like:

.. image:: images/jroot-graphexample.png


