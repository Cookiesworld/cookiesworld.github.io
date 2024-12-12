--
layout: post
title: "Performance tuning with react scan and million"
date: 2024-12-10 11:17:55 +0000
categories: personal
toc: false
---

# Performance tuning with react scan and million

I recently saw an application call [react scan](https://react-scan.com/) on twitter, so I decided to run it on my [wordle solver](https://wh.johncooke.info) to see what performance enhancements it suggests.

The easiest way to run is to simply install as a dep and run against your site
```
npx react-scan@latest http://localhost:3022
```

Once running react scan will highly rerenders of you components so you can see hot spots. It also recommended that I install the performance linting tool [million lint](https://old.million.dev/blog/lint). I then added the Million Lint vscode addin and added million to the vite.config referencing ```import MillionCompiler from "@million/lint";``` and added million compiler to the plug-ins array ```plugins: [MillionCompiler.vite(), react(), eslint()]```. Next time I ran the application the VSCode add-in started making suggestions as I rant he application, for example suggesting that I wrap components in memo to prevent unnecessary re-renders.

![Million lint results in VSCode](/assets/images/Screenshot%202024-12-10%20at%2011.35.59.png)

Not all of the suggestions were that helpful, but overall I refactored some rerendering out of the app.