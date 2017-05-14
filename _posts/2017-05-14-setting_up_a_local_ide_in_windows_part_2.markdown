---
layout: post
title:  Setting up a local IDE in Windows Part 2
date:   2017-05-13 22:47:50 -0400
---

## Optimizing your experience

About 2 weeks ago I posted a walkthrough on how to set up your own [local IDE within Windows using VirtualBox and Xubuntu.](http://michaelries.info/2017/04/27/setting_up_a_local_ide_in_windows/)  In this follow-up post I'll show a few tweaks that you might use to improve your experience.

1. Initially, I had some trouble resizing windows -- the piece of the screen in which my cursor would transform into a resize icon was way too small.  It turns out that this is because the default window theme for Xubuntu has a very narrow border.  The solution to this problem is to pick a new window theme.
  * Whisker Menu (the little blue 'mouse' in the upper left) -> Settings -> Window Manager.  Then pick a new Style for your window.  I use Moheli.

2. The next big item for me was to enable dual monitor.  It turns out that VirtualBox allows you to have up to 8 monitors depending on what your video card supports.  To enable more than one monitor, make sure you're in the Windows (host) side and open up the VirtualBox Manager.  If your virtual machine is running, you will have to shut it down first.  This can be done through the Machine Menu or by right-clicking on the virtual machine and choosing Close.
  * Then select "Settings" and choose the Display group.  Here you'll probably want to increase the allocated memory (again depending on your hardware).  In my case, I upped it to 64MB out of 128MB.
  * Increase the 'monitor count' from one to however many you want to have.
  * You may be tempted to check either Enable 2D or Enable 3D acceleration on this page.  Feel free to play around with this, but I never got it working satisfactorily for me.  Whether or not this works will probably depend a great deal on your hardware and the drivers.
  * VirtualBox will give you a window corresponding to each virtual monitor.
  * To use the second (or third, or forth) monitors you need to configure them inside the VM. Whisker Menu -> Settings -> Display will let you set their resolutions, choose a primary display, and orient them.  I have to confess that this particular settings dialog seems pretty buggy.  I was never able to convince it that my second screen is actually on my left rather than might right.  But since a 'monitor' is really just a VirtualBox window, I can drag it wherever I please and it works fine.


3.  Shut down your VM and go back into the VirtualBox settings for your VM. There are a few other things to take a look at.
  * Under General -> Advanced you have some choices for sharing the clipboard and drag and drop between the VM and the host.  This allows you to copy and paste to and/or from the host machine to the virtual machine.  Very useful if you find yourself working between both OSs.  I find myself sticking to linux almost exclusively when I'm developing, but I have them both set to 'Bidirectional' anyway.  Who knows when I'm going to want to drag and drop that cute puppy picture from Windows into my Slack channel?
  * In the System group, you have a couple of tabs of interest.  First, in the Motherboard tab, you can adjust the amount of RAM that you initially allocated when you installed.  When I installed, I gave 4MB RAM to the VM out of 8MB physical RAM, and I haven't had any reason to change it.  In the Processor tab, you can enable your VM to use multiple processors.  When I installed originally, I left this at the default of 1 processor.  When I started having issues with Zoom, I bumped this up to 3 processors and haven't had any more problems.
  * The only other tab I've had reason to play with is the User Interface.  In this tab, you can customize the menus that appear inside the VirtualBox window while you're interacting with your VM.  In my case, I disabled them entirely as I don't use them and they get in the way.  That way I can go into fullscreen mode and completely forget that I'm actually running a windows application.  I just have to remember that Right-Ctrl-F is how to get back into "windowed mode" so that I can access my windows OS.


4. Install Cairo or some other dock.  Cairo provides me with a couple things I really like.  
  * First, it gives me a Mac-like animated dock where I can drag and drop shortcuts (launchers in linux-speak) to quickly access things I use often.  Mine has Chrome, Atom, Zoom, Gmail, VioletUML, Terminal, DB Browser for SQLite, Screenshot, and a few others.  It's configured to automatically hide, but appear when I move my mouse to the bottom of the screen. 
  * The second thing is that it provides me with multiple workspaces.  I can have one (dual monitor) workspace set up for plowing through lessons, and another workspace for slack, email, music, etc.  And I can switch between them with a single click or even by scroll wheel if I mouse over the icon in the menu bar.  If you've never used multiple workspaces, you should definitely try it out.  There are windows apps to do this too, if you're curious, but it seems more prevalent in the linux world.


5.  Other Ubuntu/Linux apps that I've found useful
  * [VioletUML](http://alexdp.free.fr/violetumleditor/page.php)  I know Avi loves gliffy, but it's way more than I need and it costs money after a couple weeks.  VioletUML isn't as polished, but if all you want to do is draw some simple UML diagrams to help you make it through those tough ActiveRecord Associations labs, this is really easy.  And it works in both Windows AND Linux.
  * [Synaptic Package Manager ](https://apps.ubuntu.com/cat/applications/precise/synaptic/)  The more I use linux the less I use a package manager, instead just using apt-get inside the console.  Or downloading a  deb package and just double-clicking on it.  But this one is nice if you're just looking to see what's out there.
  * [DB Browser for SQLite3](http://sqlitebrowser.org/) Lets you open up your SQLite3 data base and run queries on it so that you can actually see what's in there.
  * [Vokoscreen](https://github.com/vkohaupt/vokoscreen)  You'll need a way to record yourself while coding or walking through your portfolio projects.  Vokoscreen is a very simple and easy to use program that does exactly this.
  * [ Open Broadcaster Software](https://obsproject.com/)  I have to admit that I installed this as an alternative to vokoscreen, but I've never used it.  It's clearly way more polished and powerful, but the learning curve is too high.  I think it has some editing features that some of you might find useful, so I put it on this list.
  * [Git Kraken](https://www.gitkraken.com/)  I heard about this one on Slack today.  It's a GUI interface for git, and it's beautiful.  You probably won't need 10% of its features until well after graduation.  But I mention it because it might help you visualize your branches and commits, especially when pairing.  And it has an "Undo" button that should make you more comfortable exploring Git's capabilities for collaborations.


I'll be interested in hearing what useful tools and tweaks you discover!  DM me on slack at @michaelries.


