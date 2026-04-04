-Ask about the use of GenAI. I am using it somewhat. Most of the time to ask/discuss stuff. Sometimes for code snippets. Especially useful to me when writing text.

- benchmarking:
	- Should the datasets always be normalized? When yes, when no?
	- How many runs for one experiment?
	- How many nprobe? I think I used too much in first experiments.
	- Warm-up cache? Do a run before? For all nprobes or only before first?
	- If full warm-up, compute recall there ok?
	- Should I reorder queries and/or nprobes between runs?
	- Latency use global stats in faiss.
	- When multi-threading introduced probably fall back to measuring time between index.search call
	- Does it make sense to introduce nprobe=index.nlist to see what the max recall of a method on a dataset can be? ('measuring the irreducible approximation error of each ANN representation, independent of search budget')
	- I noticed that I transform all the queries for PCA myself and don't count that towards search time, whereas IVF OPQ the transformation of the query vector is part of the search, which we measure. I need to be consistent. I suppose letting index transform makes more sense. Do both to see difference? What's your thought?

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