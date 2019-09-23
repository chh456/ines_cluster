# ines_cluster
Working scripts for the microcontroller cluster at INES University Kassel

## Server modul (servermodule)

Preparation on your workstation:

Copy your public ssh key to the server module so you will be able to connect to sshd without a password.

```
ssh-copy-id outsider@192.168.1.111
```



#### sim_to_nodes

Location: server module
Path: /pxe/meta/sim_to_nodes
Description: this script is used within R scripts to distribute simulations to single, range or multiple ranges of nodes
Usage: sim_to_nodes "Simulation Identifier" "1-3;7;33-51"

Preparation on your workstation:

Install the "ssh" package for R