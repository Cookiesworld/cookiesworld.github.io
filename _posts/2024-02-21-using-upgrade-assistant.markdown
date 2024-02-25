---
layout: post
title: "Saving time with the upgrade assistant"
date: 2024-02-21 16:30:55 +0000
categories: dotnet
toc: true
---

# Saving time with the upgrade assistant

I wanted to upgrade one of my code [example projects](https://github.com/Cookiesworld/RomanNumeralsKata) to .net 8, but to save time I decided to try out the [upgrade assistant](https://learn.microsoft.com/en-gb/dotnet/core/porting/upgrade-assistant-overview). This is a Microsoft tool which automatically upgrades dotnet projects.

As I am a Mac users I downloaded the command line tool by using the following.
```
dotnet tool install -g upgrade-assistant  
```
After downloading the tool its run by invoking 
```
upgrade-assistant upgrade .
```
![upgrade assistant](/assets/images/upgrade-assistant.png)
The upgrade must be run for each project in the solution but it should update the associated nuget packages.