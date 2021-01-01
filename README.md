# Creating Debian packages in Docker container with cross compiling support

## Overview

This project is based on [docker-deb-builder](https://github.com/tsaarni/docker-deb-builder).

## Create build environment

Start by building a container that will act as package build environment:

```bash
docker build -t debbuilder:buster-amd64 -f Dockerfile-debian-buster-amd64 .
```

If you want to cross compile to other platforms, simply use another Dockerfile:

```bash
docker build -t debbuilder:buster-armhf -f Dockerfile-debian-buster-armhf .
```

In this example the target is Debian 10 but you can create and
modify Dockerfile to match your target environment.

## Building packages

First download or git clone the source code of the package you are
building:

```bash
git clone ... ~/my-package-source
```

The source code should contain subdirectory called `debian` with at
least a minimum set of packaging files: `control`, `copyright`,
`changelog` and `rules`.

Run the build script to see usage:

```bash
$ ./build
usage: build [options...] SOURCEDIR
Options:
  -i IMAGE  Name of the docker image (including tag) to use as package build environment.
  -o DIR    Destination directory to store packages to.
  -d DIR    Directory that contains other deb packages that need to be installed before build.
```

To build Debian packages run following commands:

```bash
# create destination directory to store the build results
mkdir output

# build package from source directory
./build -i debbuilder:buster-armhf -o output ~/my-package-source
```

After successful build you will find the `.deb` files in `output` directory.

Sometimes build might require dependencies that cannot be installed with `apt-get`.  You can install them into the build environment by passing option `-d DIR` where DIR is a directory with `*.deb` files in it.

```bash
./build -i debuilder:buster-armhf -o output -d dependencies ~/my-package-source
```
