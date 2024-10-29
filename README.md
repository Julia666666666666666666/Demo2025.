### Модуль № 1:

##### Сначала нужно развернуть все виртуальные машины, которые указаны в таблице.

![image](https://github.com/user-attachments/assets/aa64e23e-0d48-4c28-89f8-d60fac9868cc)

##### EcorR установка

Переходим в Esxi и делаем импорт

![image](https://github.com/user-attachments/assets/d10b1f0f-84a5-4a6c-89f0-88cacb2404e5)

Далее задаем имя машине и вставляем файлы сюда

![image](https://github.com/user-attachments/assets/195086f2-b62d-48ad-ac43-a048b1126d84)

После

![image](https://github.com/user-attachments/assets/4804e098-d072-4780-9b20-a46615fca413)

##### Установка vESR на ESXI 8, обычная установка с небольшими изменениями.

![image](https://github.com/user-attachments/assets/6534547e-31f3-414c-bebd-d4f03aa7e63f)

Далее, после установки нужно включить машину

После загрузки выбираем 

`vESR installation-ok-пробел-ok-yes`

Далее `reboot`

После переустановки нужно ввести учетные данные и нужно сразу сменить пароль администратора

Логин `admin`

Пароль `password`

Зафиксируем изменения введем `commit`

Подтвердим изменения `confirm`

##### Создание виртуальных подключений

Раздел `Networking` затем во вкладка `Virtual switchs`

Добавляем  виртуальный коммутатор `add standart virtual switch`

![image](https://github.com/user-attachments/assets/dbb7a9cd-5c02-4d0b-8914-4e0af9476b56)

Задаем имя виртуальному коммутатору 

Раскрываем пункт `Security` изменяем все пункты на `Accept`

Нажимаем кнопку `ADD`

![image](https://github.com/user-attachments/assets/a4683487-d97f-40c2-b0d2-08db60004026)

В результате должно получиться 

![image](https://github.com/user-attachments/assets/3202c0ca-cd88-4e5c-a30f-34423b75d7d7)

![image](https://github.com/user-attachments/assets/baa213f6-f82d-4fc5-9152-73320cee97cb)

##### Настройка внеполосного управления

Включаем службу в firewall на ESXi

Переходим в раздел `Networking`

Открываем вкладку `Firewall rules`

Ищем правило `remoteSerialPort`. Активируем это правило.

![image](https://github.com/user-attachments/assets/14aadda1-2766-4b9a-96af-20d42d0fbd3a)

Настраиваем виртуальные машины (работа выполняется на всех машинах) 

Выключаем виртуальные машины

Добавляем новое устройство нажав на `Add other device`

Выбираем пункт `Serial port`

Изменяем тип порта на `Use network`

В строку `Port URL` добавляем значение в формате `telnet://0.0.0.0:5055` 

Сохраняем изменения нажав кнопку `SAVE`

![image](https://github.com/user-attachments/assets/c27968c6-31bb-464a-9a6c-852aa6a31550)

В Linux необходимо включить службу, добавить ее в автозагрузку и проверить статус:

`systemctl start serial-getty@ttyS0.service`

`systemctl enable serial-getty@ttyS0.service`

`systemctl status serial-getty@ttyS0.service`

##### Настройка консоли vESR

Чтобы логи выводились на удалённую консоль, выполним следующие настройки.

`config`

`syslog console`

`virtual-serial`

`do commit`

`do reload system` `y`


![image](https://github.com/user-attachments/assets/9ace5062-8737-4abd-96e3-21f3e3901cfa)


## 1. Произведите базовую настройку устройств (ALT)

● Настройте имена устройств согласно топологии. Используйте полное доменное имя (ALT)

` hostnamectl set-hostname hq-srv.au.team.irpo; exec bash`

` hostnamectl set-hostname hq-cli.au.team.irpo; exec bash`

` hostnamectl set-hostname br-srv.au.team.irpo; exec bash`

o Настройте имена устройств согласно топологии. Используйте полное доменное имя (EcoR)

`en`

`conf t`

`hostname hq-rtr.au-team.irpo`

o Настройте имена устройств согласно топологии. Используйте полное доменное имя (vESR)

`configure`

`hostname br-rtr.au-team.irpo`

`do commit`

`do confirm`

● На всех устройствах необходимо сконфигурировать IPv4 

![image](https://github.com/user-attachments/assets/bed48150-8df0-46e8-ae15-70c671766b03)

Настройка айпи (ISP(Vesr))

`configure`

`int gi1/0/3`

`ip address 172.16.4.1/28`

`ip firewall disable`

`no shutdown`

`exit`

`int gi1/0/2`

`ip address 172.16.5.1/28`

`ip firewall disable`

`no shutdown`

`commit`

`confirm`

![image](https://github.com/user-attachments/assets/2ff58a60-041c-43b4-9c04-c1faabff850c)

Ecor(HQ-RTR)

Создаем сущность интерфейса и назначаем IP (посмотреть интерфейсы)

![image](https://github.com/user-attachments/assets/117238f5-9598-4a78-9270-0b0fac08103a)

Привязываем созданный интерфейс к физическому протоколу

Заходим в `port ge0`

Создаем `service-instance service-instance SI-ISP`

Указываем, что кадры на этом интерфейсе будут без тега `encapsulation untagged`

Привязываем сущьность интрефейса к порту `connect ip interface TO-ISP`

![image](https://github.com/user-attachments/assets/f2168178-3cf2-4de3-bb32-1b598d8fdddb)

Создаем интерфейсы, которые будут обрабатывать трафик vlan 100, 200, 999

![image](https://github.com/user-attachments/assets/2730782c-0912-4dd0-860a-a660d53fa7f2)
 
Заходим на порт и создаем для каждой `vlan свой service-instance`.

Заходим в port ge1.

Создаем service-instance service-instance ge1/vlan100

Указываем инкапсуляцию для 100 vlan

Чтобы кадры из этого интерфейса выходили с тегом задаем настройку `rewrite pop 1`

Привязываем сущность интрефейса к порту `connect ip interface HQ-SRV`

Проделываем эти действия для vlan 200 и 999

![image](https://github.com/user-attachments/assets/13fe3d95-1376-44c2-8471-2d392d7cc839)

`connect ip interface HQ-SRV`

`service-instance ge1/vlan200`

`encapsulation dot1q 200`

`rewrite pop 1`

`connect ip interface HQ-CLI`

`service-instance ge1/vlan999`

`encapsulation dot1q 999`

`rewrite pop 1`

`connect ip interface HQ-MGMT`

Проверка

![image](https://github.com/user-attachments/assets/a567e440-0dd2-4673-9735-775fc0ccfe96)

Создаем GRE туннель

![image](https://github.com/user-attachments/assets/7c7b5a34-564f-4e9b-afcb-7f5bc104314b)

Задаем маршрут по умолчанию в сторону ISP

![image](https://github.com/user-attachments/assets/1771c7c4-91e6-455c-a33d-a28fc154ed26)

ALT (HQ-SRV)

`echo 192.168.0.2/26 > /etc/net/ifaces/ens192/ipv4address`

`echo default via 192.168.0.1 > /etc/net/ifaces/ens192/ipv4route`

`systemctl restart network`

![image](https://github.com/user-attachments/assets/1bcaf221-d35b-4db5-8a2c-b3f4d4d1ebe8)

ALT (BR-SRV)

`echo 192.168.1.2/26 > /etc/net/ifaces/ens192/ipv4address`

`echo default via 192.168.1.1 > /etc/net/ifaces/ens192/ipv4route`

![image](https://github.com/user-attachments/assets/6a117570-bc77-4cc0-8b6e-94aabfafbc2b)


#### HQ-CLI(ALT)

BR-RTR(vesr)

![image](https://github.com/user-attachments/assets/cc8655b2-8ead-454f-9955-b42efef662ba)

`ip route 0.0.0.0/0 172.16.5.1`

`do commit`

`do confirm`

●  Создание локальных учетных записей

ALT: HQ-SRV / BR-SRV

`useradd -m -u 1010 sshuser`

`passwd sshuser`

`nano /etc/sudoers.d/sshuser`

`sshuser ALL=(ALL) NOPASSWD:ALL`

Проверка:

![image](https://github.com/user-attachments/assets/3fd522e4-3efa-457d-925e-cf1b8a20e62c)

![image](https://github.com/user-attachments/assets/98aae368-ebe7-4795-8929-a144944c1585)

HQ-RTR (EcoRouter)

`conf t`

`username net_admin`

`password P@ssw0rd`

`role admin`

`activate`

![image](https://github.com/user-attachments/assets/9ce70cd6-684b-4dc1-a3d9-cfabb0ec94e5)

BR-RTR (Eltex - vESR)

`configure`

`username net_admin`

`password P@ssw0rd`

`privilege 15`

`end`

`commit`

`confirm`












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
