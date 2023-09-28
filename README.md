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
to install it yourself you just have to choose the OS you want to use and the provider will install it for you.

### SSH
#### What is SSH?
SSH stands for Secure Shell. 
It is a cryptographic network protocol for operating network services securely over an unsecured network.
It is used for remote login to a computer and to execute commands on a remote machine.
You can read more about SSH [here](https://en.wikipedia.org/wiki/Secure_Shell).

TLDR: It is a way to connect to a remote machine and execute commands on it. That's how you will interact with your VPS.

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

#### Firewall (not final yet)
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

### Install Docker
#### What is Docker?
