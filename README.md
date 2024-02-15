# ALT
![image](https://github.com/Ganibal-24/ALT/assets/148868527/080f73ac-d6d9-49a9-8bc6-5af3a35b9527)
# Модуль 1: Выполнение работ по проектированию сетевой инфраструктуры
# 1. Выполните базовую настройку всех устройств
a. Присвоить имена в соответствии с топологией

b. Рассчитайте IP-адресацию Ipv4. Необходимо заполинть таблицу №1, чтобы эксперты могли проверить ваше рабочее место

c. Пул адресов для сети офиса BRANCH - не более 16

d. Пул адресов для сети офиса HQ - не более 64

# Выполнение:
# a. Присвоить имена в соответствии с топологией
```
hostnamectl set-hostname <NAME>; exec bash
```
| Имя устройства | Интерфейс   | IPv4             | Маска/Префикс       | Шлюз         |
| -----------    | ----------- |------------      |   ---------------   |------        |
| CLI            | ens192      | 192.168.0.170    | 255.255.255.252/30  |              |
|                | ens224      | 192.168.0.174    | 255.255.255.252/30  |              |
| ISP            | ens192      | 10.12.10.6       | 255.255.255.0/24    | 10.12.10.254 |
|                | ens224      | 192.168.0.162    | 255.255.255.252/30  |              |
|                | ens256      | 192.168.0.166    | 255.255.255.252/30  |              |
|                | ens161      | 192.168.0.169    | 255.255.255.252/30  |              |
| HQ-R           | ens192      | 192.168.0.126    | 255.255.255.128/25  |              |
|                | ens224      | 192.168.0.161    | 255.255.255.252/30  |              |
|                | ens256      | 192.168.0.173    | 255.255.255.252/30  |              |
|                | gre1        | 10.0.0.1         | 255.255.255.252/30  |              |
| HQ-SRV         | ens192      | 192.168.0.1      | 255.255.255.128/25  |              |
| BR-R           | ens192      | 192.168.0.158    | 255.255.255.224/27  |              |
|                | ens224      | 192.168.0.165    | 255.255.255.252/30  |              |
|                | gre1        | 10.0.0.2         | 255.255.255.252/30  |              |
| BR-SRV         | ens192      | 192.168.0.129    | 255.255.255.224/27  |              |     
