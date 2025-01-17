# sdk_manager_docker

## Introduction

This is a Dockerfile to use [NVIDIA SDK Manager](https://docs.nvidia.com/sdk-manager/) on Docker container.

## Important Information

NVIDIA released official Docker image(<https://docs.nvidia.com/sdk-manager/docker-containers/index.html>).

## Requirements

* Docker

## Preparation

### Download NVIDIA SDK Manager

Please download the package of NVIDIA SDK Manager from <https://developer.nvidia.com/nvidia-sdk-manager>.  
And, please put the package of NVIDIA SDK Manager in the same directory as the Dockerfile.  
This time, I used `sdkmanager_1.8.0-10363_amd64.deb`.

### Build Docker image

```
docker build --build-arg GID=$(id -g) --build-arg UID=$(id -u) -t jetpack .
```

To build a Docker image with a specific SDK Manager version override the ``SDK_MANAGER_VERSION`` variable in the Docker command line

```
docker build --build-arg SDK_MANAGER_VERSION=1.8.0-10363 --build-arg GID=$(id -g) --build-arg UID=$(id -u) -t jetpack .
```

### Create Docker container

```
./launch_container.sh
```

## Launch NVIDIA SDK Manager

Please launch NVIDIA SDK Manager by the following command.

```
sdkmanager
```

Please refer to <https://docs.nvidia.com/sdk-manager/install-with-sdkm-jetson/index.html>.
And, I tested in the following setting.

* Manual Setup
* OEM Configuration: Runtime

![](image/jetson-os-flash-setting.png)

## Notes

If you get errors like this, it means that QEMU is not installed in host/container

![](image/filesystem-error.png)

To configure QEMU, run below, either on host, or the running container.

```shell
./configure_qemu.sh
```

## Rosetta (Kali OS)
```
# Add Source
$ sudo echo "deb http://ppa.launchpad.net/apptainer/ppa/ubuntu focal main" | sudo tee -a /etc/apt/sources.list
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys AFE36534FC6218AE

# Install apptainer package
$ sudo apt update
$ sudo apt install -y apptainer-suid

# Run apptainer
docker pull --platform linux/amd64 x11vnc/docker-desktop:20.04
$ apptainer build nvidia-20.04.sif docker://pinkyswear/nvidia-sdkmanager:20.04
$ apptainer shell --bind /run/user/1000:/run/user/1000 nvidia-20.04.sif
$ sdkmanager
```