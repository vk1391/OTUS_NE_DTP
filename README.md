# Домашнее задание №1: VLAN и маршрутизация между VLAN
- Цель:
  - Настройка DTP.
  - Добавление сетей VLAN и назначение портов.
- Схема связи для домашнего задания:
![alt-dtp](https://github.com/vk1391/OTUS_NE_DTP/blob/main/схема1.jpg "Схема к домашнему заданию №1")
- Таблица адресов:

**Имя Устройства** | **IP address** | **Префикс** | **VLAN** | **Коммутатор** | **Порт на коммутаторе**
 --- | --- | --- | --- | --- | ---
| host1 | 10.0.0.1 | 30 | 10 | R2 | e0/1 |
| host2 | 10.100.0.1 | 30 | 100 | R2 | e0/2 |
| host3 | 10.0.0.2 | 30 | 10 | R3 | e0/1 |
| host4 | 10.100.0.2 | 30 | 100 | R3 | e0/2 |

- Конфигурация коммутаторов:  
  R1:  
  ```
      Switch(config)#int vlan 100 
      Switch(config-if)#no shutdown 
      Switch(config-if)#exit
      Switch(config)# 
      Switch(config)#vlan 100      
      Switch(config-vlan)#state active
      Switch(config-vlan)#exit
      Switch(config)#vlan 10
      Switch(config-vlan)#state active
      Switch(config-vlan)#exit
      Switch(config)#int vlan 10 
      Switch(config-if)#no shutdown 
      Switch(config)#int e0/0
      Switch(config-if)#sw trunk encapsulation dot1q 
      Switch(config-if)#sw m tr                      
      Switch(config-if)#no shutdown 
      Switch(config-if)#exit
      Switch(config)#int e 0/1
      Switch(config-if)#sw tr encapsulation dot1q 
      Switch(config-if)#sw m tr
      Switch(config-if)#no sh
      Switch(config-if)#do wr
   ```   
  R2:
 
   ```
      Switch(config)#int e0/0
      Switch(config-if)#sw mode dynamic auto
      Switch(config-if)#exit
      Switch(config)#int e0/1
      Switch(config-if)#exit
      Switch(config)#vlan 10
      Switch(config-vlan)#state active 
      Switch(config-vlan)#exit
      Switch(config)#int e0/1
      Switch(config-if)#sw m ac
      Switch(config-if)#sw ac vlan 10
      Switch(config-if)#no sh
      Switch(config-if)#exit
      Switch(config)#vlan 100
      Switch(config-vlan)#state active
      Switch(config-vlan)#exit
      Switch(config)#int e0/2
      Switch(config-if)#sw m ac
      Switch(config-if)#sw ac vlan 100
      Switch(config-if)#no shut
      Switch(config-if)#int vlan 10
      Switch(config-if)#no shutdown 
      Switch(config-if)#exit
      Switch(config)#int vlan 100
      Switch(config-if)#no shutdown
   ```
  R3:

  ```
      Switch(config)#int e0/0
      Switch(config-if)#sw mode dynamic auto
      Switch(config-if)#exit
      Switch(config)#int e0/1
      Switch(config-if)#exit
      Switch(config)#vlan 10
      Switch(config-vlan)#state active 
      Switch(config-vlan)#exit
      Switch(config)#int e0/1
      Switch(config-if)#sw m ac
      Switch(config-if)#sw ac vlan 10
      Switch(config-if)#no sh
      Switch(config-if)#exit
      Switch(config)#vlan 100
      Switch(config-vlan)#state active
      Switch(config-vlan)#exit
      Switch(config)#int e0/2
      Switch(config-if)#sw m ac
      Switch(config-if)#sw ac vlan 100
      Switch(config-if)#no shut
      Switch(config-if)#int vlan 10
      Switch(config-if)#no shutdown 
      Switch(config-if)#exit
      Switch(config)#int vlan 100
      Switch(config-if)#no shutdown
  ```
- Вывод DTP по интерфейсу e0/0 на коммутаторах R2 и R3  
  R2:
  ```
  Switch#show dtp interface e0/0
  DTP information for Ethernet0/0:
  TOS/TAS/TNS:                              TRUNK/AUTO/TRUNK
  TOT/TAT/TNT:                              802.1Q/NEGOTIATE/802.1Q
  Neighbor address 1:                       AABBCC001010
  Neighbor address 2:                       000000000000
  Hello timer expiration (sec/state):       2/RUNNING
  Access timer expiration (sec/state):      272/RUNNING
  Negotiation timer expiration (sec/state): never/STOPPED
  Multidrop timer expiration (sec/state):   never/STOPPED
  FSM state:                                S6:TRUNK
  times multi & trunk                       0
  Enabled:                                  yes
  In STP:                                   no
  ```
  R3:
  ```
  DTP information for Ethernet0/0:
  TOS/TAS/TNS:                              TRUNK/AUTO/TRUNK
  TOT/TAT/TNT:                              802.1Q/NEGOTIATE/802.1Q
  Neighbor address 1:                       AABBCC001000
  Neighbor address 2:                       000000000000
  Hello timer expiration (sec/state):       21/RUNNING
  Access timer expiration (sec/state):      291/RUNNING
  Negotiation timer expiration (sec/state): never/STOPPED
  Multidrop timer expiration (sec/state):   never/STOPPED
  FSM state:                                S6:TRUNK
  # times multi & trunk                     0
  Enabled:                                  yes
  In STP:                                   no
  ```
- Проверка конфигурации:  
  host1:
  ```
  host1> ping 10.0.0.2

  84 bytes from 10.0.0.2 icmp_seq=1 ttl=64 time=3.510 ms
  84 bytes from 10.0.0.2 icmp_seq=2 ttl=64 time=3.043 ms
  84 bytes from 10.0.0.2 icmp_seq=3 ttl=64 time=3.595 ms
  84 bytes from 10.0.0.2 icmp_seq=4 ttl=64 time=3.682 ms
  ^C
  host1> ping 10.100.0.2
  
  10.100.0.2 icmp_seq=1 timeout
  10.100.0.2 icmp_seq=2 timeout
  ^C
  host1> ping 10.100.0.1
  
  10.100.0.1 icmp_seq=1 timeout
  10.100.0.1 icmp_seq=2 timeout
  ```
  host2:
  ```
  host2> ping 10.100.0.2

  84 bytes from 10.100.0.2 icmp_seq=1 ttl=64 time=2.411 ms
  84 bytes from 10.100.0.2 icmp_seq=2 ttl=64 time=2.941 ms
  84 bytes from 10.100.0.2 icmp_seq=3 ttl=64 time=3.116 ms
  ^C
  host2> ping 10.0.0.2  
  
  10.0.0.2 icmp_seq=1 timeout
  ^C^[[A^C
  host2> ping 10.0.0.1
  
  10.0.0.1 icmp_seq=1 timeout
  ^C^C
```
    
