# Create sudo user

Connect to server via ssh
```bash
ssh root@host
```

Update & upgrade Ubuntu Repo's

```bash
apt update && apt upgrade -y
```

Install sudo

```bash
apt install sudo -y
```

Create new user on the server

```bash
adduser newuser
```

Add user to sudo's group

```bash
usermod -aG sudo newuser
```

Testing the rights

```bash
su newuser
sudo ls -la /root
```

Connect by new user `newuser`

```bash
ssh newuser@host
```

Switch to root user with the command `sudo-i`.

```bash
sudo -i
```

or use all commands with sudo-prefix `sudo <command>`