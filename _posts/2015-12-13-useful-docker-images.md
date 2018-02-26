---
layout: post
title: Useful Docker Images
---

Some useful Docker images which I use day to day for my work and non work related tasks.
This include a torrent server, a file sharing server , youtube-dl with ui app and jenkings build server and Jekyll environment which I use to build this blog.

Here is the list.

### transmissiion control 

[docker-transmission](https://github.com/odarriba/docker-transmission)

```
docker run -h transmission --name transmission -e USER=<user> -e PASSWORD=<password> -e IP_WHITELIST=*.*.*.* -d -v /home/ubuntu/downloads:/downloads -t -i -p 9091:9091 -p 51415:51413/udp -p 51415:51413/tcp transmission
```

change permissions of download folder (on host) to 755 and make non-root user the owner of the download foler.


### youtube dl 
[Youtube-dl-WebUI](https://github.com/p1rox/Youtube-dl-WebUI)

```
docker run -d -p 9090:80 -v /home/ubuntu/Videos:/www/youtube-dl/downloads ytdlwui
```

### dc hub - Ptokax

[Ptokax](https://github.com/mbentley/dockerfiles)

```
docker run -d -e HUB=192.168.23.101 -e NICK=admin -e PASSWORD=password -p 1209:1209 -p 411:411 ptokax
```

### jenkins 

[Official Image](https://github.com/jenkinsci/docker)
<br>
[Moodified with Java 7](https://github.com/junaidk/Jenkins-Docker-image)

### jekyll environment

[jekyll](https://hub.docker.com/r/grahamc/jekyll/)

```
docker run -d -v "$(pwd):/src" -p 4000:4000 grahamc/jekyll serve
```

run this in your jekyll blog root dir or replace $(pwd) with your blog root



