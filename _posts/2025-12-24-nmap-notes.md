---
title: "nmap notes"
categories:
  - Blog
tags:
  - tooling notes
---

## Objective
To help develop my learning abit more, I want to start jotting down notes for tooling, commands, triggers etc to use to achieve certain outcomes, for example nmap

## nmap
Triggers:

-sn - Discovery only, using a combination of ICMP, ECHO, REQUEST, TCP SYN to port 443; TCP ACK to port 80; and an ICMP timestamp

-sV - Probes open ports for service version

-sU - UDP scan, shows ports that respond, ports that don't respond and ports that send an ICMP unreachable

-sC - scans using all default scripts of nmap

-sL <ip ip> <x.x.x.x-x ip range> <x.x.x.x/xx CIDR> - list of targets for scanning, this doesn't probe the IP for availability but instead just provides you a list of the IP's that would be scanned in a CIDR or range. More so sanity checking before you run an actual nmap scan on a large range of IP.

-sN <ip ip> <x.x.x.x-x ip range> <x.x.x.x/xx CIDR> - ICMP/ARP discovery of hosts, this can verify in a subnet what is up or responding to ICMP

-Pn <-p portnumber> - skips discovery, assumes all hosts are available for port scan, useful if a host denies pings but has ports available


I've seen some people using "nmap -Pn -sV -sC" as abit of a catch all triage in write ups, after doing some research, my understanding is:
- Skipping host discovery treats the target is up, which helps if ICMP is blocked (-Pn)
- Probes open ports to try triage information about the service/version (-sV)
- Uses the 'safe' scripts packaged with nmap against the discovered services (-sC)
Example: nmap -Pn -sV -sC x.x.x.x

## Example workflow for challenges
So workflow if I had a target IP would be:
- nmap -sn <target ip> - discovery of the host, understand if/what it responds to
- nmap -Pn -sV -sC <target ip> - scans the most common TCP ports and then applies -sV and -sC to whatever it finds open from the -Pn
- If there are strange ports, can use:
- nmap -Pn -p- <target ip> - scans all TCP ports (might be overkill)