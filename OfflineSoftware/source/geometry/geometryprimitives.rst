
Geometry Library Primitives
***************************

Geometry library developed for CLAS12 is full geometrical primitives library that
allows for detector representation.

Points and Lines
================



.. code-block:: java

   import org.jlab.geom.*;
   import org.jlab.geom.prim.*;

   Point3D p1 = new Point3D( 0.0, 0.5,  0.5);
   Point3D p2 = new Point3D( 0.5, 0.5, -0.5);

   System.out.println(p1);
   System.out.println(p2);
   System.out.println(" distance =  " + p1.distance(p2));

Output:

.. code-block:: bash

   Point3D:	     0.00000      0.50000      0.50000
   Point3D:	     0.50000      0.50000     -0.50000

   distance =  1.118033988749895

Points can be used to construct lines in 3D. Line3D object also can calculate
distance between two lines (considering them infinite), and returns Line3D object
that connects the points on the initial lines at their closest approach. The length
of the line will represent the closest distance between the lines and midpoint is
the middle point of the closest approach.

.. code-block:: java

   Line3D  line1 = new Line3D( p1, p2);
   Line3D  line2 = new Line3D( -1.5, 0.0, 0.0, 1.5, 0.0, 0.0);
   System.out.println(line1);
   System.out.println(line2);
   // returns line connecting point of closest approach
   // of two lines.
   Line3D  line3 = line1.distance(line2); 
   System.out.println(line3);
   System.out.println("CLOSEST APROACH  : " + line3.midpoint());
   System.out.println("CLOSEST DISTANCE : " + line3.length());

Output:

.. code-block:: bash

   Line3D:
        Point3D:       0.00000      0.50000      0.50000
        Point3D:       0.50000      0.50000     -0.50000
   Line3D:
	Point3D:   -1.50000      0.00000      0.00000
	Point3D:    1.50000      0.00000      0.00000
   Line3D:
	Point3D:    0.25000      0.50000      0.00000
	Point3D:    0.25000      0.00000      0.00000

   CLOSEST APROACH  : Point3D:	       0.25000      0.25000      0.00000
   CLOSEST DISTANCE : 0.5


   
Transformations
===============

Transformation object is describing transformation in space for any geometrical primitives class
(such as Point3D, Line3D and Plane3D). It is constructed by adding different transformations in 
sequence to the object (such as translations and rotations). Every geometrical primitive can be 
transformed by Transformation3D.apply(GeometryObject) method. Example:

.. code-block:: java

   Point3D p4 = new Point3D( 0.7, 0.7, 0.7);
   Transformation3D  transform = new Transformation3D();
   transform.translateXYZ(2.0,3.0,4.0).rotateZ(Math.toRadians(30.0));
   transform.show(); // print transformation after two added
   transform.rotateY(Math.toRadians(45.0));
   transform.show(); // print after adding rotation in Y
   System.out.println("BEFORE TRANSFORM : " + p4);
   transform.apply(p4); // apply transformation to the point
   System.out.println("AFTER  TRANSFORM : " + p4);
   // Now after the transformation we can apply an
   // inverse transformation to bring point to it's
   // original position
   Transformation3D  invtrans = transform.inverse();
   invtrans.apply(p4);
   System.out.println("INVERSE  TRANSFORM : " + p4);

Output:

.. code-block:: bash

   Transformation3D::
	Translate:	(2.0, 3.0, 4.0)
   	Rotate-Z: 	0.5235987755982988 rad

   Transformation3D::
	Translate:	(2.0, 3.0, 4.0)
	Rotate-Z: 	0.5235987755982988 rad
	Rotate-Y: 	0.7853981633974483 rad

   BEFORE TRANSFORM   : Point3D:    0.70000      0.70000      0.70000
   AFTER  TRANSFORM   : Point3D:    3.66866      4.55429      2.97814
   INVERSE  TRANSFORM : Point3D:    0.70000      0.70000      0.70000

Inverse method does not inverse the Transformation3D object, it returns a new object with inverted transformation.
