---
layout: post
title: Edit a file on remote server in sublime text 
---

Using Secure Shell (SSH) Tunneling, we can establish an SSH session that routes arbitrary traffic through it to a specified port for any use we want.  Thanks to a nifty set of scripts called [rsub](https://github.com/Drarok/rsub), modified originally from TextMate’s rmate, we can run a little utility server on our local machine that interacts with your remote server for you and lets you open up remote files and save them back, all through an encrypted channel.

### Download rsub for sublime text 
Go to your sublime text pluging directory  
 
 - OS X `~/Library/Application Support/Sublime Text 3/Packages`

 - Linux `~/.config/sublime-text-3/Packages `
 
 - Windows `%APPDATA%/Roaming/Sublime Text 3/Packages/`

and clone this git repe there. 

```
git clone git://github.com/henrikpersson/rsub.git rsub
```

### Edit ssh config on local machine 

Add this line to ~/.ssh/config file ( create if not exist )

`RemoteForward 52698 127.0.0.1:52698`

Set config file permission to 600

```
chmod 600 ~/.ssh/config
```

### Remote server configuration 
Install rsub on remote server 

```
wget -O /usr/local/bin/rsub https://raw.github.com/aurora/rmate/master/rmate

sudo chmod a+x /usr/local/bin/rsub

```


### SSH to remote server
Run this to create connection to remote server 

`sudo ssh -R 52698:localhost:52698 <user>@<host>`


### Open a file to edit 
To edit any file on remote server do 
`rsub <file name>`

it will open in subline on local machine