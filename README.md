# Ultimate Guide To VPS
How to set up a VPS (virtual private server) from 0 to 100, resources and notes for my linux server
# Introduction
The goal of this guide is to help you to set up a VPS from 0 to 100,
with all the necessary tools to self-host services such as a website, mail server,
file server, git server, database server, game server, chat server and more.
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
