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

●  Настройка SSH на HQ-SRV и BR-SRV

Делаем  бэкап конфига:

![image](https://github.com/user-attachments/assets/54db08c0-33e2-464c-b4d5-23d1160c6b85)


Редактируем файл конфигурации SSH сервера

`vim sshd_config`

Изменяем следующие параметры. Не забываем их раскоментировать. Если какой-то параметр не находиться, то просто добавьте его сами

`Port 2024`

`MaxAuthTries 3`

`Banner /etc/openssh/banner`

`AllowUsers sshuser`

Создаем файл с баннером

`
nano /etc/openssh/banner`

Вставляем в него следующие содержимое

![image](https://github.com/user-attachments/assets/54c8ace0-ca56-40f7-bd1a-eb4d162ba2dd)


Перезагружаем SSH

`systemctl restart sshd`

●  Настройка OSPF

HQ-RTR (Ecor)

![image](https://github.com/user-attachments/assets/e728770c-85f0-4514-8a86-054c74ca9e06)

BR-RTR (Vesr)

![image](https://github.com/user-attachments/assets/3f9a87da-f547-4a8d-83b7-46b74c7b0674)

`do commit`

`do confirm`

Проверка

HQ-RTR

![image](https://github.com/user-attachments/assets/312df4ab-6514-4135-90ec-d541ae83efcc)

![image](https://github.com/user-attachments/assets/fe175fdc-ece5-4c92-8a4f-ec52808d39c1)

BR-RTR

![image](https://github.com/user-attachments/assets/46b00ebb-ac80-4081-8c85-c0e500041e46)

![image](https://github.com/user-attachments/assets/02a50b4f-1711-459d-b36f-258239ffd468)


●  Настройка NAT

ISP

![image](https://github.com/user-attachments/assets/82f8c36a-e0b4-46b0-aaf8-a6769420444e)

HQ-RTR

![image](https://github.com/user-attachments/assets/794b8cd1-9e37-4e7c-bbe7-9fdac4c5214a)

BR-RTR

`object-group network LOCAL_NET`

 ` ip address-range 192.168.1.1-192.168.1.30`
 
`exit`

`nat source`

`ruleset SNAT`
  
` to interface gigabitethernet 1/0/1`
   
`rule 1`
    
` match source-address LOCAL_NET`
     
`action source-nat interface`
      
` enable`
     
` exit`
   
`exit`
  
`exit`

 Проверка

 ![image](https://github.com/user-attachments/assets/736874c6-bbee-4b9d-abd1-f923e80f1bf4)


![image](https://github.com/user-attachments/assets/6931ec34-46b2-4bb3-84c2-22764b0c763c)


Настройка DHCP на HQ-RTR (EcoRouter)

![image](https://github.com/user-attachments/assets/06015766-575c-4a47-b37e-e2605f0acd8e)

Проверка

![image](https://github.com/user-attachments/assets/10d2003d-86c5-4817-b3b0-527009a6b7e9)

 HQ-RTR (EcoRouter)

 ![image](https://github.com/user-attachments/assets/edc72b3f-928f-414d-9fdf-4719c1fefde1)

 
