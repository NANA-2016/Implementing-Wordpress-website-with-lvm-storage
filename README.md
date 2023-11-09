# Implementing-Wordpress-website-with-lvm-storage

## Implementing wordpress website with lvm storage

# SETTING UP THE WORDPRESS WEBSERVER.

-  Creating a webserver instance

![ws instance](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/013abfcd-31f5-4ac0-8c3e-a2a7f7dffe79)


- Creation of volumes on the same AZ as webserver.

![wb volumes](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/a6f5bb7b-76ec-413f-bbec-a0ba3d75af6b)

After creating a webserver instance and volumes with the same AZ, three volumes are attached to the webserver instance using redhat OS .

 Opening a webserver through ssh will enable us to run a command lsblk to inspect what block devices have been have been attached dto the server.

  ## "ssh -i Darey.io.pem ec2-user@13.40.221.117"

 ![ssh wb](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/d0864a3e-0481-408d-80a7-8ec72351bc4d)

## lsblk command

![lblsk](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/75c9e57d-42b7-46d3-8029-efff049f7430)

## ls /dev/

![ls dev](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/34b985f4-5a11-4e1f-9414-e45e10c1db45)

Using df -h command, we check the mounts and spaces on the webserver  as demonstrated below.

![df -h](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/7c486fad-bfa2-48b2-9023-5cedbd315096)

 Each of the 3 disks created then is made into a partision using the command shown for each disk.  -- sudo gdisk /dev/xdf

 - sudo gdisk /dev/xdg

 -  sudo gdisk /dev/xdh

All these has been demonstrated on the screenshots below.

 # sudo gdisk /dev/xdf

![gdisk dev xvdf](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/3494641e-bc1e-44c1-967a-1afb4b2de59e)

 # sudo gdisk /dev/xdg

![gdisk dev xvdg](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/4b3f6517-740d-48a4-a670-f11930ceb490)


 # sudo gdisk /dev/xdh
 
![gdisk dev xvdh](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/2502cf09-53b7-4c59-ac16-f96a689bda63)

 To see the newly created / configured partitions on each of the disks , use the command lsblk

 ![lsblk partition config](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/15e48735-e73e-44fd-9ed6-7aa22a6fd7ce)

# LVM INSTALLATION .

 lVM2 is installed using the command sudo yum install lvm2.

 ![lvm2 installation (2)](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/bc13bbdb-e0d2-4629-a28e-62a15f2715b1)


  Using the command sudo lvmdiskscan, we can now check the  available partitions on the webserver .
   see the screenshots below.

   ![lvmdiskscan (2)](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/b86181f0-b298-49d2-b516-2c999840314c)

   ## pvcreate 

    This is a utility used to mark each of 3 disks as physical volumes(PVs) to be used by LVM. The following commands are used.

 - sudo pvcreate /dev/xvdf1
-  sudo pvcreate /dev/xvdg1
-  sudo pvcreate /dev/xvdh1

sudo pvs is a command used to check if all the pvs(physical volumes) have been created successfully.

  ![pv creation](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/566aecd6-65b7-4519-958b-a9525b61f40e)

  Next the 3 PVs are all added to one volume group (VG) "sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1"
. In this case the VG has been nanmed as webdata-vg . To verify if the VG has been create a command "sudo vgs " is used .

 SEE BELOW.

 ![vg create](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/df786d64-6ee9-42f7-86e6-c7e265a3ebe0)

## Creation of logical volumes using lvcreate utility.

 Here you create -  apps-lv - used to store data for the website.

                 -  logs-lv - used to store data for the logs.

  Each use half of the PV size space.(14G)

 The commands - sudo lvcreate -n apps-lv -L 14G webdata-vg
              -  sudo lvcreate -n logs-lv -L 14G webdata-vg



 To ensure the Logical volumes have been created, we run the command  "sudo lvs"

![lvcreate](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/9c9e2c82-3b35-4b54-b728-0585f245bb22)


" sudo vgdisplay -v #view complete setup - VG, PV, and LV" is a command used to view the complete 

set up for VG,PV and LV.

"sudo lsblk" shows all the block that have been created and where they have been attached .

- sudo vgdisplay -v #view complete setup - VG, PV, and LV
  
 -sudo lsblk 

  These two commands can be run together to get the results as shown on the screenshot below.

![vieweng completeset up and blocks](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/5b2df3c2-40e0-42a8-b923-98841f2c09b9)

# ext4 filesystem

 - sudo mkfs -t ext4 /dev/webdata-vg/apps-lv

