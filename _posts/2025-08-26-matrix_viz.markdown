---
layout: post
title: Bandwidth Visualizer
categories: blog
---

<p style="text-align:center;">
<iframe width="420" height="420" src="https://youtube.com/embed/PvReemZQBMc?si=URhuCU08FmaoV6Wv" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
</p>


A while back I came across this [Stellar Unicorn](https://shop.pimoroni.com/products/space-unicorns) in the clearance bin at Micro Center for $15 and picked it up.  It's basically a 16x16 RGB LED display with a [Raspberry Pi Pico W](https://www.raspberrypi.com/documentation/microcontrollers/pico-series.html) stuck to the back of it.  It comes with a few neat example graphics scripts, but the real purpose is for you to write your own.  It was only now that I figured it was time to sit down and learn how to.

![matrix](/assets/matrix1.jpg)

My plan was to create a network bandwidth visualizer fashioned after the [title sequence to The Matrix](https://www.youtube.com/watch?v=kIXNpePYzZU).  My network at home is Ubiquiti-based, with a [Unifi Dream Machine](https://www.ui.com/me/en/cloud-gateways/large-scale) at the core, which is nice in many ways but also provides API access via the [Ubiquiti API](https://developer.ui.com/site-manager-api/gettingstarted).

This project has been a mix of a lot of things I'd been meaning to get into or put to use but just never had the right excuse:
- Network visibility and programmability
- Asynchronous programming in Python
- Embedded systems

The Python side of things is [MicroPython](https://micropython.org/download/RPI_PICO_W/), a stripped down and streamlined version of Python made for microcontroller boards such as this RPi Pico W.  When you flash the MicroPython firmware to the board, it creates a little filesystem for you to upload your scripts, and at power-on, it finds and runs main.py.  It's also got its own version of pip, [mip](https://docs.micropython.org/en/latest/reference/packages.html) (which came in handy for installing [uasyncio](https://docs.micropython.org/en/v1.14/library/uasyncio.html) and [ujson](https://docs.micropython.org/en/v1.15/library/ujson.html)).


Inside, I have three loops running asynchronously (in parallel):
1. Poll the API at regular intervals and update the downstream utilization on the uplink in bytes per second. 
2. Spawn the dots at the top of the screen according to an interval based on the uplink utilization.  
3. Update the positions of the dots as they scroll down the screen, clearing out the ones that reach the bottom, and then update the display according to the configured frames per second variable (currently set to 4 FPS, which is slow but I'll explain).

I had originally had these running synchronously, which was a tremendous pain because I had to:
1. Track when each variable was last updated
2. Track how long it's been since the last update
3. Actually update the variable
4. Update the last update time.

Even with just a handful of variables, it took a lot of time that I should have just spent learning async io.

![matrix2](/assets/matrix2.jpg)

The remaining problem is, when the API call is made, the whole device freezes for a fraction of a second, in spite of being run asynchronously.  It doesn't sound like much, but it manifests as a very annoying stutter that is more noticeable at higher FPSes.  Even at 4 FPS it is still there and that's as good as I can get it.

There are also four buttons on the back of the display, and I've been wondering about what kinds of things I could do with them with the API.  Turn the ad blocker on and off?  Cut my kid's internet access when necessary?  Maybe initiate a graceful shutdown in case of a power outage.

In any case, I'm hoping to make a library of these visualizations because I spend my day at work looking at line graphs, but it's nice to see network activity in a more tangible way, even if it's completely qualitative and not exactly representative of reality (I fudged with multipliers a lot to get it to "look right").

Oh, and another great thing about this -- it's got wifi, so I can just put this anywhere in the house, give it 5VDC and it automatically connects and starts doing its thing.

[Github repository here](https://github.com/2fivefive/stellar-unicorn-viz)
