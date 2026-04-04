Note most, if not all these variants of search are assumed to be faster than baseline, since most if not all inspect/process less data(in bytes). Quantizers a little bit special, since they 'decompress' vector before doing distance calculation.

### Flat
+++Flat - baseline

### Quantizer
##### ==PQ== - quantizer, less memory used
	-Can it still achieve acceptable recall while improving speed? 
(Compared against baseline and other quantizers)

##### ==OPQ== - PQ with learned rotation
	-Does transforming the data lead to better accuracy than PQ?
	-Does it incur any costs during search?


##### PQ/OPQ with ==Refinement==
	-If we return n >> k candidates and re-rank them with higher precision representations of the vectors, can we gain accuracy?
	-How much time do we lose doing this?
(Compared against base PQ/OPQ)


##### ==SQ== - quantizer, less memory used
	-Can it still achieve acceptable recall while memory footprint and improving speed?
(Compared against baseline and other quantizers)

### Dimension Reduction
##### ==PCA== 
	-Transforming data, such that dimensions are ordered by highest variance, can we reduce the dimensions(in this case truncate) of vectors and retain accuracy?
(Compared against baseline and other dim reductions)

##### ==Matryshoka==
	-Does a learned representation that focuses on encoding as much information as possible in the early dimensions lead to good accuracy if only first dimensions are used?
(Compared against baseline and other dim reductions)



### Pruning methods
	-Does pruning lead to faster query responses?
	-Can the accuracy be maintained?
(Compared against baseline for all pruning methods)
##### PDX
	- Does memory layout increase efficiency(less loads, maybe even less computations?)
(Compared against other pruning methods(check average dim for pruning))

##### Panorama
	- Does the transformation lead to less dimensions being inspected than other pruning methods?
(Compared against other pruning methods(check average dim for pruning))