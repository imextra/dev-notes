# SSH

    sudo apt update && sudo apt upgrade -y

    sudo apt install openssh-client openssh-server -y


## SSH-KEY: Create ssh-key and make ssh-key connection to remote server

### 1. Create the key with `ed25519` encription (it's better than `rsa`)

    ssh-keygen -t ed25519

Set the key location and key password

    ~/.ssh/ssh-keys/my-new-key

Show the key

    cat {path-to-key}
    cat ~/.ssh/ssh-keys/my-new-key

### 2. Add public ssh-key to remote server for new user `username`

#### 2.1 Fast way via `ssh-copy-id`

    ssh-copy-id -i {path-to-key} username@server1

Example: 

    ssh-copy-id -i ~/.ssh/ssh-keys/my-new-key username@server2

#### 2.2. Long way. Connect to remote server and copy public key

On remote server edit the file `.ssh/authorized_keys`

    vi /home/username/.ssh/authorized_keys

Add to file `.ssh/authorized_keys` ssh-key and save it. In previous steps we show the key by `cat` command


### 3. Now you can connect to the server via ssh-key (without password)

    ssh username@host -i {path-to-key}

### 4. Update ssh config file for easy connection to remote server just use command `ssh server-name`

    vi ~/.ssh/config

Examples: 
```
Host my-server-name
  HostName 192.168.1.1
  Port 22
  User remote-username
  IdentityFile /Users/username/.ssh/ssh_keys/my-key-name
  UserKnownHostsFile ~/.ssh/known_hosts
  StrictHostKeyChecking no
  ForwardAgent yes
  IdentitiesOnly yes
  UseKeychain yes
  AddKeysToAgent yes
  ServerAliveInterval 60
  ServerAliveCountMax 1200
```

## Deep ssh settings on remote server


Create a copy of ssh config

    sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.factory-defaults

Change default port (from 22 to new-one (example 55673))

    sudo vi /etc/ssh/sshd_config

Make changes

    Port 55673
    PermitRootLogin no
    PubkeyAuthentication yes

Save config-file and restart ssh

```
# on ubuntu 20.04 or 22.04
sudo systemctl restart ssh

# or on ubuntu 24.04
sudo systemctl daemon-reload
sudo systemctl restart ssh.socket
```

Make a new connection on a NEW TERMINAL! If something goes wrong, you have previous terminal window.

    ssh -p 55673 user@host


Update others ssh settings

    sudo vi /etc/ssh/sshd_config

New settings

    PasswordAuthentication no
    PermitEmptyPasswords no
    MaxAuthTries 3
    AllowUsers username 
    LoginGraceTime 30

Save config-file and restart ssh

```
# on ubuntu 20.04 or 22.04
sudo systemctl restart ssh

# or on ubuntu 24.04
sudo systemctl daemon-reload
sudo systemctl restart ssh.socket
```


## More info

[Установка и настройка SSH на сервере с Ubuntu](https://selectel.ru/blog/ssh-ubuntu-setup/)

[Авторизация на базе ключей SSH](https://selectel.ru/blog/ssh-authentication/)

[Генерируем ssh-ключ на рабочем компьютере](https://selectel.ru/blog/tutorials/how-to-generate-ssh/)

[[Youtube] Создание SSH ключа, настройка SSH-сервера, клиента, проброс портов](https://www.youtube.com/watch?v=dy_XaQOJnPw)

[Почему SSH-ключ — безопасная альтернатива паролю](https://selectel.ru/blog/ssh-keys/)
