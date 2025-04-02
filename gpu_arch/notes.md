# Basics

A **Kernel** is a grid of **Thread Blocks**. Each block contains multiple threads, and the GPU hardware groups every 32 threads into a **Warp**.

- Number of blocks per grid : Grid Size
- The number of blocks in a grid should be larger than the number of multiprocessors so that all multiprocessors have at least one block to execute.
- Furthermore, there should be multiple active blocks per multiprocessor so that blocks that aren’t waiting for a __syncthreads() can keep the hardware busy. This recommendation is subject to resource availability; therefore, it should be determined in the context of the second execution parameter—the number of threads per block, or block size— as well as shared memory usage.
- Multiple concurrent blocks can reside on a multiprocessor, so occupancy is not determined by block size alone.
- Improving occupancy generally does not translate to a similar increase in performance. A lower occupancy kernel will have **more registers** available per thread than a higher occupancy kernel, which may result in less register spilling to local memory. Typically, once an occupancy of 50 percent has been reached, additional increases in occupancy do not translate into improved performance.
- The CPU sends the kernel launch request to the GPU driver/runtime.

The GPU schedules the kernel for execution on its Streaming Multiprocessors (SMs).

The kernel is broken down into grids of thread blocks.
Grid: A collection of Thread Blocks (TBs).

Thread Block: A group of threads that run together and share shared memory.The GPU has multiple SMs (e.g., 84 SMs in an RTX 3090).

The Grid is split into Thread Blocks that are assigned to different SMs.

Each SM processes multiple Thread Blocks based on available resources (registers, shared memory, etc.).

More SMs = More parallel execution of thread blocks.

A Thread Block does not migrate—it stays on one SM until all its threads finish execution. Inside each SM, a Thread Block is broken into Warps.All threads in a warp execute the same instruction at the same time (SIMT execution model).

If threads in a warp diverge (e.g., due to an if-else statement), performance is reduced.Each SM has multiple CUDA cores (ALUs) where warps execute.