# DHCPv4




Addressing table:
| Device        | Interface     | IP Address   | Subnet Mask      | Default Gateway |
| ------------- | ------------- | ----------   | ---------------  | --------------  |
| R1            | et0/0         | 10.0.0.1     | 255.255.255.252  | N/A             |
| R1            | et0/1.100     | 192.168.1.1  | 255.255.255.192  | N/A             |
| R1            | et0/1.200     | 192.168.1.65 | 255.255.255.224  | N/A             |
| R1            | et0/1.1000    | N/A          | N/A              | N/A             |
| R1            | et0/1         | N/A          | N/A              | N/A             |
| R2            | et0/0         | 10.0.0.2     | 255.255.255.252  | N/A             |
| R2            | et0/1         | 192.168.1.97 | 255.255.255.240  | N/A             |
| S1            | VLAN200       | 192.168.1.66 | 255.255.255.224  | 192.168.1.97    |
| S2            | VLAN1         | N/A          | N/A              | N/A             |
| PC-A          | NIC           | DHCP         | DHCP             | DHCP            |
| PC-B          | NIC           | DHCP         | DHCP             | DHCP            |



VLAN Table:
| VLAN          | Name          | Interface Assigned   |
| ------------- | ------------- | -------------------- |
| 1             | N/A           | S2: et 0/0           |
| 100           | Clients       | S1: et 0/0           |
| 200           | Management    | S1: VLAN200          |
| 999           | Parking_Lot   | S1: et0/1-3          |
| 1000          | Native        | N/A                  |

# Objectives

Part 1: Build the Network and Configure Basic Device Settings

Part 2: Configure and verify two DHCPv4 Servers on R1

Part 3: Configure and verify a DHCP Relay on R2

## Part 1: Build the Network and Configure Basic Device Settings

**Main Scheme**

![mainscheme](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/05%20DHCPv4%20and%20v6%20%D0%B8%20SLAAC/pictures/mainscheme.PNG)



***Why is interface F0/5 listed under VLAN 1?*** - В моем случае это интерфейс **et0/1**, так как VLAN1 он назначается на все интерфейсы по умолчанию.

***Step 9: Step 9: Manually configure S1’s interface F0/5 as an 802.1Q trunk.*** - В моем случае речь идет об интерфейсе **et0/1**. И вопрос в этом шаге звучал так "***At this point, what IP address would the PC’s have if they were connected to the network using DHCP?*** То **PC-A** получил бы адрес ***192.168.1.X/26***.

Так как:
```
S1#sh  int status

Port      Name               Status       Vlan       Duplex  Speed Type 
Et0/0                        connected    100          auto   auto unknown
```
Конечно, при условии, что DHCP настроен и функционирует на R1.

## Ход работы

Рачитать подсеть исходя из условия лабораторной работы это должна быть 1 сеть **/24** и поделить на 3 части, сеть А, В, С соответсвенно:

- А: 192.168.1.0 255.255.255.192 (VLAN100) на 58 хостов
- B: 192.168.1.65 255.255.255.224 (VLAN200) на 28 хостов
- C: 192.168.1.96 255.255.255.240 (VLAN1) на 12 хостов

Далее нужно было выполнить базовую настройку маршрутизаторов и коммутаторов:

```
!
hostname R1
!
no ip domain lookup
!
service password-encryption
!
enable secret 5 $1$aD1I$wSEtLv.cJKCeYmCGxP4VP/
!
banner motd ^CCC
Attention!
Please log out immediately if you are not an authorized administrator!
^C 
!         
line con 0
 exec-timeout 0 0
 password 7 1511021F0725
 logging synchronous
 login    
line aux 0
line vty 0 4
 exec-timeout 0 0
 password 7 01100F175804
 logging synchronous
 login    
 transport input none
!         
```
на **R2** конфигурация аналогичная.

Базовая настройка на примере S1 выглядит следующим образом:
```
!
hostname S1
!
enable secret 5 $1$eHly$8IcMbud/3gU/65XJ/kGo0/
!
no ip domain lookup
!         
banner motd ^CC
Attention!
Please log out immediately if you are not an authorized administrator!
^C        
!
line con 0
 exec-timeout 0 0
 password 7 060506324F41
 logging synchronous
 login    
line aux 0
line vty 0 4
 exec-timeout 0 0
 logging synchronous
 login    
!       
```

Дальше нужно было настроить маршрутизацию между **R1** и **R2** (маршрут по умолчанию) и проверить работоспособность (по ping должен быть доступен интерфейс et0/1 на **R2**)

**R1**
```
R1#sh ip route
Gateway of last resort is 10.0.0.2 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 10.0.0.2
      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.0.0.0/30 is directly connected, Ethernet0/0
L        10.0.0.1/32 is directly connected, Ethernet0/0
```

**R2**
```
R2#sh ip route 
Gateway of last resort is 10.0.0.1 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 10.0.0.1
      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.0.0.0/30 is directly connected, Ethernet0/0
L        10.0.0.2/32 is directly connected, Ethernet0/0
```

