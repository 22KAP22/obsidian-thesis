[[Systems + Libraries]]

Facebook AI Similarity Search (FAISS) is Facebook's open source implementation of vector similarity search.

I had trouble building it from source. Important later, as I need to implement a few things myself. Installed the library via conda, though ran into some issues there aswell... They use somewhat older python/numpy/etc. versions.

It includes quite a few indexes with different quantization or dimension reduction techniques.

There exist both a C++ and Python API, which support the same functionalities. (SWIG is used to 'translate' the C++ API to the python one.)

So far I've used the python API. Usage is straight forward. Provide a set of vectors as an ndarray to an index, and the index then gets built on its own.
For stuff like PQ the user can choose with which set to train the Quantization and afterwards add the dataset to the index. This way one can choose a subset of vectors to speed up the k-means for the centroids. Note that it's important that this subset has a good distribution in order to produce precise results later on.
Search Parameters can be tuned as well. Doesn't seem like there are parameters one can set for the training phase.

Interesting things that are already implemented:
	- Brute force search/Flat Index
	- [[Inverted File Vector Search (IFV)]] index with different quantization techniques
	- [[Hierarchical Navigable Small World (HNSW)]] index with different quantization techniques
	- [[Product Quantiazion (PQ)]] index with 2 different search algorithms
	- data transformation techniques such as:
		- PQ
		- Scalar Quantizer
		- Additive Quantizer
		- [[Principle Component Analysis (PCA)]]
		- Iterative Quantization (ITQMatrix)('A procrustean approach to learning binary codes for large-scale image retrieval,')


Performance so far seems meh. Though I have to mention that it runs on my own system without GPU support. GPU support exists, but it is limited to Flat and IFV indexes.

INDEXES AND THE TRAINED PQ REPRESENTATIONS CAN BE STORED. GOD BLESS!!! Ahem, that's rather convenient for benchmarking, we're interested in Queries Per Second(QPS) rather than index build time and model training. These indexes should be living in RAM anyway for a longer time. These datasets should be queried quite a lot and served by, well, servers.

--Still need to look into the code base--