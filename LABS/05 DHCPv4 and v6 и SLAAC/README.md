# DHCPv4/v6 и SLAAC




Addressing table:
| Device        | Interface     | IP Address   | Subnet Mask   | Default Gateway |
| ------------- | ------------- | ----------   | ----------    | --------------  |
| R1            | Ethernet0/2.3 | 192.168.3.1  | 255.255.255.0 | N/A             |
| R1            | Ethernet0/2.4 | 192.168.4.1  | 255.255.255.0 | N/A             |
| S1            | VLAN 3        | 192.168.3.11 | 255.255.255.0 | 192.168.3.1     |
| S2            | VLAN 4        | 192.168.4.12 | 255.255.255.0 | 192.168.4.1     |
| PC-A          | NIC           | 192.168.3.3  | 255.255.255.0 | 192.168.3.1     |
| PC-B          | NIC           | 192.168.4.3  | 255.255.255.0 | 192.168.4.1     |


VLAN Table:
| VLAN          | Name          | Interface Assigned   |
| ------------- | ------------- | -------------------- |
| 1             |               |                      |
| 100           |               |                      |
| 200           |               |                      |
| 999           |               |                      |
| 1000          |               |                      |

# Objectives

Part 1: Build the Network and Configure Basic Device Settings

Part 2: Configure and verify two DHCPv4 Servers on R1

Part 3: Configure and verify a DHCP Relay on R2

## Part 1: Build the Network and Configure Basic Device Settings

![mainscheme](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/05%20DHCPv4%20and%20v6%20%D0%B8%20SLAAC/pictures/Lab%20-%20Implement%20DHCPv4.PNG)