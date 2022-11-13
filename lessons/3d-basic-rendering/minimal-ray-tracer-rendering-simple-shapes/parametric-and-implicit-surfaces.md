## Introduction

In the previous lesson we learned how to generate primary rays, but we haven't been able to produce an image yet, because we haven't learned how to compute the intersection of these primary rays with any sort of geometry. In this lesson, we will learn about computing the ray-geometry intersection for simple shapes such as spheres. Spheres are easy to ray-trace which is the reason they are often used by people learning how to program a ray tracer. This lesson will be a little bit of a mismatch of topics:

- In this chapter, we will learn about parametric and implicit surfaces. These surfaces or shapes are special in the sense that they can be defined mathematically. This is the base of the technique we will study in the next chapter to compute their intersection against rays.
- In chapter 2, we will learn about a couple of techniques to compute the intersection of a ray with a sphere.
- In chapter 3, we will put together what we learned in the previous lesson about generating primary rays, as well as what we learned in chapter 2 of this lesson to compute whether primary rays intersect any of the spheres making up the scene. The program we will develop for this lesson will form the base of a minimal but functional ray tracer. We will learn about the `trace()` function. This is where we test each sphere against the primary ray and return the intersection distance, as well as a pointer to the closest intersected sphere (if the ray intersected a sphere at all). In the `castRay()` function we will also learn how to use the normal and texture coordinates at the intersection point to perform some simple shading.
- Finally in chapters 4 and 5, we will learn about various techniques to compute the intersection of a ray with other simple shapes such as planes, disks, and axis-aligned boxes.
- As usual, the last chapter of this lesson contains the full source code of all the programs developed for this lesson (with instructions on how to compile them).

## Ray-Geometry Intersection

In the previous lesson, we learned how to generate primary rays. To produce an image, we now need to learn about the ray-geometry intersection. The idea is to use mathematics to find if a ray intersects an object. As often mentioned in the previous lessons, in CG, geometry or 3D objects can be described in many different ways. We already mentioned polygon meshes (which are made of faces), NURSBs surfaces, subdivision surfaces, etc. though apart from triangular meshes which are a subset of polygon meshes, we haven't yet studied what NURBS and subdivision surfaces are.

The reason these types of geometry are useful in computer graphics is that they are good at describing the shape of complex objects. Though simple shapes such as spheres, planes, disks, or boxes can be rendered directly using much simpler methods. These shapes can indeed be described mathematically, by equations, and we can use these equations to analytically compute whether they are intersected by a ray. This is what we will learn about in this lesson.

How can a shape be defined mathematically? This can be done in generally two different ways: **parametrically** and **implicitly**.

## Parametric Surfaces

![Figure 1: parametric definition of a ray.](/images/ray-simple-shapes/impsurf-ray.png)

