---
title: "Getting started with astronomy programming"
layout: post
date: 2022-01-10 06:46:04
categories: programming
---

I have for a long time had a fascination with space and space-exploration. I am fascinated with and a big fan of everything related with the cosmos and our small unimportant part in it. It is something that I keeps returning to every few months or years but that I have never really gone further into.

With a very long background as programmer and currently working full-time as a freelance consultant doing everything from business analyst assignments over architect roles to management consulting I have good and financially rewarding career. But the one thing that keeps eluding me is purpose and long-term goal. 

My hope of breaking into something space-related is not high, but I would like to have it as a long term goal to somehow participate. This could be just participating on open source software or the like.

While researching it became apparent that one of the widely used languages in space-related programming (if you could ever encompass this massive field into one word) is Python. I have done very tiny projects in Python and wrote some automated deployment scripts in Python in the past, but it is has always been one of the languages I have had my eyes on for a potential next one to become proficient with.

Having recently bought a new laptop and installed a fresh copy of Ubuntu on it I had to set it up for Python development, which I mentioned here: [Setting up Ubuntu 20.04 for Python programming with Poetry](https://jmreiche.github.io/setting-ubuntu-20-04-python-programming)

The next step was finding a place to start my space/astronomy-related programming endeavor. 

## Getting started with Astropy

Looking around for tutorials/projects to start with I found the Astropy project ([Astropy.org](https://www.astropy.org/)) and their guides under "Tutorials" ([learn.astropy.org](https://learn.astropy.org/)). Browsing them and knowing absolutely nothing about any of them I started with the one about FITS files called [Viewing and manipulating FITS images](https://learn.astropy.org/tutorials/FITS-images.html).

Doing this tutorial in VSCode highlighted a problem for me. I could easily run the program/py-file from within VSCode and see the output of the console. So doing:

```python
print("Hello World")
```

Would show "Hello World" in the output of the console as expected. But working through the tutorial linked above I found that I did not see the output that they described:

```python
plt.imshow(image_data, cmap='gray')
plt.colorbar()
```

Would from the tutorials point of view show an actual gray-scale image of the FITS file that was worked on. But I was unable to see this anywhere when I ran the code. I had of course installed the Python extension for VSCode - so that was not the issue.

Researching this for a while I found that what I could do is have VSCode show the "Interactive" window for this part of the code.

While this may not be the only, or the right way for that matter, I found a solution for this.

By adding the following to the top of the python file I was working on, a number of options became available directly above the text in the file and one of them was "Run cell".

```python
# %%
```

When I pressed "Run cell" an "Interactive window" would open alongside the python file and in this there would be the actual image that matched the output from the tutorial. So finally I found a way to get the output described in the tutorial.

As I reached towards the end of the tutorial it became very apparent, that while I sure would be able to just go through the tutorial and get the right results I really needed to up my skills in the data science department.

So my next post will be about me figuring out how to start on that journey and working myself backwards to a starting point that matches my current abilities.

