# Server Configuration 

## connecting
- web: http://52.79.223.178/
- ip: 52.79.223.178
- ssh port: 2222

## ssh key for grader
- ssh key for gradier is in the file ./grader
- passcode for sshkey : grader123

## SSH config
- root login disabled (PermitRootLogin no)
- password authenticated disabled (PasswordAuthentication no)

## sudo previllage
- grader given the sudo previllage by created a grader file in /etc/sudoers.d/
- content of the grader file: grader ALL=(ALL) NOPASSWD:ALL

## Ports
- sudo ufw default deny incoming
- sudo ufw default allow outgoing
- sudo ufw allow 2222/tcp
- sudo ufw allow www
- sudo ufw allow 123/tcp
- sudo ufw enable

## SSH default port from 22 to 2222
- open port in amazon lightsail 
    - Custom    TCP    2222
- edit default port in /etc/ssh/sshd_config 


## Package Upgrade
- sudo apt-get install update
- sudo apt-get install upgrade


## installing package
```
    $ export LC_ALL="en_US.UTF-8"
    $ export LC_CTYPE="en_US.UTF-8"
    $ sudo dpkg-reconfigure locales

    $ apt-get -qqy install make zip unzip postgresql
    $ apt-get -qqy install python python-pip
    $ pip2 install --upgrade pip
    $ pip2 install flask packaging oauth2client redis passlib flask-httpauth
    $ pip2 install sqlalchemy flask-sqlalchemy psycopg2-binary bleach requests
```


