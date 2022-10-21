---
title: ".Net Core Command Line Commands"
layout: post
date: 2022-02-22 06:46:04
categories: programming
---

If you are a .Net Core developer using Linux you will probably be forced or prefer to use the command line a lot more than what you would if you were on Windows using full-fledged Visual Studio. 

When I program .Net Core on Ubuntu I do all of the adding of projects, references, packages etc, from command line. When I add files, folders etc. I prefer to do it directly from Visual Studio Core.

But every time I have been away from programming a bit I can never remember the commands properly and every time I look them up online I need to try and figure out exactly how they work and from where in the directory.

So this is my own self-help guide to dotnet commands surrounding projects, packages and references.

## Add package

`dotnet add [project name].csproj package Microsoft.AspnetCore.Identity`

## Add reference

Being in the directory of the project that you want to add the reference. So if you have a class library containing models you want to use in your WebApi project, then the command below assumes that you are standing in your WebApi's directory where the WebApi.csproj file is located.

The folder structure in my project looks as follows:

[Main Project Folder]

- WebApi(folder)
  - WebApi.csproj
- DataModel(folder)
  - DataModel.csproj

`dotnet add WebApi.csproj reference ../DataModel/DataModel.csproj `

## Add new project

When adding a new project make sure that you stand in the main folder. So from the structure above then make sure that you are standing inside the [Main Project Folder] and not down in either the WebApi or DataModel folder because then you will create the project and project folder as sub-folder inside that one.

But assuming you stand in the [Main Project Folder] then to create a new class library called Database you can run the following command:

`dotnet new classlib -n Database`

## Add project to solution

Depending on where and how you have create your project you may or may not have a solution-file (.sln) but if you have then this command can be useful for adding your new projects to the solution file and hence having them built together with the other projects.

The solution-file would in the hiearchy used above most likely be located in the [Main Project Folder]. So the command below assumes this.

`dotnet sln Project.sln add DataModel/Datamodel.csproj`





