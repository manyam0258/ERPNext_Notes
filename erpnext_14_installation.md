Hi guys! :raised_hand_with_fingers_splayed:

ERPNext v14 has just released and I know everyone wants to try it out quickly. Installing Frappe & ERPNext can be a daunting task, especially when try to install it manually but don’t worry! Just follow this simple step-by-step guide and you’ll sail through the installation process.

This guide might look intimidating at first but trust me it is lengthy because it is detailed and explains each step one by one.

Let’s start!

:page_facing_up: Table of Contents:

Server Setup
Install Required Packages
Configure MySQL Server
Install CURL, Node, NPM, Yarn
Install Frappe Bench
Install ERPNext and other Apps
Setup Product Server
PRE-REQUISITES:
Operating System: Linux Ubuntu 22.04 LTS
Minimum Recommended Hardware: 2 CPU | 2 GB RAM | 10 GB Disk
Root shell access to the server (via SSH)
:one: SERVER SETUP
1.1 Login to the server as root user

1.2 Setup correct date and timezone (important step as it impacts ERPNext usage)

Check the server’s current timezone

date
Set correct timezone as per your region

timedatectl set-timezone "Asia/Kolkata"
1.3 Update & upgrade server packages

sudo apt-get update -y
sudo apt-get upgrade -y
1.4 Create a new user
We create this user as a security measure to prevent root user access.
This user will be assigned admin permissions and will be used as the main Frappe Bench user

sudo adduser [frappe-user]
usermod -aG sudo [frappe-user]
su [frappe-user] 
cd /home/[frappe-user]/
Note: Replace [frappe-user] with your username. Eg. sudo adduser myname

:two: INSTALL REQUIRED PACKAGES
Frappe Bench and ERPNext requires many packages to run smoothly. In this step we will install all the required packages for the system to work correctly.

Note: During the installation of these packages the server might prompt you to confirm if you want to continue installing the package [Y/n]. Just hit “y” on your keyboard to continue.

2.1 Install GIT

sudo apt-get install git
Check if GIT is correctly installed by running git --version

2.2 Install Python

sudo apt-get install python3-dev python3.10-dev python3-setuptools python3-pip python3-distutils
2.3 Install Python Virtual Environment

sudo apt-get install python3.10-venv
Check if Python is correctly installed by running python3 -V

2.4 Install Software Properties Common (for repository management)

sudo apt-get install software-properties-common
2.5 Install MariaDB (MySQL server)

sudo apt install mariadb-server mariadb-client
Check if MariaDB is correctly installed by running mariadb --version

2.6 Install Redis Server

sudo apt-get install redis-server
2.7 Install other necessary packages (for fonts, PDFs, etc)

sudo apt-get install xvfb libfontconfig wkhtmltopdf
sudo apt-get install libmysqlclient-dev
:three: CONFIGURE MYSQL SERVER
3.1 Setup the server

sudo mysql_secure_installation
During the setup process, the server will prompt you with a few questions as given below. Follow the instructions to continue the setup;

Enter current password for root: (Enter your SSH root user password)

Switch to unix_socket authentication [Y/n]: Y

Change the root password? [Y/n]: Y
It will ask you to set new MySQL root password at this step. This can be different from the SSH root user password.

Remove anonymous users? [Y/n] Y

Disallow root login remotely? [Y/n]: N
This is set as N because we might want to access the database from a remote server for using business analytics software like Metabase / PowerBI / Tableau, etc.

Remove test database and access to it? [Y/n]: Y

Reload privilege tables now? [Y/n]: Y

3.2 Edit the MySQL default config file

sudo vim /etc/mysql/my.cnf
Add the below code block at the bottom of the file;

[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
If you don’t know how to use VIM:

Once the file is open, hit “i” key to start editing the file.
After you’re done editing the file hit “Esc + :wq” to save the file
3.3 Restart the MySQL server (for the config to take effect)

sudo service mysql restart
:four: Instal CURL, Node, NPM and Yarn
4.1 Install CURL

sudo apt install curl
4.2 Install Node

curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash

source ~/.profile

nvm install 16.15.0
4.3 Install NPM

sudo apt-get install npm
4.4 Install Yarn

sudo npm install -g yarn
Check if Node is correctly installed by running node --version

:five: INSTALL FRAPPE BENCH
5.1 Install Frappe Bench

sudo pip3 install frappe-bench
Check if Frappe Bench is correctly installed by running bench --version

5.2 Initialize Frappe Bench

bench init --frappe-branch version-14 frappe-bench
5.3 Go to Frappe Bench directory
This will be the main directory from where we will be running all the commands.
The full path of this directory will be: /home/[frappe-user]/frappe-bench/

cd frappe-bench/
5.4 Change user directory permissions
This will allow execution permission to the home directory of the frappe user we created in step 1.4

chmod -R o+rx /home/[frappe-user]/
5.5 Create a New Site
We will use this as the default site where ERPNext and other apps will be installed.

bench new-site site1.local
:six: Install ERPNext and other Apps
Finally, we’re at the last stage of the installation process!

6.1 Download the necessary apps to our server
Download the payments apps . This app is required during ERPNext installation

bench get-app payments
Download the main ERPNext app

bench get-app --branch version-14 erpnext
Download the HR & Payroll app (optional)

bench get-app hrms
Download the ecommerce integrations apps (optional)

bench get-app ecommerce_integrations --branch main
Check if all the apps are correctly downloaded by running bench version --format table

6.2 Install all the Apps

Install the main ERPNext app

bench --site site1.local install-app erpnext
Install the HR & Payroll app (optional)

bench --site site1.local install-app hrms
Install the ecommerce integrations apps (optional)

bench --site site1.local install-app ecommerce_integrations
:seven: SETUP PRODUCTION SERVER
7.1 Enable scheduler service

bench --site site1.local enable-scheduler
7.2 Disable maintenance mode

bench --site site1.local set-maintenance-mode off
7.3 Setup production config

sudo bench setup production [frappe-user]
7.4 Setup NGINX web server

bench setup nginx
7.5 Final server setup

sudo supervisorctl restart all
sudo bench setup production [frappe-user]
When prompted to save new/existing config files, hit “Y”

:tada: Ready to Go!
You can now go to your server [IP-address]:80 and you will have a fresh new installation of ERPNext ready to be configured!

If you are facing any issues with the ports, make sure to enable all the necessary ports on your firewall using the below commands;

ufw allow 22,25,143,80,443,3306,3022,8000/tcp
ufw enable
Next, I will continue to update this guide to help you setup a custom domain and install SSL certificate.

In case you get stuck during any step of this process or have any doubts, feel free to drop your messages below and I’ll try my best to answer.