# cf-jenkins

# Install Jenkins/Ant


# Install ColdFusion/Apache

Create new group for coldfusion and apache

`groupadd webusers`

create new coldfusion user

`adduser --system -g webusers -s /sbin/nologin -M -c ColdFusion cfuser`

Install Apache and git `yum install -y httpd git`

create default wwwroot
``
mkdir -p /www
mkdir -p /www/default
mkdir -p /www/default/wwwroot
``

