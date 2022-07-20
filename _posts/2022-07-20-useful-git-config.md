---
layout: post
title: "Useful git config"
date: 2022-07-20 17:51:55 +0000
categories: git
toc: false
---
For anybody who has run git push only to get the following
```
git push                                                                        
fatal: The current branch test has no upstream branch.
To push the current branch and set the remote as upstream, use

git push --set-upstream origin test
```
It is now possible to default git to automatically set the upstream branch by setting the config
```
git config --global push.autoSetupRemote true
```
Now git will automatically set the upstream branch without you having to use --set-upstream.