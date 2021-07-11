# LuceeOnDebianApache
Lucee development environment on Debian using Apache.

Versions:
- Debian 10
- Apache 2

Apache Installation

#Add webadmin user
sudo groupadd webadmin
sudo useradd webadmin
sudo usermod -a -G webadmin username

#Install Apache
apt-get install apache2

#Start Apache service
systemctl start apache2

#Create development folder
mkdir /home/username/www
mkdir /home/username/www/luceeapp

#Create symlink of development folder inside default Apache folder to simplify user permission.
cd /var/www/html/
ln -s /home/username/www/luceeapp luceeapp.symlink

#Copy default Apache configuration as base for new site http://luceeapp/
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/luceeapp.conf

#Edit /etc/apache2/sites-available/luceeapp.conf
nano /etc/apache2/sites-available/luceeapp.conf
#Set the following values
-------------------------
ServerName luceeapp
DocumentRoot /var/www/html/luceeapp.symlink
ErrorLog ${APACHE_LOG_DIR}/error.luceeapp.log
CustomLog ${APACHE_LOG_DIR}/access.luceeapp.log combined
-------------------------
#Save from nano: CTRL+SHIFT+o
#Exit from nano: CTRL-x

#Enable the new site
sudo a2dissite 000-default.conf
sudo a2ensite luceeapp
systemctl reload apache2

#Point luceeapp hostname to localhost
nano /etc/hosts
#Add new entry
127.0.0.1 luceeapp
#Save from nano: CTRL+SHIFT+o
#Exit from nano: CTRL-x

#Installation is complete.
#Enter http://luceeapp/ in your browser.
#It should display content from /home/username/www/luceeapp folder.