```
R1#ping 192.168.1.97
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.97, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
```

Далее нужно было настроить VLAN на свичах, и SVI интерфейсы (стоит отметить, что использование VLAN1 на **S2** было условием лабораторной работы - на практике следует избегать VLAN1):

**S1** 
```
S1#sh vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
100  CLIENTS                          active    Et0/0
200  MANAGEMENT                       active    
999  PARKING-LOT                      active    Et0/2, Et0/3
1000 NATIVE                           active    
!
!
S1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES unset  up                    up      
Ethernet0/1            unassigned      YES unset  up                    up      
Ethernet0/2            unassigned      YES unset  administratively down down    
Ethernet0/3            unassigned      YES unset  administratively down down    
Vlan200                192.168.1.66    YES NVRAM  up                    up     
```


**S2**
```
S2#sh vla

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/0
999  VLAN0999                         active    Et0/2, Et0/3
1000 NATIVE                           active   
!
!
S2#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES unset  up                    up      
Ethernet0/1            unassigned      YES unset  up                    up      
Ethernet0/2            unassigned      YES unset  administratively down down    
Ethernet0/3            unassigned      YES unset  administratively down down    
Vlan1                  192.168.1.98    YES manual up                    up 
```

Дальше нужно было настроить DHCP  на **R1** и DHCP relay на **R2** (на **R2** это выполняется на интерфейсе который смотрит в сеть с хостами где нужна настройка DHCP). Помимо шлюза нужно было настроить еще и время аренды "2 дня, 12 часов, 30 минут".

**R1**
```
R1#sh running-config | section dhcp
ip dhcp excluded-address 192.168.1.1 192.168.1.6
ip dhcp pool POOL100
 network 192.168.1.0 255.255.255.192
 default-router 192.168.1.1 
 lease 2 12 30
ip dhcp pool POOL1R2
 network 192.168.1.96 255.255.255.240
 default-router 192.168.1.97 
 lease 2 12 30
```
**R2** 
```
R2#sh run int et0/1
Building configuration...

Current configuration : 98 bytes
!
interface Ethernet0/1
 ip address 192.168.1.97 255.255.255.240
 ip helper-address 10.0.0.1
end
```

Результатом проделанных действиий стало то что **PC-A** и **PC-В** получили свои адреса из нужных нам подсетей и смогли пропинговать друг друга:

**PC-A**
```
VPCS>  ip dhcp
DDORA IP 192.168.1.7/26 GW 192.168.1.1

VPCS> ping 192.168.1.100
84 bytes from 192.168.1.100 icmp_seq=1 ttl=62 time=2.687 ms
84 bytes from 192.168.1.100 icmp_seq=2 ttl=62 time=1.090 ms
84 bytes from 192.168.1.100 icmp_seq=3 ttl=62 time=1.159 ms
84 bytes from 192.168.1.100 icmp_seq=4 ttl=62 time=0.968 ms
84 bytes from 192.168.1.100 icmp_seq=5 ttl=62 time=0.951 ms
```

**PC-В**
```
VPCS> ip dhcp
DDORA IP 192.168.1.100/28 GW 192.168.1.97

VPCS> ping 192.168.1.7

84 bytes from 192.168.1.7 icmp_seq=1 ttl=62 time=2.432 ms
84 bytes from 192.168.1.7 icmp_seq=2 ttl=62 time=0.987 ms
84 bytes from 192.168.1.7 icmp_seq=3 ttl=62 time=0.987 ms
84 bytes from 192.168.1.7 icmp_seq=4 ttl=62 time=0.937 ms
84 bytes from 192.168.1.7 icmp_seq=5 ttl=62 time=1.075 ms
```

***Все вайлы конфигураций находятся в*** [папке](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/tree/main/LABS/05%20DHCPv4%20and%20v6%20%D0%B8%20SLAAC/configs)


Далее будет рассмотрена настройка для IPv6.

# DHCP/v6

**Addressing Table**

| Device        | Interface     | IPv6 Address           |
| ------------- | ------------- | -----------------------|
| R3            | et0/0         | 2001:db8:acad:2::1 /64 |
|               |               | fe80::1                |
| R3            | et0/1         | 2001:db8:acad:1::1/64  |
|               |               | fe80::1                |
| R4            | et0/0         | 2001:db8:acad:2::2/64  |
|               |               | fe80::2                |
| R4            | et0/1         | 2001:db8:acad:3::1 /64 |
|               |               | fe80::1                |
|LAP-A          | NIC           | DHCP                   |
|LAP-B          | NIC           | DHCP                   |


Базовая настройка устройств аналогична настройке для **DHCPv4** отличия только в имени хостов:
```
hostname R3
hostname S3
hostname R4
hostname S4
```

![mainscheme](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/05%20DHCPv4%20and%20v6%20%D0%B8%20SLAAC/pictures/DHCPv6main_scheme.PNG)


