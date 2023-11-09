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


  
   

   

 


 
   




  
  




