---
layout: post
title: Set Up Jenkins to Build Docker Images From Inside A Docker Container 
---

For one of my projects I needed to setup a jenkins environment for autobuild. I pulled a jenkins docker image form docker hub and started a jenkins container. My next requiremt was to build docker image of the project I was building inside jenkings. But it is difficult and complicated to install docker inside a container. So I mounted docker binary inside the container thinking it will do the job. But that was not the case. I had to mount all the libraries which docker runtime depend on. After I mounted all the libraries and docker binary inside the container, I was able build and run docker images from inside the container.



Here is the docker file which I used to build Jenkins Image

```Dockerfile 

	FROM jenkins:latest

	USER root
	RUN apt-get update \
	      && apt-get install -y sudo \
	      && rm -rf /var/lib/apt/lists/*
	RUN echo "jenkins ALL=NOPASSWD: ALL" >> /etc/sudoers

	USER jenkins

```

Libraries needed to run docker inside a container

```bash
	/usr/lib/x86_64-linux-gnu/libapparmor.so.1
	/lib/x86_64-linux-gnu/libsystemd-journal.so.0
	/lib/x86_64-linux-gnu/libcgmanager.so.0
	/lib/x86_64-linux-gnu/libnih.so.1 
	/lib/x86_64-linux-gnu/libnih-dbus.so.1 
	/lib/x86_64-linux-gnu/libgcrypt.so.11

```

docker run command for starting Jenkins container

```bash

	docker run -d --name jenkins -p 8080:8080 -p 50000:50000 
	-v /var/run/docker.sock:/var/run/docker.sock 
	-v $(which docker):/usr/bin/docker 
	-v /usr/lib/x86_64-linux-gnu/libapparmor.so.1:/usr/lib/x86_64-linux-gnu/libapparmor.so.1 
	-v /lib/x86_64-linux-gnu/libsystemd-journal.so.0:/lib/x86_64-linux-gnu/libsystemd-journal.so.0 
	-v /lib/x86_64-linux-gnu/libcgmanager.so.0:/lib/x86_64-linux-gnu/libcgmanager.so.0 
	-v /lib/x86_64-linux-gnu/libnih.so.1:/lib/x86_64-linux-gnu/libnih.so.1 
	-v /lib/x86_64-linux-gnu/libnih-dbus.so.1:/lib/x86_64-linux-gnu/libnih-dbus.so.1 
	-v /lib/x86_64-linux-gnu/libgcrypt.so.11:/lib/x86_64-linux-gnu/libgcrypt.so.11 
	-v /var/jenkins-home:/var/jenkins_home  
	jenkins

```

