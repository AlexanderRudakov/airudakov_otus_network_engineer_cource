# Inter VLAN Routing - LAB

Задание:

1. В среде EVE-NG собрать лабораторную работу согласно схеме.
2. Настроить схему "Роутер на палочке"
   - Задокументировать адресное пространство.
3. Добиться чтобы компьютер **PC-A** мог "пинговать" **PC-B**




Addressing table:
| Device        | Interface     | IP Address   | Subnet Mask   | Default Gateway |
| ------------- | ------------- | ----------   | ----------    | --------------  |
| R1            | Ethernet0/2.3 | 192.168.3.1  | 255.255.255.0 | N/A             |
| R1            | Ethernet0/2.4 | 192.168.4.1  | 255.255.255.0 | N/A             |
| S1            | VLAN 3        | 192.168.3.11 | 255.255.255.0 | 192.168.3.1     |
| S2            | VLAN 4        | 192.168.4.12 | 255.255.255.0 | 192.168.4.1     |
| PC-A          | NIC           | 192.168.3.3  | 255.255.255.0 | 192.168.3.1     |
| PC-B          | NIC           | 192.168.4.3  | 255.255.255.0 | 192.168.4.1     |


VLAN table:
| VLAN          | NAME          | Interface Assigned                        | 
| ------------- | ------------- | ------------------------------------------|  
| 3             | Management    | Et0/1                                     | 
| 4             | Operations    | Et0/1                                     | 
| 111 (S1)      | DUMP          | Et0/3, Et1/0, Et1/1, Et1/2 Et1/3          | 
| 111 (S1)      | DUMP          | Et0/2, Et0/3, Et1/0, Et1/1 Et1/2, Et1/3   |
| 8             | Native        |                                           | 

# Ход работы:
1. В среде EVE-NG была реализована схема:

![interVlanRoutingschema](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/02%20inter%20VLAN%20routing/Pictures/Schema%20Inter%20VLAN%20routing.png)

2. Было настроенно указанное на схеме оборудование. Конфигурации оборудования можно найти в соответсвующей [папке](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/tree/main/LABS/02%20inter%20VLAN%20routing/configs)

2-1. Для начала я настроил **PC-A** и **PC-B**, так как в сети не используется **DHCP**, то использовались статические **IP** адреса. Сама настройка выглядит так:

```
192.168.3.3 255.255.255.0 gateway 192.168.3.1
```

2-2. Для **S1** и **S2** нужно было настроить trunk порты, access порты, создать VLAN-а, изменить native VLAN, и отключить порты которые не используются (в целях безопасности).

   Trunk порты я настраивал следующим образом:

    ```
    interface Ethernet0/0
    switchport trunk allowed vlan 3,4
    switchport trunk encapsulation dot1q
    switchport trunk native vlan 8
    switchport mode trunk
    switchport nonegotiate
    ```

   На trunk интерфейсах я отлкючил автоматическое согласование trunk, назначил native VLAN, который не ведет никуда и добавил разрешенные VLAN. 
   
   Конфигурация Access порта выглядит так:
   ```
   interface Ethernet0/1<br/>
   switchport access vlan 3<br/>
   switchport mode access<br/>
   switchport nonegotiate<br/>
   spanning-tree portfast edge<br/>
   ```
   Тут я так же добавил порт в 3-й VLAN и отключил автоматическое согласование режима trunk. 
   
   Сам порт я настроил командой ***switchport host*** подробнее про эту команду можно почитать [тут](https://www.rogerperkin.co.uk/cisco/switching/switchport-host-cisco-ios-switch-security/).

   Остальные порты как на **S1** так и на **S2** я выключил, добавли в несуществующий VLAN, и усредненная конфигурация выглядит так:
   ```
   interface Ethernet1/2
   switchport access vlan 111
   switchport mode access
   switchport nonegotiate
   shutdown 
   spanning-tree portfast edge
   ```
   Так же были добавлены SVI интерфейсы (для обеспечения маршрутизации) и шлюз по умолчанию:
   ```
   interface Vlan3
   ip address 192.168.3.11 255.255.255.0       
   ip default-gateway 192.168.3.1
   ```

2-3. На роутере **R1** была настроена схема "роутер на палочке" , которая сводится к настроке SVI интерфейсов с номером соответсвующего VLAN для наших **PC-A** и **PC-B**.

   ```
   interface Ethernet0/2.3
   encapsulation dot1Q 3
   ip address 192.168.3.1 255.255.255.0
   !         
   interface Ethernet0/2.4
   encapsulation dot1Q 4
   ip address 192.168.4.1 255.255.255.0
   ```

2-4. Так же согласно условию в методичеке (можно ознакомиться [тут](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/tree/main/LABS/02%20inter%20VLAN%20routing/misc)) на **S1, S2, R1** был настроен пароль на вход в консоль и vty. И так же на доступ в привелегированный режим.

3. В результате настроек компьютер **PC-A** ***"увидел"*** **PC-B**

**PC-A to PC-B**

![PCAtoPCB](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/02%20inter%20VLAN%20routing/Pictures/PC-A%20to%20PC-B.PNG)

**PC-B to PC-A**

![PCBtoPCA](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/02%20inter%20VLAN%20routing/Pictures/PC-B%20to%20PC-A.png)


   ***Все вайлы конфигурации находятся в*** [папке](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/tree/main/LABS/02%20inter%20VLAN%20routing/configs)