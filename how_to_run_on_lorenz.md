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

Go to the directory by typing:

    cd OUTDIR16
    
Also the executable <code>climber.x</code> is copied here as well as directories <code>input</code>, <code>maps</code> and <code>restart</code> containing needed data files. Finally a job script is created called: <code>job.submit</code>. This script does not contain the correct <code>#SBATCH </code>keywords yet. For now please modify the submit script yourself by opening it with an editor. Probably the easiest editor to use on Lorenz is **nano**. Also **vi** can be used but it has a steeper learning curve. Simply edit the file by typing:

    nano job.submit

or 

    vi job.submit

Then make sure that the script looks like this:

    #!/bin/bash

    #SBATCH --job-name=climberX
    #SBATCH --output=./out.out
    #SBATCH --error=./out.err

    # Make sure all cores we get are on one node
    #SBATCH --nodes=1
    
    # We're a 1-task job
    #SBATCH --tasks-per-node=1
    #SBATCH --time=120:00:00

    # This is how many CPUs our task will require 
    #SBATCH --cpus-per-task=16

    export OMP_PROC_BIND=true  # make sure our threads stick to cores
    export OMP_NUM_THREADS=16  # matches how many cpus-per-task we asked for

    export OMP_STACKSIZE=512M   
    export MKL_NUM_THREADS=16

    export GMON_OUT_PREFIX=.//gmon

    ulimit -s unlimited
    ulimit -v unlimited
    ulimit -a

    srun ./climber.x ./


After this you can start the run on 16 cores by typing:

    sbatch job.submit

To check if your job is running type:

    squeue

If you want to kill the job with e.g. jobid 123456 type:

    scancel 123456

to check more information about the job, for instance where it was submitted from (WorkDir) type:

    scontrol show job 123456   


P.S. If you don't want having to modify the job.submit script all the time after creating a new case with the 
**job_climber** line then modify the file **job_climber** as follows:

Change line 643 into:

    #SBATCH --time=120:00:00

Also change line 669 into:

    """  % (outfldr,outfldr,threads,threads,threads,outfldr,outfldr,outfldr)
    
Finally remove lines 633, 635, 638, 644-645, 652, 659-662 and 664 i.e lines:
    
    #SBATCH --qos=%s
    #SBATCH --account=%s 
    #SBATCH --mail-user=%s@pik-potsdam.de
    #SBATCH --no-requeue
    #SBATCH --constraint=%s
    #export OMP_STACKSIZE=256M
    #ulimit -c unlimited
    #ulimit -s unlimited
    #ulimit -d unlimited
    #ulimit -m unlimited
    #ulimit -f unlimited

    
    


 
