# Lab 2: Static Routes
- [Devices Description](#devices)
- [logical (layer 3) diagram](#diagram)
- [Logging In](#login)
- [Overview](#overview)
- [Tasks](#tasks)
  - [1. R1 Routes](#R1routes)
  - [2. R2 and R3 Routes](#R2&3routes)
  - [3. BR Routes](#BRroutes)
  - [4. Check Connectivity](#connectivity)
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

## <a name="overview"></a> Overview
At this point we only have directly connected routes. For instance, R2 only knows how to reach 10.61.0.0/24 and 10.61.1.192/30. This means that to obtain full connectivity between all the subnetworks, we must configure static routes for the non-connected networks. Configure routing so that traffic between BR and networks 1 & 2 will go through R2; traffic between BR and network 3 will go through R3; traffic between R1 and network 0 will go through R2; traffic between R1 and network 3 will go through R3; and traffic between R1 and the default route will take the path of R1 > R2 > BR. Also include default route on BR that points to the ISP and on R1, R2, and R3 include a default route that points traffic to BR. Afterwards check the routing tables and check connectivity.

## <a name="tasks"></a> Tasks
### <a name="R1routes"></a> 1. R1 Routes
Set static routes on R1 following the guidelines stated above.
R1 is directly connected to 10.61.1.0/25 (net1), 10.61.1.128/26(net2), 10.61.1.192/30(link between R1 and R2), and 10.61.1.196/30(link between R1 and R3). R1 needs routes to get to network 0, network 3, and default. 

Example: Route on R1 to get to network 0 via R2
```
r1(config)# ip route 10.61.0.0/24 10.61.1.193
```

Check the routing table in order to view the added routes:
```
r1(config)# show ip route
Codes: K – kernel route, C - connected, S - static, R - RIP, O – OSPF,
       I - IS-IS, B – BGP, > - selected route, * - FIB route

S>* 10.61.0.0/24 [1/0] via 10.61.0.3, eth2
C>* 10.61.1.0/25 is directly connected, eth1
C>* 10.61.1.128/26 is directly connected, eth0
C>* 10.61.1.192/30 is directly connected, eth2
C>* 10.61.1.196/30 is directly connected, eth3
C>* 127.0.0.0/8 is directly connected, lo
```

### <a name="R2&3routes"></a> 2. R2 and R3 Routes
Set static routes on R2 and R3 following the guidelines stated above.
R2 is directly connected to 10.61.0.0/24(net0) and 10.61.1.192/30(link between R2 and R1). R2 needs routes to get to network 1, network 2, network 3, and default.

Example: Route on R2 to get to network 1 via R1 
```
R2(config)#ip route 10.61.1.0 255.255.255.128 10.61.1.194
```

R3 is directly connected to 10.61.0.0/24(net0), 10.61.2.0/24(net3), and 10.61.1.196/30(link between R3 and R1). R3 needs routes to network 1, network 2, and default.

Example: Route on R3 to get to network 1 via R1
```
R3(config)#ip route 10.61.1.0 255.255.255.128 10.61.1.198
```

Check the routing table on both routers in order to view the added routes:
```
R3#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter                                                                    area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external t                                                                   ype 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E -                                                                    EGP
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - I                                                                   S-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-use                                                                   r static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 5 subnets, 4 masks
C       10.61.2.0/24 is directly connected, Ethernet1/2
S       10.61.1.0/25 [1/0] via 10.61.1.198
C       10.61.0.0/24 is directly connected, Ethernet1/0
C       10.61.1.196/30 is directly connected, Ethernet1/1
```

### <a name="BRroutes"></a> BR Routes
Set static routes on BR following the guidelines stated above.
BR is directly connected to 10.61.0.0/24(net0) and 10.90.0.0/24(ISP Peering net). BR needs routes to link between R2 and R1, link between R3 and R1, network 1, network 2, network 3, and default.

Example: Route on BR to get to 10.61.1.192/30 via R2
```
[edit]
juniper@br# edit routing-options

[edit routing-options]
juniper@br# set static route 10.61.1.192/30 next-hop 10.61.0.2
```

Check the routing table in order to view the added routes:

```
juniper@br> show route
inet.0: 7 destinations, 7 routes (6 active, 0 holddown, 1 hid                                                                   den)
+ = Active Route, - = Last Active, * = Both

10.61.0.0/24       *[Direct/0] 1d 01:43:26
                    > via fxp1.0
10.61.0.1/32       *[Local/0] 1d 01:43:26
                      Local via fxp1.0
10.61.1.0/25       *[Static/5] 00:00:06
                    > to 10.61.0.2 via fxp1.0
10.61.1.192/30     *[Static/5] 00:00:06
                    > to 10.61.0.2 via fxp1.0
10.90.0.0/24       *[Direct/0] 1d 01:43:26
                    > via fxp0.0
10.90.0.61/32      *[Local/0] 1d 01:43:26
                      Local via fxp0.0

__juniper_private1__.inet6.0: 1 destinations, 1 routes (1 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

fe80::5254:ff:fede:e69a/128
                   *[Direct/0] 4w3d 01:11:12
                    > via lo0.16385
```

**Do not forget to save all your changes**

### <a name="connectivity"></a> 4. Check Connectivity
Now that all our static routes are set, it is time to check that we have full connectivity of the entire network meaning that the routers are able to communicate with the subnetworks that are not directly connected to them. To do this we use the `ping` command. ping verifies 2-way connectivity so if there is connectivity from the source to destination but the path from destination to source is not working, the ping will not work.

Example: ping interface eth1 on router R1 from router BR
```
juniper@br> ping 10.61.1.1
PING 10.61.1.1 (10.61.1.1): 56 data bytes
64 bytes from 10.61.1.1: icmp_seq=0 ttl=63 time=14.744 ms
64 bytes from 10.61.1.1: icmp_seq=1 ttl=63 time=17.874 ms
64 bytes from 10.61.1.1: icmp_seq=2 ttl=63 time=15.844 ms
^C
--- 10.61.1.1 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max/stddev = 14.744/16.154/17.874/1.296 ms
```

## <a name="submission"></a> Submission
Submit the complete configurations for each router and include a description of what you have learned and any problems encountered. 