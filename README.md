# cf-jenkins

## Install Jenkins/Ant

Install java, unzip, wget, and nano

```
yum install -y java-1.8.0-openjdk java-1.8.0-openjdk-devel unzip wget nano git
```

update localtime
``
timedatectl set-timezone America/Chicago
yum install -y ntp
systemctl start ntpd
systemctl enable ntpd
``

Set ssh-keygen for root.

``ssh-keygen``

Install Jenkins

```
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
yum install -y jenkins
sudo service jenkins start
sudo chkconfig jenkins on

```

goto browser `IP_address:8080`
Find jenkins inital password

``cat /var/lib/jenkins/secrets/initialAdminPassword``

## Manage Jenkins
Goto Manage Jenkins

#### Install Plugins

install these Plugins
- GitHub API Plugin
- GitHub Plugin
- Ant Plugin
- Green balls Plugin
- Git Parameter Plugin

Restart Jenkins

Login Jenkins

### Configure System
- Github
  - Enter Github.com info
  - create a personal token in github.com that has repo:status, all admin:repo_hook (write, read)
  - save github personal token as secret text credentials. Test connection





#### Configure firstJob

Creete freestyle project
- check:: github project and enter github repo
- check::This project is parameterized
  - add string parameter
    - name: ENIVRONMENT. default value: DEV. description: DEV, PROD
  - add git paramter
    - name: Git Branches. description: list git branches. paramter Type: branch. default value: origin/master
- under source code.
  - choose Git. Enter repo  user github Clone with HTTPS url.
- add additional behaviours:: Wipe out repository & force clone
- check:: GitHub hook trigger for GITScm polling <!-- http://JENKIND-IP:8080/github-webhook/  -->
- Build
  - Add build step:: Invoke Ant
- Post-build Actions
  - Email Notification
    - add email address
- Save Job

Goto gihub.com repo that you are setting Jenkins job for. Goto settings, webhooks and add http://JENKIND-IP:8080/github-webhook/
- let me select individual events
  - check:: pull request
  - check:: pushes
  - save


Run the firstJob in Jenkins. It might fail. Need to setup local ANT.


## Install Ant

Download Apache Ant

``wget http://mirror.cc.columbia.edu/pub/software/apache//ant/binaries/apache-ant-1.10.6-bin.zip``

Extract it using command

``unzip apache-ant-1.10.6-bin.zip``

Move the extracted contents to /opt/ directory.

``mv apache-ant-1.10.6/ /opt/ant``

Make a symlink to ant/bin folder as shown below.

``ln -s /opt/ant/bin/ant /usr/bin/ant``

### Setup ANT environment variable

Create a file called ant.sh under /etc/profile.d/ directory.

``nano /etc/profile.d/ant.sh``

Add the following contents

```
#!/bin/bash
ANT_HOME=/opt/ant
PATH=$ANT_HOME/bin:$PATH
export PATH ANT_HOME
export CLASSPATH=.
```

Save and close the file. Make it executable using the following command

``chmod +x /etc/profile.d/ant.sh``

Then, set the environment variables permanently by running the following command

``source /etc/profile.d/ant.sh``

Log out or reboot your system

Check the ant version using command

``ant -version``

cp and rename root id_rsa

```
cp -p /root/.ssh/id_rsa /var/lib/jenkins/secrets/deploy
chown jenkins:jenkins /var/lib/jenkins/secrets/deploy
chmod 400 /var/lib/jenkins/secrets/deploy
chcon -t var_lib_t -u system_u /var/lib/jenkins/secrets/deploy
```

### Add Additional Tools
ant-contrib, JSch

```
cd /var/lib/jenkins/tools/
wget https://sourceforge.net/projects/ant-contrib/files/ant-contrib/1.0b3/ant-contrib-1.0b3-bin.zip
unzip ant-contrib-1.0b3-bin.zip
chcon -R -t var_lib_t -u system_u ant-contrib/
```

add JSch - Java Secure Channel http://www.jcraft.com/jsch/

```
cd /opt/ant/lib
wget https://sourceforge.net/projects/jsch/files/jsch.jar/0.1.55/jsch-0.1.55.jar
```

Login as jenkins user

``
su - jenkins -s /bin/sh
``

Create ssh key

``
ssh-keygen
``

Copy jenkins public key to other servers

Make sure to ssh into servers

```
ssh -i /var/lib/jenkins/secrets/deploy  root@CF-server-IP
ssh -i /var/lib/jenkins/secrets/deploy  root@CF-server-IP
ssh -i /var/lib/jenkins/secrets/deploy  root@CF-server-IP
```


## ColdFusion Installation

Create new group for coldfusion and apache

```groupadd webusers```

create new coldfusion user

```adduser --system -g webusers -s /sbin/nologin -M -c ColdFusion cfuser```

create / edit silent install properties

```touch silent.install```

make coldfusion install executeable

```
chmod +x ColdFusion_2018_WWWE_64linux.bin

./ColdFusion_2018_WWWE_64linux.bin -f silent.install
```

start coldfusion

```service cf2018 start```

## Install and Configure Apache Web Server

### Install Apache and git

```yum install -y httpd git```

### Setup Directory for Web Roots

create default wwwroot and logs folder

```
mkdir -p /www
mkdir -p /www/default
mkdir -p /www/default/wwwroot
mkdir -p /www/default/logs
```

add the apache user to group

```usermod -aG webusers apache```

Update file system permissions

```
chown -R root:webusers /www
chmod -R 750 /www
chcon -R -t httpd_sys_content_t -u system_u /www/default/wwwroot/
```

Edit httpd.conf and change the DocumentRoot from /var/www/html to your new default site root /www/default/wwwroot

create index.html

```echo 'Hello' > /www/default/wwwroot/index.html```

Restart Apache

```service httpd restart```

Test apache

```curl http://localhost/```

### Start Apache on Boot

```systemctl enable httpd.service```


### Add vhost
create vhost
``` touch /etc/httpd/conf.d/vhost.conf ```

add below to file created above.

```
<VirtualHost *:80>
  ServerName http://ip.address
  ServerAlias ip.address
	DocumentRoot /www/default/wwwroot
  <Directory "/www/default/wwwroot">
      Options -Indexes FollowSymLinks
      AllowOverride All
      Order allow,deny
      Allow from all
  </Directory>

  #logs
  ErrorLog /www/default/logs/error.log
  CustomLog /www/default/logs/access.log combined
</VirtualHost>
```


### add logrotate
``` cd /etc/logrotate.d/ ```

create new config
``` touch -p /etc/logrotate.d/http-custom ```

add below to file created above.

```
/www/default/logs/*.log {
    rotate 10
    size 25M
    missingok
    notifempty
    sharedscripts
    compress
    postrotate
        /sbin/service httpd reload > /dev/null 2>/dev/null || true
    endscript
}

```

Restart Apache

```service httpd restart```
