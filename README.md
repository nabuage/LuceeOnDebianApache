# LuceeOnDebianApache
Lucee development environment on Debian using Apache.

Versions:
- Debian 10
- Apache 2
- Lucee 5.3.8.189

**Apache Installation**

Become a superuser
```
su
```

Add webadmin user
```
sudo useradd webadmin
```

Install Apache
```
apt-get install apache2
```

Start Apache service
```
systemctl start apache2
```

Create development folder
```
mkdir /home/username/www
mkdir /home/username/www/luceeapp
```

Create symlink of development folder inside default Apache folder to simplify user permission.
```
cd /var/www/html/
ln -s /home/username/www/luceeapp luceeapp.symlink
```

Copy default Apache configuration as base for new site http://luceeapp/
```
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/luceeapp.conf
```

Edit /etc/apache2/sites-available/luceeapp.conf
```
nano /etc/apache2/sites-available/luceeapp.conf
```

Set the following values
```
ServerName luceeapp
DocumentRoot /var/www/html/luceeapp.symlink
ErrorLog ${APACHE_LOG_DIR}/error.luceeapp.log
CustomLog ${APACHE_LOG_DIR}/access.luceeapp.log combined
```
Note: `Save from nano: CTRL+SHIFT+o`
Note: `Exit from nano: CTRL+x`

Enable the new site
```
sudo a2dissite 000-default.conf
sudo a2ensite luceeapp
systemctl reload apache2
```

Point luceeapp hostname to localhost
```
nano /etc/hosts
```
Add new entry
```
127.0.0.1 luceeapp
```
Note: `Save from nano: CTRL+SHIFT+o`
Note: `Exit from nano: CTRL+x`

Apache installation and setup is complete. Enter http://luceeapp/ in your browser. It should display content from /home/username/www/luceeapp folder.

**Lucee Installation**

Download Lucee Linux (64b) Installer at `https://download.lucee.org/`.

Run the installer
```
chmod +x /home/username/Downloads/lucee-5.3.8.189-linux-x64-installer.run
cd /home/username/Downloads
./lucee-5.3.8.189-linux-x64-installer.run
```

Installer interactions:
- Press [Enter] to continue:
- Do you accept this license? [y/n] y
- Installation Directory [/opt/lucee]: /opt/lucee
- Lucee Password:
- Lucee Password (confirm):
- Minimum (mb) [256]: 256
- Maximum (mb) [512]: 512
- Tomcat System User [root]: webadmin
- Tomcat Web Server Port: [8888]: 8888
- Tomcat Shutdown Port: [8005]: 8005
- Tomcat AJP Port: [8009]: 8009
- Yes, Start Lucee at Boot Time [Y/n]: n
- Yes, Install Apache Connector [Y/n]: y
- Yes, Install mod_cfml [Y/n]: y
- Apache Control Script Location [/usr/sbin/apachectl]: /usr/sbin/apachectl
- Apache Modules Directory [/usr/lib/apache2/modules]: /usr/apache2/modules
- Apache Configuration File [/etc/apache2/apache2.conf]: /et/apache2/apache2.conf
- Apache Logs Directory [/var/logs/apache2]: /var/logs/apache2
- Do you want to continue? [Y/n] y

Since I did not want to start Lucee during boot time, I have to start Lucee server manually
```
/opt/lucee/lucee_ctl start
```

Change development directory permission
```
chown -R username:webadmin /home/username/www/luceeapp/
```

Lucee installation is complete. Enter http://luceeapp/ in your browser. It should display ColdFusion content from /home/username/www/luceeapp folder.

**Use mod_jk instead of mod_cfml**

During Lucee installation, answer `n` to `Yes, Install mod_cfml [Y/n]`.

Install mod_jk
```
apt-get install libapache2-mod-jk
```

Edit /etc/libapache2-mod-jk/httpd-jk.conf
```
nano nano /etc/libapache2-mod-jk/httpd-jk.conf
```

Add the following before the closing IfModule tag
```
JkMount /*.cfm ajp13_worker
JkMountCopy all
```
Note: `Save from nano: CTRL+SHIFT+o`
Note: `Exit from nano: CTRL+x`

Edit /etc/libapache2-mod-jk/workers.properties
```
nano /etc/libapache2-mod-jk/workers.properties
```

Set the following values
```
workers.tomcat_home=/opt/lucee/tomcat
```
Note: `Save from nano: CTRL+SHIFT+o`
Note: `Exit from nano: CTRL+x`

Edit /opt/lucee/tomcat/conf/server.xml
```
nano /opt/lucee/tomcat/conf/server.xml
```

Set the following values
```
<Connector protocol="AJP/1.3" port="8009" secretRequired="false" address="::1" redirectPort="8443" />
<Host name="luceeapp" appBase="webapps">
 <Context path="" docBase="/home/username/www/luceeapp" />
</Host>
```
Note: `Save from nano: CTRL+SHIFT+o`
Note: `Exit from nano: CTRL+x`

Restart Apache and Lucee
```
systemctl restart apache2
/opt/lucee/lucee_ctl restart
```

MOD_JK setup is complete.
