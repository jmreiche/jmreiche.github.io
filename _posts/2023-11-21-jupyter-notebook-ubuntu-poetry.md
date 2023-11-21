---
title: Jupyter notebook ubuntu poetry
layout: post
date: 2023-11-21 06:46:04
categories: programming python
canonical_url:
---

I recently found some free online courses on deep learning that piqued my interest. They used Jupyter Notebook for some of the material and even though they recommended using kaggle.com I wanted to run it locally. 

My main laptop runs Ubuntu so I had to figure out how to run it on that platform. I did a bit of experimentation with python a couple of years ago but as I found out I had been better of just starting from scratch with the setup as all things I thought was in order turned out not to be. Hence I will write this little guide to myself and potentially others that might need it in the future.

The one thing I remembered from last time - thanks to this blog - is that I used "Poetry" to control packages. So I wanted to keep using that. As I found out I must have removed it or done something to it as I had to start all over from scratch with that as well. But this guide shows how to get Jupyter Notebook running using poetry.

 First of all I used this guide to install pyenv: [https://www.laac.dev/blog/setting-up-modern-python-development-environment-ubuntu-20/](https://www.laac.dev/blog/setting-up-modern-python-development-environment-ubuntu-20/)

It explains how to do it easily so use the step-by-step guide to install pyenv.

Next it explains how to install pipx. In order to run Jupyter Notebook we do not need pipx - so you can omit that part. 

Finally it talks about installing Poetry and recommends using other method that pipx. As I did not install pipx this recommendation aligns perfectly.

You can find the link to installation here: [https://python-poetry.org/docs/#installing-with-the-official-installer](https://python-poetry.org/docs/#installing-with-the-official-installer)

It basically recommends running this curl command to download a script that will take care of the installation. It is also possible to copy the content of the script from that page as well as other options, so please refer to the link above if you do not want to run the script I supply directly.

But this is the command I ran:

`curl -sSL https://install.python-poetry.org | python3 -`

Once I had installed Poetry I changed directory into where I wanted to have my Jupyter Notebook test project placed. 

Then I ran:

`poetry new jupyter-test`

This command creates a folder with the name "jupyter-test" which contains files for the new project. You can see the full specification of what "poetry new" does here: [https://python-poetry.org/docs/basic-usage/](https://python-poetry.org/docs/basic-usage/)

Then I changed directory into the newly created folder:

`cd jupyter-test`

To install and activate the poetry environment run:

`poetry install`

Then in order to add/install Jupyter Notebook run:

`poetry add jupyter`

This will add the Jupyter Notebook files to the directory and project we created.

And finally to run the Jupyter Notebok I used the following command:

`poetry run jupyter notebook`

If everything goes to plan, then a webpage will open and you can see the Jupyter Notebook interface. 

In order to open a notebook then just select File -> New -> Notebook

I hope this helped. If everything goes to plan, then I might update with my progress in the future.

Happy coding.