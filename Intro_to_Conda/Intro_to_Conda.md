# Introduction to Conda

Created February 25, 2019      
Updated February 7, 2020

Created by Elizabeth Miller (millere@umn.edu)  

## Table of Contents

- [Introduction](#Introduction)
- [Running conda on MSI](#Running-conda-on-MSI)
- [Managing conda environments](#Managing-conda-environments)
  * [Creating an environment](#Creating-an-environment)
  * [Deactivating an environment](#Deactivating-an-environment)
  * [Viewing a list of your environments](#Viewing-a-list-of-your-environments)
  * [Viewing packages in an environment](#Viewing-packages-in-an-environment)
  * [Removing an environment](#Removing-an-environment)
- [Getting help](#Getting-help)

<!-- toc -->

## Introduction

Many of software packages we want to use are already available on MSI and simply needed to be loaded using the `module load` command. However, other software we use are not available through MSI. While we could manually download and install these packages and all of their dependencies, this can be incredibly difficult and time-consuming.

For example, to properly install the software package Snippy (a SNP-calling program), not only do you need the actual Snippy program, but you also need to install 299 additional dependency programs and scripts. It could get even more complicated when you need to install dependency programs for your dependency programs!

Many software packages will give you instructions on how to comprehensively install them (e.g. HomeBrew), but these instructions typically require you to have system administrator privileges. While you probably have administrator privileges on your own computer, you do not have them on MSI. 

To get around these issues, I like to use an open source package management system called [conda](https://conda.io/en/latest/). This program will quickly install, run, and update packages and their dependencies. It also allows you to create special directories, called *environments*, that contain a specific collection of packages (i.e. programs) you have installed. For example, maybe you want to create one environment for Snippy and all of its dependencies and another environment for IQ-Tree and all if its dependencies. You can easily *activate* or *deactivate* environments, which is how you switch between them. For instance, if you wanted to use the Snippy program, you would activate the `snippy` environment, which would give you access to the Snippy program and all of its dependencies. When you’re done with Snippy, you would simply deactivate the environment. You would then be ready to use a different program by activating that program’s environment.

[Anaconda](https://anaconda.org) is a website where conda packages are curated and stored. To see if a particular program is available for download via conda, you can search for it on the Anaconda Cloud website. *Conda packages* (also called *recipes*) are compressed tarball files that contain all the system-level libraries, Python or other modules, executable programs, and other components.

[Miniconda](https://docs.conda.io/en/latest/miniconda.html) is a free minimal installer for conda. It is a small version of Anaconda that includes only conda, Python, the packages they depend on, and a small number of other useful packages. 

Conda packages are downloaded to environments you create from *remote channels*, which are URLs to directories containing conda packages. The main *defaults channel* has a large number of general packages, but users can add additional channels to install software not available in the defaults channel. In particular, [bioconda](https://bioconda.github.io) is a channel specializing in bioinformatics software. We will use this channel a lot.

## Running conda on MSI

#### 1.	Load the `python3` MSI module *You must do this step every time you want to use conda.*

Conda is included in all versions of Anaconda and Miniconda. On MSI, the default version of [Python 3](https://www.python.org) includes Anaconda, so to use conda you simply have to load the python module.

```
$ module load python3
```

#### 2.	Set up channels. *You only do this step the first time you load conda.*

After installing conda you will need to add the `bioconda` channel as well as the the channels bioconda depends on. It is important to add them in this order so that the priority is set correctly (that is, `conda-forge` is highest priority).

```
$ conda config --add channels defaults
$ conda config --add channels bioconda
$ conda config --add channels conda-forge
```

Now you’re ready to start creating and using environments!

## Managing conda environments

Below we go over the basics of managing conda environments, including creating, activating/deactivating, and removing. More detailed information on managing conda environments can be found [here](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).

### Creating an environment

Once you have loaded conda (`module load python3`), you can create environments and add programs to them. Let's create an environment for the SNP-calling program, [Snippy](https://github.com/tseemann/snippy).

#### 1.	Create an environment

You can call this environment whatever you would like to, but I usually call it after whatever software I plan to add to it. In this case, let’s call the environment `snippy`.

```
$ conda create -n snippy
```

The `-n` option specifies the name you want to give the environment. `--name` would also work here.

#### 2.	When conda asks if you want to proceed, type `y`:

```
proceed ([y]/n)?
```

#### 3.	Activate the environment

Now that we have created the environment `snippy`, let’s activate it so we can use it. 

```
$ source activate snippy
```

Notice that once the environment is activated, you should see the environment name before the command line prompt.

#### 4.	Install packages

To install the package(s) you want in your environment, it’s always best to go to the [Anaconda Package Repository](https://anaconda.org/anaconda/repo), search for the package you want, and use the installation code they provide. Sometimes this process doesn’t work so you may have to do some searching to find the installation code that works for you. You can also search for packages via command line with the `conda search` command, but it won’t give you specific installation instructions.

However you end up getting the installation code, once you have it, enter it into the command line. To download the program Snippy and all of its dependencies, the [Snippy instructions](https://anaconda.org/bioconda/snippy) on Anaconda recommend the following code:

```
(snippy)$ conda install -c bioconda snippy
```

When conda asks if you want to proceed, type `y`:

```
proceed ([y]/n)?
```

Once all the packages are loaded, you can now run Snippy. In the future, any time you want to run Snippy, all you need to do is activate the `snippy` environment (`source activate snippy`) and you’re ready to go! 

If you’re curious, you can find all of your created environments and associated downloaded packages at the following path:  `~/.conda/envs`. For the `snippy` environment, we could see everything downloaded by entering:

```
$ ls ~/.conda/envs/snippy
```

#### Creating an environment (a slightly faster way)

In the previous section, we created an environment, activated that environment, and then added specific software to it. You can also create an environment and install specific packages in the same command. For example, let’s create an environment called `iqtree` and install the program [IQ-Tree](http://www.iqtree.org) along with all of its dependencies using the [Anaconda instructions](https://anaconda.org/bioconda/iqtree):

```
$ conda create -n iqtree -c bioconda iqtree
```

The `-n` option provides the option for the name of the environment (in this case, `iqtree`) and the `-c` flag indicates that we want to install all of the iqtree package software, which is located in the `bioconda` channel.

### Deactivating an environment

To deactivate an environment, simply type:

```
$ source deactivate
```

Notice that once the environment has been deactivated, the environment name before the command line prompt should disappear.

### Viewing a list of your environments

To see a list of all the environments you have created use:

```
$ conda info --envs
```

or

```
$ conda env list
```

### Viewing packages in an environment

To view a list of all the packages loaded into a specific environment use (example is with `snippy`), type:

```
$ conda list -n snippy
```

### Updating an environment

You may need to update your environment for a variety of reasons. For example, it may be the case that one of your core dependencies just released a new version. To do this, go to the [Anaconda Package Repository](https://anaconda.org/anaconda/repo), search for the package you want, and use the update code they provide (example is with `snippy`):

```
(snippy)$ conda update snippy
```

When conda asks if you want to proceed, type `y`.

If the `snippy` environment is not currently active when you run this, you can simply add to the code `-n snippy` and conda will then know to update the Snippy software in the `snippy` environment:

```
$ conda update snippy -n snippy
```

When conda asks if you want to proceed, type `y`.

### Removing an environment

To remove an environment and all associated packages use (example is with `snippy`):

```
$ conda remove -n snippy --all
```

When conda asks if you want to proceed, type `y`.

## Getting help

More detailed information on managing conda environments can be found [here](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html). The MSI help desk (help@msi.umn.edu) can also be a great resource.
