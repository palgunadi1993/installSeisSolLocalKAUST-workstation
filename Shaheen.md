# install SeisSol Shaheen II
This repository is for compiling open-source software SeisSol on KAUST Super Computer Facility:

# Compiling SeisSol cmake on Shaheen

## use cdl5 environment
ssh cdl5<\br>

## load necessary modules
module swap PrgEnv-cray PrgEnv-intel<\br>
module load cdt<\br>
module load python/3.8.0<\br>

## set path
export HOMESW=/project/k1488/kadek/myLibs/cmakeSeisSol<\br>
export PATH=$HOMESW/bin:$PATH<\br>
export LIBRARY_PATH=$HOMESW/lib:$LIBRARY_PATH<\br>
export LD_LIBRARY_PATH=$HOMESW/lib:$LD_LIBRARY_PATH<\br>
export PKG_CONFIG_PATH=$HOMESW/lib/pkgconfig:$PKG_CONFIG_PATH<\br>
export CMAKE_PREFIX_PATH=$HOMESW:$CMAKE_PREFIX_PATH<\br>
export EDITOR=vi<\br>
export CPATH=$HOME/include:$CPATH<\br>


## HDF5
wget https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.8/hdf5-1.8.21/src/hdf5-1.8.21.tar.bz2<\br>
tar -xaf hdf5-1.8.21.tar.bz2<\br>
cd hdf5-1.8.21<\br>
CPPFLAGS="-fPIC ${CPPFLAGS}" CC=cc FC=ftn ./configure --enable-parallel --prefix=$HOMESW --with-zlib --disable-shared --enable-fortran<\br>
make -j8<\br>
make install<\br>
cd ..<\br>

## LIBXSMM
git clone https://github.com/hfp/libxsmm<\br>
cd libxsmm<\br>
make generator<\br>
cp bin/libxsmm_gemm_generator ../bin/<\br>

## PSpaMM
git clone https://github.com/peterwauligmann/PSpaMM.git<\br>
ln -s $(pwd)/PSpaMM/pspamm.py bin/<\br>

## GemmForge
module load python/3.8.0-cdl<\br>
pip install gemmforge<\br>

