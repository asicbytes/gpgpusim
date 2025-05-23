# Basics

A **Kernel** is a grid of **Thread Blocks**. Each block contains multiple threads, and the GPU hardware groups every 32 threads into a **Warp**.

- Number of blocks per grid : Grid Size
- The number of blocks in a grid should be larger than the number of multiprocessors so that all multiprocessors have at least one block to execute.
- Furthermore, there should be multiple active blocks per multiprocessor so that blocks that aren’t waiting for a __syncthreads() can keep the hardware busy. This recommendation is subject to resource availability; therefore, it should be determined in the context of the second execution parameter—the number of threads per block, or block size— as well as shared memory usage.
- Multiple concurrent blocks can reside on a multiprocessor, so occupancy is not determined by block size alone.
- Improving occupancy generally does not translate to a similar increase in performance. A lower occupancy kernel will have **more registers** available per thread than a higher occupancy kernel, which may result in less register spilling to local memory. Typically, once an occupancy of 50 percent has been reached, additional increases in occupancy do not translate into improved performance.
- The CPU sends the kernel launch request to the GPU driver/runtime. The GPU schedules the kernel for execution on its Streaming Multiprocessors (SMs). The kernel is broken down into a grid of thread blocks.
  - Grid: A collection of Thread Blocks (TBs).
  - Thread Block: A group of threads that run together and share shared memory. The GPU has multiple SMs (e.g., 84 SMs in an RTX 3090). The Grid is split into Thread Blocks that are assigned to different SMs. Each SM processes multiple Thread Blocks based on available resources (registers, shared memory, etc.).
  - More SMs = More parallel execution of thread blocks.
  - A Thread Block does not migrate—it stays on one SM until all its threads finish execution. Inside each SM, a Thread Block is broken into Warps.All threads in a warp execute the same instruction at the same time (SIMT execution model).
  - If threads in a **warp diverge** (e.g., due to an if-else statement), performance is reduced.Each SM has multiple CUDA cores (ALUs) where warps execute. A Warp Scheduler in the SM picks a ready-to-execute warp. The warp executes its instruction using CUDA cores. If a warp stalls (e.g., waiting on memory), the scheduler switches to another warp to keep the SM busy.More active warps → Better latency hiding (because when some warps wait on memory, others keep executing). In older GPU, there was only one warp scheduler and therefore only one warp can issue instructions at a time. Modern GPUs have multiple warp schedulers per SM, so several warps execute simultaneously.
- Threads per block should be a multiple of warp size to avoid wasting computation on under-populated warps and to facilitate coalescing.
- A minimum of 64 threads per block should be used, and only if there are multiple concurrent blocks per multiprocessor.
  Between 128 and 256 threads per block is a better choice and a good initial range for experimentation with different block sizes.
  Use several (3 to 4) smaller thread blocks rather than one large thread block per multiprocessor if latency affects performance. This is particularly beneficial to kernels that frequently call __syncthreads().


| Compute Capability | Architecture Name | Example GPU       |
| ------------------ | ----------------- | ----------------- |
| `1.x`              | Tesla             | Quadro FX5800     |
| `2.x`              | Fermi             | GTX 480           |
| `3.x`              | Kepler            | GTX 780           |
| `5.x`              | Maxwell           | GTX 750 Ti        |
| `6.x`              | Pascal            | GTX 1080, Titan X |
| `7.0`              | Volta             | Titan V           |
| `7.5`              | Turing            | RTX 2060          |
| `8.0 / 8.6`        | Ampere            | A100, RTX 3080    |
