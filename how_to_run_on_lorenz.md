# Run Climber on Lorenz

Here are instructions for running climate model Climber on Lorenz:

First make sure that you have all the needed software by typing:

    module purge
    module load cdo
    module load netCDF/Intel/4.9.2
    module load mpi/2021.3.0
    module load compiler/2021.3.0
    module load miniconda/3
    
There are very useful python scripts that help setting up a run. They make use of the relatively old version python2.
First set up a (conda) environment that enables you to use this version by typing:

    conda create --name py2  python=2.7
    conda activate py2

Then type:

      cd ~/models/climber

 Now suppose you want to do a climber run on 16 cores and you want the model to generate output in a subdirectory called <code>OUTDIR16</code>, then type: 

     ./job_climber -s -f -o OUTDIR16 -j parallel -n 16

<code>-s</code> means that you want to submit a job to the queue and not run it interactively on the main node.  
<code>-f</code> means force, this will initiate your job without confirmation from the user.  
<code>-j</code> stands for the job type which can be <code>serial</code> or <code>parallel</code> multithread (openmp).  
<code>-n</code> stands for the number of threads for OpenMP parallelisation, only with parallel job_type.  

Now automatically a directory <code>OUTDIR16</code> is created with all needed <code>.nml</code> files containing general parameters like the number of simulation years <code>nyears</code> in <code>control.nml</code> or the number of ocean layers <code>nlayers</code> in <code>ocn_par.nml</code>.
Also the executable <code>climber.x</code> is copied here as well as directories <code>input</code>, <code>maps</code> and <code>restart</code> containing needed data files. Finally a job script is created called: <code>job.submit</code>. This script does not contain the correct #SBATCH keywords yet. I (Michael Kliphuis) will change the <code>job_climber</code> python script later such that they will be correct. For now please modify the python script yourself with 



 
