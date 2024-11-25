---
title: 10x Hacking With Be10x
tags: AI OSINT Wordpress sad
article_header:
  type: 
  image:
   #src: 
sidebar: 
  nav: blogs
---

Three months ago, i made a post on LinkedIn on how i got unauthorised access to an account on Skill Nation Website maintained by Jatin Shah, if you don't know him, he's one of the guy whose Ad run on your Youtube videos, where he talks about integrating AI with Microsoft Office and save your ass from the AI revolution.

## How it began ?

Fast Forward to today, i was watching a video by `Low Level`(amazing guy), when an Ad played on my screen by `AI For Techies` with some highlights like `Learn Python In 30 Days`, `Debug any code in under 10 minutes` and obviously a cherry on the top `Integrate AI with python`. Now, even though none of these claims intrigued me, the guy in the video did.

I remember watching this guy in one of those learn AI or you will lose your job ads, i wasn't very sure if it was the same guy, but something around AI and courses for sure. Vulnerable me, led by my curiosity, clicked on the link.

![t](/Images/10x/1.png){:.shadow}

## Jahapana Tofa Kabool Karo

The course seemed pretty good, with 26,000 enrolled students with an average rating of `4.7`, i wasn't really looking for anything particular and was just browsing the site, but the hacker mode kicked in, when i clicked on the login button.

![t](/Images/10x/2.png){:.shadow}

Yep, skipped password management entirely, i am not surprised at all, because this is not the first time, our Jatin Bhai also did something similar. 

![t](/Images/10x/3.jpg){:.shadow}

## Enumeration

Once i had the password, i did what anyone else would do, i.e. look for the username. I did some search on the internet, but couldn't find any valid username. So i came back to the website and did some technology profiling, it revealed that the website is running over Wordpress. If it's Wordpress, we can easily enumerate usernames using a variety of methods. So i crafted some bash one liners instead of using `wp-scan`, just to look cool, even though i knew `wp-scan` would've given me faster results.

```bash
# Method 1
curl  https://wordpress-site/wp-json/wp/v2/users 2> /dev/null | jq | grep name | cut -d '"' -f 4 > users.txt

# Method 2
for i in {1..100}; do curl "wordpress-site/?author=$i" --location 2> /dev/null | grep "Author name" | cut -d ':' -f2 | cut -d '<' -f1 > temp;sort -u temp >> users.txt;rm temp;done
```

![t](/Images/10x/4.png){:.shadow}

## Gaining Access

By now, i had 10 usernames to login and check if the password really works. Using the password that was gifted to us and the username that we harvested, i found a working combination and i was in.

![t](/Images/10x/5.png){:.shadow}

On top right i saw a button `GPT`, curious me clicked another button and i was taken to a custom GPT.

![t](/Images/10x/6.png){:.shadow}

Be10x GPT ? (more on this later)

I thought i was studying from `AI For Techies`. Anyway, i clicked on `Login with Registered Email ID` and it logged me in with the account i already had access to, using OpenID Connect (basically SSO) via Wordpress and i had unrestricted access to the internal custom GPT as well as the user's chat history....

![t](/Images/10x/7.png){:.shadow}

## Privilege Escalation

One interesting thing to notice is, the website used OpenID to authenticate me via Wordpress, meaning our user is a legit Wordpress user and should have access to Wordpress Dashboard as well. 

![t](/Images/10x/8.png){:.shadow}

This got me thinking, out of the usernames that we have identified, there must be at least one administrator user, but i wasn't sure if the admin user would share the same password that was gifted to us. But to my surprise, it did.

![t](/Images/10x/9.png){:.shadow}

This was not expected at all, first putting the password out on the internet for people to share and then using the same password for an administrative account ? I have to switch to Hindi and say `bhai sharm naam ki cheez h thodi bht?`, this is straight up joke. But it's okay, if you open a hole, sooner or later, something will get in, and it'll hurt. But it's an important lesson.

## Linking Pieces

Coming back to `Be10x`, i did some digging and instantly found a face not so difficult to forget.

![t](/Images/10x/10.png){:.shadow}

You remember these people? These are our saviours, the warriors to protect us from the wrath of AI, they sell their courses for just 9 rupees to prevent us from being kicked out of our current job. I checked `be10x` youtube channel, and do you know what i found ?

![t](/Images/10x/11.png){:.shadow}

The same guy who i was trying to recall initially, so basically `AI For Techies` is indeed linked with `Be10x`, or `AI For Techies` is just an extension of `Be10x`, whatever this is. One thing is common.

![t](/Images/10x/12.png){:.shadow}

The password management solution :)

I tried to dig around some information around the faces and watched some more ad videos by `be10x`. One thing that is rubbed the most into viewer's face is `IIT Kharagpur` (one of the most prestigious college in India), i am not going to talk about the Alumni, let's just talk about the people running this business. If you graduate from one of the most prestigious technical college of India, and you still don't understand such a basic concept like password management, it's plain sad, idk who the real culprit is, nor i am interested in finding out, but it raises question on a lot of people and the education system too. 

Also, i don't agree with claims like 
- `AI will replace you`
- `Employees are getting laid off because of AI, so if you don't want to get laid off, take this course`.

I know AI is rising in popularity and is definitely a good skill to have, but fear mongering isn't. 

![t](/Images/10x/13.png){:.shadow}

I hope you enjoyed reading this blog, as much as i loved writing it. Make sure you check out my youtube channel and if you find it interesting, don't forget to hit the subscribe button. Until then

*Stay Safe & Keep Hacking*