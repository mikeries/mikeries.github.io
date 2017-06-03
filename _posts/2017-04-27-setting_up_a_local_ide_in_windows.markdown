---
layout: post
title:  "Setting up a local IDE in Windows"
date:   2017-04-27 21:39:23 +0000
---


I see a lot of folks in Slack struggling with Learn IDE disconnect problems, so I thought I'd share how I set up my local IDE in Windows.

My first thought was to simply set up an IDE inside windows run do ruby and rails development.  There is some [information on how to do this](http://railsinstaller.org/en).  Unfortunately, I did some additional research and learned that some of the Gems that we have all come to know and love (e.g. Nokogiri) use native OS extensions that simply don't work in windows.

> So what can a Windows user do?

There are several options.  One is to[ install another OS alongside your windows OS](http://www.everydaylinuxuser.com/2015/11/how-to-install-ubuntu-linux-alongside.html).  This works just fine, but it requires modifying your OS boot system and repartitioning your hard disk. Rather intimidating!

An alternative is to run a different OS *inside of windows*.  What does that mean?  Well, it means that you boot into windows, but you start an application that emulates another computer.  Huh?  This is what is known as a virtual machine, and the software that runs the virtual machine is known as a hypervisor.  The hypervisor creates a controlled environment in which it can load and run programs.

> I'm still confused.  My Windows OS already does this, why do I need this *virtual machine*?  

One reason is that the virtual machine can be almost any OS that you choose, provided that you have installation media.  In effect, the program that is running believes that it is on a computer with the chosen OS. So you can run all your usual apps (email client, browser, minecraft, etc.) but you'll have an additional app -- the hypervisor --which is like another computer running its own programs inside.

The responsibility of the hypervisor is to make the *guest* VM believe that it is inside its own little universe, and to provide it with access to the 'real world' using an interface that the guest OS understands.  That means that when the guest OS wants to put the phrase "Hello World" on the screen, it does so just as it normally would, and the hypervisor  translates it for the *host OS*, which writes it to the screen.

As an aside, this pattern exemplifies many software development patterns - [encapsulation](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming)), [interfaces](https://en.wikipedia.org/wiki/Interface_(computing)), the [adaptor design pattern](https://en.wikipedia.org/wiki/Adapter_pattern),  and others, but that discussion is worthy of another blog post (or 3).

So now that we understand a bit about what a virtual machine is and what it does, let's move on to actually setting one up for our local IDE.

## Installing VirtualBox
There are many [options for virtualization](https://www.davescomputers.com/support-seven-alternatives-virtualbox/) software.  Here I'm going to show you how to set up VirtualBox, which is free, open source, and very popular.  I'll also be using Xubuntu, which is a variant of Ubuntu, but you should be able to use any version of linux and the process will be very similar.

1. Head over to [https://www.virtualbox.org/](https://www.virtualbox.org/) and download the hypervisor software.  Or you can click [this link](http://download.virtualbox.org/virtualbox/5.1.20/VirtualBox-5.1.20-114628-Win.exe) to download the installer for windows.
2. Download Xubuntu or another linux distribution of your choosing.  You're looking for a file with an '.iso' extension, and you'll want to be careful to pick the one matching your machine architecture.  For me, I downloaded this file: [http://mirror.csclub.uwaterloo.ca/xubuntu-releases/16.04/release/xubuntu-16.04-desktop-amd64.iso](http://mirror.csclub.uwaterloo.ca/xubuntu-releases/16.04/release/xubuntu-16.04-desktop-amd64.iso).  It's a big file, so go grab a cup of coffee.
3. Run the installer.  You will be presented with some options for features to install.  You can look over these if you want, but I just installed all the default stuff.  There will be a warning that you'll lose your network connection momentarily, so be prepared for that, but it'll only be for a second or two.
4. Run VirtualBox.  You'll see a welcome page.  Click the 'new' button to create a new VM.  Choose a name for your VM, and select "linux" for the OS type.  For version, choose either Ubuntu (64-bit) or Ubuntu (32-bit) depending on your computer.
5. The next setup dialog asks you how much memory to allocate to the VM.  I was cautioned not to be too stingy or my development environment would be slow, so I went with 4GB, which was half of my total system RAM.
6. Next, you'll set up a hard disk for the VM.  By default, it will create a hard disk that is 10GB in size.  **I recommend bumping this up to 20GB or more**.  This will create a large file on your C: drive, so you might have to make some room.
7. For the hard disk file type, go with the default VDI unless you have a reason not to.
8. I chose to use a dynamically allocated disk.  This means that the physical hard disk space that VirtualBox allocates for VM storage will only be as large as is required by the VM.
9. Next it asks how big to make the hard disk.  I'm not really sure what this setting does, giving the previous pages, so I left it at the default.
10. You should now be back at the VirtualBox management page, but this time with your fresh new VM in the list on the left.  Click on it and select Start.
11. The VM will start, and will ask you to select a startup disk.  Click on the little folder icon with the green ^ to open your file browser, and navigate to where you downloaded your '.iso' file in step 2.  Select the file, and click the start button.
12. The Xubuntu installation process will begin.  Pick "Install Xubuntu" to proceed to the next page, and click both checkboxes to download updates during install, and to download third party drivers.  Then 'continue'.
13. When prompted, choose to erase the disk and install Xubuntu.  **Don't worry, it's not going to erase any of your windows files!**  Remember, Xubuntu doesn't have any idea that it isn't the only thing on your computer.
14. Go through the rest of the setup process, selecting your timezone, keyboard preference, computer name, and creating a login.  You might want to check the box 'automatically log in'.  **Remember your password!**
15. The downloading and installing takes a while.  Go take a shower.
16. After you towel off and get dressed, you should see "Installation Complete" and pressing the button will restart.  When I do this, I get an error about 'no valid rapl domains', and it hangs.  From the Xubuntu menu, choose Machine->Reset, read the warning, and then press reset to continue.
17. It boots!  You now have a Xubuntu virtual machine running on windows!

## Adding Guest Additions
VirtualBox comes with additional software that can be installed in the guest OS to provide better hardware support.

**TIP: VirtualBox menu shortcuts are labeled 'Host-H' for example.  For windows hosts, the Host key is the Right-Ctrl key.**

1. From the VirtualBox menu, choose Devices->'Insert Guest Additions CD Image'.  This tells VirtualBox to insert an imaginary CD into the imaginary CD drive of the Xubuntu computer, and Xubuntu opens it.  
2. Right-click inside the file manager window and choose "Open Terminal Here".  In the shell, type`sudo bash autorun.sh` and enter your password if prompted.  Another terminal window will open and some additional files will be installed. 
3. Reboot the machine (Host-H).

## Update the OS
1. At this point, it's good idea to update the software.  In Xubuntu, this is done by opening the little blue mouse icon in the upper left to open the system menu, then select 'Settings' on the right, and scroll down and pick 'software updater on the left.  
2. Enter your password and grab some breakfast.  *You may see an error that your application panel unexpectedly closed.  Just tell it to relaunch.*
3. Restart the machine when done.

## Install your Dev Environment
### Install Chrome
1. Open the system menu by pressing the blue mouse icon in the upper right.  Choose web browser to open up firefox.
2. Navigate to https://www.google.com/chrome/ and click the "Download Now" button.
3. Accept the license agreement, and choose the default software installation option.
4. The software installer opens.  Press the "install" button, and type in your password when prompted.
5. Chrome will install, and you can access it through your system menu.  Right-click on it and make it a favorite and/or put a launcher on your desktop for quick access.
6. Launch chrome.  It may ask you for a password for the 'keyring'.  If you put one in, you will have to enter this password whenever chrome starts, which can be a nuisance.  If you want, you can leave the password blank.
7. Sign in to chrome, so that your settings/bookmarks/etc. will be synced.

### Set Up For Learn
1. Open chrome, and navigate to https://learn.co/manual_setup.  Be sure to do this from within the VM!  The page should tell you something like 'Manually Configure Your Environment For Ubuntu Linux".
1. *Carefully* follow the instructions on that page, and *don't miss the steps before step 1*. **TIP: You can copy and paste from the browser window to your terminal, but pay attention you don't copy the remarks**
1. The installation may stop occasionally and ask if you want to continue.  Just enter "y".
1. In step 6, before installing RVM, you will need to run an addional command before proceeding: `source /home/<yourname>/.rvm/scripts/rvm`  Then you can run the `rvm install 2.3.1` and `rvm use 2.3.1 --default` commands.
1. In step 8, you need to do `sudo apt install npm` before you can continue.
1. In step 10 you need to `sudo apt install git` before you can continue, and be sure to use your own email and name!
1. In step 11, don't forget to edit the $HOME/.gitconfig file.
1. Skip steps 12, 13 and 14.

### Install Atom
1. Run `sudo add-apt-repository ppa:webupd8team/atom`
2. Run `sudo apt update; sudo apt install atom`
3. Go take the dog for a walk.
4. When it's finished, find the Atom software in your system menu (it's in the development category) and make it a favorite and/or put a launcher on your desktop.
5. Open Atom.  Choose file->Open Folder.  Navigate to your home directory, select the 'code' directory.

## Welcome to your local IDE!
At this point, you're ready to go.  I'll follow up with another blog post with more tips and things you might need, but one is so good that I don't want to wait.  **It's live!: [Setting up a Local IDE in Windows Part 2](http://michaelries.info/2017/05/14/setting_up_a_local_ide_in_windows_part_2/)
1. Inside Atom, press ctrl-comma to open the settings page.
2. Choose install from the left, and type 'platformio-ide-terminal' in the search box.  Be sure the 'packages' button on the right is selected (instead of themes) and then press enter.
3. You should see the platformio-ide-terminal package at the top of the list.  Click install.
4. Press the ctrl-backtick button (backtick is the button below the Esc key) and you should see a terminal window appear.

By now, things should be looking pretty familiar!

I'd appreciate any feedback you have.  You can reach me on slack at @michaelries, email me at michael.ries@gmail.com, or you can submit an issue here: https://github.com/mikeries/mikeries.github.io/issues






