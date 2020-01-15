# Starting the cluster

The cluster is simply started with connecting the electric cable to the main socket. The manufacturer's proprietary software will power every single module, do 
some self tests and then restarts them. You can find a more detailed overview in the manufacturer's manual. In theory every module should be booted within minutes. 
In the practical environment however a lot of modules won't boot properly because of [https://github.com/raspberrypi/firmware/issues/862](problems with the firmware) 
and issues with the TFTP or DHCP server. Usually traffic on the network interfaces and/or rebooting single modules will solve these issues. For that you can use 
the provided python and bash script either in workstation or server context.

## Preparation

1. Install python
2. Install fping
```sudo apt-get install fping -y```
3. Only if you are in workstation context. Make sure the route to the modules is present for your wifi interface.
```ip route```
```10.42.0.0/24 via 192.168.1.111 dev wlp3s0```
3.1 If there is no route present add it manually
```sudo ip route add 10.42.0.0/24 via 192.168.1.111```

Network activity 
especially [https://github.com/raspberrypi/firmware/issues/894](broadcasted traffic) can solve these problems.



A script that 

apt-get install fping

There are several ways to start the cluster. The most reliable methods base on triggering the manufacturer's proprietary software that handles the boot process. 