# ORCA-External-Tools-V2.0.0 UMA-Installation

### Step 1: Clone Pull

Using your terminal, navigate to the directory where you want to install `orca_external_tools`.

Download the package from https://github.com/faccts/orca-external-tools using the following command.

`git clone https://github.com/faccts/orca-external-tools`


### Step 2: Create a virtual Env

Create a virtual environment in the system. I chose to create it inside the downloaded `orca-external-tools` directory.

`virtualenv orcatools`

This will create a `venv` named `orcatools` inside the `orca-external-tools` directory.

### Step 3: Install the package

Activate the new `env`

`source /home/fslcollab286/orca-external-tools/orcatools/bin/activate`

Install the tools using the following command.

`python install.py --venv-dir orcatools/ -e uma` 

Here, `-e uma` installs the necessary additional dependencies. This process may take some time, depending on your internet speed.

### Step 4: Activate the env

Use the following command to activate the `venv`.

`source /home/fslcollab286/orca-external-tools/orcatools/bin/activate`

### Step 5: Test the UMA installation

The tests in the `orca-external-tools/tests/uma/` directory did not function correctly in the FSL environment.

So, I have created an ORCA input file and tested it. Use the following input file to test the installation.

    ! ExtOpt sp
    
    %method
    ProgExt "/home/fslcollab286/orca-external-tools/bin/oet_uma"
    end
    
    * xyz 0 1
    H 0.0 0.0 0.0
    H 0.0 0.0 0.9
    *

Run this job in your local directory using the following command.

`/home/fslcollab286/orca_6_1_1/orca h2.inp > h2.out &`

When running the job for the first time, it takes some time to download the UMA model. This only happens during the initial run; subsequent jobs will load the already downloaded model.


### Step 6: Test the Client-Server Interface

To use the client-server model of UMA, initialize a UMA server using the following command.

`/home/fslcollab286/orca-external-tools/bin/oet_server uma &`

The server will be initialized in the login node, named `oet_server`. Use the command `top` in the terminal to view the active server and its PID.

Once the server is active, submit the following job in the same way as before. The only difference in the new input is the `ProgExt` path, which is now directed to `oet_client`.

    ! ExtOpt sp
    
    %method
    ProgExt "/home/fslcollab286/orca-external-tools/bin/oet_client"
    end
    
    * xyz 0 1
    H 0.0 0.0 0.0
    H 0.0 0.0 0.9
    *

`/home/fslcollab286/orca_6_1_1/orca h2.inp > h2.out &`

Once the calculation is finished, do not forget to kill the server in your login node using the command `kill PID`


### Step 7: Test to see if this can be run using salloc

To keep the server longer for multiple jobs, request salloc via the following.

`salloc --time 1:00:00 --nodes 1 --ntasks 4 --gpus h200:1 --mem 8g`. Here h200 gpu is requested because the latest fairchem-core only works with modern gpus. 

Activate the `env`

`source /home/fslcollab286/orca-external-tools/orcatools/bin/activate`

Initialize a UMA server using the following command.

`/home/fslcollab286/orca-external-tools/bin/oet_server uma -n 4 &`   Here -n 4 is needed to request 4 Procs for the server. Otherwise, a job requesting 4 procs will not work.

The server will be initialized in the login node, named `oet_server`. Use the command `top` in the terminal to view the active server and its PID.

To run calculations using multiple processors, use MPI. Initialize them using the following,

`export PSM2_CUDA=1`

`export OMP_NUM_THREADS=4`

`export OMP_STACKSIZE=4G`

`export PATH="/apps/openmpi/5.0.3/gcc-14.1.0_cuda-12.5_ucx-1.17.0/bin:$PATH"`

`export LD_LIBRARY_PATH="/apps/openmpi/5.0.3/gcc-14.1.0_cuda-12.5_ucx-1.17.0/lib:$LD_LIBRARY_PATH"`

Now, submit calculations on the head node as usual. The only caveat is that it works fine with the CPU, but with the GPU, it is throwing some errors. `torch.AcceleratorError: CUDA error: no kernel image is available for execution on the device`

Need to fix this!

To change cpu/gpu, go to calculator/uma.py `/orca-external-tools/src/oet/calculator/uma.py` line 188.

