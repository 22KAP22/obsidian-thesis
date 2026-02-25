What I want to show:
- Results PDX vs. FAISS (both vanilla ivf and ivf+sq8)
- Results PCA for vanilla IVF on FAISS
- Currently running benchmark for PQ8 on IVF FAISS
- Next:
	- Panorama (faiss or author's implementation?)
	- Matryoshka (have to look into it)
	- start combining methods?


Experiments setup:
- Normalized dataset
- No multithreading/ only 1 thread
- Search queries are transformed before search/isn't included in time measurement
- Only measuring search call. Note it's the python binding we're calling. Overhead from python to C++ is included.
- Only L2 distance, though if dataset is normalized this shouldn't be an issue.
- Only FAISS? Or should I create multiple 'baselines'. I guess this depends on what the novel techniques build upon. For example Panorama and PDX build on top of FAISS -> makes sense to have FAISS as a baseline.


Thesis Questions:
- What should I note down? What do I need to write in my thesis in the end?
- For example how thoroughly do the processes in the experiments need to be noted down? Each method will bring its own problems with how we handle data, etc. does this need to be mentioned in the thesis?
- Focus more on 'good' results? Or treat all results the same?
