# cf-jenkins

## Install Jenkins/Ant

Install java, unzip, wget, and nano

```
yum install -y java-1.8.0-openjdk java-1.8.0-openjdk-devel unzip wget nano
```

update localtime
``
mv /etc/localtime /etc/localtime.bak
ls -s /usr/share/zoneinfo/America/Chicago /etc/localtime
``

Set ssh-keygen for root. 

``ssh-keygen``

Install Jenkins

```
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
yum install -y jenkins
systemctl start jenkins.service
```

goto browser `IP_address:8080`
Find jenkins inital password

``cat /var/lib/jenkins/secrets/initialAdminPassword``

Install Ant

```
wget http://mirror.cc.columbia.edu/pub/software/apache//ant/binaries/apache-ant-1.10.6-bin.zip
unzip apache-ant-1.10.6-bin.zip
mv apache-ant-1.10.6 /opt/
cd /opt/
ln -s /opt/apache-ant-1.10.6 ant

ls -s /opt/any/bin/ant /usr/bin/ant

```

add additional tools ant-contrib

```
wget https://sourceforge.net/projects/ant-contrib/files/ant-contrib/1.0b3/ant-contrib-1.0b3-bin.zip
unzip ant-contrib-1.0b3-bin.zip


cd /opt/ant/lib
wget https://sourceforge.net/projects/jsch/files/jsch.jar/0.1.55/jsch-0.1.55.jar
```

cp and rename id_rsa

``
cp -p /root/.ssh/id_rsa /var/lib/jenkins/secrets/deploy
chown jenkins:jenkins /var/lib/jenkins/secrets/deploy
chmod 400 /var/lib/jenkins/secrets/deploy
``

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
