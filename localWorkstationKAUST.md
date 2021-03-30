# install SeisSol Local KAUST-workstation
This repository is for compiling open-source software SeisSol on KAUST workstation:

## Set path
export HOMESW=/home/palgunkh/Documents/Software/SeisLib<br/>
export PATH=$HOMESW/bin:$PATH<br/>
export LIBRARY_PATH=$HOMESW/lib:$LIBRARY_PATH<br/>
export LD_LIBRARY_PATH=$HOMESW/lib:$LD_LIBRARY_PATH<br/>
export PKG_CONFIG_PATH=$HOMESW/lib/pkgconfig:$PKG_CONFIG_PATH<br/>
export CMAKE_PREFIX_PATH=$HOMESW:$CMAKE_PREFIX_PATH<br/>
export EDITOR=vi<br/>
export CPATH=$HOME/include:$CPATH<br/>

## Install HDF5
wget https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.8/hdf5-1.8.21/src/hdf5-1.8.21.tar.bz2<br/>
tar -xaf hdf5-1.8.21.tar.bz2<br/>
cd hdf5-1.8.21/<br/>
CPPFLAGS="-fPIC ${CPPFLAGS}" CC=/usr/bin/mpicc FC=/usr/bin/mpif90 ./configure --enable-parallel --prefix=$HOMESW --with-zlib --disable-shared --enable-fortran<br/>
make -j8<br/>
make install<br/>
cd ..<br/>

## LIBXSMM
Turn off anaconda path in .bashrc<br/>
git clone https://github.com/hfp/libxsmm<br/>
cd libxsmm<br/>
make generator<br/>
cp bin/libxsmm_gemm_generator ../bin/<br/>
cd ..<br/>
Turn on anaconda path in .bashrc<br/>

## PSpaMM
git clone https://github.com/peterwauligmann/PSpaMM.git<br/><br/>
ln -s $(pwd)/PSpaMM/pspamm.py bin/<br/>

## GemmForge
pip install gemmforge<br/>

## CMAKE
(cd $(mktemp -d) && wget -qO- https://github.com/Kitware/CMake/releases/download/v3.16.2/cmake-3.16.2-Linux-x86_64.tar.gz | tar -xvz -C "." && mv "./cmake-3.16.2-Linux-x86_64" "${HOMESW}/bin/cmake")<br/>

## ParMetis
wget http://glaros.dtc.umn.edu/gkhome/fetch/sw/parmetis/parmetis-4.0.3.tar.gz<br/>
tar -xvf parmetis-4.0.3.tar.gz<br/>
cd parmetis-4.0.3/<br/>
#edit ./metis/include/metis.h IDXTYPEWIDTH to be 64 (default is 32).<br/>
make config cc=/usr/bin/mpicc cxx=/usr/bin/mpicxx prefix=$HOMESW<br/>
make install<br/>
cp build/Linux-x86_64/libmetis/libmetis.a $HOMESW/lib<br/>
cp metis/include/metis.h $HOMESW/include<br/>
cd ..<br/>

## NetCDF
wget https://syncandshare.lrz.de/dl/fiJNAokgbe2vNU66Ru17DAjT/netcdf-4.6.1.tar.gz<br/>
tar -xaf netcdf-4.6.1.tar.gz<br/>
cd netcdf-4.6.1/<br/>
Turn off anaconda PATH<br/>
Set the PATH<br/>
CFLAGS="-fPIC ${CFLAGS}" CC=h5pcc ./configure --enable-shared=no --prefix=$HOMESW --disable-dap<br/>
make -j8<br/>
make install<br/>
cd ..<br/>
Turn on anaconda PATH<br/>

## SeisSol
export CMAKE_PREFIX_PATH=$HOMESW:$CMAKE_PREFIX_PATH<br/>
git clone https://github.com/SeisSol/SeisSol.git<br/>
cd SeisSol<br/>
git submodule update --init<br/>
mkdir build-release && cd build-release<br/>

CC=/usr/bin/mpicc CXX=/usr/bin/mpicxx FC=/usr/bin/mpif90 CMAKE_PREFIX_PATH=$HOMESW:$CMAKE_PREFIX_PATH PKG_CONFIG_PATH=/home/palgunkh/Documents/Software/SeisLib/lib/pkgconfig/:$PKG_CONFIG_PATH /home/palgunkh/Documents/Software/SeisLib/bin/cmake/bin/cmake -DNETCDF=ON -DMETIS=ON -DPLASTICITY=OFF -DCOMMTHREAD=OFF -DASAGI=OFF -DHDF5=ON -DCMAKE_BUILD_TYPE=Release -DTESTING=OFF -DLOG_LEVEL=warning -DLOG_LEVEL_MASTER=info -DHOST_ARCH=hsw -DPRECISION=double ..<br/>
<br/>
/home/palgunkh/Documents/Software/SeisLib/bin/cmake/bin/ccmake ..<br/>