- sudo mkfs -t ext4 /dev/webdata-vg/logs-lv

 the above commands are used to format logical volumes into ext4filesystem.

 ![ext4](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/37632fca-dffb-47f9-a0d3-e4dfc73a28a4)


 # Website file storage.
 
 Happens in var/www/html while backup for log data is 
 
 stored in /home/recovery/logs

  These two directories are created using the commands 

  - sudo mkdir -p /var/www/html

  - sudo mkdir -p /home/recovery/logs

   ![var logs directories](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/d6122f6f-9fb3-4f8a-924f-feb320a4ab48)

 ## Mount and rsync utilities.

  These are utilities used  to

 " sudo mount /dev/webdata-vg/apps-lv /var/www/html/2"  mounts logical volumes

 "sudo rsync -av /var/log/. /home/recovery/logs/ " used to backup all the 
   
   log directory /var/log into home/recovery/logs/

   "sudo mount /dev/webdata-vg/logs-lv /var/log" mounts var/logs on logs-lv 
   
   logical volume

  " sudo rsync -av /home/recovery/logs/log/. /var/log "restore log file back

    into /var/log
    
 All the listed above commands are as shown on the below screenshot.

 ![mount and rsync on apps lv](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/1351ef26-621c-46a5-a35f-b37bb59b4190)

![mount and rsync logs lv](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/87f4ca72-daf1-49c4-b266-7c7ce327e60f)

# Persist

 updating the /etc/fstab file  helps maintain(persist) the mount 
 
 configuration after restarting the server hence no data will be lost . The
 
 process starts with a command "sudo blkid" which helps in retriving the 
 
 UUID of both logs and apps.

  This is followed by configuration of the /etc/fstab file using the 
  
  "sudo vi /etc/fstab " command .

   The screen shots below demonstrate the steps .

   ![SUDO BLKID](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/6228bc56-f0cb-491b-a216-ed2ba51a68c3)

![etcfstab config](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/12ac6dd6-ca16-4e7b-bca8-96e4828c5620)

    Later we need to check if the mount is successful and reload the daemon.
    
 - sudo mount -a

-  sudo systemctl daemon-reload

-  df -h helps verify if the set up is ok.

   the screen shot helow demonstrates the three commands consecutively as

   shown with the output of df-h demonstration.

   ![mount reload df-h](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/6fae2121-9eeb-4d8a-a973-46a19c2e695d)

## INSTALLING WORDPRESS AND CONFIGURING TO USE MYSQL DATABASE.

First updating the repository is mandatory.

![update repository](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/ea556b13-71d9-41c8-b265-5a563f813f34)


 ## Install wget apache and its dependencies

 " sudo yum -y install wget httpd php php-mysqld php-fpm php-json " is a 
  
  command used to do the above task.

![Screenshot 2023-11-08 235717](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/5c66fcec-a716-4968-8662-b5cf123dd430)

  After these the below commands are used to ensure apache is enabled and 
  
  is in an active and running state.

 - sudo systemctl enable httpd
  
  - sudo systemctl start httpd

    ![start and enable httpd](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/5271f244-cb79-43b3-af14-f7df2f3b8558)

The set of commands below are used to install php and its dependencies

sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
sudo setsebool -P httpd_execmem 1

![install php and dependencies](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/6c807bd7-93dd-491e-84ba-3f207d22dc6f)


 After that we ensure we restart apache  by using the command sudo systemctl restart httpd

 We then now download wordpress  and configure SELinux Policies


  the two sets of command below are used .
  # Download wordpress.
mkdir wordpress
cd   wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
 sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php

 # copy wordpress to var/www/html/
 sudo cp -R wordpress /var/www/html/
 
 ![restart httpd and  download wordpress](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/5cc1835d-06e4-4cb6-a866-0b37f8b290b7)


# Configure SELinux Policies.

   sudo chown -R apache:apache /var/www/html/wordpress
 sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
 sudo setsebool -P httpd_can_network_connect=1

![SELinux](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/2c6956ac-5a79-4f79-8737-47916a56d343)


# SETTING UP THE DATABASE SERVER .

 First ids the creation of volumes and attacching the columes to instance created as in the case 
 
 of the webserver .

 
 ![db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/b43d5bf2-2baf-4941-b843-176afd5ef1b9)
 

  Later the ssh connection is done to the terminal so as to be able to set up the database and 
  
  run all the necessary commands on the ternminal.

  ![ssh db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/19338a6f-6e35-407d-bab2-32f6930bf7b3)

  

   Sudo lsblk a command is run to check al, the block that have been created as well as the  
   
   ls /dev command is run to the list of all available blocks.

   ![lsblk ls dev db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/86a4ec6e-f180-4a99-8e47-5e879cdd74cc)


 df -h is a command that is used to help us get to know all the soace that is availablre for use for the database set 
 
 up.
 
![df -h db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/d00274cf-efe9-4b6b-9b90-853ee16234a4)


  Each disk needs a partition to be created and the command  sudo gdisk is used for all the blocks that have been 
  
  created  to run as 
  
  - sudo gdisk /dev/xvdf

    ![gdisk 1 db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/5b241c56-a886-4a89-9c1f-59f097166e84)


  - sudo gdisk /dev/xvdg

    ![gdisk2 db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/89b6bdfa-0981-45b2-b214-e0419a8531a2)


  - sudo gdisk /dev/xvdh

    ![gdisk 3 db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/672f0a9e-2bc4-4a23-9390-c86f74cc81b2)


     lsblk is used as a command to check allthe newly created partitions as shown on the screen shot below.

    ![lsblk](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/ba6a4545-bb11-4324-a24d-f78096781495)


As it is with the webserver in the database server as well we need to install lvm2  and using the command  sudo 

lvmdiskscan, we are able to check  the available partitions.


