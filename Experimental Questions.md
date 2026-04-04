Note most, if not all these variants of search are assumed to be faster than baseline, since most if not all inspect/process less data(in bytes). Quantizers a little bit special, since they 'decompress' vector before doing distance calculation.

##### Parameters to play with (IVF specific)
- coarse quantizer (how to search through centroids(for now only indexFlat L2))
- nlist(number of centroids/groups)
- nprobe(amount of centroids/groups to be considered during search, selects nprobe closest centroids to query)
- bacth_size (how many queries sent to index at once(for latency 1))
- (k, I guess, but I think setting it to 100 is best)


### Flat
+++Flat - baseline

### Quantizer
##### ==PQ== - quantizer, less memory used
	-Can it still achieve acceptable recall while improving speed? 
(Compared against baseline and other quantizers)
- nbits adjustable
- M adjustable

##### ==OPQ== - PQ with learned rotation
	-Does transforming the data lead to better accuracy than PQ?
	-Does it incur any costs during search?
(compare with PQ)
- nbits adjustable
- M adjustable

##### PQ + FastScan
	- Can it achieve similar accuracy to PQ?
	- Can it match PDX in terms of Latency/Throughput?
(compare with PQ(accuracy), and PDX(speed, because of memory layout))
- M adjustable
- SIMD width adjustable(? I think more like choose avx2 or avx512)
- residual toggleable (better accuracy, but slower)


##### PQ/OPQ with ==Refinement==
	-If we return n >> k candidates and re-rank them with higher precision representations of the vectors, can we gain accuracy?
	-How much time do we lose doing this?
(Compared against base PQ/OPQ)
- nbits adjustable
- M adjustable
- refinement algo/stage adjustable

##### ==SQ== - quantizer, less memory used
	-Can it still achieve acceptable recall while memory footprint and improving speed?
(Compared against baseline and other quantizers)
- nbits adjustable

### Dimension Reduction
##### ==PCA== 
	-Transforming data, such that dimensions are ordered by highest variance, can we reduce the dimensions(in this case truncate) of vectors and retain accuracy?
(Compared against baseline and other dim reductions)
- dim reduction factor adjustable

##### ==Matryshoka==
	-Does a learned representation that focuses on encoding as much information as possible in the early dimensions lead to good accuracy if only first dimensions are used?
(Compared against baseline and other dim reductions)
- dim reduction factor adjustable



### Pruning methods
	-Does pruning lead to faster query responses?
	-Can the accuracy be maintained?
(Compared against baseline for all pruning methods)
##### PDX
	- Does memory layout increase efficiency(less loads, maybe even less computations?)
(Compared against other pruning methods(check average dim for pruning))
- epsilon value adjustable (controls threshold for pruning)

##### Panorama
	- Does the transformation lead to less dimensions being inspected than other pruning methods?
(Compared against other pruning methods(check average dim for pruning))
- nlevels adjustable
- epsilon value adjustable (controls threshold for pruning)

##### Panorama + PQ
	- Does PQ increase the speed while pruning?
	- Does pruning keep the accuracy of IVF PQ?
(Compared against Panorama(memory, speed) and PQ(accuracy))
- M adjustable
- nlevels adjustable
- epsilon value adjustable (controls threshold for pruning)