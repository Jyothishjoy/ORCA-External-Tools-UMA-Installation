# ORCA-External-Tools-V2.0.0 UMA-Installation

### Step 1: Git Pull

Go to the directory where you want to install orca_tools, and download the package from https://github.com/faccts/orca-external-tools.

`git pull https://github.com/faccts/orca-external-tools`


### Step 2: Create a virtual Env

Create a virtual environment in the system. I chose to make one inside the downloaded `orca-external-tools` directory.

`virtualenv -p python3.12 orcatools`

This will create a `venv` named `orcatools` inside the `orca-external-tools` directory.

### Step 3: Install the package

Install the tools using the following command.

`python install.py --venv-dir orcatools/ -e uma` 

Here `-e uma` is used to additionally install the required dependencies. This step may take some time depending on your internet speed.

### Step 4: Activate the env

Use the following command to activate the `venv`.

`source /home/fslcollab286/orca-external-tools/orcatools/bin/activate`

### Step 5: Test the UMA installation

The tests given in the `orca-external-tools/tests/uma/` did not work in the FSL.

I have made an orca input file and tested it. Use the following input file to test the installation.

    ! ExtOpt sp
    
    %method
    ProgExt "/home/fslcollab286/orca-external-tools/bin/oet_uma"
    end
    
    * xyz 0 1
    H 0.0 0.0 0.0
    H 0.0 0.0 0.9
    *

When running the job for the first time, it takes some time to download the UMA model. This only happens during the initial run; subsequent jobs will load the already downloaded model.



