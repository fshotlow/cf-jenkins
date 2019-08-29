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

Install Ant

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

cp and rename id_rsa

```
cp -p /root/.ssh/id_rsa /var/lib/jenkins/secrets/deploy
chown jenkins:jenkins /var/lib/jenkins/secrets/deploy
chmod 400 /var/lib/jenkins/secrets/deploy
```

### Add Additional Tools
ant-contrib, JSch

```
cd /var/lib/jenkins/tools/
wget https://sourceforge.net/projects/ant-contrib/files/ant-contrib/1.0b3/ant-contrib-1.0b3-bin.zip
unzip ant-contrib-1.0b3-bin.zip
```

add JSch - Java Secure Channel http://www.jcraft.com/jsch/
```
cd /opt/ant/lib
wget https://sourceforge.net/projects/jsch/files/jsch.jar/0.1.55/jsch-0.1.55.jar
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

create default wwwroot

```
mkdir -p /www
mkdir -p /www/default
mkdir -p /www/default/wwwroot
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
