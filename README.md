

Lab configuration for Ansible

The related code repository available at - https://github.com/raj17rane/ansible-setup.git 


If you experience any problems with the lab, please reach out to me direct, raj17rane@gmail.com

The lab environment, makes use of Docker with Docker Compose.  If you're on Windows or Mac, you can install the convenient
Docker Desktop to make both Docker and Docker-Compose available

Linux, will require the installation of Docker and Docker Compose

### Download and Preparation

I recommend that the lab environment is downloaded to your respective home directory, i.e. -

* Mac     - /Users/rajesh/ansible-setup
* Windows - C:\Users\rajesh\ansible-setup
* Linux   - /home/rajesh/ansible-setup

On a Mac or Linux system, you should be able to clone the repository accordingly from a terminal whilst in your home directory with the following command -

```git clone https://github.com/raj17rane/ansible-setup.git
On Windows, if you don't have git installed, the lab can be downloaded using the following url - :

After unzipping the archive, you must ensure that a single ansible-lab folder is copied into your home directory (not multiple folders, i.e. ansible-lab-master/ansible-lab or ansible-lab/ansible-lab).  See the next section on Validation.

### Validation (IMPORTANT)

Please verify that all of the lab files, are in the expected locations after either cloning, or extracting the zip file, for your corresponding OS and User.  They should be similar to the following -

Mac OS X -

```
/Users/rajesh/ansible-lab/.env
/Users/rajesh/ansible-lab/DiveIntoAnsible_Cover.png
/Users/rajesh/ansible-lab-lab/README.md
/Users/rajesh/ansible-lab/docker-compose.yaml
/Users/rajesh/ansible-lab/config/guest_name
/Users/rajesh/ansible-lab/config/guest_passwd
/Users/rajesh/ansible-lab/config/guest_shell
/Users/rajesh/ansible-lab/config/root_passwd
```

Windows -

```
C:\Users\rajesh\ansible-lab\.env
C:\Users\rajesh\ansible-lab\DiveIntoAnsible_Cover.png
C:\Users\rajesh\ansible-lab\README.md
C:\Users\rajesh\ansible-lab\docker-compose.yaml
C:\Users\rajesh\ansible-lab\config\guest_name
C:\Users\rajesh\ansible-lab\config\guest_passwd
C:\Users\rajesh\ansible-lab\config\guest_shell
C:\Users\rajesh\ansible-lab\config\root_passwd
```

Linux -

```
/home/james/ansible-lab/.env
/home/james/ansible-lab/DiveIntoAnsible_Cover.png
/home/james/ansible-lab/README.md
/home/james/ansible-lab/docker-compose.yaml
/home/james/ansible-lab/config/guest_name
/home/james/ansible-lab/config/guest_passwd
/home/james/ansible-lab/config/guest_shell
/home/james/ansible-lab/config/root_passwd
```

### Configuration

Inside the ansible-lab directory is a hidden file called .env that needs to be edited with a text editor.  Whilst this file contains a lot of information, it is only the CONFIG and ANSIBLE_HOME entries that need to be customised.  

Please use the following as references for each operating system accordingly, changing the username to match your own -

Mac OS X -

```
# Shared config volume
CONFIG=/Users/rajesh/ansible-lab/config

# Shared home directories
ANSIBLE_HOME=/Users/rajesh/ansible-lab/ansible_home
```

Windows (n.b. 'users' and the username (in my case this is james) are in lowercase, this is important) -

```
# Shared config volume
CONFIG=/host_mnt/c/users/rajesh/ansible-lab/config

# Shared home directories
ANSIBLE_HOME=/host_mnt/c/users/rajesh/ansible-lab/ansible_home
```

Linux -

```
# Shared config volume
CONFIG=/home/rajesh/ansible-lab/config

# Shared home directories
ANSIBLE_HOME=/home/rajesh/ansible-lab/ansible_home
```

### Running the lab

Once you've made these changes, you should then be able to run the following in your command prompt or terminal, directly from the ansible-lab directory -

```
docker-compose up
```

If all goes well, it should start the lab environment.  The lab is ready for use when you see text similar to the following -

```
Attaching to docker, centos1, ubuntu2, ubuntu3, centos2, centos3, ubuntu-c, ubuntu1, portal
```

Keep this terminal open and running whilst using the course.  In your browser, then browse to http://localhost:1000 and you should get the lab interface.  If you find that you cannot login to the Ansible control host (ubuntu-c) as ansible and the password of password, then there is a fault with your configuration.  If this is the case, it is important to perform the following actions before troubleshooting or changing your configuration.  Press CTRL-C, then run the following -

```
docker-compose rm
```

Should you encounter issues at this stage that you cannot resolve, please contact me or, raise an issue in the repository with as much detail as possible (including copies of your .env file)

### Troubleshooting Common Issues

#### 1. Cannot log in as the ansible user

When the lab environment is configured and started, it reads the configuration directory and sets up the ansible user.  This is based on the entry in config/guest_name.  It expects the config directory to be provided as a volume using the CONFIG entry in the .env file.  See the 'Configuration' section above as this will vary for Windows, Linux and Mac OS X.  

If this entry is incorrect, it will not find the config directory and subsequently, the ansible user will not be created.

A convenient way of verifying that the path you're using is correct is by running a similar command to the following, substituing the path you're trying to use.  If you see the output of 'ansible', the configuration path is as expected, if not, you'll need to tweak the configuration -

```
docker run --rm -v /Users/rajesh/ansible-lab/config:/config ubuntu cat /config/guest_name
```

This command passes the volume mount manually (-v for volume), the local path (/Users/james/ansible-lab/config), the target mount point (:/config).  Then, it runs a ubuntu container with a command to show the contents of /config/guest_name.  Finally, --rm cleans up the container after it exits.

Lastly, the ANSIBLE_HOME entry within .env should use the same format.

#### 2. WSL with Ubuntu and exit code 255

When using Ubuntu with WSL, this issue is a result of the WSL Ubuntu image not having systemd.  It is a stripped down version of Ubuntu. The containers, all leverage systemd and when they try to use the parents subsystem this is missing.   We can work around this by just creating the components that are needed, prior to the execution of ```docker-compose up```

```
sudo mkdir -p /sys/fs/cgroup/systemd
sudo mount -t cgroup -o none,name=systemd cgroup /sys/fs/cgroup/systemd
```

This will need to be executed once every time the system is rebooted.  Rather than making permanent changes, I recommend that in these cases, a quick shell script is created to start the lab with these commands, followed by ```docker-compose up```

### Extra step for Linux users

Owing to the permissions model for Docker with Linux, there is one additional step that needs to be carried out.  With the lab working and connectivity working as ansible, perform the following actions -

```
su - 
<enter the password of password when prompted>
chown ansible /shared
exit
```

### Lab commands

To refresh the images with the latest course images -

```
docker-compose pull
```

To remove the lab

```
docker-compose rm
```


