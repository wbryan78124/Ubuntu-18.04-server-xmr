# ubuntu-18.04-server-xmr
ubuntu 18.04 server xmr mining guide
# this is a guide to setup ubuntu 18.04.01 server for mining xmr with castxmr, im new to this so please bear with me.
# i'll do my best to go step by step with things to copy and paste. there's soo many guides ive followed that didnt work i thought id help
# this setup is for AMD cpu (FX-chips in particular) and AMD GPU (powercolor red devil rx570)



parts needed are the usual for a pc (mother board, cpu, cooler, ETC... you get the idea) except a hdmi dummy plug ($5 ebay).
alright here we go (hopefully your newly built pc/server boots, and can see usb ports) these instructions might be out of order during the install but its all here and easy to read through

Step 1. download ubuntu server with traditional installer (so you can setup WIFI easier). i go directly to ubuntu:                      > https://www.ubuntu.com/download/server/thank-you?version=18.04.1.0&architecture=amd64

Step 2. flash a freshly formatted usb drive of 8gb or larger (FAT32 or NTFS file systems) with your new .iso For windows etcher works well:             > https://www.balena.io/etcher/ 

if on ubuntu use Startup disk creator. on MAC i have no idea i dont have one lol

Step 3. boot to bios on your newly built machine (theres soo many options on which button to hit to get to bios i cant list them but usually its F1 or Delete key, now hunt through your menu looking for secure boot. once you find it you might have to google how to turn it off and how to turn on CSM compatibility (AMD cards with modded bios dont play nice with secure boot on and CSM off). id leave the other options at default unless it wont boot.

Step 4. plug your usb drive into your new maching and boot to the boot device menu, just like getting to bios theres many different keys for the different motherboard makers (F12, F11, F8 are common ones), pick your usb drive from the list, you might see it listed twice. if listed twice go with the uefi one. within a short amount of time it should have a screen that at the top says install ubuntu, hit enter to begin the install.

Step 5. installing the OS,  its a fairly straight foward install, it may stop and ask you a couple things along the way. Most of it should be obvious what it wants to know. setting up the HDD or SSD it will ask you if you want it to unmount the drive select yes then hit enter, after a shourt amount of time a menu will pop up with a list of ways to setup up the HDD/SSD i pick the top one say's guided use entire disk thats the one im using

Step 6. internet, well 2 choices ethernet or wifi (dont let anyone tell you wifi on linux server doesn't work it does and quite well if you have a Atheros A9280) i prefer the wifi route since i tend to move my pc around a bunch.

Step 7. security updates, it will give you 3 choices here no automatic updates (you update it yourself occasionally), automatic updates(does it for you), or managed updates with an outside program. i go no automatic updates (let's face it windows killed the whole idea of auto updates and thats why we're going with linux lol)

Step 8. Install programs (yes yes i know their called packeges but im just a regular guy doing this not a programmer) there will be a list of packages it asks if you want installed (LAMP, Print Server, DNS server, ETC....) for this we only need the Openssh one. space bar selectes/unselects it then tab button takes you to the bottom where you can hit enter to install. from here it with go through and install stuff. its handy to be near by incase it wants more info from you. when its done it will tell you to remove the install usb drive and reboot, so do what it says and hit enter

Step 9. The reboot, so now your probably watching a bunch of big giberish moving fast then a flash of the screen then small giberish followed by stuff you can read that with any luck has a green OK next to it on the left, if so..... thats a good sign. once its doon booting you will be left with an empty black screen and it wants your login name (the name you gave it after it asked for your full name during the install) and password you gave it. the password field DOES NOT SHOW ANYTHING YOU TYPE that is normal. 

Step 10. Login once you get logged in you wont see much other than some basic info but you will have a command prompt. so first thing we want to do is give it a password for ROOT so type:
```
sudo passwd
```
then hit enter and give it the password you logged in with, next it says new unix password (give it a different password for security reasons) then enter it again to comfirm

Step 11. Upgrading so now its time to get the whole system upto date type:
```
sudo apt update
```
(it will ask for login password again)(if it says failed to fetch then most likely it has no internet and time to google, internet will be extremely important)
once its updated all its info its time to upgrade those systems, type:
```
sudo apt upgrade
```
once its finished and back to the prompt type:
```
sudo reboot
```
(reboot so that it can start using all the new packages)

Step 12. SSH. so if it rebooted properly and its back at the login then login, were getting to the part where we'll be able to copy and paste once we're accessing it remotely but first we need it's ip address. i have an app for my router so i can see it's ip there but you can also get it from the info when you logged in, also if you type:
```
ifconfig
```
record that ip since you'll need it later
theres many ways to remotely connect, the most common it called putty:
> https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

you can also add the exstension to google chrome just google "google chrome secure shell extension"
from a pc on your local network you can input that ip address into putty and make sure it says port 22, then hit open. from outside your network like from your phone you will need to do more work opening a port on your router which i wont cover in this but google it.
if all goes good and putty connects you will see the same login as you did on the monitor hooked to your machine, go ahead login. do the update and upgrade again for good measure lol. then copy and paste (paste is right click in the putty window, its actually copy too so takes a bit to get the hang of it)
OPTIONAL, download winsp(great gui tool for copying files, creating directories, and modifying files):
> https://winscp.net/eng/download.php
```
sudo shutdown
```
it will tell you it scheduled a shutdown, so wait a minute and see if it does. if it does GREAT!!!!! now you can unplug the monitor install dummy plug and move it to where ever you want (hence why i prefer WIFI in mine)
Once you power it back up you need to SSH in and login, its time to change some important settings, install drivers, and miner (Important as in you get it wrong and system wont boot and you'll have to reinstall ubuntu)

Step 13. GRUB. grub is the bootloader and we need to add some things to make the gpu's, drivers, opencl software, and overclocking work.
we're going to use the built in editor NANO to do this(you can use any that you prefer, im just used to NANO).
```
sudo nano /etc/default/grub
```
the screen will change and you'll be looking at the grub config file. your going to replace and entire line GRUB_CMDLINE_LINUX_DEFAULT="" with this modified one by moving the cursor to the end of it on the right and hit backspace till the line is empty then copy & paste this line
```
GRUB_CMDLINE_LINUX_DEFAULT="iommu=soft amdgpu.ppfeaturemask=0xffffffff"
```
once thats done you hit control and O(the letter not the number) then hit enter (that tell's nano you want to write the new info to the file) then control and X to exit nano. The iommu is an AMD thing and built into the motherboard bios, it maps the hardware in your machine but it's problematic so =soft tells grub and linux to map this with software. THIS PART IS VITAL!!!! you now have to update the grub with the new info to do so type:
```
sudo update-grub
```
hit enter and let it do its thing

Step 14. Drivers, this is where i hit the most problems. different versions of the drivers have different quirks so we'll just use the most recent. you can download to your pc and use winscp to transfer to your server (very easy so shouldnt have to explain) or you can go the difficult route(i'll show this option). As of writing this amdgpu-pro-18.50-708488-ubuntu-18.04 is the current one and i can confirm it works. first to make things easier lets create to new directories to sort things out, "gpu" for drivers, and "miner" for mining software.
```
mkdir /gpu
mkdir /mining
```
if you type dir at the prompt you should now see your 2 new ones. So lets hop into "gpu" and download a driver
```
cd gpu
```
next we will download directly from AMD
```
wget --referer=http://support.amd.com  https://drivers.amd.com/drivers/linux/amdgpu-pro-18.50-708488-ubuntu-18.04.tar.xz
```
if it says wget is an unknown command you will install it by typing:
```
sudo apt install wget
```
and ther try download again, once it downloads we need to decompress it by using:
```
tar -Jxvf amdgpu-pro-18.50-708488-ubuntu-18.04.tar.xz
```
this is where winscp can make things easier by allowing easy renaming of this file, but we're doing it that hard way lol
so we need to jump into our newly decompressed folder
```
cd amdgpu-pro-18.50-708488-ubuntu-18.04
```
Now to run some install's. this will install the needed parts to allow opencl to run on your system for the miner, there's 2 versions it can use for opencl PAL and LEGACY best i can tell legacy is for everything but vega cards, i havent had any luck running vega and rx cards at the same time reliably. we will be setting up for RX cards. Do one line at a time and let it finish before doning the next line.
```
./amdgpu-install -y

./amdgpu-pro-install -y --opencl=legacy

sudo usermod -a -G video $LOGNAME
```
now time to reboot again. if it boots and you can SSH back in AWESOME!!!! if not welcome to linux and AMD drivers and time to try a different guide.

Step 15. Opencl
now that we have drivers we need to test them to know if we even have a chance of the rest working. To do that we need a couple more packages
```
sudo apt install opencl-headers

sudo apt install clinfo
```
once each is done time to reboot again and SSH again (all the reboots aren't totally required but we want to give linux every chance to make this work) type clinfo and hit enter after a short wait you should have a screen the has a bunch of info about your gpu's. If the system hangs, or your get "segment fault","illegal instruction", there's a problem and something isnt compatible so the rest wont work and try a different guide (trouble shooting linux is complicated for average user). if no fault's and its back to command prompt time for next step
  


