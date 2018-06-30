# Item-Catalog-Deployment

This folder contains the necessary details about how Item-Catalog website is deployed and hosted on AWS Lightsail Instance using Apache web server.

## Configuration Steps

1. IP address of the Linux instance 54.214.194.122
2. SSH port 2200
3. Application Url http://54.214.194.122.xip.io:80/
4. Update all currently installed packages
    * ```sudo apt-get update```
    * ```sudo apt-get upgrade```
    * ```sudo apt-get dist-upgrade```
5. Set up automatic security updates.
    * install ```sudo apt-get install unattended-upgrades```
    * enable it by ```sudo dpkg-reconfigure --priority=low unattended-upgrades```
    * display file contents to verify the upgrade setup.
        * ```/etc/apt/apt.conf.d/20auto-upgrades```
        * ```   APT::Periodic::Update-Package-Lists "1";
                APT::Periodic::Unattended-Upgrade "1"; ```
    * current configuration can be queired using
        * ```apt-config dump APT::Periodic::Unattended-Upgrade```
    * Refer below link for more info on setting up automatic updates.
        * ```https://help.ubuntu.com/community/AutomaticSecurityUpdates```
6. Configure Uncomplicated Firewall
    *  display current status of UFW firewall.
        * ```sudo ufw status verbose```
        * set ufw to deny all incoming by default.
            * ```sudo ufw default deny incoming```
        * set ufw to allow all outgoing by default.
            * ```sudo ufw default allow outgoing```
        * configure ports in ufw
            * ```sudo ufw allow ssh```
                * This will create firewall rules that will allow all connections on port 22, which is the port that the SSH daemon listens on by default
            * ```sudo ufw allow 2200```
            * ```sudo ufw deny 22```
            * ```sudo ufw allow 80```
            * ```sudo ufw allow 123```
        * ```sudo ufw enable```
        * Refer below link for more info on configuring UFW
                * ```https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-16-04```
7. Update the AWS lightsail instace firewall rules. create a custom application protocol that accepts port 2200.
8. Change the SSH port from default 22 to 2200
    * ```sudo vi /etc/ssh/sshd_config```
    * locate the line ```# Port 22```. Change 22 to 2200
    * ```sudo service ssh restart```
9. Create user named "grader"
    * ```sudo adduser grader```
    * add the user to the sudo group
        * ```usermod -aG sudo grader```
    * provide sudo access
        * ```sudo visudo -f /etc/sudoers```
        * add the following line at the end of file.
        * ```usernameusedforlogin ALL=(ALL) NOPASSWD:ALL```
        * ```esc :wq!```
    * generate key for password less access
        * ``` ssh-keygen``` on your local machine
        * This will generate one public key and private key.Public key will be copied to the server and private key will be used access the server.
        * Login to linux instance using grader.
            * ```cd ~/```
            * ```mkdir .ssh```
            * ```touch authorized_keys```
            * ```nano authorized_keys```
            * copy the public key to the authorized_keys file and save.
            * ```chmod 700 .ssh```
            * ```chmod 644 .ssh/authorized_keys```
            * force key based authentication
                * ``` sudo nano /etc/ssh/sshd_config```
                * update ```PasswordAuthentication no```
                * ```sudo service ssh restart```
            * login to grader from local machine using the private key generated.
                * ```ssh -i ~/.ssh/grader grader@54.214.194.122 -p 2200 -v```
10. Disable remote root login
    * ```sudo nano /etc/ssh/sshd_config```
    * update ```PermitRootLogin no ```
11. Install and setup PostgreSQL
    * ```sudo apt-get install postgresql```
    * ```sudo -u postgres psql```
    * ```sudo -u postgres createuser catalog```
    * ```create database itemcatalog;```
    * ```grant all privileges on database itemcatalog to catalog;```
12. Install Git
    * ```apt-get install git-core```
    * clone the application folder from github to ```/var/www/Item-Catalog```
13. Install and configure apache.
    * ```sudo apt-get install apache2```
    * ```sudo nano /etc/apache2/ports.conf``` make sure listening port is 80
    * ```sudo service apache2 restart```
14. Install required python packages for application to run
    * ```sudo apt-get pip```
    * ```sudo pip install Flask```
    * ```sudo pip install flask-sqlalchemy```
    * ```sudo pip install sqlalchemy```
    * ```sudo pip install requests```
    * ```sudo pip install oauth2```
    * ```sudo pip install psycopg2```
15. Configured the local timezone on the linux instance to UTC.
    * ```sudo timedatectl set-timezone Etc/UTC```
16. Update OAuth credentials for the registered app at google developer console.
    * ```https://console.developers.google.com/apis```
    * update Authorized Javascript origin to accept the ip address of the server
        * ```http://54.214.194.122.xip.io```
    * update the client_secrets.json file accordingly.
17. create itemcatalog.wsgi file under ```/var/www/Item-Catalog/```
18. Update the following to set up wsgi for apache
    * ```sudo nano /etc/apache2/sites-available/000-default.conf```
    * ```VirtualHost *:80```
    * ```DocumentRoot /var/www/Item-Catalog/```
    * ```WSGIScriptAlias / /var/www/Item-Catalog/itemcatalog.wsgi```
19. Restart Apache
    * ```sudo /etc/init.d/apache2 restart```
    * to check the error log
        * ```sudo tail /var/log/apache2/error.log```
