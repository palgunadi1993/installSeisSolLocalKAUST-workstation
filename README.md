# installSeisSolLocalKAUST-workstation
This repository is for compiling open-source software SeisSol on KAUST workstation:

## Set path
export HOMESW=/home/palgunkh/Documents/Software/SeisLib
export PATH=$HOMESW/bin:$PATH

export LIBRARY_PATH=$HOMESW/lib:$LIBRARY_PATH
export LD_LIBRARY_PATH=$HOMESW/lib:$LD_LIBRARY_PATH
export PKG_CONFIG_PATH=$HOMESW/lib/pkgconfig:$PKG_CONFIG_PATH
export CMAKE_PREFIX_PATH=$HOMESW:$CMAKE_PREFIX_PATH
export EDITOR=vi
export CPATH=$HOME/include:$CPATH

## Install HDF5
wget https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.8/hdf5-1.8.21/src/hdf5-1.8.21.tar.bz2
tar -xaf hdf5-1.8.21.tar.bz2
cd hdf5-1.8.21/
CPPFLAGS="-fPIC ${CPPFLAGS}" CC=/usr/bin/mpicc FC=/usr/bin/mpif90 ./configure --enable-parallel --prefix=$HOMESW --with-zlib --disable-shared --enable-fortran
make -j8
make install
cd ..

## LIBXSMM
Turn off anaconda path in .bashrc
git clone https://github.com/hfp/libxsmm
cd libxsmm
make generator
cp bin/libxsmm_gemm_generator ../bin/
cd ..
Turn on anaconda path in .bashrc

## PSpaMM
git clone https://github.com/peterwauligmann/PSpaMM.git
ln -s $(pwd)/PSpaMM/pspamm.py bin/

## GemmForge
pip install gemmforge

## CMAKE
(cd $(mktemp -d) && wget -qO- https://github.com/Kitware/CMake/releases/download/v3.16.2/cmake-3.16.2-Linux-x86_64.tar.gz | tar -xvz -C "." && mv "./cmake-3.16.2-Linux-x86_64" "${HOMESW}/bin/cmake")

## ParMetis
wget http://glaros.dtc.umn.edu/gkhome/fetch/sw/parmetis/parmetis-4.0.3.tar.gz
tar -xvf parmetis-4.0.3.tar.gz
cd parmetis-4.0.3/
#edit ./metis/include/metis.h IDXTYPEWIDTH to be 64 (default is 32).
make config cc=/usr/bin/mpicc cxx=/usr/bin/mpicxx prefix=$HOMESW
make install
cp build/Linux-x86_64/libmetis/libmetis.a $HOMESW/lib
cp metis/include/metis.h $HOMESW/include
cd ..

## NetCDF
wget https://syncandshare.lrz.de/dl/fiJNAokgbe2vNU66Ru17DAjT/netcdf-4.6.1.tar.gz
tar -xaf netcdf-4.6.1.tar.gz
cd netcdf-4.6.1/
Turn off anaconda PATH
Set the PATH
CFLAGS="-fPIC ${CFLAGS}" CC=h5pcc ./configure --enable-shared=no --prefix=$HOMESW --disable-dap
make -j8
make install
cd ..
Turn on anaconda PATH

## SeisSol
export CMAKE_PREFIX_PATH=$HOMESW:$CMAKE_PREFIX_PATH
git clone https://github.com/SeisSol/SeisSol.git
cd SeisSol
git submodule update --init
mkdir build-release && cd build-release

CC=/usr/bin/mpicc CXX=/usr/bin/mpicxx FC=/usr/bin/mpif90 CMAKE_PREFIX_PATH=$HOMESW:$CMAKE_PREFIX_PATH PKG_CONFIG_PATH=/home/palgunkh/Documents/Software/SeisLib/lib/pkgconfig/:$PKG_CONFIG_PATH /home/palgunkh/Documents/Software/SeisLib/bin/cmake/bin/cmake -DNETCDF=ON -DMETIS=ON -DPLASTICITY=OFF -DCOMMTHREAD=OFF -DASAGI=OFF -DHDF5=ON -DCMAKE_BUILD_TYPE=Release -DTESTING=OFF -DLOG_LEVEL=warning -DLOG_LEVEL_MASTER=info -DHOST_ARCH=hsw -DPRECISION=double ..

/home/palgunkh/Documents/Software/SeisLib/bin/cmake/bin/ccmake ..
