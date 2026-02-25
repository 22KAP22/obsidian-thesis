(https://arxiv.org/abs/1603.09320) (https://en.wikipedia.org/wiki/Hierarchical_navigable_small_world) (https://www.pinecone.io/learn/series/faiss/hnsw/)

(The following information is my interpretation of the pinecone article on HNSW)

HNSW is comparable to a skip list. The idea is that we have multiple layers on a graph (or multiple graphs each representing a layer) where the lower we go, the more vertices we have in a layer. In layer 0 all vertices exist. In each layer we add an edge between two vertices if they're close to each other (whatever close means, can be euclidean distance, l1 distance, cosine thingy, etc.). A maximal degree M needs to be defined. This indicates how many neighbors a vertex has in layers 1-k. In layer 0 the max degree is 2\*M.  The search then works as following:
Greedily search for the closest vertex to the query, by choosing the vertex with the lowest distance to the query. Once we found a local minimum, we go a layer down and repeat the process. This is done until we find a local minimum in layer 0.
The idea is to use the upper layers to find the right region quickly and then by going down the layers do a more and more fine-grained search.
The parameters efConstruction and efSearch can be set. efConstruction needs to be set before building the index and efSearch before search.
According to chatGPT efSearch is the size of the candidate set. Don't know yet how a vector gets in there. Only while searching nearest neighbor? What about 2nd nearest, k-nearest?

