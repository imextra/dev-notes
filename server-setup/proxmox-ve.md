# Proxmox Virtual Enviroment

# Create new VM

## Create VM settings

Click button "Create VM"


## First steps iun new VM

Update & upgrade Ubuntu Repo's

    sudo apt update && sudo apt upgrade -y

### Install Qemu Guest Agent

Install qemu-guest-agent

    sudo apt install qemu-guest-agent -y

Enable qemu

    sudo systemctl enable qemu-guest-agent

Restart VM

    sudo shutdown now