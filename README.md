### Модуль № 1:

Настройка сетевой инфраструктуры

![image](https://github.com/user-attachments/assets/9ace5062-8737-4abd-96e3-21f3e3901cfa)

![image](https://github.com/user-attachments/assets/978cb567-95bf-45bd-87fb-a71becdb570c)

## 1. Произведите базовую настройку устройств (ALT)

● Настройте имена устройств согласно топологии. Используйте полное доменное имя (ALT)

` hostnamectl set-hostname hq-srv.au.team.irpo; exec bash`

` hostnamectl set-hostname hq-cli.au.team.irpo; exec bash`

` hostnamectl set-hostname br-srv.au.team.irpo; exec bash`

o Настройте имена устройств согласно топологии. Используйте полное доменное имя (EcoR)

`en`

`conf t`

`hostname hq-rtr.au-team.irpo`

`hostname br-rtr.au-team.irpo`


● На всех устройствах необходимо сконфигурировать IPv4

`ip -с a`

`echo ***.***.*.*/** > /etc/net/ifaces/ens**/ipv4address`

`echo default via ***.***.*.* > /etc/net/ifaces/ens**/ipv4route`

`nano(vim) /etc/net/ifaces/ens**/options`

`nano(vim) /etc/net/ifaces/ens**/options`

![image](https://github.com/user-attachments/assets/0e1b4b8a-ca12-461d-9c5f-d52dae49000d)

Сохраняем изменения: `Esc: wq`

DNS-сервер: `echo nameserver 8.8.8.8 > /etc/resolv.conf`

Создание нового интерфейса (предположительно свериться в ip a): `mkdir /etc/net/ifaces/ens__`

Перезагрузка адаптера: `service network restart`& `systemctl restart network.service`

## 2. Настройка ISP

● Настройте адресацию на интерфейсах:

o Интерфейс, подключенный к магистральному провайдеру,получает адрес по DHCP

`apt-get -y install dhcp-server`

/etc/sysconfig/dhcpd, указываю интерфейс внутренней сети:

DHCPDARGS=ens**

Копирую образец:

`cp /etc/dhcp/dhcpd.conf.sample /etc/dhcp/dhcpd.conf`

`/etc/dhcp/dhcpd.conf` параметры раздачи:

`ddns-update-style-none;`

subnet 192.168.0.0 netmask 255.255.255.128 {

        option routers                  192.168.0.1;
        
        option subnet-mask              255.255.255.128;
        
        option domain-name-servers      8.8.8.8, 8.8.4.4;

        range dynamic-bootp 192.168.0.20 192.168.0.50;
        
        default-lease-time 21600;
        
        max-lease-time 43200;
}

host HQ-SRV

    {
    hardware ethernet 00:50:56:b6:89:75;
    
    fixed-address 192.168.0.5;
    }

o Настройте маршруты по умолчанию там, где это необходимо

o Интерфейс, к которому подключен HQ-RTR, подключен к сети 172.16.4.0/28

o Интерфейс, к которому подключен BR-RTR, подключен к сети 172.16.5.0/28

o На ISP настройте динамическую сетевую трансляцию в сторону HQ-RTR и BR-RTR для доступа к сети Интернет

## 3. Создание локальных учетных записей

● Создайте пользователя sshuser на серверах HQ-SRV и BR-SRV

`adduser sshuser`

o Пароль пользователя sshuser с паролем P@ssw0rd

`passwd sshuser`

`P@ssw0rd`

`P@ssw0rd`

o Идентификатор пользователя 1010

o Пользователь sshuser должен иметь возможность запускать sudo без дополнительной аутентификации.

● Создайте пользователя net_admin на маршрутизаторах HQ-RTR и BR-RTR (EcoRouter)

`adduser net-admin`

o Пароль пользователя net_admin с паролем P@$$word

`passwd net-admin`

`P@ssw0rd`

`P@ssw0rd`

o При настройке на ОС EcoRouter или аналоге пользователь net_admin должен обладать максимальными привилегиями

o При настройке ОС на базе Linux, запускать sudo без дополнительной аутентификации

## 4. Настройте на интерфейсе HQ-RTR в сторону офиса HQ виртуальный коммутатор:

● Сервер HQ-SRV должен находиться в ID VLAN 100

● Клиент HQ-CLI в ID VLAN 200

● Создайте подсеть управления с ID VLAN 999

● Основные сведения о настройке коммутатора и выбора реализации разделения на VLAN занесите в отчёт

## 5. Настройка безопасного удаленного доступа на серверах HQ-SRV и BRSRV: 38

● Для подключения используйте порт 2024

● Разрешите подключения только пользователю sshuser

● Ограничьте количество попыток входа до двух

● Настройте баннер «Authorized access only»
