# Dokku PoC

## Dokku server installation and test application deployment

1. Provision a virtual machine for Dokku. These scripts have been tested with the following VM specs:
   - AWS t2.medium x86
   - Ubuntu 22.04
1. Configure the security groups of the server to allow inbound SSH and HTTP connections.
1. Clone this repo to the server.
1. Install Ansible on the server:
   ```
   sudo ./install-ansible.sh
   ```
1. Install the Ansible Galaxy roles on the server:
   ``` 
   ansible-galaxy install -r requirements.yml
   ```
1. Define the variables in the dokku-server-and-app.yml playbook's vars section:
   - dokku_global_domain: this domain should have an A record or CNAME pointing at your server's IP, or alternatively configure the hosts file of your own workstation.
   - dokku_users -> admin -> ssh_key: the public key that you use to SSH the server, this is used for making pushes to Dokku
1. Run the Ansible playbook on the server:
   ```
   sudo ansible-playbook dokku-server-and-app.yml
   ```
1. Clone the Heroku Ruby On Rails getting started on your local workstation:
   ```
   git clone https://github.com/heroku/ruby-getting-started
   ```
1. Deploy the app (change the domain name if needed):
   ```
   cd ruby-getting-started
   git remote add dokku dokku@dokku.me:ruby-getting-started
   git push dokku main
   ```
1. Browse to http://ruby-getting-started.dokku.me/
