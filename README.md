# ERPNext 14
ERPNext is the world's best free and open source ERP

## NOTES

#### SHOULD BE ONLY 2 USERS IN SYSTEM [ root & frappe ]
you can use any other user other then frappe

#### ROOT & FRAPPE PASSWORDS SHOULD BE SAME AS MARIADB
you can use any other password other then frappe07

#### SERVER SHOULD BE UPGRADED BEFORE START INSTALLATION

.
.

## DEVELOPMENT SETUP

```
sudo apt-get clean -y
sudo apt-get autoremove -y
sudo apt --fix-broken install -y
sudo dpkg --configure -a
sudo apt-get install -f
sudo apt-get update && sudo apt-get upgrade
```

## root@server22
```
su - root
```
#### FIREWALL
```
ufw allow 22,25,143,80,443,3306,3022,8000/tcp
ufw enable
```
#### REQUIRED TOOLS
```
apt-get install git python3-dev python3.10-dev python3-setuptools python3-pip python3-distutils redis-server -y
apt install python3.10-venv -y
sudo apt-get update -y
apt-get install xvfb libfontconfig wkhtmltopdf -y
```
#### MARIADB
#### SET YOUR ROOT PASSWORD HERE
```
sudo passwd root
password: frappe07
```
```
apt-get install mariadb-server mariadb-client -y
mysql_secure_installation
```
```
Change the root password? [Y/n] n
Change the root password? [Y/n] y
password: frappe07
Remove anonymous users? [Y/n] y
Disallow root login remotely? [Y/n] y
Remove test database and access to it? [Y/n] y
Reload privilege tables now? [Y/n] y
```
#### set root password. THIS IS MUST
```
mysql -u root -p
USE mysql;
ALTER USER root@localhost IDENTIFIED VIA mysql_native_password;
SET PASSWORD = PASSWORD(‘frappe07’);
#### GIVE THE PASSWORD AS ABOVE IN mysql_secure_installation SETUP
FLUSH PRIVILEGES;
exit
```
```
nano /etc/mysql/my.cnf
#### add these lines @end of the file

[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
```
```
service mysql restart
sudo reboot
```
## frappe@server22
```
#### SET YOUR USER PASSWORD HERE
sudo passwd frappe
password: frappe07
```
```
sudo su - frappe
sudo chmod o+x $HOME
tee -a ~/.bashrc<<EOF
PATH=$PATH:~/.local/bin/
EOF
source ~/.bashrc
```
#### NODE JS & NPM
```
sudo apt-get install curl
curl -sL https://deb.nodesource.com/setup_16.x 1 | sudo -E bash -
sudo apt-get install -y nodejs
```
#### BENCH DIRECTORY
```
sudo mkdir /home/bench
sudo chown -R frappe /home/bench
cd /home/bench
sudo npm install -g yarn
```
#### FRAPPE
```
sudo pip3 install frappe-bench
```
```
#### Dont use other versions or something else
bench init frappe --verbose --frappe-branch v14.0.0
cd frappe
bench start
```
## OPEN NEW SESSION/TERMINAL
```
#### SITE CREATION
sudo su - frappe
cd /home/bench/frappe
bench new-site site1.local
bench use site1.local
bench migrate
```
#### ERPNEXT
```
bench get-app payments
###### make sure bench is runing in other terminal
bench get-app erpnext --branch v14.0.0
###### make sure bench is runing in other terminal
bench get-app hrms
###### make sure bench is runing in other terminal
bench --site site1.local install-app erpnext
###### make sure bench is runing in other terminal
bench --site site1.local install-app hrms
###### make sure bench is runing in other terminal

bench use site1.local
bench migrate
```

## GO TO [SERVER IP]:8000 SETUP YOUR DEVELOPMENT SYSTEM



## PRODUCTION SETUP
```
bench --site site1.local enable-scheduler
bench --site site1.local add-to-hosts
bench --site site1.local set-maintenance-mode off
bench --site site1.local scheduler resume
sudo snap install --classic certbot
sudo bench setup production frappe
bench setup supervisor
sudo ln -s pwd/config/supervisor.conf /etc/supervisor/conf.d/frappe.conf
sudo bench setup sudoers $(whoami)
```
#### STOP BENCH ++> ctrl+c
```
sudo service supervisor stop
sudo service nginx stop
sudo service supervisor start
sudo service nginx start
sudo bench setup production frappe
```

## GO TO [SERVER IP] YOUR PRODUCTION SYSTEM IS UP

