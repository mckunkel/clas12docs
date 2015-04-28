
Geometry Container Classes
**************************

Container classes are more complex objects that which can be either
surfaces or closed objects. They are used to construct detector components
and contain algorithms that can track intersection with line objects.


Triangle3D and Shape3D
======================

Triangle3D object is surface defined by 3 points in space. 

.. code-block:: java

   import org.jlab.geom.*;
   import org.jlab.geom.prim.*;

   Triangle3D tri = new Triangle3D(  0.0, 0.0, 0.0, // first point
                                     1.0, 0.0, 0.0, //second point
				     0.0, 0.0, 1.0 ); // third point
   tri.show();

This code will create a triangle in Y-Z plane. 

Output:

.. code-block:: bash

   Triangle3D:	( 0.00000  0.00000  0.00000) ( 1.00000  0.00000  0.00000) ( 0.00000  0.00000  1.00000)

Shape3D object is a collection of Triangle3D objects. It is created by adding triangles to the shape, 
and transformation object can be applied to the entire shape. In the following example we will crate
a box with size 2, at the origin (bottom of the box on YZ plane at X=0), then translate it along
Z axis by 8 units. 

.. code-block:: java

   //****************************************************
   //  BOX object
   //****************************************************
   // Creating a BOX with side=2
   Shape3D  box = new Shape3D();
   // Bottom Square at X=0 plane
   box.addFace(new Triangle3D( 0.0, 1.0, 1.0, 0.0,-1.0, 1.0, 0.0,-1.0,-1.0));
   box.addFace(new Triangle3D( 0.0, 1.0, 1.0, 0.0, 1.0,-1.0, 0.0,-1.0,-1.0));
   // Top Square at X=2 plane
   box.addFace(new Triangle3D( 2.0, 1.0, 1.0, 2.0,-1.0, 1.0, 2.0,-1.0,-1.0));
   box.addFace(new Triangle3D( 2.0, 1.0, 1.0, 2.0, 1.0,-1.0, 2.0,-1.0,-1.0));
   // Side face XY-Plane at Z=1
   box.addFace(new Triangle3D( 0.0, 1.0, 1.0, 2.0, 1.0, 1.0, 2.0,-1.0, 1.0));
   box.addFace(new Triangle3D( 0.0, 1.0, 1.0, 2.0,-1.0, 1.0, 0.0,-1.0, 1.0));
   // Side face XY-Plane at Z=-1
   box.addFace(new Triangle3D( 0.0, 1.0,-1.0, 2.0, 1.0,-1.0, 2.0,-1.0,-1.0));
   box.addFace(new Triangle3D( 0.0, 1.0,-1.0, 2.0,-1.0,-1.0, 0.0,-1.0,-1.0));
   // Side face XZ plane  Y=1
   box.addFace(new Triangle3D( 0.0, 1.0, 1.0, 0.0, 1.0,-1.0, 2.0, 1.0,-1.0));
   box.addFace(new Triangle3D( 0.0, 1.0, 1.0, 2.0, 1.0, 1.0, 2.0, 1.0,-1.0));
   // Side face XZ plane  Y=-1
   box.addFace(new Triangle3D( 0.0,-1.0, 1.0, 0.0,-1.0,-1.0, 2.0,-1.0,-1.0));
   box.addFace(new Triangle3D( 0.0,-1.0, 1.0, 2.0,-1.0, 1.0, 2.0,-1.0,-1.0));
   box.show();

   Transformation3D  transform = new Transformation3D();
   transform.translateXYZ(0.0,0.0,8.0);
   transform.apply(box);

   box.show();

Output:

