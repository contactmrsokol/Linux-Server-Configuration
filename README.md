# Linux-Server-Configuration

## IP Address

52.14.139.55

## URL

http://52.14.139.55/

## Software Installed

Apache, PostgreSQL, Flask, pip, oauth2client, requests, git.

## Configurations

Below are list of commands I used for the purpose described.

### Add User grader

useradd -m -s /bin/bash grader

### Add User grader to sudo group

usermod -aG sudo grader

### Update all currently installed packages

apt-get update

apt-get upgrade

reboot

### Set-up SSH keys for user grader

On a local machine:

ssh-keygen

in /home/grader/:

sudo mkdir .ssh

sudo cd .ssh

sudo nano authorized_keys

Paste the public key contents that you have on your local machine as a result of ssh-keygen into the above authorized_keys file.

### Disable root login

sudo nano /etc/ssh/sshd_config

change line "PermitRootLogin without-password" to "PermitRootLogin no"

uncomment line "PasswordAuthentication no"

service ssh restart

### Change timezone to UTC

sudo timedatectl set-timezone UTC

### Change SSH port from 22 to 2200

sudo nano /etc/ssh/sshd_config

change line "Port 22" to "Port 2200"

sudo service ssh restart

### Configure Uncomplicated Firewall

sudo ufw allow 2200

sudo ufw allow 80

sudo ufw allow 123

sudo ufw deny 22

sudo ufw enable

### Clone the repository

cd /var/www

sudo git clone https://github.com/contactmrsokol/Catalog.git

### Update Catalog.wsgi

I put this into the file:

"
import sys
import logging

logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, '/var/www/Catalog')

from application import app as application
application.secret_key = 'SUPER_SECRET_KEY'  
"
### Update the Google OAuth client secrets file

nano client_secrets.json

add http://52.14.139.55/ to "javascript_origins": part and also in your google developer console in credentials for this project.

### Configure Apache2 to serve the app

sudo nano /etc/apache2/sites-enabled/Catalog.conf

Paste this into a file:

"
<VirtualHost *:80>
     ServerName  PublicIP
     ServerAdmin webmaster@localhost
     #Location of the Catalog WSGI file
     WSGIScriptAlias / /var/www/Catalog/application.wsgi
     #Allow Apache to serve the WSGI app from our catalog directory
     <Directory /var/www/Catalog>
          Order allow,deny
          Allow from all
     </Directory>
     #Allow Apache to deploy static content
     <Directory /var/www/Catalog/static>
        Order allow,deny
        Allow from all
     </Directory>
      ErrorLog ${APACHE_LOG_DIR}/error.log
      LogLevel warn
      CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
"

sudo a2dissite 000-default.conf

sudo a2ensite Catalog.conf

sudo service apache2 reload

And that's it enjoy the app!

## Resources

[Flask Config](http://flask.pocoo.org/docs/0.12/config/)

[Google](http://www.google.com)

## About

Author: Alexander Sokol

Email: contactmrsokol@gmail.com
