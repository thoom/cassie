# cassie
A simple Vagrant script to create a Centos6.6 Cassandra VM

## Installation

1. Clone this repo.
2. Make sure that Vagrant is installed. Tested on 1.7.2.
3. Run `vagrant up`.
4. Enjoy!

## Options
To override the default configurations, just add an environment variable:

### CASSIE_VAGRANT_PRIVATE_NETWORK_IP
This is the IP address that Cassandra will bind itself to.
Defaults to `192.168.77.102`.

*You will need to reprovision your VM if after changing the IP address*

### CASSIE_VAGRANT_VM_NAME
The VM name. 
Defaults to `cassie`.
