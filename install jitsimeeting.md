# Install Jitsi Meeting 
Jitsi Meet is a free and open source video conferencing service solution that is packed with various premium features, such as superior sound quality, excellent encryption and privacy, and universal multi-platform availability. With the help of Jitsi Meet, you can easily setup a stunning video conferencing service of your own.

In this tutorial, I will guide you through the process of establishing a video conferencing service on an Ubuntu 18.04 LTS server instance using Jitsi Meet.



## Prerequisites 

* A fresh vultr Ubuntu 18.04 LTS server instance an IPv4 address 111.222.333.444
* A 'sudo user'
* A domain ** abc.example.com ** being pointed to the server instance mentioned above. 
**note:** when deploying on your server instance, be sure to replace all example values with your actual ones.

### Step 1: setup a swap partion 
For a machine with 2GB of memory, it's recommended to setup a 2GB (2048M) swap partition in order to improve system performance.
```
sudo dd if=/dev/zero of=/swapfile count=2048 bs=1M
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile   none    swap    sw    0   0' | sudo tee -a /etc/fstab
free -m 
```
**note:**  If you are using a different server size, the size of the swap partition may vary.
### step 2: setup the machine's hostname and fully qualified domain name(FQDN)
you need to properlys setup a hostname and FQDN for the machine before you cand enable HTTPS security by deploying a Let's Encrypt HTTPS certificate.
the following command will setup a hostname **abc**,and an FQDN, **abc.example.com**, for machine:
```
sudo hostnamectl set-hostname jitsimeet
sudo sed -i 's/^127.0.1.1.*$/127.0.1.1 jitsimeet.example.com jitsimeet/g' /etc/hosts
```
confirm the results: 
```
hostname
hostname -f 
```
### Step 3: Tweak firewall rules for running Jitsi Meet
As required by Jitsi Meet, you need to allow **OpenSSH**, **HTTP**, and **HTTPS** traffic, along with inbound UDP traffic on port **10000** through port **20000**:
```
sudo ufw allow OpenSSH
sudo ufw allow http
sudo ufw allow https
sudo ufw allow in 10000:20000/udp
sudo ufw enable
```
When you see the prompt **Command may disrupt existing ssh connections. Proceed with operation (y|n)?**, input **y** and then press |enter|

### Step 4: Update the system 

For security and performance purposes, its necessary to update the Ubuntu 18.04 LTS system to the latest status:

```
sudo apt update
sudo apt upgrade -y && sudo shutdown -r now
```
During the upgrade, you may be informed that the currently installed version of the grub configuration file has been locally modified. Since we are actually not responsible for the modification, use the |UP| arrow to highlight the **install the package maintainer's version option**, and then press |ENTER|.
After the system reboot, log back in as the same sudo user to move on.

### Step 5: Install OpenJDK Java Runtime Environment (JRE) 8
Jitsi Meet requires Java Runtime Environment. Install OpenJDK JRE 8:

```
sudo apt install -y openjdk-8-jre-headless
```
Having OpenJDK JRE 8 installed, use the following command to verify the result:

```
java -version
```
The output will be similar to the following:
```
openjdk version "1.8.0_171"
OpenJDK Runtime Environment (build 1.8.0_171-8u171-b11-0ubuntu0.18.04.1-b11)
OpenJDK 64-Bit Server VM (build 25.171-b11, mixed mode)
```
In addition, you can setup the **JAVA_HOME** environment variable as follows:
```
echo "JAVA_HOME=$(readlink -f /usr/bin/java | sed "s:bin/java::")" | sudo tee -a /etc/profile
source /etc/profile
```
### Step 6: Install the Nginx web server
In order to better serve Jitsi Meet, you can install an Nginx web server before actually installing Jitsi Meet:
```
sudo apt install -y nginx
sudo systemctl start nginx.service
sudo systemctl enable nginx.service
```
Having Nginx installed, you don't need to manually configure it because the Jitsi Meet installer will deal with this job for you later.

**Note:** If Nginx or Apache is not in place, the Jitsi Meet installer will automatically install Jetty along with the Jitsi Meet program.
### Step 7: Install Jitsi Meet
On a modern Ubuntu or Debian system, you can easily install Jitsi Meet using the official Jitsi deb repo.

First setup the Jitsi repository on your system:
```
cd
wget -qO - https://download.jitsi.org/jitsi-key.gpg.key | sudo apt-key add -
sudo sh -c "echo 'deb https://download.jitsi.org stable/' > /etc/apt/sources.list.d/jitsi-stable.list"
sudo apt update -y
```
Then install the full suite of Jitsi Meet:
```
sudo apt install -y jitsi-meet

```
During the installation, when you are asked to provide the hostname of the current installation, type in the FQDN **abc.example.com** you setup earlier and then press |ENTER|.
When you are asked about the SSL certificate, highlight the **Generate a new self-signed certificate (You will later get a chance to obtain a Let's Encrypt certificate)** option and then press |ENTER|.
Having Jitsi Meet successfully installed, use the following script to apply for a Let's Encrypt SSL certificate:
```
sudo /usr/share/jitsi-meet/scripts/install-letsencrypt-cert.sh
```
uring the process, input your email **admin@example.com** as prompted and then press |ENTER|. This script will automatically handle any tasks related to the Let's Encrypt SSL certificate.
Finally, point your favorite web browser to **http://jitsimeet.example.com** or **https://jitsimeet.example.com** to access your Jitsi Meet Video conferencing service. Feel free to explore the interface. Clicking the **GO** button will immediately create a Video conferencing channel for you.









