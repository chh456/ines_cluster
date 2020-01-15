# Starting the cluster

The cluster is simply started with connecting the electric cable to the main socket. The manufacturer's proprietary software will power every single module, do 
some self tests and then restarts them. You can find a more detailed overview in the manufacturer's manual. In theory every module should be booted within minutes. 
In the practical environment however a lot of modules won't boot properly because of [problems with the firmware](https://github.com/raspberrypi/firmware/issues/862) 
and issues with the TFTP or DHCP server. Usually traffic on the network interfaces and/or rebooting single modules will solve these issues. For that you can use 
the provided python and bash script either in workstation or server context.

## Preparation in workstation context

1. Install python (2 or 3) with websocket-client
2. Install fping
```sudo apt-get install fping -y```
3. Check your routing table and make sure you are able to send packages to the nodes
```
ip route
# Output(similar): 10.42.0.0/24 via 192.168.1.111 dev wlp3s0
```
  3. If there is no route present add it manually
```sudo ip route add 10.42.0.0/24 via 192.168.1.111```

4. Make the script executable and start
```chmod +x relentless_start && ./relentless_start```

## Server context

On the server module you should be able to just run the script without any preparations.

```./relentless_start```

## Troubleshooting

If for any reason you are not able to run the script or don't want to risk restarting the nodes, you can try sending packages to the broadcast ip address with

```
ping -b -c 3 -i 20 192.168.0.255
```

You can manually restart the module or a range of modules with the python scripts located in ```~/client``` 
