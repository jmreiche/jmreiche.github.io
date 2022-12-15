---
title: File from template VS Code
layout: post
date: 2022-12-15 06:46:04
categories: programming vscode
canonical_url:
---

One of the small issues that kept me from writing as much on this blog as I could have, was to do with the way a new blog post was added. In a way it is quite simple as it is just another markdown file added to a folder and that is it.

But then the name has to follow a format, starting with the date of the blog post, followed by what will be part of the url for the blog post. Then at the top of the markdown file there is a small header, that contains information about the title of the blog post, the date and which category to put it into.

In the beginning I always copied an existing file, then had to rename it, remove all the old text and try to remember the format for the date. 

Was it YYYY-dd-MM or YYYY-MM-dd? 

And of course the blog post I copied always had a date that could work either way. 

Then if I wanted to add more than one category I had to remember whether it was space or comma that separated them?

All in all very small issues - I know.

But somehow it was enough to keep me from writing as much on this blog as I perhaps could have. 

I used a site called Nicheless.com - which had this very neat interface and 300 word maximum. It was just the right amount of nice layout, low barrier of entrance and a word count that kept me writing small pieces, which I would not have added to my own blog.

But then I figured, why write all this information to a domain I do not own. Why not just write these small pieces to my own page? I own it, so it is my decision whether blog-posts should be 200 words or 2000. 

Hence I had to find a way to make my barrier to entry lower for writing to this blog.

I use Visual Studio Code to maintain this site and hence went looking for an extension that could deliver what I wanted. I am very much a "keyboard" guy and wanted to be able to do everything from the keyboard.

After some research I found [File Templates](https://marketplace.visualstudio.com/items?itemName=brpaz.file-templates) which is a very basic extension, that enables you to create any type of file as template. The big plus for me is that it add an entry to the content menu, meaning that if I navigate to the file explorer in VS Code, goes to the folder I want to add a file to and then presses Shift+F10 to bring forward the content menu, then there is and entry called "New File from template" that I can use.

This bring a list of files which I have put in the "template folder" and once I have selected the one I want it will promt me for a filename - and voila - I have a new file in the right format just to start working on.

The template file is simply a file you create and put in the directory for the extension. I am on Ubuntu and it looks in this directory:

`.config/Code/User/FileTemplates`

I created a file called "NewPost.md" and it has the pre-filled header for this blog with a date in a format that makes it easy for me to recognize the format, as well as an example of more than one category, so I won't forget that either.

All in all it makes it so much easier for me to create new posts for this blog. Seeing that I now have created, this post included, 4 posts within the last week, then I must conclude that it has worked. Then I just need to keep it going.