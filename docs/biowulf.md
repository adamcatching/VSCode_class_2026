# Biowulf

![Biowulf](images/Biowulf.png)

## Background
Biowulf is the high-performance computation (HPC) cluster at the NIH. Most questions one can have about using Biowulf are answered in their [user guides](https://hpc.nih.gov/docs/user_guides.html). This guide assumes one has never used Biowulf before and serves as the starting point, which the rest of the guide will consist of working on Biowulf using a NIH computer. As Biowulf is a linux system running [slurm](https://slurm.schedmd.com/documentation.html) job management, parts will be applicable to other institution level HPCs.

## Getting an account

To get an account on Biowulf you first need a NED account, as well as a PI and IC that agrees to add you to their Biowulf group. Once a PI has agreed to add you to their account, fill out [this form](https://hpcnihapps.cit.nih.gov/auth/accounts/account_request.php), which must be accessed through the NIH network, or through a NIH VPN. Once the form is submitted, your PI must approve the account via an automated email. A thorough walkthrough is found [here](https://hpc.nih.gov/docs/accounts.html).

WARNING: If you don't use your Biowulf account for 60 days it will be deactivated. Make sure if you have an account you are at least somewhat consistently using it.

## Logging onto Biowulf

There are many ways to interact with Biowulf, for brevity the terminal interface and interactive interface will be discussed.  

### Terminal

Once you have an account, you can access your Biowulf account through terminal. On a Mac use the terminal, on PC use PowerShell to connect to Biowulf via `ssh username@biowulf.nih.gov`. From there you can move around using Bash commands such as `cd`, `pwd`, and `ls`. 

If you want to move files to or from Biowulf, a simple way to move files is `scp username@helix.nih.gov:/path/to/file path/on/your/device`. It is important you use the `helix` value instead of `biowulf` when transferring files, as one is built for computation, and the other one is built for file transfer.

### Interactive

While it is possible to activate a [tunnel](https://hpc.nih.gov/docs/tunneling/) for interfacing with Biowulf, a simplified web-browser based method has been created. 

[hpcondemand.nih.gov](https://hpcondemand.nih.gov/) is a convenient, interactive platform for interfacing with Biowulf. Once you log in there is a link at the bottom of the screen called [all available apps](https://hpcondemand.nih.gov/pun/sys/dashboard/apps/index). There you can find a list of all the methods to interface with Biowulf, alloting time of the session, cpu/memory usage, and even GPUs to allocate. 

![HPCOnDemand](images/HPCOnDemand.png)

We recommend using VSCode, as this Integrated Development Environment (IDE) allows for using Python, R, Bash, and pretty much any other programming language. In addition, Jupyter notebooks can be run within VSCode using a pluggin, with all the optionality within Jupyter. For an introduction to VSCode, look at the [VSCode section](vscode.md). 

Once you are familiar with VSCode, navigate to the file icon at the top left hand side. There you can select the `open folder` button, and navigate to a folder you have access to, or create one if you are starting a new project. From there you can begin writing scripts, running analysis, and submitting jobs to the Biowulf Slurm job scheduler.

## Write and submit a script

Within VSCode you can use the interactive terminal the same way you can via `ssh`. This includes loading some of the many modules that are already on Biowulf. To load `fastqc` for instance and run it on a folder with some fastqs, in terminal you write:

` USER$ module load fastqc`  
` USER$ fastqc --output='path/to/some/fastqs' 'path/to/fastq/metrics'`

However, this is not the preferred method for job submission on Biowulf, or for reproducible science. This code won't be saved if you want to re-run it, and it doesn't properly allocate Biowulf resources. The better method is to create a bash script called `fastqc.sh` as follows:

```
#!/bin/bash 

# Lines with # symbol will be ignored except when followed by SBATCH
# The lines below define the resources and parameters of the job you are submitting

#SBATCH --cpus-per-task 1                              # Number of threads you are using
#SBATCH --mem-per-cpu=16                               # Gb of memory for this job
#SBATCH --time 3:00:00                                 # Max time for job (HH:MM:SS)
#SBATCH --gres=quick                                   # What kind of node (quick, largemem, GPU...)
#SBATCH --output=/data/$USER/path/to/output/file.out   # Where to save output messages from job
#SBATCH --input=/data/$USER/path/to/error/file.err     # Where to save error files

# Load a package
module load fastqc

# Run the command
fastqc --output='path/to/some/metrics' 'path/to/some/fastqs'
```

Once you have your script written, you can easily submit it as a job with the terminal command:

> bash fastqc.sh  

The benefit is that the bash script serves as a record for data processing or analysis. In addition, the output file can serve as a time-stamp of when you ran the job. But, more realistically, there will be errors with you script at some point and troubleshooting will be required. The `.err` file serves as a record of what went wrong, so you can make modifications of your bash script. Maybe this script serves as a jumping off point for a more advanced analysis. 

Regardless of how you are using Biowulf, bash scripts are a critical for remembering what you did, when, and how.

## Jupyter notebooks 

For more interactive analysis prototyping, while recording your work, it is highly recommended to use Jupyter notebooks. Jupyter notebooks allow for multiple programming and text languages to be used in a single format, including:

- Python (juPYter)
- R (jupyteR)
- Terminal (jupyTEr)
- Julia (JUpyter)
- Markdown 

A jupyter notebook can be created just like any other file, by going to file > new file > Jupyter Notebook. Once a new notebook is made, code and text can be entered into each cell. It is best if you treat notebooks like a lab notebook, where each day has a separate file and you don't go back and erase something that doesn't work. Keep the mistakes, and save the code that does work in either a special Jupyter notebook or a script to be run as a job. This process then becomes documented and iterative, recording each day's work and keep the parts that work in a pipeline you begin to build.

To run code in a Jupyter notebook cell, you will need a kernel. For the purposes of this walkthrough, we will use [Anaconda environments](anaconda.md)
