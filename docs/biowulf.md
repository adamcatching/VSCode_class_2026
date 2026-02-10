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

Once you are familiar with VSCode, navigate to your 

## Write a script

Once you have

## Submit a job