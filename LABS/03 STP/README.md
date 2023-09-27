# LAN Redundancy (STP) - LAB

**Топология:**

![Топология](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/03%20STP/pictures/topology.PNG)

**Таблица адресации:**
| Device        | Interface     | IP Address   | Subnet Mask   |  
| ------------- | ------------- | ----------   | ----------    |  
| S1            | VLAN 1        | 192.168.1.1  | 255.255.255.0 |
| S2            | VLAN 1        | 192.168.1.2  | 255.255.255.0 |
| S3            | VLAN 1        | 192.168.1.3  | 255.255.255.0 |

## Цель

**1. Создание сети и настройка основных параметров устройства.**<br/>
**2. Выбор корневого коммутатора.**<br/>
**3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов**<br/>
**4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов**<br/>

## Ход работы:

**1. Создание сети и настройка основных параметров устройства.**<br/>

a. Отключите поиск DNS. В данном случае на всех коммутаторах конфигурация одинаковая
```
!         
no ip domain-lookup
!
```
b. Присвойте имена устройствам в соответствии с топологией.
```
!
hostname S1 
!
```
(***для коммутаторов меняется только окончание S2, S3, соответсвенно***)
c. Назначьте class в качестве зашифрованного пароля доступа к привилегированному режиму.
```
enable secret 5 $1$PAo.$YPOXnLRDunx0AMBcZrsvO0
```
d.	Назначьте cisco в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.
```
!         
line con 0
 password 7 045802150C2E
 logging synchronous
 login    
line aux 0
line vty 0 4
 password 7 104D000A0618
 login    
!    
```
e.	Настройте logging synchronous для консольного канала. 
***См. пункт "d." (logging synchronous)***
f.	Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.
```
!         
banner motd ^C
Attention!
Please log out immediately if you are not an authorized administrator!
^C        
!   
```
g.	Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.
### S1
```
!
interface Vlan1
 ip address 192.168.1.1 255.255.255.0
 !
```
### S2
```
!         
interface Vlan1
 ip address 192.168.1.2 255.255.255.0
!   
```
### S3
```
!         
interface Vlan1
 ip address 192.168.1.3 255.255.255.0
!  
```
h.	Скопируйте текущую конфигурацию в файл загрузочной конфигурации.
***Это по сути просто команда `wr`

## Проверка связи
Проверьте способность компьютеров обмениваться эхо-запросами. <br/>
Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2? ***Да*** <br/>
Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3? ***Да*** <br/>
Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3? ***Да*** <br/>
***ping c S1 коммутатора S3***

![ping](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/03%20STP/pictures/pingS1_S3.PNG)


**2. Выбор корневого коммутатора.**<br/>

***Пример выолнения требований **S1***:

a. Отключите все порты на коммутаторах.<br/>
b. Настройте подключенные порты в качестве транковых.<br/>
c. Включите порты F0/2 и F0/4 на всех коммутаторах.<br/>

![ping](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/03%20STP/pictures/pingS3_S1.PNG)

d. Отобразите данные протокола **spanning-tree**:

![shspan](https://github.com/AlexanderRudakov/airudakov_otus_network_engineer_cource/blob/main/LABS/03%20STP/pictures/STP_root_switch(1).PNG)

