## Project Title
### Linux Server Configuration
This project is a baseline installation of a Linux server and prepare it to host the web applications. This server is configured with a database server, made secure from number of attacks and will deploy is a web application i.e. the existing project *Item Catalog*.

### Pre-requesites
1. IP Address : 52.15.106.147
2. SSH Port : 2200
3. URL for the application : http://ec2-52-15-106-147.us-east-2.compute.amazonaws.com/

### Step-By-Step Server configuration and Referrences
1. Create a new Ubuntu Linux server instance using *Amazon Lightsail*   **Referrence** : [Amazon Lightsail](https://lightsail.aws.amazon.com/ls/webapp/home/)    

2. Follow the instructions provided to SSH into your server.  
**Referrence** : [Get started on Lightsail step by step guidance by Udacity](https://classroom.udacity.com/nanodegrees/nd004/parts/ab002e9a-b26c-43a4-8460-dc4c4b11c379/modules/357367901175462/lessons/3573679011239847/concepts/c4cbd3f2-9adb-45d4-8eaf-b5fc89cc606e)

3. Update all currently installed packages on a server. Use the following commands.  
`sudo apt-get update`  
`sudo apt-get upgrade`  
`sudo apt-get dist-upgrade`     
**Referrence** :  [How to install updates](https://askubuntu.com/questions/196768/how-to-install-updates-via-command-line)

4. Change the SSH port from 22 to 2200.   
a. Use command `Sudo nano /etc/ssh/sshd_config` to change the port number as desired.    
Also in the same file you can change values for following variables:
`Permitrootlogin no` to disable access to root login 
and `Passwordauthentication no` to allow only key-based authentication.  
b. Make sure to configure the Lightsail firewall to allow it.  
Eanble ports 2200, 80, 123 in Amazon Lightsail Firewall settings.    
**Referrences** :  
(1) [Disable SSH root login](https://www.howtogeek.com/howto/linux/security-tip-disable-root-ssh-login-on-linux/)  
(2) [SSH Disable password authentication](https://stackoverflow.com/questions/20898384/ssh-disable-password-authentication)  
(3) [Firewall settings for Amazon Lightsail instance](https://aws.amazon.com/premiumsupport/knowledge-center/connect-http-https-ec2/)   

5. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).    
Use the following commands:    
*To check the status*    
`Sudo ufw status`       
*Default settings for incoming traffic and outgoing traffic*  
`Sudo ufw default deny incoming`, `Sudo ufw default allow outgoing`   
*Allow firewall for desired ports*  
`Sudo ufw allow ssh`,`Sudo ufw allow 2200/tcp`,  
`Sudo ufw allow http`,`Sudo ufw allow ntp`  
*To eanble the firewall settings*  
`Sudo ufw enable`  
**Referrence** : [Manage Firewall on Ubuntu](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)

6. Create a new user account named **grader**.  
With this command `sudo adduser grader` a new user **grader** can be created.
It will initially ask for password and basic information *eg. Full Name*  
**Referrence** : [Add user in Ubuntu](http://mixeduperic.com/ubuntu/how-to-add-a-new-user-in-ubuntu-using-the-command-line.html)


7. Give grader the permission to sudo.  
   In ubuntu, you can copy and edit the user's file in *sudoers* directory using following commands.  
`sudo /etc/sudoers.d/existing_user_name /etc/sudoers.d/grader`  
`sudo nano /etc/sudoers.d/grader`   
Make changes to *username* field as grader. Save the file and exit.  
**Referrence** : [How to add user and grat sudo priviledges to it](https://www.digitalocean.com/community/tutorials/how-to-add-delete-and-grant-sudo-privileges-to-users-on-a-debian-vps)

8. Create an SSH key pair for grader using the ssh-keygen tool.  
Use command `ssh key-gen` on client machine.   
It will ask for file to save the key value. *eg. ~/.ssh/grader_rsa*.   
It will create few files and save the details.  
Use `cat ~/.ssh/grader_rsa.pub` and copy these contents in order to save further.  
After generating a key, switch to *grader* user and use following commands   
*Create directory ssh if dirctory is not there*  
`mkdir .ssh`  
*Create and edit the authorized_keys file*
`touch .ssh/authorized_keys`  
`nano .ssh/authorized_keys` and paste the above copied contents in it.    
Use `chmod 700 .ssh` and `chmod 644 .ssh/authorized_keys` for user priviledges.  
On client machine you can use following to logged in with *grader*  
`ssh grader@52.15.106.147 -p 2200 -i ~/.ssh/grader_rsa`  
**Referrences** :  
(1) [How to set up SSH keys](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)  
(2) [Linux Chmod Commands](https://www.linuxtopia.org/online_books/introduction_to_linux/linux_The_chmod_command.html)  

9. Configure the local timezone to UTC.  
Use command `sudo timedatectl set-timezone Etc/UTC`  
**Referrence** : [How to change timezone on ubuntu](https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt)

10. Install and configure Apache to serve a Python mod_wsgi application.   
For apache2 server use command `sudo apt-get install apache2`  
If you built your project with Python 3, you will need to install the Python 3 mod_wsgi package on your server: `sudo apt-get install libapache2-mod-wsgi-py3`.  

11. Install and configure PostgreSQL  
For postgresql use command: `sudo apt-get install postgresql`  
a. Do not allow remote connections  
Use command `sudo nano /etc/postgresql/9.3/main/pg_hba.conf` to edit the file.   
Add the following contents int it, save and exit the file.
`#local   all   all   peer`   
`local   all   all   md5`  
b. Create a new database user named **catalog** that has limited permissions to your catalog application database.  
Use following commands.  
*to start editing in psql mode with postgres user*  
`sudo -i -u postgres psql` 
*To create a user*
`CREATE ROLE catalog WITH LOGIN PASSWORD '*****';` 
*To add roles to user*  
`Alter catalog WITH CREATEDB`  
*To list out the users and their roles*
`\du`   
Once you are done, exit the psql.  
c. Use command `sudo service postgresql restart` to restart the postgresql  
d. After these setting, it is reccomended to restart the *apache server*  
Use command `Sudo service apache2 restart`  
You can check status for apache2 using this command `sudo systemctl status apache2`  
**Referrences** :  
(1) [Install postgresql on Ubuntu and create role](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04)    
(2) [Postgres User Roles](http://arnulf.us/PostgreSQL_Permissions_and_Roles)  
(2) [Secure postgreql](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-against-automated-attacks)  
(3) [Apache2 server start,stop,restart](https://www.cyberciti.biz/faq/star-stop-restart-apache2-webserver/)  

12. Install git.  
Use command `Sudo apt-get install git` to install git on Ubuntu  
You can oncfigure it by providing basic information such as username, email.  
`git config --global user.name "Example User"`  
`git config --global user.email email@example.com`  
**Referrence** : [Git Install and basic setup](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-14-04)

13. Clone and setup your project from the Github repository.  
Here we are using *Item-Catalog-Project* from GitHub repository.  
a. Enable mod_wsgi.  
Use commands `sudo apt-get install libapache2-mod-wsgi python-dev` to serve Python
and `sudo a2enmod wsgi` to enable it.  
b. Deploy Flask.  
For that let's first create a flask appication.  
(1) We will place our app in the **/var/www** directory.  
(2) Use the following command to move to the /var/www directory  
`cd /var/www`  
(3) Create the initial directory **catalog** by giving following command   
`sudo mkdir catalog`  
(4) cd catalog  
(5) Create another directory **catalog** by giving following command  
`sudo mkdir catalog`  
(6) Then, move inside this directory and create two subdirectories named **static** and **templates** using the following commands  
`cd catalog`  
`sudo mkdir static templates`  
(7) Create the `__init__.py` file that will contain the flask application logic.  
`sudo nano __init__.py `  
Add following to this file  
`from flask import Flask  
app = Flask(__name__)  
@app.route("/")  
def hello():  
    return "Hello!"  
if __name__ == "__main__":  
    app.run()`  
save and close the file.  
This logic in __init__.py is just for a testing purpose.  
(8) In this step, we will create a virtual environment for our flask application.
We will use pip to install virtualenv and Flask. If pip is not installed, install it on Ubuntu through apt-get.  
`sudo apt-get install python-pip `  
If virtualenv is not installed, use pip to install it using following command  
`sudo pip install virtualenv `  
Give the following command (where venv is the name you would like to give your temporary environment)  
`sudo virtualenv venv`  
(9) Now, install Flask in that environment by activating the virtual environment with the following command  
`source venv/bin/activate`  
Give this command to install Flask inside  
`sudo pip install Flask`   
Next, run the following command to test if the installation is successful and the app is running  
`sudo python __init__.py `  
It should display “Running on http:....” or "Running on http://127.0.0.1:5000/". If you see this message, you have successfully configured the app.  
(10) To deactivate the environment, give the following command
`deactivate`    
(11) Configure and Enable a New Virtual Host  
Issue the following command in your terminal  
`sudo nano /etc/apache2/sites-available/catalog`  
NOTE: Newer versions of Ubuntu (13.10+) require a ".conf" extension for VirtualHost files -- run the following command instead  
`sudo nano /etc/apache2/sites-available/catalog.conf`  
Add the following lines of code to the file to configure the virtual host. Be sure to change the ServerName to your domain or cloud server's IP address:    
`<VirtualHost *:80>    
		ServerName mywebsite.com    
		ServerAdmin admin@mywebsite.com    
		WSGIScriptAlias / /var/www/catalog/catalog.wsgi    
		<Directory /var/www/catalog/catalog/>    
			Order allow,deny    
			Allow from all    
		</Directory>    
		Alias /static /var/www/catalog/catalog/static    
		<Directory /var/www/catalog/catalog/static/>    
			Order allow,deny    
			Allow from all    
		</Directory>    
		ErrorLog ${APACHE_LOG_DIR}/error.log    
		LogLevel warn    
		CustomLog ${APACHE_LOG_DIR}/access.log combined    
</VirtualHost>`      
Save and close the file.  
Get the hostname for your IP at [hcidata](http://www.hcidata.info/host2ip.cgi)
Update host name as Server Alias and IP address as Server name in catalog.conf file.  
(12) Enable the virtual host with the following command  
`sudo a2ensite catalog`  
(13) Create the .wsgi File  
Apache uses the .wsgi file to serve the Flask app. Move to the /var/www/FlaskApp directory and create a file named catalog.wsgi with following commands  
`cd /var/www/catalog`  
`sudo nano catalog.wsgi`  
Add the following lines of code to the catalog.wsgi file  
`#!/usr/bin/python  
import sys  
import logging  
logging.basicConfig(stream=sys.stderr)  
sys.path.insert(0,"/var/www/catalog/")`  
`from FlaskApp import app as application`  
`application.secret_key = 'Add your secret key'`  
(14) Restart Apache with the following command to apply the changes  
    `sudo service apache2 restart `  
You have successfully deployed a flask application  
c. Clone the project into this flask application.  
Use command `sudo git clone https://github.com/.../Item-Catalog-Project.git /var/Item-Catalog` to clone.  
Move the contents of the project folder to our created catalog directory   
`sudo mv -v /var/Item-Catalog/*  /var/www/catalog`  
**Referrences** : 
(1) [Deploy a Flask app](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)  
(2) [Clone a git repo on Ubuntu](https://stackoverflow.com/questions/651038/how-do-you-clone-a-git-repository-into-a-specific-folder)  
(3) [Move git directory contents to other folder](https://stackoverflow.com/questions/3900805/git-command-to-move-a-folder-inside-another)    
(4) [hcidata to get a hostname](http://www.hcidata.info/host2ip.cgi)  

14. Set it up in your server so that it functions correctly when visiting your server’s IP address in a browser. Make sure that your .git directory is not publicly accessible via a browser!  
To do this make a **.htaccess** file in /var/www/catalog.  
Paste the content - `RedirectMatch 404 /\.git` in this file and save it .  
You can delete unwanted files in your project folder (for example - readme, vagrant folder etc).  
**Referrence** :  [Prevent apache from serving the git directory](https://serverfault.com/questions/128069/how-do-i-prevent-apache-from-serving-the-git-directory)

15. Install other required packagaes to run this project.  
(1)`sudo apt-get install python-pip`  
(2) `pip install httplib2`  
(3) `pip install requests`  
(4) `sudo pip install sqlalchemy`  
(5) `pip install Flask-SQLAlchemy`  
(6) `sudo pip install --upgrade oauth2client`  

16. Run the project.  
First let's restart Apache server : `sudo service apache2 restart`  
(1) Update the postgresql database connection string in python files i.e. application.py, dance_data.py, database_setup.py .Make changes for `engine` values as per the following:  
`engine = create_engine('postgresql://catalog:passwd@localhost/catalog')`  
(2) Open python mode using `python`
Run `python database_setup.py` to create a database for our catalog user.   
Run `python dance_data.py` to fill up the initial data in the created database.    
(3) Remove file __init__.py.Use following command.  
`rm /var/www/catalog/catalog/__init__.py`  
Rename application.py to __init__.py  
`mv /var/www/catalog/catalog/application.py /var/www/catalog/catalog/__init__.py`  
(4) Enable the virtual host : `sudo a2ensite catalog`  
Restart the apacheserver : `sudo service apache2 restart`  
(4) We have to update client_secrets.json and fb_client_secrets.json files too.  
(a) For Google Authentication:  
Go to [Google Developers Console](https://console.developers.google.com/)  
Click on Credentails and edit them add you hostname and public IP address (http://.....) to Authorised JavaScript origins.  
Also add hostname (http://..hostname.../) to Authorised redirect URIs.    
(b) For Facebook authentication:  
Go to [Facebook Developer Console](https://developers.facebook.com/)  
Go to your app  
Go to facebook login and open settings  
Update the valid OAuth redirect URIs with our hostname and url.  
Update the fb_client_secrets.json file from terminal also.    
Make sure for the updated values in both the client_secrets.json and fb_client_secrets.json files.  
Remove unwanted files from project folder. *eg. vagrant, readme file*  
Restart Apache server : `sudo service apache2 restart` 
(4) You can enter URL from browser to run the project.  
**Referrences** :   
(1) [Remove Files in Ubuntu](https://www.ibm.com/support/knowledgecenter/en/ssw_aix_72/com.ibm.aix.osdevice/HT_cmd_del_files.htm)  
(2) [Google Developers Console](https://console.developers.google.com/)  
(3) [Facebook Developer Console](https://developers.facebook.com/)  
(4) [Udacity Discussion Forums](https://discussions.udacity.com/search?q=linux%20server%20configuration%20google%20oauth%20redirect%20URI)  
(5) [Raw IP address for Google OAuth](https://stackoverflow.com/questions/14238665/can-a-public-ip-address-be-used-as-google-oauth-redirect-uri)








