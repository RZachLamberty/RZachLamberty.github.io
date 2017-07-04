---
layout: post
title: Why not 5j?
excerpt: "In which our hero decyphers"
keywords: "aws, linux, debian, java, neo4j, install, admin, sysad"
series: aws
part: 3
---

## installing `neo4j` on aws

first of all, we're installing the latest and greatest (`neo4j v3.2`). In order to do this, I went to the project documentation found [here](http://neo4j.com/docs/operations-manual/3.2-alpha/installation/linux/debian/).


### prerequisites

first, we need us some `java` 8. I decided to go with my trust ol' `debian` environment (see aws install instructions [here](https://wiki.debian.org/Cloud/AmazonEC2Image)). At the time of writing, the newest version supported was still `jessie`, so I went with that.

Fortunately, I'm not the first person to ever install `java` 8 on `debian jessie`, so [instructions](http://neo4j.com/docs/operations-manual/3.2-alpha/installation/linux/debian/) were already readily available from `neo4j`. Not too shabby.

Just a quick heads up, though: I was unable to get the command

``` shell
apt-get install openjdk-8-jdk
```

to run successfully even after adding it to my `apt` sources files. Perhaps this was my fault. The way I dealt with this, eventually, was by being explicit about the targeted version (i.e. demanding we install the backports library, because apparently there was a non-backports version available for me in the default package repositories?). In any case, the working command was

``` shell
apt-get install -t jessie-backports openjdk-8-jdk
```

where, above, the `jessie-backports` above was the alias given to the backports repo url in my `apt` sources file (per the `neo4j` site instructions).

Confusingly, I had to open more than just the http and https ports -- I also had to open the bolt port, as that was used for the web interfact (this is spelled out *very* clearly in the documentation, but why read documentation when you canjust yolo?).
