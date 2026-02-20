# ORCA-External-Tools-V2.0.0 UMA-Installation

### Step 1: Git Pull

Using your terminal, navigate to the directory where you want to install `orca_external_tools`.

Download the package from https://github.com/faccts/orca-external-tools using the following command.

`git pull https://github.com/faccts/orca-external-tools`


### Step 2: Create a virtual Env

Create a virtual environment in the system. I chose to create it inside the downloaded `orca-external-tools` directory.

`virtualenv -p python3.12 orcatools`

This will create a `venv` named `orcatools` inside the `orca-external-tools` directory.

### Step 3: Install the package

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

To use the client-server model of UMA, initialize a UMA client using the following command.

`/home/fslcollab286/orca-external-tools/bin/oet_server uma &`

This will initialize a server in the login node, named `oet_server`. Use the command `top` in the terminal to view the active server and its PID.

Once the server is active, submit the following job in the same way as before. The only difference in the new input is the `ProgExt` path, which is now directed to `oet_client`.

    ! ExtOpt sp
    
    %method
    ProgExt "/home/fslcollab286/orca-external-tools/bin/oet_client"
    end
    
    * xyz 0 1
    H 0.0 0.0 0.0
    H 0.0 0.0 0.9
    *

Once the calculation is finished, do not forget to kill the server in your login node using the command `kill PID`


