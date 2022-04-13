# Magento-2.4.x-installation-guide for Linux Users


### Step 1:- Download 2.4.x package and Install php 7.x according to Magento Requirements [Here](https://devdocs.magento.com/guides/v2.4/install-gde/system-requirements.html)

Download Package from this link:- [ReferenceLink](https://magento.com/tech-resources/download)

> From Magento 2.4.1 to 2.4.3 requires php 7.4 I'm going to install php 7.4 using below commands:-
```
sudo apt-get update
sudo apt -y install software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update

sudo apt install php7.4-common php7.4-mysql php7.4-xml php7.4-xmlrpc php7.4-curl php7.4-gd php7.4-imagick php7.4-cli php7.4-dev php7.4-imap php7.4-mbstring php7.4-opcache php7.4-soap php7.4-zip php7.4-intl -y

sudo apt -y install php7.4
```

Make sure to install the php extensions according to your need.

### Step 2:- Install Elastic Search

If you want to follow a Reference link then Here is the link:- [Reference Link](https://www.howtoforge.com/tutorial/ubuntu-elastic-stack/)

> Instead of the reference link you can follow these commands as well:-
```
  wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
  echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
  sudo apt update
  sudo apt install elasticsearch -y
```

**After the installation is complete, go to the '/etc/elasticsearch' directory and edit the configuration file 'elasticsearch.yml'.**
```
cd /etc/elasticsearch/
vim elasticsearch.yml
```

Instead of changing the file in terminal, you can go to the path `/etc/elasticsearch/` and open `elasticsearch.yml` file and make the changes.

> Uncomment the 'network.host' line and change the value to '127.0.0.1', and uncomment the 'http.port' line for the elasticsearch port configuration.
```
network.host: 127.0.0.1
http.port: 9200
```

### Step 3:- Start and Enable Elastic search

> Now start the elasticsearch service and enable it to launch every time on system boot:-
```
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch
```

> The elasticsearch is now up and running, check it using netstat command netstat and curl commands below:-
```
netstat -plntu
curl -XGET '127.0.0.1:9200/?pretty'
```
  
Now you will get the elasticsearch version '7.x' is running on the default port '9200'.

The elasticsearch installation has been completed.


### Step 4:- Install Magento

> After Elastic search installation run below command in your magento directory:-

```
php bin/magento setup:install --base-url=http://127.0.0.1/magento243/ --db-host=localhost --db-name=magento243 --db-user=root --db-password=admin@123 --admin-firstname=Magento --admin-lastname=User --admin-email=user@example.com --admin-user=admin --admin-password=admin123 --language=en_US --currency=USD --timezone=America/Chicago --use-rewrites=1 --backend-frontname="admin" --elasticsearch-host=127.0.0.1
```

Make sure to add at the end of the command `--elasticsearch-host=127.0.0.1` and also change db name and baseurl according to your magento folder name.

### Step 5:- Install Magento

If you're not sure about your admin url you can simply use below command to get your admin url,

> php bin/magento info:adminuri

If you face two factor authorization error in admin run below commands:-
```
php bin/magento module:disable Magento_TwoFactorAuth
php bin/magento cache:flush
```

### Step 6:- Make Db changes

> You should be facing the issue of frontend and backend not working properly, For that make below changes:-
```
1. Rename .htaccess file
2. Move .htaccess file from pub folder to your magento folder (Magento_243)
3. Copy index.php file of 2.4.1 to your magento folder (Magento_243)
4. Using insert query in core_config table for add below records 
	path -> web/unsecure/base_media_url value -> http://127.0.0.1/magento243/pub/media/
	path -> web/unsecure/base_static_url value -> http://127.0.0.1/magento243/pub/static/
	path -> web/secure/base_static_url value -> http://127.0.0.1/magento243/pub/static/
	path -> web/secure/base_media_url value -> http://127.0.0.1/magento243/pub/media/
5. Apply query in core_config_data INSERT INTO core_config_data (path, value) VALUES ('dev/static/sign', 0) ON DUPLICATE KEY UPDATE value = 0;
```

**Now your magento should be running fine.**