.. code-block::	bash

   Shape3D:
	Triangle3D:	(     0.00000      1.00000      1.00000) (     0.00000     -1.00000      1.00000) (     0.00000     -1.00000     -1.00000)
	Triangle3D:	(     0.00000      1.00000      1.00000) (     0.00000      1.00000     -1.00000) (     0.00000     -1.00000     -1.00000)
	Triangle3D:	(     2.00000      1.00000      1.00000) (     2.00000     -1.00000      1.00000) (     2.00000     -1.00000     -1.00000)
	Triangle3D:	(     2.00000      1.00000      1.00000) (     2.00000      1.00000     -1.00000) (     2.00000     -1.00000     -1.00000)
	Triangle3D:	(     0.00000      1.00000      1.00000) (     2.00000      1.00000      1.00000) (     2.00000     -1.00000      1.00000)
	Triangle3D:	(     0.00000      1.00000      1.00000) (     2.00000     -1.00000      1.00000) (     0.00000     -1.00000      1.00000)
	Triangle3D:	(     0.00000      1.00000     -1.00000) (     2.00000      1.00000     -1.00000) (     2.00000     -1.00000     -1.00000)
	Triangle3D:	(     0.00000      1.00000     -1.00000) (     2.00000     -1.00000     -1.00000) (     0.00000     -1.00000     -1.00000)
	Triangle3D:	(     0.00000      1.00000      1.00000) (     0.00000      1.00000     -1.00000) (     2.00000      1.00000     -1.00000)
	Triangle3D:	(     0.00000      1.00000      1.00000) (     2.00000      1.00000      1.00000) (     2.00000      1.00000     -1.00000)
	Triangle3D:	(     0.00000     -1.00000      1.00000) (     0.00000     -1.00000     -1.00000) (     2.00000     -1.00000     -1.00000)
	Triangle3D:	(     0.00000     -1.00000      1.00000) (     2.00000     -1.00000      1.00000) (     2.00000     -1.00000     -1.00000)
   Shape3D:
	Triangle3D:	(     0.00000      1.00000      9.00000) (     0.00000     -1.00000      9.00000) (     0.00000     -1.00000      7.00000)
	Triangle3D:	(     0.00000      1.00000      9.00000) (     0.00000      1.00000      7.00000) (     0.00000     -1.00000      7.00000)
	Triangle3D:	(     2.00000      1.00000      9.00000) (     2.00000     -1.00000      9.00000) (     2.00000     -1.00000      7.00000)
	Triangle3D:	(     2.00000      1.00000      9.00000) (     2.00000      1.00000      7.00000) (     2.00000     -1.00000      7.00000)
	Triangle3D:	(     0.00000      1.00000      9.00000) (     2.00000      1.00000      9.00000) (     2.00000     -1.00000      9.00000)
	Triangle3D:	(     0.00000      1.00000      9.00000) (     2.00000     -1.00000      9.00000) (     0.00000     -1.00000      9.00000)
	Triangle3D:	(     0.00000      1.00000      7.00000) (     2.00000      1.00000      7.00000) (     2.00000     -1.00000      7.00000)
	Triangle3D:	(     0.00000      1.00000      7.00000) (     2.00000     -1.00000      7.00000) (     0.00000     -1.00000      7.00000)
	Triangle3D:	(     0.00000      1.00000      9.00000) (     0.00000      1.00000      7.00000) (     2.00000      1.00000      7.00000)
	Triangle3D:	(     0.00000      1.00000      9.00000) (     2.00000      1.00000      9.00000) (     2.00000      1.00000      7.00000)
	Triangle3D:	(     0.00000     -1.00000      9.00000) (     0.00000     -1.00000      7.00000) (     2.00000     -1.00000      7.00000)
	Triangle3D:	(     0.00000     -1.00000      9.00000) (     2.00000     -1.00000      9.00000) (     2.00000     -1.00000      7.00000)


The shape object can also calculate it's intersections with Line3D object. Because Shape3D is a collection of faces, there can be more
than one intersection. 

Intersections of Shapes with Lines
----------------------------------

The Shape3D object can detect intersections of any of it's faces with line 3D object, there are three modes of intersection.
The line can be threated as an infinite line, as a ray or a segment. Here is an example code showing how to check for intersection.

.. code-block::	java

   Line3D line = new Line3D(0.0,0.0,0.0, 0.0, 0.0, 5.0);

   System.out.println("Line    Intersection : " + box.hasIntersection(line));
   System.out.println("Ray     Intersection : " + box.hasIntersectionRay(line));
   System.out.println("Segment Intersection : " + box.hasIntersectionSegment(line));


Output:

.. code-block::	bash

   Line    Intersection : true
   Ray     Intersection : true
   Segment Intersection : false

Since the line ends at Z=5, and we moved the box to Z=8, the first face in XY plane is at Z=7, so infinite line and a ray will
intersect with the box, but the segment will not. After checking the if intersection exists, the intersection points can also 
be retrieved from the box class.

.. code-block::	java

   Line3D segment = new Line3D(0.0,0.0,0.0, 0.0, 0.0, 8.0);

   ArrayList<Point3D> ip = new ArrayList<Point3D>();
   // Get intersection as RAY
   box.intersectionRay(segment,ip);
   System.out.println("INTERSECTION POINTS FOR RAY : ");
   for(Point3D point : ip){
      System.out.println(point);
   }

   ip.clear(); // clear the points array
   box.intersectionSegment(segment,ip);
   System.out.println("INTERSECTION POINTS FOR SEGMENT : ");
   for(Point3D point : ip){
      System.out.println(point);
   }


Output:

.. code-block::	java

	INTERSECTION POINTS FOR RAY : 
	Point3D:          0.00000      0.00000      9.00000
	Point3D:          0.00000      0.00000      7.00000
	
	INTERSECTION POINTS FOR SEGMENT : 
	Point3D:          0.00000      0.00000      7.00000

As before, this line extends only to Z=8 and the face of the box at Z=9 in XY plane will not intersect with the
line segment, however it does intersect if the line is treated as a ray.

