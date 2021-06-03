# dckvpn
Builds a docker image based on 'fedora', then uses it as a selective gateway to a variety of concurrently open VPN Clients.

**Author**: Mirko Graziani (mirkobau)\
**Name**: dckvpn\
**Release**: 20210603, from the dining table.\
**Language**: bash scripting\
**Intended use**: technical users (FIXME: make a YT video for making this usable by everyone)\
**Requires**: bash, docker container system, openssl, GNU coreutils.\

## How to use this script
1. Install an hypoervisor FIXME CONTINUE HERE 20210603
First, you'll have to build a VM.

### Note1 - for Windows 8/10 (and beyond?)
Windows 10's network routing strategy is a great system..for domesticated monkeys.

So if you're such that evolved animal that you expect to get what you typed in your routing table..

..then you'll need to cope a couple of minutes with gpedit.msc.\
So, after opening it:

1. Go to: *Computer Configuration*\*Administrative Templates*\*Network*\*Windows Connection Manager*
1. Set '*Minimize Number of Simultaneous Connections to Internet or a Windows Domain*' to "**Enabled**" and "**0**".
1. Reboot
1. Enjoy it, my little rascal: you're free now.

### Note2 - FIXME: option to choose from other docker images
But hey, after a quick search this was the only one I found being able to install all required packages without any kind of magic tricks.

And honestly, if it works and this all is about docker, then I don't feel portability as an essential issue.

Ciao!
