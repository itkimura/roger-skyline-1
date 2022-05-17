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

### Change Default Machine Folder
Visual Box > Preference > Default Machine Folder:/goinfre/itkimrua

<img width="554" alt="Screen Shot 2022-05-16 at 5 43 20 PM" src="https://user-images.githubusercontent.com/61685238/168619550-88bf54f7-d211-4ae9-b429-06cb5a05b59c.png">

*This allows you to make Clone of VM

## OS update
```
su
apt-get update -y && apt-get upgrade -y
apt-get install sudo vim -y
```
```su``` enters root user
```apt-get update``` downloads the package lists from the repositories and "updates" them to get information on the newest versions of packages and their dependencies. ```apt-get upgrade``` will fetch new versions of packages existing on the machine if APT knows about these new versions by way of ```apt-get update.```  ```sudo apt-get update``` is the command used to updates the package index files to get the latest list of available packages in the repositories, the ```-y``` option will not do anything as as the ```apt-get update``` does not prompt the user for any responses.

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


### Enable DHCP
Visual Box > File > Host Network Manager > Uncheck Enable DHCP Server
<img width="600" alt="Screen Shot 2022-05-16 at 5 11 41 PM" src="https://user-images.githubusercontent.com/61685238/168617668-9aab0572-8c37-4f11-885e-95eea2602669.png">

### Set preferences of the VM
* First interface as NAT
* Host-Only Adapter (chose the host created ins step 1)
<img width="655" alt="Screen Shot 2022-05-16 at 5 34 36 PM" src="https://user-images.githubusercontent.com/61685238/168617680-f23406a2-4629-4759-8bd2-92ac76564bcb.png">
*VM should be powered off by ```sudo poweroff```

### Make static IP
1.  Edit the ```/etc/network/interfaces``` file to setup the primary network. Change the primary network line as
```
# The primary network interface
auto enp0s3
iface enp0s3 inet dhcp
auto enp0s8
iface enp0s8 inet static
            address 192.168.56.2
            netmask 255.255.255.252
```
<img width="804" alt="Screen Shot 2022-05-16 at 5 40 07 PM" src="https://user-images.githubusercontent.com/61685238/168618707-94d87d28-9e8c-40fa-8b7f-faca21162284.png">

*```address``` should be unique
*```netmask``` \30 is 255.255.255.252 Check <a href="https://www.pawprint.net/designresources/netmask-converter.php">Netmask Conversions</a>

Restart the network service
```
sudo service networking restart
```
Check the static IP address which you have assigned using ifconfig.

## You have to change the default port of the SSH service by the one of your choice. SSH access HAS TO be done with publickeys. SSH root access SHOULD NOT be allowed directly, but with a user who can be root.
1. Check if the port number is free
```
lsof -i:4242
```
2. Edit the sshd config file ```/etc/ssh/sshd_config``` and change default number
```
#Port 22
Port 4242
```
3.  Syntax check of the configuration file
```
sshd -t
```
4.  Restart the sshd searvice ```/etc/init.d/ssh restart``` with this, 
5.  Set up SSH access with public keys instead. Run ssh-keygen to generate a key pair. Then install the public key on the Virtual Machine OS with the following syntax and set password.
```
ssh-copy-id [username]@[server IP address] -p [port number]
```
5.  login via ssh from Mac Termonal
```
ssh [username]@[IP address] -p [port number]
```

Result:

<img width="793" alt="Screen Shot 2022-05-16 at 5 53 09 PM" src="https://user-images.githubusercontent.com/61685238/168621564-3a2be2ff-0f06-4529-8f4f-c5ed65d9d9c3.png">

*To disable the root login directly, edit the ```/etc/ssh/sshd_config file```, chainging the PermitRootLogin setting to no

## You have to set the rules of your firewall on your server only with the services used outside the VM.
### Install ufw
Install and set up UFW Firewall:
```
sudo apt install ufw -y 
sudo systemctl enable ufw --now
sudo systemctl status ufw
sudo ufw enable
```
*Disable UFW by ```sudo ufw disable```

*Remove UFW altogether from your Debian system ```sudo apt remove ufw --purge```

*Do not remove UFW unless you have a solid option or know how to use IPTables, especially when running a server environment connected to the public.

