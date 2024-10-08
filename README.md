# ML_Computer_Vision_on_Raspberry_Pi_guide
### Step-by-step guide implement ML object detection (and later object tracking) on a Raspberry Pi

# INTRO
## BACKGROUND
I've been part of a RoboCup (Rescue Line) team and among my task was the development of a software which had to identify and track some balls, so that the robot could pick them.

In this guide I'll try to explain my journey and to cover how to train a TensorFlow Lite ML model with your own dataset and then deploy it on a Raspberry Pi.

Now I'd like to share what I've learned, hopefully someone will find it helpful.
<br/>
## REPOS
In this guide I'm trying to organize the code from the following repositories:
- https://github.com/gpego/robot1
- https://github.com/gpego/tflite
- https://github.com/gpego/newCode

They're quite messy 😅, that's why I'm trying to put all toghether in this guide.

You can find the final version of the object detection software here:

https://github.com/gpego/robot1/tree/main/tflite/tflite2/dev

If you are interested in RoboCup (Rescue Line) you can also have a look at the line following software here:

- https://github.com/gpego/lineFollowing
- https://github.com/gpego/arduino-serial-com
- https://github.com/gpego/Python-serial-com
<br/>
## DISCLAIMER
I'm definitely not an expert in this field therefore there might be some errors in the following instructions and I would like to apologize in advance. Anyway I'll write only after having had things working so following the guide you should be able to have something working. I'm not a native English speaker either so there may be some errors on that side too.

IF YOU HAVE ANY SUGGESTIONS, THINK SOMETHING IS WRONG, ECC. PLEASE LET ME KNOW.
<br/>
# SETUP
## HARDWARE
To deploy a TFLite model on a Raspberry Pi you'll need... You guessed it, a Raspberry Pi!

The project started with a Pi 4, then mid-development the Pi 5 came out. Below there's a small description about the Pis I've used:

- Pi 3B+: if you have one laying around it's technically usable to get started but don't expect it to perform real-time object detection
- Pi 4: started with that, if you already have one it's ok (I've tried the 2GB and 4GB), in real-time applications I was able to get ~10fps
- Pi 5 (👑): if you have to buy a Pi, buy this one (I've got the 8GB but the 4GB should be absolutely fine), definitely worth it. Works well in real time and it's also nicer during the development process

If you want to perform real-time object detection, you'll likely need a camera, I would recomend the Raspberry Pi Camera Module 3 (depending on your application the "Wide" version might come in handy) for its image quality and ease of use.


Of course you'll need to power your Pi, the recomended way to do that is throught the official power supply but you can use a battery as well.


ATTENTION ⚠️: if you choose the latter be careful to provide the Pi with the correct voltage (the PS outputs 5.1V but 5.0V are OK). Small variations may well kill the Pi, I mistakenly provided one with 5.95V... It is now hung on a wall as an ornament.


## RASPBERRY PI CONFIGURATION
First of all you'll have to flash the OS onto your microSD card. The [Raspberry Pi OS](https://www.raspberrypi.com/software/) is higly recomended, it is the official Debian distro of the Raspberry Pi Foundation.

The Pi can be connected to actual peripherals but for this application I found it convenient to use it *headless* which means to connect to the Pi using a client device (your PC), basically what you'd do with AnyDesk or TeamViewer.

To read more about the topic and configure a headless Raspebrry Pi I'd recomend [this article](https://www.tomshardware.com/reviews/raspberry-pi-headless-setup-how-to,6028.html) from Tom's Hardware.

Please note that the Raspberry Pi Foundation is currently developing *Raspberry Pi Connect* a proprietary tool to make this process easier and more reliable. I haven't tried it yet, it is in open beta so you can try it out. You can read more about it at [this page](https://www.raspberrypi.com/software/connect/).


## DEVELOPMENT SETUP
Where it is technically possible to do the entire process on the Pi given that it is by all means a PC, I wouldn't recomend it beacuse it can be laggish and unpractical.

There are 2 main approaches you can use instead:
- Method #1: code on a PC and deploy on the Pi afterwards
- Method #2: connect the PC to the Pi and code remotely (👑)

### Method #1
According to me this method is not always optimal beacuse it requires you to sync the code between two devices. The main problem comes when you code on a machine and need to test the code on the Pi (beacuse you need that specific camera, have some connected devices which you need to use, ...). That's beacuse it is an annoying and time consuming process: syncronization isn't immediate and you'll need to switch from a device to the other each time.

As we saw the Pi runs a Linux distro, therefore the software has to be Linux-compatible. Unfortunately the code we're going to make is not platform-agnostic, indeed there are some differences between Windows and Linux (I don't know what's the case for mac). Therefore you'll need to either modify the software each time you move it from one machine to the other or simply use Linux. The latter is the best option.

You actually have several alternatives:

- A native Linux system: either dual-boot or Linux-only
- A virtual machine
- WSL 2 (Windows only): you can read more about WSL 2 [here](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux). If you want to install it I've followe [this guide](https://learn.microsoft.com/en-us/windows/wsl/install). An optional problem with WSL is that you don't have access to the webcam of the PC and its video stream.

After you've setted up your code environment you'll need to syncronize the files between the two devices. We've started using [Syncthing](https://en.wikipedia.org/wiki/Syncthing) but it turned out to be a bit annoying. You have to wait quite a bit for the files to syncronize (~ 10/20 sec) and it is not-so-straightforward to setup and use. By the way if you want to discover more I'd reccoment [this guide](https://pimylifeup.com/raspberry-pi-syncthing/) from PiMyLifeUp.

The best way to syncronize files is throw GitHub, it works better that Syncthing, we found it to be faster and more straightforward, plus you'd need it anyway. The use of GitHub will be covered in another section since it will be helpful regardless of the method you choose to set up your environment.

### Method #2
IDEs allow you to connect to remote hosts where the code is physically stored and run it basically using your PC as a terminal. It may seem similar to screen mirroring but the process is quite different. Indeed it works thanks to SSH (the one you used setting up the headless Pi), in this way it isn't needed to send an entire frame of pixels several times a second. Text (which is much lighter) is sent instead and the IDE on the client (aka your PC) handles the GUI part. In this way you're seamlessly coding on the Pi by remaining on your PC, where you can access all the tools you want that might be unpractical in the Pi (where web surfing itself is quite slow).

I used VSCode since I find it easy-to-use and straightforward and I don't need super fancy features you may find elsewhere. Anyway other IDEs (e.g. Pycharm from JetBrains) support this feature as well. You can follow [this guide](https://code.visualstudio.com/docs/remote/ssh-tutorial) from Microsoft to set up a remote SSH connection in VSCode.


## ⚠️WORK IN PROGRESS!!⚠️
As you can see this guide is currently under development.
