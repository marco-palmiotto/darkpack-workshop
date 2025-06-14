# README first

If you are having problems with the installation, you can contact me even before the 
workhop, at the address marco.plamiotto@fys.uio.no. 

I noticed that github does not display some LateX expressions in markdown, so, the 
tutorial part, download this file and open it locally.
# Resources

- MARTY repository: https://github.com/docbrown1955/marty-public
- MARTY website: https://marty.in2p3.fr/
- DarkPACK repository: https://github.com/marco-palmiotto/darkpack-cmake-public
- Dockerhub:
  - Dev: https://hub.docker.com/repository/docker/marcopalmiotto/darkpack-dev/general
  - Scan: https://hub.docker.com/repository/docker/marcopalmiotto/darkpack-scan/general
  - Deploy: https://hub.docker.com/repository/docker/marcopalmiotto/darkpack-deploy/general
 
# Configuration

## Local installation
You can install MARTY by following the instructions in the MARTY repository. 
The MARTY website provides the doxygen documentation and some examples. 
In `DarkPACK`, I prefer to use `ninja` instead of `make` to compile it, so make sure 
to have the package installed, otherwise you will need to change the option in 
`configure.sh` before calling `cmake` the first time.

We will provide some jupyter notebooks for data display. You can use poetry, if you 
have it installed (see below), or install the following python packages manually:
```
ipykernel jupyter matplotlib notebook numpy pandas pandoc scipy
```

## Container usage

### General information
If you are using Visual Studio Code or CLion, and you have docker or podman installed, you might
make use of the containers, thanks to the devcontainer extension of the IDEs:
  - Reccommended IDE: Visual Studio Code (or CLion)
  - Needed extension: Devcontainer
  - Reccommended container manager: docker (or podman is also an option if you know how to configure the IDE accordingly)

All the container images ship with all the dependencies needed by MARTY, and come with MARTY pre-installed
and configured. The differences among the different images are the following:
 - Deploy: MARTY dependencies + MARTY
 - Scan: MARTY dependencies + MARTY + CMA-ES (library for function optimisation)
 - Dev: MARTY dependencies + MARTY + CMA-ES + python + poetry + profiling tools

In the Darkpack repository there is the `.devcontainer` folder, which contains all the Dockerfiles for all 
the images. By default, it will use the image dev-fedora41. If you wish to change it, you just need
to change the corresponding line in the configuration file `.devcontainer/devcontainer.json`.

### Optional step

Even though it is completely optional, we reccommend to have already pulled the 
image before opening the repository in the devcontainer:

    docker pull marcopalmiotto/darkpack-dev:fedora41

In this way you can save approximately **10 minutes** of full CPU time, needed to 
build the image locally, if it has not been pulled before. 

### Configuration
Now, provided that you have cloned the darkpack-cmake-public repository, ans installed
the required extensions in the IDE, you can launch your IDE in the folder. The IDE
will detect the devcontainer configuration, and ask if you want to open the folder 
inside the devcontainer.

By chosing to do so, it will build the image from the dockerfile already present 
in the repository, which, by default is the dev one. This operation will 
take approximately **10 minutes** of full CPU time. Tho shorten this time, you might `docker pull` the 
image from dockerhub, as described in the optional step.

### Other options
If you want to use only Darkpack, and use your local installation of python (or whatever other program
you prefer) to do the plots, you can use the deploy image: 
    
    docker pull marcopalmiotto/darkpack-deploy:fedora41

and then use python, as described in the local installation instructions.
If you prefer using python in the container, we reccommend to use the Dev image:

    docker pull marcopalmiotto/darkpack-dev:fedora41

## Python setup with poetry

If you are using the image with python, or if you have poetry installed, you can quickly sey up 
a notebook session. You can run 
```bash
cd <folder_with_results>
poetry run jupyter-notebook
```
This will start a jupyter-notebook session in the environment defined by poetry. 
If this does not open immediately a tab in your browser, it is possible to use the link in the format

    http://localhost:8888/tree?token=...

and this should open a tab in your local browser, where you can use the instance of jupyter-notebook.

# Model building and library generation

In the following tutorial, we shall consider a model in which