## CMAKE
(cd $(mktemp -d) && wget -qO- https://github.com/Kitware/CMake/releases/download/v3.16.2/cmake-3.16.2-Linux-x86_64.tar.gz | tar -xvz -C "." && mv "./cmake-3.16.2-Linux-x86_64" "${HOMESW}/bin/cmake")<\br>

## ParMetis
wget http://glaros.dtc.umn.edu/gkhome/fetch/sw/parmetis/parmetis-4.0.3.tar.gz<\br>
tar -xvf parmetis-4.0.3.tar.gz<\br>
cd parmetis-4.0.3/<\br>
edit ./metis/include/metis.h IDXTYPEWIDTH to be 64 (default is 32).<\br>
make config cc=cc cxx=CC prefix=$HOMESW<\br>
make install<\br>
cp build/Linux-x86_64/libmetis/libmetis.a $HOMESW/lib<\br>
cp metis/include/metis.h $HOMESW/include<\br>
cd ..<\br>

## Netcdf
wget https://syncandshare.lrz.de/dl/fiJNAokgbe2vNU66Ru17DAjT/netcdf-4.6.1.tar.gz<\br>
tar -xaf netcdf-4.6.1.tar.gz<\br>
cd netcdf-4.6.1/<\br>
CFLAGS="-fPIC ${CFLAGS}" CC=h5pcc ./configure --enable-shared=no --prefix=$HOMESW --disable-dap<\br>
make -j8<\br>
make install <\br>

## SeisSol
export CMAKE_PREFIX_PATH=$HOMESW<\br>
git clone https://github.com/SeisSol/SeisSol.git<\br>
cd SeisSol<\br>
git submodule update --init<\br>
mkdir build-release && cd build-release<\br>

CC=cc CXX=CC FC=ftn CMAKE_PREFIX_PATH=/project/k1488/kadek/myLibs/cmakeSeisSol:$CMAKE_PREFIX_PATH PKG_CONFIG_PATH=/project/k1488/kadek/myLibs/cmakeSeisSol/lib/pkgconfig/:$PKG_CONFIG_PATH ../../bin/cmake/bin/cmake -DNETCDF=ON -DMETIS=ON -DPLASTICITY=OFF -DCOMMTHREAD=OFF -DASAGI=OFF -DHDF5=ON -DCMAKE_BUILD_TYPE=Release -DTESTING=OFF  -DLOG_LEVEL=warning -DLOG_LEVEL_MASTER=info -DHOST_ARCH=hsw -DPRECISION=double ..<\br>

../../bin/cmake/bin/ccmake .<\br>




> ASAGI                            OFF                                                                                                    
> BUILD_SHARED_LIBS                OFF                                                                                                    
> CMAKE_BUILD_TYPE                 Release                                                                                                
> CMAKE_INSTALL_PREFIX             /usr/local                                                                                             
> COMMTHREAD                       OFF                                                                                                    
> DEVICE_ARCH                      none                                                                                                   
> DEVICE_SUB_ARCH                  none                                                                                                   
> DYNAMIC_RUPTURE_METHOD           quadrature                                                                                             
> EQUATIONS                        elastic                                                                                                
> GEMM_TOOLS_LIST                  LIBXSMM,PSpaMM                                                                                         
> HDF5                             ON                                                                                                     
> HDF5_C_LIBRARY_dl                /usr/lib64/libdl.so                                                                                    
> HDF5_C_LIBRARY_hdf5              /project/k1488/kadek/myLibs/cmakeSeisSol/lib/libhdf5.a                                                 
> HDF5_C_LIBRARY_hdf5_hl           /project/k1488/kadek/myLibs/cmakeSeisSol/lib/libhdf5_hl.a                                              
> HDF5_C_LIBRARY_m                 /usr/lib64/libm.so                                                                                     
> HDF5_C_LIBRARY_z                 /usr/lib64/libz.so                                                                                     
> HOST_ARCH                        hsw                                                                                                    
> LOG_LEVEL                        warning                                                                                                
> LOG_LEVEL_MASTER                 info                                                                                                   
> Libxsmm_executable_PROGRAM       /project/k1488/kadek/myLibs/cmakeSeisSol/bin/libxsmm_gemm_generator                                    
> MEMKIND                          OFF                                                                                                    
> MEMORY_LAYOUT                    auto                                                                                                   
> METIS                            ON                                                                                                     
> MPI                              ON                                                                                                     
> NETCDF                           ON                                                                                                     
> NUMBER_OF_FUSED_SIMULATIONS      1                                                                                                      
> NUMBER_OF_MECHANISMS             0                                                                                                      
> OPENMP                           ON                                                                                                     
> ORDER                            5                                                                                                      
> PLASTICITY                       OFF                                                                                                    
> PLASTICITY_METHOD                nb                                                                                                     
> PRECISION                        double                                                                                                 
> PSpaMM_PROGRAM                   /project/k1488/kadek/myLibs/cmakeSeisSol/bin/pspamm.py                                                 
> SIONLIB                          OFF                                                                                                    
> TESTING                          OFF                                                                                                    
> TESTING_GENERATED                OFF                                                                                                    
> YAML-CPP_DIR                     YAML-CPP_DIR-NOTFOUND                                                                                  
> netCDF_DIR                       netCDF_DIR-NOTFOUND                                                                                    





## Configuration for job submission

>#!/bin/bash
>#
>#SBATCH --job-name=TPVtest
>#SBATCH --partition=workq
>#SBATCH --export=ALL
>#SBATCH --chdir=/project/k1488/kadek/tpvTest
>#SBATCH --output=logs/dfn.o%j
>#SBATCH --error=logs/dfn.e%j
>
>
>#SBATCH --nodes=32
>#SBATCH --ntasks-per-node=1
>#SBATCH --cpus-per-task=64
>
>
>#SBATCH --time=03:00:00
>#SBATCH --no-requeue
>
>
>#SBATCH --account=k1488
>
>date
>echo 'working directory is'
>pwd
>echo 'printing modules'
>module swap PrgEnv-cray PrgEnv-intel
>module load cdt
>
>#export PROC_BIND=spread
>export MP_SINGLE_THREAD=no
>export OMP_PLACES="cores(62)"
>export OMP_NUM_THREADS=62
>export KMP_AFFINITY=compact,granularity=thread
>
>export MPICH_MAX_THREAD_SAFETY=multiple
>export XDMFWRITER_ALIGNMENT=8388608
>export XDMFWRITER_BLOCK_SIZE=8388608
>export SC_CHECKPOINT_ALIGNMENT=8388608
>export SEISSOL_CHECKPOINT_ALIGNMENT=8388608
>export SEISSOL_CHECKPOINT_DIRECT=1
>export ASYNC_MODE=THREAD
>export ASYNC_BUFFER_ALIGNMENT=8388608
>
>
>export LD_LIBRARY_PATH=/project/k1488/kadek/myLibs/ASAGI/build/lib:$LD_LIBRARY_PATH
>echo 'setting stripe size'
>lfs setstripe -c 144 output


