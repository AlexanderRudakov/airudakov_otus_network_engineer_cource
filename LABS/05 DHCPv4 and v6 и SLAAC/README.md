# DHCPv4/v6 и SLAAC




Addressing table:
| Device        | Interface     | IP Address   | Subnet Mask      | Default Gateway |
| ------------- | ------------- | ----------   | ---------------  | --------------  |
| R1            | et0/0         | 10.0.0.1     | 255.255.255.252  | N/A             |
| R1            | et0/0.100     | 192.168.1.1  | 255.255.255.192  | N/A             |
| R1            | et0/0.200     | 192.168.1.65 | 255.255.255.224  | N/A             |
| R1            | et0/0.1000    | N/A          | N/A              | N/A             |
| R1            | et0/1         | N/A          | N/A              | N/A             |
| R2            | et0/0         | 10.0.0.2     | 255.255.255.252  | N/A             |
| R2            | et0/1         | 192.168.1.97 | 255.255.255.240  | N/A             |
| S1            | VLAN200       |              |                  |                 |
| S2            | VLAN1         |              |                  |                 |
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

