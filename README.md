# Ultimate Guide To VPS
How to set up a VPS (virtual private server) from 0 to 100, resources and notes for my linux server
# Introduction
The goal of this guide is to help you to set up a VPS from 0 to 100,
with all the necessary tools to self-host services such as a website, mail server,
file server, git server, database server, game server, chat server and more.

## What is a VPS?
A virtual private server (VPS) is a virtual machine sold as a service by a hosting provider.
A VPS runs its own copy of an operating system (OS), and users have root access to that operating system instance,
so they can install almost any software that runs on that OS.
They are priced much lower than an equivalent physical server, 
but as they share the underlying physical hardware with other VPSs,
performance may be lower, and may depend on the workload of other instances on the same hardware node.

# Table of Contents
- [Ultimate Guide To VPS](#ultimate-guide-to-vps)
- [Introduction](#introduction)
- [Table of Contents](#table-of-contents)
- [VPS Providers](#vps-providers)
- [VPS Setup](#vps-setup)

# VPS Providers
Disclaimer: I'm from Germany, it could be possible that some providers are not available in your country.
I'm also not affiliated with any of these providers, I just want to share my experience with them.

I'm really interested to get the most out of my money, so I'm always looking for the best price-performance ratio. 
I have never tried [Hetzner](https://hetzner.com/), but I heard only good things about them,
so if you are looking for a no-brainer, I can also recommend them, but they are a bit pricey.
- [Contabo](https://contabo.com/en/vps/)
- [Netcup](https://www.netcup.eu/vserver/vps.php)
- [Strato](https://www.strato.com/)

# VPS Setup
## Initial Setup

### Operating System
If you are new to Linux, I recommend you to use Debian or Ubuntu,
because they are the most beginner-friendly distributions.
I'll use Debian 12 in this guide.
In general, all providers offer a pre-installed Linux distribution,
so you don't have
to install it yourself you have to choose the OS you want to use and the provider will install it for you.

### SSH
#### What is SSH?
SSH stands for Secure Shell. 
It is a cryptographic network protocol for operating network services securely over an unsecured network.
It is used for remote login to a computer and to execute commands on a remote machine.
You can read more about SSH [here](https://en.wikipedia.org/wiki/Secure_Shell).

TLDR: It is a way to connect to a remote machine and execute commands on it. That's how you will interact with your VPS.

#### Getting started
There are two types, how providers give you access to your VPS:
1. You get a password, and you have to change it on your first login
2. You have to upload a public SSH key to your provider, and you can only connect with your private SSH key

If you get a password, you can skip the following sections and go to [Create a new user](#create-a-new-user), but
you will have to return to this section later to [Generate an SSH Key](#generate-an-ssh-key).

#### What is an SSH Key?
An SSH key is a pair of cryptographic keys
that can be used to authenticate to an SSH server as an alternative to password-based logins.
One key is a public key that you share with others, and the other is a private key that you keep to yourself.
Even if someone gains access to the public key, they won't be able to derive the private key from it.
You can read more about public-key cryptography [here](https://en.wikipedia.org/wiki/Public-key_cryptography).

TLDR: It is a way to authenticate yourself to an SSH server. 
It's more secure than a password, so you should use it. 
Also, many providers require you to use one.

#### Generate an SSH Key
Since my provider requires me to use an SSH key, I will show you how to generate one.
I will use Windows for this.
* Open a terminal and type:
```bash
ssh-keygen
```
* You will be asked to enter a file in which to save the key. 
  You can use for example `C:\Users\Name\.ssh\vps_rsa`.
* You will be asked to enter a passphrase. 
  You can leave it empty, but I recommend you to use one.
* You will be asked to enter the passphrase again.

Now you should have two files in `C:\Users\Name\.ssh\`: `vps_rsa` and `vps_rsa.pub`.
The first one is your private key, the second one is your public key.
Keep in mind that you should never share your private key with anyone in this example `vps_rsa`.
Only share your public key `vps_rsa.pub`.

Now you can upload your public key to your provider.

#### SSH config
SSH config is a nice way to save your SSH settings on your local machine.
It is located in `C:\Users\Name\.ssh\config`.
* Open the file with your favorite text editor.
* Add the following lines:
```bash
Host Name
    HostName IP
    Port 22
    User root
    IdentityFile C:\Users\Name\.ssh\vps_rsa
```
* Replace the values if necessary.
* Save the file and exit the text editor.
* Now you can open your terminal and connect to your VPS with:
```bash
ssh <Name>
```

### Create a new user
It is not recommended to use the root user for everything, so we will create a new user.
Later we will also remove the option to login as root, to increase security.
* Create a new user:
```bash
adduser <Name>
```
* Choose and enter a password for your new user.
* You will be asked to enter some information about your new user. You can just press enter to skip them.
* Verify that your information is correct by typing `Y` and pressing enter.
* Add your new user to the sudo group:
```bash
usermod -aG sudo <Name>
```
* Switch to your new user:
```bash
su <Name>
```
* Navigate to your home directory:
```bash
cd ~
```
* Create a new directory called `.ssh`:
```bash
mkdir .ssh
```
* Navigate to your new directory:
```bash
cd .ssh
```
* Create a new file called `authorized_keys`:
```bash
touch authorized_keys
```
* Open the file with your favorite text editor and paste your public key into it.
* Save the file and exit the text editor.
* Set the correct permissions for the file:
```bash
chmod 600 authorized_keys
```
* Navigate back to your home directory:
```bash
cd ~
```
* Open the file `/etc/ssh/sshd_config` with your favorite text editor:
```bash
sudo nano /etc/ssh/sshd_config
```
* Find the line `PermitRootLogin yes` and change it to `PermitRootLogin no`.
* Find the line `PasswordAuthentication yes` and change it to `PasswordAuthentication no`.
* Save the file and exit the text editor.
* Restart the SSH service:
```bash
sudo service ssh restart
```
* Now you have to adjust your SSH config of your local machine:
```bash
Host Name
    HostName IP
    Port 22
    User <Name>
    IdentityFile C:\Users\Name\.ssh\vps_rsa
```
* Now you should only be able to log in with your new user.
* You can test it by opening your terminal and connecting to your VPS with:
```bash
ssh <Name>
```

### Additional Security
#### SSH
* Open the file `/etc/ssh/sshd_config` with your favorite text editor:
```bash
sudo nano /etc/ssh/sshd_config
```
* Find the line `Port 22` and change it to a custom port.
* With `Port 22` you are using the default port, so it is easier for attackers to find your server. Even though it is not a big deal, because you are using an SSH key, I still recommend you to change it.
* Save the file and exit the text editor.
* Restart the SSH service:
```bash
sudo service ssh restart
```
* Now you have to adjust your SSH config of your local machine:
```bash
Host Name
    HostName IP
    Port <Port>
    User <Name>
    IdentityFile C:\Users\Name\.ssh\vps_rsa
```
* Now you should only be able to log in with your custom port.

#### Firewall (not final yet, skip for now)
* Install `ufw`:
```bash
sudo apt install ufw
```
* Allow your custom SSH port:
```bash
sudo ufw allow <Port>/tcp
```
* Enable `ufw`:
```bash
sudo ufw enable
```
* Check the status of `ufw`:
```bash
sudo ufw status
```

### Install Docker (Advanced users only, and you are interested in docker / want to use it) 
#### What is Docker?
Docker is a set of platform as a service (PaaS) 
products that use OS-level virtualization to deliver software in packages called containers.
Containers are isolated from one another and bundle their own software, libraries, and configuration files;
they can communicate with each other through well-defined channels.
Containers are created from images that specify their contents.
Images downloaded from public repositories.

TLDR: It is a way to run applications in a container, so you don't have to install them on your host system.

#### Install Docker
The best way to install Docker is
to use the official Docker documentation you can find the documentation [here](https://docs.docker.com/engine/install/).
<details>
<summary>If you want to use my commands, that I used to install Docker, you can expand this section. 
</summary>

* Set up Docker's Apt repository.
```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
"deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
"$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
* Install Docker Engine:
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
* Verify that Docker Engine is installed correctly by running the `hello-world` image:
```bash
sudo docker run hello-world
```

</details>

### UFW configuration for Docker
Due to the way Docker manipulates iptables on Linux, you have to make some changes to your firewall configuration. 
If you want to read more about this problem,
you can find some more information [here](https://github.com/chaifeng/ufw-docker).
* Modify the UFW configuration file /etc/ufw/after.rules and add the following rules at the end of the file:
```bash
# BEGIN UFW AND DOCKER
*filter
:ufw-user-forward - [0:0]
:ufw-docker-logging-deny - [0:0]
:DOCKER-USER - [0:0]
-A DOCKER-USER -j ufw-user-forward

-A DOCKER-USER -j RETURN -s 10.0.0.0/8
-A DOCKER-USER -j RETURN -s 172.16.0.0/12
-A DOCKER-USER -j RETURN -s 192.168.0.0/16

-A DOCKER-USER -p udp -m udp --sport 53 --dport 1024:65535 -j RETURN

-A DOCKER-USER -j ufw-docker-logging-deny -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 192.168.0.0/16
-A DOCKER-USER -j ufw-docker-logging-deny -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 10.0.0.0/8
-A DOCKER-USER -j ufw-docker-logging-deny -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 172.16.0.0/12
-A DOCKER-USER -j ufw-docker-logging-deny -p udp -m udp --dport 0:32767 -d 192.168.0.0/16
-A DOCKER-USER -j ufw-docker-logging-deny -p udp -m udp --dport 0:32767 -d 10.0.0.0/8
-A DOCKER-USER -j ufw-docker-logging-deny -p udp -m udp --dport 0:32767 -d 172.16.0.0/12

-A DOCKER-USER -j RETURN

-A ufw-docker-logging-deny -m limit --limit 3/min --limit-burst 10 -j LOG --log-prefix "[UFW DOCKER BLOCK] "
-A ufw-docker-logging-deny -j DROP

COMMIT
# END UFW AND DOCKER
```
* Restart UFW:
```bash
sudo service ufw restart
```


### Install portainer
#### What is portainer?
Portainer is an open-source lightweight management UI that allows you to easily manage your Docker environments.

TLDR: It is a nice UI to manage your Docker containers.

#### Install portainer
The best way to install portainer is to use the official portainer documentation
you can find the documentation [here](https://docs.portainer.io/start/install-ce/server/docker/linux/).

<details>
<summary>
If you want to use my commands, that I used to install portainer, you can expand this section.
</summary>

* First, create the volume that Portainer will use to store its database:
* Create the volume:
```bash
sudo docker volume create portainer_data
```
* Then, download and install the Portainer container:
```bash 
sudo docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```
* Add firewall rules:
```bash
sudo ufw route allow proto tcp from any to any port 9443
```
* Now you can access portainer with your browser at `https://<IP>:9443`.

</details>

### Install nginx proxy manager (not final yet, skip for now)
#### What is nginx proxy manager?
We use nginx proxy manager to manage our reverse proxies and SSL certificates.

#### Install nginx proxy manager
Follow the official Quick Setup [here](https://nginxproxymanager.com/guide/#quick-setup), it should be up to date.

<details>
<summary>
If you want to use my commands, that I used to install nginx proxy manager, you can expand this section.
</summary>

* Create a new stack in portainer.
* Paste the following code into the `Web editor`:
```bash
version: '3.8'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```
* Click on `Deploy the stack`.
* Wait until the stack is deployed.
</details>

* After the stack is deployed, you have to add some firewall rules:
```bash
sudo ufw route allow proto tcp from any to any port 80
sudo ufw route allow proto tcp from any to any port 81
sudo ufw route allow proto tcp from any to any port 443
```
* Now you can access nginx proxy manager with your browser at `https://<IP>:81`.

#### Configure nginx proxy manager and portainer
* Open nginx proxy manager with your browser at `https://<IP>:81`.
* Click on `Proxy Hosts` and then on `Add Proxy Host`.
* Enter the following values for portainer proxy host:
```bash
Details
  Domain Names: portainer.<your domain>
  Scheme: https
  Forward Hostname / IP: IP Address of portainer in portainer (navigate to containers -> portainer -> inspect -> ip address)
  Forward Port: 9443
SSL
  SSL Certificate: Request a new SSL Certificate
  Force SSL: yes
```
* Click on `Save`.
* Now you can access portainer with your browser at `https://portainer.<your domain>`.
* Click on `Proxy Hosts` and then on `Add Proxy Host`.
* Enter the following values for nginx proxy manager proxy host:
```bash
Details
  Domain Names: npm.<your domain>
  Scheme: https
  Forward Hostname / IP: IP Address of nginx proxy manager in portainer (navigate to containers -> nginx proxy manager -> inspect -> ip address)
  Forward Port: 81
SSL
  SSL Certificate: Request a new SSL Certificate
  Force SSL: yes
```
* Click on `Save`.
* Now you can access nginx proxy manager with your browser at `https://npm.<your domain>`.
* Because we now access portainer and nginx proxy manager through the reverse proxy, we don't need the firewall rules anymore:
* List all firewall rules:
```bash
sudo ufw status numbered
```
* Delete the firewall rules:
```bash
sudo ufw delete <number>
```
* Remove the rule for 81, 9443

### Minecraft Server (not final yet, skip for now)
<details>
<summary>
Minecraft Docker Compose
</summary>

```bash
version: "3.9"

services:
  minecraft:
    container_name: valhelsia
    hostname: valhelsia
    image: itzg/minecraft-server
    volumes:
    - /opt/valhelsia/modpacks:/modpacks:ro
    - /opt/valhelsia/data:/data
    - /mnt:/mnt
    environment:
    - EULA=TRUE
    - TYPE=AUTO_CURSEFORGE
    - CF_SLUG=valhelsia-5
    - CF_FILE_ID=modpack id
    - CF_API_KEY=api key
    - TZ=Europe/Berlin
    - MEMORY=4G
    - ENABLE_RCON=true
    - RCON_PASSWORD=secret
    - RCON_PORT=25575
    ports:
      - 25565:25565
      - 25575:25575
    restart: unless-stopped
```


</details>


### Set up a TeamSpeak 3 Server with Docker Compose
* create a folder in your home directory called `teamspeak3`
```bash
mkdir teamspeak3
```
* navigate to your new folder
```bash
cd teamspeak3
```
* create a docker-compose.yml file
```bash
nano docker-compose.yml
```
* paste the following code into the file
```bash
version: '3'
services:
  teamspeak:
    image: teamspeak:latest
    environment:
      TS3SERVER_LICENSE: accept
    ports:
      - "9987:9987/udp"
      - "10011:10011"
      - "30033:30033"
    volumes:
      - ./ts3_data:/var/ts3server/
    restart: always
```
* save the file and exit the text editor
* start the container
```bash
docker-compose up
```
* now you can connect to your teamspeak server with your teamspeak client at `IP:9987`
* use the admin token to get admin permissions, you can find it in the terminal output
* after you have used the admin token, you can stop the container with `CTRL + C`
* now you can start the container in the background
```bash
docker-compose up -d
```
