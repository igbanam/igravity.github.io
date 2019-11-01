---
layout: post
title: Cleaning up my Macbook
date: 2019-10-31 12:05 +0100
author: igbanam
category: blog
tag:
  - laptop
headerImage: true
image: /assets/images/slim-pre.png
---

![Total Bloat](/assets/images/slim-pre.png)

There are many culprits which eat up your hard drive space. I recently had to clean up my Macbook. What I learned is listed here.

> **TL;DR** Media is not the only content which eats up your hard disk space.

Waking up to "Your memory is almost full", I was forced to take a look at how I use my hard disk space. Who would have known that 256GB would run out so soon? Through my time on this machine, I have been careful about storing audiovisual media: I have no music, I don't use iTunes, I have no movies, I limit the number of PDFs I store by reading and deleting. Despire these practices — which some might deem _ascetic_, 256GB hard disk ran out in no time. Why?

### Operating System

![System Bloat](/assets/images/slim-system.png)

There is a non-negotiable 5GB to 6GB used up by the operating system. This is necessary for your computer to run. There's no touching this, no tampering with this. Matter of fact, when inspecting your newly gotten computer, chances are you would see 251GB free. This is where the extra 6GB ish went to. Some other areas which could contribute to system bloat are files in your `/Library` folder. These files are used to support your computer's runtime. But as we shall see, most of them are obsolete or unnecessary.

> The bloat thickens

My Macbook breaks down my storage use into the following categories

- Documents
- Apps
- Music Creation
- System
- Other Volumes in Container

< Insert the plain storage information image here >

I'll tackle this in increasing order of bloat.

### Music Creation

![Music Bloat](/assets/images/slim-music.png)

This is Garageband, basically. My work as a software developer for software developers has little to nothing to so with Music Creation, so the assets here are little.

### Apps

![Apps Bloat](/assets/images/slim-apps.png)

I currently have 66 apps on this Macbook. The biggest of them all is — ...drum roll... — **XCode**. Yup, you guessed right. That fat blob of I-only-make-apple-apps sits in a proud 19.02GB space. The next largest app is iMovie at 2.82GB. Who uses these things?

### Documents

![Documents Bloat](/assets/images/slim-documents.png)

This contains everything in the user space. It's hard figuring out what the culprit is here, so I used a few tips to help me out.

1. Use the List View in finder. Set this to show sizes of files and folders in the Show View option of the context menu. Order by Size.
2. (Pretty much the same thing as 1. but in the command line)

```
cd ~
du -hd 1 | sort -h
```

### The Clean-up 🧹

Here, I found many interesting things

1. Most of the culprits are in `/Library`. — This folder is not shown by default. Click "Go" in the Finder menu, and hold down Option to reveal this.
2. [Zoom](https://zoom.us/) stores the pictures of everyone you've ever joined a conference with on your computer.
3. After you uninstall an application, the Application Support within your Library may live on.  
  a. Also, when you install a newer version of an application and its underlying file struucture changes, the remnants of the old structure remain in your `/Library`
4. Hidden folders are not accounted for in the Finder view.
  a. For this, I used the commandline option, which showed me that `~/.ruby-motion` sat in a comfortable 77GB space.
5. Beware of Docker. It's fun to play with, and it solves an amazing problem. But it leaves so much behind, and does not automatically clean up after itself.

### Conclusion

![The Slim!](/assets/images/slim-post.png)

Cleaning up should not be a one-time thing. It should be a lifestyle.
