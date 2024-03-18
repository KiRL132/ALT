# Топология
![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/7ba30513-da83-4b4c-938d-9d3d24e2c9ee)

| Имя устройства | Интерфейс | IPv4/IPv6 | Маска/префикс | Шлюз |
| ----------- | ----------- | ----------- | ----------- | ----------- |
| CLI         | ens192      | 192.168.0.170    | **30/** 255.255.255.252    |  192.168.0.169  |
|             | ens224      | 192.168.0.174    | **30/** 255.255.255.252    |  192.168.0.173  |
| ISP         | ens192      | 10.12.18.13      | **24/** 255.255.255.0      |  10.12.13.254   |
|             | ens224      | 192.168.0.162    | **30/** 255.255.255.252    |                 |
|             | ens256      | 192.168.0.166    | **30/** 255.255.255.252    |                 |
|             | ens161      | 192.168.0.169    | **30/** 255.255.255.252    |                 |
| HQ-R        | ens192      | 192.168.0.1      | **25/** 255.255.255.128    |                 |
|             | ens224      | 192.168.0.161    | **30/** 255.255.255.252    |  192.168.0.162  |
|             | ens256      | 192.168.0.173    | **30/** 255.255.255.252    |                 |
|             | GRE1        | 10.0.0.2         | **30/** 255.255.255.252    |                 |
| BR-R        | ens192      | 192.168.0.129    | **27/** 255.255.255.224    |                 |
|             | ens224      | 192.168.0.165    | **30/** 255.255.255.252    |  192.168.0.166  |
|             | GRE1        | 10.0.0.1         | **30/** 255.255.255.252    |                 |
| HQ-SRV      | ens192      | 192.168.0.126    | **25/** 255.255.255.128    |  192.168.0.1    |
| BR-SRV      | ens192      | 192.168.0.158    | **27/** 255.255.255.224    |  192.168.0.129  |


| Имя устройства | Пользователь | Пароль |
| ----------- | ----------- | ----------- |
| CLI         | root      | toortoor    |
| ISP         | root      | toortoor    |
| HQ-R        | root      | toortoor    |
| BR-R        | root      | toor        |
| HQ-SRV      | root      | toor        |
| BR-SRV      | root      | toor        |

# Базовая настройка устройств, IP адресация и маршрутизация.

> [!TIP]
> **После создания машин добавляем сетевые интерфейсы**

> [!NOTE]
![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/0bfe9da2-dec4-450d-b8ec-8f3ccb07092c)

> [!TIP]
> - [x] **Задаем имя устройствам**
>
> hostnamectl set-hostname ISP;exec bash

>[!TIP] 
> - [x] **Устанавливаем все необходимые пакеты сразу**
>
> apt-get update
> 
> apt-get install nano
>
> apt-get install -y NetworkManager-{daemon,tui} [на HQ-BR]
>
> apt-get install -y dhcp-server [на HQ-R]
>
> apt-get install -y firewalld [на ISP, BR-R, HQ-R]
>
> apt-get install -y iperf3 [на ISP, HQ-R]

## 1. Маршрутизация
>[!NOTE] 
> - [x] **Включаем IPv4**
>
> vim /etc/net/ifaces/default/options --> **CONFIG_IPV4=yes**
>
> - [x] **Узнаем номера интерфейсов и создаем файлы конфигурации устройств**
> ```
> ip a
> ```
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/42ce4fd9-4b44-44fd-8878-9711cdc2fbf7)
>
> ```
> mkdir /etc/net/ifaces/ens192
>
> mkdir /etc/net/ifaces/ens224
>
> mkdir /etc/net/ifaces/ens256
>
> mkdir /etc/net/ifaces/ens160
>
> ```
> 
> - [x] **Настраиваем конфигурацию интерфейсов**
> ```
> nano /etc/net/ifaces/ens192/options
> ```
> ![image](https://github.com/KiRL132/ALT-DEMO/assets/148869565/2f69cbde-290e-4e03-acd1-d69e118920ce)
>
> - [x] *Копируем настройки на все необходимые интерфейсы*
> ```
> cp /etc/net/ifaces/ens192/options /etc/net/ifaces/ens**НОМЕР ИНТЕРФЕЙСА**/
> ```
> - [x] **Вписываем IP адреса и при необходимости шлюз по умолчанию**
> ```
> nano /etc/net/ifaces/ens192/ipv4address
> ```
> > Вписываем только адрес и через слеш его маску (Пример: 192.168.0.1/27)
> ```
> nano /etc/net/ifaces/ens192/ipv4route
> ```
> > Прописываем [default via адрес] (Пример: default via 192.168.0.129)
>
# 7. Настройте подключение по SSH для удалённого конфигурирования устройства
# Выполнение: 
На HQ-SRV меняем порт с 22 на 2222:
```
sed -i "s/#Port 22/Port 2222/g" /etc/openssh/sshd_config
```
Перезагружаем службу sshd:
```
systemctl restart sshd
```
Проверка:
```
ss -tlpn | grep sshd
```
На HQ-R устанавливаем nftables:
```
apt-get install -y nftables
```
Включаем и добавляем в автозагрузку службу nftables:
```
systemctl enable --now nftables
```
Создаём правило:
```
nft add table inet nat
```
Добавляем цепочку в таблицу:
```
nft add chain inet nat prerouting '{ type nat hook prerouting priority 0; }'
```
Добавляем ещё одно правило:
```
nft add rule inet nat prerouting ip daddr 192.168.0.161 tcp dport 22 dnat to 192.168.0.10:2222
```
Сохраняем правила:
```
nft list ruleset | tail -n 7 | tee -a /etc/nftables/nftables.nft
```
Перезапускаем службу:
```
systemctl restart nftables
```
Проверка на BR-R:
```
ssh admin@192.168.0.161
password
hostname
```

