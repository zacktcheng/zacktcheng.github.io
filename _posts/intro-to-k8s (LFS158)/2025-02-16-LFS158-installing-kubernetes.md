# Installing Kubernetes

### Table of contents
- [Installing Ubuntu(Linux) on macOS](#installing-ubuntu-on-macos)
- [Installing Minikube on macOS](#installing-minikube-on-macos)

# TLDR
I have a MacBook Pro and I want to practice Kubernetes on my laptop, so I decided to install Ubuntu on my Mac first, and once the installation is done, I will install Kuberenetes inside a Ubuntu VM. Installing Ubuntu on Mac to run Kubernetes can offer several benefits:
- Ubuntu is one of the most popluar Linux distributions for running Kubernetes because it supports the latest container technologies and has a large community providing support.
- Linux systems generally have better performance and resource utilization than macOS for containerized workloads. Running Kubernetes on a native Linux environment may result in better efficiency and speed.
- By running Ubuntu, you gain access to a wide range of open-source tools and libraries that are optimized for Linux. This ecosystem includes a plethora of software packages through Ubuntu's package management system.
- Ubuntu has strong support for Docker and other container technologies, which are fundamental for running Kubernetes effectively.

## Installing Ubuntu on the macOS

[How to install Ubuntu on Mac](https://www.youtube.com/watch?v=Hzji7w882OY&t=15s)

Let's follow the steps to install:
1. [Download VirtualBox](https://www.virtualbox.org/wiki/Downloads). You can find out which processor your Mac is using through [these steps](https://www.howtogeek.com/706226/how-to-check-if-your-mac-is-using-an-intel-or-apple-silicon-processor/)
2. [Download Ubuntu](https://ubuntu.com/download/desktop). If you need the specific version like me, you can search to download the release like so: [Ubuntu 22.04 Jammy Jellyfish](https://releases.ubuntu.com/jammy/). Also, please pay attention to the system requirements listed below the image release description.
3. Once VirtualBox is completely installed, launch the Oracle VM VirtualBox Manager app.
   1. On the menu bar, click on **New** button, then fill in the name of our virtual machine, this name is going to be the name appears on your dashboard once it is alive. Once the naming is done, click **Continue**.
   2. Now we have to allocate the RAM! It's important to know the minimum amount of memory we need for applications on the VM. We can just leave it 2048 MB for now, and the good news is that we can come back to tweak it even after the VM is created. Then let's click **Continue**.
   3. Create a Virtual Hard Disk for the VM. We can leave the default values but for the space size, **it's important to note that the hard drive space size can not be modified once it is created!** So spend some time to figure out how much it needs. Once the space size's figured out, we can click **Create** to spin up our first VM!
4. Now back on the dashboard. We would like to have some minor adjustment. Click *Settings*, on the popup window, click **Display** and then crank the video memory ll the way up. We can also **Enable 3D Acceleration** to boost the graphic performance but it's not neccessary. Finally, click **OK** to save the changes.
5. Click **Start**. For the first time, the program will ask you to direct the location of your virtual disk of Ubuntu. Just simply locate wherever we saved the downloaded Ubuntu disk file then hit **Start**. This will bring us to the Ubuntu installation screen.
6. On the installation screen, click **Install Ubuntu**. We can keep the default checkbox settings then set up the username and password.
7. Once you see the purple desktop background shows up with an animal *(Mine is a jellyfish)*, you are all set! Now the Ubuntu VM is ready to play with! 

## Installing MiniKube on the Ubuntu 22.04 (Jammy Jellyfish)

Let's follow the steps to install:
1. v
2. v
3. v
4. v
