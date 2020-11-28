---
layout: post
title:  "Running Windows and Linux on the same machine"
date:   2020-08-31 00:00:00 +0200
comments: true
categories: ~
---
For over ten years I've been happily using Windows for personal needs and Linux for work. I find Windows perfect for gaming, media and browser based usage. And as a software engineer doing mostly backend the OS of choice must be Linux. This strict segregation based on usage has worked ok-ish for me most of the time, since I tend not to mix work and personal life too much. Still, there have been some odd cases where I wished I could have both OSes usable interchangeably. From time to time I take on some contract work, or start a hobby project.

Maybe the obvious solution to my longing would be to jump on the Apple bandwagon, but first of all I'd avoid needlessly spending thousands of euros on shiny hardware, and second I really dislike, on principle, how aggressively Apple is trying to get users locked into their ecosystem. So it's just Windows and Linux for me. I'd switch fully to Linux if I could, but sadly Ubuntu did not really deliver on the usability front to make me abandon Windows.

Now let's see if we can get my problem properly defined. I'd want to be able to use both Windows and Linux with the following conditions:
- both must run on the same machine
- switching from one to the other must be fast
- filesystem must be shared; in both directions
- Windows must run natively (gaming, video playback)
- Linux must be (relatively) fast for compiling, transpiling, building etc.
- easy to manage - I need this mostly for hobby work, so I don't want to invest too much time to set it up or keep it running

## What I've tried so far?

Having a Linux virtual machine. It's slow and file sync and networking are a mess. Docker? Kind of the same thing as a virtual machine, but a bit faster. Didn't try that, but I'm guessing the admin work of keeping this running on Windows would be too much, and I'm betting the files sync and networking would need some tweaking and fixing from time to time. It's that way with Docker on Linux, so I'm not expecting better with Docker on Windows.

The other solution I tried, was keeping Linux on a secondary boot next to Windows. That's definitely fast since both run native, but it comes with some serious drawbacks. First, you can't switch between them. You need to restart! That in turn made me not really want to do much work on Linux, since it's so inconvenient to switch. And when I set aside a larger block of time to do some coding, I realised it's been so long since last time that my system is too outdated, so I needed to spend lots of time updating packages.

## Is there something that works?

Apparently since a couple of years Microsoft started to show some real developer love and worked on the Windows Subsystem for Linux. The point of it being to allow running Windows and Linux together - exactly what I always wanted. The first iteration of WSL, coming out in 2018 was Linux _friendly_, but that was kind of it. It's not that helpful to have **bash** available, but to not be able to install all the packages that would work on a real Linux. The good news is that starting with Windows 10, Version 2004, so end of May 2020, the new WSL 2 became generally available. And this one has a real Linux kernel, making it fully Linux _capable_. We're talking about real distributions on which you can run real Linux software, not just a fancier shell, like in version 1.

## So how do you get it running?

It's fairly easy actually. First check that you are on a supported version of Windows. This is a relatively new feature.

Then enable the 'Virtual Machine Platform' optional component and set WSL 2 as your default version. Microsoft has been kind enough to [document](https://docs.microsoft.com/en-us/windows/wsl/install-win10) all these steps. But you can also just go to Windows Features and tick the Windows Subsystem for Linux checkbox. A restart will be needed.

![Check the WSL box](/assets/wsl2/wsl-box.png)

Next up is picking a Linux distribution of your choice. There are quite a few to choose from. I quickly took a Ubuntu 20.04 LTS because I was to eager to try it out and in web development it's hard to get it too wrong with Ubuntu.

![Linux distributions](/assets/wsl2/linux-distros.png)

And at this point you can just search for your Linux (Ubuntu in my case) through the Windows applications and jump right into it.

## What can it do?

Well, for starters, it _can't_ do a multi-tab terminal. So that was the first thing to improve after getting it working. I picked up the Windows Terminal from the Microsoft Store. Then I went to its Settings and changed the configuration a bit so that new tabs are Ubuntu ones. It just requires identifying the right profile ID in that config file and replacing the default profile with it.

Then I wanted to check if I can quickly serve a web app from inside Linux and use an IDE and browser on Windows. My go-to recipe for pulling a web server up in a few steps is Flask, so I did just that.

Choose a nice cosy new project folder and set up a new python environment.

{% highlight bash %}
python3 -m venv venv
. venv/bin/activate
pip install Flask
{% endhighlight %}

Then inside an **app.py** goes the Flask micro application.

{% highlight python %}
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'
{% endhighlight %}

And you serve it with.

{% highlight bash %}
flask run
{% endhighlight %}

And to my delight I was greeted by the most beautiful, and standard, Hello, World! page.

![Hello, World! in browser](/assets/wsl2/hello-world.png)

So networking is working just fine, but how are we doing with sharing the filesystem? Let's not forget about that. From Linux to Windows, as one would expect the Windows volumes are already mounted. You can find them in /mnt.

{% highlight bash %}
cd /mnt
ll
{% endhighlight %}

And the other way around, and here comes a bonus feature, you can call the Windows Explorer directly from the Linux terminal and it will open in Windows. Then you can see where your Linux filesystem is from the Explorer location bar.

{% highlight bash %}
cd ~
explorer.exe .
{% endhighlight %}

And with this comes the last piece of the puzzle: the IDE. Since the Linux files are visible to Windows, it means you can use am IDE on Windows. I added PyCharm for that. It mostly works and it even has some integration with WSL, but only in the Professional version.

One important thing to remember is that your project folder should be under the Linux filesystem for faster performance. Having it anywhere will work, as I did before reading the [disclaimer](https://docs.microsoft.com/en-us/windows/wsl/compare-versions#use-the-linux-file-system-for-faster-performance), but it's considerably slower.

## Conclusion

WSL 2 is for sure a perfect match for my problem. I now have both Windows and Linux running not just on the same machine, but side by side, with seamless jumping from one to the other. The filesystem is fully interconnected and the basic networking is hooked up by default. The performance of Windows has not been affected. The Linux part runs pretty good too, especially since there is no need to have an UI. It really feels like having the best of both worlds. And for my personal use it may be perfect. Still, I'd give it some more time until considering it for professional work. For that I'll stick to my Debian box for now. But who knows, maybe in a few years this system will be mature enough even for the office. Fingers crossed!

