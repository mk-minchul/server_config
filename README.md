# Server Configuration 

## connecting
- web: http://15.164.20.78//
- ip: 15.164.20.78/
- ssh port: 2200

## ssh key for grader
- ssh key for gradier is in the file ./grader
- passcode for sshkey : grader123

## SSH config
- root login disabled (PermitRootLogin no)
- password authenticated disabled (PasswordAuthentication no)

## sudo previllage

```
$ sudo adduser grader
$ sudo usermod -aG sudo grader
```

- grader given the sudo previllage by created a grader file in /etc/sudoers.d/
- content of the grader file: grader ALL=(ALL) NOPASSWD:ALL

## adding sshkey to grader

```
mkdir /home/grader/.ssh
chown grader:grader /home/grader/.ssh
chmod 700 /home/grader/.ssh
```

- add public key to /home/grader/.ssh/authorized_keys

```
chown grader:grader /home/grader/.ssh/authorized_keys
chmod 644 /home/grader/.ssh/authorized_keys
```

## Ports
- sudo ufw default deny incoming
- sudo ufw default allow outgoing
- sudo ufw allow 2200/tcp
- sudo ufw allow www
- sudo ufw allow 123/tcp
- sudo ufw enable

## SSH default port from 22 to 2200
- open port in amazon lightsail 
    - Custom    TCP    2200
- edit default port in /etc/ssh/sshd_config 


## Package Upgrade
- sudo apt-get install update
- sudo apt-get install upgrade
- reboot


## setup apache and postgres user and db
```
sudo timedatectl set-timezone UTC
export LC_ALL="en_US.UTF-8"
export LC_CTYPE="en_US.UTF-8"

sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi
sudo apt-get install postgresql postgresql-contrib

sudo -u postgres createuser -P catalog
sudo -u postgres createdb -O catalog catalog
```

## setup necessary python packages
```
sudo pip install --upgrade pip
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
```

## download the app from git and place it in fullstack-nanodegree-vm folder
```
cd /var/www/
sudo mkdir fullstack-nanodegree-vm
sudo chown www-data:www-data fullstack-nanodegree-vm/
sudo -u www-data git clone https://github.com/mk-minchul/item_catalog.git fullstack-nanodegree-vm
```

## create a catalog directory and move all files into it.
```
mkdir catalog
mv fullstack-nanodegree-vm/* catalog/
```

## 
sudo vim /var/www/.htaccess
# content should be 
```RedirectMatch 404 /\.git```


## change all files containing create_engine to the correct postgre db
```
cd catalog
grep -iRl "create_engine" ./
```
change
```
engine = create_engine('sqlite:///restaurantmenu.db')
```
to
```
engine = create_engine('postgresql://catalog:grader123@localhost/catalog')
```

## setup for apache
```
mv app.py __init__.py

```

## oauth setup
"javascript_origins":["http://15.164.20.78"]


## create /var/www/fullstack-nanodegree-vm/catalog.wsgi file
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/fullstack-nanodegree-vm/")

from catalog import app as application

application.secret_key = 'YOUR_SECRET_KEY'
```

## create /etc/apache2/sites-available/catalog.conf
```
<VirtualHost *:80>
	ServerName http://15.164.20.78
	ServerAdmin admin@15.164.20.78
	WSGIDaemonProcess catalog user=www-data group=www-data threads=5
	WSGIProcessGroup catalog
	WSGIApplicationGroup %{GLOBAL}
	WSGIScriptAlias / /var/www/fullstack-nanodegree-vm/catalog.wsgi
	<Directory /var/www/fullstack-nanodegree-vm/catalog/>
		Require all granted
	</Directory>
	Alias /static /var/www/fullstack-nanodegree-vm/catalog/static
	<Directory /var/www/fullstack-nanodegree-vm/catalog/static/>
		Require all granted
	</Directory>
	ErrorLog ${APACHE_LOG_DIR}/error.log
	LogLevel warn
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

## start service
```
sudo a2dissite 000-default.conf
sudo a2ensite catalog.conf
sudo service apache2 restart
tmux
python __init__.py
sudo service apache2 restart
```


## Reference
- https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps