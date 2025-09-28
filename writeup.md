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

## Program 4

## Program 5

## Program 6
