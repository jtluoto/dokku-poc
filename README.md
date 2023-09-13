# Dokku installation scripts

Tested on Ubuntu 22.04

```
sudo ./install-ansible.sh
ansible-galaxy install -r requirements.yml
sudo ansible-playbook ansible-dokku-role.yml
```
