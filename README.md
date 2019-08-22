##### Table of Contents: AWS-Koha Installation  
* [Phase 1: setup AWS instance.](#phase1)
	* [Launching an instance in the AWS EC2 dashboard.](#launchinstance)
	* [Which Amazon Machine Image to use.](#ubuntu)
	* [Inctance type.](#instancetype)
	* [Configuring instance details.](#instanceSettings)
	* [EBS Settings.](#ebsSettings)
	* [Adding tags.](#addTags)
	* [Security group settings](#securityGroups)
	* [Generate a key-pair .pem file.](#keyPair)
	* [Access the EC2 instance via SSH.](#secureShell)
	* [Using sudo for the instance.](#sudo)

* [Phase 2: Installing Koha from Terminal.](#phase2)
	* [Add a Koha Community Repository.](#kohaCommunity)
	* [Update Ubuntu](#updateUbuntu)
	* [Install Koha.](kohaInstall)
	* [Modify the Koha site file](#kohaSiteFile)
	* [Install MySQL.](#installMySQL)
	* [Enable Apache modules.](#enableApache)
	* [Creating a Koha Instance.](#kohaInstance)
	* [MySQL Secure Installation.](#mySQLSecureInstall)
	* [Edit the Apache ports.](#editApachePorts)
	* [Access the freshly installed Koha website (with admin page).](#accessIPV4)
	
* [Phase 3: Migrating the old Koha database instance to new installation.](#phase3)
	* [Download the latest .db backup from the current production(old) Koha instance.](#downloadDB)
	* [Upload the Koha SQL database file to your EC2 instance.](#scpTheFile)
	* [Drop and recreate existing koha database instance.](#dropRecreate)
	* [Restore the database to the newly installed instance.](#restoreDatabase)
	* [Upgrade database schema.](#upgradeDatabaseSchema)
	* [Rebuild the Zebra database.](#rebuildZebra)


# aws-koha-installation

Koha Installation to AWS instance

<a name="phase1"/>**Phase 1**: setup AWS instance:

* <a name="launchinstance"/>Launch an instance in the AWS EC2 dashboard:
![launch instance](../assets/001_create_instance.png)

* <a name="ubuntu"/>Under Community AMIs, select ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-server-20190514 - ami-07b4156579ea1d7ba
![Select operating system](../assets/003_ubuntu_1604.png "Ubuntu 16.04")

* <a name="instancetype"/>Use t2 micro for instance type
![instance type](../assets/004_t2_micro.png "Select the instance type")

* <a name="instanceSettings"/>Use default instance settings
![instance settings](../assets/005_configure_instance_settings.png "Configure the instance settings")

* <a name="ebsSettings"/>Use default storage settings
![add storage](../assets/006_add_storage.png "add storage")

* <a name="addTags"/>Add tags
![add tag](../assets/007_add_tag.png "add tag")

* <a name="securityGroups"/>Select the following settings for the Security Group Settings:

  * SSH; port 22 - default / turned on; Source: Anywhere
  * HTTP; port 80; Source: Anywhere
  * HTTPS; port 443; Source: Anywhere
  * Custom TCP Rule: port 8080; Source: Anywhere

![security groups](../assets/008_security_groups.png "add the security groups")

* <a name="keyPair"/>Generate a key-pair to connect (SSH) to the instance
	* Make sure to save the keypair.pem to a directory for later access.
	![key pair](../assets/009_key_pair.png "key pair")
	

* <a name="secureShell"/>Once running, select the instance and click the "connect" button to see how to connect via SSH

	* Go to the Terminal or the Command Prompt, and change the current working directory to where you previously saved the keypair file.
	* type: chmod 400 keypair.pem, and hit return.
	* subsequently type: ssh -i "keypair.pem" ubuntu@(public DNS address)
	* ssh -i "keypair.pem" ubuntu@ec2-54-88-132-7.compute-1.amazonaws.com
	* this will connect you to the EC2 server.
![SSH](../assets/010_connect_to_instance.png "SSH to instance")

* <a name="sudo"/>Once connected, open the terminal and activate super user (sudo su)
![ubuntu](../assets/011_connect_to_ubuntu.png "Ubuntu")

<a name="phase2"/>**Phase 2**: Install Koha from Terminal (refer to [Koha on Ubuntu wiki!](https://wiki.koha-community.org/wiki/Koha_on_ubuntu_-_packages#Pre-Installation_Setup))

* <a name="kohaCommunity"/>Add a Koha Community Repository. Type:
	* echo deb http://debian.koha-community.org/koha stable main | sudo tee /etc/apt/sources.list.d/koha.list
	* wget -O- http://debian.koha-community.org/koha/gpg.asc | sudo apt-key add -

* <a name="updateUbuntu"/>Make sure that Ubuntu is updated:
	* sudo apt-get update
	* sudo apt-get upgrad
	* sudo apt-get clean

* <a name="kohaInstall"/>Install Koha:
	* sudo apt-get install koha-common

* <a name="kohaSiteFile"/>modify some settings in the koha-site file:
	* Replace the current domain with empty quotes ""
	* Set Intraport to 8080
	* Set Opacport to 80
	![launch instance](../assets/012_modify_koha_sites.png)

* <a name="installMySQL"/>Install MySQL Server
	* sudo apt-get install mysql-server

* <a name="enableApache"/>Enable Apache modules
	* sudo a2enmod rewrite 
	* sudo a2enmod cgi 
	* sudo service apache2 restart

* <a name="kohaInstance"/>Create a **Koha instance**
	* **sudo koha-create --create-db library**
	* where library is the name of the koha instance
	* this will be the database name to be used in MySQL.
		* **Note**: Koha will attach "koha_" as prefix of your library name and that will be the name of the MySQL database instance.

* <a name="mySQLSecureInstall"/>Performing a MySQL Secure Installation
	* sudo **mysql_secure_installation**
	* You will be prompted with the following:
		* Change the root password? [Y/n] n
		* Remove anonymous users? [Y/n] n
		* Disallow root login remotely? [Y/n] n
		* Remove test database and access to it? [Y/n] y
		* Reload privilege tables now? [Y/n] y

* <a name="editApachePorts"/>Edit the apache ports
	* go to directory **/etc/apache2**
	* edit the ports configuration file, enter: nano ports.conf
		![ports config](../assets/013_ports_config.png "ports config 8080")
		*  add Listen 8080 after Listen 80. This will allow access to port 8080 specified in the security groups.

* <a name="accessIPV4"/>At this point, Koha should be installed and the EC2 IPV4 address should be accessible.
	* The Koha system maintenance screen should appear.
	![Koha system maintenance screen](../assets/014_system_maintenance.png)
	* In addition, adding the :8080 at the end of the IPV4 address will show the **Koha administrator Login page**.
	![Koha admin login screen](../assets/015_koha_admin_login.png)
	* Since this is a database replication and not a fresh Koha installation, we will not need the username and password credentials for the Koha administrator login page. We may now proceed with the data migration.

<a name="phase3"/>**Phase 3**: Migrating the old Koha database instance to new installation.

* <a name="downloadDB"/>Download the latest .db backup from the current production(old) Koha instance.
	* Login to the admin page of the production Koha instance currently in use.
	* Go to tools, and then click **Export Data**
	* Go to the **Export database** tab
	* Select a version of the Koha database you want to export. The file is in .sql format.
		* **Note**: Time to download will depend on file size. This is usually large.
	* Save the sql file in a folder you can access later.

* The next steps will be done within the EC2's /home/ubuntu directory.

* <a name="scpTheFile"/>Upload the Koha SQL database file to your EC2 instance by using SCP (Secure Copy Protocol).
	* The format is as follows:
		* scp -i keypair.pem koha_library.sql ubuntu@(public DNS address):
		* scp -i itskohatest.pem koha_library.sql ubuntu@ec2-54-88-132-7.compute-1.amazonaws.com:
			* **Note**: This will save the file to /home/ubuntu directory on your EC2 instance.

* <a name="dropRecreate"/>Drop and recreate existing koha database instance.
	* mysql -uroot -p
	* type your password
	* drop database koha_library;
	* create database koha_library;
	* exit;
	![drop and recreate koha library](../assets/019_drop_recreate_koha_library.png)
	

* <a name="restoreDatabase"/>Perform database restoration command:
	* **mysql -uroot -p koha_library < koha_library.sql**, enter the password.
	* where **koha_library** is the name of your recreated Koha MySQL database
	* and **koha_library.sql** is the backup file saved in the directory that will be used for migration.

* <a name="upgradeDatabaseSchema"/>Upgrade database schema:
	* sudo service memcached restart
	* sudo koha-upgrade-schema library
	* **Note**: library does not have the "koha_" prefix.
	![Upgrade zebra library](../assets/016_upgrade_zebra_library.png)

* <a name="rebuildZebra"/>Rebuild Zebra Database
	* sudo koha-rebuild-zebra -v -f library
	* This may take a couple of minutes to take effect.
	![exporting files](../assets/017_exporting_database_files.png)
	![exporting files done](../assets/018_exporting_database_done.png)