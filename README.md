# High-availability-of-Website
for provide high availability of wordpress website and share it using nfs server
## Create two ec2-instances on aws cloud and install all dependenciesa regarding wordpress setup.
      In this setup we need to install firstly a apache webserver for hosting our website and its modules after this we need to install php for providing coneection and after this create a RDS mysql db for database and after this download wordpress file and setup database details in it's configuration.
```
sudo apt-get update ;
## Step-1:- Install Apache
sudo apt install apache2 ;
sudo systemctl enable apache2 ;
sudo systemctl status apache2 ;
## step-2:- Configure RDS database in AWS
## Step-3:- Install PHP
sudo apt install php libapache2-mod-php php-mysql ;
sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip ;
php -v ;
## Step-4:- Install WordPress
cd /var/www/html
sudo wget -c http://wordpress.org/latest.tar.gz
sudo tar -xzvf latest.tar.gz
sudo chown -R www-data:www-data /var/www/html/wordpress
sudo chmod -R 777 wordpress/
cd wordpress/
## Step-4:- Setup and Configure WordPress
sudo mv wp-config-sample.php wp-config.php
sudo vim wp-config.php
```
And after this edit the details of database wordpress, mysql database username, mysql database password and in mysql hostname we given the RDS url in wp-config.php files.
Once you have done this, you can access your WordPress page to finish the installation. Open the browser and go to https://localhost or instance-ip/wordpress/
The next screen will open. Click on Continue to select the language.
Click on “Install WordPress” to enter your preferred information, including site title, username, and password.
WordPress will now be installed successfully. You can log in to your admin dashboard with the previously set up information.
To log in, enter your username and password.
After successfully logging in, the WordPress dashboard page will greet you.

## Now, using nfs we share the wordpress directory to second instance
In this we need to install apache web-server and php and its module and the wordpress files and configure 'wp-config.php' file. we don't need to installation process of wordpress.
setup of nfs server for file share:-
```
## install nfs server on first instance
sudo apt install nfs-kernel-server
## Configuring the NFS Exports on first instance
sudo vim /etc/exports
/var/www/html    client_ip(rw,sync,no_subtree_check)  # enter input in exports file
## restart the nfs-server
sudo systemctl restart nfs-kernel-server
## Creating Mount Points and Mounting Directories on the second instance
sudo mkdir -p /nfs/mnt
sudo mount host_ip:/var/www/html /nfs/mnt
## after this we need to add this mount point to the both instances 'wp-config.php' file.
sudo vim wp-config.php
define('WP_CONTENT_DIR', '/nfs/mnt/wordpress/wp-content');
```
After this Open the browser and go to https://localhost or instance-ip/wordpress/
You can log in to your admin dashboard with the previously set up information.
To log in, enter your username and password.
After successfully logging in, the WordPress dashboard page will greet you.
 
## Setup a loadbalancer
In this we create an application loadbalancer where we create a target group also and attach to both instance to this target group.
After sucessfully created loadbalancer we open the web browser and using the loadbalancer DNS name to seen our wordpress website are running on both instances.

## Setup an auto-scaling
In this firstly we create a template on our aws where we mention all the dependencies regarding the wordpress setup.
and after this we use this template to create auto scaling group and attach this to laodbalncer which is created previously.
