# interconnectDocker
This folder and readme contains all information and files necessary to create a
running instance of opal/agate/rserver/mongodb and datashield necessary on the
[Docker](https://www.docker.com/) platform.

The easiest way to follow this guide is to `git clone`, or download this directory onto the machine
that will serve as the Docker server, and follow the guide with the command line interface
open within this directory.

## Prerequisites
As a prerequisite, the docker engine needs to be installed to create the docker
instance and run it. The process is slightly different depending on the
operating system it is installed on. This section will describe the installation of the docker engine on
a Debian/Ubuntu 14.04 server on the command line interface. Installation instructions for Windows and Ubuntu can be found on
the docker documentation portal ([Windows](https://docs.docker.com/docker-for-windows/)
and [Mac](https://docs.docker.com/docker-for-mac/)). If docker is already installed please move onto the next
section: 'Docker Compose'.

### Pre-Prerequisites
Docker requires a 64-bit installation of the Ubuntu installation, and the Linux kernel version must be higher than 3.10.
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
As a user with `sudo` privileges

1. Update the `APT` package index

    ```
    sudo apt-get update
    ```

2. Install Docker and start the docker daemon

    ```
    sudo apt-get install docker-engine
    sudo service docker start
    ```

3. Verify that `docker` has been installed correctly

    ```
    sudo docker run hello-world
    ```


### Configuring Docker
#### Docker Group
The docker daemon binds to a Unix socket instead of a TCP port. By default that
Unix socket is owned by the user root and other users can access it with sudo.
For this reason, docker daemon always runs as the root user.

To avoid having to use sudo when you use the docker command, create a Unix group
called docker and add users to it. When the docker daemon starts, it makes the
ownership of the Unix socket read/writable by the docker group.

***Warning:The docker group is equivalent to the root user or sudoers group. If this configuration is a problem please contact us***

To create the docker group and add your user:

1. Log into your machine as a user with `sudo` privileges.
2. Create the `docker` group and add the user to the docker group.

    ```
    sudo groupadd docker
    sudo usermod -aG docker $USER
    ```

3. Log out and log back in. This ensures your user is running with the correct permissions.
4. Verify docker runs its demo without `sudo`.

    ```
    docker run hello-world
    ```


#### Adjusting the memory and swap accounting
We adjust the memory and swap accounting of the system halt error messages on account of swap memory.

To enable memory and swap on system using GNU GRUB (GNU GRand Unified Bootloader), do the following:

1. Log into Ubuntu as a user with sudo privileges.
2. Edit the /etc/default/grub file and set the `GRUB_CMDLINE_LINUX` value as follows:

    ```
    GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"
    ```

3. Save and close the file, then update `GRUB`

    ```
    sudo update-grub
    ```

4. Reboot the system so that the changes take effect

#### (Optional) Enable UFW forwarding:
Work in progress: TODO

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

Logout of root user to main `$USER`

## Running the Data Server
There are two files in the directory that installs the correct software and configurations for
the opal data server for use with DataShield: the `Dockerfile` and the `docker-compose.yml` files.

### Changing the Opal Administrator Password
Within both files one can change the `OPAL_ADMINISTRATOR_PASSWORD` environment variable from its default `password` to a better one.

### (Optional): Change the application ports
This is only useful to those who have ports 8843,8880,6611,6612,8844,8881 used by other applications running on the same server. If any ports are changed please reflect the changes in the `Dockerfile` (mainly 8843 and 8880).

### Spinning up the opal container

And then finally to get the docker machine to run, within the project directory
(the interconnectDocker folder if you downloaded this directory) run:

```
docker-compose up
```

or

```
docker-compose up -d
```
to run the `up` process in the background. This reads the `docker-compose.yml` file
and finds the necessary dependencies and spins up the necessary Docker containers
for opal to function. The `docker-compose.yml` is dependent on the `Dockerfile`
and vice versa, so please mimic any changes made such as the opal administrator password.

Check the running docker instances with:

```
docker-compose ps
```

You will see the running docker containers, the applications they run, and which
port they are available for access from. Test connecting to the opal server on
port `8843` and upload your data. Congratulations you have completed the set
up of a data server for InterConnect! Once completed it is very easy to run
more instances as the installation of docker has been completed.

## Testing the Container Installation
To test the installation, a test data set will be uploaded, and a user will be created to interact with the uploaded data through datashield.

### Testing opal and uploading some data
Visit the web interface of opal via on port 8843 (or the chosen port) on a web browser. If you have just started the container with `docker-compose up` this may take a while before opal is accessible.

Enter the application with the credentials

* Username: administrator
* Password: <password you have chosen in previous steps,'password' if unchanged>

This will display the Opal Dashboard from which several actions can be taken.

1. Please click on the `Projects` tab on the top
2. Then `+ Add Project`
3. Within the add project pop up:
    * Provide 'test_project' for name
    * Provide a title if you wish
    * Choose `opal_data (default storage)` as the database option
    * Save

Once the project has been created, we need to upload a dataset to test on, we will use the LifeLines dataset for this purpose.

1. Click on the `test_project` that was just created.
2. Once inside the 'Tables' pane, click on `Import`
3. Within this dialog pane:
    * Choose SPSS under 'Data Format' and click next.
    * Click Browse and Upload, then choose the LifeLines.sav that is included in the repository.
    * Once uploaded, select the box for LifeLines.sav
    * Click Next in the 'Select the source to be imported' page and Next again on the 'Configure data import' page
    * Check the box next to LifeLines (see that it has 11 Unmodified Variables)
    * Click Next through the pane
    * Click Finish
4. This will start the upload and automatic configuration of the data for use, this may take a few moments. Progress can be checked using the 'Tasks' icon in the left of the 'Tables' page.

Once the dataset has been uploaded we can create a test user to interact with the LifeLines dataset through DataShield.

1. As administrator, click on `Administration` in the top bar.
2. Under 'Data Access' click on `Users and Groups`
3. Click the `+ Add user` button choosing `Add user with password...` as the option:
4. In the pop up create a user with name 'test' and password 'test1234' and save. The user 'test' should now be visible under 'Users'
5. Now we have to allow the user to use DataSHIELD.

1. As administrator, click on `Administration` in the top bar.
2. Under 'Data Analysis' click on `DataSHIELD`
3. Here one can see all the packages installed for DataShield, for this installation all packages necessary have been preinstalled and configured for Opal. However we need to add a user who can use this.
4. In 'Permissions' at the bottom of the page. Click on `+ Add Permission` and `Add user permission...`. Enter the name 'test' and click Save
5. Now the user test is allowed to make basic DataShield commands, however only to projects/tables where it is permitted to do so. Hence we have to allow 'test' to run DataShield commands on LifeLines.
6. Click on `Projects` in the top bar.
7. Click on `test_project`.
8. Inside this 'Tables' page click on the Lifelines table.
9. Under the 'Permissions' tab in test_project / Lifelines Click `+ Add Permission` and `Add user permission...`
10. Add the 'test' user and save.

We now have created a user who can use datashield to run analyses on the LifeLines table! We now can perform a quick test in R.

1. Open RStudio
2. Run this code snippet. **Please pay attention to the url variable and provide the correct IP address**

    ```
    library(opal)
    library(dsBaseClient)
    library(dsStatsClient)
    library(dsGraphicsClient)
    library(dsModellingClient)
    library(metafor)


    # assumes you have created a user called 'test' and pass: 'password'
    # and given him datashield and table rights
    server <- c( 'lifelines')
    url <- c('https:<YOUR_IP_ADDRESS>:8843')
    table <- c('test_project.LifeLines')
    password <- c('test1234')
    user <- c('test')
    logindata_all <- data.frame(server,url,user,password, table)

    opals <- datashield.login(logins=logindata_all, assign=TRUE)
    ```

3. Once logged in, find the mean of the weights using `ds.mean` which should result in a global average of roughly 76.19645

    ```
    ds.mean('D$GEWICHT')
    ```

4. Logout of opal and datashield with

    ```
    ds.logout(opals)
    ```

Congratulations you have a working installation of opal and datashield. Now you may upload your own dataset and participate in the InterConnect project!
