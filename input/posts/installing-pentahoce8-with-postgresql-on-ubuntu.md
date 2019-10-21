---
Title: Installing Pentaho CE 8.0 with PostgreSQL on Ubuntu Server 16.04 LTS
Published: 2018-01-22 22:10:00 +0000
Tags: 
  - Pentaho
  - PostgreSQL
  - Linux
---
Last week I had to install a Pentaho CE 8.0 instance on a Linux virtual machine. Ubuntu was the chosen distro and for many reasons I like to keep it simple, so instead of installing the desktop version I made the decision to run the software I needed on top of Ubuntu Server 16.04 LTS. Being the first time I had to install Pentaho CE I looked for articles to guide me through the process but unfortunately most of them were out-dated or using graphical interfaces that I didn’t had and didn’t want to add, otherwise I wouldn’t have chosen to go with the Ubuntu server version in the first place.

This post is the compilation of all the steps I had to perform to install the needed software. The steps below don’t cover the installation of the Ubuntu server distro itself, so its expected that you already have a (virtual) machine with the Ubuntu Server installed. Please also take in mind that I’ll be using almost all default passwords and configurations to keep this post the simplest as possible.

- Before we start
- Install Java Runtime 8
- Getting Pentaho Server 8.0 CE
- PostgreSQL installation
- Configuring Pentaho connection to PostgreSQL
- Running Pentaho Server

### Before we start

``` bash
# Update the apt-get index
sudo apt-get update

# If you are running a clean Ubuntu install I also recommend to upgrade the system
sudo apt-get upgrade

# Since we'll later need to unzip some files let's also install the unzip package.
sudo apt-get install unzip
```

### Install Java Runtime 8

``` bash
# This command will install the Java JRE package, specifically the OpenJDK 8
sudo apt-get install default-jre

# Next we need to set the JAVA_HOME environment variable, but before that we need to check where exactly Java is installed
sudo update-alternatives --config java

# Copy the Java installation path use an editor to open change the environment variables
sudo vim /etc/environment

# At the end of the file add the following line, making sure to replace the path with the one you copied above
JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"

# Save the file and reload it. After that you can check if the environment variable is properly configured with a simple echo command
source /etc/environment
echo $JAVA_HOME
```

### Getting Pentaho Server 8.0 CE

``` bash
# Download the package using wget. Please take in mind that the name of the file is the most recent at the time of this writing. You can double check the correct link on sourceforge (https://sourceforge.net/projects/pentaho/files/). 
wget https://sourceforge.net/projects/pentaho/files/Pentaho%208.0/server/pentaho-server-ce-8.0.0.0-28.zip/download -O pentaho-server-ce-8.0.0.0-28.zip

# When the download is completed we need to unzip the file and move the extracted pentaho-server directory to another directory. In this example I'll just put everything under /opt/pentaho
unzip pentaho-server-ce-8.0.0.0-28.zip
sudo mkdir /opt/pentaho
sudo pentaho-server /opt/pentaho

# Next we'll create a pentaho group and user on the machine and configure it to be the owner of the /opt/pentaho path
sudo addgroup pentaho
sudo adduser --system --ingroup pentaho --disabled-login pentaho
sudo chown -R pentaho:pentaho /opt/pentaho
```

### PostgreSQL installation

``` bash
# It is time to install the PostgreSQL instance. Once again we'll just use apt-get to install the required package
sudo apt-get install postgresql

# By default a postgres user will be created, without any password. So next step is to set the password for this user
# The following commands will open psql with postgres user, set its password and quit psql
sudo -u postgres psql postgres
\password 
\q 

# Now that we have a password set for the default user, we need to open the pg_hba.conf file using an editor and allow local connections.
sudo vim /etc/postgresql/9.5/main/pg_hba.conf
```

Locate the line that looks like: 
```
local all all peer
```
Replace the peer method to md5, save and close the file and restart postgresql

``` bash
sudo service postgresql restart 
```
 
### Configuring Pentaho connection to PostgreSQL

``` bash
# Almost there but first we'll need to run some SQL scripts to create some needed databases on the PostgreSQL instance. 
cd /opt/pentaho/pentaho-server/data/postgresql
psql -U postgres -h 127.0.0.1 -p 5432 -f create_jcr_postgresql.sql
psql -U postgres -h 127.0.0.1 -p 5432 -f create_quartz_postgresql.sql
psql -U postgres -h 127.0.0.1 -p 5432 -f create_repository_postgresql.sql

# Next we'll configure JDBC to use PostgreSQL
cd /opt/pentaho/pentaho-server/tomcat/webapps/pentaho/META-INF
sudo vim context.xml
```

On both of the Resources configured in that XML file make sure to alter the driverClassName and url attributes so that your final result will look something like 
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<Context path="/pentaho" docbase="webapps/pentaho/">
   <Resource name="jdbc/Hibernate" auth="Container" type="javax.sql.DataSource" 
     factory="org.apache.tomcat.jdbc.pool.DataSourceFactory" maxActive="20" minIdle="0" 
     maxIdle="5" initialSize="0" maxWait="10000" username="hibuser" password="password" 
     driverClassName="org.postgresql.Driver" 
     url="jdbc:postgresql://127.0.0.1:5432/hibernate" validationQuery="select count(*) FROM INFORMATION_SCHEMA.SYSTEM_SEQUENCES" />
   <Resource name="jdbc/Quartz" auth="Container" type="javax.sql.DataSource" 
     factory="org.apache.tomcat.jdbc.pool.DataSourceFactory" maxActive="20" minIdle="0" 
     maxIdle="5" initialSize="0" maxWait="10000" username="pentaho_user" 
     password="password" driverClassName="org.postgresql.Driver" 
     url="jdbc:postgresql://127.0.0.1:5432/quartz" validationQuery="select count(*) from INFORMATION_SCHEMA.SYSTEM_SEQUENCES" />
</Context>
```

### Running Pentaho Server

``` bash
# Start the Pentaho Server 8.0
cd /opt/pentaho/pentaho-server
sudo ./start-pentaho.sh

# If needed use ifconfig to find out your current IP address
ifconfig
```

On another machine just open a web browser and hit the 8080 port of the machine were Pentaho Server is running. 
If everything went as expected you should see the below screen.

![Pentaho CE 8.0 initial screen](/assets/img/pentaho_ce_8.0_login_screen.png)

Enjoy and see you on a next post.