### Модуль № 1:

Настройка сетевой инфраструктуры

![image](https://github.com/user-attachments/assets/9ace5062-8737-4abd-96e3-21f3e3901cfa)

![image](https://github.com/user-attachments/assets/978cb567-95bf-45bd-87fb-a71becdb570c)

## 1. Произведите базовую настройку устройств

● Настройте имена устройств согласно топологии. Используйте полное доменное имя

` hostnamectl set-hostname hq-r.hq.work; exec bash`

● На всех устройствах необходимо сконфигурировать IPv4

`ip -с a`

`echo ***.***.*.*/** > /etc/net/ifaces/ens**/ipv4address`

`echo default via ***.***.*.* > /etc/net/ifaces/ens**/ipv4route`

`nano(vim) /etc/net/ifaces/ens**/options`

`nano(vim) /etc/net/ifaces/ens18/options`

![image](https://github.com/user-attachments/assets/0e1b4b8a-ca12-461d-9c5f-d52dae49000d)

Сохраняем изменения: `Esc: wq`

DNS-сервер: `echo nameserver 8.8.8.8 > /etc/resolv.conf`

Создание нового интерфейса (предположительно свериться в ip a): `mkdir /etc/net/ifaces/ens__`

Перезагрузка адаптера: `service network restart`& `systemctl restart network.service`




● IP-адрес должен быть из приватного диапазона, в случае, если сеть
локальная, согласно RFC1918
● Локальная сеть в сторону HQ-SRV(VLAN100) должна вмещать не
более 64 адресов
● Локальная сеть в сторону HQ-CLI(VLAN200) должна вмещать не
более 16 адресов
● Локальная сеть в сторону BR-SRV должна вмещать не более 32
адресов
● Локальная сеть для управления(VLAN999) должна вмещать не
более 8 адресов
● Сведения об адресах занесите в отчёт, в качестве примера
