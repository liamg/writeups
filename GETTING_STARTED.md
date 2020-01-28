# Getting Started with HTB Machine Challenges

This guide assumes you are already an HTB member. If not, head to [the registration challenge](https://www.hackthebox.eu/invite) and try to hack your way through the invite form (there are no "real" invites, this itself is a challenge.)

## Setting up your system

The machine challenges require you to connect to a VPN. I don't know about you, but joining my machine to a network filled solely with hackers makes me a little nervous. For this reason I do all of my HTB work inside a VM. I recommend you install [VirtualBox](https://www.virtualbox.org/) and [create a Kali VM](https://www.youtube.com/watch?v=aXo5dMffjds). [Kali](https://www.kali.org/) is a Linux distribution built for penetration testers and has has a bunch of useful tools baked in and preconfigured, which saves a whole bunch of time.

All instructions from here on are to be run inside the VM!

## Connect to the VPN

Download your [connection pack](https://www.hackthebox.eu/home/htb/access) from HTB. Then run `openvpn *.ovpn` to connect. Even better, add this to a script + alias, so you can run `htbvpn` or similar.

## Start Hacking!

My approach is [documented here](APPROACH.md) if you want a rough starting point.