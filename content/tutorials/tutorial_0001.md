---
title: "Tutorial 1 - Our first project"
date: 2020-05-15T21:43:12+02:00
draft: true
tags: ["first","installing", "nodes", "video", "webcam"]
categories: ["basics"]
authors: ["Giacomo Sabatini"]
---

Welcome to this second part of this PraxisLIVE tutorial series!
In this new episode we're going to install the software, start a new project and play a little with some nodes.

**SETUP**

Regardless your OS (Windows, MacOS or Linux), just go to the website and download the software. Just for Linux, if your distro is not Debian-based, you have to install dpkg on your system and run the following command:

  {{< highlight html>}}
     sudo dpkg -i praxis-live_*version_all.deb
  {{</highlight>}}

Or you can also download it from GitHub.

Once you're setup, we can boot the software. In the main tab, install the examples, the additional components and configure Video playback and capture (basically you have to install GStreamer).

**OUR FIRST PROJECT**

What we're going to do now it's to load our webcam input and modify trough filters and other nodes!

Let's go to File -> New Project. After created the project, go to File-> New File and choose video. It will open a new tab with a single node called "screen". This is the output node; it must not be eliminated, since it allows the project to be viewed during playback.

{{<figure src="/imgs/1_OurFirstProject/video_1.png" alt="" >}}
  
Let's create **capture** node with right-click in the tab, Add-> video-> capture. Plug the Out Socket from **capture** to In from **screen** and play (look at the image). Once you're on play mode (up-left button, not the one near to **build**), a blank window will pop-up.

Double-click the **capture** node, and hit play. Now you should be able to see yourself from the webcam!

Now let's see what happens if we start to modify the input through other nodes, maybe with **solarize**! Just add it (right-click, Add -> video:gl:custom -> isf-solarize.pxg). When you'll hit play, it should happen something like this:

{{<figure src="/imgs/1_OurFirstProject/video_capture_solarize.png" alt="" >}}

With Praxis you can also modify the project while in execution (that's one of the main features we saw in our Introduction), so you don't have to always push play and then stop it. It's also useful to see what is the outcome of each modify you make on the fly.

Now, we may want to make it more complex and overlay some other effects. If you're already familiar with graphical patching softwares like Blender, then it won't be difficult to figure out how this is going to happen: we'll add a **composite** node (right-click, Add -> video -> composite). You'll see that now two inputs are available: IN and SRC. In this case, SRC will be applied _over_ IN. To see what it could happen, let's add a **noise** node (right-click, Add -> video:source -> noise), then plug it to SRC and the **solarize** to IN. You may see the picture turn completely white: that's beause we have to change the blend mode (right-click over **composite** -> Edit, then select from _mode_ the option you like.) In my case, I've chosen Mask:

{{<figure src="/imgs/1_OurFirstProject/video_capture_comp_noise.png" alt="" >}}
  
There are also other two options: the first one is _mix_ that will determine how much visible will be the SRC input (in our case the **noise** node); the second one is force-alpha, that forces the input to be transparent (black-> transparent, white -> opaque), but it's not useful right now.

So we said that here on PraxisLIVE, values can be changed during the execution of the project. But to do that, you'll have to change them manually. Immagine that you would like them to change by themselves during the video in a specific ammount of time. How we can achieve this?

First of all, let's add a **blur** node (right-click, Add -> video:fx -> blur), just to test another function. In this case we want the radius to change from 0 to 15 in 5 seconds. To do that let's add an **animator** node (timing -> animator), and plug its output to radius; this is because the node will return, according to our instructions, the radius value that has been generated in that second.

{{<figure src="/imgs/1_OurFirstProject/video_blur_animation_notworking.png" alt="" >}}

Let's modify the parameters (to = 15, value = 0, time = 5). If you'll try to play, no animation will occur. This because we need an *event* that will trigger the **animator** node.
To get this, we need two new nodes:  **variable** and **start-trigger**, both under _core_. Plug them like on the picture:
{{<figure src="/imgs/1_OurFirstProject/video_blur_animation.png" alt="" >}}
What do they mean? A **start-trigger** will trigger all the connected values when the project will start. In our case, _play_ (**capture**) will play at start, _trigger_ (**variable**) means that will insert _value_ content in _to_ socket (**animator**); that will trigger our "animation".
Set **variable** node's _value_ and play the project. You shall see now your webcam input blur gradually in 5 seconds.

So what did we learn in this lesson?
- how to create a project;
- how to use nodes;
- every value can be managed dynamically (or, if you like, animated);
- a project needs a trigger to start, because it doesn't know the concept of time by its own.

Well, it has been quite long, so for this episode we're done!
Next time we'll keep working on this project and learn how to do the following things:

- Modify a node's code;
- Map our webcam input into a 3D cube and make it rotate randomly.

Thank you for reading, see you at the next episode!
