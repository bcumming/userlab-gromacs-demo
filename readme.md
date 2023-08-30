# UENV demo - CSCS User Lab Day 2023


## The Default Environment

Log into daint-gpu and inspect the environment:

```bash
ssh daint

# inspect the module environment
module list
module avail
```

Further use of commands `module load` 

The first difference you will notice
Log into eiger and 

```bash
ssh eiger

# after logging in, the environment is clean
module avail
module list

# to access the CPE load a module
module load cray
module list
module avail
```

The default environment on Alps clusters is much simpler than on Daint.

## Using user environments


On eiger, let's start a user-environment for GROMACS

```bash
ssh eiger

uenv status

# load the gromacs environment
uenv start /scratch/e1000/bcumming/gromacs-eiger.squashfs
uenv status
```

The output of `uenv status` shows information about the currently loaded 
```
/user-environment:gromacs-multicore
  Gromacs with and without Plumed, and the toolchain to build your own Gromacs for multicore

  modules: available
  views:
    develop
    gromacs
    plumed
```

The environment provides three interfaces:

**spack**:

The environment is built using Spack, and a configuration that that allows you to directly integrate it into your Spack 

**modules**:

* the familiar module environment that allows us to selecexposes gromacs and
* need to be activated using `uenv modules --use`

**environment views**

Subsets of the installed software can be loaded into the environment using the `uenv view load` command.
The GROMACS uenv provides three views:

* `gromacs`: GROMACS 2023.1 the most recent version at the time of creation
* `plumed`: GROMACS 2022.5 with PLUMED (2022.5 is the most recent version of GROMACS that supports PLUMED)
* `develop`: The compiler toolchain, MPI and optimised libraries like FFTW and OpenBLAS required to build GROMACS.

The `develop` view can be used to load 

```
uenv view develop
which mpicc && mpicc --version
```

Make a working directory:

```bash
mkdir $SCRATCH/gmx-custom
cd $SCRATCH/gmx-custom
git clone https://github.com/gromacs/gromacs.git
(cd gromacs; git checkout 631f42ed83e3524e12cbee11821a4b537ebf1c57)
```

mkdir build
cd build
CC=mpicc CXX=mpicxx cmake -DGMX_MPI=yes  -DCMAKE_INSTALL_PREFIX=$SCRATCH/gmx-custom/install ../gromacs

# focus on how Python3, OpenBLAS, etc are found

make install -j32

source $SCRATCH/gmx-custom/install/bin/GMXRC.bash

gmx_mpi --version
```


