---
title: TrueCrypt Explained
tags: truecrypt truecrack on-the-fly-encryption
article_header:
  type: 
  image:
   #src: 
sidebar: 
  nav: blogs
---

## What is TrueCrypt ?
TrueCrypt is an on-the-fly encryption software, that is used to encrypt folders, partitions or even a complete hard drive and store all of that into a single encrypted file often known as TrueCrypt container or Virtual Volumes.

## What does on-the-fly encryption mean ?
On-the-fly encryption means, the data is automatically encrypted before it is saved and automatically decrypted when mounted/loaded(using a key or a passphrase), without any user interaction, no decrypted data is stored on the disk, everything happens inside RAM .

## Type of Volumes
Truecrypt support two types of volumes:
- **Standard TrueCrypt Volume** : This is the normal way of creating a TrueCrypt volume, where the user simply enters the password mounts the drive and starts interacting with the virtual volume.
- **Hidden TrueCrypt Volume** : This method of creating volumes is used, when the user wants to create a hidden volume that can't be detected, this method creates a hidden TrueCrypt volume inside another TrueCrypt volume.

![t](/Images/tc.png){:.shadow}

## TrueCrypt On Linux
TrueCrypt can be installed from [here](http://truecrypt.sourceforge.net/OtherPlatforms.html).
To encrypt a volume
```coffee
Syntax:
truecrypt -c /volume/to/be/encrypted /destination/path

Example:
truecrypt -c /home/a3h1nt/secret /opt/random
```
To mount a volume
```coffee
Syntax:
truecrypt -m /path/to/TrueCryptContainer

Example:
truecrypt -m /opt/random 
```
To dismount a volume
```coffee
truecrypt -d 
```

*These volumes can also be mounted using other tools like veracrypt*
```bash
v1g1lant3@kali:~/test$ veracrypt -tc Secret.tc mounted/
Enter password for /home/v1g1lant3/test/Secret.tc: 
Enter keyfile [none]: 
Protect hidden volume (if any)? (y=Yes/n=No) [No]: 
v1g1lant3@kali:~/test$ cd mounted/
v1g1lant3@kali:~/test/mounted$ ls
'$RECYCLE.BIN'   secret.zip

# veracrypt -tc Secret.tc mounted/
# -t : text mode / interactive
# -c : create a new volume
# mounted/ : destination path
```

## Cracking TrueCrypt Password
To crack a TrueCrypt encrypted volume, there's a tool called `Truecrack`. Which can be installed in kali Linux using the following command.
```bash
sudo apt-get install truecrack
```

To crack the password for an encrypted file
```bash
truecrack -t /TrueCrypt/volume/file -w /path/to/wordlist
```
