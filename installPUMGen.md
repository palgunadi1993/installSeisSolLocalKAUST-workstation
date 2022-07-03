# Install PUMGen (parallel meshing software) on Shaheen II
This repository is for compiling open-source PUMGen on KAUST Super Computer Facility.

# Compiling PUMGen cmake on Shaheen II

## Use cdl5 environment
ssh cdl5<br/>

## Load necessary modules
module swap PrgEnv-cray PrgEnv-intel<br/>
module load cdt<br/>
module load python/3.8.0-cdl<br/>

## Set path
export HOMESW=/project/k1488/kadek/myLibs/cmakeSeisSol<br/>
export PATH=$HOMESW/bin:$PATH<br/>
export LIBRARY_PATH=$HOMESW/lib:$LIBRARY_PATH<br/>
export LD_LIBRARY_PATH=$HOMESW/lib:$LD_LIBRARY_PATH<br/>
export PKG_CONFIG_PATH=$HOMESW/lib/pkgconfig:$PKG_CONFIG_PATH<br/>
export CMAKE_PREFIX_PATH=$HOMESW:$CMAKE_PREFIX_PATH<br/>
export EDITOR=vi<br/>
export CPATH=$HOME/include:$CPATH<br/>

## Compiling PUMGen with Simmodeler
mkdir simmodeler && cd simmodeler<br/>
Create empty bash file and copy the following script (e.g. `downloadFiles.sh`):<br/>

```bash
#!/bin/bash -ex

EXPECTED_ARGS=4

if [ $# -ne $EXPECTED_ARGS ];then
  echo $#
  echo "Usage: `basename $0` <username> <password> <downloadcode (0 or 1): 0: library 1:documentation><release string ex) 7.1-110613 > "
  exit 1
fi;

username=$1
password=$2
downloadcode=$3
release=$4

#is the release a dev release?
str=$4
i=$((${#str}-3))
last3char="${str:$i:3}"

if [ "$last3char" = "dev" ]; then
    fold='DM'
else
    fold='M'
fi;

# in vi add newlines
# %s/<\/a>/<\/a>\r/g
# to source.php( source code of support download site ) then run the command 
# grep documentation.*zip ~/Downloads/source.php | sed 's/.*documentation\/\([A-Za-z]*.zip\).*/\1/g'
# to extract the zip file names


if [ "$downloadcode" = "1" ]; then
    documentation=( GeomSim.zip GeomSimAcis.zip GeomSimDiscrete.zip FieldSim.zip GeomSimAbstract.zip MeshSimCrack.zip MeshSimAdapt.zip MeshSimAdv.zip MeshSim.zip MeshSimCrack.zip ParallelMeshSimAdapt.zip ParallelMeshSim.zip GeomSimProe.zip GeomSimParasolid.zip GeomSimSolidWorks.zip )
    echo $documentation
else
    documentation=()
fi;

for doc in ${documentation[@]}; do
  wget --user=${username} --password=${password} http://www.simmetrix.com/application/release/${fold}/${release}/documentation/${doc}
done

# run the command
# grep linux64.tgz ~/Downloads/source.php | sed 's/.*release\/\([a-z]*-linux64.tgz\).*/\1/g'
# to extract the tarball names

if [ "$downloadcode" = "0" ]; then
    components=( gmcore-linux64.tgz aciskrnl-linux64.tgz discrete-linux64.tgz fdcore-linux64.tgz gmabstract-linux64.tgz gmadv-linux64.tgz msadapt-linux64.tgz msadv-linux64.tgz mscore-linux64.tgz msparalleladapt-linux64.tgz msparallelmesh-linux64.tgz pskrnl-linux64.tgz )
else
    components=()
fi;

for comp in ${components[@]}; do
  wget --user=${username} --password=${password} http://www.simmetrix.com/application/release/${fold}/${release}/release/${comp}
done
```
### Run the script and download simmodeler
`:$ bash downloadFiles.sh $username $passwd 0 $simmodelerVersion`<br/>
Extract all files with the following script (e.g. `extractFiles.sh`):<br/>
```bash
for filename in *.tgz
do
  tar zxf $filename
done
```
### Run the script and extract files
`:$ bash extractFiles.sh`<br/>
rm *tgz<br/>
cd ..<br/>

# Install PUMI

git clone https://github.com/SCOREC/core.git core<br/>
git submodule update --init<br/>
cd core<br/>

## Setup the configuration
Use the following script to install pumi (e.g. `compile_pumi.sh`):<br/>
```bash
mkdir build && cd build
echo Now in $PWD building with simmetrix
$HOMESW/bin/cmake/bin/cmake .. \
  -DCMAKE_C_COMPILER=cc \
  -DCMAKE_CXX_COMPILER=CC \
  -DCMAKE_C_FLAGS="-O2 -g -Wall" \
  -DCMAKE_CXX_FLAGS="-O2 -g -Wall" \
  -DENABLE_SIMMETRIX=ON \
  -DSIM_MPI="mpich3" \
  -DSIMMETRIX_LIB_DIR=/project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623/lib/x64_rhel7_gcc48/ \
  -DSIMMETRIX_INCLUDE_DIR=/project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623/include/ \
  -DMPIRUN="srun" \
  -DMPIRUN_PROCFLAG="-n"\
  -DPARMETIS_INCLUDE_DIR=/project/k1587/kadek/myLibs/cmakeSeisSolNew/include/ \
  -DCMAKE_INSTALL_PREFIX=/project/k1587/kadek/meshing_software/core \
  -DSKIP_SIMMETRIX_VERSION_CHECK=ON
make -j 24 VERBOSE=1
make install
```
### Run the script and install the pumi
`:$ bash compile_pumi.sh`<br/>
cd ..<br/>

