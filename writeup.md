# Writeup for Assignment 1

Xavier Gonzalez: xavier18@stanford.edu
JS Paul: jspaul@stanford.edu

## Program 1

## 1.2

The speed-up is not linear n the number of threads used because of a thread-imbalance (just looking at the mandelbrot image, you can see that more work is being done by the threads in the middle).

![Speed-up graph](ims/speedup.png)

## 1.3

The measurements show that because of worker imbalance, we are not getting linear speed ups, but they are instead plateauing. We see imbalance because some parts of the image require more work than others (non uniform work distribution). This is key because we split the image up into even sections in our first approach for each thread to tackle. 

## 1.4

We break the image into multiple rows. Then each thread i takes on the row r for which r mod the number of threads equals i. This means each thread takes on some rows across all numThread sections of the image. In this way, we avoid worker imbalance. Indeed, we see a 7.19x speed-up with 8 threads now. 

This works because now we more uniformly spread out the work across the entire image across each thread. This means each thread takes on a representative sample of work to do across the whole image. 

## 1.5

Only 7.08 speed up with 16 threads. This is because the specs only support 8 hardware threads, so the OS is context switching between these concurrent threads (thread clashing).

## Program 2

2, 
Results matched with answer!
****************** Printing Vector Unit Statistics *******************
Vector Width:              2
Total Vector Instructions: 162515
Vector Utilization:        79.8%
Utilized Vector Lanes:     259245
Total Vector Lanes:        325030
************************ Result Verification *************************
4
Results matched with answer!
****************** Printing Vector Unit Statistics *******************
Vector Width:              4
Total Vector Instructions: 94571
Vector Utilization:        72.1%
Utilized Vector Lanes:     272563
Total Vector Lanes:        378284
************************ Result Verification *************************
Passed!!!
8
Results matched with answer!
****************** Printing Vector Unit Statistics *******************
Vector Width:              8
Total Vector Instructions: 51627
Vector Utilization:        68.1%
Utilized Vector Lanes:     281255
Total Vector Lanes:        413016
************************ Result Verification *************************
Passed!!!
16
****************** Printing Vector Unit Statistics *******************
Vector Width:              16
Total Vector Instructions: 26967
Vector Utilization:        66.3%
Utilized Vector Lanes:     285887
Total Vector Lanes:        431472
************************ Result Verification *************************
Passed!!!

We see that utilization goes down as vector width increases. This is likely due to the fact that as vector width increses, there is probably exponent that is much larger than the rest, leading to the other lanes not doing useful computation more often.

It could also be the case in the test cases used that we are dealing with arrays which are not divisible by the vector width. Our code uses the single threaded approach for the last few entries in the values array beyond where the length is dividable by vector width. If vector width is larger, this means these remainder counts will be bigger.

To be more sure, we would need to look at the specific test cases and the way their values are distributed. 

## Program 3

## part 1

We see a 5.04x speed up from ISPC for view 1 and a 4.36x speedup for view 2. 

We might expect at most a speed of 8x under this CPU with ISPC, because of the configuration to emit 8-wide AVX2 vector instructions.

The ISPC compiler maps gangs of program instances to SIMD instructions executed on a single core. However, these instances may not be well balanced within the SIMD lanes. Notably for instance at boundaries between white and black regions in the Mandelbrot image, there are regions which will require less iterations to finalize and others that will require more. Since SIMD involves masking off lanes that are done (earlier to the ones still working), this actually means we are not fully utilzing all the lanes, explaining why we fail to see the full 8x speedup.

## part 2

### 1. 
On view 1, we see a 9.88x speedup from task ISPC. This is 1.96x the speedup we saw from ISPC without partitioning (which as said in part 1 is just a 5.04x speedup). 

### 2.

# TO ASK: ARE WE RUNNING 8 SPMD TASKS OR 4 SPMD TASKS IN PARALLEL? UNSURE ABOUT HOW THE HYPERTHREADING WORKS HERE.

We have 4 cores which can each do 2 threads at any one time. This means we can have 8 threads running at any one time. However, if we only create 8 tasks, then if one task is more computationally intensive than the others, we will have worker imbalance and not all threads will be fully utilized. Thus, to balance out the work and ensure we always have cores working, we can create more tasks than threads. 

I was worried about thread clashing so I initially picked 2 tasks per thread so 16 tasks and this saw a speedup of: 32.13x. 

Then, I experimented with 32 tasks to see if having 4 tasks per thread would help. this saw a speed up of: 32.75x which is better. 

I then explored values in between. I found that for 20 tasks, I got the best speedup of 33x.

This implies to me that 20 tasks (or about 2.5 tasks per thread) is a good number as it balances out the work well to ensure all cores are working whilst not creating too many tasks such that thread clashing becomes too significant. 

### 3. 

A thread is a process that can be scheduled by the OS. Threads have their own program counter, registers, and stack. 

The ISPC task is a job which can be scheduled to be complete by worker threads. Each ISPC task works with SPMD, such that each thread that runs an ISPC task is running the program specified by the task with SIMD properties and speedup.

Launching 10,000 threads is expensive and inefficient, because the OS has to manage and schedule all these threads. Creating threads is expensive as they have their own program counter, registers, and stack. This involves a lot of overhead from thread clashing.

Conversely, launching 10,000 tasks is cheaper and more efficient, because the OS still handles its own few threads, which run the queued ISPC tasks. The overhead is less because the OS is not managing, creating, running, and switching between as many threads. 

Thus tasks act as a more lightweight and efficient way to get work done in parallel.

## Program 4

## Program 5

## Program 6
