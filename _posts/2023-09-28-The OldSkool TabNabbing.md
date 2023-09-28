---
title: The OldSkool TabNabbing
tags: tabnabbing phishing opener
article_header:
  type: 
  image:
   #src: 
sidebar: 
  nav: blogs
---


Welcome back hackers ! 
It's going to be 2024 in two months, I remember i wrote my last blog in 2021 and a lot has changed since then, due to some personal issues i wasn't able to continue with the security field entirely, but now i am back, and here's the "old , but interesting vulnerability". Some people also consider TabNabbing as irrelevant, which is true, given the advancements made in browser's security , but nevertheless it is an interesting vulnerability to read about.
## Opening a new tab

A website has two ways of specifying a hyperlink, either through the anchor tag or through window.open method in javascript.

- Anchor Tag 
```html
<a href='https://link.com' target="_blank"></a>
```

- Window.open( )
```js
window.open("https://www.w3schools.com");
```

Whenever a website opens a link in another tab, a `window.opener` property is created, this property is usually used to provide context of the source website that opened the link.

According to W3School, the `opener` property returns a reference to the source window that created the new window.

```bash
[Website - A] ----OPENS----> [Website - B]
[Website - B] ----window.opener("https://websiteA.com")----> [Website - A]
```

1. `Website-A` opens `Website-B` and passes the window.opener property to it.
2. `Website-B` returns the reference to the source window

Example : ( Credits : [Shatabda](https://medium.com/@shatabda/security-tabnabbing-what-how-b038a70d300e ) )

While accessing your account in a website, it requires you to enter the credentials. The login page opens in a new window. On successfully validating the entered credentials, the new window can redirect the parent window to the account details and close automatically.

## The Issue

Until the first step, when `Website-A` opens `Website-B` everything looks fine. But, in the second stage we can see that `Website-B` provides the location/link to `Website-A` through the opener property. 
So as an attacker if i can manage to insert a link to a `Website-B` let's say through comments section in `Website-A`, then once the user clicks on the link and is directed to `Website-B` on a different tab which will have the `window.opener()` property, using this property the attacker  can replace `Website-A` with a phishing website `WebsitePhish-A` while the user is still on `Website-B`, this way a user can be phished easily.

## What is tab nabbing ?

Tab nabbing is a web application attack, where an attacker is able to nab information from a victim by supplying a link in the source website that the victim visits, once the victim clicks on the link, the source website opens another tab, this newly opened tab simply replaces the source website with some malicious look alike website through `window.opener()` property, hence tricking the victim into giving out information.

## Impact

This attack is primarily used for phishing, successful execution of this attack can allow an attacker to extract credentials, bank information etc. The attacker can also replace a website that the victim might be using to download some file and trick her/him into downloading a malicious one. Which can obviously have more severe impact.

## Practice Lab 

Download the lab setup from [here](some-github-link)

### Working

So there are four main files :

- **Login.php** : The legit website where the user logs in
- **dashboard.php** : User is directed here once logged in
- **tabNabbing.html** : This is the page the user will click on
- **evilLogin.php** : The source website will be replaced by this page, which is simply a replica of the legit website login page.

### Walkthrough

1. The user navigates to the legit website `login.php`

![t](/Images/tabnabbing/1.png){:.shadow}

2. Enters her/his credentials into the website and is redirected to the `dashboard.php` page.

![t](/Images/tabnabbing/2.png){:.shadow}

3. The user clicks on the "cool link" and a new tab is opened `tabNabbing.html`

![t](/Images/tabnabbing/3.png){:.shadow}

This page contains all the evil code that will replace the source tab with the mentioned URL.

```html
<h1>See the last tab that opened this link</h1>
<script>
   if (window.opener) {
      window.opener.location = "http://127.0.0.1:8080/tabnabbing/evilLogin.php";
   }
</script>
```

4. When the user returns to the source tab, notice the page says `Logged Out due to an error ! 

![t](/Images/tabnabbing/4.png){:.shadow}

But if you notice the URL, the file is now replaced by `evilLogin.php`, the user will try to login again and once the user submits the form, the attacker will have the credentials. 

For the sake of simplicity, `evilLogin.php` will simply create a file to store credentials, and the user will be redirected to some other page.

![t](/Images/tabnabbing/5.png){:.shadow}

Contents of our newly created file :

![t](/Images/tabnabbing/6.png){:.shadow}

## Prevention

- A good news for all the developers is that after this [issue](https://github.com/whatwg/html/issues/4078) , most of the browsers now append `rel="noopener noreferrer"` to the anchor tag by default, so nothing to worry about. But, just to be on a safer side, it is recommended to add `rel="noopener noreferrer"` to all outgoing links on your website, this will simply make sure that the reference returned by the new tab ( Website-B ) is always null.

```js
/* For Anchor Tag */
<a href='https://link.com' target="_blank" rel="noopener noreferrer"></a>

/* For window.open set */
newWnd.opener = null;
```

- You can also implement [COOP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cross-Origin-Opener-Policy) ( Cross Origin Opener Policy ), this will make sure the new window does not have reference to the source window and also sets window opener property to null.

*As a security researcher you can look if the website has explicitly provided the opener property, in such cases it could be considered a bug.*
## Reference

- [OWASP Reverse Tabnabbing](https://owasp.org/www-community/attacks/Reverse_Tabnabbing)
- [About rel=noopener](https://mathiasbynens.github.io/rel-noopener/)