# Linux-server-configuration

AIM: 

To build a web based linux server and host web application through that server by deploying one web application

REQUIREMENTS:

* web application that is already created
* ubuntu linux server instance on Amazon lightsail
* xip.io DNS server

HOW TO RUN:
* go to any browser and type the hosted ip address and click enter

RUN THE SERVER AS A GRADER:
* save the private key provided in your local system then
* run the command 
```
 ssh -i /path/to/privatekey -p 2200 grader@13.232.113.129 
```
* The password for grader is also provided in ```notes to reviewer``` field
* The grader have permission as root user
* grader is also a super user and have given a sudo permission for grader

IP AND SSH PORT:
* server ip ```13.232.113.129```
* ssh port ```2200```

HOSTED SITE URL:
* hosted url [http://13.232.113.129.xip.io/](http://13.232.113.129.xip.io/)

SUMMARY:
1. Create an instance in amazon lightsail account
2. Go to account and download static ip on your desktop
3. Download [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) to overcome the disconnections to the server
USER:
4. Make sure your system is upto date
```sudo apt-get update```
5. creating Grader User
```sudo adduser grader```
6. Go to the sudoers file
```sudo /etc/sudoers```
7. Append the following code below the root user
```grader  ALL=(ALL:ALL) ALL```
KEY PAIRS:
8. Generate an ssh key for grader on your local machine
```ssh-keygen```
9. give sudo permissions to grader
```su - grader```wait a min 
10. currently the grader is in the user folder i.e., root folder
11. create a directory .ssh there and new file named authorized_keys
```
	sudo mkdir .ssh
   sudo nano .ssh/authorized_keys
```
12. copy the public key and paste it into authorized_keys folder and press ctrl_x to save the file
PERMISSIONS:
13. give chmod permissions to the .ssh directory and authorized_keys file
```
	chmod 700 .ssh
   chmod 644 .ssh/authorized_keys
```
14. restart ssh
```sudo service ssh restart```
15. Log in to the grader with the private key
```
ssh -i .ssh/id_rsa grader@ipaddress 
```
CHANGING PORT AND ROOT LOGIN PERMISSIONS:
16. change port
```
sudo nano /etc/ssh/sshd_config
```
17. restart ssh 
```service ssh restart```
18. add a custom port tcp 2200 and custom port udp 123 in networking in amazon lightsale account
19. now log into the grader with private key and ssh port with the following command
```ssh -i .ssh/id_rsa -p 2200 grader@ipaddress```
20. disable the root login
```
	sudo nano /etc/ssh/sshd_config
    change permitrootlogin no
```
FIREWALL CONFIGURATION:
21. type the following commands 
```
	sudo ufw allow 2200/tcp
    sudo ufw allow 80/tcp
    sudo ufw allow 123/udp
    sudo ufw enable
```
22. check the status make sure if it is active
```sudo ufw status```
TIME ZONE:
23. changing time zone
```
	sudo dpkg-reconfigure tzdata
```
24. select the none of the above option and then select utc option
APACHE2 CONFIGURATION:
25. install apache2
```sudo apt-get install apache2```
26. install mod_wsgi
```sudo apt-get install python-setuptools libapache2-mod-wsgi```
27. Enable mod_wsgi
```sudo a2enmod wsgi```
SETTING UP FLASK:
28. create a flask app folder in /var/www and move into it
```
	sudo mkdir FlaskApp
    cd FlaskApp
```
29. Install git
```sudo apt-get install git```
30. Create another flask app folder
```
	sudo mkdir FlaskApp
    cd FlaskApp
```
31. clone your project directory
```sudo git clone https://github.com/username/catalog_item.git```
32. rename your main file to ```__init__.py```
33. Use json_url instead client_secrets.json in script otherwise u get an error
POSTGRESQL AND DB SET UP:
34. install postgres ```sudo apt-get install postgresql```
35. log into the user and shell ```sudo -i -u postgres psql```
36. create a user catalog with password 'password'
```
	CREATE USER catalog WITH PASSWORD 'password';
```
37. if you change the password u need to make changes in database_setup and init files where engine is used
38. give permission to user to create database ```ALTER USER catalog CREATEDB;```
39. create a database with user catalog
```CREATE DATABASE catalog WITH OWNER catalog;```
40. log into the database
```\c catalog```
41. revoke permissions to public ```REVOKE ALL ON SCHEMA public FROM public;```
42. give schema permission to catalog ```GRANT ALL ON SCHEMA public TO catalog;```
43. then quit```\q exit```
44. change the connection in database_setup and __init__ files to 
```
engine = create_engine('postgresql://catalog:password@localhost/catalog')
```
ENABLE AND CONFIG A NEW VIRTUAL HOST:
45. open the flaskapp.config file
```
	sudo nano /etc/apache2/sites-available/FlaskApp.conf
```
46. past the following code into that file
```
	<VirtualHost *:80>
		ServerName mywebsite.com
		ServerAdmin admin@mywebsite.com
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
47. enable the virtual host
```sudo a2ensite FlaskApp```
48. to disable the apche2 page
```sudo a2dissite 000-default.conf```
49. create the flaskapp.wsgi file in /var/www/FlaskApp folder
```
	cd /var/www/FlaskApp
    sudo nano flaskapp.wsgi 
```
50. add the following code in wsgi file
```
	#!/usr/bin/python
 	import sys
 	import logging
 	logging.basicConfig(stream=sys.stderr)
 	sys.path.insert(0,"/var/www/FlaskApp/")

 	from FlaskApp import app as application
 	application.secret_key = 'Add your secret key'
```
51. Install pip 
```sudo apt-get install python-pip```
52. install all the packages required for your project
```pip install flask sqlalchemy psycopg2 requests oauth2client```
SCRIPT ORIGIN AND URI REDIRECTS:
java script origin:
 ```http://ipaddress.xip.ip```
Redirect uri:
 ```
 http://ipaddress.xip.io/login

http://ipaddress.xip.io/gconnect

http://ipaddress.xip.io/callback
```
AT LAST:
52. restart your server and type the origin url in browser
```
sudo service apache2 restart
```
```http://ipaddress.xip.ip```

RESOURCES:
[Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps) to deploy Flask app into an ubuntu vps
[stack overflow](https://stackoverflow.com/)for error corrections
[xip.io](http://xip.io/)wildcar DNS Server free for everyone

OUTPUT:
to see the output [click here](http://13.232.113.129.xip.io/)

RESULT:
Deploying a web site into a linux server and providing security and configuration is completed
gained skills of sshkey pairs, ufw, creating user etc.,
