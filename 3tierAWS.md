 # Three Tier Web Solution with Wordpress

 ### Part 1: Configure storage subsystem for Web and Database servers based on Linux OS.
Steps

Launch an EC2 instance that will serve as “Web Server”. Create 3 volumes in the same Availability Zone as your Web Server EC2, each of 10 GiB.

Attach all three volumes one by one to your Web Server EC2 instance

Open up the Linux terminal to begin configuration of the instance. Use lsblk command to inspect what block devices are attached to the server. The 3 newly created block devices are names xvdf, xvdh, xvdg

Use df -h command to see all mounts and free space on your server

Use gdisk utility to create a single partition on each of the 3 disks 

    sudo gdisk /dev/xvdf

A prompt pops up, type n to create new partition, enter no of partition(1), hex code is 8e00, p to view partition and w to save newly created partition.

Repeat this process for the other remaining block devices.

Type lsblk to view newly created partition.

<img width="543" alt="image" src="https://user-images.githubusercontent.com/102925329/215853016-024d84ee-0c8b-4a99-9409-03fe1ebf715f.png">

Install lvm2 package by typing: 

    sudo yum install lvm2. 
 Run sudo lvmdiskscan command to check for available partitions.
 
Create physical volume to be used by lvm by using the pvcreate command:

    sudo pvcreate /dev/xvdf1
    sudo pvcreate /dev/xvdg1
    sudo pvcreate /dev/xvdh1


To check if the PV have been created type: sudo pvs

Next, Create the volume group and name it webdata-vg: 

    sudo vgcreate webdata-vg /dev/xvdf1 /dev/xvdg1 /dev/xvdh1

<img width="613" alt="image" src="https://user-images.githubusercontent.com/102925329/215858335-270b7033-c1d9-4eec-b236-edc1aa12bfc9.png">

View newly created volume group type: sudo vgs

Create 2 logical volumes using lvcreate utility. Name them: apps-lv for storing data for the Website and logs-lv for storing data for logs.

    sudo lvcreate -n apps-lv -L 14G webdata-vg
    sudo lvcreate -n logs-lv -L 14G webdata-vg

<img width="616" alt="image" src="https://user-images.githubusercontent.com/102925329/215859048-4789aa98-56b3-4850-a98f-f600e51243f8.png">

Verify Logical Volume has been created successfully by running: sudo lvs

Next, format the logical volumes with ext4 filesystem:
 
    sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
    sudo mkfs -t ext4 /dev/webdata-vg/logs-lv

Next, create mount points for logical volumes. 
Create /var/www/html directory to store website files: sudo mkdir -p /var/www/html 

and mount /var/www/html on Mount /var/www/html on apps-lv logical volume : sudo mount /dev/webdata-vg/apps-lv /var/www/html/

Then create /home/recovery/logs to store backup of log data: sudo mkdir -p /home/recovery/logs

Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (It is important to backup all data on the /var/log directory because all the data will be deleted during the mount process) Type the following command: sudo rsync -av /var/log/. /home/recovery/logs/

Mount /var/log on logs-lv logical volume: sudo mount /dev/webdata-vg/logs-lv /var/log

Finally, restore deleted log files back into /var/log directory: sudo rsync -av /home/recovery/logs/. /var/log

Next, update /etc/fstab file so that the mount configuration will persist after restart of the server.

The UUID of the device will be used to update the /etc/fstab file to get the UUID type: sudo blkid and copy the both the apps-vg and logs-vg UUID (Excluding the double quotes)

Type sudo vi /etc/fstab to open editor and update using the UUID you copied.

<img width="618" alt="image" src="https://user-images.githubusercontent.com/102925329/215862667-e42edea7-7480-4870-805f-a37238d060f2.png">


<img width="617" alt="image" src="https://user-images.githubusercontent.com/102925329/215871112-c878ae82-1b88-46eb-b5bb-e78c1c7c9fef.png">


<img width="606" alt="image" src="https://user-images.githubusercontent.com/102925329/215872515-78028685-1ddf-4b15-adc9-7bf0607d17ea.png">

Test the configuration and reload the daemon:
      
    sudo mount -a`
    sudo systemctl daemon-reload

Verify your setup by running:  df -h

### Part 2 — Prepare the Database Server

Launch a second RedHat EC2 instance and name it DB Server

Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.

### Part 3 -Install WordPress and connect it to a remote MySQL database server.

Update the repository: sudo yum -y update

Install wget, Apache and it’s dependencies: 
    
    sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json

Start Apache

    sudo systemctl enable httpd
    sudo systemctl start httpd
<img width="615" alt="image" src="https://user-images.githubusercontent.com/102925329/215873136-e241c43f-a267-401c-91bf-01a5c0fe7346.png">


<img width="509" alt="image" src="https://user-images.githubusercontent.com/102925329/215875144-0c56bc55-c770-4305-baf1-19e08e86504a.png">


<img width="566" alt="image" src="https://user-images.githubusercontent.com/102925329/215879409-f463d7a0-4f9e-4ba8-bda4-e5df66953de1.png">
