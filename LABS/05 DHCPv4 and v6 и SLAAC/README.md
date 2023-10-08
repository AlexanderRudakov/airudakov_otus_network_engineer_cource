# DHCPv4/v6 и SLAAC




Addressing table:
| Device        | Interface     | IP Address   | Subnet Mask   | Default Gateway |
| ------------- | ------------- | ----------   | ----------    | --------------  |
| R1            |               |              |               |                 |
| R1            |               |              |               |                 |
| S1            |               |              |               |                 |
| S2            |               |              |               |                 |
| PC-A          | NIC           |              |               |                 |
| PC-B          | NIC           |              |               |                 |


VLAN Table:
| VLAN          | Name          | Interface Assigned   |
| ------------- | ------------- | -------------------- |
| 1             | N/A           | S2: et 0/0           |
| 100           | Clients       | S1: et 0/0           |
| 200           | Management    |                      |
| 999           | Parking_Lot   |                      |
| 1000          | Native        |                      |

# Objectives

Part 1: Build the Network and Configure Basic Device Settings

Part 2: Configure and verify two DHCPv4 Servers on R1

Part 3: Configure and verify a DHCP Relay on R2

## Part 1: Build the Network and Configure Basic Device Settings

**Main Scheme**

![mainscheme](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/05%20DHCPv4%20and%20v6%20%D0%B8%20SLAAC/pictures/Lab%20-%20Implement%20DHCPv4.PNG)

