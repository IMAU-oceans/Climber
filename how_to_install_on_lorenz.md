# Installation Climber on Lorenz

In order to install climate model Climber on Lorenz please follow the next instructions:

First log in on Lorenz. [Here](https://github.com/IMAU-oceans/Lorenz/tree/main#login) are guidelines on how to do this.

Then on the commandline type:

    mkdir -p models/climber
    cd models/climber

Then open a web browser and download the latest version of Climber at: https://zenodo.org/record/7898797

Today (July 19, 2023) the latest version is version 4 with date may 5, 2023.  
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






