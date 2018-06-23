# SkyPi Howto

## Overview
This howto is intended to provide guidance in setting up and updating`Skywire` from the `Skycoin` project on a `Raspberry Pi`.

This howto is based on my own research and experience setting up `Skywire` on a `Raspberry Pi 2 B+` and more recently the new `Raspberry Pi 3 B+` using standard `Raspbian OS (NOOBs)` running `Go v1.10` and building `Skywire` directly from `GitHub` source. 

This guide has continued to work for me and others as of `25-May-2018`. I endevor to keep the guide current - so please let me know if there are any ommissions, gaps or issues.

I now have a mini cluster of Raspberry Pi's running `Skywire Nodes` and will slowly add more over time.

Some of my motivations for writing this howto are:
* Learn more about the [Skycoin project](https://github.com/skycoin).
* Setup a `Skywire` node using a `Rasberry Pi`.
* Share and help others in the community.
* Help to dispell misinformation that this cannot be done - there was a lot out there! Mainly that the Raspberry Pi is 32bit and can’t run Golang 1.9, and therefore Skywire.

**Basically: Learn, Share, Repeat!**

## Skywire testnet is now live!
The Skywire testnet was launched on 22-May. The following are links to the official announcements (on Medium):
* [Skywire testnet release announcment (21-May)](https://medium.com/@Skycoinproject/skywire-testnet-release-announcement-153583bc9d0e?source=linkShare-3babfcdcbb45-1526854196)
* [Skywire testnet whitelisting and installation manuals (22-May)](https://medium.com/@Skycoinproject/skywire-testnet-whitelisting-installation-manuals-eac7bca63597?source=linkShare-3babfcdcbb45-1527025600)
* [Skywire whitelist application form](https://www.skycoin.net/whitelist/)

The Skywire team have released an official installation guide which I recommend you read and follow (it contains details of new discovery nodes):
* [Skywire Installation Guide v1.0](https://downloads.skycoin.net/skywire/Skywire-Installation-Guide-v1.0.pdf) (Linux section is important for RasPi owners)

# Instructions
Get ready for the Skywire testnet and update your DIY RasPi miners (note this proces should work for other DIY platforms also - however I have not tested it specifically).

* Follow the [Update Go](#Update-Go) section below to ensure you are using the recommended version of `Go`.
* Follow the [Update Skywire](#Update-Skywire) section below to update an existing Skywire installation on RasPi.
* Follow the [Install a new Skywire node](#Install-a-new-Skywire-node) section below to install a fresh (new) RasPi with Skywire (from scratch).
* Follow the [Backup Node Keys](#Backup-node-keys) section below after you complete the whitelist application.

## Update Go
**Note:** This section assumes you already have `Go` installed - and it can be skipped if you dont.

The  official documentation now refers to the need to have `Go v1.10`. While it appears `Go v1.9` still works (for me at least) - it is unclear for how long and what issues may arrise - so always best to follow the official advise.

For those of you already running `Go v1.10`, you can skip this section. If you are unsure log into your node(s) and run the following command
```
go version
```
This will report the version of Go that is currently installed. If it does not report `v1.10` (or greater), follow these steps to update it:
```
cd

wget https://storage.googleapis.com/golang/go1.10.2.linux-armv6l.tar.gz

sudo mv /usr/local/go /usr/local/go-old

sudo tar -C /usr/local -xzf go1.10.2.linux-armv6l.tar.gz

```
The above steps will move your existing `Go` installation into the `/usr/local/go-old` folder and then extract the new version into the `/usr/local/go` folder. This is all that should be required and assuming all goes well you can remove the `go-old` folder at the end.

Test that your version of `Go` has been updated using the following cmd:
```
go version
```

## Update Skywire
The following commands are required to update an already running Skywire node to the latest version of software from the official Github repo. You will need to run these commands on each Pi (node).

```
cd $GOPATH/src/github.com/skycoin/skywire

git reset --hard

git clean -f -d

git pull origin master

cd $GOPATH/src/github.com/skycoin/skywire/cmd

go install -v ./...
```
Following the `go install -v ./...` cmd, you should see output stating which apps have been rebuilt. At this point you should be updated and can run the Manager and Nodes again.

As a further check, you can also get a listing of the files in the `$GOPATH/bin` folder to check their datetime stamp and confirm they were updated. The following command can be used for this:
```
ls -la $GOPATH/bin
```

This will produce a directory listing with the files and their datetime stamps.

At this point, assuming all went well - you should be updated and able to restart you new manager and node software. Again please refer to the [Skywire Installation Guide](https://downloads.skycoin.net/skywire/Skywire-Installation-Guide-v1.0.pdf) (Linux section) for details about the discovery node address to use.

## Install a new Skywire node
The following steps will help you to setup Skywire on a new Raspberry Pi (from scratch):
* Download the latest version of [NOOBS](https://www.raspberrypi.org/downloads/noobs/) from the official `Raspberry Pi` web site.
* Setup an SD Card with NOOBs. Follow the official instructions on the `Raspberry Pi` site.
* Add a file named `ssh` to the root folder of your NOOBs SD card. This will enable SSH by default from the first boot and will allow you to complete a headless setup (no directly connected keyboard and monitor).  
* Boot your `Raspberry Pi` following the [NOOBS Setup Guide](https://www.raspberrypi.org/learning/software-guide/). 

During the initial (first) boot, you will be asked which type of OS you want to install onto your `Raspberry Pi`. For my experiment I selected `Raspbian Lite` as I didnt want the bloat associated with the full desktop version, and also wanted to access my Pi remotely via SSH (headless).

Once Raspbian is installed (this takes a while), log into the console using the default username and password:
```
username: pi
password: raspberry
```
Once logged in, test your Pi is able to access the internet correctly. This appears to be a stumbling point for some and will cause issues later. Use the following command to test your connectivity:
```
curl v4.ifconfig.co
```
The command will make an enquiry to the ifconfig.co server and respond with your public IP. This tests two things:
* Your Pi can access and resolve internet hosts (via DNS).
* Confirm your public IP address

If you get problems with the above cmd line, your problem is most likely your network setup. Everyones network is different, so I don’t cover network setup here.

Next, update the system using the following commands:
```
sudo apt-get update
sudo apt-get upgrade
```
**Note:** Some people have reported issues when trying to install `git` (steps outlined later). Based on feedback, I believe (in some cases) you may need to reboot the `Raspberry Pi` and then repeat the `update` and `upgrade` steps above to complete the update cycle properly.

If you are connected directly to your `Raspberry Pi` (keyboard and HDMI screen) you can reboot it simply by pressing `CTRL+ALT+DEL`

Alternativly, you can issue the following command: 
```
sudo reboot
```

Next run the `Raspberry Pi Configuration` app using the following command:
```
sudo raspi-config
```
Once the Configuration App starts, you should update the following:
* Regional Settings.
* Timezone.
* Keyboard.
* Password (always change this from the default!).
* Enable SSH access - if you havent enabled it already using the `ssh` file. SSH settings
are found in Interfacing Options > SSH. 

**Note:** SSH is a very powerful tool and is used to remotly access your Pi. Do your research and harden your Pi before making it accessable to any public network - I don’t cover how to do this here, but there are many good resources abailable - some have been listed in the Further Reading section below.

### Remove pre-installed Golang.
The following commands can be used to remove any pre-installed versions of `GoLang` from the Pi - seems it may not be installed by default, but no harm in running this anyway:
```
sudo apt-get remove golang
sudo apt autoremove
```

### Install Go v1.10
Skywire (now) requires `Go v1.10` or above. Download and install `Go v1.10` from the official `GitHub` repo for the `Raspberry Pi` architecture using the following command:
```
wget https://storage.googleapis.com/golang/go1.10.2.linux-armv6l.tar.gz

sudo tar -C /usr/local -xzf go1.10.2.linux-armv6l.tar.gz
```

Make sure you are in your users home folder, create your local `Go` environment folders:
```
cd ~
mkdir go
mkdir go/bin
mkdir go/src
```

Add the newly installed `Go` binaries and your build environment folders to your users `PATH`. Edit your `~/.profile` file:
```
nano ~/.profile
```
Add the following to the end of the file:
```
export PATH=$PATH:/usr/local/go/bin
export GOPATH=$HOME/go
export GOBIN=$HOME/go/bin
```

I recommend looking at the official [Golang Settings Guide](https://github.com/golang/go/wiki/SettingGOPATH) for more information on environment variables you should add to your `~/.profile` file to make this work better (`$GOPATH`, `$GOBIN`)

Next you need to update your running environment with the changes you made to your `~/.profile` file by running the following command:
```
source ~/.profile
```

Assuming everything went well to this point, you should now have `Go v1.10` installed on your `Raspberry Pi`. Use the following command to check:
```
go version
```
If you get errors here, something is wrong with your `Go` setup - most likely your paths or environment so check them carefully and make sure you reloaded you profile file using the `source` command.

### Install Git
Use the following command to install Git:
```
sudo apt-get install git
```
### Install Skywire
Finally, follow the  [Skywire Offical Documentation](https://github.com/skycoin/skywire/blob/master/README.md) to clone the `GitHub` repo, build and then run the node using the instructions provided in the `Skywire` documentation.

### Running the Manager and the Node
The official `Skywire` doco on GitHub provides the command lines needed to run both the Manager and the Node. 

Previously this guide contained the commands needed to run both the Manager and the Node, however as the Skywire team may update these cmds from time to time so please follow their instructions:
[Skywire Offical Documentation](https://github.com/skycoin/skywire/blob/master/README.md)

## Backup Node Keys
After building your DIY node and completing the official whitelist application form, i strongly suggest you securely backup your Node keys (and other related config). This section will guide you in doing this.

**Note:** The Node
config files appear to contain seeds and private information. You and you alone are responsible for these. Do not share them and make sure you have taken adequate steps to secure them.

The process outlined here involves the use of an SFTP tool to securly access your node and copy the require files to you machine where you can securly back them up.

Find an SFTP tool you like. Some suggestions:
* Forklift (MacOS)
* FileZilla (Cross Platform)
* scp (cmd line)

With your selected tool, enter your SSH connection details for the Node you want to access. Log into the node and make sure your in the users home folder (normally default).

Copy the entire contents of the `.skywire` folder. There are multiple folders in this folder - but it will get everything needed from your nodes current configuration, including its keys.

**Note:** The `.skywire` folder is located in different places depending on if you run a DIY or an Official. This is likely a result of the autostart scripts used by the Official miner image.
* On a DIY node, you will find the folder in your users Home folder `~/.skywire`
* On an Official node, you will find the folder in the application Bin folder `$GOPATH/bin/.skywire`

Once you have the folder and its contents on your computer back it up and name it meaningfully (so you know which node it came from).

## Setup OpenDNS
For my DIY setup, i use a Ubiquiti EdgeRouterX as the Router. It’s a pretty neat bit of gear - but there is a learning curve. I have set mine up to force all DNS requests to go through OpenDNS, and prevent access to other DNS provoders (via firewall rules). 

The process used for this is described well in this [Youtube Video](https://youtu.be/YYwL6-n0qrU). 

The video uses a different DNS provider - just substitute their DNS server IPs for OpenDNS.

You can then use the OpenDNS dashboard to setup site restriction policies to deny access to specific classes of sites.

## Further Reading
The following are a list of additional reading that will no doubt help you in the setup:
* [Raspberry Pi - Security](https://www.raspberrypi.org/documentation/configuration/security.md)
* [Digital Ocean - SSH Essentials](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys)
* [Linode - Securing Your Server](https://linode.com/docs/security/securing-your-server/)
* [Howto Forge - OpenSSH Best Practices](https://www.howtoforge.com/tutorial/openssh-security-best-practices/)
* [SSH KeyGen Guide](https://www.ssh.com/ssh/keygen/)
* [Skywug Forum](https://skywug.net/)
* [Skywire Telegram Group](https://t.me/skywire)
* [Raspberry Pi Headless Setup](http://www.circuitbasics.com/raspberry-pi-basics-setup-without-monitor-keyboard-headless-mode/)
* [ifconfig.co Command line public IP enquiry service](https://ifconfig.co/)

### SD Card Management
* [Etcher](https://etcher.io/)
* [Raspberry Pi Bakery](http://www.pibakery.org/index.html)
* [Raspberri Pi SD Card Cloning: Linux, Mac, Windows](https://beebom.com/how-clone-raspberry-pi-sd-card-windows-linux-macos/)

Good luck, and let me know how you get on.

# Acknowledgements
Thanks to those in the [Skywire](https://t.me/skywire) and Telegram groups,as well as the [Skywug Forum](https://skywug.net/) who have helped to provide feedback to enrich this howto. 

Lots of helpful people in those groups - but special thanks to:
* MrHodlr | Systems Integrator | Skycoin
* K. | Systems Integrator | Skycoin
* asxtree
* JohnSmith_h
* BobUltra

***
If you found my tips useful, consider providing a tip of your own ;-)
```
Skycoin:    2aAprdFyxV3bqYB5yix2WsjsH1wqLKaoLhq

BitCoin:    37rPeTNjosfydkB4nNNN1XKNrrxxfbLcMA
```
***
