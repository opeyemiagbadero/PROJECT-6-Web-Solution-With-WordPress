## Web Solution With WordPress
Step 1 — Web Server

Launched an EC2 instance that serves as a web Server, and created 3 volumes in the same AZ as the Web Server EC2, each of 10 GiB.

![1  Launched an EC2 that will server as a Webserver](https://user-images.githubusercontent.com/79456052/177103706-9bd312a0-f9a6-4e87-a69e-dc2ac8129ed3.png)

![2  Created 3 volumes in the same AZ as the  webserver](https://user-images.githubusercontent.com/79456052/177104969-aa453cbe-7029-4d03-8585-ad1852df3ffa.png)

Confirmed the block devices attached to the server, i.e xvdf, xvdh, xvdg and used df -h command to see all mounts and free spaces on the server.
![5](https://user-images.githubusercontent.com/79456052/177106586-c63374e7-55fa-4016-af14-297dbbce96ff.png)

Used gdisk utility to create a single partition on each of the 3 disks.

![7](https://user-images.githubusercontent.com/79456052/177107157-558c8238-c01b-4701-b7bc-bd676d52bec7.png)

Confirmed the newly configured partiton on each of the disks using the lsblk utility.

![13](https://user-images.githubusercontent.com/79456052/177107918-7dfd02bd-78f9-4cf5-ad59-53f18396bb16.png)

Installed lvm2 package using *sudo yum install lvm2*, used the pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM,
verified that the physical volume had been created successfully by running *sudo pvs* and also used lvcreate utility to create 2 logical volumes. app-lv and logs-lv. NOTE: app-lv was used to store data for the website while logs-lv was used to store data for logs. The lvs was confirmed to be running using the command *sudo lvs*

![15](https://user-images.githubusercontent.com/79456052/177142505-fe9974f6-1f1a-4f84-86ec-830f252000d2.png)

Used mkfs.ext4 to format the logical volumes with ext4 filesystem, i.e  app-lv abd logs-lv logical volumes.

![17](https://user-images.githubusercontent.com/79456052/177154291-71edf65a-fa13-4993-b266-61b2f30ad315.png)



Created /var/www/html directory to store website files and a /home/recovery/logs to store backup of log data. Mounted /var/www/html on app-lv logical volume.

![19](https://user-images.githubusercontent.com/79456052/177155508-4e7851eb-8a4e-45e5-a732-cd979c672e89.png)


Used rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted) This was followed by restoring log files back 
into /var/log directory

![20](https://user-images.githubusercontent.com/79456052/177155820-0f590370-5b63-4698-81ee-3b3a420c9d9c.png)

Created an fstab file in the etc folder (/etc/fstab file) and updated the file with the UUID of the device. (removed the leading and ending quotes)

![22](https://user-images.githubusercontent.com/79456052/177181862-d620ea4e-c7f0-44c2-a9e2-95811b43acaa.png)

Tested the configuration in the /etc/fstab file and reloaded the daemon followed by the verification of the setup by running df -h.

![23](https://user-images.githubusercontent.com/79456052/177156489-27865ed9-3b08-42a3-a2bf-f4ffe4cbd036.png)


Step 2 — Database Server
Launched a second RedHat EC2 instance that serves as a database server, repeated the same steps as for the Web Server, but instead of app-lv created db-lv and mounted it on the /db directory instead of /var/www/html/
![1](https://user-images.githubusercontent.com/79456052/177190542-d2730834-5f35-4bd0-8d2f-1c6af520211f.png)

![2](https://user-images.githubusercontent.com/79456052/177190544-2005ad00-2a6d-41d0-a68b-cc6793b251f0.png)

![3](https://user-images.githubusercontent.com/79456052/177190751-8729e50a-9294-4fd4-ad29-ad8405f4a19b.png)

![4](https://user-images.githubusercontent.com/79456052/177190770-59daf460-eddd-4cec-9cf9-612f79768cb0.png)

![5](https://user-images.githubusercontent.com/79456052/177190792-fd2fe8cb-459e-48c2-9098-dafc815082a3.png)

![6](https://user-images.githubusercontent.com/79456052/177190806-95b645f9-20ba-4f82-9815-a9aa0c940c14.png)


Step 3 — Installation of wordpress on the webserver

Updated the repository and installed wget, apache and it’s dependencies. I started and restarted apache, and confirmed that the apache test page was up by inputting the public ip address of the web server on a browser. I downloaded wordpress, copied wordpress to var/www/html and configured SELinux Policies.

![8  sudo yum update](https://user-images.githubusercontent.com/79456052/177555942-fa31f76b-fa1e-4511-99c8-349a7f45a714.png)

![9](https://user-images.githubusercontent.com/79456052/177555955-bd596fde-fa04-4a31-917d-6ce2cd4eb8f1.png)


![10](https://user-images.githubusercontent.com/79456052/177506089-d40a9a1e-7755-4716-91ea-7bc9e621d8f0.png)


Step 4/5 — Installed MySQL on the DB Server EC2 and configured the DB to work with wordpress. 

![13](https://user-images.githubusercontent.com/79456052/177507339-c5c74425-2c54-43e5-a8ff-f7fb0d98f686.png)


Step 6 — Configured wordpress to connect to remote database

Confirmed MySQL port 3306 on DB Server EC2 was opened, and allowed access to the DB server ONLY from the web server’s IP address. 
Installed MySQL client and tested the connectivity from my web server to the DB server by using mysql-client. Confirmed a listing 
of existing databases using the *SHOW DATABASES;* command

![16](https://user-images.githubusercontent.com/79456052/177543494-18ac7017-1bc5-426f-b5cb-3cfd9c28d5f4.png)


Accessed the link to my WordPress http://34.224.79.116/wordpress/ from my browser, and filed out my DB credentials.
  
![17](https://user-images.githubusercontent.com/79456052/177543516-cd843b3a-a0a7-424f-8b4f-5bb2e1ece58f.png)


![18](https://user-images.githubusercontent.com/79456052/177543563-2c49db22-9673-4a71-97a0-9d2e112969c1.png)







