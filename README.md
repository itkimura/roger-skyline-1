# roger-skyline-1
A project of Hive Helsinki.
This subject follows Init to learn some of the basic commands and first reflexes in system and network administration. This project is a concrete example on the use of those commands and will let you start your first own web server. 

# V.2 VM Part
## Set up VisualBox and Debian
Install a hypervisor of yout choice, I chose VisualBox and Debian.
UTM is a good choice for MacBook with Apple Silicon.

1.  Download debian from the link below
https://www.debian.org/

2.  Open VisualBox and start set up debian

*Choose "Fix size" for hard disk option

<img width="600" alt="Screen Shot 2022-05-10 at 1 42 01 PM" src="https://user-images.githubusercontent.com/61685238/167612186-53dda0c2-1f1e-43ac-99ac-27404c770825.png">

*Choose File location (If you are Hiver and use the school PC, put file under ~/goinfre) and set size 8GB

<img width="600" alt="Screen Shot 2022-05-10 at 1 56 08 PM" src="https://user-images.githubusercontent.com/61685238/167613313-70dab568-ed5f-485e-b709-f0de4dfa83c5.png">

*Choose debian file donloaded in step 1

<img width="600" alt="Screen Shot 2022-05-10 at 1 57 36 PM" src="https://user-images.githubusercontent.com/61685238/167613711-109dce28-b6af-4a96-9777-b97bef1dfd96.png">

### Partition setting
Need to create
*   4.2GB, primary, beggining, ext4, on the root /
*   500MB, swap
*   Rest memory, logical, ext4, on /home

### How to set up
1. Choose manual for partitioning method
<img width="600" alt="Screen Shot 2022-05-10 at 2 01 34 PM" src="https://user-images.githubusercontent.com/61685238/167614839-ff18a034-a170-4540-a773-2a8c125b47a8.png">

2.  Select "SCSI3 (0,0,0) (sda) - 8.6 GB ATA VBOX HARDDISK" to create three partition 
<img width="600" alt="Screen Shot 2022-05-10 at 2 02 04 PM" src="https://user-images.githubusercontent.com/61685238/167615003-2e9bd710-22f9-4ee7-b7f7-508aaab5de50.png">

4. Select swap area for swap partition
<img width="600" alt="Screen Shot 2022-05-10 at 2 03 35 PM" src="https://user-images.githubusercontent.com/61685238/167615395-d6f91936-c9c9-4af6-bbeb-30ee7f92f513.png">

5.  Check to install SSH server and Standard system utilities
<img width="600" alt="Screen Shot 2022-05-10 at 2 10 13 PM" src="https://user-images.githubusercontent.com/61685238/167615627-9f2e429b-1093-4694-a502-8160148f45b9.png">

6.  Remeber to select Yes to install the GRUB boot loader
<img width="600" alt="Screen Shot 2022-05-10 at 2 12 03 PM" src="https://user-images.githubusercontent.com/61685238/167616057-4eeee8b0-03d8-4093-9c8f-011b16a68f59.png">

## Install sudo
```
apt install sudo
sudo apt update
sudo upgrade
```
* check the difference <a href="https://linuxconfig.org/apt-update-vs-apt-upgrade">apt update VS apt upgrade</a>

## Partition size and disk size

### Check Partition size
```
sudo apt install parted
sudo parted
unit GB
print all
```
Result:

<img width="538" alt="Screen Shot 2022-05-16 at 12 03 36 PM" src="https://user-images.githubusercontent.com/61685238/168557841-1cdaec0f-da67-4148-ad05-38709a7509fe.png">


Change pertition size in parted mode
```
resizepart
```

Make new partition by command line in parted mode
```
mkpart
```

# V.3 Network and Security Part
## You must create a non-root user to connect to the machine and work
Add user
```
sudo adduser [username]
```
Login as the user
```
su - [username]
```
## Use sudo, with this user, to be able to perform operation requiring special rights.
### OS update
```
su
apt-get update -y && apt-get upgrade -y
apt-get install sudo vim -y
```
```su``` enters root user
```apt-get update``` downloads the package lists from the repositories and "updates" them to get information on the newest versions of packages and their dependencies. ```apt-get upgrade``` will fetch new versions of packages existing on the machine if APT knows about these new versions by way of ```apt-get update.```  ```sudo apt-get update``` is the command used to updates the package index files to get the latest list of available packages in the repositories, the ```-y``` option will not do anything as as the ```apt-get update``` does not prompt the user for any responses.

Give sudo access to a user
```
usermod -aG sudo [username]
```
Also add write permissions to the ```/etc/sudoers``` file. In the file under # User priviledges information, add the new user under the root user with the following:
```
[username] ALL=(ALL:ALL) ALL
```
## We don't want you to use the DHCP service of your machine. You've got to configure it to have a static IP and a Netmask in \30
### Install ifconfig on Debian 11
```
sudo apt update
sudo apt install net-tools
vim ~/.bashrc
```
And add ```export PATH=/usr/sbin:$PATH``` and run ```source ~/.bashrc```. Then ready to use ifconfig in debian.

### Make static IP
1.  Edit the ```/etc/network/interfaces``` file to setup the primary network. Change the primary network line as
```
# The primary network interface
auto enp0s3
```
2.  Create an ```enp0s3``` file in /etc/network/interfaces.d/ and add:
```
iface enp0s3 inet static
            address 10.1X.0.0
            netmask 255.255.255.252
            gateway 10.1X.254.254
```  
VM is going to be on the same network as the host, so assign ```address``` as ```10.1x.0.0```. X is the cluster number in Hive campus.  
```netmask``` \30 is 255.255.255.252 Check <a href="https://www.pawprint.net/designresources/netmask-converter.php">Netmask Conversions</a>

Restart the network service
```
sudo service networking restart
```
Check the static IP address which you have assigned using ifconfig.

## You have to change the default port of the SSH service by the one of your choice. SSH access HAS TO be done with publickeys. SSH root access SHOULD NOT be allowed directly, but with a user who can be root.
1.  Check if the port number is free
```
lsof -i:424242
```
2.  Edit the sshd config file ```/etc/ssh/sshd_config``` and change default number
```
#Port 22
Port 424242
```
3.  Syntax check of the configuration file
```
sshd -t
```
4.  Restart the sshd searvice ```sudo service sshd restart```
5.  With ssh and you can create an ECDSA key "yes"
```
sudo ssh magic@10.11.1.200 -p 55556 (on VM terminal)
```
6.  Run ssh-keygen to generate a key pair and install the public key on the Virtual Machine OS
```
ssh-keygen
ssh-copy-id [username]@[server IP address] -p [port number]
```

