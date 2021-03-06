# dckvpn
Builds a docker image based on 'fedora', then uses it as a selective VPN gateway to a variety of multiple concurrently open VPN Clients.

With "a veriety of" meaning these clients and VPN types:
- charon-cmd by StrongSWAN, for
  - IPSEC ikev1
  - IPSEC ikev2
- OpenVPN
- OpenConnect for protocols:
  - Palo Alto Networks (PAN) GlobalProtect SSL VPN
  - Cisco AnyConnect SSL VPN
  - (still to implement) Pulse Connect Secure SSL VPN
  - (still to implement) Juniper Network Connect
- OpenFortiVPN for FortiNet's FortiClient SSL VPN



The final purpose is to have, for example, a VPN Client constantly connected to your company, so you'll be able to reach the Domain Controller, the File Server, and the Password Server.\
This script will allow you to specify those server's single IP addresses.\
This way you'll not expose the entire company, while still having an excellent usability.

On the other side, while still being connected to your company, you can open a second VPN Client to your Customer's network.\
But again, you don't need all those subnets that their gateway pushes to you as a split tunnel.\
In fact, you've been charged to deploy a single server, and that's all you need to do your work.

This script lets you isolate the VPN Client in a docker container, then you'll route your traffic to a very restricted range of target IP Addresses.

This script is **NOT** configured with security in mind, but in a certain manner it helps you isolate networks while still having a good compromise between usability and isolation (hence, a security aspect). 

**Author**: Mirko Graziani (mirkobau)\
**Name**: dckvpn\
**Release**: see inside the script.\
**Language**: bash scripting\
**Target users**: technical users (FIXME: make a YT video for making this usable by everyone)\
**Requires**: bash, docker container system, openssl, GNU coreutils.\

## How to put this script at some use

1. Install an hypervisor with host-only network interfaces capability. \
(I used Oracle VirtualBox)
2. Build a basic linux VM (oh, c'mon, **don't** ask me how to do it)
3. Set an host-only Virtual NIC with IP address in link-local address space (see: https://www.iana.org/assignments/iana-ipv4-special-registry/iana-ipv4-special-registry.xhtml ).\
Say, we'll give 169.254.86.1/30 for the VM and 169.254.86.2/30 to VirtualBox Network Card on the Windows side.\
This will be the direct channel between your Windows host and the Linux VM.\
It'll be a default route for your personalized VPN networks, too.\
\
(*if you're wondering about the meaning of that '86' in IP Address, here's your answer: 169+254+86+1+2 = 512 = 2^9 -- a power of two.   I always make these stupid little tricky things, like a mantra that remembers you that computers have **nothing** to share with powers of 10*)
4. Set a second interface to your Linux VM, configured as 'NAT'.\
This will be the default gateway of your Linux VM.
6. Put 'dckvpn' in a runable directory.\
Say, /usr/local/bin, or /root/bin (you'll probably need to create this dir).
6. Don't forget to `chmod 700 dckvpn` and to set `root:root` as owner.
7. Run dckvpn and follow the USAGE below.

## USAGE
If no arguments are given dckvpn will ask for a set of things before actually running the docker image and launching the VPN client.\
Otherwise, you can specify as first arg:

- the VPNNAME
- 'list' (or 'ls' or 'ps')\
to get a list of open VPN Clients (each one in a corresponding docker container)
- 'kill' and a VPNNAME\
to kill a specific container, this ways shuttind down the VPN.

#### VPN Name
First of all, you'll need to define a VPN Name.\
This will also be docker's VPN Name and settings filename.

#### Routes
This is the focal point of the whole script.

Here you actually define a space-separated list of subnets, instead of complete route definitions.\
But it's called 'Routes'.\
Meh. Whatever.

"Routes" (say: subnets) defined here will be routed (a-ha! gotcha!) through the VPN tunnel you're defining.\
All this assuming your VPN Clients **has** the right to reach the subnets you're defining here.

#### VPN Type
Since there's no help, the list of available types can be taken directly from the last part of the script, under the 'case..esac' context.

Sorry.

#### VPN IP
Well, it depends.

On certain VPN Clients this is actually an IP Address, while other ones will need a FQDN and optionally a port number.

#### IPSEC (ikev1 and ikev2)
For IPSEC VPN Type, you'll be asked for extra-parameters: Peer ID, Phase1 and Phase2.

Gateway's identity is set to '%any' (we use charon-cmd as VPN Client), so you'll not need to define it here.

Phase1 and Phase2 are in the form: CIPHER-HASH-DHGROUP.\
For example: AES266-SHA256-DH19.

DH Group will automatically be converted to the corresponding cipher suite (ref.: https://wiki.strongswan.org/projects/strongswan/wiki/IKEv1CipherSuites).
