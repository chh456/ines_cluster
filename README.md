# Detailed guide for the microcontroller cluster at "Integrierte Energiesysteme"

![Test](github_resources/ines_cluster.jpg)

This repository provides information about setting up and using the INES cluster. The included scripts aim to simplify and automate tasks which need to be performed regularly. 
The scripts are categorised by context.

* servermodule: Script is intended to be run on the server module
* clientmodule: Script is intended to be run on one of the client modules
* workstation:  Scripts can be used "outside" the cluster

The first part of the documentation provides all the necessery information about using the cluster. Combined with the trouble shooting section it should be enough for students who write their Bachelor and masterthesis on the cluster. 
The latter sections cover extensions to the manufactorer's state and provide insight into design decisions. 

# Starting the cluster

The 60 client modules of the cluster are booted via TFTP and NFS-server located on a server module. Netbooting the Raspberry Pi is only possible on the ethernet interface at this point so the ethernet interface of the server 
module is already in use providing the clients their boot and root images. The wireless interface on the server module in turn connects to an external wifi that we will use for connecting to the cluster.

## Preparing your workstation

* Receive the password and make sure the wifi "nostromo" is visible at your location

Optional help:
```
# Receives the names of your wireless interfaces
iw dev | awk '$1=="Interface"{print $2}'
# Scans for the cluster's wifi
iwlist <wifi-interface> scan | grep "nostromo"
```

* Connect to the wifi with the provided password

## Powering the modules and adding route into cluster

* Connect the main power supply
* Wait until the client modules are successfully rebooted and at least some yellow LED are on
* Ping the server module and add a route into the cluster
```
# The server's ip address is provided by the dhcp server and should be the same
ping 192.168.1.111
# If you are sure the server module is answering, add the route
sudo ip route add 10.42.0.0/24 via 192.168.1.111
# The route should be in route's output
route
# 10.42.0.0       sevastopol      255.255.255.0   UG    0      0        0 wlp3s0
```
* You will notice a lot of modules were not started initially. With a functioning route into the cluster you can use the script `cluster_start` that will boot the remaining modules.
###### Dependencies cluster_start
  1. You need a working python interpreter with websocket client
  2. Download `cluster_start` and `controll.py` from workstation directory into the same directory
  3. Install `fping`
  ```
  sudo apt-get install fping
  ```
  4. Run `cluster_start`
  ```
  ./cluster_start
  ```
  5. After some time every module will be booted. You can find further details about how this works in the troubleshooting section.

* Optional: For scripts running outside the cluster an additional account `outsider` was created. You can add your ssh credentials to that account on the server module so your scripts can run without password questions.
```
ssh-copy-id outsider@192.168.1.111
```

* Optional: It's easier to work directly on the data stock without having to ssh into the server module everytime
```
# Install sshfs
sudo apt-get install sshfs
# Create a directory where we can mount on
mkdir -p cluster_mount
# Mount the root partition of the server module
sshfs -o allow_other pi@192.168.1.111:/ ./cluster_mount 2>&1
```

* Optional: Use `cluster_mount` available in workstation context
```
# Usuage:
./cluster_mount root|pxe on|off
# root for the server module's root partition
# pxe for the directory the client modules get their boot/root partition from
```

## Using and running commands on the cluster

There are multiple ways to run commands on the server and the client modules. The most basic tasks like restarting or shutting down all modules can be done with the manufactorer's software on the server module. They are located on the server module in the client directory in user pi's home directory.

```
ssh pi@192.168.1.111
cd client
```

If you need to run a task just once on a client module the easiest way is to ssh into the client's sshd.

  1. Make sure the client is booted successfully and you have a working route on your workstation into the cluster
  ```
  ping <client ip>
  route
  ```
  2. Ssh via console or script
  ```
  ssh outsider@<client ip>
  ```
  
  ```
  #!/bin/bash
  ssh -o StrictHostKeyChecking=no outsider@<client ip> 'commands to run seperated by semicolon'
  # or
  ssh -o StrictHostKeyChecking=no outsider@<client ip> < file_with_commands_created_in_the_same_directory
  ```

| Note |
| :--- |
| Using variables in the ssh command can be a bit tricky. For a possible solution take a look at the `sim_start_on_nodes` in server module context. |

You can also use the sim_\* toolchain provided in server module context. The sim_-scripts distribute/run the software to/on the clients and keep the standard error/output in the clients' individual directories.

TODO: Mv this part to server module context.

### Copying simulation files or custom software to nodes

When the nodes finished booting they mount an indidivual content directory based on their hostname outside the root partition. Every content directory in `/pxe/nodes` is exclusively worked on 
by the specific node and outside the scope of the other nodes. The individual content directory includes a `simulation` directory that is used to make external scripts, software or simulation files 
available to the specific node. 

#### Making the simulation files available

The directory `/pxe/meta/simulation` on the server module holds simulation files and software in seperate directories. The script `sim_to_nodes` makes those available to the nodes in their individual content directories. 

* We create a new directory for our simulation content

```
sim_dir="/pxe/meta/simulation/sim01"
ssh pi@192.168.1.111 "mkdir $sim_dir"
```

##### Note: If you are using the code snippet in a script there can be problems with bash internals variable interpolation.

| Note |
| :--- |
| If you are using the code snippet in a script there can be problems with bash internals variable interpolation. |

* We upload our simulation content into that directory


Basic overview `starter` script client context
* Takes a command and parameters
* Checks whether the command is available on the node
* Handles signals for command
* Creates custom file descriptors for standard output and error
* Logs where to find the file descriptors in individual node directory and additional debug information
* Blocks when everything is ready and waits for SIGCONT
* Runs the command after signal was received
* Logs return codes of command

###### Example in bash



###### Example in R





## Connecting the power supply

* Connect the main power supply on the backside
* The server module will power all modules (red led is active)
* Some self tests are performed
* The modules are rebooted by geographic position (0 to 60)
* The yellow led will power on after the module executes /etc/rc.local at least once successfully

There will be modules left that are not able to start initially. We can start them by running cluster_start in the workstation directory. For that you need to be connected to the same WiFi than the server module and 
add valid route into the cluster.

## Connecting to the cluster and adding a route

1. Connect to the WiFi with SSID "nostromo"
2. Get the server module's ip address and add a route to the modules
```
sudo ip route add 10.42.0.0/24 via 192.168.1.111
# 192.168.1.111 being the server modules ip
```
3. Route output should contain the added route
```
route
# 10.42.0.0       sevastopol      255.255.255.0   UG    0      0        0 wlp3s0
```

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