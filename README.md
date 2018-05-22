# SkyPi Howto

## Skywire Testnet Launch 22-May
The Skywire Testnet is launching on 22-May. Read about new software, and whitelisting process in the official [Skywire testnet release announcment](https://medium.com/@Skycoinproject/skywire-testnet-release-announcement-153583bc9d0e?source=linkShare-3babfcdcbb45-1526854196)

# DIY RasPi Updates
Get ready for the Skywire Testnet and update your DIY RasPi miners (note this proces should work for other DIY platforms also).

Log into each board and run the following cmds (one by one):
```
cd $GOPATH/src/github.com/skycoin/skywire

git reset --hard

git clean -f -d

git pull origin master

cd $GOPATH/src/github.com/skycoin/skywire/cmd

go install -v ./...
```
Following the `go install` cmd, you should see output stating which apps have been rebuilt. At this point you should be updated and can run the Manager and Nodes again. 


## Overview
This howto is intended to provide guidance in setting up `Skywire` from the `SkyCoin` project on a `Raspberry Pi`.

This HOWTO is based on my own research and experience setting up `Skywire` on a `Raspberry Pi 2 B+` and more recently the new `Raspberry Pi 3 B+` using standard `Raspbian OS` running `GoLang 1.9` and building `Skywire` directly from `GitHub` source. This has continued to worked for me (and others) as of `28-Mar-2018`.

I now have a mini cluster of 3 Raspberry Pis running `Skywire Nodes` (and slowly adding to this over time).

Some of my motivations for writing this howto are:
* Learn more about the [SkyCoin project](https://github.com/skycoin).
* Setup a `Skywire` node using a `Rasberry Pi`.
* Share and help others in the community.
* Help to dispell misinformation that this cannot be done - there was a lot out there! Mainly that the Raspberry Pi is 32bit and can’t run Golang 1.9, and therefore Skywire.

**Basically: Learn, Share, Repeat**

## Instructions
### Setup your Raspberry Pi
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
* Password (aleays change this from the default!).
* Enable SSH access - if you havent enabled it already using the `ssh` file. SSH settings
are found in Interfacing Options > SSH. 

**Note:** SSH is a very powerful tool and is used to remotly access your Pi. Do your research and harden your Pi before making it accessable to any public network - I don’t cover how to do this here, but there are many good resources abailable - some have been listed in the Further Reading section below.

### Remove pre-installed Golang.
The following commands can be used to remove any pre-installed versions of `GoLang` from the Pi - seems it may not be installed by default, but no harm in running this anyway:
```
sudo apt-get remove golang
sudo apt autoremove
```

### Install Golang v1.9
Skywire requires `Golang v1.9` or above. Download and install `Golang v1.9` from the official `GitHub` repo for the `Raspberry Pi` architecture using the following command:
```
wget https://storage.googleapis.com/golang/go1.9.linux-armv6l.tar.gz

sudo tar -C /usr/local -xzf go1.9.linux-armv6l.tar.gz
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

Assuming everything went well to this point, you should now have `Golang v1.9` installed on your `Raspberry Pi`. Use the following command to check:
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

***
**Acknowledgements**

Thanks to those in the [Skywire](https://t.me/skywire) and [SPO-Community](https://t.me/WeAreSPACO) Telegram groups, as well as the [Skywug Forum](https://skywug.net/) who have helped to provide feedback to enrich this HOWTO. Lots of helpful people in there - but special thanks to:
* MrHodlr | Systems Integrator | Skycoin
* K. | Systems Integrator | Skycoin
* asxtree
* JohnSmith_h
* BobUltra

***
If you found my tips useful, consider providing a tip of your own ;-)
```
Skycoin:    2aAprdFyxV3bqYB5yix2WsjsH1wqLKaoLhq

SPO:        Wcw8jifS7nXBeHPhdJfBsXqbUHBubUDHAp

BitCoin:    37rPeTNjosfydkB4nNNN1XKNrrxxfbLcMA
```
***