### Set up ufw
1. Set up default policies to deny all incoming traffic, and allow all outgoing traffic. The default rules handle traffic that do not explicitly match any other rules.
```
sudo ufw default deny incoming
sudo ufw default allow outgoing
```
2. Add ufw ports to SSH, HTTPS, HTTP 
```
sudo ufw allow 4242     # SSH allows connection from port
sudo ufw allow 80	# HHTPS Web servicem such as Apache, typically listen for HTTP requests on port 80 (You can also replace 80 with http)
sudo ufw allow 443	# Allows https traffic
```
3. Enable the UFW to activate the firewall
```
sudo ufw enable
```
4. To check ufw status:
```
sudo ufw status verbose
sudo ufw show added
```
5. Delete rules
```
sudo ufw status numbered
sudo ufw delete <rule-number-here>
```
6. Check open ports
```
sudo apt update
sudo apt install nmap -y
nmap -A 192.168.56.2
netstat -lntu           #For opened Network ports
```
*https://www.freecodecamp.org/news/what-is-nmap-and-how-to-use-it-a-tutorial-for-the-greatest-scanning-tool-of-all-time

For more information about firewalls:
* https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-debian-10
* https://opensource.com/article/18/9/linux-iptables-firewalld

## You have to set a DOS (Denial Of Service Attack) protection on your open ports of your VM.
### Set up Apache2 webserver
The Apache HTTP Server is a free and open-source cross-platform web server software.
Install with:
```
sudo apt-get install apache2
```
Then test that the webserver is running by entering the ```http://[IP Address]/``` into your browser.
This should give you an Apache2 default webpage,the html for which is located at /var/www/html/index.html
*Check <a href="https://www.layerstack.com/resources/tutorials/Installing-Apache-server-on-Linux-Cloud-Servers">Installing Apache web server on Linux Cloud Servers</a>

