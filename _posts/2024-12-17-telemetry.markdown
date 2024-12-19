---
layout: post
title: telemetry
categories: blog
---

![speedo1](/assets/speedo1.jpg)

One of several undocumented achievements this year, and probably my proudest, was this speed/tach for EA's F1 2024.  I managed to pull in the whole family to make this happen, with some amazing contributions from each member.

Work had sent me to [Cisco Live](https://www.ciscolive.com/) in June, where there was a pretty sizable McLaren F1 presence since Cisco are one of their major sponsors.  Zak Brown and Oscar Piastri were there for one of the major keynotes.

![Cisco Live](/assets/ciscolive1.jpeg)

Anyway, there was a Splunk demo booth at the show floor with some racing simulators set up.  I tried my hand and managed to get second place at the time (not _that_ huge of an achievement since it was pretty early in the day.  I checked back in a few hours later and the best time was several seconds faster than the one I had posted).  On the sides of the booth, Splunk was demoing [a plugin that tracked telemetry data](https://splunkbase.splunk.com/app/4884) streaming out from the game.  It saw everything from tire temperatures to steering and throttle/braking inputs, but for the purposes of Cisco Live, it had a simple map of the Montreal track with a heatmap of speeds that players were traveling at.

The main takeaway from this was, there was some manner of data being streamed out from the game.  Some further research brought me to a [forum post](https://answers.ea.com/t5/General-Discussion/F1-24-UDP-Specification/m-p/13745520) where there was a pretty formal looking pdf file detailing the spec.

Apparently, when set to do so, the game puts out a stream of UDP packets to a port and destination of your choosing.  The payload of each packet is a bunch of unstructured binary information to pick apart.  This was where I enlisted my wife, who deals with data streams in her day job.  I showed her the doc from EA, and she knew exactly what to do with it.  At this point it was only a passing "Hey wouldn't it be neat" sort of thing but a few hours later she came back and told me she had written a preliminary parser for the telemetry stream, and from there it was up to me to figure out what to do with that data.

I had to tweak the code a bit because I really just wanted to focus on the speed and tachometer metrics (you really get everything out of this stream, including car pitch/roll, track temperature, individual tire temperatures, etc.), but my contribution to this all was on the network side.  The only way to test the code was to capture a live stream, and the only way to have a live stream was to be (ostensibly) playing the game.  For the first few iterations I would have to play the game while my wife watched the printlines to the console, but I realized I could simulate this with a packet capture.

[Wireshark](https://wireshark.org) has a replay feature where you can give it a recording of packets and it will send those back out on the network at your request.  This is where things like [replay attacks](https://en.wikipedia.org/wiki/Replay_attack) happen.  Anyway, I recorded the UDP stream of myself driving around Suzuka, and had Wireshark replay that over multicast on the network. From there, whoever was interested could listen in and test their parser.

![speedo2](/assets/speedo2.jpg)

So, now we have some relevant data.  What next?  Our kid has a [Circuit Playground Express](https://www.adafruit.com/product/3333) that he noodles around with now and then.  With the [CRICKIT](https://www.adafruit.com/product/3093) attachment it can control a few servos, which we would use for the gauges.  The only problem was, it had no network capabilities.  That was where the [Feather M0](https://www.adafruit.com/product/3010) came in.  It has wifi, but no CircuitPython support, which was key for having our kid involved.  So, I had to configure the Feather to talk to the CircuitPlayground via a serial stream over the alligator clips.

Our kid was in charge of the servo code.  His task was to write a function with two inputs, and move the servos to the right spots on the gauges.  I have a huge regret here in that we did not preserve the code because we were just coding it live on the CircuitPlayground.  Along the way we had a handy little test and calibration suite too.  It was relatively simple, but the code was entirely his and we were all extremely proud of it.  Unfortunately it was wiped when we started our next project, which I may get around to posting here.

In any case, it works!  Here's us testing it.  He did a great job on that chicane too!  Github repo for the project [here](https://github.com/minsun-ss/racingtelemetry).

<iframe width="560" height="315" src="https://www.youtube.com/embed/CRz9H5GCs_4?si=YOq3tJucevnV7BKS" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

He was 8 years old at the time of the video.
