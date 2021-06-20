# Installing Meshing/place_receivers
This file is for compiling Meshing/place_receivers on KAUST Supercomputer Facility for placing receivers on topography

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

## Clone ASAGI and update submodules
git clone https://github.com/SeisSol/Meshing.git</br>
cd Meshing</br>
git submodule update --init</br>

## Install place reveivers
cd place_receivers</br>
mkdir build && cd build</br>
$HOMESW/bin/cmake/bin/cmake .. </br>
make</br>
