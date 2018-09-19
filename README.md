# Backup server configuration

This is an Ansible playbook which provisions a Raspbery Pi with Raspbian to act as
a NAS server for storing backups from multiple other computers.

## Requirements

- Raspberry Pi with Raspbian flashed on the memory card
- One external hard drive connected to the RPi. Use an external power source, such
  as a powered USB hub. RPi can't power a hard drive from its USB ports.

## Usage

As it stands the playbook will provision the server and accounts for two backup 
clients, a desktop and a laptop. Each client can access their own backup directory
on the hard drive and needs their own SSH key for connecting.

One privileged user account, called `user` will be created. It has passwordless
sudo access and requires an SSH key for connecting. All password logins are disabled
and a firewall is setup which allows traffic only from the 192.162.1.* subnet to
port 22.

The default Raspbian `pi` user account is removed altogether.

- Format the USB drive and note down the filesystem UUID (`lsblk --fs`)
- Copy `hosts.template` as `hosts` and fill the correct IP address of the RPi
- Copy `group_vars/vars.template` as `group_vars/vars` and fill the drive UUID and
  SSH public keys for the clients and the privileged user.
- For the first run, change `remote_user` to `pi` in `playbook.yml`. Afterwards
  revert back to `user` as the first run removes the `pi` account.
- Install Ansible on some computer. Execute 
  `ansible-playbook -i hosts -v playbook.yml --ask-pass`. On subsequent runs drop
  the `--ask-pass` and make sure ssh-agent is set up correctly for key
  authentication.

## TODO

- Multiple hard drives for mirroring the data
- Off-site mirroring on some cloud storage service