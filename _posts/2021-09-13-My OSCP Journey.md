---
title: My OSCP Journey
tags: OSCP
article_header:
  type: 
  image:
   #src: 
sidebar: 
  nav: blogs
---


## My OSCP Journey 

Hey guys, I am happy to share that I recently cleared the OSCP exam, during my preparation period I read tons of blogs about the exam that helped me a lot in my preparation, and as a gesture of giving back to the community I try to do the same through this blog. It all started on the day I passed the eJPT exam by eLearnSecurity.

### April 2021
I passed the eJPT exam and I wasn't feel quite satisfied , i felt that eJPT was way too easy than what I expected and it wasn't quite a challenge ( not to discourage anyone, it solely depends upon your prior knowledge ). I knew I had some basic knowledge about pentesting, but I wasn't quite there yet, I didn't have any privesc knowledge, no buffer overflow, never touched the windows command prompt, and all those advanced concepts. I knew I had to work a lot and with the things that were happening at that time, I wasn't quite sure if I can rely on someone who could teach me all this, hence I decided to do it all by myself and so the preparation began. 

### Enumeration/Preparation
I knew I wanted to do OSCP and was willing to put it all in, but how ? 
My strategy was to download the syllabus, go through the entire syllabus like a checklist, check the topics that I am familiar with and cover the topics that I haven't done before. 

Since I already had some programming, networking, linux, cryptography knowledge I moved onto Linux services, I covered the most common services like (NFS, SMTP, SMB, telnet, SNMP, etc) from setting them up on my local machine to pentesting them, I covered it all. 

![t](/Images/ssh_oscp.png){:.shadow}

Once i felt i had enough knowledge about getting initial access, i moved onto privilege escalation, doing privesc was a lot of fun, I really enjoyed all the exercises, i used the following platform/courses.

- [Tryhackme](https://tryhackme.com/)
- [Tib3rus Privesc](https://hackersacademy.com/courses/privilege-escalation-oscp-windows-linux-bundle?coupon=TWITTER)
- [thecybermentor](https://academy.tcm-sec.com/courses?query=privilege+escalation)

After completing these courses, I had developed enough knowledge to do some of the easy machines on tryhackme. I did around 8-10 machines from tryhackme and then I moved onto Vulnhub.

### Pwn Pwn - 1
I started solving VulnHub machines, my plan was to do around 50 machines, with one machine each day,  i was successfully able to execute this plan and ended up rooting more than 60 machines. 
Now this sounds very easy but it wasn't that easy, i had to go through all the disappointment, looking up writeups, getting exhausted, feeling demotivated etc, but i stayed persistent.
A lot of people feel guilty when they refer to writeups to solve a box, but there's nothing to be guilty about as `you can't do something, you don't know about` and in the end, it's about learning, so as long you are discovering/learning new ways/concepts of solving machines, it's cool, but if it's something you've already practiced and still wasn't able to execute it, then it's a problem, the best way to deal with this is to maintain a checklist of all the checks to look for, once the list is exhausted,  you can move onto looking up writeups.

### June 2021
By now i felt much more confident about my Linux exploitation skills, but OSCP is not just `Linux Exploitation`, there was another monster waiting for me. The **BUFFEROVERFLOW** and then **Windows Exploitation/Privesc**.

#### Bufferoverflow
Since i already had some basic experience in assembly language, understanding registers was not much difficult, but understanding how the overflow occurs, nop sleds, finding bad characters, etc, was. I invested two weeks in BOF, watched a ton of videos, read research papers, read blogs, and finally, i was done with BOF too. The primary resources were

- [Buffer Overflow For Dummies](https://www.sans.org/white-papers/481/)
- [dostackbufferoverflowgood](https://github.com/justinsteven/dostackbufferoverflowgood)
- [The Cyber Mentor BOF Playlist](https://youtube.com/playlist?list=PLLKT__MCUeix3O0DPbmuaRuR_4Hxo4m3G&si=3zZAIFDeBCNKs9xr)

I was still left with Windows Exploitation and Privesc, but I couldn't resist, so I ended up taking the PWK course, and for windows, i said to myself `Jai Bhadrakali`( if you know, you know ).

### PWK
PWK labs were fun, I enjoyed solving all the machines, pwned my first windows machine there, learned a lot about windows exploitation and privesc, the BOF exercises were good, the PDF content was moderate (since I already covered most of the topics, it wasn't a problem). The videos were fine ( it's just a guy reading out things from the PDF and demonstrating stuff ). For some topics PDF worked fine, for some topics i did videos and for some topics, I did both. Once done with the labs, i moved on to HTB ( HackTheBox ).

### Pwn Pwn - 2
Doing HTB machines, allowed me to re-enforce the topics that i learned in PWK labs and as a result, i was getting better at chaining vulnerabilities, i did around 50 machines. By now i was kinda tired, i didn't want to do any more machines, i just wanted to give the exam and get the thing I've been working on for months.

### Pre-Exam Enumeration
Knowing that people have failed the OSCP exam multiple times, i didn't want to take any chance, so the first thing i did is to go out on the internet and read blogs of people who have failed the OSCP exam, i read tons of blogs/Reddit posts/discord chats, etc and made note of all that and made sure that i don't repeat any of these mistakes, doing i reduced my chances of failure. 

Three days before the exam, i had to shift to some other place due to some reason, two days before the exam, I did nothing new, just prepared my report template and went through my notes again.

### The Exam
Doing the OSCP exam was a hell of an experience, the initial verification process was pretty smooth and I was able to start within 15 minutes of my scheduled time.

- Completed BOF+Writeup in the first hour.
- Rooted 20 Pointer machine in next 3 hours
- Another 20 Pointer machine in next 3 and a half hour.
- 10 pointer machine in the next one hour.

Twelve hours in and i had enough points to pass the exam, since i was already tired i asked the proctor for a break and i went to sleep for next 6 hours, waking up i connected back, went through my report again, to make sure i had everything in place, tried few things with the 25 pointer, but couldn't find anything, finally, the exam was over.

#### Post-Exam
Since i already had the template ready, creating the report was not a problem, i submitted the report and got the mail the next day/.

![t](/Images/Screenshot 2021-09-13 at 5.56.36 PM.png){:.shadow}


### Some Questions
- **How difficult is the OSCP exam ?**

It really depends on your experience, practice and the amount of work you put in, for me the more you practice, the easier it gets. In one of the interview i was asked, how were you able to pass OSCP in college itself even though people with years of experience fail. My answer "Hard Work & Perseverance".

- **How many machines did you solve in total ?**

I don't remember the exact figure but i did more than 150 machines.

- **What was your exam strategy ?**

My strategy was to start the Nmap scans in the background and start doing the BOF, after BOF i planned to solve the next 25 pointer, but since the scan for that machine was not finished yet, i had to move on the 20 pointer.
  

  ```bash
  Plan : `25 - 25 - 20 - 20 - 10` 
  Execution : `25 - 20 - 20 - 10`
  ```