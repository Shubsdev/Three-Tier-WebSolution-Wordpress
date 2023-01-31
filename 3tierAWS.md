 # Three Tier Web Solution with Wordpress

 ### Part 1: Configure storage subsystem for Web and Database servers based on Linux OS.
Steps

Launch an EC2 instance that will serve as “Web Server”. Create 3 volumes in the same Availability Zone as your Web Server EC2, each of 10 GiB.

Attach all three volumes one by one to your Web Server EC2 instance

Open up the Linux terminal to begin configuration of the instance. Use lsblk command to inspect what block devices are attached to the server. The 3 newly created block devices are names xvdf, xvdh, xvdg

Use df -h command to see all mounts and free space on your server

Use gdisk utility to create a single partition on each of the 3 disks sudo gdisk /dev/xvdf

A prompt pops up, type n to create new partition, enter no of partition(1), hex code is 8e00, p to view partition and w to save newly created partition.

Repeat this process for the other remaining block devices.

Type lsblk to view newly created partition.

<img width="543" alt="image" src="https://user-images.githubusercontent.com/102925329/215853016-024d84ee-0c8b-4a99-9409-03fe1ebf715f.png">



<img width="613" alt="image" src="https://user-images.githubusercontent.com/102925329/215858335-270b7033-c1d9-4eec-b236-edc1aa12bfc9.png">




<img width="616" alt="image" src="https://user-images.githubusercontent.com/102925329/215859048-4789aa98-56b3-4850-a98f-f600e51243f8.png">