Далее цитата из описания лабораторной работы:
>Step 4: Configure interfaces and routing for both routers.
a.	Configure the G0/0/0 and G0/0/1 interfaces on R1 and R2 with the IPv6 addresses specified in the table above.
b.	Configure a default route on each router pointed to the IP address of G0/0/0 on the other router.
c.	Verify routing is working by pinging R2’s G0/0/1 address from R1
d.	Save the running configuration to the startup configuration file.

Настройка интерфейсов и статического маршрута по умолчанию выглядит так:
**SR3**
``` 
R3(config-if-range)#int et0/0      
R3(config-if)#ipv6 address 2001:db8:acad:2::1/64
R3(config-if)#ipv6 address fe80::1 link-local 
R3(config-if)#int et0/1
R3(config-if)#ipv6 address  2001:db8:acad:1::1/64
R3(config-if)#ipv6 address fe80::1 link-local 
!
R3#sh ipv6 interface brief
Ethernet0/0            [up/up]
    FE80::1
    2001:DB8:ACAD:2::1
Ethernet0/1            [up/up]
    FE80::1
    2001:DB8:ACAD:1::1
!
R3(config)#ipv6 unicast-routing
!
R3(config)#ipv6 route ::/0 2001:DB8:ACAD:2::2
!
R3#ping 2001:DB8:ACAD:3::1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:ACAD:3::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
!
```

**R4**
```
R4(config)#int et 0/0
R4(config-if)#ipv6 address 2001:db8:acad:2::2/64
R4(config-if)#ipv6 address fe80::2 link-local 
R4(config-if)#int et 0/1
R4(config-if)#ipv6 address 2001:db8:acad:3::1/64 
R4(config-if)#ipv6 address fe80::1 link-local 
R4(config-if)#exit
!
ipv6 unicast-routing
!
R4(config)#do sh ipv6 int br
Ethernet0/0            [up/up]
    FE80::2
    2001:DB8:ACAD:2::2
Ethernet0/1            [up/up]
    FE80::1
    2001:DB8:ACAD:3::1
Ethernet0/2            [administratively down/down]
    unassigned
Ethernet0/3            [administratively down/down]
    unassigned
!
R4(config)#ipv6 route ::/0 2001:DB8:ACAD:2::1
!
R4#ping 2001:DB8:ACAD:1::1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:ACAD:1::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
!
```

Результат такой, что **LAP-A** получает IPv6 адрес и может пинговать **LAP-B**
**LAP-A**
```
VPCS> show ipv6

NAME              : VPCS[1]
LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:6807/64
GLOBAL SCOPE      : 2001:db8:acad:1:2050:79ff:fe66:6807/64
DNS               : 
ROUTER LINK-LAYER : aa:bb:cc:00:b0:10
MAC               : 00:50:79:66:68:07
LPORT             : 20000
RHOST:PORT        : 127.0.0.1:30000
MTU:              : 1500

VPCS> ping 2001:db8:acad:3:2050:79ff:fe66:6808

2001:db8:acad:3:2050:79ff:fe66:6808 icmp6_seq=1 ttl=60 time=1.088 ms
2001:db8:acad:3:2050:79ff:fe66:6808 icmp6_seq=2 ttl=60 time=1.170 ms
2001:db8:acad:3:2050:79ff:fe66:6808 icmp6_seq=3 ttl=60 time=1.391 ms
2001:db8:acad:3:2050:79ff:fe66:6808 icmp6_seq=4 ttl=60 time=1.030 ms
2001:db8:acad:3:2050:79ff:fe66:6808 icmp6_seq=5 ttl=60 time=1.122 ms
```

**LAP-B**
```
VPCS> sh ipv6

NAME              : VPCS[1]
LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:6808/64
GLOBAL SCOPE      : 2001:db8:acad:3:2050:79ff:fe66:6808/64
DNS               : 
ROUTER LINK-LAYER : aa:bb:cc:00:c0:10
MAC               : 00:50:79:66:68:08
LPORT             : 20000
RHOST:PORT        : 127.0.0.1:30000
MTU:              : 1500

VPCS> ping 2001:db8:acad:1:2050:79ff:fe66:6807

2001:db8:acad:1:2050:79ff:fe66:6807 icmp6_seq=1 ttl=60 time=20.046 ms
2001:db8:acad:1:2050:79ff:fe66:6807 icmp6_seq=2 ttl=60 time=1.127 ms
2001:db8:acad:1:2050:79ff:fe66:6807 icmp6_seq=3 ttl=60 time=1.050 ms
2001:db8:acad:1:2050:79ff:fe66:6807 icmp6_seq=4 ttl=60 time=1.606 ms
2001:db8:acad:1:2050:79ff:fe66:6807 icmp6_seq=5 ttl=60 time=1.294 ms
```




***Все вайлы конфигураций находятся в*** [папке](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/tree/main/LABS/05%20DHCPv4%20and%20v6%20%D0%B8%20SLAAC/configs)

