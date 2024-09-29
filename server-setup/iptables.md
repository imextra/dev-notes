# IPTABLES SETTINGS

Update & upgrade Ubuntu Repo's

    sudo apt update && sudo apt upgrade -y

Install iptables

    sudo apt install -y iptables-persistent

## Как очистить список правил iptables
Сброс всех правил обычно требуется совместно с изменением политики по умолчанию на ACCEPT, эта процедура связана с вводом набора правил, поэтому удобнее всего создать небольшой скрипт для автоматизации процесса:

    nano /etc/iptables.reset

Наполняем его командами:
```bash
#!/bin/bash
colGreen="\033[32m"
resetCol="\033[0m"
echo -n "Flushing firewall rules and changing default policy to ACCEPT..."
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
echo -e "$colGreen Done! $resetCol"
```

Сохраняем и выходим. Команды можно последовательно подавать вручную, но это замедляет работу и увеличивает риск ошибки за счет влияния человеческого фактора.

Делаем файл исполняемым:

    chmod +x /etc/iptables.reset

Даем команду:

    /etc/iptables.reset

### SSH Access

Разрешим соединения по SSH на 55673 порту, чтобы не потерять доступ к машине:

    iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
    iptables -A INPUT -p tcp --dport 55673 -j ACCEPT

### Как открыть порты в iptables

Сразу много портов одной командой:

    iptables -A INPUT -p tcp -m multiport --dports 80,443 -j ACCEPT

Или каждый порт своей командой:

    iptables -A INPUT -p tcp --dport 80 -j ACCEPT
    iptables -A INPUT -p tcp --dport 443 -j ACCEPT


### Как разрешить трафик на локальном узле

Трафик на локальном сетевом интерфейсе lo должен быть разрешен для корректной работы сервисов, использующих для обмена данными интерфейс локальной петли, например, базы данных, прокси-сервера squid. Поэтому рекомендуется разрешить трафик на вход и выход:

    iptables -A INPUT -i lo -j ACCEPT 
    iptables -A OUTPUT -o lo -j ACCEPT

### ICMP PING

Access ping

    iptables -A INPUT -p icmp --icmp-type 8 -j ACCEPT

Blocking ping

    sudo iptables -A INPUT -p icmp --icmp-type 8 -j DROP

### Запретим все прочие соединения к серверу:

    iptables -A INPUT -j DROP
    iptables -A FORWARD -j DROP

### Save iptables rules

    sudo service netfilter-persistent save
    sudo service netfilter-persistent reload

View current rules

    sudo iptables --line-numbers -L -v -n


Another way 

    sudo iptables -S



## More info

[Настройка iptables в Linux](https://selectel.ru/blog/setup-iptables-linux/)

[[Youtube] Защита SSH-сервера — порты, ключи, port knocking, iptables](https://www.youtube.com/watch?v=5TCvRlD1sSw&t=1376s)

[[Youtube] Продвинутая защита ssh с помощью iptables. Ограничение кол-ва подключений в минуту, час, день...](https://www.youtube.com/watch?v=zqMYJaz6elM)
