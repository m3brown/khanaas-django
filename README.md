# KHAN as a service, in Django

  - **Technology stack**: 
   - Python 2.7
   - Django 1.8
   - Postgres

## Dependencies
- **Vagrant / Virtualbox** - creates the VM used for development
  - Vagrant download links can be found [here](http://www.vagrantup.com/downloads)
  - Virtualbox download links can be found [here](https://www.virtualbox.org/wiki/Downloads)

## Installation
1. Clone this repository `git clone https://github.com/m3brown/khanaas-django.git`
1. Run `vagrant up` to create and provision the virtual machine
1. Follow the [guide](KHANAAS-GUIDE.md) for creating your own Khan AAS app

### Command Line Access
Once provisioning completes, connect to the machine using `vagrant ssh`

### Starting / Stopping the VM
To start the vagrant box: `vagrant up`  
To stop the vagrant box: `vagrant halt`  
