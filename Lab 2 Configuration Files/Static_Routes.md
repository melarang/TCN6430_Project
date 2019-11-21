- [logical (layer 3) diagram](#diagram)
- [1. R1 Routes](#R1routes)
- [2. R2 and R3 Routes](#R2&3routes)
- [3. BR Routes](#BRroutes)

## <a name="diagram"></a> Logical (layer 3) diagram
![logical diagram](https://users.cs.fiu.edu/~esj/cnt4504/figs/logical-a.png)

### <a name="R1routes"></a> 1. R1 Routes
| Destination      |	Next-hop              |
|------------------|--------------------------|
|10.61.0.0/24(net0)|	10.61.1.193(R2 eth1/1)|
|10.61.2.0/24(net3)|	10.61.1.197(R3 eth1/1)|
|0.0.0.0/0(default)|	10.61.1.193(R2 eth1/1)|

### <a name="R2&3routes"></a> 2. R2 and R3 Routes
|Destination         |	Next-hop            |
|--------------------|----------------------|
|10.61.1.0/25(net1)  |	10.61.1.194(R1 eth2)|
|10.61.1.128/26(net2)|	10.61.1.194(R1 eth2)|
|10.61.2.0/24(net3)	 |10.61.0.3(R3 eth1/0)  |
|0.0.0.0/0(default)	 |10.61.0.1(BR fxp1)    |

|Destination         |	Next-hop            |
|--------------------|----------------------|
|10.61.1.0/25(net1)  |	10.26.1.198(R1 eth3)|
|10.61.1.128/26(net2)|	10.26.1.198(R1 eth3)|
|0.0.0.0/0(default)  |	10.26.0.1(BR fxp1)  |


### <a name="BRroutes"></a> BR Routes
|Destination                           |	Next-hop            |
|--------------------------------------|------------------------|
|10.61.1.192/30(link between R2 and R1)|	10.61.0.2(R2 eth1/0)|
|10.61.1.196/30(link between R3 and R1)|	10.61.0.3(R3 eth1/0)|
|10.61.1.0/25(net1)                    |	10.61.0.2(R2 eth1/0)|
|10.61.1.128/26(net2)                  |	10.61.0.2(R2 eth1/0)|
|10.61.2.0/24(net3)                    |	10.61.0.3(R3 eth1/0)|
|0.0.0.0/0(default)                    |	10.90.0.254(ISP)|
