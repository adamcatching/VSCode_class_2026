# Singularity

Anaconda containers break, often due to changes in underlying packages that support the packages you actually use. For true reproducibility, we want a snapshot of when you built your environment. 

![https://m.xkcd.com/1987/](images/python_environment_XKCD.png)

[Singularity](https://docs.sylabs.io/guides/3.5/user-guide/introduction.html) builds a 'container' around your current environment and creates Linux image file. Whenever you run Singularity in the future using this container essentially runs a mini-linux kernel with all the software you had previously installed. 

The upside is that all future computation will run exactly as you saved it, either by running code within `singularity shell my_first_env.sif` or by using this `my_first_env.sif` as the environment in your Snakemake rule.

![Singularity](images/Singularity.png)

## Build a Singularity `.def` file

Below is a good starting point if you want to turn your standard Anaconda file into a Singularity container. Simply replace `my_first_conda` with the name of your Anaconda environment. This `my_first_env.def` file is used as the instruction manual on how to build your container that runs your Anaconda environment.

```
Bootstrap: docker
From: condaforge/miniforge3

%environment
    export LC_ALL=en_US.utf-8
    export LANG=en_US.utf-8
    export LANGUAGE=en_US.UTF-8
    # Set BASH_ENV which points to init file loaded during non-interactive shell
    export BASH_ENV=/opt/etc/bashrc
    # Force loading of copied bashrc for interactive shell
    source /opt/etc/bashrc
%files
    my_first_conda.yml
%post
    # Ensure locales packages installed
    apt-get -y update
    apt-get install -y locales locales-all

    # Create target dir for non-root-accessible bashrc
    mkdir -p /opt/etc

    # Define environment name. Doesn't really matter as it's auto-loaded regardless
    ENVNAME='my_first_conda'

    # Create environment using mamba
    conda env create -f my_first_conda.yml \
    
    # Finalize bashrc file
    echo "#! /bin/bash\n\n# script to activate the conda environment" > ~/.bashrc 
    conda init bash
    echo "echo \"Activating ${ENVNAME}\"" >>  ~/.bashrc
    echo "\nconda activate ${ENVNAME}" >> ~/.bashrc

    # Copy bashrc to non-root-accessible location
    cp ~/.bashrc /opt/etc/bashrc

# %runscript
    exec /bin/bash "$@"
```

## Use Sylabs remotely to build image

As Biowulf does not let users have sudo permissions, SyLabs can be used as a remote host to create the Singularity Image File (`.sif`). Create an account, go to [cloud.sylabs.io](https://cloud.sylabs.io/dashboard), login go to access tokens, then create access tokens and copy to your clipboard. Back on Biowulf terminal, type module load singularity, then:
 `singularity remote login SylabsCloud` and paste the token when prompted. 
 
Now when the `--remote option` is used with Singularity, SyLabs will be used as a remote host to create the image. *Note: There is a limit for how many minutes of image building on Sylabs (500 minutes), and the total size of the image hosted, so make sure you double check the definition file should work.

Build the image through SyLabs by typing:
 `singularity build --remote my_first_env.sif my_first_env.def`

## Save and use other containers with Apptainer

To remotely save your containers online, we can't use GitHub due to the size of these files. Instead, we use (Quay.io)[https://quay.io/]. Login, make an account, and we can then save our containers remotely.

![Apptainer](images/apptainer.png)

To upload your file use the module [apptainer.org](https://apptainer.org/). Apptainer is a schism from Singularity, where certain functions only work with one or another. We use Apptainer to upload and download containers from Quay.io.

Load Apptainer:
 `module load apptainer`

Log into Quay.io apptainer:
 `remote login --username USERNAME docker://quay.io`

Upload the repository:
 `apptainer push my_first_env.sif oras://quay.io/USERNAME/my_first_env:0.1`

The suffix at the end of the environment name lets Quay.io know which version of your container is being uploaded. Using this version control method allows for you to make changes to your environment and reupload later.

If you want to use another container, use the `pull` command in place of `push`. Apptainer allows for you to read other kinds of containers, like Docker, by substituting the pull location:

`apptainer pull --disable-cache some_docker_image.sif docker://DOCKER_USER_NAME/some_docker_image:latest`

We add the flag `--disable-cache` to not store extra cache data in your home directory, which will quickly become full with cache if you don't use this flag.