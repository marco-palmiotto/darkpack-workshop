# Resources

- MARTY repository: https://github.com/docbrown1955/marty-public
- MARTY website: https://marty.in2p3.fr/
- Darkpack repository: https://github.com/marco-palmiotto/darkpack-cmake-public
- Dockerhub:
  - Dev: https://hub.docker.com/repository/docker/marcopalmiotto/darkpack-dev/general
  - Scan: https://hub.docker.com/repository/docker/marcopalmiotto/darkpack-scan/general
  - Deploy: https://hub.docker.com/repository/docker/marcopalmiotto/darkpack-deploy/general
 
# Configuration

## Local installation
You can install MARTY by following the instructions in the MARTY repository. 
The MARTY website provides the doxygen documentation and some examples.

We will provide some jupyter notebooks for data display. You can use poetry, if you 
have it installed (see below), or install the following python packages manually

    list-of-packages

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

In the Darkpack repository there is the .devcontainer folder, which contains all the Dockerfiles for all 
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

    cd <folder_with_results>
    poetry run jupyter-notebook

This will start a jupyter-notebook session in the environment defined by poetry. 
If this does not open immediately a tab in your browser, it is possible to use the link in the format

    http://localhost:8888/tree?token=...

and this should open a tab in your local browser, where you can use the instance of jupyter-notebook.
