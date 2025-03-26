# Basics

A **Kernel** is a grid of **Thread Blocks**. Each block contains multiple threads, and the GPU hardware groups every 32 threads into a **Warp**.

- Number of blocks per grid : Grid Size
- The number of blocks in a grid should be larger than the number of multiprocessors so that all multiprocessors have at least one block to execute.
- Furthermore, there should be multiple active blocks per multiprocessor so that blocks that aren’t waiting for a __syncthreads() can keep the hardware busy. This recommendation is subject to resource availability; therefore, it should be determined in the context of the second execution parameter—the number of threads per block, or block size— as well as shared memory usage.
- 