### Install fail2ban
fail2ban is a tool protects servers from unauthorised access. Specifically, it monitors the content recorded in log files and, if it finds logs with repeated authentication failures or logs with continuous access, it automatically adjusts the firewall to protect the server from unauthorised access.
```
sudo apt-get install fail2ban
```
### fail2ban setting files
|File name|Description|
|-----|----|
|fail2ban.conf|Specify log storage location and log level|
|filter.d/*.conf|Defines access violations using regular expressions 	|
|action.d/*.conf|Define the action to be taken when an access violation occurs|
|jail.conf|Defines how much (time, number of times) access is blocked. However, this file is not edited. So need to make jail.local to change the setting|

### Set up jail.local
Copy jail.conf to jail.local
```
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```
Set rules in jail.local
```
sudo vim /etc/fail2ban/jail.local
```
|File name|Description|
|-----|----|
|bantime|How long we should drop incoming GET requests for a given IP for, in this case it's 5 minutes|
|findtime|The time period in seconds in which we're counting "retries" (300 seconds = 5 mins)|
|maxRetry|How many GETs we can have in the findtime period before getting narky|

Set sshd rules in jail.local file. Find [sshd] part and comment out the original texts and add:
```
[sshd]

enabled = true
port = 4242
findtime  = 600
maxretry  = 5
bantime   = 900
logpath = %(sshd_log)s
backend = %(sshd_backend)s
```
Add [http-get-dos] in the end of jail.local file.
```
[http-get-dos] 

enabled = true
port = http,https
filter = http-get-dos
findtime  = 300
maxretry  = 300
bantime   = 300
logpath = /var/log/apache2/access.log
action = iptables[name=HTTP, port=http, protocol=tcp]
```
Configule the filter file by ```sudo vim /etc/fail2ban/filter.d/http-get-dos.conf``` and add filter following synatic
```
[Definition]
failregex = ^<HOST> -.*"(GET|POST).*
ignoreregex =
```
Restart ufw and fail2ban
```
sudo ufw reload
sudo service fail2ban restart
sudo systemctl enable fail2ban
```
Check fail2ban status
```
sudo fail2ban-client status sshd
sudo fail2ban-client status http-get-dos
```
Check fail2ban process
```
ps -LC fail2ban-server -o comm,pid,ppid
```
### Test ssh
Make fail ssh logins from the client to the server
```
ssh itkimura@192.168.56.2 -p 4242
```
Result of 5 times fail try:

<img width="554" alt="Screen Shot 2022-05-17 at 1 36 37 PM" src="https://user-images.githubusercontent.com/61685238/168792617-1f74ad05-3004-455f-b53d-f50625ee1bff.png">

Check fail2ban status by ```sudo fail2ban-client status sshd``` and you can see your IP is banned. Result:

<img width="452" alt="Screen Shot 2022-05-17 at 1 38 19 PM" src="https://user-images.githubusercontent.com/61685238/168792856-f3e1a0ad-b85b-44d2-b4e4-c5d9413b667d.png">
Unbanned the IP again
```
sudo fail2ban-client set sshd unbanip 192.168.56.1
```

## You have to set a protection against scans on your VM’s open ports.
Portsentry has the capability to use advanced port-scan detection even for the more sophisticated port scans.

1. Install Portsentry
```
sudo apt update 
sudo apt install portsentry
```


2. Set up portsentry in advanced mode for the TCP and UDP protocols ```sudo vim /etc/default/portsentry```
```
TCP_MODE="atcp"
UDP_MODE="audp"
```
3. Also edit the section Ignore Options to activate blocking with portsentry by ``` sudo vim /etc/portsentry/portsentry.conf```
```
BLOCK_UDP="1"
BLOCK_TCP="1"
```
4. Configure portsentry to drop routes using iptables. Incoming request packets from malicious IP addresses will be dropped using the iptables command with DROP. We are using linux, so will uncomment the line:
```
#KILL_ROUTE="/sbin/iptables -I INPUT -s $TARGET$ -j DROP"
```
5. The above line is the packet filter, which portsentry.conf mentions as the PREFERRED method, and we needed to explicitly uncomment it. The default method uses the route ccommand, and is supposedly the leaast optimal way of blocking and does not provide complete protection against UDP attacks. We can comment out the following line:
```
#KILL_ROUTE="/sbin/route add -host $TARGET$ reject"
```
6. Restart the service and check its status:
```
sudo service portsentry start
sudo service portsentry status
```
7. Check open ports and which application is listening on what port by using ```lsof -i```. Portsentry logs in the file ```/var/log/syslog```, where you can see logs of any attacks. The following command also lists blocked IP addresses via iptables ```sudo iptables -L -n -v```. Also test that portsentry has detected a port scan, by running ```sudo nmap -v -A -sV 10.12.203.42``` from another VM. the ```/var/log/syslog``` should who an attackalert from the attacking host IP, and subsequently dropping the packets from the attacker IP.

## Stop the services you don’t need for this project.
List the service which are enabled at the moment
```
sudo systemctl list-unit-files --type=service | grep "enabled "
```
|Command|Description|
|-----|-----|
|systemctl|Query or send control commands to the system manager|
|list-units [PATTERN...]|List units currently in memory|
|--type=TYPE|List units of a particular type|
|grep " "|Search for PATTERNS in each FILE.|

Stop the service
```
sudo systemctl stop [application].service
```
Disable the service
```
sudo systemctl disable [application].service
```
*Disabling the service will prevent it from starting automatically whenever the machine is launched.

The services which you need for this project
|Name|Description|
|-----|-----|
|apache2.service|Apache Hypertext Transfer Protocol (HTTP) Server|
|apparmor.service|Kernel enhancement to confine programs to a limited set of resources|
|cron.service|Daemon to execute scheduled commands (Vixie Cron)|
|fail2ban.service|A set of server and client programs to limit brute force authentication attempts|
|getty@.service|Opens a tty port, prompts for login and invokes /bin/login command.|
|keyboard-setup.service|Keyboard setting|
|networking.service|Raises or downs the network interfaces configured in /etc/network/interfaces|
|rsyslog.service|Syslog server, for managing logs|
|ssh.service|Open SSH remote login client|
|systemd-timesyncd.service|Used to synchronize the local system clock with a remote network time protocol server.|
|ufw.service|Managing a netfilter firewall|

## Create a script that updates all the sources of package, then your packages and which logs the whole in a file named /var/log/update_script.log. Create a scheduled task for this script once a week at 4AM and every time the machine reboots.
1. Create a script
```
sudo touch /usr/local/bin/update.sh
sudo chmod +x /usr/local/bin/update.sh
```
2. Update script by ```sudo vim /usr/local/bin/update.sh```
```
#!/bin/bash
sudo apt-get update -y >> /var/log/update_script.log
sudo apt-get upgrade -y >> /var/log/update_script.log
```
3. Update crontab file with ```sudo crontab -e``` and add:
```
0 4 * * 0 sudo /usr/local/bin/update.sh
@reboot sudo /usr/local/bin/update.sh
```
*The ```cron``` command-line utility, also known as cron job is a job scheduler on Unix-like operating systems.

*crontab format is```minute hour day(month) month day(week) command```

## Make a script to monitor changes of the /etc/crontab file and sends an email toroot if it has been modified. Create a scheduled script task every day at midnight.

1. Install mailx command
```
sudo apt install mailutils
```
*mailx is a Mail User Agent program which is a console application that is used for sending and receiving emails
2. Create a script
```
sudo touch /usr/local/bin/monitor_cron.sh
sudo chmod +x /usr/local/bin/monitor_cron.sh
```
3. Update script by ```sudo vim /usr/local/bin/monitor_cron.sh```
```
#!/bin/bash
if [ ! -e /etc/crontab.back ]
then
	exit 0
fi
DIFF=$(diff /etc/crontab /etc/crontab.back)
sudo bash -c "cat /etc/crontab > /etc/crontab.back"
if [ "$DIFF" != "" ]
then
	echo "crontab monitor: change detected, alerting root!"
	echo "$DIFF" | mailx -s "crontab monitor: change detected" root
fi
```
4. Update crontab file with ```sudo crontab -e``` and add:
```
0 0 * * * sudo /usr/local/bin/monitor_cron.sh
```
5. To read the mail, execute ```mailx```
# VI.1 Web Part

### Adjusting the Firewall
Before testing Apache, it’s necessary to modify the firewall settings to allow outside access to the default web ports. 
List the ufw application profiles by typing:
```
sudo ufw app list
```
You will receive a list of the application profiles. As indicated by the output, there are three profiles available for Apache:

|App name|Description|
|---|---|
|Apache|This profile opens only port 80 (normal, unencrypted web traffic)|
|Apache Full|This profile opens both port 80 (normal, unencrypted web traffic) and port 443 (TLS/SSL encrypted traffic)|
|Apache Secure|This profile opens only port 443 (TLS/SSL encrypted traffic)|

It is recommended that you enable the most restrictive profile that will still allow the traffic you’ve configured. Since we haven’t configured SSL for our server yet in this guide, we will only need to allow traffic on port 80:
```
sudo ufw allow 'Apache'
```
You can verify the change by typing:
```
sudo ufw status
```
### Create the SSL Certificate

Create the SSL certificate with the ```openssl``` command:
```sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt```
|Command|Description|
|-----|-----|
|openssl|This is the basic command line tool for creating and managing OpenSSL certificates, keys, and other files.|
|req|This subcommand specifies that we want to use X.509 certificate signing request (CSR) management. The “X.509” is a public key infrastructure standard that SSL and TLS adheres to for its key and certificate management. We want to create a new X.509 cert, so we are using this subcommand.|
|-x509|This further modifies the previous subcommand by telling the utility that we want to make a self-signed certificate instead of generating a certificate signing request, as would normally happen.|
|-nodes|This tells OpenSSL to skip the option to secure our certificate with a passphrase. We need Apache to be able to read the file, without user intervention, when the server starts up. A passphrase would prevent this from happening because we would have to enter it after every restart.|
|-days 365|This option sets the length of time that the certificate will be considered valid. We set it for one year here.|
|-newkey rsa:2048|This specifies that we want to generate a new certificate and a new key at the same time. We did not create the key that is required to sign the certificate in a previous step, so we need to create it along with the certificate. The rsa:2048 portion tells it to make an RSA key that is 2048 bits long.|
|-keyout|This line tells OpenSSL where to place the generated private key file that we are creating.|
|-out|This tells OpenSSL where to place the certificate that we are creating.|
### Useful links
* <a href="https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-20-04">How To Install the Apache Web Server on Ubuntu 20.04</a>
* <a href="https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-apache-in-ubuntu-16-04">How To Create a Self-Signed SSL Certificate for Apache in Ubuntu 16.04</a>
* <a href="https://stackoverflow.com/questions/51537084/i-installed-apache-2-but-in-sudo-ufw-app-list-there-is-no-apache-applications-in">i installed apache 2 but in sudo ufw app list there is no apache applications in the app list</a>
# VI.2 Deployment Part
