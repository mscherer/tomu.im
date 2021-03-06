---
layout: default
title: A Prototype of an FPGA In Your USB Port
permalink: /fomu-evt3/
redirect_from:
  - /fpga-evt3/
  - /fpga-evt3
---

<style>
.container {
  max-width: initial;
}
</style>

# Fomu EVT3

Fomu EVT3 is the revision that shipped for _Crowd Supply_ backers of the **Fomu EVT Board** tier.  This page covers how to get started, as well as what you can expect to do with the board.

## Getting Started

Fomu EVT3 requires the following components:

1. Fomu EVT3 development board
1. Raspberry Pi
1. SD Card

Download the [latest Fomu Pi Image release](https://github.com/im-tomu/fomu-pi-gen/releases/latest) image and write it to an SD card.  This can be done in a variety of ways.  You can consult the [official Raspberry Pi documentation](https://www.raspberrypi.org/documentation/installation/installing-images/) for recommendations on how to do this.

Insert the SD card into your Raspberry Pi and apply power.  The first time it runs, it will reboot several times.  This is because it's doing various setup processes such as generating SSH keys and creating the user partition.

Once the Raspberry Pi has finished, you can connect to it either via SSH or by directly logging on using a keyboard and an HDMI monitor.  The default username and password are:

* Username: **fomu**
* Password: **fomudev**

The Fomu EVT3 image does not contain a GUI, as it is designed to be run from the command line.

## Loading your first project

To load your first project, clone the `fomu-tests` repository and build it:

`git clone https://github.com/im-tomu/fomu-tests.git`

Build the simple `blink` demo:

```sh
cd fomu-tests/blink
make
```

This will generate `blink.bin`.  Finally, load it onto the Fomu hardware:

```sh
fomu-flash -f blink.bin
```

![Fomu EVT1](img/tomu-fpga-evt-1-smaller.jpg)

## About the Fomu Raspberry Pi Image

The Fomu Raspberry Pi image has a number of design decisions that may seem unusual at first.

### The root filesystem is mounted readonly

SD cards have a tendency to become corrupt when writing to them as you remove power.  Unfortunately, removing power causes log messages to be generated, which are written to disk just as power is removed.  Some cards can only survive a few power cycles before they become completely unusable.

Making the root filesystem readonly solves this problem completely.  It is easy enough to temporarily make root read-write:

`sudo mount -oremount,rw /`

However, this should only be done if you need to do something such as updating packages.  In general, you should leave it mounted readonly.

### /home is its own partition

This was done in order to make the home directory read-write.

### Time is set using htpdate

Sometimes ntp is blocked.  Furthermore, ntp doesn't like to adjust the time by relatively large amounts.  By using htpdate in "force" mode, we avoid needing special ports open and the Pi just needs internet access.

### There is no GUI

This build was designed to be accessed via a command line.  However, a GUI can be installed using `apt`.

### There is no Risc-V compiler

This is a known issue, and we're working to build one for it.