# Install PUMGen
git clone https://github.com/SeisSol/PUMGen.git<br/>
cd PUMGen<br/>
git submodule update --init<br/>

## Setup the configuration
Use the following script to setup PUMGen (e.g. `install_pumgen.sh`):<br/>
```bash
mkdir build
cd build

$HOMESW/bin/cmake/bin/cmake .. -DCMAKE_PREFIX_PATH=/project/k1587/kadek/meshing_software/core \
    -DSIMMETRIX=ON \
    -DSIMMETRIX_ROOT=/project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623/ -DSIM_MPI=mpich3 \
    -DCMAKE_BUILD_TYPE=Release\
    -DCMAKE_C_COMPILER=cc -DCMAKE_CXX_COMPILER=CC
```

$HOMESW/bin/cmake/bin/cmake . (here using hdf5 1.18.21)
```bash
 APF_LIB                          /project/k1587/kadek/meshing_software/core/lib/libapf.a            
 APF_SIM_INCLUDE_DIR              /project/k1587/kadek/meshing_software/core/include                 
 APF_SIM_LIB                      /project/k1587/kadek/meshing_software/core/lib/libapf_sim.a        
 APF_ZOLTAN_INCLUDE_DIR           /project/k1587/kadek/meshing_software/core/include                 
 APF_ZOLTAN_LIB                   /project/k1587/kadek/meshing_software/core/lib/libapf_zoltan.a     
 CMAKE_BUILD_TYPE                 Release                                                            
 CMAKE_INSTALL_PREFIX             /usr/local                                                         
 GMI_INCLUDE_DIR                  /project/k1587/kadek/meshing_software/core/include                 
 GMI_LIB                          /project/k1587/kadek/meshing_software/core/lib/libgmi.a            
 GMI_SIM_INCLUDE_DIR              /project/k1587/kadek/meshing_software/core/include                 
 GMI_SIM_LIB                      /project/k1587/kadek/meshing_software/core/lib/libgmi_sim.a        
 HDF5_C_LIBRARY_dl                /usr/lib64/libdl.so                                                
 HDF5_C_LIBRARY_hdf5              /project/k1587/kadek/myLibs/cmakeSeisSolNew/lib/libhdf5.a          
 HDF5_C_LIBRARY_hdf5_hl           /project/k1587/kadek/myLibs/cmakeSeisSolNew/lib/libhdf5_hl.a       
 HDF5_C_LIBRARY_m                 /usr/lib64/libm.so                                                 
 HDF5_C_LIBRARY_z                 /usr/lib64/libz.so                                                 
 LION_INCLUDE_DIR                 /project/k1587/kadek/meshing_software/core/include                 
 LION_LIB                         /project/k1587/kadek/meshing_software/core/lib/liblion.a           
 LOG_LEVEL                        info                                                               
 MA_INCLUDE_DIR                   /project/k1587/kadek/meshing_software/core/include                 
 MA_LIB                           /project/k1587/kadek/meshing_software/core/lib/libma.a             
 MDS_INCLUDE_DIR                  /project/k1587/kadek/meshing_software/core/include                 
 MDS_LIB                          /project/k1587/kadek/meshing_software/core/lib/libmds.a            
 MESH_SIM_INCLUDE_DIR             /project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623
 MTH_INCLUDE_DIR                  /project/k1587/kadek/meshing_software/core/include                 
 MTH_LIB                          /project/k1587/kadek/meshing_software/core/lib/libmth.a            
 NETCDF                           ON                                                                 
 PARASOLID                        OFF                                                                
 PCU_INCLUDE_DIR                  /project/k1587/kadek/meshing_software/core/include                 
 PCU_LIB                          /project/k1587/kadek/meshing_software/core/lib/libpcu.a            
 SIMMETRIX                        ON                                                                 
 SIMMETRIX_ROOT                   /project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623
 SIM_DISCRETE_LIB                 /project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623
 SIM_MESHING_LIB                  /project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623
 SIM_MESH_TOOLS_LIB               /project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623
 SIM_MODEL_LIB                    /project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623
 SIM_MPI                          mpich3                                                             
 SIM_PARTITINED_WRAPPER_LIB       /project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623
 SIM_PARTITIONED_MESH_LIB         /project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623
 SIM_PARTITIONED_MESH_MPI_LIB     /project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623
 SIM_PS_KRNL_LIB                  /project/k1587/kadek/myLibs/meshing_software/simmodeler/16.0-220623
 TIRPC                            TIRPC-NOTFOUND                                                     
 easi_DIR                         /project/k1587/kadek/myLibs/cmakeSeisSolNew/lib64/cmake/easi       
 impalajit_DIR                    /project/k1587/kadek/myLibs/cmakeSeisSolNew/lib64/cmake/impalajit  
 yaml-cpp_DIR                     /project/k1587/kadek/myLibs/cmakeSeisSolNew/share/cmake/yaml-cpp   
```
Turn NETCDF ON

If your installation is succesful, the executable file should be in `build/pumgen`<br/>
