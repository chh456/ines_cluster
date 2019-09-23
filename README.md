# ines_cluster
Working scripts for the microcontroller cluster at INES University Kassel

## Server module (servermodule)

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
Usage (R):		
```