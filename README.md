# KHAN as a service, in Django

This is an introductory tutorial for learning core Django concepts.  You will be replicating the functionality of www.khanaas.com, plus additional features to showcase Django strengths such as the ORM and Forms.

  - **Technology stack**: 
   - Python 2.7
   - Django 1.8
   - Postgres 9.3

## Dependencies
- **Vagrant / Virtualbox** - creates the VM used for development
  - Vagrant download links can be found [here](http://www.vagrantup.com/downloads)
  - Virtualbox download links can be found [here](https://www.virtualbox.org/wiki/Download_Old_Builds_4_3)
    - We had issues getting the recently released Virtualbox 5.0 working with the Ubuntu virtual image used in this workshop.
    - The link above is for the 4.3 version of Virtualbox, which is still being maintained.
  
## Windows Dependencies
- Windows users may need to install SSH software to get Vagrant working
  - Cygwin should work fine with Vagrant
  - Another easy way to enable SSH is to download [Git](http://git-scm.com/download/win)
    - At the the `Adjusting your PATH environment` screen of the installer, check the `Use Git from the Windows Command Prompt` option

## Installation
1. Clone this repository `git clone https://github.com/m3brown/khanaas-django.git`
1. Open a terminal/Powershell instance and navigate to the `khanaas-django` directory you just created
1. OPTIONAL: Download [khanaas-django.box](https://s3.amazonaws.com/techtalkdc/khanaas-django.box), which has already provisioned the development environment and applications to save time and bandwidth
 - After downloading the box, activate it with `vagrant box add --name khanaas-django khanaas-django.box`
 - Modify the Vagrantfile in this repo so that `config.vm.box = "khanaas-django"`
1. Run `vagrant up` to create the virtual machine
 - If you don't use khanaas-django.box from the previous step, this step will also download Django, PostgreSQL, and other requirements
 - Windows users, if you get prompted with a firewall warning, make sure you allow 'private network' access

Coming Soon: A guide for creating your own Khan AAS app

### Command Line Access
Once provisioning completes, connect to the machine using `vagrant ssh`

### Starting / Stopping the VM
To start the vagrant box: `vagrant up`  
To stop the vagrant box: `vagrant halt`  
