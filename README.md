# Dokku installation scripts

Instructions for setting up Dokku environment and deploying a simple application on it.

## Installation of Dokku server
1. Provision a virtual machine for Dokku. Specs for the VM used for the PoC:
   - AWS t2.medium
   - Ubuntu 22.04
   - AMD64
1. Configure the security groups of the server to allow inbound SSH and HTTP connections.
1. Install Ansible on the server:
   ```
   sudo ./install-ansible.sh
   ```
1. Install the Ansible Galaxy roles:
   ``` 
   ansible-galaxy install -r requirements.yml
   ```
1. Run the Ansible playbook locally:
   ```
   sudo ansible-playbook ansible-dokku-role.yml
   ```
1. **TODO: automate this!**
   Check that the global vhost of Dokku host is set to your domain that points to the server, e.g. dokku.me
   ```
   # Check the status of the vhost:
   dokku domains:report
   # Use this if not set correctly:
   dokku domains:set-global dokku.me
   ```
   This domain should have an A record or CNAME pointing at your server's IP (or alternatively configure the hosts file of your own workstation).
 
## Application deployment
1. Add the public SSH key that you will use for git push operations to Dokku, e.g:
   sudo cat ~/.ssh/authorized_keys | dokku ssh-keys:add admin
1. Create the Dokku app:
   dokku apps:create ruby-getting-started
1. Install PostgreSQL plugin:
   ```
   sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git
   dokku postgres:create railsdatabase
   dokku postgres:link railsdatabase ruby-getting-started
   ```
1. **On your local mahcine,** clone the ruby-getting-started Heroku example app:
   ```
   git clone https://github.com/heroku/ruby-getting-started
   ```
1. Deploy the app to the Dokku server:
   ```
   cd ruby-getting-started
   git remote add dokku dokku@dokku.me:ruby-getting-started
   git push dokku main
   ```
1. Browse to http://ruby-getting-started.dokku.me/
