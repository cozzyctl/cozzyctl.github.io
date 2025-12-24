---
title: "Investigating Windows"
categories:
  - htb
tags:
  - HackTheBox
---


## Summary
I haven't touched one of these ctf websites before so I thought I'd go in with a familiar topic, this is classified as easy.
A windows machine has been hacked, its your job to go investigate this windows machine and find clues to what the hacker might have done.
https://tryhackme.com/room/investigatingwindows

## Recon and thought process for answering
Immediately on boot there's an powershell script that's trying to run C:\TMP\p.exe connecting on 10.34.2.3.

There isn't much thought process for the first few questions and are generally all basic commandline or viewing system properties.

For finding the users logon times, you can get a list from net user and then search users individually, since these are local accounts I didn't have the knowledge without searching to go through each user in a single command. Worth noting that I saw Jenny was an admin of the box. You can find the last logon times here for the next questions.

The initial cmd window of the assumed malware shows the connection IP for the following question.

For listing the group of of local admins
net localgroup Administrators

Opening scheduled tasks shows the big red flag of a task named "Clean file system" that's running a script from C:\TMP, which is what the original psexec was running from when the computer boots, but for the sake of this. For looking into the rogue scheduled task, the VM time is natively hidden so using Get-Date in powershell shows the time of 5:xx:xxAM which lines up with 'Clean file system', looking at the actions of the task shows very clearly that there's something funny going on, running a powershell script that listens on port 1348 running nc.ps1. This gives the answers for the following few questions.

For understanding when the compromise happens, you can look at the when the scheduled task was initiated, or you can look at when the C:/TMP folder was created. It's worth noting that you can see inetpub was created around the same time as the tmp folder.

Since we know roughly when the compromise happened, you can filter through the security events in event viewer to find where privileges were assigned to a new logon, in this case I filtered them to 03/02/2019 for most of the day although the scheduled task indicates it happened in the PM. 

With C:\tmp\ being the cause of the compromise, I navigated to the folder and was looking for any logs or indication of what was running. You can find this very plainly in the mim-out.txt file.

For some reason I got stuck on finding the external control and command servers IP, but I ended up looking into the hosts file which is very cleary edited to redirect basically everything to 127.0.0.1 aside from google which had an unusual IP.

I think there's abit of a red herring within the C:/inetpub folder named "shell.gif" which is meant to throw you off for one of the questions, but the answer is within the .jsp files which sent data off when looking at them.

Another tricky one that I underestimated was finding the last firewall rule that was edited, my first thought was to go to eventviewer and filter events for new firewall rules. Unfortunately eventviewer kept crashing on me. So I just manually looked at the windows firewall for any strange firewall rules and found there were rules without groupings on port 8888 and port 1337. I went with port 1337 and it was successful.

For the last question, it was a matter of looking at the hosts file again

## Takeaways
This was classified as easy and it was, although windows was my bread and butter I did have a hard time finding some answers within the VM and with eventvwr crashing on me several times I was a little lost. But as an introduction as to what to expect, and the format of what these labs are. I thought it was insightful.

In terms of doing this write up, I didn't really put all the technical details/commands I used but wanted to document my thought process so I can revisit it myself. I think I wouldn't mind documenting the commands and fact finding process for more technical problems though.

## Follow up
Investigate if there's an easier way to find when firewall rules were last modified..
