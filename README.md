# cf-jenkins

## Install Jenkins/Ant


## ColdFusion Installation

Create new group for coldfusion and apache

`groupadd webusers`

create new coldfusion user

`adduser --system -g webusers -s /sbin/nologin -M -c ColdFusion cfuser`

create / edit silent install properties

`touch silent.install`

make coldfusion install executeable

```
chmod +x ColdFusion_2018_WWWE_64linux.bin

./ColdFusion_2018_WWWE_64linux.bin -f silent.install
```

start coldfusion

`service cf2018 start`

## Install and Configure Apache Web Server

### Install Apache and git 

`yum install -y httpd git`

### Setup Directory for Web Roots

create default wwwroot

```
mkdir -p /www
mkdir -p /www/default
mkdir -p /www/default/wwwroot
```

add the apache user to group

`usermod -aG webusers apache`

Update file system permissions

```
chown -R root:webusers /www
chmod -R 750 /www
chcon -R -t httpd_sys_content_t -u system_u /www/default/wwwroot/
```

Edit httpd.conf and change the DocumentRoot from /var/www/html to your new default site root /www/default/wwwroot

create index.html

`echo 'Hello' > /www/default/wwwroot/index.html`

Restart Apache

`service httpd restart`

Test apache

`curl http://localhost/`

### Start Apache on Boot

`systemctl enable httpd.service`
