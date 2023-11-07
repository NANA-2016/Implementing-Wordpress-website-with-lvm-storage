# Implementing-Wordpress-website-with-lvm-storage

## Implementing wordpress website with lvm storage

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






