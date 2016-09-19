# interconnectDocker
This folder and readme contains all information and files necessary to create a
running instance of opal/agate/rserver/mongodb and datashield necessary on the
[Docker](https://www.docker.com/) platform.

## Prerequisites
As a prerequisite, the docker engine needs to be installed to create the docker
instance and run it. The process is slightly different depending on the
operating system it is installed on. This section will describe the installation on
a Debian/Ubuntu 14.04 server on the command line interface. Installation instructions for Windows and Ubuntu can be found on
the docker documentaion portal ([Windows](https://docs.docker.com/docker-for-windows/)
and [Mac](https://docs.docker.com/docker-for-mac/)). If docker is already installed please move onto the next
section titled 'Docker Compose's

### Pre-Prerequisites
Docker requires a 64-bit installation of the Ubuntu installation, and the linux kernel version must be higher than 3.10.
To check your current kernel version, use `uname -r`:

```
$ uname -r
3.16.0-30-generic
```

The apt sources have to be updated, so please:
1. log into the command line with `sudo` privileges or as the `root` user.
2. Update package information, ensure that `APT` works using the `https` protocol and `ca-certificates` is installed.
 ```
 sudo apt-get update
 sudo apt-get install apt-transport-https ca-certificates
 ```
3. Add the new GPG key
  ```
  sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
  ```
4. Open the `/etc/apt/sources.list.d/docker.list` on a text editor (if the file doesn't exist create it)
5. Remove any existing entries and add in your Ubuntu operating system
  - On Ubuntu Trusty 14.04 (LTS)
  ```
  deb https://apt.dockerproject.org/repo ubuntu-trusty main
  ```
  - Ubuntu Xenial 16.04 (LTS)
  ```
  deb https://apt.dockerproject.org/repo ubuntu-xenial main
  ```
6. Save and close the file
7. Update the `APT` package index.
  ```
  sudo apt-get update
  ```
8. Purge any old repo if it exists
  ```
  sudo apt-get purge lxc-docker
  ```
9. Make a quick check that `APT` is pulling from the right repositoty (it can get silly at times)
  ```
  apt-cache policy docker-engine
  ```

It is recommended that the `linux-image-extra-*` kernel packages are installed (for use of the `aufs` storage driver, which makes the docker containers more efficient in their use of processing and memory).

```
sudo apt-get update
sudo apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual
```

## Actually installing Docker



## Docker Compose
Assuming that docker has been correctly installed and configured the next step would be to install `docker-compose`
which is a tool for defining multi-container Docker applications. **Note: Users who have installed via Windows and Mac will
already have docker compose installed**

Installing docker-compose must be performed as the `root` user.
```
curl -L https://github.com/docker/compose/releases/download/1.8.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

Executable permission have to be set to downloaded binary
```
chmod +x /usr/local/bin/docker-compose
```

Test the installation
```
docker-compose --version
```

This should say something like: `docker-compose version: 1.8.0`

## Obiba Opal
Obiba has created a docker image that includes all the necessary software and database configurations to jump right into running
opal server, to which we can upload data and test datashield.
