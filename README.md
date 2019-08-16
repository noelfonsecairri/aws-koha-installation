# aws-koha-installation
Koha Installation to AWS instance

**Phase 1** : setup AWS instance:
* Launch an instance in the AWS EC2 dashboard:
![launch instance](../assets/001_create_instance.png)
* Under Community AMIs, select ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-server-20190514 - ami-07b4156579ea1d7ba
![launch instance](../assets/003_ubuntu_1604.png)
* Use t2 micro for instance type
![launch instance](../assets/004_t2_micro.png)
* Use default instance settings
![launch instance](../assets/005_configure_instance_settings.png)
* Use default storage settings
![launch instance](../assets/006_add_storage.png)
* Add tags
![launch instance](../assets/007_add_tag.png)
* Select the following inbound rules for the Security Group Settings:

  * SSH; port 22 - default / turned on; Source: Anywhere
  * HTTP; port 80; Source: Anywhere
  * HTTPS; port 443; Source: Anywhere
  * Custom TCP Rule: port 8080; Source: Anywhere

![launch instance](../assets/008_security_groups.png)

* Generate a key-pair to connect (SSH) to the instance
![launch instance](../assets/009_key_pair.png)
* Once running, select the instance and click the "connect" button to see how to connect via SSH
![launch instance](../assets/010_connect_to_instance.png)
* Once connected, open the terminal and activate super user (sudo su)
![launch instance](../assets/011_connect_to_ubuntu.png)

**Phase 2**: Install Koha from Terminal (refer to [Koha on Ubuntu wiki!](https://wiki.koha-community.org/wiki/Koha_on_ubuntu_-_packages#Pre-Installation_Setup))

* Add a Koha Community Repository. Type:
	* echo deb http://debian.koha-community.org/koha stable main | sudo tee /etc/apt/sources.list.d/koha.list
	* wget -O- http://debian.koha-community.org/koha/gpg.asc | sudo apt-key add -
* Make sure that Ubuntu is updated:
	* sudo apt-get update
	* sudo apt-get upgrad
	* sudo apt-get clean
* Install Koha:
	* sudo apt-get install koha-common
* modify some settings in the koha-site file:
	* Replace the current domain with empty quotes ""
	* Set Intraport to 8080
	* Set Opacport to 80
	* ![launch instance](../assets/012_modify_koha_sites.png)
* Install MySQL Server
	* sudo apt-get install mysql-server
* Enable Apache modules
	* sudo a2enmod rewrite 
	* sudo a2enmod cgi 
	* sudo service apache2 restart
* Create a **Koha instance**
	* **sudo koha-create --create-db library**
	* where library is the name of the koha instance
	* this will be the database to be used in MySQL
* Install MySQL
	* sudo **mysql_secure_installation**
	* You will be prompted with the following:
		* Change the root password? [Y/n] n
		* Remove anonymous users? [Y/n] n
		* Disallow root login remotely? [Y/n] n
		* Remove test database and access to it? [Y/n] y
		* Reload privilege tables now? [Y/n] y
* Edit the apache ports
	* go to directory **/etc/apache2**
	* edit the ports configuration file, enter: nano ports.conf
		*  ![test](../assets/011_connect_to_ubuntu.png)