![lvm diskscan db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/d7ae925e-c9fe-4736-a13a-5b0f0bba8801)



 After the disk have been created, tthey can now be set as physical volumes using the three command s below jst as it 
 
 was with the webserver .
 
 - sudo pvcreate /dev/xvdf1
   
-  sudo pvcreate /dev/xvdg1
  
-  sudo pvcreate /dev/xvdh1

  and to veryfy that all the 3 pvs have been created sucessfully hte command sudo pvs is used .

  ![pvs](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/2d6e4b4d-7ad1-4ca5-ba37-ee44eb462238)


   All the three PVs are put together in a Volume Group where in this case the name of the volume group is webdata-vg 
   
   using the command "sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1"
   
with "sudo vgs "being used as a command to check if VG has been created susscessfully.

![sudo vgcreate](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/522b5f36-0806-45f1-85f7-eccb9eda400d)



Lv is a utility used to create Logical volumes to be used on the data base  that is db-lv  used to store data for the 

databse while the lv-logs will be used to store data for logs.

 Fot these to be acchieved , we use the commands below with sudo lvs being used tio check if the Logical volume has 
 
 been created sucessfully .

- sudo lvcreate -n db-lv -L 14G webdata-vg

- sudo lvcreate -n logs-lv -L 14G webdata-vg

![sudo lvs db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/2089ef90-f951-4deb-9935-396b59826d23)


 sudo vgdisplay -v #view complete setup - VG, PV, and LV

sudo lsblk 

 These two commands are used to veryfy the entire set up.

 ![complete set up lv,vg,pv db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/befe1335-e58e-4409-9512-f2b978780d25)


 ## ext4 file system 

  This is a format used the logical volumes  using the commands below.
 
 - sudo mkfs -t ext4 /dev/webdata-vg/db-lv
 - 
 - sudo mkfs -t ext4 /dev/webdata-vg/logs-lv

![ext4 db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/ce357088-195a-4980-b491-fec67911df0c)


# Website file storage and logs backup.
 
 Happens in /db/ directory while backup for log data is 
 
 stored in /home/recovery/logs

  These two directories are created using the commands 

  - sudo mkdir -p /db

  - sudo mkdir -p /home/recovery/logs

 ![dg mkdir](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/c236df33-eecd-4cb7-863d-408d20b79853)


 ## Mount and rsync utilities.

  These are utilities used  to

 " sudo mount /dev/webdata-vg/db-lv /var/www/html/2"  mounts logical volumes

 "sudo rsync -av /var/log/. /home/recovery/logs/ " used to backup all the 
   
   log directory /var/log into home/recovery/logs/

   "sudo mount /dev/webdata-vg/logs-lv /var/log" mounts var/logs on logs-lv 
   
   logical volume

   " sudo rsync -av /home/recovery/logs/log/. /var/log "restore log file back

    into /var/log
    
![mount rysnc db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/684b261e-7af0-4169-91c4-382ef0138962)

# Persist
    sudo blkid is a command used to get the UUID of the  device which is used too configure the file  /etc/fstab which 
    
    helps in of the data even after the database server  has been restarted with the command sudo vi /etc/fstab .

  ![blkid db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/833addf0-27c3-46f3-8f0a-c6659e1a66db)

 ![etcfstab db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/25dd707c-a631-478d-b7c9-fbed4872d27c)

  To test confuguration and reload daemon , we use the commands below as well as verfy the whole set up. the as 
  
  demonstrated below by the three commands running consecutively.
- sudo mount -a

- sudo systemctl daemon-reload

- df -h

![df -h, etc fstab db](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/a1ff2f96-770a-43e7-846a-3d12a33231c1)

   
With the set of commands below, we are able to confugure DB to work with wordpress .

 sudo mysql
CREATE DATABASE wordpress;
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit

![database ,ownership, mysql](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/8a9e3472-d121-471c-8b8e-51adc57bd095)

# Configure wordpress to connect to remote database 

 By opening mysql port 3306 and the sourse as the subnet value fromm the webserver ie 172.31.32.0/20 in this case .

 ![subnet bd](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/79fd2d46-3a7d-44ae-8380-9603869c2b7d)


 

 # Installing Mysql -client and connect to the webserver .

  Commands below are used to achieve the above .

-  sudo yum install mysql
 
-  sudo mysql -u admin -p -h <DB-Server-Private-IP-address>

 Run command show databases on mysql  to verify the connection.

 ![test database on ws](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/983a5e62-94b4-4672-83db-1dfcb0b82b1f)


  Change premissions and configurations so as apache can use  Wordpress.

  ![mysql client](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/2389dc21-ad85-4f7c-be7d-b476da2ab9ef)


   Lastly enablre TCP port 80 on the inbound rules of the webserver  and enable everywhere at 0.0.0.0/0

   ![tcp 80 webserver](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/e832f723-af1d-4131-9953-2c251e15527e)


    Access the link to the Wordpress.

    
 ![http](https://github.com/NANA-2016/Implementing-Wordpress-website-with-lvm-storage/assets/141503408/909719f6-7bd3-441a-9faa-777af1a1064e)
  




  
  




