# EPrints-box
Vagrant box with preconfigured EPrints repository for rapid deployment

Ever want a digital repository in your home?

EPrints-box allows an institutional-grade repository system [EPrints](http://www.eprints.org) to be deployed instantly as a virtual machine. EPrints is a full-fledged respository system employed in hundreds on universities and libraries worldwide. With EPrints-box, it takes only a few steps to have your own powerful repository up-and-running locally in no time.

EPrints-box is designed to work as a local repository where large traffic and security are not concerns. It works best as a local repository, home repository or a demo. For large scale production repository, it is advised to deploy EPrints properly on a server following the instructions at http://wiki.eprints.org/w/Main_Page .
 
## Installation

For first time installation of EPrints-box, follow the following steps:

### 1. Install VirtualBox

Oracle VirtualBox for your OS can be found and install following the instruction at https://www.virtualbox.org/wiki/Downloads

### 2. Install Vagrant

Vagrant can be found and install following the instructions at https://www.vagrantup.com/downloads.html

### 3. Install Vagrant Persistent Storage plugin

Use the following command in your terminal to install the persistent storage plugin for Vagrant:
```bash
vagrant plugin install vagrant-persistent-storage
```

### 4. Start up your EPrints-box

Your should already have a copy of your EPrints-box repository already.

In your terminal, go to the folder where the EPrints-box is then run `vagrant up`.
Within a few minutes, your repository is then up and running.

Example
```bash
cd eprints-box-<version>
vagrant up
```

If this is really your first time ever and you have no EPrints-box repository yet, download a fresh box from https://github.com/rorasa/eprints-box/releases . In this case please follow the steps in [Create your repository](https://github.com/rorasa/eprints-box/blob/master/README.md#create-your-repository) topic first before proceeding to the next step.

### 5. Setting your host

An EPrints repository is required to have its own domain name. Suppose your repository's domain is *your.repository.domain*, please map this domain to 192.168.33.10 in your machine's host setting.

**In Linux or MAC** Add the domain to your `/etc/hosts`.

Example

```bash
echo "192.168.33.10     your.repository.domain | sudo tee -a /etc/hosts
```

**In Windows**

1. Start Notepad.exe as Administrator.
2. Using Notepad, open C:\Windows\System32\Drivers\etc\hosts
3. Add `192.168.33.10     your.repository.domain` to the last line. Save the file.

### 6. Access your repository

Open your web browser. Type in `your.repository.domain` (don't forget to change this to your real domain) into the browser URL box. Suppose that the repository is already created, the homepage of EPrints should show up. Enjoy!

## Day-to-day use of EPrints-box

Now that you have everything setup, to use EPrints-box in a normal day-to-day fashion is easy.

### Start repository server

Just go to your EPrints-box folder and run `vagrant up`

Example
```bash
cd eprints-box-<version>
vagrant up
```

### Access your repository

Just open your web browser and type in your repository's domain.

### Stop repository server

When you'd like to shutdown the repository server, you have a few options to choose from.

`vagrant suspend` suspends your virtual machine and leaves both the machine itself and its memory state in your harddisk. It is the fastest way to shut it down and boot it up again. However, it uses the biggest amount of disk space on your machine.

`vagrant halt` leaves the virtual machine in your machine after cleanly shut it down first. It is not as fast as the suspend option but requires less disk space.

`vagrant destroy` removes your virtual machine completely. This is the slowest option because a new virtual machine must be copied again at start up. However it takes no more space apart from the EPrint-box data. Your repository data is *not* removed when perform this destroy option.

## Create your repository

If this is the first time you use EPrints-box, then you have to create a new repository.

### 1. Get a fresh EPrints-box 

After all the requires dependencies are installed, please download and extract the latest version of EPrints-box from https://github.com/rorasa/eprints-box/releases .

### 2. Start up your repository server

In your terminal, go to the folder where your EPrint-box is.
```bash
cd eprints-box-<version>
vagrant up
```

### 3. Log in to your server

Log in using SSH
```bash
vagrant ssh
```

You should now at the server's shell `vagrant@vagrant$`. Run this command
```bash
sudo su eprints
```
to switch to *eprints* admin user. You should now be at `eprints@vagrant$`.

### 4. Create new repository

The instance of EPrints is installed at `/usr/share/eprints3/`. To create a new repository, use command
```bash
/usr/share/eprints3/bin/epadmin create
```
then follow the on-screen instructions.

Please take note of the *Host Name*. This is your repository's domain (*your.repository.domain*) the you will use to access your repository. It is also the domain name you use [during the setup of your host](https://github.com/rorasa/eprints-box#5-setting-your-host).

When prompt to create a database, use username `root` and password `root`.

### 5. Finalise your repository

After a new repository is created. Do
```bash
exit
```
to drop back to `vagrant@vagrant$` shell.

Then restart the server by
```bash
sudo service apache2 restart
```

Do `exit` again to leave the server.

Now, supposed that you already [setup your machine's host](https://github.com/rorasa/eprints-box#5-setting-your-host), you can put your repository's domain into your browser. If everything is working properly you should see an EPrints homepage.

# Technical details

The following part of this documentation is inteded for developers and is not requires for a day-to-day use of EPrints-box.

## Structure of EPrints-box

There are 4 files in EPrints-box folder.

* data.vdi - a virtual hard drive that stores all of the repository data. This is implemented as a persistent storage using [Vagrant persistent storage plugin](https://github.com/kusnier/vagrant-persistent-storage). If you want to back up your repository, this is THE file you need to back up.
* package.box - a Vagrant box that contains the preconfigured Ubuntu system with EPrints software installed.
* Vagrantfile - a Vagrant configuration file
* bootstrap.sh - a Vagrant provision file

## Software in the package

The current version of EPrints-box is shipped with EPrints 3.3, Ubuntu 14.04 LTS, and Apache 2.

## Construction of the package.box

This section describes the steps to construct the package.box in details.

### Create Vagrantfile

The vanilla ubuntu box from [Bento Box](https://atlas.hashicorp.com/bento/boxes/ubuntu-14.04) is used as a base for EPrints-box.
```bash
vagrant init bento/ubuntu-14.04
```

Open the Vagrantfile ad add the following configurations:
```
# Provision Apache to enable EPrints
config.vm.provision :shell, path: "bootstrap.sh"

# accessing "localhost:8080" will access port 80 on the guest machine.
config.vm.network "forwarded_port", guest: 80, host: 8080

# Create a private network, which allows host-only access to the machine
# using a specific IP.
config.vm.network "private_network", ip: "192.168.33.10"

# Create persistent storage to store main data
config.persistent_storage.enabled = true
config.persistent_storage.location = "data.vdi"
config.persistent_storage.size = 1000000
config.persistent_storage.mountname = 'data'
config.persistent_storage.filesystem = 'ext4'
config.persistent_storage.mountpoint = '/usr/share/eprints3'
config.persistent_storage.volgroupname = 'myvolgroup'
```

### Create bootstrap.sh

Create `bootstrap.sh` file with the following content:
```bash
#!/usr/bin/env bash

a2ensite eprints
```

### Boot up the server
```bash
vagrant up
```

You will see a warning about a failed provision because apache is not yet installed. Ignore it.

Access the server via SSH
```bash
vagrant ssh
```

### Add EPrints APTs

You are now at `vagrant@vagrant$` shell. Add EPrints APTs by
```bash
sudo vi /etc/apt/sources.list
```
then add 
```
deb http://deb.eprints.org/3.3/ stable/
deb-src http://deb.eprints.org/3.3/ source/
```
to the file. ( You might have to install Vim first.)

Once finish, do 
```bash
sudo apt-get update
```

### Install LAMP Stack

```
sudo tasksel install lamp-server
```

MySQL password is set to `root` by default.

### Install EPrints

Do
```bash
sudo apt-get install eprints
```
to install EPrints system.

It should be noted that the location `/usr/share/eprints3/` where EPrints is being installed is in fact mounted to the data.vdi persistent storage. 

After EPrints is installed, some patches are needed. These are required only for Eprints 3.3.12 and below.
```bash
sudo su eprints
wget -N https://raw.githubusercontent.com/eprints/eprints/88567f9cf9deb146c24a8088e452d4561c90f05e/perl_lib/EPrints/Repository.pm -O /usr/share/eprints3/perl_lib/EPrints/Repository.pm
wget -N https://raw.githubusercontent.com/eprints/eprints/26e97fc3dbaa28e89e7ffbe0e6f8eedbfc7804cd/lib/defaultcfg/cfg.d/security.pl -O /usr/share/eprints3//lib/defaultcfg/cfg.d/security.pl
wget -N https://raw.githubusercontent.com/eprints/eprints/67986a00f042077a8388278cbcfdd51e0d737647/perl_lib/EPrints/Apache/LogHandler.pm -O /usr/share/eprints3/perl_lib/EPrints/Apache/LogHandler.pm
wget -N https://raw.githubusercontent.com/eprints/eprints/34f85e9b994d47cb95e225874749c274020688b5/perl_lib/EPrints/DataObj/LoginTicket.pm -O /usr/share/eprints3/perl_lib/EPrints/DataObj/LoginTicket.pm
sed -i 's/$r->connection->remote_ip/$r->connection->client_ip()/g' /usr/share/eprints3/perl_lib/EPrints/DataObj/LoginTicket.pm
exit
```

### Move MySQL location

The default MySQL location `/var/lib/mysql` will be destroyed along with the virtual machine by `vagrant destrot`. To prevent the loss of data, we will move MySQL data directory to the persistent storage at `/usr/share/eprints3/mysql`.

#### First, stop MySQL service
```bash
sudo service mysql stop
```

#### Copy MySQL to the new location
```bash
sudo rsync -av /var/lib/mysql /usr/share/eprints3
```

#### Remove the old instance of MySQL
```bash
sudo rm -rf /var/lib/mysql
```

#### Pointing to the new data location
Open `/etc/mysql/my.cnf`
```bash
sudo vi /etc/mysql/my.cnf
```

Change the line
```
datadir=/var/lib/mysql
```
to
```
datadir=/usr/share/eprints3/mysql
```

#### Configure AppArmor Access Control Rule
Open `/etc/apparmor.d/tunables/alias`
```bash
sudo vi /etc/apparmor.d/tunables/alias
```
Add 
```
alias /var/lib/mysql/ -> /usr/share/eprints3/mysql/,
```

Restart the AppArmor
```bash
sudo service apparmor restart
```

#### Restart MySQL
Create a fake directory to pass MySQL's environment check
```bash
sudo mkdir -p /var/lib/mysql/mysql
```

Start MySQL
```bash
sudo service mysql start
```

MySQL should start with no problem. You can check if the data directory has moved by
```
mysql -u root -p
select @@datadir;
exit
```
which should shows that datadir is now at `/usr/share/eprints3/mysql`.

### Fix Vagrant SSH key
We have done setting up the base machine for EPrints-box. However, if we package this machine as-is with `vagrant package`, the resulting box will have authentication failure when trying to connect to via SSH. 

To fix this, do the following commands:
```bash
mkdir -p /home/vagrant/.ssh
wget --no-check-certificate https://raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub -O /home/vagrant/.ssh/authorized_keys
chmod 0700 /home/vagrant/.ssh
chmod 0600 /home/vagrant/.ssh/authorized_keys
chown -R vagrant /home/vagrant/.ssh
```
This effectively changed the SSH key for this machine to a default key. Once packaged, the resulting box will now accessible via SSH.
**Warning** Once this step is done, we can no longer SSH this machine again. Do this as the VERY LAST step.

### Package the box
Once all the setting up is done, we can now package our box.

If haven't done so, exit the SSH session with `exit`.

The Vagrant base box can be packed by
```
vagrant package --base <virtual-machine-name>
```

The name of our virtual machine can be found with
```
VBoxManage list vms
```

Once the packaging is completed, we should have a `package.box` file, ready for deploy.

### Distribution

Before distribute the EPrints-box, change the following line in Vagrantfile from:
```
config.vm.box = "bento/ubuntu-14.04"
```
to
```
config.vm.box = "eprints"
config.vm.box_url = "package.box"
```

All 4 files (package.box, data.vdi, Vagrantfile, and bootstrap.sh) must be shipped together.

## References

[EPrints manual](http://wiki.eprints.org/w/EPrints_Manual)

[Vagrant documentation](https://www.vagrantup.com/docs/)

[HOW-TO : Install Eprints v3.3.12  on Ubuntu 14.04 With LDAP Authentication](http://wiki.unimas.my/unimaswiki/bin/view/HOW-TO,+Tutorial+%26+User+Manual/HOW-TO+%3A+Install+Eprints+v3.3.12++on+Ubuntu+14.04+With+LDAP+Authentication)

[How To Move a MySQL Data Directory to a New Location on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-move-a-mysql-data-directory-to-a-new-location-on-ubuntu-16-04)

[How to authenticate to a VM using Vagrant up?](http://superuser.com/questions/745881/how-to-authenticate-to-a-vm-using-vagrant-up)

[Vagrant Persistent Storage](https://github.com/kusnier/vagrant-persistent-storage)
