[![license](https://img.shields.io/badge/license-MIT-blue.svg)](https://choosealicense.com/)

<a href="https://www.udacity.com/">
  <img src="https://s3-us-west-1.amazonaws.com/udacity-content/rebrand/svg/logo.min.svg" width="300" alt="Udacity logo svg">
</a> 


# Linux Server Configuration

The purpose of this project was to take a baseline installation of Ubuntu and configure it to serve a Flask application via Apache2. Furthermore, this project involved creating user accounts, granting permissions, configuring ssh logins, configuring an uncomplicated firewall, and configuring Apache2 to serve the application.

The application source code can be found <a href="https://github.com/louismagdaleno/fsnd-flask-social-network"><strong>here</strong></a>.

The deployed application can be accessed at <strong>https://louismagdaleno.com</strong>.

The public IP address of the server is <strong>54.158.49.179</strong>.

## Technologies Used

* `Ubuntu`
* `Apache2` 
* `Python`
* `Flask`
* `Sqlite`

## Create Droplet
Instructions for creating a digital ocean Ubuntu droplet can be found <a href="https://www.digitalocean.com/docs/droplets/how-to/create/"><strong>here</strong></a>

## Create SSH Keys
1. Run the command `ssh-keygen` on your local machine to generate a key pair.

## Connect to the server
2. Run the command `ssh ubuntu@54.158.49.179 to connect to the server. You will be prompted for the root password, which was sent via email by digital ocean.




## Create a new user named grader
1. `sudo adduser grader`
2. `nano /etc/sudoers`
3. `touch /etc/sudoers.d/grader`
4. `nano /etc/sudoers.d/grader`, type in `grader ALL=(ALL:ALL) ALL`, save and quit

## Set ssh login using keys
1. generate keys on local machine using`ssh-keygen` ; then save the private key in `~/.ssh` on local machine
2. deploy public key on developement enviroment

	On you virtual machine:
	```
	$ su - grader
	$ mkdir .ssh
	$ touch .ssh/authorized_keys
	$ vim .ssh/authorized_keys
	```
	Copy the public key generated on your local machine to this file and save
	```
	$ chmod 700 .ssh
	$ chmod 644 .ssh/authorized_keys
	```
	
3. reload SSH using `service ssh restart`
4. now you can use ssh to login with the new user you created

	`ssh -i [privateKeyFilename] grader@louismagdaleno.com -p 2200`

## Disable Password Authentication and Root login
1. Run `sudo nano /etc/ssh/sshd_config`
2. Find the line that specifies PasswordAuthentication, uncomment it by deleting the preceding #, then change its value to "no".
3. Find the line that specifies PermitRootLogin and set it's value to "no".
3. Type this to reload the SSH daemon: `sudo systemctl reload sshd`

Resource https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04


## Update all currently installed packages

	sudo apt-get update
	sudo apt-get upgrade

## Change the SSH port from 22 to 2200
1. Use `sudo nano /etc/ssh/sshd_config` and then change Port 22 to Port 2200 , save & quit.
2. Reload SSH using `sudo service ssh restart`

## Configure the Uncomplicated Firewall (UFW)

Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

	sudo ufw allow 2200/tcp
	sudo ufw allow 80/tcp
	sudo ufw allow 123/udp
	sudo ufw enable 
 
## Configure the local timezone to UTC
1. Configure the time zone `sudo dpkg-reconfigure tzdata`
2. It is already set to UTC.

## Install and configure Apache to serve a Python mod_wsgi application
1. Install Apache `sudo apt-get install apache2`
2. Install mod_wsgi `sudo apt-get install python-setuptools libapache2-mod-wsgi`
3. Restart Apache `sudo service apache2 restart`


	```
## Install git, clone and setup your Catalog App project.
1. Install Git using `sudo apt-get install git`
2. Use `cd /var/www` to move to the /var/www directory 
3. Create the application directory `sudo mkdir socialnetwork`
4. Move inside this directory using `cd socialnetwork`
5. Clone the Catalog App to the virtual machine `git clone https://github.com/louismagdaleno/fsnd-flask-social-network.git`
6. Move the contents of the cloned repo inside the socialnetwork folder `sudo mv ./fsnd-flask-social-network ./`
7. Delete the fsnd-flask-social-network folder `sudo rm -rf fsnd-flask-social-network`
8. Install pip `sudo apt-get install python-pip`
9. Use pip to install dependencies `sudo pip install -r ../requirements.txt`


## Configure and Enable a New Virtual Host
1. Create socialnetwork.conf and edit: `sudo nano /etc/apache2/sites-available/socialnetwork.conf`
2. Add the following lines of code to the file to configure the virtual host. 
	
	```
	<VirtualHost *:80>
		ServerName louismagdaleno.com
		ServerAdmin admin@louismagdaleno.com
		WSGIScriptAlias / /var/www/socialnetwork/socialnetwork.wsgi
		<Directory /var/www/socialnetwork/socialnetwork/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/socialnetwork/socialnetwork/static
		<Directory /var/www/socialnetwork/socialnetwork/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
	</VirtualHost>
	```
3. Enable the virtual host with the following command: `sudo a2ensite socialnetwork`

## Create the .wsgi File
1. Create the .wsgi File under /var/www/socialnetwork: 
	
	```
	cd /var/www/socialnetwork
	sudo nano socialnetwork.wsgi 
	```
2. Add the following lines of code to the socialnetwork.wsgi file:
	
	```
	#!/usr/bin/python
	import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0,"/var/www/socialnetwork/")

	from socialnetwork import app as application
	application.secret_key = 'Add your secret key'
	```

## Restart Apache
1. Restart Apache `sudo service apache2 restart `

## References:
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps



[(Back to top)](#top)
