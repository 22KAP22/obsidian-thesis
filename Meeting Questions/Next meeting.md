-Ask about the use of GenAI. I am using it somewhat. Most of the time to ask/discuss stuff. Sometimes for code snippets. Especially useful to me when writing text.

- benchmarking:
	- Can't use Python PAPI. 'pypapi.exceptions.PapiPermissionError: Permission level does not permit operation. (PAPI_EPERM)'
	  'cat /proc/sys/kernel/perf_event_paranoid -> 4'
	  No root level permissions to change
	- Should the datasets always be normalized? Somewhat necessary, since we use only L2 metric. PDX only capable of that/optimized only for that.
		- will prob keep normalizing,. maybe check if makes difference between normalized and non-normalized?
	- How many runs for one experiment?
		- I use 3-5 now
	- How many nprobe? I think I used too much in first experiments.
		- If we want to reach certain nprobe, then start low and increase until hit
		- I like the idea to inspect the same fraction of the dataset for all datasets. Instead of constant nprobes, start with 1/8 of the nlist and then half it until we hit 2 by integer division or something similar.
	- Warm-up cache? Do a run before? For all nprobes or only before first?
		- Warm-up needed I suppose. It's supposed to load data-structure for search into cache, so maybe only for big nprobe needed?
		- If warm-up for all nprobe, compute recall there instead of in another loop at the end?
	- Should I reorder queries and/or nprobes between runs?
	- Latency use global stats in faiss.
	- When multi-threading introduced probably fall back to measuring time between index.search call
	- Does it make sense to introduce nprobe=index.nlist to see what the max recall of a method on a dataset can be? ('measuring the irreducible approximation error of each ANN representation, independent of search budget')
	- I noticed that I transform all the queries for PCA myself and don't count that towards search time, whereas IVF OPQ the transformation of the query vector is part of the search, which we measure. I need to be consistent. I suppose letting index transform makes more sense. Do both to see difference? What's your thought?
		- I opted for the index_factory for all indexes. This includes transformations of the query in the search call.

- Matryoshka
	- I would like to do that, but still need to decide which dataset to use.
	- What to compare it against? Do the same as PCA?
- Dim reduction in general
	- How is dim reduction most effective?
	- I used PCA with ||TPCA(x) - TPCA(q)|| ≈ ||x - q|| where TPCA stands for applying PCA and then truncating the vector. The idea was that PCA transforms the vector in such a way that max variance dimension first, then second, ..., this would mean last dimensions should on average contribute the least to the distance between any two points in the set.
	- NOTE FOR ABOVE: ||TPCA(x) - TPCA(q)|| ≈ ||x - q||  doesn't even have to hold. What's important is that the ranking stays the same. So even if ||TPCA(x) - TPCA(q)|| = C * ||x - q|| it's only important that constant C stays the same for every x ∈ dataset. This way the ranking should stay the same.
	- Would be nice to use PCA in Panorama, but I think they already do that in their paper, since their idea is to start with PCA and find a better transformation, that shifts energy towards first dimensions for all vectors, not on average for entire dataset.
	- combine these rim reduction methods with quantization, maybe even with pruning possible, not sure

- What methods are applicable to other indexes?
	- PDX no shot. PDX shines by predicting how memory is accessed. For graph- or tree-based it's unclear which path is taken. Lots of jumps in memory -> SIMD killer.
	- Panorama should be easily applicable, since it is only concerned about vector representation. Not how memory is accessed. Simply transform vectors, build tree out of that, use pruning to break early distance computation.