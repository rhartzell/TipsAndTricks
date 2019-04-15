# Amazon Linux 2 AMI (HVM), SSD Volume Type (t2.micro)
## Use private key to SSH to server
ssh ec2-user@172-31-93-10 -i MyUSEast1KP.pem 
## Superuser mode
[root@ip-172-31-93-10 ec2-user]$ sudo su
## Update OS
[root@ip-172-31-93-10 ec2-user]$ yum update -y  
## Install Apache                                                                                                           
[root@ip-172-31-93-10 ec2-user]$ yum install httpd -y
## Navigate to webroot
[root@ip-172-31-93-10 ec2-user]$ cd /var/www/html
## Create an index.html file
[root@ip-172-31-93-10 html]$ nano index.html
[root@ip-172-31-93-10 html]$ ls
index.html
## Start Apache
[root@ip-172-31-93-10 html]$ service httpd start

Redirecting to /bin/systemctl start httpd.service
## Set flag to turn on Apache on startup
[root@ip-172-31-93-10 html]$ chkconfig on
