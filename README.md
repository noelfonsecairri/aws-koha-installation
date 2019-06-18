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
* Turn on the following for the Security Group Settings:

  * SSH; port 22 - default / turned on; Source: Anywhere
  * HTTP; port 80; Source: Anywhere
  * HTTPS; port 443; Source: Anywhere
  * Custom TCP Rule: port 8080; Source: Anywhere

![launch instance](../assets/008_security_groups.png)

* Generate a key-pair to connect (SSH) to the instance
![launch instance](../assets/009_key_pair.png)
* Once connected, open the terminal and activate super user (sudo su)

**Phase 2**: Install Koha from Terminal (refer to [Koha on Ubuntu wiki!](https://wiki.koha-community.org/wiki/Koha_on_ubuntu_-_packages#Pre-Installation_Setup))

asdasd

