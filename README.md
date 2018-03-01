# SkyPi Howto
## Overview
This howto is intended to provide guidance in setting up `Skywire` from the `SkyCoin` project on a `Raspberry Pi`.

This howto is based on my own research and experience setting up `Skywire` on a `Raspberry Pi 2 B+` using standard `Raspbian OS` running `GoLang 1.9` and building `Skywire` directly from `GitHub` source.  This all worked for me as of `31-Jan-2018`.

Some of my motivations for writing this howto are:
* Learn more about the [SkyCoin project](https://github.com/skycoin).
* Setup a `Skywire` node using a `Rasberry Pi`.
* Help others who are also following the project.
* Help to dispell misinformation that this cannot be done - there was a lot out there! Mainly that the Raspberry Pi is 32bit and cant run Golang 1.9, and therefore Skywire.

## Instructions
### Setup your Raspberry Pi
* Download the latest version of [NOOBS](https://www.raspberrypi.org/downloads/noobs/) from the official `Raspberry Pi` web site.
* Setup an SD Card and boot your `Raspberry Pi` following the [NOOBS Setup Guide](https://www.raspberrypi.org/learning/software-guide/)

During the initial (first) boot, you will be asked which type of OS you want to install onto your `Raspberry Pi`. For my experiment I selected `Raspbian Lite` as I didnt want the bloat associated with the full desktop version, and also wanted to access my Pi remotely via SSH (headless).

Once Raspbian is installed (this takes a while), log into the console using the default username and password:
```
username: pi
password: raspberry
```
Once logged in, update the system using the following commands:
```
sudo apt-get update
sudo apt-get upgrade
```
You might need to reboot your Pi after this - depending on what was updated. Easiest way to do this is with `CTRL+ALT+DEL`.

Next run the `Raspberry Pi Configuration` app using the following command:
```
sudo raspi-config
```
Once the Configuration App starts, you should update the following:
* Regional Settings.
* Timezone.
* Keyboard.
* Password (suggest changing this from the default!).
* Enable SSH access (Do your research and harden your Pi before making it accessable to the public internet - I dont cover how to do this here).

### Remove pre-installed Golang
Use the following commands to remove any pre-installed versions of `GoLang` from the Pi:
```
sudo apt-get remove golang
sudo apt autoremove
```

### Install Golang v1.9
Skywire requires `Golang v1.9`. Download and install `Golang v1.9` from the official `GitHub` repo for the `Raspberry Pi` architecture using the following command:
```
wget https://storage.googleapis.com/golang/go1.9.linux-armv6l.tar.gz
sudo tar -C /usr/local -xzf go1.9.linux-armv6l.tar.gz
```

From your users home folder, create your local `Go` environment folders:
```
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

I recommend looking at the official [Golang Settings Guide](https://github.com/golang/go/wiki/SettingGOPATH) for more information on environmental variables you should add to your `~/.profile` file to make this work better (`$GOPATH`, `$GOBIN`)

Next you need to update your running environment with the changes you made to your `~/,profile` file by running the following command:
```
source ~/.profile
```

Assuming everything went well to this point, you should now have `Golang v1.9` installed on your `Raspberry Pi`. Use the following command to check:
```
go version
```

### Install Git
Use the following command to install Git:
```
sudo apt-get install git
```
### Install Skywire
Finally, follow the  [Skywire Offical Documentation](https://github.com/skycoin/skywire/blob/master/README.md) to clone the `GitHub` repo, build and then run the node using the instructions provided in the `Skywire` documentation.

### Running the Manager and the Node
The `Skywire` doco above provides the command lines needed to run both the Manager and the Node. It suggests running from two seperate shell terminals. This is ok when your testing, but if you want your rig to run perminantly in the background without needinf to leave you shell session open, use the following commands:

```
nohup ./manager -web-dir ${GOPATH}/src/github.com/skycoin/skywire/static/skywire-manager &
nohup ./node -connect-manager -manager-address :5998 -manager-web :8000 -discovery-address messenger.skycoin.net:5999 -address :5000 -web-port :6001
```
This tells the processes not to hangup (`nohup`) when you log out of the shell, and to place the process into the background (`&`) (not interactive with the command line).

### Further Reading
There has been a lot of discussion recently around setup of secure, hardend `SSH` servers for management of `Skywire` nodes in the Skywire relared Telegram groups. A lot of thanks to users like `@asxtree` for contributions to this discussion and write-ups on the [Skywug](https://skywug.net/) forums.

The following are a list of additional reading that will no doubt help you in the setup:
* https://www.raspberrypi.org/documentation/configuration/security.md
* https://linode.com/docs/security/securing-your-server/
* https://www.howtoforge.com/tutorial/openssh-security-best-practices/
* https://www.ssh.com/ssh/keygen/
* [Skywug Forum](https://skywug.net/)
* [Skywire Telegram Group](https://t.me/skywire)

These should give you a fairly good grounding in `SSH` and whats required to harden the the server.

Good luck, and let me know how you get on.


```
If you found my tips useful, consider providing a tip of your own ;-)

SkyCoin:    2aAprdFyxV3bqYB5yix2WsjsH1wqLKaoLhq
Spaco:      Wcw8jifS7nXBeHPhdJfBsXqbUHBubUDHAp
BitCoin:    37rPeTNjosfydkB4nNNN1XKNrrxxfbLcMA
```