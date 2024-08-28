---
title: Hacking Into Your Medical History
tags: Pentesting Bug-Bounty Hospital Real-World SocialEngineering Vishing
article_header:
  type: 
  image:
   #src: 
sidebar: 
  nav: blogs
---

*The screenshots in these photos have been intentionally edited to maintain the identity of the company and keep it confidential.*

## Backstory

Few days ago, i got a call from one of my relative who got to know about my work. They were very fascinated by it and wanted to get an assessment done for their hospital. We discussed  multiple types of assessment, and decided to go for a Red Team Engagement. 

The only thing i had to start with, was the URL.

### Web Enumeration

So i started doing recon on the hospital, browsed through their websites, different branches that they have, the founders, the people who work there, the types of service they provide etc. Just to have a good understanding of how big it is and to gather information, that might help me in later stages.

Browsing through the website, i came across a patient login portal for any patient to download their report... and that's when things started getting interesting.

![t](/Images/medical/1.png){:.shadow}

### OSINT

So even though i had access to the patient login portal, i didn't have any patient information as such, so i started doing some OSINT in the hope that i might get a report or a bill on the internet, and the results were amazing.

![t](/Images/medical/2.png){:.shadow}

Not one, but multiple reports posted on the internet. Most of them dated back to 2019-2020, but they had it mentioned in the report that reports can be downloaded from the online portal. This provided me with multiple patient usernames to try. 

But... none of it worked :(

![t](/Images/medical/3.png){:.shadow}

### Calling Your Hospital

After exhausting all the ideas, i called up the hospital and here's how it went

```md
A: Hi i've been trying to download my reports from the online portal of the hospital
  
H: Umm Hmm  
  
A: I've been trying to download the report of my grandfather, but it's not working.  
  
H: The name... umm... the first four letters of the patient's name should be entered in the username 
  
A: Yes ma'am, the patient's name is REDACTED_PATIENT. I have entered "REDA" in the login.  
  
H: Okay  
  
A: And according to the instructions, I have entered "REPA" in the password field  
  
H: You have entered it wrong, sir. What have you entered for PHID? I mean in the username?  
  
A: For the username, I have entered "REDA"  
  
H: No, sir, there should be some numbers on your bill as PHID  
  
A: Okay, yes, 55...  
  
H: Please enter those in the username, and use the first four letters of the patient's name as the password. Okay?  
  
A: The first four letters, right?  
  
H: Yes, the first four.  
  
A: Okay, thanks !
```

Few minutes of stressful silence while i tried logging in with the new information that our friend provided, i sent the request and came a 302.

### Gaining Access

![t](/Images/medical/4.png){:.shadow}

Boom, i was in. For a moment I could not believe what i just pulled off, it took me sometime to digest the entire situation, but when it did....

![t](/Images/medical/Pasted image 20240828194531.png){:.shadow}

Now the bad news, this patient had no reports available on the portal, so basically i still wasn't really "IN THERE".

### Getting Root

 Since, i had the access to the dashboard, i started enumerating sub pages and directories. That's when i came across another login page.

![t](/Images/medical/5.png){:.shadow}

This seemed like an admin panel to the administrative dashboard, i made this assumption on the basis that i was already authenticated, but still it asked for credentials. I tried a few default credentials, but none of them worked. Then when i thought of the quote :) and you won't believe what happened next.

```bash
admin' OR 1=1-- -
```

A simple, basic, vintage SQL injection did the trick and i was in. That's not it, the scary part was that what i just bypassed was the admin panel to access and manage all the reports that have been ever since the website started.

![t](/Images/medical/6.png){:.shadow}

I had access to around a million of medical reports. 

***The power, felt real.***


## Exit Status

You thought that was it ? Well, pretty much, but here's a little more.

The next thing i did was pull out some research on the software company that built the system to find out how many other hospitals were affected. For that i used multiple techniques.

- **Google Dorks :**
	- Used the copyright in footer of the login page and found four more hospitals affected by this.
- **Active Recon :**
	- The software company had the client names mentioned on their website, which gave me an overview of how many systems were affected by this. The software company did not only created these systems for medical industry but other industries as well, including real estate, distributors etc.

But i did not perform any tests or anything on other clients, since i didn't have permission to do so. But yes, i did inform the software company about the issue. But it's sad to see such critical infrastructures maintained by such incompetent software devs who don't understand security, i mean a basic SQLi in 2024? That's BAD. I understand companies are meant to be profit driven, and there's nothing bad about it, but they should also be value driven, taking care of your client by ensuring the client's data is well protected and secure. The client comes to you with trust in your ability, don't break that please.

