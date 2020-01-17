# Detailed working guide for the Raspberry Pi Cluster at "Integrierte Energiesysteme" University Kassel

This repository provides information about setting up and using the INES cluster. The included scripts aim to simplify and automate tasks which need to be performed regularly. 
The scripts are categorised by context.

* servermodule: Script is intended to be run on the server module
* clientmodule: Script is intended to be run on the client modules
* workstation:  Scripts can be used "outside" the cluster

This README contains the basics. Troubleshooting and indepth information is outsourced into different files.

## Starting the cluster

* Connect the main power supply on the backside
* The server module will power all modules (red led is active)
* Some self tests are performed
* The modules are rebooted by geographic position (0 to 60)

The yellow led will power on after the module executes /etc/rc.local at least once successfully. More information is available in Troubleshooting#LED.

## Getting all the modules to start

### Connecting

There are different ways to start the modules depending on what server image you are using. The first method describes how to start and work with the modules when the image was set back to default 
factory settings. 

Power the cluster 

## Connecting to the cluster with your workstation

The scripts in workstation can be run externally and usually use the "outsider" account on the server module.

Additionally there are guides available

* userguide: This will cover the basics and daily usage
* manual

## Server module

Copy your public ssh key to the server module so you will be able to connect to sshd without a password.
```
ssh-copy-id outsider@192.168.1.111
```

Add "ssh" package to your R installation. On Ubuntu as example.
```
sudo apt-get install -y libssh-dev
sudo -i R
install.packages("ssh")
require("ssh")
...
q()
```

You can test if you are able to connect without a password as your default user.
```
R
require("ssh")
session <- ssh_connect("outsider@192.168.1.111")
print(session)
```

#### sim_to_nodes
```
Location: server module
Path: /pxe/meta/sim_to_nodes
Description: this script is used within R scripts to distribute simulations to single, range or multiple ranges of nodes
Usage (bash):	sim_to_nodes "Simulation Identifier" "1-3;7;33-51"
Usage (R):	file_path = "<path to simulation>/simulationidentifier"
	scp_upload(session, file_path, to = '/pxe/meta/simulation', verbose = TRUE) # uploads the simulation
	out <- ssh_exec_wait(session, command = '/pxe/meta/sim_to_nodes "simulationidentifier" "1-7;9"')
```