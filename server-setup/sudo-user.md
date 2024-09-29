# Create sudo user

Connect to server via ssh

    ssh root@host

Update & upgrade Ubuntu Repo's

    sudo apt update && sudo apt upgrade -y

Create new user on the server

    adduser newuser

Add user to sudo's group

    usermod -aG sudo newuser

Testing the rights

    su newuser
    sudo ls -la /root

Connect by new user newuser

    ssh newuser@host

Switch to root user with the command "sudo-i".
    
    sudo -i

or use all commands with sudo-prefix

    sudo <command>