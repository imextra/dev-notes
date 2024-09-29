# Settings for fail2ban in SSH

Update & upgrade Ubuntu Repo's

    sudo apt update && sudo apt upgrade -y

Install, activate and check status

    sudo apt install -y fail2ban python3-systemd
    sudo systemctl enable fail2ban
    systemctl status fail2ban

Create a copy of config

    sudo cp /etc/fail2ban/jail.d/defaults-debian.conf /etc/fail2ban/jail.d/defaults-debian.conf.factory-defaults

Create out own file

    sudo vi /etc/fail2ban/jail.d/sshd.conf

Set the settings

```
[sshd]
enabled = true
bantime = 600
findtime = 650
maxretry = 5
```

Restart fail2ban

    sudo systemctl restart fail2ban
    systemctl status fail2ban

Check the blocked clients

    sudo fail2ban-client status sshd

### Additional steps for DEBIAN 12

First go to the config file:

    nano /etc/fail2ban/jail.local

And add this `backend=systemd` before `enabled=true` to make it look like that for example:

```
[sshd]
backend=systemd
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
```

And then save and run this command:

    apt install python3-systemd

Now restart fail2ban:

    systemctl restart fail2ban
    systemctl status fail2ban


## More info

[Как обеспечить защиту SSH-соединений с помощью fail2ban на Ubuntu](https://www.servers.ru/knowledge/linux-administration/how-to-protect-ssh-using-fail2ban-on-ubuntu-16_04)