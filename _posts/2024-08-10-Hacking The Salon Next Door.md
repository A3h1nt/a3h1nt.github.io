---
title: Hacking The Salon Next Door
tags: Pentesting Bug-Bounty Salon Real-World
article_header:
  type: 
  image:
   #src: 
sidebar: 
  nav: blogs
---

*The target company has been informed multiple times about the vulnerability through various mediums, to which they have promptly replied with nothing. The only thing that's left is to visit the physical office, but ehh, i am too lazy for that. But let's not ruin a good story, right? Here's how I hacked the salon next door.*

## The Haircut

After a long period of procrastination, I finally decided to get my hair cropped. I visited my regular salon, got the haircut, paid the bill and left. Once I reached home, I received an SMS from the salon with my bill.

![t](/Images/salon/1.png){:.shadow}

### May I Check Your Bill ?

The SMS specified my customer name, salon name, and two links: one to view the bill &  the other for feedback. It contained a tinyurl link that redirected to the bill URL.

![t](/Images/salon/Pasted image 20240809155538.png){:.shadow}

In the request there's a unique number which identifies the bill, it's a bill ID, and what do any hacker do when they see something of like this ? IDOR, obviously. And yes, I was able to access bills of other customers on the application. This issue also existed in Domino's before 2024, I think. I reported it, but they simply ignored it; however, now they have fixed it.

### Impersonating The Salon

In the above request, there's a `BILLIMAGE` parameter in the requested end-point, which tells the server to view the bill image to the customer, but what if I remove it? 

![t](/Images/salon/Pasted image 20240809190944.png){:.shadow}

Well well well, that's something. Amongst all the data in the response, the most interesting parameter was `SMSMESSAGE`, sending a request to the `URL`  specified in `SMSMESSAGE` sends a message to the phone number present in the `dest` parameter. 

Wait... can I change the number and send a message to anyone on the company's behalf ?
Not entirely, but yes you can send a message to anyone on the planet on the company's behalf, but you can only modify some fields in the message (specified using `{field}`)

![t](/Images/salon/Pasted image 20240809191904.png){:.shadow}

Some of the links present in `SMSMESSAGE` also revealed the username & password to the SMS management software that the Salon uses to manage sending these SMS, meaning i could now craft any message from scratch and impersonate the salon to send a message to anyone on the planet. Just think what chaos someone can cause with this access.

## Wavering Curiosity 

Curious about what i found, I was excited to see what other salons had to unravel about their security posture. I started asking my friends about their salons and if they received any messages, after going through some of them, i stumbled upon a message that roughly said, "You can access your bill on this portal and book future appointments." 

### Breaking Authentication

I opened the link to the portal and was greeted with the following

![t](/Images/salon/Pasted image 20240809201448.png){:.shadow}

The portal requires a registered client's phone number and a client PIN or an OTP in order to log into the account. I had the phone number, but my friend couldn't recall his pin, what do we do now? Try to break it :)

![t](/Images/salon/Pasted image 20240809202302.png){:.shadow}

The server returns `-1` for an incorrect pin, i changed the response to `1` and the next request was

![t](/Images/salon/Pasted image 20240809203911.png){:.shadow}

Yep, that's right, the server sends the request to get the client data with the client ID specified in the GET parameter and the server returns the user data including Cards, membership details, service taken etc, giving complete access to the user account. Hence, authentication bypassed.

### Breaking Authorization

I was not only able to bypass authentication, but by changing the client ID, i was also able to access the data of other clients on the website, i was also able to use anyone's card, view the services they took at salon, book appointments that they never booked etc. Being a good hacker i did none of that and moved on to other parts. On browsing the portal, i came across another end-point that returned data for different salon branches.

![t](/Images/salon/Pasted image 20240809204829.png){:.shadow}

The website returns, the salonID, Salon Name and phone number of the salon or the owner, this made me curious about what if i try to log in with the salon owner's number? 

Well, it did not work. Doing some more reconnaissance, i found one interesting subdomain `staff.target.com`.

### Going Super Saiyan

On `staff.target.com` i found another login panel for staff, which i suspected to be as vulnerable as the client login panel.

![t](/Images/salon/Pasted image 20240810001042.png){:.shadow}

Using the numbers enumerated from the client panel, i was able to get into a Salon Owner's account.

![t](/Images/salon/Pasted image 20240810002548.png){:.shadow}

Similar to the client portal, this portal was also vulnerable to broken authorization and i was able to access the data & account of any other salon owner. The access allowed me to create new services, set prices for them, view all appointments and total revenue etc.

Just to clarify what all was compromised, here's a visual representation of it.

![t](/Images/salon/Pasted image 20240810005615.png){:.shadow}

And that's how i hacked the salon next door (yours & mine). 

## A Message

That's all for today. It is sad to say but we people sign up for things at companies that don't even care about our data, businesses are profit oriented and as long as something doesn't affect their business operations directly, they don't care. Look at what happened with the Domino's, Boat and countless other companies. They don't suffer the consequences of these data breaches, but people like us do, our data floats in the air,  but this will change. We will make it change.

*Let thee who deserveth suffer the consequence.*
