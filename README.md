# Getting pyrocko working on ARC4

Using Anaconda to get Pyrocko on ARC4.

```{bash}
$ module load anaconda

$ conda create --name pyrocko

$ conda activate pyrocko
$ conda install -c pyrocko pyrocko
# to get fomosto to work also install PyOpenGL
$ conda install -c conda-forge pyopengl
```

This gets the basic Pyrocko install with the fomosto command line tool for working with Green's functions.

To install the qseis and qssp backends as requested by the user we have to do the following:

```{bash}
# example using qseis but commands the same for qssp

$ git clone https://git.pyrocko.org/pyrocko/fomosto-qseis.git

$ cd fomosto-qseis

$ autoreconf -i 

$ ./configure --prefix=/nobackup/example/location/for/qseis/bin

$ make

$ make install # this will install the backend into the specified directory

```

## Installing beat

Following the instructions from https://hvasbath.github.io/beat/anaconda_installation.html

```{bash}
$ conda create -n beat python=3.6
$ source activate beat
$ mkdir beat_src
$ cd beat_src
$ git clone https://github.com/hvasbath/beat
$ conda install -n beat libgfortran openblas theano pygpu openmpi pandas=0.24.2 numpy=1.17.1
$ nano ~/.theanorc # contents below
$ cd beat
$ python test/numpy_test.py
dotted two (1000,1000) matrices in 11.6 ms
dotted two (4000) vectors in 6.02 us
SVD of (2000,1000) matrix in 0.907 s

$ THEANO_FLAGS=mode=FAST_RUN,device=cpu,floatX=float32 python test/gpu_test.py     
[Elemwise{exp,no_inplace}(<TensorType(float32, vector)>)]
Looping 1000 times took 2.187031 seconds
Result is [1.2317803 1.6187934 1.5227807 ... 2.2077181 2.2996776 1.6232328]
Used the cpu

$ conda install -n beat -c conda-forge pymc3
$ conda install -n beat -c pyrocko pyrocko

```

Contents of .theanorc

```{bash}
# copied from https://hvasbath.github.io/beat/anaconda_installation.html
[blas]
ldflags = -L/home/home01/medacola/.conda/envs/beat/lib -lopenblas -lgfortran

[nvcc]
fastmath = True

[global]
device = cpu
floatX = float64

[lib]
amdlibm = False

```

With all these settings configured and packages installed we can then run the `setup.py` file in beat.

```{bash}
$ cd beat_src/beat
$ python setup.py install

# then do a fast check for beat running
module remove openmpi # we need to unload the openmpi module as we've installed openmpi via conda
beat init --help
```
