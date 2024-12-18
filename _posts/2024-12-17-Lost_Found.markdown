---
layout: post
title: Lost and Found
categories: blog
---
![oops](/assets/rubyfail1.png)

Uh oh.  

Now that it's almost the end of the year I reminded myself that one of my 2024 New Years Resolution was to document my accomplishments more, and this blog was supposed to be the medium.  I got a few good ones at the beginning of the year but like all my New Years resolutions I fell off at around April.

Since the last post I've reinstalled my OS a few times, taking my home folder along with me, which for the most part has been working just fine.

...until now.

I had to reinstall [Ruby](https://wiki.archlinux.org/title/Ruby) to get [Jekyll](https://jekyllrb.com/) going again.  Unfortunately what I got was the above error, along with a whole day's worth of other errors as I fell into a edit-check-retry loop.

Eventually I came across this line in the Arch page for Ruby:

![Ruby](/assets/rubyfail2.png)

A `sudo pacman -S ruby-sdlib` finally cleared up the last of it.  I'm actually not sure if that was what started everything in the first place but it was the end of several hours of reinstalling and updating and uninstalling Ruby and Gems and Jekyll and editing files all over the place.

When Arch says to read the manual, you really can't skim the manual because of things like this where there's a line that says "you _could_ do this thing if you needed to, and chances are you do need it, but we're going to leave it up to you."
