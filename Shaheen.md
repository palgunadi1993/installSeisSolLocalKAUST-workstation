# install SeisSol Shaheen II
This repository is for compiling open-source software SeisSol on KAUST Super Computer Facility:

# Compiling SeisSol cmake on Shaheen
ssh -X cdl5<br/>

## load necessary modules
module swap PrgEnv-cray PrgEnv-intel<br/>
module unload intel<br/>
module load intel/19<br/>

## set path
export HOMESW=/project/k1488/kadek/myLibs/cmakeSeisSol<br/>
export PATH=$HOMESW/bin:$PATH<br/>
export LIBRARY_PATH=$HOMESW/lib:$LIBRARY_PATH<br/>
export LD_LIBRARY_PATH=$HOMESW/lib:$LD_LIBRARY_PATH<br/>
export PKG_CONFIG_PATH=$HOMESW/lib/pkgconfig:$PKG_CONFIG_PATH<br/>
export CMAKE_PREFIX_PATH=$HOMESW:$CMAKE_PREFIX_PATH<br/>
export EDITOR=vi<br/>
export CPATH=$HOME/include:$CPATH<br/>

## ASAGI path
export PKG_CONFIG_PATH=$HOMESW/ASAGI/build/lib/pkgconfig:$PKG_CONFIG_PATH<br/>
export LD_LIBRARY_PATH=$HOMESW/ASAGI/build/lib:$LD_LIBRARY_PATH<br/>
If you haven't install ASAGI, please follow this [step](https://github.com/palgunadi1993/installSeisSolLocalKAUST-workstation/blob/main/installASAGI.md).</br>

## HDF5
wget https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.8/hdf5-1.8.21/src/hdf5-1.8.21.tar.bz2<br/>
tar -xaf hdf5-1.8.21.tar.bz2<br/>
cd hdf5-1.8.21<br/>
CPPFLAGS="-fPIC ${CPPFLAGS}" CC=cc FC=ftn ./configure --enable-parallel --prefix=$HOMESW --with-zlib --disable-shared --enable-fortran<br/>
make -j8<br/>
make install<br/>
cd ..<br/>

## LIBXSMM
git clone https://github.com/hfp/libxsmm<br/>
cd libxsmm<br/>
make generator<br/>
cp bin/libxsmm_gemm_generator ../bin/<br/>

## PSpaMM
git clone https://github.com/peterwauligmann/PSpaMM.git<br/>
ln -s $(pwd)/PSpaMM/pspamm.py bin/<br/>

## GemmForge
pip install gemmforge<br/>