$$
  \mathcal L = \mathcal L_{SM} + \mathcal L_{\chi, free} + \mathcal L_{\phi, free}  -g_\chi \phi \bar \chi \chi + \sum_{f \in \{\rm SM fermions\}} \frac{y_f}{\sqrt{2}}g_f \phi \bar f f
$$

where $\phi$ is a parity-even scalar mediator, and $\chi$ is a Dirac fermion. 
$y_f$ is the Yukawa coupling of the fermion $f$, while $g_\chi$ and $g_f$ are two arbitrary
coupling constants. 

This model is encoded in the file `src/models/scalar.cpp`. 
Let us open this file and comment it. The first thing to be noticed is that the model is 
not defined from scratch, but starting from a *base model*, i.e. the Standard Model.
This can save us a lot of time while building new models; however, it is always possible 
to do it from scratch.

Let us now open and go trough the file...

Before going on with the library generation, we can 
```bash
mv src/dp_scalar2to2  dp_scalar2to2_back
rm -rf src/scalar2to2
cd src/models
```
to remove the previosly generated libraries, associated with this model.

Let us compile the library with 
```
./lib_generate.sh scalar2to2 scalar
./lib_copy.sh scalar2to2
```
Notice that there's a file `leshouchesfrommarty.hpp` which is automatically generated only if it does not exist already. In fact, this file is the header file
for the input management, therefore if a user writes it once, he can re-generate the numerical library without worrying of it being overwritten.


Now we can 
notice which files have been automatically generated by DarkPACK, and realise which 
are the features that should be provided by the user, by looking at the newly 
generated `src/dp_scalar2to2`. This is the directory of the model-specific
files for this model. Note that this folder should already exist, and some files should
already be there, since we ran `lib_generate.sh` and `lib_copy.sh` previously. 
Now we can comment the global variables defined here.

Let us now open and go trough the file `src/dp_scalar2to2/correspondance.hpp`

In the folder we backed up, `src/dp_scalar2to2_back`, you can find the files with all 
the features. By comparing its content to `src/dp_scalar2to2`, you can check which 
files have been added. Now we can comment these files.

Let us now open and go trough the files `src/dp_scalar2to2/leshouchesfrommaty.hpp/.cpp`

# Library usage

## Folder structure
In order to use one or more libraries, one can write the source files for the executables
in `src/Test`, or `src/main`. The difference is among the compilation options, 
which, in `main`, include the linking with the `CMA-ES` submodule. The `Test` folder
will, instead, only link all the DarkPACK libraries to the executable file. 

## Additional information for compiling
You can also skip this paragraph in the tutorial, and go to the next one.

By looking at the `CMakeLists.txt` in both folders, you can see how to use the newly
generated library, and how to link it with external codes. 

However, to compile all the `DarkPACK` libraries at once, we will use `cmake` in the 
root folder. There you can find the more general `CMakeLists.txt`, with all the options. 
It is documented in the `README.md` file in the same directory. 

## Compilation
In the root folder we can quiclky look the `configure.sh` file, to check the options, 
and then run 
```bash
rm -rf build
./configure.sh
ninja -C build
```
This will clean the build directory, recreate it with the specified compilation options, 
and compile it.

**NOTE FOR THE COMPILATION OF THE MSSM** If you're compiling the MSSM (not the case here), 
in this version of `DarkPACK`, it will require a lot of memory. 
If you have at least 20 GB of RAM you can 
safely ignore this warning, but if you have 16 GB or less, you might want to compile it in 
single thread or use maximum 2 threads, by adding the predicate `-j${n_threads}` while calling
`ninja` or `make`. 

Now, in the folder `Test`, we can find the file `scalar_demo.cpp`, which is the example on how 
to write an executable.

Let us now give a look at it.

**NOTE FOR THE WORKSHOP** I have just noticed some small inconsistencies
(prefactors of the order of $1\times(1 + 10^{-2})$ in high energy regimes) if running is 
enabled in the results. Therefore, for this workshop, I am showing how to make use of
the running features by hand (this is to be considered an advanced feature, since running
is handled automatically), but it's purely demonstrative. There's and hardcoded macro
in the code that disables all running. I will fix the bug ASAP, and softcode the 
running disabling in classes that use it automatically (i.e. `AvgSvCalculator`).

Before running it, let us create, in the root directory
```bash
mkdir -p out/plots
./build/src/Test/scalar_demo.x src/models/scalar.lha 
```