## Towards Interactive and Realtime Ray Tracing

> Ray tracing isn't too slow; computers are too slow. (Kajiya 1986)

In the 1980s, even though ray tracing was considered as a better method over scanline algorithms to generate photo-realistic images, it was also known for being much slower. But as Kajiya pointed out, ray tracing is not slow. Computers are. However the good news, and that is certainly why we hear about ray tracing more and more since at least the early 2000's, computers are now fast enough (thanks to parallel computing in particular) to even ray trace certain scenes in real time. However, beside using better hardware, accelerating ray tracing in any possible ways is still necessary for achieving such performances and the most three commons ways for doing so are accelerating the ray-geometry itself, using acceleration structures such as those we presented in this lesson (to reduce the number of objects to be checked against rays) and tracing continuous bundles or rays (a technique we will talk about in another lesson).

In this lesson we have presented an "incomplete" version of a BVH (incomplete because we are yet to learn how to insert the triangles rather than the objects). The idea of grouping objects under a hierarchy of bounding volumes (extents) is almost as old as ray tracing itself (Rubin and Whitted, 1980). We have also presented a simple implementation of the grid acceleration structure which we will be using for the rest of the basic section. Other algorithms exist most notably **Octrees** (Glassner, 1984), **BSP trees** (Binary Space Partitioning, Fuchsetal.1980) and **K-d trees**. In order to more quickly complete the other lessons of this section, we won't detail these techniques in this version of the lesson (you can find information on Octrees and K-d trees in the following sections). The main advantage of these techniques over grids is mainly that they address the teapot in a stadium problem by partitioning space more finely around objects.

## What is a Good Acceleration Structure?

We mentioned before that grids are suffering from the teapot in the stadium problem. However as we also pointed out, a good acceleration structure, is a structure which gives the best possible performances for wild range of scenes. Every acceleration structure method has it pros and cons, but generally, BVH are considered to this day, to perform best compared to other methods. Several factors should be taken into account to judge the advantages of a given structure: the memory cost associated with it, the time it takes to build this structure (which is important if we aim for interactive ray tracing. K-d trees for example are slower to build than grids), the complexity (vs simplicity) and robustness of the algorithm, how it performs under different scene configurations (no pathological case such as the teapot in the stadium problem), and more importantly of course, the speed-ups it provides compared to a naive ray tracing implementation and other acceleration structures. Some models also require the user to tweak some control parameters (for example the \(\lambda \) parameter in the grid acceleration structure) which is not always considered as a good thing. Comparing acceleration structures is also difficult because the performance very much depend on the quality of the implementation itself.

## Pros and Cons

What are the pros and cons of acceleration structures? As you can observe, the code necessary to support acceleration structures is probably the longest and most complicated we had to write so far. The first cons is certainly that they introduce a great deal of complexity to the ray tracing algorithm which is originally very simple. Creating the acceleration structure can also take a significant amount of time (particularly if your goal is to reach interactive or real-time frame rates) as well as consume a lot memory.

## The Future

Acceleration structures have been the center of a massive amount of research in the computer graphics community. Even though the strengths and weaknesses of existing methods are now well studied and very well understood, this research is still very much alive today, particularly as the needs for interactive or real time ray tracing becomes more important. A lot of the current research tends to improve existing methods sometimes within some particular contexts (such as for example developing an acceleration structure that is efficient for animated scenes) however the trend in this area at the moment is almost to look at the problem from a very different angle. Recent research tends to focus on pointing out that acceleration structures which consume a lot of memory and are slow to build don't need to be created upfront but can be somehow constructed on the fly as rays are traversing space. The Divide-and-Conquer Ray Tracing algorithm (DACRT) introduced by Mora (2011) for instance, recursively subdivide groups of rays and objects together. Rather than treating one ray at a time, in this method, packets of rays are tested against groups of objects which, in a parallel computing environment, can provide drastic speedups for a fraction of the memory cost compared to traditional acceleration structures. It is likely that future renderers will have a very different architecture to those built on Fujimoto, Glassner and Kajiya's ideas.

## Source Code

You can download the full source code for this lesson in the next chapter.

## References

_A 3-Dimensional Representation for Fast Rendering of Complex Scenes_, Rubin and Whitted, 1980.

_A Parallel Ray Tracing Computer_, Cleary et al., 1983.

_A Fast Voxel Traversal Algorithm for Ray Tracing_, Amanatides, 1983.

_Ray Tracing Complex Scenes_, Kay and Kajiya, 1986.

_Accelerated Ray Tracing_, Fujimoto, 1985.

_ARTS: Accelerated Ray Tracing Systems_, Fujimoto, 1986.

_A Survey of Ray Tracing Acceleration Techniques_, Arvo and Kirk, 1989.

_Impoved Ray Tagging for Voxel-Based Ray Tracing_, Dirk and Arvo, 1991.

_Ray Tracing Animated Scenes using Coherent Grid Traversal_, Wald et al., 2006