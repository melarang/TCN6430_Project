# Lab 2 : Dynamic Host Configuration Protocol
- [Devices Description](#devices)
- [logical (layer 3) diagram](#diagram)
- [Logging In](#login)
- [overview](#overview)
- [Tasks](#tasks)
  - [1. Setting Up the Server](#server)
  - [2. DHCP Relay](#relay)
  - [3. DHCP Client](#client)
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
When a host boots up on a network, it does not have an IP address or information on how to access other network services such as the gateway router. The dynamic host configuration protocol allows hosts to get assigned an IP address and other network configuration parameters (such as netmask and default route) by a DHCP server. In this lab you will configure the DHCP server on ws0 and DHCP relay services on routers R1 and R3. You will also make ws1, ws2 and ws3 become DHCP clients. 

![DHCP](https://ns1.com/writable/resources/_asideImage/DHCP-Diagram-2.svg)

## <a name="tasks"></a> Tasks

### <a name="server"></a> 1. Setting Up the Server
In ws0, create a folder called `dhcpd` to hold all dhcp related files.
```
[root@group61ws0 ~]# cd /mnt/hda1
[root@group61ws0 hda1]# mkdir dhcpd
[root@group61ws0 hda1] #cd dhcpd
```

Create the following two empty files:
```
[root@group61ws0 dhcpd]# touch dhcpd-g61.leases 
[root@group61ws0 dhcpd]# touch dhcpd.conf
```

dhcpd requires that a lease database is present before it starts. To make the initial lease database the `dhcpd-g61.leases` file is created. [Man page for dhcpd.leases](https://linux.die.net/man/5/dhcpd.leases).
The `dhcpd-g61.conf` file is used to store the network configuration information required by DHCP clients. [Man page for dhcpd.conf](https://linux.die.net/man/5/dhcpd.conf).

Next, configure the DHCP server to support networks 0 through 3. For each network provide the DHCP clients with subnet mask and broadcast address. For network 0, specify a range which will allow 10 workstations within the IP range for that network. Make sure that all the workstations obtain the IP addresses stated in the logical diagram.
```
ws1 = 10.61.1.100 
ws2 = 10.61.1.150 
ws3 = 10.61.2.50
```

To do this, modify the sample `dhcp.conf` file below to meet the specified requirements for configuring your DHCP server. Use the `vi` editor to write to your `dhcp-g61.conf` file. 
```
[root@group61ws0 dhcpd]# vi dhcpd.conf
```

Below is an example of a dhcpd.conf file for the networks 
- 10.60.1.0/24 
- 10.60.2.0/25 
- 10.60.3.128/25 
- 10.60.4.0/24

For all networks it has a default lease of 300 seconds, and a maximum lease available of 1200 seconds. Network **10.60.1.0/24** supports DHCP assigned addresses from 10.60.1.50 to 10.60.1.250. Network **10.60.2.0/25** supports one dhcp assigned address of 10.60.2.80. Network **10.60.3.128/25** supports one dhcp assigned address of 10.60.3.180. Network **10.60.4.0/24** supports one host containing a specific MAC address.

```
authoritative;
max-lease-time 1200;
default-lease-time 300;

subnet 10.60.1.0 netmask 255.255.255.0 {
 option subnet-mask 255.255.255.0;
 option broadcast-address 10.60.1.255;
 option routers 10.60.1.1;

 range 10.60.1.50 10.60.1.250;
}

subnet 10.60.2.0 netmask 255.255.255.128 {
 option subnet-mask 255.255.255.128;
 option broadcast-address 10.60.2.127;
 option routers 10.60.2.1;

 range 10.60.2.80;
}

subnet 10.60.3.128 netmask 255.255.255.128 {
 option subnet-mask 255.255.255.128;
 option broadcast-address 10.60.3.127;
 option routers 10.60.3.129;

 range 10.60.3.150;
}

subnet 10.60.4.0 netmask 255.255.255.0 {
 option subnet-mask 255.255.255.0;
 option broadcast-address 10.60.4.255;
 option routers 10.60.4.1;
}

host ws3 {
 hardware ethernet 00:05:78:45:E2:B3;
 fixed-address 10.60.4.100;
 option host-name "ws3";
}
```

Start the DHCP server on ws0 for the entire network. `-cf` option specifies where to find the dhcpd-g61.conf file and `-lf` specifies where to store the lease information. eth0 is the ethernet interface on ws0 that faces network 0.
```
[root@group61ws0 dhcpd]# /usr/sbin/dhcpd -cf /mnt/hda1/dhcpd/dhcpd.conf -lf /mnt/hda1/dhcpd/dhcpd-g61.leases eth0
```

### <a name="relay"></a> 2. DHCP Relay
Set up dhcrelay processes on R3 and R1. The DHCP Relay Agent (dhcrelay) enables the relay of DHCP requests from a subnet with no DHCP server on it to one or more DHCP servers on other subnets.

Start dhcrelay on R1 to have it forward DHCP requests from hosts on networks 1 & 2 to ws0 (the DHCP server).
```
[root@r1 ~]# /usr/sbin/dhcrelay -m discard 10.61.0.20
```

Start dhcrelay on R3 eth1/2 to have it forward DHCP requests from hosts on network 3 to ws0 (the DHCP server).
```
R3(config)#interface e1/2
R3(config-if)#ip helper-address 10.61.0.20
```

### <a name="client"></a> 3. DHCP Client
Run dhclient on workstations 1, 2 & 3 so that they become DHCP clients.
```
[root@localhost ~]# dhclient eth0
```

If everything was configured correctly then the workstations should obtain IP addresses right away.

### <a name="connectivity"></a> 4. Check Connectivity
Check that the workstations can connect to each other.

Example: Check that ws3 can communicate with ws1
```
[root@ws3 ~]# ping 10.61.1.100
PING 10.61.1.100 (10.61.1.100): 56(84) bytes of data. 
64 bytes from 10.61.1.100: icmp_seq=1 ttl=62 time=29.1 ms
64 bytes from 10.61.1.100: icmp_seq=2 ttl=62 time=15.1 ms
64 bytes from 10.61.1.100: icmp_seq=3 ttl=62 time=11.7 ms
64 bytes from 10.61.1.100: icmp_seq=4 ttl=62 time=18.0 ms

[1]+   Stopped 		ping 10.61.1.100
```

## <a name="submission"></a> Submission
In your lab report, provide screenshots showing that workstations 1, 2, and 3 have obtained the correct IP addresses through DHCP. To do this you can run the command `dhclient -r eth0` which will tell the client to release the current lease that it has from the server. Then run `dhclient eth0` again to obtain the IP address back. Also include a description of what you have learned and any problems encountered.