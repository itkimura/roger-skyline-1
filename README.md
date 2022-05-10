### roger-skyline-1
A project of Hive Helsinki.
This subject follows Init to learn some of the basic commands and first reflexes in system and network administration. This project is a concrete example on the use of those commands and will let you start your first own web server. 

## Set up VisualBox and Debian
1.  Download debian from the link below
https://www.debian.org/

2.  Open VisualBox and start set up debian

*Choose "Fix size" for hard disk option

<img width="600" alt="Screen Shot 2022-05-10 at 1 42 01 PM" src="https://user-images.githubusercontent.com/61685238/167612186-53dda0c2-1f1e-43ac-99ac-27404c770825.png">

*Choose File location (If you are Hiver and use the school PC, put file under ~/goinfre) and set size 8GB

<img width="600" alt="Screen Shot 2022-05-10 at 1 56 08 PM" src="https://user-images.githubusercontent.com/61685238/167613313-70dab568-ed5f-485e-b709-f0de4dfa83c5.png">

*Choose debian file donloaded in step 1

<img width="600" alt="Screen Shot 2022-05-10 at 1 57 36 PM" src="https://user-images.githubusercontent.com/61685238/167613711-109dce28-b6af-4a96-9777-b97bef1dfd96.png">

## Partition setting
Need to create
    - 4.2GB, primary, beggining, ext4, /
    - 500MB, swap
    - Rest memory, logical, ext4, home/

1. Choose manual for partitioning method
<img width="600" alt="Screen Shot 2022-05-10 at 2 01 34 PM" src="https://user-images.githubusercontent.com/61685238/167614839-ff18a034-a170-4540-a773-2a8c125b47a8.png">

2.  Select SCSI2... to create three partition 

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
apt update sudo
apt upgrade sudo
```
* check the difference <a href="https://linuxconfig.org/apt-update-vs-apt-upgrade">apt update VS apt upgrade</a>

## Partition size and disk size

```
fdisk -l
```
Result:

<img width="587" alt="Screen Shot 2022-05-10 at 2 28 26 PM" src="https://user-images.githubusercontent.com/61685238/167618498-43abec6f-2f75-41de-9bdf-298d67d72cf2.png">

```
sudo apt install parted
sudo parted
unit GB
print all
```
Result:

<img width="569" alt="Screen Shot 2022-05-10 at 2 19 37 PM" src="https://user-images.githubusercontent.com/61685238/167617709-e269961a-aba3-4837-8ec5-364417e933c7.png">

Display TOTAL Hard Disk Size

```
fdick -l | grep Disk
```
Result:

<img width="516" alt="Screen Shot 2022-05-10 at 2 30 19 PM" src="https://user-images.githubusercontent.com/61685238/167618759-563c86bb-94b0-46c3-b6dd-bf533bbe8cd3.png">
