# wire_skin
A python script to construct a simple skin around a wire frame mesh in Blender.

```
+-----+     #=====#   (My ASCII art sucks,
|     |  => ||   ||    but you get the picture)
+-----+     #=====#
```

Non-ASCII version:

![Screenshot](documentation/wire_skin_demo1.png?raw=true "Screenshot")

## Motivation
Blender has a skin modifier that does great things for a lot of meshes. It doesn't seem to do the right thing for some of the sorts of meshes I want 3D printed (usually the caps where the vertices are have an asymmetry that is displeasing to me). The meshes I am trying to create are typically geometric cages for presenting other 3D objects.

This script is meant to address this for me. It certainly isn't a general purpose skinning script, and doesn't pretend to be -- it will likely fail if your input mesh doesn't match a use case similar to mine. I hope the script is useful to you, but really the Blender skin modifier should be more than adequate for most purposes.

Play around with the demo blend file before adapting the script (imported as a module) into your project.

## That's great Chris, but how does it work?

We break the problem down into two parts: calculating "VertCaps" for each vertex in the input mesh, and joining the VertCaps using profile connectors.

![Screenshot](documentation/wire_skin_demo2.png?raw=true "Screenshot")

Each VertCap can be decomposed further. The vertices that are needed to make a VertCap are comprised of a couple of "poles" that stradle either side of the concavity of the vertex, and some profile shapes (at this stage of development, rectangles) that go around each edge connected to the vertex.

![Screenshot](documentation/wire_skin_demo3.png?raw=true "Screenshot")

Next, faces connect these vertices. These are broken into two groups: the faces (triangles) that connect the profiles to a pole, and strips of faces that run between the profiles that connect the pole (a triangle, a quad, and another triangle).

![Screenshot](documentation/wire_skin_demo4.png?raw=true "Screenshot")

At this point, the VertCap is complete.

Quads connect the VertCaps together along edges, and this is pretty straight forward.

## Okay, but how do I control this thing?

Well, take a look at the demo file. The `WireSkin` object generates a mesh when it's `create_mesh()` method is called. The object is initialized with a mesh as input, and a number of keyword arguments:

* `dist`: distance between each vertex in the input mesh and the profile vertices described above.
* `width`: the width of the rectangular profiles.
* `height`: the height of the rectangular profiles.
* `outside_radius`: the distance between a vertex in the base mesh and a outside pole vertex in a VertCap
* `inside_radius`: the distance between a vertex in the base mesh and a inside pole vertex in a VertCap.

A couple of cryptic illustrations should make this clear:

![Screenshot](documentation/wire_skin_demo5.png?raw=true "Screenshot")
![Screenshot](documentation/wire_skin_demo6.png?raw=true "Screenshot")

But wait, there's more! ...

* `crease`: this sets the subsurf edge creases for edges that surround the holes in the output mesh. Set this to a float between `0.0` and `1.0` or `None`.

![Screenshot](documentation/wire_skin_demo_crease_0.0.png?raw=true "Screenshot")
![Screenshot](documentation/wire_skin_demo_crease_1.0.png?raw=true "Screenshot")
