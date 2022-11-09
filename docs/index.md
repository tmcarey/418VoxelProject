<!-- Output copied to clipboard! -->

<!-----

Yay, no errors, warnings, or alerts!

Conversion time: 0.28 seconds.


Using this Markdown file:

1. Paste this output into your source file.
2. See the notes and action items below regarding this conversion run.
3. Check the rendered output (headings, lists, code blocks, tables) for proper
   formatting and use a linkchecker before you publish this page.

Conversion notes:

* Docs to Markdown version 1.0β33
* Tue Nov 08 2022 21:33:49 GMT-0800 (PST)
* Source doc: Project Proposal
----->


Thomas Carey

Title: Voxel Octree Engine

URL: https://418-project.s3.amazonaws.com/index.html

Summary: I am going to build a ray-traced voxel game engine which uses acceleration data structures and the tools we have learned in this class to efficiently render a game world which can be dynamically lit and manipulated.

Background: The project will involve designing a voxel data representation and a rendering pipeline that will allow us to efficiently render (using per-pixel rays) and interact with a complex game world. The most appealing candidate for the data structure is an Octree, which will allow us to most efficiently search the 3D space and compress the voxel data in larger non-leaf nodes. However, since we also want to be able to manipulate and interact with the world space in interesting ways, and octrees alone are not extremely well suited to parallel operations, we may need additional data structures and tricks to allow this to take place.

For each pixel we will fire a ray from the camera, and traverse the octree (across nodes and vertically in the tree) starting from the root node until we reach a leaf or exit the tree altogether. Once we hit a leaf we can report the necessary color/lighting data. Since each pixel can work entirely independently, this process is extremely parallelizable across CUDA cores, and could even work in real time on some GPUs. Once we have a function which can shoot a ray within the world, we can call this many times to collect light and shadow information for each pixel on the world’s surface.The data structure and algorithm for this ray traversal will be inspired by the NVIDIA paper [Efficient Sparse Voxel Octrees](https://research.nvidia.com/sites/default/files/pubs/2010-02_Efficient-Sparse-Voxel/laine2010i3d_paper.pdf). In short, we keep the octree axis-aligned (rotate the ray so that it matches the octree’s axes), and use a 3-bit mask to track the child index, updating incrementally as we traverse the tree. 

The NVIDIA paper outlines a reasonable strategy for static octrees, but we will need to change both the algorithm and data structure to support efficient read and write operations to the world data. Additionally we will need to come up with a clever storage method to keep the octree size reasonable and within memory limits.

Challenge: There are three main challenges we are addressing in this project: 



* Rendering: We need to be able to shoot rays through the scene extremely efficiently, as we will need to collect lighting information for every single pixel.
* Manipulating+Updating the Octree: We want to be able to perform updates on the world, such as destroying or constructing large portions of it. These operations should also be efficient, and thus we will need to find efficient parallel algorithms to perform these operations. I suspect these algorithms will be better suited on the CPU as Octrees are not very parallelizable to large process counts. Thus additionally we will need to keep the GPU updated with the latest version of the world data from the CPU, so we will need to manage bandwidth and memory latency as well. 
* Memory Usage (less important/stretch goal): As the world size grows, the size of the octree grows very quickly. In order to render larger, more interesting worlds we will need to be clever with the actual information we store in our octree. Additionally we may explore streaming algorithms so that we don’t have to store the entire world in device memory at once.

Resources: The NVIDIA paper will help with the basic traversal algorithm and will inspire the starting of our data structure and codebase. Additionally I have attempted (not very successfully) to build a rendering system inspired by this NVIDIA paper in the past and that code may help me get started. I have a very powerful computer in my living space so I can use that for incremental testing rather than staying SSH-ed in lab computers. 

 \
Goals/Deliverables: A high quality renderer which takes a location and rotation of the camera and a binary file which describes the world. It will output a globally illuminated image.

Additionally, I would like to include a real-time renderer (with a lower sample count) which can run on high-end GPUs at around 30fps.

We should be able to generate octree-worlds with some parameters (perhaps just a surface with varied height according to some noise function), as well as set off large “bombs” which can destruct the world. This will test our ability to efficiently update the octree. 

As a stretch goal, it would be cool to be able to render and simulate a large number of physically-enabled particles being poured over the octree surface. 

Platform Choice: Since this is a visual rendering project we will need a very large number of parallel work units, which only a GPU can give us. Since we are using an octree representation, there may be some operations which are better suited for the CPU, so a mix of parallel programming on both devices will be in use. 

Schedule:

	Week 1: Be able to trace rays on the octree in CUDA with the basic algorithm

	Week 2: Be able to render images and generate extremely simple worlds, improve ray performance so we can do reasonably non-noisy illumination

	Week 3: Generate larger and more complex worlds, runtime editing of the world.

	Week 4: Large edits which restructure the tree, multiple bounce lighting

	Week 5: Reserved for polishing, attempt particle stretch goal if possible

