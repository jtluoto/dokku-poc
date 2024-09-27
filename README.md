# Dokku PoC

A PoC of how continuous deployment can be implemented with Dokku without allowing pushes outside of the server. The server can pull for changes and in case of new commits, the app is redoployed.

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
1. Configure the domain names. Create an A record or CNAME pointing to your server's IP. Also create an A record for the test app. Alternatively configure your hosts file, e.g:
   ```
   12.345.67.89      dokku-poc.me ruby-getting-started.dokku-poc.me
   ```
1. Define the variable dokku_global_domain in the dokku-server-and-app.yml playbook's vars section. Use the same domain you defined in the previous step.
   
1. Run the Ansible playbook on the server:
   ```
   sudo ansible-playbook dokku-server-and-app.yml
   ```
1. Browse to http://ruby-getting-started.dokku-poc.me/


## Redeploying the application
The app can be redeployed by running the same playbook with deploy_only tag:
```
sudo ansible-playbook dokku-server-and-app.yml --tags deploy_only
```
Automatic deployment by pulling could be implemented e.g. by checking for new commits in cron and if detected running this tag.

## Deploying by pushing to Dokku
The deployment can be also triggered by pushing new commits to Dokku.

1. Uncomment the dokku_users block in dokku-server-and-app.yml.
   
1. Add the public key to the path indicated by the path in the dokku_users block. This key will be used for git operations when pushing commits to Dokku. This can be the same key you use for SSHing the server.

1. Rerun the Ansible playbook.

1. Clone the Heroku Ruby On Rails Getting Started project on your local workstation:
   ```
   git clone https://github.com/heroku/ruby-getting-started
   ```
1. Deploy the app (change the domain name if needed):
   ```
   cd ruby-getting-started
   git remote add dokku dokku@dokku-poc.me:ruby-getting-started
   git push dokku main
   ```

Change1
