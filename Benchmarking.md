We want to benchmark several things. First we benchmark each implementation on their own and then we start combining the methods where possible.

Based on what system/library we use there are multiple things already implemented. Some newer stuff like PDX, Panorama, Matryoshka still need to be implemented and properly supported by the indexes. I think the search algorithm needs to be slightly adjusted. The main idea is the same, but especially for Matryoshka one needs to switch from lower dim vectors to higher ones eventually.

	 On which system do we benchmark all this stuff?

Based on the dataset size this will never fit in memory on my system. Linux or Windows? I think I have to code in C++, since Python essentially calls the C++ code via the SWIG generated interface.

	What will we compare against each other?

We will benchmark quite a few things, so what should be compare in the end against each other? Do we simply want to most performance at a specific recall and ignore how the indexes achieve this? What if one is super strong at sequential search, while another index can do great at parallel search? I suppose this doesn't really matter if we only care for Queries Per Second (QPS).