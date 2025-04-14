---
layout: post
title: "How to Fix \"No Free PE\" When Creating an LVM RAID Mirror"
date: 2025-04-13 11:17:55 +0000
categories: personal
toc: false
---

# How to Fix \"No Free PE\" When Creating an LVM RAID Mirror

Recently, I ran into a frustrating issue while setting up an LVM RAID mirror on my Linux system. The error? “Physical volume sda5 has no free PE”. I’ll walk you through what this error means and how to resolve it step-by-step, based on my own experience.

## What’s the Problem?

I already had an LVM mounted partition. I wanted to add a new disk and create a mirror of the data for redundancy. The error popped up when I tried to create an LVM RAID mirror. The physical volume (PV) in my case, /dev/sda5—has no free physical extents (PE). PE's are the building blocks LVM uses to allocate storage, and if they’re all used up, LVM can’t create the mirror. Essentially, there’s no room to work with.

## Step-by-Step Fix
Here’s how I tackled the issue:

#### Check the Physical Volume (PE).
   
First, I used pvdisplay to inspect /dev/sda5. This command shows the total and free PEs for the PV. Sure enough, “Free PE” was zero, confirming the PV was fully allocated.

``` pvdisplay /dev/sda5 ```

#### Inspect the Volume Group.

Next, I ran ```vgdisplay``` to check the volume group (VG) that /dev/sda5 belonged to. This helped me see if there were any free extents in the VG overall.

```vgdisplay```

#### Free Up Space.

Since my physical volume  had no free extents, I needed to make room. I decided to shrink one of the logical volumes (LVs) in the VG. If your filesystem supports shrinking (like ext4), you can reduce an LV’s size to free up PEs. **Warning check that your filesystem supports resizing and always backup data!** Here’s an example reducing by 50G:

```resize2fs /dev/vgname/lvname 50G```<br>
```lvreduce -L 50G /dev/vgname/lvname```

_Note: Replace vgname and lvname with your actual VG and LV names, and adjust the size as needed._  **Always back up data before resizing!** In my case, shrinking an LV gave me enough free extents to proceed.

#### Create the RAID Mirror

With free PEs available, I retried creating the RAID mirror, and it worked like a charm:

```lvcreate --type raid1 -m 1 -L 100G -n mirror``` <br>
```lv vgname ```

#### Verify Everything

To be safe, I checked the setup with ```lvdisplay``` and ```lvs``` to confirm the mirror was active and healthy.

## Alternative Solutions

If you can’t shrink an LV, here are a couple of other options:

* Add a New PV: Extend your VG by adding another disk or partition as a PV:

    ```pvcreate /dev/sdX```<br>
    ```vgextend vgname /dev/sdX```
 
* Remove Unused LVs: If you have unnecessary LVs, delete them to reclaim space: 
 
    ```lvremove /dev/vgname/lvname```
  

## Pro Tip
Always double-check your PVs for errors with [pvck](https://man7.org/linux/man-pages/man8/pvck.8.html) if things aren’t working as expected:

```pvck /dev/sda5```

## Wrapping up
Running out of physical extents can be a problem, with a bit of investigation and the right commands a solvable one. For me, shrinking an LV did the trick, but your setup might need a different approach. 
