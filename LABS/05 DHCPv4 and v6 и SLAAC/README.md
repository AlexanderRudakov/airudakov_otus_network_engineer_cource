# DHCPv4/v6 и SLAAC




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
| S2            | VLAN1         | 192.168.1.98 | 255.255.255.240  | 192.168.1.97    |
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

![mainscheme](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/05%20DHCPv4%20and%20v6%20%D0%B8%20SLAAC/pictures/mainschemeDHCP_IPv4.PNG)



***Why is interface F0/5 listed under VLAN 1?*** - В моем случае это интерфейс **et0/1**, так как VLAN1 он назначается на все интерфейсы по умолчанию.

***Step 9: Step 9: Manually configure S1’s interface F0/5 as an 802.1Q trunk.*** - В моем случае речь идет об интерфейсе **et0/1**. И вопрос в этом шаге звучал так "***At this point, what IP address would the PC’s have if they were connected to the network using DHCP?*** То **PC-A** получил бы адрес ***192.168.1.X/26***.

Так как:
```
S1#sh  int status

Port      Name               Status       Vlan       Duplex  Speed Type 
Et0/0                        connected    100          auto   auto unknown
```
Конечно, при условии, что DHCP настроен и функционирует на R1.



***Все вайлы конфигураций находятся в*** [папке](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/tree/main/LABS/05%20DHCPv4%20and%20v6%20%D0%B8%20SLAAC/configs)

