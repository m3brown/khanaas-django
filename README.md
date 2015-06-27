# KHAN as a service, in Django

  - **Technology stack**: 
   - Python 2.7
   - Django 1.8
   - Postgres

## Dependencies
- **Vagrant / Virtualbox** - creates the VM used for development
    Installation instructions can be found here: http://docs.vagrantup.com/v2/installation/

## Installation
1. Clone this repository `git clone https://github.com/m3brown/khanaas-django.git`
2. Run `vagrant up` to create and provision the virtual machine

### Command Line Access
Once provisioning completes, connect to the machine using `vagrant ssh`

### Starting / Stopping the VM
To start the vagrant box: `vagrant up`  
To stop the vagrant box: `vagrant halt`  
