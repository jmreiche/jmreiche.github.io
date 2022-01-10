---
title: "Setting up Ubuntu 20.04 for Python programming with Poetry"
layout: post
date: 2022-01-09 06:46:04
categories: programming python
---

As part of my venture into space-related programming I found that I needed to educate myself a lot more on Data Science. My language of choice for this endeavor is Python. I am a very experienced C# developer with over 15 years of experience in the field and has also done a tiny bit of Python before. But as I recently bought a new X1 Carbon Gen 9 and made a fresh install of Ubuntu I thought I would document my steps to have a running environment.

Hopefully I have not left out too many steps. I am doing this from memory as over the course of setting this up I was having to constantly search for answers to my problems. So the things that went smooth for me I may have missed. But feel free to contact me if you run into any issues. 

As mention my new daily driver runs Ubuntu and fortunately Ubuntu offers great support for Python. To set up my local machine for Python development I followed this guide to include both pipx and poetry: [Setting Up a Modern Python Development Environment on Ubuntu 20.04](https://www.laac.dev/blog/setting-up-modern-python-development-environment-ubuntu-20/) 

This guide functioned perfectly for me, but left me with a question:

> Now that I have set up the environment etc. how do I actually get started with a new project?

If I just google how to start a python project then I will spawn/create a project that does not use Poetry. So how do I actually use Poetry, now that I have installed it to make sure that there is a safe line between the packages used by the operating system and my projects?

After a bit of searching I found the following. To create a new Python project I ran this command inside the folder where I wanted to create my project:

```
poetry new python3
```

After having created this I actually started using it and stumbled upon a problem. By default Poetry creates the virtual environment in its "own" directory. But Visual Studio Code does not recognize this. So all the packages that you install will not be found by VSCode and it will not run the virtual environment you created.  

In order for Visual Studio Code to "recognize" the poetry environment I had to use the solution from this answer on SO: [VSCode doesn't show poetry virtualenvs in select interpreter option](https://stackoverflow.com/questions/59882884/vscode-doesnt-show-poetry-virtualenvs-in-select-interpreter-option)

So in short type the following in CMD:

```
poetry config virtualenvs.in-project true
```

If you already have created a virtual environment for your project then do the following:

```
poetry env list  # shows the name of the current environment
poetry env remove <current environment>
poetry install  # will create a new environment using your updated configuration
```

You may need to close VSCode and open it again for it to recognize the new venv. After I restarted VSCode it worked perfectly and VSCode could recognize the packages I had installed.

With these steps completed I had a fully running Python project with poetry taking care of my dependencies.

So the next section here is a bit out of my depth. I am unsure about whether Poetry automatically will "register" all dependencies added by pipx inside this project once it is created with Poetry. But so far I have just used Poetry to add the dependencies/packages to ensure this:

```
poetry add <package>
```

If you run into issues with Poetry I can recommend their documentation: [https://python-poetry.org/docs/basic-usage/](https://python-poetry.org/docs/basic-usage/)

