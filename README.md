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

If this is really your first time ever and you have no EPrints-box repository yet, download a fresh box from https://github.com/rorasa/eprints-box/releases . In this case please follow the steps in **Create your repository** topic first before proceeding to the next step.

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

### Create your repository

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