If you remember what we said in the previous lesson, rays too can be defined using the following [**parametric equation**](http://en.wikipedia.org/wiki/Parametric_equation):

$$P = O + t D$$

Where \(P\) is a point on the ray half-line, \(O\) is the ray origin, and \(D\) is the ray direction. The term \(t\) is called a parameter. By changing the value of \(t\) we can describe as many points on the ray half-line as we want and the collection of these points forms the half-line itself. In other words, it describes how to generate an ordered sequence of points along the ray. Spheres too can be defined using a parametric form. Here is the parametric equation of a sphere:

$$
\begin{array}{l}
P.x = \cos(\theta)\sin(\phi),\\
P.y = \cos(\theta),\\
P.z = \sin(\theta)\sin(\phi).\\
\end{array}
$$

<details>
The equations to compute Cartesian coordinates from spherical coordinates may vary from source to source. It depends on the convention being used for the naming of the coordinate system axes as well as whether \(\theta\) defines the polar or azimuthal angle. In the example above, y is the up axis, x points to the right and z is in a plane perpendicular to y. \(\theta\) defines the polar angle as shown in figure 2.
</details>

![Figure 2: parametric definition of a sphere.](/images/ray-simple-shapes/impsurf-sphere.png?)

Or: 

$$
\begin{array}{l}
\vec r(\theta,\phi) = (\cos\theta \sin\phi, \cos\theta, \sin\theta \sin \phi), \\
0 \leq \theta < 2\pi, 0 \leq \phi \leq \pi.
\end{array}
$$

Where \(\theta\) and \(\phi\) are the latitude and longitude coordinates of a point on a sphere defined in radians. The angle \(\theta\) (the Greek letter _theta_) is contained in the range [0, \(\pi\)], and than angle \(\phi\) (the greek letter _phi_) is contained in the range [0, \(2\pi\)]. We have already introduced these equations in the lesson on [Geometry](lessons/mathematics-physics-for-computer-graphics/geometry/spherical-coordinates-and-trigonometric-functions). The coordinates \(\theta, \phi\) of a point on a sphere, are also known as [spherical coordinates](http://en.wikipedia.org/wiki/Spherical_coordinate_system). What's important here though, is that a sphere can be described using a set of three equations. In these parametric equations, \(\theta\) and \(\phi\) are the parameters. 3D objects that can be defined using such equations are called **[parametric surfaces](http://en.wikipedia.org/wiki/Parametric_surface)**.

In CG this representation is useful because the two parameters \(\theta\) and \(\phi\) are often denoted \(u\) and \(v\) or \(s\) and \(t\) in the generic case, can be used as the texture coordinates of a point on the 3D surface of the object. In the case of a sphere, we can easily remap the two parameters \(\theta\) and \(\phi\) to the range [0, 1] and use these coordinates to perform a lookup in a texture or generate a pattern using a procedural approach. An example of this technique will be provided in this lesson ([chapter 3](lessons/3d-basic-rendering/minimal-ray-tracer-rendering-simple-shapes/minimal-ray-tracer-rendering-spheres)). In other words, the process can be seen as some sort of mapping from 2D space to 3D space (and we use the 2D coordinates for texturing, as our st coordinates).

In general, what you need to remember about parametric surfaces, is that it requires 1 parameter to describe a curve and two parameters to describe a 3D surface.

## Implicit Surfaces

![Figure 3: implicit form of a circle of radius \(r\).](/images/ray-simple-shapes/impsurf-circle.png?)

Implicit surfaces in a way are very similar to parametric surfaces. To start with, we will be using the example of a circle which in its implicit form is defined by the following equation:

$$x^2 + y^2 = r^2.$$

Where \(x\) and \(y\) are the coordinates of a point in 2D space, and \(r\) is the radius of the circle (figure 1). All it says is that the equation above is true for any points lying in a circle of radius \(r\). In other words, if you take the coordinates of any points on the circle or radius \(r\), raise these coordinates to the power of 2, and sum them up, then you will get a number that is equal to the radius of the circle raised to the power of 2\. Note that in this example the circle is assumed to be centered around the origin. Though you can generalize this equation to circles with arbitrary center positions:

$$(x^2 - O_x^2) + (y^2 - O_y^2) = r^2.$$

Where \(O\) is the circle center position. All this equation does though is just "move" the center of the circle to the origin. The equation above is the implicit equation of a circle. The concept can easily be extended to 3D. The implicit equation of the sphere is as follows:

$$x^2 + y^2 + z^2 = r^2.$$

The concept is the same. The equation will be true for all points lying on the sphere of radius \(r\).

![Figure 4: using a sphere as a bounding volume to test whether a ray may intersect the enclosed geometry. If the ray doesn't intersect the sphere, then we know for sure that it can't intersect the object.](/images/ray-simple-shapes/impsurf-bounding-sphere.png?)

In this lesson, we will learn how these equations can be used to test the intersection of a ray with an implicit surface.

Many shapes can be defined with such equations as planes, spheres, cones, tori, etc. You might think that these shapes are not very useful to describe the shape of complex objects and this would be true. This is what we said in the introduction of this lesson. Rendering spheres might be useful for testing your program but are limited indeed. But they can be useful in other contexts. For example, spheres can be used to represent the overall volume of an object, **as a bounding volume** (figure 4). If the enclosed object is very complex, testing if a ray intersects this object is likely to be computationally expensive. What we can do first is check if the ray intersects the bounding sphere. If it doesn't then we know for sure that the ray can't intersect the enclosed object, which saves us the time it would have taken to test the intersection with the object itself. This method is only advantageous if the time it takes to compute the ray-sphere intersection is less than the time it takes to compute the intersection of the ray with the enclosed object of course. The good news is the cost of an intersection test between a ray and an implicit surface is often less than a ray-triangle intersection test for example. If the scene contains many such complex objects, this simple ray-geometry acceleration method can already save us a lot of computation time. In conclusion, learning about implicit surfaces and how they can be used to compute ray-geometry intersections is still very useful despite the simplicity of their shapes.

<details>
![](/images/ray-simple-shapes/blobbies.png)
Implicit surfaces and Blobies: one class of implicit surfaces is very interesting. They are called by many names in the CG literature: blobies, metaballs, etc. You can see blobies as little spheres which influence each other. If you can get two blobies close to each other then their surface will start to blend in the middle to form a larger blob. Blobies were a very popular modeling technique in the 1980s-1990s. They are very good to model organic shapes. Blobies can't be ray-traced directly easily. They often require to be converted to a polygon mesh first. A lesson on blobies can be found in the modeling section.
</details>

## Why Are these Surfaces Useful in Ray-Tracing

The topic of this lesson is to study how the property of being defined with an equation can be used to compute the ray-geometry intersection test. Parametric surfaces are less useful than implicit surfaces to compute this ray-geometry intersection test, but they are useful to compute the texture coordinates of a point lying on the surface of an implicit object (as explained in the next chapter). Thus knowing about both representations is still useful and needed. They are useful for the reason we already mentioned above:

- The solution to a ray-implicit surface is often simpler to compute than with another type of geometry.
- The intersection of a ray with an implicit surface is often faster to compute than with another geometry type.
- The shapes are too simple to represent the shape of complex objects, but they can be used as bounding volumes for instance which in turn can be used to accelerate ray-geometry intersection testing.
- The ray-implicit surface intersection test is an example of practical use of mathematical concepts such as computing [the roots of a quadratic equation](http://en.wikipedia.org/wiki/Quadratic_equation#Solving_the_quadratic_equation).

In this lesson, we will learn about the ray-sphere, ray-plane, ray-disk (which is an exertion of the ray-plane case), and ray-box intersection test. The sphere belongs to a category of surfaces called **quadrics**. Any quadrics (cones, torus, etc.) can be tested against a ray using the same solution as the solution we will describe for the sphere.

## Integrate and Differentiate: Derivatives, Tangents, and Normal Vectors

Another advantage of using parametric or implicit surfaces is that the equations that define the shape of these surfaces can be used to compute other useful values such as the derivatives, the tangent, the bi-tangent, and the normal at any given point on the surface. We already know about normals and the role they play in shading. Derivatives at the surface of a point are also important for things such as texture filtering. Tangent and bi-tangent can also be used to form a local coordinate system at any given point on the surface of the object which is useful in shading. The mathematics involved in computing these values can be significantly more complex than the mathematics used in computing the intersection of a ray with an implicit surface and won't be studied in this lesson. If you are interested in this topic, you can search for differential geometry on the Web. A separate lesson will be devoted to this topic alone. We will use the normal in the next lessons, but shapes such as spheres and triangles, are simpler methods to compute the normal than using techniques from differential geometry.

## About Ray-Tracing Spheres and Writing a Production Quality Ray-Tracer

The method we will learn about in this lesson to compute the intersection of a ray with a sphere is different than the method we will study in the next lesson to compute the ray-triangle intersection. As mentioned in a previous lesson, generally we want to avoid having to support several types of geometry in a renderer for lots of different reasons. First, because it requires writing more code but more importantly because every feature supported by the program (motion blur, displacement, texture mapping, acceleration structure, etc.) needs to work with each supported geometry type which puts an additional constraint on the programmer. So, it is generally easier to support only one geometry type (triangle is the most common choice) and convert other geometry types to that type instead. Most production renderers provide a way of rendering simple shapes such as spheres, tori, etc., though the way they handle it internally is by converting these shapes to polygon meshes (generally using these shapes' parametric representation) rather than implementing a ray-geometry intersection routine specific to spheres and tori (we will learn how to convert a parametric surface to a polygon mesh in the lesson Ray-Tracing: [Ray-Tracing a Polygon Mesh](lessons/3d-basic-rendering/ray-tracing-polygon-mesh)). The reality is that, in production, you rarely render spheres anyway. Though this is not the approach we will use in this lesson. We will use the old-fashion or say it differently, the native way of ray-tracing these shapes. Of course, the advantage of the method we will learn about in this lesson is that it doesn't require a conversion to a polygon mesh and that rendering a sphere using equations is much faster than ray-tracing a sphere made of 100, 1.000, or 10,000 triangles. Learning how to ray-trace spheres or quadric surfaces is good as an exercise, but practically, this is generally not the way it is done in a production quality renderer.