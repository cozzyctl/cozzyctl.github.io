---
title: "hydra notes"
categories:
  - notes
tags:
  - tooling notes
---

## Objective
To help develop my learning abit more, I want to start jotting down notes for tooling, commands, triggers etc to use to achieve certain outcomes, for example Hydra. To begin with i'll be using the tryhackme hydra room to familiarise myself.

https://tryhackme.com/room/hydra
https://en.kali.tools/?p=220

## Hydra
My only understanding of hydra is its name, and the fact it's used for brute forcing passwords. 

It can be used to brute force web forms, but you need to know whether it's a GET or POST being made. Yo ucan see this in the developer tools network tab to see what request types being made.

## Webform

Example of a full command to test a web form that is requesting through POST:

sudo hydra -l <username> -P <wordlist> <ip address> GET/POST "/:username=^USER^&password=^PASS^:F=incorrect" -V

Explanation of the command:
hydra -l <username> - username to brute force
hydra -p <file.txt> - uses password list to test against
http-post-form - POST request
^USER^ - specified username replaces this
^PASS^ - provided passwords will be replacing this
F=incorrect appears as a log for when the login fails

For the lab section of THM, I wasn't sure of what password list to use and found 'rockyou.txt' which is a collection of the most common passwords.

I went to the target IP and just chucked in a dummy username/password and inspected the network to see it was using POST.

Ended up running hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.48.182.127 http-post-form "/:username=^USER^&password=^PASS^:F=incorrect" -V

This gave me the password which I used to login to the webportal and give me the flag.

## SSH

Similar to webform

hydra -l <username> -P <passwordlist> <IP address> ssh

For the second flag I ran:
hydra -l molly -P /usr/share/wordlists/rockyou.txt ssh -V

Password was given through this which I used to ssh into the box and cat on the flag file which gave me the flag.

## Initial thoughts
Was a decent introduction to the tool with a very basic hands on lab to try your knowledge on. 