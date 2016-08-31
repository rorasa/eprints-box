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
