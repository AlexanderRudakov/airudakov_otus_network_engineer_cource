# Inter VLAN Routing - LAB


Addressing table:
| Device        | Interface     | IP Address   | Subnet Mask   | Default Gateway |
| ------------- | ------------- | ----------   | ----------    | --------------  |
| R1            | Ethernet0/2.3 | 192.168.3.1  | 255.255.255.0 | N/A             |
| R1            | Ethernet0/2.4 | 192.168.4.1  | 255.255.255.0 | N/A             |
| S1            | VLAN 3        | 192.168.3.11 | 255.255.255.0 | 192.168.3.1     |
| S2            | VLAN 4        | 192.168.4.12 | 255.255.255.0 | 192.168.4.1     |
| PC-A          | NIC           | 192.168.3.3  | 255.255.255.0 | 192.168.4.1     |
| PC-B          | NIC           | 192.168.4.3  | 255.255.255.0 | 192.168.4.1     |


VLAN table:
| VLAN          | NAME          | Interface Assigned                        | 
| ------------- | ------------- | ------------------------------------------|  
| 3             | Management    | Et0/1                                     | 
| 4             | Operations    | Et0/1                                     | 
| 111 (S1)      | DUMP          | Et0/3, Et1/0, Et1/1, Et1/2 Et1/3          | 
| 111 (S1)      | DUMP          | Et0/2, Et0/3, Et1/0, Et1/1 Et1/2, Et1/3   |
| 8             | Native        |                                           | 


# Devices configuration:
## PC
**PC-A**

`192.168.3.3 255.255.255.0 gateway 192.168.3.1`

**PC-B**

`192.168.4.3 255.255.255.0 gateway 192.168.4.1`

## Switches
**S1**
```
S1#sh run<br/>
Building configuration...<br/>
service password-encryption<br/>
service compress-config<br/>
!<br/>
hostname S1<br/>
!<br/>
enable secret 5 $1$htQ6$dJRz11DS38X31nWOuSwgi1<br/>
!<br/>
username class privilege 15 secret 5 $1$S9e9$siqcJXFBMgyk6uGBAN9CY0<br/>
no aaa new-model<br/>
!    
no ip domain-lookup      
!        
spanning-tree mode rapid-pvst<br/>
spanning-tree extend system-id<br/>
!                        
interface Ethernet0/0<br/>
 switchport trunk allowed vlan 3,4<br/>
 switchport trunk encapsulation dot1q<br/>
 switchport trunk native vlan 8<br/>
 switchport mode trunk<br/>
 switchport nonegotiate<br/>
 spanning-tree portfast edge<br/>
!         
interface Ethernet0/1<br/>
 switchport access vlan 3<br/>
 switchport mode access<br/>
 switchport nonegotiate<br/>
 spanning-tree portfast edge<br/>
!         
interface Ethernet0/2<br/>
 switchport access vlan 111<br/>
 switchport trunk allowed vlan 3,4<br/>
 switchport trunk encapsulation dot1q<br/>
 switchport trunk native vlan 8<br/>
 switchport mode trunk<br/>
 switchport nonegotiate<br/>
 spanning-tree portfast edge<br/>
!         
interface Ethernet0/3<br/>
 switchport access vlan 111<br/>
 switchport mode access<br/>
 switchport nonegotiate<br/>
 shutdown <br/>
 spanning-tree portfast edge<br/>
!         
interface Ethernet1/0<br/>
 switchport access vlan 111<br/>
 shutdown <br/>
!         
interface Ethernet1/1<br/>
 switchport access vlan 111<br/>
 shutdown <br/>
!         
interface Ethernet1/2<br/>
 switchport access vlan 111<br/>
 shutdown <br/>
!         
interface Ethernet1/3<br/>
 switchport access vlan 111<br/>
 shutdown <br/>
!         
interface Vlan3<br/>
 ip address 192.168.3.11 255.255.255.0<br/>
!         
ip default-gateway 192.168.3.1<br/>
ip forward-protocol nd<br/>
!         
no ip http server<br/>
no ip http secure-server<br/>
!         
banner motd ^CUnauthorized access to this device is prohibited!^C
!         
line con 0<br/>
 password 7 104D000A0618<br/>
 logging synchronous<br/>
 login    <br/>
line aux 0<br/>
line vty 0 4<br/>
 password 7 0822455D0A16<br/>
 login    <br/>
!                  
end   `<br/>
```
**S2**
```
S2#sh run
Building configuration...
!
! Last configuration change at 15:14:11 UTC Tue Sep 19 2023
!
service password-encryption
!
hostname S2
!
enable secret 5 $1$u08L$ZU.rqktKlLB4tsK.gZMGT1
!
username class privilege 15 secret 5 $1$6DNJ$e0Cd4bCCF9dK0hmwPFpLx0       
!         
no ip domain-lookup                             
!         
interface Ethernet0/0
 switchport trunk allowed vlan 3,4
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 8
 switchport mode trunk
 switchport nonegotiate
 spanning-tree portfast edge
!         
interface Ethernet0/1
 switchport access vlan 4
 switchport mode access
 switchport nonegotiate
 spanning-tree portfast edge
!         
interface Ethernet0/2
 switchport access vlan 111
 switchport mode access
 switchport nonegotiate
 shutdown 
 spanning-tree portfast edge
!         
interface Ethernet0/3
 switchport access vlan 111
 switchport mode access
 switchport nonegotiate
 shutdown 
 spanning-tree portfast edge
!         
interface Ethernet1/0
 switchport access vlan 111
 switchport mode access
 switchport nonegotiate
 shutdown 
 spanning-tree portfast edge
!         
interface Ethernet1/1
 switchport access vlan 111
 switchport mode access
 switchport nonegotiate
 shutdown 
 spanning-tree portfast edge
!         
interface Ethernet1/2
 switchport access vlan 111
 switchport mode access
 switchport nonegotiate
 shutdown 
 spanning-tree portfast edge
!         
interface Ethernet1/3
 switchport access vlan 111
 switchport mode access
 switchport nonegotiate
 shutdown 
 spanning-tree portfast edge
!         
interface Vlan4
 ip address 192.168.4.12 255.255.255.0
!         
ip default-gateway 192.168.4.1              
!         
banner motd ^CUnauthorized access to this device is prohibited!^C
!         
line con 0
 password 7 121A0C041104
 logging synchronous
 login    
line aux 0
line vty 0 4
 password 7 030752180500
 login    
!                 
end 
```
**R1**
```
R1#sh run
Building configuration...
!
service password-encryption
!
hostname R1
!
enable secret 5 $1$stp0$6e/PLnX/qHb/6EmPD/4q9.        
!         
no ip domain lookup
!                         
username class privilege 15 secret 5 $1$3Bbi$yyyu4W53pNz5h91Ll6CV1.        
!         
interface Ethernet0/0
 no ip address
 shutdown 
!         
interface Ethernet0/1
 no ip address
 shutdown 
!         
interface Ethernet0/2
 no ip address
!         
interface Ethernet0/2.3
 encapsulation dot1Q 3
 ip address 192.168.3.1 255.255.255.0
!         
interface Ethernet0/2.4
 encapsulation dot1Q 4
 ip address 192.168.4.1 255.255.255.0
!         
interface Ethernet0/3
 no ip address
 shutdown                 
!                                  
banner motd ^CUnauthorized access to this device is prohibited!^C
!         
line con 0
 password 7 00071A150754
 logging synchronous
 login    
line aux 0
line vty 0 4
 password 7 05080F1C2243
 login    
 transport input none
!         
!         
end 
```
