# Item-Catalog-Deployment

This folder contains the necessary details about how Item-Catalog website is deployed and hosted on AWS Lightsail Instance using Apache web server.

## Steps

1. IP address of the Linux instance 54.214.194.122
2. SSH port 2200
3. Application Url http://54.214.194.122.xip.io:8000/
4. Softwares Installed
    * git
    * apache2
    * postgresql
    * Python pip
    * Python Flask
    * Python sqlalchemy
    * Python requests
    * Python oauth2client
    * Python psycopg2
    * libapache2-mod-wsgi
 5. Configuration Changes
    * Changed the SSH port from 22 to 2200. Updated UFW firewalls to allow incoming only from ports 2200, 80, 8000, 8080 and 123.
    * Configured the local timezone on the linux instance to UTC.
    * Configured Flask application code to run the app on the IP address of the linux instance (127.0.0.1) at port: 8000
    * created  itemcatalog.wsgi file as an interface between apache2 and flask app.
    * Updated google api credentials to accept the new URL for the authentication. Google only accepts DNS in place of call back url. xip.io is a magic domain name that provides wild card DNS for any IP address.
    * updated client_secrets.json file with the new redirect_uri and new javascript origins.
    * Installed Postgres database and created database called itemcatalog.
    * created database user "catalog" with necessary permissions on itemcatalog db.
    * created database schema, tables and populated data.
    * created a user "grader" with sudo permissions to root.
    * ssh key for user grader is located in /home/grader/.ssh/authorized_keys
    * private key for the user will be provided during the submission.
    
