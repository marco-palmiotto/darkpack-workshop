# Resources

- MARTY repository: https://github.com/docbrown1955/marty-public
- MARTY website: https://marty.in2p3.fr/
- Darkpack repository: https://github.com/marco-palmiotto/darkpack-cmake
- Dockerhub:
  - Dev: https://hub.docker.com/repository/docker/marcopalmiotto/darkpack-dev/general
  - Scan: https://hub.docker.com/repository/docker/marcopalmiotto/darkpack-scan/general
  - Deploy: https://hub.docker.com/repository/docker/marcopalmiotto/darkpack-deploy/general
 
# Configuration

## Local installation
You can install MARTY by following the instructions in the MARTY repository. 
The MARTY website provides the doxygen documentation and some examples.

## Container usage
If you are using Visual Studio Code or CLion, and you have docker or podman installed, you might
make use of the containers, thanks to the devcontainer extension of the IDEs. You need to have the
corresponding extension installed in your IDE.

All the container images ship with all the dependencies needed by MARTY, and come with MARTY pre-installed
and configured. The differences between the different images are the following:
 - Deploy: MARTY dependencies + MARTY
 - Scan: MARTY dependencies + MARTY + CMA-ES (library for function optimisation)
 - Dev: MARTY dependencies + MARTY + CMA-ES + python + poetry + profiling tools

If you want to use only Darkpack, and use your local installation of python (or whatever other program
you prefer) to do the plots, you can use
the deploy image: 
    
    docker pull marcopalmiotto/darkpack-deploy:fedora41

If you prefer using python in the container, we reccommend to use the Dev image:
    
    docker pull marcopalmiotto/darkpack-dev:fedora41

In the Darkpack repository there is the .devcontainer folder, which contains all the Dockerfiles for all 
the images. By default, it will use the image dev-fedora41. If you wish to change it, you just need
to change the corresponding line in the configuration file `.devcontainer/devcontainer.json`.
We reccomment to have already pulled the image before opening the repository in the devcontainer, so that
you will not have it built locally (this build will take a few minutes of 100% CPU time). 
