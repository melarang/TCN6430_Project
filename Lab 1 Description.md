# Lab 1: Configure IP Addresses
- [Devices Description](#devices)
- [logical (layer 3) diagram](#diagram)
- [Logging In](#login)
- [Tasks](#tasks)
  - [1. Linux Router R1](#R1ips)
  - [2. Cisco R2 & R3](#R2R3ips)
  - [3. Juniper BR](#BRips)
- [Submission](#submission)
  
## <a name="devices"></a> Devices Description

R1 - Linux router running the Quagga routing suite <br>
R2 - Cisco 7206 router <br>
R3 - Cisco 7206 router <br>
BR - Juniper router and border router to the network <br>
WS0 – end workstation that connects to network 0 and functions as DHCP server <br>
WS1 – end workstation that connects to network 1 <br>
WS2 – end workstation that connects to network 2 <br>
WS3 – end workstation that connects to network 3 <br>

## <a name="diagram"></a> Logical (layer 3) diagram
![logical diagram](https://users.cs.fiu.edu/~esj/cnt4504/figs/logical-a.png)

## <a name="login"></a> Logging In
vm server is fluorine.cs.fiu.edu

XX is group number 61-63

4XX00   - BR serial console via telnet protocol un=juniper pw=juniper1 <br>
4XX01   - R1 console via VNC protocol pw=cis2008PW un=root pw=r1password <br>
4XX02   - R2 serial console via telnet protocol enable=cisco <br>
4XX03   - R3 serial console via telnet protocol enable=cisco <br>
4XX04   - arista switch serial console via telnet protocol un=admin pw=arista <br>
4XX10   - ws0 console via VNC protocol vnc pw=cis2008PW un=root pw=aitgXXpw <br>
4XX11   - ws1 console via VNC protocol vnc pw=cis2008PW un=root pw=cis2008PW <br>
4XX12   - ws2 console via VNC protocol vnc pw=cis2008PW un=root pw=cis2008PW <br>
4XX13   - ws3 console via VNC protocol vnc pw=cis2008PW un=root pw=cis2008PW

ws0 for each can be accessed from hosts in the department (like 
margay.cs.fiu.edu) via

ssh cisnlab-ws0-gXX.cs.fiu.edu -l root with password aitgXXpw


For the telnet access to out of band consoles you would 
telnet fluorine.cs.fiu.edu 4XXYY  (XX is group, YY is 
from table above)

For VNC use a vnc client to talk to fluorine.cs.fiu.edu
on the port listed.  VNC is also only available from 
department hosts.  (though if student provides a public 
IP for a home or office machine, it can be allowed off campus
VNC access. )

## <a name="tasks"></a> Tasks
Configure the correct IP address (according to the logical layer 3 diagram) on each interface of each router. View and save the changes made into the running configuration and then view the running configuration. 

#### <a name="R1ips"></a> 1. Linux Router R1
use vtysh command to control zebra. vtysh is a shell for FRR daemons. It provides users with a Cisco IOS-like shell to operate quagga-based routers so all router configuration commands must be done from here.
```
[root@r1 ~]# vtysh
r1#
```

Enter configuration mode. Configuration mode allows users to modify the running system configuration.
```
r1# conf terminal
r1(config)#
```

configure each interface with the correct IP address according to the diagram.

Example: Assign 10.61.1.129/26 to R1 on interface eth0
```
r1(config)# interface eth0                  - enter interface mode 
r1(config-if)# ip address 10.61.1.129/26    - set ip address 
r1(config-if)# description link to net2     - add a description 
r1(config-if)# link-detect                  - allow zebra to detect changes in link state 
r1(config-if)# quit                         - return to regular config mode 
```

Check that the interface is up and has been configured with the correct IP address.
```
r1# show interface eth0
  Description: link to net2
  Index 2 metric 1 mtu 1500 <UP,BROADCAST,RUNNING,MULTICAST>
HWaddr: 52:54:00:c1:1d:e2
inet 10.61.1.129/26 broadcast 10.61.1.191
inet6 fe80::5054:ff:fec1:1de2/64 
…
```

Check the changes made by viewing the running configuration.
```
r1#show running-config
Building configuration...
```

Finally, save the changes made.
```
r1# copy running-config startup-config
Building Configuration…
Configuration saved to /mnt/hda1/quagga/zebrad.conf
Configuration saved to /mnt/had1/quagga/ospfd.conf
[ok]
```

#### <a name="R2R3ips"></a> 2. Cisco R2 & R3
Enter enable prompt and from there enter global configuration mode.
```
changeme>enable
password:cisco
changeme#                 - Privileged EXEC (enable) prompt
changeme#config terminal
changeme(config)#         - global configuration mode
```

Change the name of the router to R2.
```
changeme(config)#hostname R2
R2(config)#
```

configure each interface with the correct IP address according to the diagram. <br>
Note: Cisco IOS does not understand slash notation; therefore, the netmask must be entered in dotted quad notation.

Example: Assign 10.61.0.2/24 to R2 on interface eth1/0
```
R2(config)#interface e1/0                            - enter interface configuration
R2(config-if)#ip address 10.61.0.2 255.255.255.0     - set ip addresses
R2(config-if)#no shutdown                            - bring interface up
R2(config-if)#description link to net0               - add a description
R2(config-if)#exit                                   - leave interface configuration mode
```

Check that the interfaces are up and have been configured with the correct IP address.
```
R2#show ip interface brief
Interface             IP-Address      OK? Method Status                Protocol
FastEthernet0/0       unassigned      YES NVRAM  administratively down down
Ethernet1/0           10.61.0.2       YES manual up                    up
Ethernet1/1           unassigned      YES NVRAM  administratively down down
Ethernet1/2           unassigned      YES NVRAM  administratively down down
Ethernet1/3           unassigned      YES NVRAM  administratively down down
```

Check the changes made by viewing the running configuration.
```
R2#show running-config
Building configuration...

Current configuration : 1298 bytes
…
```

Finally, save the changes made.
```
R2#copy run start
Destination filename [startup-config]?
Building configuration...
[OK]
```

#### <a name="BRips"></a> 1.3 Juniper BR
Enter configuration mode.
```
juniper@br> edit
Entering configuration mode

[edit]
juniper@br#
```

Configure the interfaces with the correct IP addresses according to the diagram.

Example: Assign 10.90.0.61/24 to BR on interface fxp0
```
[edit]
juniper@br# edit interfaces                                 -move down the hierarchy to edit the interface tree

[edit interfaces]
juniper@br# edit fxp0                                       

[edit interfaces fxp0]
juniper@br# edit unit 0

[edit interfaces fxp0 unit 0]
juniper@br# set family inet address 10.90.0.61/24           -set IP address

[edit interfaces fxp0 unit 0]
juniper@br# set description "connection to ISP peering net" -add a description

[edit interfaces fxp0 unit 0]
juniper@br# show                                            -show changes
description "connection to ISP peering net";
family inet {
    address 10.90.0.61/24;
}

[edit interfaces fxp0 unit 0]
juniper@br# up                                              -move up a level in the hierarchy

[edit interfaces fxp0]
juniper@br# up

[edit interfaces]
juniper@br#
```

Permanently save changes and go back to operational mode.
```
[edit interfaces fxp1 unit 0]
juniper@br# commit and-quit
commit complete
Exiting configuration mode

juniper@br>
```

Check that the interfaces are up and have been configured with the correct IP address.
```
juniper@br> show interfaces terse
Interface               Admin Link Proto    Local                                                                                    Remote
dsc                     up    up
fxp0                    up    up
fxp0.0                  up    up   inet     10.90.0.61/24
fxp1                    up    up
gre                     up    up
ipip                    up    up
lo0                     up    up
lo0.0                   up    up   inet     127.0.0.1                                                                              --> 0/0
lo0.16385               up    up   inet
                                   inet6    fe80::5254:ff:fede:e                                                                   69a
lsi                     up    up
mtun                    up    up
pimd                    up    up
pime                    up    up
tap                     up    up
```

Check the changes made by viewing the running configuration.
```
juniper@br> show configuration
version 8.0R1.9;
…
```

## <a name="submission"></a> Submission
Submit the complete configurations for each router and include a description of what you have learned and any problems encountered. 