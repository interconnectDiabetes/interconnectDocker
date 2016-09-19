# interconnectDocker
This folder and readme contains all information and files necessary to create a
running instance of opal/agate/rserver/mongodb and datashield necessary on the
docker platform.

## Prerequisites
As a prerequisite, the docker engine needs to be installed to create the docker
instance and run it. The process is slightly different depending on the
operating system it is installed on. This section will describe the installation on
a Debian/Ubuntu server. Installation instructions for Windows and Ubuntu can be found on
the docker documentaion portal ([Windows](https://docs.docker.com/docker-for-windows/)
and [Mac](https://docs.docker.com/docker-for-mac/))

If docker is already installed please move onto the next
section titled 'Docker Compose's

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
