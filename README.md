# Udacity-linux-server-configuration
## Description 
Goals of the project
>You will take a baseline installation of a Linux distribution on a virtual machine and prepare it to host your web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.
## User information
Public URL: [54.244.17.173](54.244.17.173)
Port: 2200
Application URL: [ec2-54-244-17-173.us-west-2.compute.amazonaws.com](ec2-54-244-17-173.us-west-2.compute.amazonaws.com)
## Steps
1. Create a new user grader and give it sudo permission
`sudo adduser grader`
`sudo adduser grader sudo`
2. Update packages
`sudo apt-get update`
`sudo apt-get upgrade`
3. Add public key authentication
   - In the terminal of local machine, generate rsa key pair, and save it to the file 'udacity_key'
   `ssh-keygen -f ~/.ssh/udacity_keys`
   - Display the key 
   `cat ~/.ssh/udacity_key.rsa.pub`
   - Connect the remote terminal and copy the key to the file 
   `sudo nano /home/grader/.ssh/authorized_keys`
   - Change permission to the file
   `chomd 600 /home/grader/.ssh/authorized_keys`
   - Change the owner from root to grader
   `sudo chown -R grader:grader /home/grader/.ssh`
4. Enforce the key authentication 
   - Edit the file `sudo nano /etc/ssh/sshd_config`
   - Change the PasswordAuthentication to no
   - retart `sudo service ssh restart`
5. Change the ssh port from 22 to 2200
   - Edit the file `sudo nano /etc/ssh/sshd_config`
   - Change Port line from 22 to 2200
   - Restart `sudo service ssh restart`
   - Try login to the remote VM by the command
     `ssh -i ~/.ssh/udacity_key.rsa.pub grader@54.244.17.173 -p 2200`
6. Disable ssh root access
   - Edit the file `sudo nano /etc/ssh/sshd_config`
   - Set PermitRootLogin to no
   - Restart `sudo service ssh restart`
7. Configure the Uncomplicated Firewall (UFW)
   ```
   sudo ufw allow ssh
   sudo ufw deny 22
   sudo ufw allow 2200/tcp
   sudo ufw allow 80/tcp
   sudo ufw allow 123/udp
   sudo ufw enable
   ```
8. Install Apache and mod_wsgi
   - Install Apache 
   `sudo apt-get install apache2`
   - Install mod_wsgi, which is an Apache HTTP server mod that enables Apache to serve Flask applications.
   `sudo apt-get install libapache2-mod-wsgi python-dev`
   - Enable mod_wsgi
   `sudo a2enmod wsgi`
   - Start Apache
   `sudo service apache2 start`
9. Install Git and clone project
   - `sudo apt-get install git`
   - Create a new folder named Catalog
    ```
    sudo mkdir /var/www/Catalog
    ```
   - Set the onwership of all files and directories
    `sudo chown -R grader:grader Catalog`
   - Clone the project to Catalog
    `cd /var/www/Catalog`
    `git clone https://github.com/anqijing/udacity-catlog.git Catalog`
   - rename the main file app.py to __init__.py
    `cd Catalog`
    `sudo mv app.py __init__.py`
10. Install virtual environment, Flask and project's required packages.
    -```
     sudo apt-get install python-pip
     sudo pip install virtualenv
     ```
    - Create a virtual enviroment in the folder `cd /var/www/Catalog`
     `sudo virtualenv venv`
    - Activate it
     `source venv/bin/activate`
    - Change the permission to the virtual environment folder
     `sudo chmod -R 777 venv`
    - Install Flask
     `pip install Flask`
    - Install other required packages
      Notice: Don't use sudo with pip.
11. Confugre and enable a new virtual host
    - Add the codes to the file `sudo nano /etc/apache2/sites-available/Catalog.conf`
     ```
     <VirtualHost *:80>
          ServerName 54.244.17.173
          ServerAdmin admin@example.com
          WSGIScriptAlias / /var/www/Catalog/catalog.wsgi
          <Directory /var/www/Catalog/Catalog/>
              Order allow,deny
              Allow from all
          </Directory>
          Alias /static /var/www/Catalog/Catalog/static
          <Directory /var/www/Catalog/Catalog/static/>
              Order allow,deny
              Allow from all
          </Directory>
          ErrorLog ${APACHE_LOG_DIR}/error.log
          LogLevel warn
          CustomLog ${APACHE_LOG_DIR}/access.log combined
  	</VirtualHost>
     ```
     - Enable the virtual host
     `sudo a2ensite Catalog`
12. Create .wsgi file
    - `cd /var/www/Catalog`
    - `sudo nano catalog.wsig`
    - Add the following codes to the file
     ```
     import sys
     import logging
     logging.basicConfig(stream=sys.stderr)
     sys.path.insert(0,"/var/www/Catalog/")
     
     activate_this='/var/www/Catalog/venv/bin/activate_this.py'
     execfile(activate_this, dict(__file__=activate_this))
     
     from Catalog import app as application
     application.secret_key = 'Add_your_secret_key'
     ```
     - Restart apache2
      `sudo service restart apache2`
13. Install and configure PostgreSQL
    - `sudo apt-get install libpq-dev`
      `sudo apt-get install postgresql postgresql-contrib`
    - Change the user 
      `sudo su - postgres`
    - Connect to the database with `psql`
    - Create a new user 'catalog' with the password
      `CREATE USER catalog WITH PASSWORD 'password';`
    - Create a new database 'catalog' owned by user 'catalog':
      `CREATE DATABASE catalog WITH OWNER catalog;`
    - Connect to the database `\c catalog` and revoke rights `REVOKE ALL ON SCHEMA public FROM public;`
    - Log out `\q` and exit
    - Notice: Inside the Catalog project, change the database connection
      `engine = create_engie('postgresql://catalog:password@localhost/catalog')`
14. Update Oauth authorized JavaScript origins
15. Restart Apache and launch the app
   - `sudo service apache2 restart`
     
    

  
