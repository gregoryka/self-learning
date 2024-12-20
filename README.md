# Self Learning Materials
This contains my personal learning materials, focused mostly on HPC techs. Made so I don't forget different stuff I come across and want to look into.

## Compilers
This is a avery non-exhaustive list of different compilers. A modern compiler includes optimizations that may improve code execution, so this should be up to date.
 - GCC - The De-facto compiler for linux based systems.
 - Clang - A C family compiler based on LLVM. Made to be very modular, and thus be easy to add new languages to.
 - [Whole bunch of other compilers](https://en.wikipedia.org/wiki/List_of_compilers) - Absolutely no reason to list them all myself.

## Languages
A lot of languages exist, but for preformance-centric tasks I want to focus on:
- C++ - A high level language that has a large community
- Rust - A relatively new systems programming language focusing on safety. Unfortunately doesn't have a lot of support for heterogenous computring
- Python - While not strictly a performant language, the ease of programming means that it is supported by a lot of performance libraries.

## Heterogenous Computing
Refers to systems that use multiple types of computing hardware. List of hardware:
- CPU - No need to explain. Some CPUs come with different types of cores, "efficiency" cores and "performance" cores.
- GPU - Originally made for graphics, but very good for parallelizeable tasks.
- FPGA - Harder to acquire the skill set, but raw hardware is more efficient.

## Frameworks for Heterogenous Computing
- CUDA - The Nvidia "standard". Programmed in C++ with a syntax extension for launching kernels (`<<<>>>`).
  - HIP - AMD's equivalent, can support Nvidia deivces by simply "wrapping" the CUDA headers.
- SYCL - A khronos standard. Is based on C++ with only a library extension for launching kernels. Is also made for different kinds of accelerators, not just GPUs.
  - oneAPI - An Intel led implementation. Has adapters for Nvidia and AMD GPUs as well (although the AMD implementation seems to be very bad[^1].
  - AdaptiveCPP - A non-vendor implementation.
- OpenCL - An older, lower-level khronos standard. May be used by other standards under the hood.
- Vulkan - A graphics API, but can launch compute kernels on hardware as well. May be used by other standards under the hood.
- OpenMP, OpenMPI, OpenACC, ...

## Executable Optimization
Includes tech to optimize executable code without touching the source code at all.
- Compiler optimizations - Pretty much any compiler today implements optimizations (example: dead code elimination, loop unrolling, function inlining, etc)
- Link time optimization - By deferring some code generation to link time, when entire program is known and not just one part (compilation unit), more optimizations are possible.
  - ThinLTO - Clang tech. Full LTO seems to be quadratic in memory and time, this brings it to linear levels.
- Code Profiling - By actually running the program and getting data on the frequency of code execution, the compiler can focus more code optimization on "hot spots". Requires a "representative workload" to be good (unit tests are usually not, because they focus on extreme unlikely cases too much).
  - Profile-guided optimization - A special build of the program with code frequency counters inserted, which is run with the workload to generate a code heat map, which is then used to compile to get an optimized binary. The profiling binary however is severely slowed down.
  - AutoFDO - The code is compiled almost as-is, with (almost?) no slowdown. The profile is gathered using CPU counters instead. Is usually good enough, but may involve a small loss compared to full blown PGO.
- Binary layout optimization - Involves re-linking the binary to optimize layout in memory (taking advantage of cache locality, etc)
  - BOLT - The first such optimizer. Involves some disassembly of the executable - may hurt correctness.
  - Propeller - Another implementation.
  - Thin layout optimizer - Used by clearlinux. Supports more compilers and linkers, but currently out of tree.

## Libraries
Different libraries may serve the same goal but perform better on certain hardware.

### FFT
- FFTW - the de-facto standard for fft libraries today.
- cuFFT - The nvidia implementation on their GPUs.
- [gearshifft](https://github.com/mpicbg-scicomp/gearshifft) - a benchmark for fft implementations.

### BLAS + LAPACK
Linear algebra libraries.
- flexiblas - Runtime exchange of BLAS and LAPACK implementaions.
- NETLIB-BLAS, OpenBLAS, ATLAS, Intel MKL, BLIS, ...

### Digital Signal Processing
- LiquidDSP
- Gnuradio - Framework for software DSP based on "Blocks".
- Pothos - Another framework for software DSP. Has more control over scheduling and such?

### Misc/to sort through
- Intel IPP / Nvidia NPP
- Intel MKL
- oneTBB

## Package Managers
The numerous software that should be installed requires good package management to keep track of versions, compile flags, etc
- Nix - A functional package manager. Involves a steep learning curve, and is not easy to debug, but creates reproducible builds and has complete control over all dependencies.
  - Software can be containerized using nix2container efficiently (reusing deps).
- Spack - A package manager for HPC. Is based on python, and uses system deps if possible (unlike Nix).
- APT / RPM - The package managers for debian-based or fedora-based linux distros, respectively. Doesn't allow multiple installed versions of a package.

## Parallelization
- Processes - The basic unit that an os can run. Inter Process Communication (IPC) is a pain.
- Threads - Is the basic scheduling unit for the kernel. Each process can use multiple threads to perform some tasks concurrently (not necessarily in parallel).
- Fibers - Also called user-space threads. Each thread can run multiple fibers, and they use cooperative scheduling (i.e must yield for another fiber to run). By using userspace context switching instead of kernelspace, they are more efficient (as a switch to kernel space is costly).
- Coroutines - Functions that can "pause" themselves and be resumed later. Do not actually parallelize.

## Other
- MLIR - tool for compiler builders.

 [^1]: [SYCL-Bench 2020: Benchmarking SYCL 2020 on AMD, Intel, and NVIDIA GPUs](https://dl.acm.org/doi/fullHtml/10.1145/3648115.3648120)
