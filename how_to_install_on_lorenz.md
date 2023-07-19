# Installation Climber on Lorenz

In order to install climate model Climber on Lorenz please follow the next instructions:

First log in on Lorenz. [Here](https://github.com/IMAU-oceans/Lorenz/tree/main#login) are guidelines on how to do this.

Then on the commandline type:

    mkdir -p models/climber
    cd models/climber

Then open a web browser and download the latest version of Climber at: https://zenodo.org/record/7898797

Today (July 15, 2023) the latest version is version 4 with date may 5, 2023.  
You can find and download it on the right side of the zenodo web page.

The downloaded zip file will probably end up in your 'Downloads' directory on your local PC.  
Open a terminal and go to this directory, e.g.

    cd Downloads

Then copy the zip file directly (via server Gemini) to your directory on Lorenz by typing:  

    scp -o ProxyJump=kliph103@gemini.science.uu.nl CLIMBER-X_v1.0cc_rev.zip kliph103@lorenz.science.uu.nl:/nethome/kliph103/models/climber

*In your case replace the string kliph103 by your own SolisID*

Then go to the terminal where you were logged in on Lorenz again. Make sure that you are in ~/models/climber and type:

    unzip CLIMBER-X_v1.0rev.zip

You also need the library LIS (Library of Iterative Solvers for Linear Systems) which you can download from http://www.ssisc.org/lis/

Choose not the latest version but the one before that (2.1.2)

From the 'Downloads' directory on your local machine type:

scp  -o ProxyJump=kliph103@gemini.science.uu.nl lis-2.1.2.zip  kliph103@lorenz.science.uu.nl:/nethome/kliph103/models/climber/

*Again replace the string kliph103 by your own SolisID*

Then on Lorenz in ~/models/climber type:

    unzip lis-2.1.2.zip
    cd lis-2.1.2

After that make sure that all the needed (Intel) compilers, packages (cdo) and libraries (netCDF and mpi) are available.  
You can load them as modules by typing: 

    module purge
    module load cdo
    module load netCDF/Intel/4.9.2
    module load mpi/2021.3.0
    module load compiler/2021.3.0

Then configure and compile the LIS library by typing:

    ./configure --prefix=`pwd` MPICC=mpiicc --enable-mpi
    make    

This generates a directory test and executable libtool

Check if everything went OK by typing:

    make check

This checks if a linear solver works and should end with the line: 1 test passed    

Ok now you are ready to build the Climber executable.  Matteo Willeit and Alex Robinson from the Potsdam Institute for Climate Impact (PIK)
advice to do this with the Intel compiler. We first need a so called Makefile containing all the compiler and library locations and settings.
This Makefile will be generated with a python script that on its turn needs a config file. There is a template config file for the Intel compiler 
but it needs a few modifications. Implement them by typing:

    cd ~/models/climber/config
    cp pik_ifort lorenz_ifort

Then open the file <code>lorenz_ifort</code> with an editor. Probably the easiest editor to use on Lorenz is **nano**. Also **vi** can be used but it has a steeper learning curve. Simply create (or edit) the file by typing:

    nano lorenz_ifort 

or 

    vi lorenz_ifort

and set:

    INC_NC  = -I/opt/intel/netcdf/netcdf-fortran-4.6.1/include
    LIB_NC  = -L/opt/intel/netcdf/netcdf-fortran-4.6.1/lib -lnetcdff -L/opt/intel/netcdf/netcdf-c-4.9.2/liblib/.libs -lnetcdf

Also make sure that one line above the line <code>LIB_MKL = -L${MKLROOT}/lib/intel64 -lmkl_intel_lp64 -lmkl_intel_thread -lmkl_core -liomp5 -lpthread</code> you add a line:

    MKLROOT = /opt/intel/oneapi/mkl/2021.3.0

After this create the Makefile (needed for compilation) by typing:

    cd ..
    python config.py config/lorenz_ifort
    make clean
    make climber climate_only=1

This takes about 10 minutes but after this an executable <code>climber.x</code> is created in the current directory.

You are now ready to do climate runs with climber :) 

 [Here](https://github.com/IMAU-oceans/Lorenz/tree/main#login) are guidelines on how to do this.

Note that there is also the option to leave out <code>climate_only=1</code> in the make command above. This does a full compilation with extra components but at the moment this gives errors during compilation because files like:

    bgc_model.f90
    vilma.f90 

for the ocean biogeochemistry model (bgc) and viscoelastic lithosphere and mantle model (vilma) are missing. The climber zip file (also all other versions on the zenodo website) containing all the code do contain files <code>bgc_model_dummy.f90</code> and <code>vilma_dummy.f90</code> which are used when adding the <code>climate_only=1</code> in the make command. If you also want to use the bgc and vilma model please contact Michael Kliphuis. He can ask the developers to sent us the missing files.  


    












