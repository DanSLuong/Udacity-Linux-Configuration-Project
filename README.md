# Udacity-Linux-Configuration-Project

### Get your server
For this project we selected to use [Amazon Lightsail](https://lightsail.aws.amazon.com/ls/webapp/home/instances?#)

1. Login or create an account and then select create instance.

2. Configure the instance with the following settings: 
   
   * Platform: Unix/Linux
   
   * Blueprint: OS Only
   
   * Ubuntu
   
   * Plan: The first plan is fine for this project.

3. Name your instance and hit create (The instance will gray out for a little and then light up orange when ready.).

### Get your hostname from the assigned IP Address

1. Goto [HCIDATA](hcidata.info/host2ip.htm)

2. Type in your IP Address(In this case it is ```13.58.26.117```) and select ```Find Host Name```

3. Save the info for the Host Name section that is going to be the URL we will be using from this point on.

   ```ec2-13-58-26-117.us-east-2.compute.amazonaws.com```

### Connect to your new server

1. Click on your instance's name and goto the settings page.

2. On the bottom of the page click the link to the Account page.

3. Download and save the default SSH key pair and name it ```ssh_key.pem```.

4. Open a unix terminal and enter 

   ```$ ssh -p 22 -i ssh_key.pem ubuntu@13.58.26.117```

   or connect to the server through the link in the website.

### Secure your server

1. On the instance's settings page goto the networking tab.

2. Under the firewall section, add Custom TCP 2200 and Custom UDP 123 and hit save.

### Update all currently installed packages.

1. Update the package lists.
   
   ```$ sudo apt-get update```
   
2. Upgrade the the packages you have the the newest versions.

   ```$ sudo apt-get upgrade```
   
3. Remove dependencies that were installed but are no longer being used.
   ```$ sudo apt-get autoremove to remove unused packages```

4. Enable auto upgrades on the server.

   ```$ sudo apt-get install unattended-upgrades```
   
5. Install finger to determine that the user grader is actually created later. From udacity lesson.

   ```$ sudo apt-get install finger```

### Change the SSH port from 22 to 2200
1. Open the file ```/etc/ssh/sshd_config```.

   ```$ sudo nano /etc/ssh/sshd_config```

2. Change the port from 22 to 2200.

3. Save and exit.
   
   ```ctrl+o``` & ```ctrl+x```
 
### Configure the Uncomplicated Firewall
1. ```$ sudo ufw allow 2200/tcp```

2. ```$ sudo ufw allow 123/upd```

3. ```$ sudo ufw allow 80/tcp```

4. ```$ sudo ufw enable```

5. Restart the ssh server.
   
   ```$ sudo service ssh restart```

### Creating the grader user and granting sudo access
1. Create a new user account named ```grader```.

   ```sudo adduser grader```
   
2. Give sudo access to the ```grader```.

   * Open the file ```/etc/sudoers.d/grader```.
   
     ```$ sudo nano /etc/sudoers.d/grader```
    
   * Paste the following:
   
     ```$ grader ALL=(ALL) NOPASSWD:ALL```
    
   * Save and exit.
   
     ```ctrl+o``` & ```ctrl+x```

### Create an SSH key pair for grader using the ssh-keygen tool.
1. From a native terminal create a ssh-key.

   ```$ ssh-keygen```
   
2. Save the file as authorized_keys and it will generate two keys:

   ```authorized_keys``` and ```authorized_keys.pub```
   
3. Read and copy the contents of the ```autorized_keys.pub```.

   ```$ sudo cat authorized_keys.pub```
   
4. In the instance as the user grader, create the .ssh folder.

   ```$ sudo mkdir .ssh```

5. Create the authorized_keys file.

   ```$ sudo touch .ssh/authorized_keys```
   
6. Paste the copied content.
   
7. Save and exit.
   
   ```ctrl+o``` & ```ctrl+x```

### Install and configure Apache to serve a Python mod_wsgi application.

1. Install git.

   ```$ sudo apt-get install git```

2. Install Apache.
   
   ```$ sudo apt-get install apache2```

3. Install mode-wsgi for python development.

   ```$ sudo apt-get install libapache2-mod-wsgi python-dev```

4. Install pip.
   
   ```$ sudo apt-get install python-pip```

5. Update pip.

   ```$ sudo pip install --upgrade pip```

6. Install Flask.

   ```$ sudo pip install Flask```

7. Install all of the remaining packages that you used for your application.

   - ```$ sudo pip install sqlalchemy```
   - ```$ sudo pip install sqlalchemy_utils```
   - ```$ sudo pip install oauth2client```
   - ```$ sudo pip install --upgrade oauth2client```
   - ```$ sudo pip install render_template```
   - ```$ sudo pip install requests```
   - ```$ sudo pip install redirect```
   - ```$ sudo pip install httplib2```
   

### Install and configure PostgreSQL

1. Install PostgrSQL.

   ```$ sudo apt-get install postgresql```
   
2. Change users to postgres.

   ```$ sudo su - postgres```
   
3. Connect to postgres shell.

   ```$ psql```
   
4. Create the ```catalog``` database.

   ```# CREATE DATABASE catalog;```

5. Create the ```catalog``` user.

   ```# CREATE USER catalog;```

6. Set password for ```catalog``` user.

   ```# ALTER ROLE catalog WITH PASSWORD 'password';```

7. Grant control of ```catalog``` database to the ```catalog``` user.

   ```# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;```
   
8. Exit postgres shell.

   ```# \q```
   
9. Exit postgres user.

   ```$ exit```

10. Install the PostgreSQL adapter for Python, psycopg2.

   ```$ sudo apt-get -qqy install postgresql python-psycopg2```   
   
### Clone the Item Catalog project

1. Connect to ```grader```.

   ```$ sudo su - grader```
   
2. Goto the ```/var/www/``` folder.

   ```$ cd /var/www/```

3. Create a folder named FlaskApp.

   ```$ sudo mkdir FlaskApp```

4. Move into folder.

   ```$ cd FlaskApp```

5. Clone your Item Catalog project from the Github repository you created earlier in this Nanodegree program.

   ```$ sudo git clone https://github.com/DanSLuong/catalog.git FlaskApp```

### Configure the Item Catalog Project

1. Move into FlaskApp folder.

   ```$ cd FlaskApp```

2. Rename the application.py file to __init__.py.

   ```$ sudo application.py __init__.py```
   
3. Change the path to the client_secrets.json and fb_client_secrets.json files in __init__.py to the direct file path.

   ```/var/www/FlaskApp/FlaskApp/client_secrets.json``` and ```/var/www/FlaskApp/FlaskApp/fb_client_secrets.json```

4. Change the database connection from ```create_engine('sqlite:///basketballteam.db')``` to ```create_engine('postgresql://catalog:password@localhost/catalog')``` in the ```__init__.py```,  ```database_setup.py```, and ```rosters.py``` files.

5. Create the database.

   ```$ sudo python database_setup.py```
   
6. Populate the database.

   ```$ sudo python rosters.py```

7. Configure and update the Client Secret and Client ID info for the [Google](https://console.developers.google.com/) and [Facebook](https://developers.facebook.com/apps/) after updating your credentials with the URL and IP address of our server.

### Configure a Virtual Host

1. Created FlaskApp.conf.

   ```$ sudo nano /etc/apache2/sites-available/FlaskApp.conf```
   
2. Paste the following lines of code into the file and change the ServerrName and ServerAdmin to your info.
   
   ```  
	   <VirtualHost *:80>  
		                  ServerName 13.58.26.117 
                    ServerAdmin Admin  
                    WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi  
                    <Directory /var/www/FlaskApp/FlaskApp/>  
                            Order allow,deny  
                            Allow from all  
                    </Directory>  
                    Alias /static /var/www/FlaskApp/FlaskApp/static  
                    <Directory /var/www/FlaskApp/FlaskApp/static/>  
                            Order allow,deny  
                            Allow from all  
                    </Directory>  
                    ErrorLog ${APACHE_LOG_DIR}/error.log  
                    LogLevel warn  
                    CustomLog ${APACHE_LOG_DIR}/access.log combined  
	   </VirtualHost>  
   ```  

3. Disable the default virtual host.

   ```$ sudo a2dissite 000-default.conf```
   
4. Enable the newly created virtual host.

   ```$ sudo a2ensite FlaskApp.conf```

### Create the .wsgi file

1. Move to the ```/var/www/FlaskApp``` folder.

   ```$ cd /var/www/FlaskApp```
   
2. Create the ```flaskapp.wsgi``` file.

   ```$ sudo nano flaskapp.wsgi```
   
3. Paste the following lines of code into the fille.

   ```
   #!/usr/bin/python
   import sys
   import logging
   logging.basicConfig(stream=sys.stderr)
   sys.path.insert(0,"/var/www/FlaskApp/")

   from FlaskApp import app as application
   application.secret_key = 'Add your secret key'
   ```

4. Save and exit.

   ```ctrl+o``` & ```ctrl+x```
   
### Restart the Apache server

```$ sudo service apache2 restart```
   

### References

* [How To Deploy a Flask Application on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
