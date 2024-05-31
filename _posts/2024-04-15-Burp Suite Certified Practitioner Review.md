---
title: Burp Suite Certified Practitioner Review
tags: BSCP BurpSuite Appsec
article_header:
  type: 
  image:
   #src: 
sidebar: 
  nav: blogs
---

Hello hackers, I'm thrilled to announce that I recently passed the Burp Suite Certified Practitioner Exam by PortSwigger Labs, and it was nothing less than amazing. It's kinda like OSCP, but for web application pentesting. Now, before you start throwing words at me about how can i compare the two, here are a few facts on basis of which i say that.

- Both are based on black-box testing.
- Both certifications have a steep learning curve.
- Both are beginner-friendly, provided you put in the time and effort.

The places where these two differs are :

| **Metric** | OSCP               | BSCP                            |
| ---------- | ------------------ | ------------------------------- |
| **Time**       | 24hrs              | 4hrs                            |
| **Reporting**  | Report Required    | No Report Required              |
| **Category**   | General Pentesting | Core Web Application Pentesting |

Even though BSCP isn't exactly like OSCP—because there are no rabbit holes as such, and you don't have to find hidden parameters or deobfuscate JavaScript files like you do in OSCP, which also makes the BSCP exam less CTF-like. The exam is challenging, especially due to the 4-hour time constraint and the vast syllabus, but doable. You can read more about BSCP [here](https://portswigger.net/web-security/certification). Now that's out of the way, let's talk about my journey.

## 7 November 2023 : init

Ever since OSCP, i didn't really aim for any challenging certificate in terms of time, content and complexity. As a part of my new role, i was slowly transitioning from network security to application security, and since i love to do certs, i started looking for certificates around Web Application Pentesting. I came across CBBH ( by HTB), BSCP (by PortSwigger Academy) & OSWE (by offensive security). Comparing the three, it boiled down to two: BSCP and OSWE. The reason I chose BSCP over OSWE was because OSWE involved source code review aka White Box Testing, which I wasn't planning to do at that time.

Reading more about BSCP got me fascinated, as it covered some of the most complex web application vulnerabilities, including HTTP Request smuggling, Web Cache Poisoning, Insecure Deserialization, Blind SQL Injections, OAST techniques, etc. The other part that really challenged me was that people were failing this exam a lot. Every blog I read about BSCP reviews mentioned failing the first attempt. There was even a blog that went to the extent of saying, 'Be ready to fail your first attempt,' and this caught my attention. Hence, the preparation started.

## Starting From Scratch

Even though I had been doing PortSwigger labs from time to time, this time I decided to start fresh. So, I created a new account and went through every topic one by one in the following sequence.

1. **Server Side Vulnerabilities**
  - Access Control
  - Path Traversal
  - Authentication Attacks
  - SSRF
  - File Upload Vulnerabilities
  - OS Command Injection
  - Business Logic Vulnerabilities
  - Information Disclosure
  - Race Conditions
  - XXE Injection
  - SQL Injection

2. **Client Side Vulnerabilities** 
  - XSS
  - CSRF
  - CORS
  - ClickJacking
  - WebSockets

5. **Advanced Topics**
  - Insecure Deserialisation
  - SSTI
  - Web Cache Poisoning 
  - OAuth Authentication
  - HTTP Host Header Attacks
  - JWT Attacks
  - HTTP Request Smuggling
  - OAuth

![t](/Images/BSCP/Pasted image 20240414093926.png){:.shadow}

As mentioned by PortSwigger Academy in the exam guide, I completed almost all the labs on these topics, including practitioner and a few expert labs. I made extensive notes, revised them, and moved forward.

## 1 February 2024 : SIGSTOP

As we all know, things don't always go as planned, and hence came the 'SIGSTOP' signal to my BSCP process. Workload increased day by day, and eventually, I had no time and energy left for BSCP. This went on for a month, and when I came back to preparation, I realized I couldn't recall things properly due to a lack of revision. I kind of felt demotivated that I'd have to start all over again, going through all the topics, doing more than 170 labs again, and whatnot. But again, what inspired me was the challenge and the reason why I started. Hence, game on, again.

## 1 March 2024 : SIGCONT

I started my preparation again. Since I already had the notes, I just had to go through them once, solve labs, and add the missing pieces. This time, with every lab I solved, I also made a cheatsheet for the exam, so that I wouldn't have to run searches for payloads during those four hours. Since a lot of people failed because of the time constraint, I wanted it to be the least of my concerns. The cheatsheet had everything: the vulnerability, test cases, payloads, and any additional notes required for the lab.

![t](/Images/BSCP/Pasted image 20240414093511.png){:.shadow}

At this time, I also categorised the vulnerabilities into three stages :
- **Stage 1**: Gaining Access 
- **Stage 2**: Privilege Escalation
- **Stage 3**: Code Execution

This distinction was primarily based on the individual stages of the exam and corresponding vulnerabilities.

![t](/Images/BSCP/Pasted image 20240414085955.png){:.shadow}

This allowed me to save time during the exam, so that I didn't have to bombard the application with random payloads. Once I completed all the labs, notes, and cheatsheet, I moved onto mystery labs. Mystery labs were challenging at first, but after doing around 50 of them, they were out of my way too.

Since PortSwigger provides two practice exams before you take the final exam, I took both of them, and I miserably failed both. At this time, I was demotivated again, but not much, since I was making a silly mistake, which didn't allow me to get through. So you fall and learn, fall again and learn again, until you really understand how to do it.

![t](/Images/BSCP/Pasted image 20240414092819.png){:.shadow}

## 13 April 2024 : exit code 0

After leaving no stone unturned, I finally decided to schedule the exam. I was kind of nervous, but I also heard this quote, 'He who sweats more in training bleeds less in battle,' by George S. Patton Jr., and boy, I was sweating hard.

The exam requires you to solve two applications in total, with three challenges each, within a duration of four hours. Here's how my exam went.
- **Application 1** : 60 Minutes
- **Application 2** : 120 Minutes

The reason why the second application took time was due to a lack of attention to detail and obviously, the pressure. Eventually, I completed the exam in three hours. Once done, I tried uploading the Burp project file for verification, but it didn't work. Hence, I had to email the project file. After two days, i got the official mail and the certificate.

![t](/Images/BSCP/bscp.png){:.shadow}

## My Take

All in all, completing BSCP was a great learning experience. It not only taught me how to detect vulnerabilities but also how to exploit them. I learned a lot about Burp Suite, including the Comparer, Collaborator, Macros, Single Packet Attacks, Payload Processing, and using Burp for race conditions, etc. The exam really pushes you to improve your timing in detection and then from detection to exploitation. I would strongly suggest this certificate to anyone who's into AppSec and is looking forward to improving their skills in web application black box pentesting.

## Tips

You can read the hints and guidance provided by PortSwigger Academy [here](https://portswigger.net/web-security/certification/exam-hints-and-guidance) , other than that my only tip is to use the elimination method, the way to do it is to know how vulnerabilities can be prevented. Take the following examples for reference :

- You wouldn't look for web cache poisoning, if the cache control header is not present or if the cache control header is set to private. In context of labs, it's the `X-Cache` header.
- If `X-Frame-Options` is set, you won't look for clickjacking attacks.
- If cookie is set to `SameSite=Strict`, then no CSRFs there, similarly if the cookie has `httpOnly` flag set, then no XSS for stealing cookies.
- For HTTP Request Smuggling, simply throw in both the headers and see how the application responds, if it shows some error like `CT & TE both headers are specified`, then it's probably not vulnerable. There's one catch, in real life scenarios we can try to obfuscate the TE header, but for exam and the labs,  i don't think this would be the case.
- In case of SSTI, an indication can be if the page is rendering HTML but not executing Javascript code.

This way, you can basically use the preventive measures as a way to eliminate vulnerabilities during the exam, rather than actually trying and carrying out the probing for each and every vulnerability type. Also, make sure you give `attention to detail`, this could be anything like response headers, response messages, error messages, page source etc.

## A Message For PortSwigger

Thank you so much for the amazing learning experience. You guys have one of the best knowledge rich contents on the planet. BSCP has been a great experience, and I am looking forward to new content in the future. I have a few suggestions. First and foremost, improve the certificate design, and if possible, provide a physical copy of the certificate to people who've cleared it. There's a more promising sense of achievement that comes with the physical touch.



