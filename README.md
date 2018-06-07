# Udacity-Linux-Configuration-Project

### Get your server
1. https://lightsail.aws.amazon.com/ls/webapp/home/instances?#
2. Select Unix/Linux
3. Blueprint: OS Only
4. Ubuntu
5. The first plan is fine for this project
6. Name your instance and hit create (The instance will gray out for a little and then light up orange when ready.

### Connect to your new server
1. Click on your instance's name and goto the settings page.
2. On the bottom of the page click the link to the Account page.
3. Download and save the default SSH key pair and name it ssh_key.pem.
2. Open a unix terminal and enter ssh -p 22 -i ssh_key.pem ubuntu@13.58.26.117 or through the website UI by selecting connect.

### Secure your server
1. On the instance's settings page goto the networking tab.
2. Under the firewall section, add Custom TCP 2200 and Custom UDP 123 and hit save.

### Update all currently installed packages.
1. sudo apt-get update
2. sudo apt-get upgrade
3. sudo apt-get autoremove to remove unused packages
4. sudo apt-get install unattended-upgrades to enable autougrades
5. sudo apt-get install finger just like in the udacity lessons to check when the grader user is created

### Change the SSH port from 22 to 2200
1. sudo nano /etc/ssh/sshd_config
2. Change the port from 22 to 2200
3. ctrl+o & ctrl+x to save and exit
 
### Configure the Uncomplicated Firewall
1. sudo ufw allow 2200/tcp
2. sudo ufw allow 123/upd
3. sudo ufw allow 80/tcp
4. sudo ufw enable
5. sudo service ssh restart

### Creating the grader user and granting sudo access
1. Create a new user account named grader with the following terminal command
  sudo adduser grader
2. Give sudo access to the grader
  sudo nano /etc/sudoers.d/grader
  paste the following:
  grader ALL=(ALL) NOPASSWD:ALL
  ctrl+o & ctrl+x to save and exit

### Create an SSH key pair for grader using the ssh-keygen tool.
1. From a native terminal create a ssh-key using the command ssh-keygen
2. Save the file as authorized_keys and it will generate two keys:
authorized_keys and authorized_keys.pub
3. Read and copy the contents of the autorized_keys.pub with sudo cat authorized_keys.pub
4. In the instance as the user grader, create the .ssh folder
sudo mkdir .ssh
5. Create the authorized_keys file
sudo touch .ssh/authorized_keys
6. Paste the copied content
7. ctrl+o & ctrl+x to save and exit

### Install and configure Apache to serve a Python mod_wsgi application.
1. Install git sudo apt-get install git
2. sudo apt-get install apache2
3. sudo apt-get install libapache2-mod-wsgi python-dev
4. sudo apt-get install python-pip
5. sudo pip install Flask



If you built your project with Python 3, you will need to install the Python 3 mod_wsgi package on your server: sudo apt-get install libapache2-mod-wsgi-py3.
### Install and configure PostgreSQL
1. sudo apt-get install postgresql
2. sudo su - postgres
3. psql
4. CREATE DATABASE catalog;
5. CREATE USER catalog;
6. ALTER ROLE catalog WITH PASSWORD 'password';
7. GRANT ALL PRIVILEGES ON DATABASE catalog TO catalgo;
8. \q and exit


### Deploy the Item Catalog project
1. Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.
2. Set it up in your server so that it functions correctly when visiting your server’s IP address in a browser. Make sure that your .git directory is not publicly accessible via a browser!