## CMAKE
(cd $(mktemp -d) && wget -qO- https://github.com/Kitware/CMake/releases/download/v3.16.2/cmake-3.16.2-Linux-x86_64.tar.gz | tar -xvz -C "." && mv "./cmake-3.16.2-Linux-x86_64" "${HOMESW}/bin/cmake")<br/>

## ParMetis
wget http://glaros.dtc.umn.edu/gkhome/fetch/sw/parmetis/parmetis-4.0.3.tar.gz<br/>
tar -xvf parmetis-4.0.3.tar.gz<br/>
cd parmetis-4.0.3/<br/>
edit ./metis/include/metis.h IDXTYPEWIDTH to be 64 (default is 32).<br/>
make config cc=cc cxx=CC prefix=$HOMESW<br/>
make install<br/>
cp build/Linux-x86_64/libmetis/libmetis.a $HOMESW/lib<br/>
cp metis/include/metis.h $HOMESW/include<br/>
cd ..<br/>

## Netcdf
wget https://syncandshare.lrz.de/dl/fiJNAokgbe2vNU66Ru17DAjT/netcdf-4.6.1.tar.gz<br/>
tar -xaf netcdf-4.6.1.tar.gz<br/>
cd netcdf-4.6.1/<br/>
CFLAGS="-fPIC ${CFLAGS}" CC=h5pcc ./configure --enable-shared=no --prefix=$HOMESW --disable-dap<br/>
make -j8<br/>
make install <br/>

## yaml-cpp
git clone https://github.com/jbeder/yaml-cpp.git<br/>
cd yaml-cpp<br/>
mkdir build && cd build<br/>
CC=cc CXX=CC FC=ftn $HOMESW/bin/cmake/bin/cmake ../ -DCMAKE_INSTALL_PREFIX=$HOMESW<br/>
make -j4<br/>
make install<br/>
cd ../.. <br/>

## ImpalaJIT
git clone https://github.com/uphoffc/ImpalaJIT.git<br/>
cd ImpalaJIT<br/>
mkdir build && cd build<br/>
CC=cc CXX=CC FC=ftn $HOMESW/bin/cmake/bin/cmake ../ -DCMAKE_INSTALL_PREFIX=$HOMESW<br/>
make -j4 <br/>
make install<br/>
cd ../.. <br/>

## Eigen3
wget https://gitlab.com/libeigen/eigen/-/archive/3.4.0/eigen-3.4.0.tar.gz<br/>
tar -xf eigen-3.4.0.tar.gz<br/>
cd eigen-3.4.0<br/>
mkdir build && cd build<br/>
cmake .. -DCMAKE_INSTALL_PREFIX=$HOMESW<br/>
make install<br/>
cd ../..<br/>

## easi
git clone https://github.com/SeisSol/easi.git<br/>
cd easi<br/>
mkdir build && cd build<br/>
CC=cc CXX=CC FC=ftn $HOMESW/bin/cmake/bin/cmake ../ -DCMAKE_PREFIX_PATH=$HOMESW -DCMAKE_INSTALL_PREFIX=$HOMESW -DASAGI=OFF -DIMPALAJIT=ON<br/>
make -j4 install<br/>

if ASAGI is installed, turn it ON.<br/>


## SeisSol
export CMAKE_PREFIX_PATH=$HOMESW<br/>
module load gcc/9.3.0<br/>
git clone https://github.com/SeisSol/SeisSol.git<br/>
cd SeisSol<br/>
git submodule update --init<br/>
mkdir build-release && cd build-release<br/>

CC=cc CXX=CC FC=ftn CMAKE_PREFIX_PATH=$HOMESW:$CMAKE_PREFIX_PATH PKG_CONFIG_PATH=$HOMESW/lib/pkgconfig/:$PKG_CONFIG_PATH $HOMESW/bin/cmake/bin/cmake -DNETCDF=ON -DMETIS=ON -DCOMMTHREAD=OFF -DASAGI=OFF -DHDF5=ON -DCMAKE_BUILD_TYPE=Release -DTESTING=OFF  -DLOG_LEVEL=warning -DLOG_LEVEL_MASTER=info -DHOST_ARCH=hsw -DPRECISION=double ..<br/>

$HOMESW/bin/cmake/bin/ccmake .<br/>



```bash
 ADDRESS_SANITIZER_DEBUG          OFF                                                                                                   
 ASAGI                            OFF                                                                                                   
 CMAKE_BUILD_TYPE                 Release                                                                                               
 CMAKE_INSTALL_PREFIX             /usr/local                                                                                            
 COMMTHREAD                       OFF                                                                                                   
 COVERAGE                         OFF                                                                                                   
 DEVICE_ARCH                      none                                                                                                  
 DEVICE_BACKEND                   none                                                                                                  
 DYNAMIC_RUPTURE_METHOD           quadrature                                                                                            
 EQUATIONS                        elastic                                                                                               
 GEMM_TOOLS_LIST                  LIBXSMM,PSpaMM                                                                                        
 HDF5                             ON                                                                                                    
 HDF5_C_LIBRARY_dl                /usr/lib64/libdl.so                                                                                   
 HDF5_C_LIBRARY_hdf5              /project/k1488/kadek/myLibs/cmakeSeisSol/lib/libhdf5.a                                                
 HDF5_C_LIBRARY_hdf5_hl           /project/k1488/kadek/myLibs/cmakeSeisSol/lib/libhdf5_hl.a                                             
 HDF5_C_LIBRARY_m                 /usr/lib64/libm.so                                                                                    
 HDF5_C_LIBRARY_z                 /usr/lib64/libz.so                                                                                    
 HOST_ARCH                        hsw                                                                                                   
 INTEGRATE_QUANTITIES             OFF                                                                                                   
 LIKWID                           OFF                                                                                                   
 LOG_LEVEL                        warning                                                                                               
 LOG_LEVEL_MASTER                 info                                                                                                  
 Libxsmm_executable_PROGRAM       /project/k1488/kadek/myLibs/cmakeSeisSol/bin/libxsmm_gemm_generator                                   
 MEMKIND                          OFF                                                                                                   
 MEMORY_LAYOUT                    auto                                                                                                  
 METIS                            ON                                                                                                    
 MINI_SEISSOL                     ON                                                                                                    
 MPI                              ON                                                                                                    
 NETCDF                           ON                                                                                                    
 NUMA_AWARE_PINNING               ON                                                                                                    
 NUMA_ROOT_DIR                    /usr                                                                                                  
 NUMBER_OF_FUSED_SIMULATIONS      1                                                                                                     
 NUMBER_OF_MECHANISMS             0                                                                                                     
 OPENMP                           ON                                                                                                    
 ORDER                            5                                                                                                     
 PLASTICITY_METHOD                nb                                                                                                    
 PRECISION                        double                                                                                                
 PROXY_PYBINDING                  OFF                                                                                                   
 PSpaMM_PROGRAM                   /project/k1488/kadek/myLibs/cmakeSeisSol/bin/pspamm.py                                                
 SIONLIB                          OFF                                                                                                   
 TESTING                          OFF                                                                                                   
 TESTING_GENERATED                OFF                                                                                                   
 USE_IMPALA_JIT_LLVM              OFF                                                                                                   
 easi_DIR                         /project/k1488/kadek/myLibs/cmakeSeisSol/lib64/cmake/easi                                             
 impalajit_DIR                    /project/k1488/kadek/myLibs/cmakeSeisSol/lib64/cmake/impalajit                                        
 netCDF_DIR                       netCDF_DIR-NOTFOUND                                                                                   
 yaml-cpp_DIR                     /project/k1488/kadek/myLibs/cmakeSeisSol/share/cmake/yaml-cpp 
```

### Compile
make -j24


## Configuration for job submission

```bash
#!/bin/bash

#SBATCH --job-name=TPVtest
#SBATCH --partition=workq
#SBATCH --export=ALL
#SBATCH --chdir=/project/k1488/kadek/tpvTest
#SBATCH --output=logs/dfn.o%j
#SBATCH --error=logs/dfn.e%j


#SBATCH --nodes=32
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=64


#SBATCH --time=03:00:00
#SBATCH --no-requeue


#SBATCH --account=k1488

date
echo 'working directory is'
pwd
echo 'printing modules'
module swap PrgEnv-cray PrgEnv-intel
module load cdt

export MP_SINGLE_THREAD=no
export OMP_PLACES="cores(62)"
export OMP_NUM_THREADS=62
export KMP_AFFINITY=compact,granularity=thread

export MPICH_MAX_THREAD_SAFETY=multiple
export XDMFWRITER_ALIGNMENT=8388608
export XDMFWRITER_BLOCK_SIZE=8388608
export SC_CHECKPOINT_ALIGNMENT=8388608
export SEISSOL_CHECKPOINT_ALIGNMENT=8388608
export SEISSOL_CHECKPOINT_DIRECT=1
export ASYNC_MODE=THREAD
export ASYNC_BUFFER_ALIGNMENT=8388608


export LD_LIBRARY_PATH=/project/k1488/kadek/myLibs/ASAGI/build/lib:$LD_LIBRARY_PATH
echo 'setting stripe size'
lfs setstripe -c 144 output
```

