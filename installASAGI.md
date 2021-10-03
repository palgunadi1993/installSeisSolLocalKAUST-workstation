# Installing ASAGI
This file is for compiling ASAGI on KAUST Supercomputer Facility

## Use cdl5 environment
ssh -X cdl5</br>

## Load necessary modules
module swap PrgEnv-cray PrgEnv-intel</br>
module load cdt</br>
module load python/3.8.0-cdl</br>

## Set path
export HOMESW=/project/k1488/kadek/myLibs/cmakeSeisSol</br>
export PATH=$HOMESW/bin:$PATH</br>
export LIBRARY_PATH=$HOMESW/lib:$LIBRARY_PATH</br>
export LD_LIBRARY_PATH=$HOMESW/lib:$LD_LIBRARY_PATH</br>
export PKG_CONFIG_PATH=$HOMESW/lib/pkgconfig:$PKG_CONFIG_PATH</br>
export CMAKE_PREFIX_PATH=$HOMESW:$CMAKE_PREFIX_PATH</br>
export EDITOR=vi</br>
export CPATH=$HOME/include:$CPATH</br>

## Set compiler options
export FC=ftn or FC=mpifc (for gcc)</br>
export CXX=CC or CXX=mpigxx (for gcc)</br>
export CC=cc or CC=mpigcc (for gcc)</br>

## Clone ASAGI and update submodules
git clone https://github.com/TUM-I5/ASAGI.git</br>
cd ASAGI</br>
git submodule update --init</br>

## Install ASAGI
mkdir build</br>
cd build</br>
export CMAKE_PREFIX_PATH=$CMAKE_PREFIX_PATH</br>
$HOWESW/bin/cmake/bin/cmake ../ -DCMAKE_INSTALL_PREFIX=$HOMESW/ASAGI/build</br>
make -j8</br>
make install</br>

## Set the following path
export PKG_CONFIG_PATH=$HOMESW/ASAGI/build/lib/pkgconfig:$PKG_CONFIG_PATH</br>
export LD_LIBRARY_PATH=$HOMESW/ASAGI/build/lib:$LD_LIBRARY_PATH</br>

## Continue to install SeisSol
After the installation is done, continue to [install SeisSol](https://github.com/palgunadi1993/installSeisSolLocalKAUST-workstation/blob/main/Shaheen.md).



