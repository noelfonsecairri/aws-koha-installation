# aws-koha-installation
Koha Installation to AWS instance

**Phase 1** : setup AWS instance:
Launch an instance in the AWS EC2 dashboard
Under Community AMIs, select ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-server-20190514 - ami-07b4156579ea1d7ba
Use t2 micro for instance type
Use default instance settings
Use default storage settings
Add tags
Turn on the following for the Security Group Settings:
SSH; port 22 - default / turned on; Source: Anywhere
HTTP; port 80; Source: Anywhere
HTTPS; port 443; Source: Anywhere
Custom TCP Rule: port 8080; Source: Anywhere

Generate a key-pair to connect (SSH) to the instance
Once connected, open the terminal and activate super user (sudo su)

**Phase 2**: Install Koha from Terminal (refer to Koha on Ubuntu wiki)


