Let's try to fix the errors such that the PDX library properly compiles on the mobile system. Plus make sure that the dependencies are 'working' for both faiss and PDX.
PDX needs to be running on an AVX2 capable machine. The dependencies, let's see about that one.

It seems like I managed to get a working environment for the FAISS library. I'll shortly describe what I did in which order to get it working:

#Conda_Env
1) Create new virtual environment:
	1) I started fresh by initializing a venv the following way:
	   `conda create -n <env_name> python=3.10 'numpy<2' cmake ninja pkg-config pybind11 compilers swig gflags pytest -c conda-forge`
	   `conda activate <env_name>`
	   These packages and python version should cover everything needed to install create and install the files for FAISS. Consider the package `llvmdev` if C++ testing is important/needed. Replace `<env_name>` with the name you want to give your environment.


#FAISS
1) The following commands set up some environment variables and then build the C++ files for the FAISS library. Using my old code in which I used the conda-forge installed faiss library works! Seems like a good start.
	1) `git clone https://github.com/facebookresearch/faiss.git`
	   If you haven't cloned the FAISS repo yet.
	2) The following command disables user site-packages. This means only the ones installed the virtual environment are considered. Note that this is only for python. 
	   `export PYTHONNOUSERSITE=1`
	3) The following command exports the directory in which the numpy package is installed inside the virtual environment. Used during cmake, I think.
	   `export NUMPY_INCLUDE_DIR=$(python -c "import numpy; print(numpy.get_include())")`
	4) The following command builds the library. We need to specify which python and which packages to consider. CMake will otherwise use user site-packages, which might not be the correct versions of the ones we actually need. Note that it needs the python path, path to the packages/include and the path to numpy.
	   `cmake -B build   -DPython_EXECUTABLE=$(which python)   -DPYTHON_INCLUDE_DIR=$(python -c "from sysconfig import get_paths as gp; print(gp()['include'])")   -DNUMPY_INCLUDE_DIR=$NUMPY_INCLUDE_DIR   -DCMAKE_PREFIX_PATH="$CONDA_PREFIX"   -DFAISS_ENABLE_PYTHON=ON   -DFAISS_ENABLE_GPU=OFF   -DFAISS_ENABLE_TESTS=ON   -DCMAKE_BUILD_TYPE=Release -DNUMPY_INCLUDE_DIR=$NUMPY_INCLUDE_DIR`
	5) This one makes the files:
	   `make -C build -j4 faiss_avx2`
	   Note that `-j` should be avoided, since it spawns too many threads which end up overpopulating the RAM leading the OS to kill the process.
	   `make -C build -j4 faiss`
	   If AVX2 is not desired.
	6) Make the files for the python bindings and also install them for python(?):
	   `make -C build -j4 swigfaiss`
	   `(cd build/faiss/python && python setup.py install)`
	7) Installing the C++ library and headers:
	   `make -C build install`
	8) Testing: (I noticed that I first did everything with the flag tests=OFF, maybe that's why a few of the test were skipped or failed)
	   `make -C build test`
	   `(cd build/faiss/python && python setup.py build)`
	   `PYTHONPATH="$(ls -d ./build/faiss/python/build/lib*/)" pytest tests/test_*.py`

#PDX 
- We run into some issues with the AVX2 code. The base code shared by all architectures is on the state of the AVX512 one. AVX2 is outdated, I don't know what's the state of NEON. This leads to a lot of compilation errors, since the base code tries to resolve stuff that doesn't exist in the AVX2 code. If I'm not mistaken it's mainly the updated version of PDX, where they added another layer of k-means on top of the first k-means and also the addition of other quantization techniques/formats. For example the class SIMDComputer<L2, Quantization::U8> is completely empty for the AVX2 code, where as the AVX512 is implemented.

		Is it worth to implement everything myself or should I use another system (One from the group, if they have one)?