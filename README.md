# Things I want

- 3 finger swipe to go back in browser
- Way to just scale Reaper (like on the Mac)

# Things to check

- What's going on with python packages, a lotta this stuff installs them


# Setup

## Low latency kernel (for realtime audio help)

`sudo apt install linux-lowlatency`

## Display

### Fractional scaling

On gnome 3.32 fractional scaling settings under wayland looked fuzzy in some programs - notably chrome and firefox. Seems to be a common problem. Monitor handling seemed good though. Under X11, see https://www.linuxuprising.com/2019/04/how-to-enable-hidpi-fractional-scaling.html. Basically, just set `gsettings set org.gnome.mutter experimental-features "['x11-randr-fractional-scaling']"` and the menu options should appear in display. I set 175% and it looks pretty good. Monitor handling is not bad as well, but sometimes you need to re-check (not necessarily change) display settings when you plug/unplug.

Note that if you change from X to wayland on login you'll have to reset this stuff.

### Washed out colors

This seems to fix washed out colors on external monitor (haven't tested locally yet):

`xrandr --output HDMI2 --set "Broadcast RGB" "Full"`

Added to ~/.xinitrc, but that didn't seem to work. So I tried `/etc/X11/Xsession.d/` and added a file (originally 99... but
that didn't work so I changed it to 45... and that worked). Note that updates changed the output name (from HDMI-2 to HDMI2), will need to keep an eye on that.

## Crappy wifi

edit /etc/NetworkManager/conf.d/default-wifi-powersave-on.conf 
and change 

    wifi.powersave = 3

to

    wifi.powersave = 2


## Midi

If using qjackctl, install a2jmidid and run `adjmidid -e`.  

## Audio

A lot of this is repeated under the Alesis stuff, but basically, to get high
quality audio transport:
1. Install low latency kernel (see above).
2. When installing jackd, select realtime audio, make audio group, and set
   appropriate stuff in the conf file.

BUT this does not (in disco dingo) set the correct memory locking capabilities
(or ??priority?? need to check). This appears to be a bug. See

https://bbs.archlinux.org/viewtopic.php?id=243648
https://unix.stackexchange.com/questions/366352/etc-security-limits-conf-not-applied?newreg=1e0c8f6c19284546b3ca201c03032628
https://superuser.com/questions/1200539/cannot-increase-open-file-limit-past-4096-ubuntu/1200818#_=_

There's a long discussion here too: https://bugzilla.redhat.com/show_bug.cgi?id=1364332

I tried the .config stuff and that seemed to work for qjackctl, but not in
gnome-terminal. Haven't checked reaper yet (cause how do I start reaper from
the GUI and monitor the output?)

And if you fix it, answer here:
https://askubuntu.com/questions/1142943/ulimit-unlimited-cannot-set

For now it seems if you sudo yourself it's actually fixed. If you want to know
if it's actually working, start reaper in the terminal (you'll have to do this
if you sudo yourself) and watch for memlock errors, or just open the performance monitor in reaper and watch the memory.


3. Errors about inability to write to /dev/cpu_dma_latency

This appears to have something to do with the cpu sleeping or something like that.
When monitoring reaper output from the command line it looks like this may be 
responsible for some nasty xruns (which result in serious lost frames when recording).
One way to evaluate this may be to see if these errors occur at set intervals (like
approx. 2:20 after starting to record). For now, I'm trying to follow [these
rules](https://unix.stackexchange.com/questions/111593/allow-non-root-user-to-read-write-dev-files) to see if it fixes this. Oh yeah, the monitoring is in reaper CL messages.
	 
## Bluetooth

Seems to stop computer from sleeping, or takes enough power when asleep that it
burns down the battery. To keep it off across reboots, add

`DEVICES_TO_DISABLE_ON_STARTUP="bluetooth"`

to `/etc/default/tlp`

## Alesis Multimix Firewire 8

from https://getsatisfaction.com/alesis/topics/multimix-16-firewire-and-linux:

1. Install jack, etc.

`sudo apt install jackd qjackctl pulseaudio-module-jack`

2. Edit /etc/modprobe.d/alsa-no-jack.conf.

Add: 
```
blacklist snd-fireworks
blacklist snd-bebob
blacklist snd-oxfw
blacklist snd-dice
blacklist snd-firewire-digi00x
blacklist snd-firewire-tascam
blacklist snd-firewire-lib
blacklist snd-firewire-transceiver
blacklist snd-fireface
blacklist snd-firewire-motu
```

3. Set irq priority. 

Find irq using e.q. `cat /proc/interrupts | grep firewire`. Add it to
`RTIRQ_NAME_LIST` var in `/etc/default/rtirq`.


4. Setup JACK via qjackctl (or otherwise):

  1. Select firewire
  2. Check start on startup, save config to, enable sequencer support,
     enable D-Bus interface, enable Jack D-Bus, single instance
  3. They say 128 frames /period and 3 periods / buffer but I was getting
     skips. Currently at 512 & 3. This increase latency but works better (and
     skipping may be due to pulseaudio integration).


#### Windows 10

If not using firmware 2.0 (no way to tell): 

1. Install XP SP3 on some laptop lying around with IEEE1394 port.
2. Find firmware update (to 2.0) on some forum somewhere.
3. Install firmware update.

Install latest driver on windows. Should work, but can't fall asleep. Working
on that.





# Software

## neovim
sudo apt-get install neovim

## chrome
google.com/chrome

## git
sudo apt install git

## libinput-gestures

see https://github.com/bulletmark/libinput-gestures
Note that xdotool based gestures do not work on wayland (see /etc/libinput.conf)

### R
sudo apt install r-base-core

### pip
sudo apt install python-pip


## Reaper
Download from reaper.fm. Need to remember how many ins / outs the Alesis mixer has. Icons are too small on scaled screen but look fine on big monitor. For now, assuming we'll usually use big monitor when recording (since the mixer is right by it), but will look for better alternatives. One option is to find a doubled icon set. I will also note several freezes to the UI itself, will have to see if it persists...

Note on wayland that it scales fuzzily a'la the mac (but more fuzzy b/c wayland) 

## Kontakt

- Install winehq-staging (or at least winehq v4.10 or so.
- Follow instructions somewhere to install native access
- Try to install Kontakt Factory Selection, then see these directions:

(https://www.linuxmusicians.com/viewtopic.php?t=16620&start=15)

"Native Access crashes or stops because it tries to start a Wine incompatible iso mounter (after downloading the iso file) for the downloaded iso and maybe also tries to start something for zip files.

Anyway, Wine can't handle it but the iso or zip file actually downloads and then needs to be installed manually.

Native Access downloads the file ok in ~/.wine/drive_c/users/user/Downloads (user is your username)

The file can be a iso or zip file.

If the downloaded iso file is manually mounted in Linux and installed then when Native Access is run again it will say that it's installed, same goes for a downloaded zip file.

The iso file needs to be mounted as a udf with a unhide option because the iso is Windows/Mac and the Windows part needs the unhide option.

sudo mount -t udf ~/.wine/drive_c/users/user/Downloads/isoname.iso -o unhide /mnt

cd /mnt and then install.

To unmount the iso change to a drirectory away from /mnt and then sudo umount
/mnt"


To get plugins working in reaper, do linvst and also set the vst direction to
include the Kontakt installation path in `~/.wine/c_drive/Program Files/Native
Instruments/`


## GDAL etc
sudo add-apt-repository ppa:ubuntugis/ppa
sudo apt-get update
sudo apt-get install gdal-bin qgis

## Power Management

### Powerstat
Monitor/poll power usage
`sudo apt-get install powerstat`
`sudo powerstat`


### TLP power management
Supposed to be plug and play
sudo apt install tlp
and maybe also
sudo apt isntall acpi-call-dkms

Settings are in /etc/default/tlp. One thing I've changed to help with audio
performance is uncommenting the `CPU_SCALING_GOVERNOR...` options and setting
powersave for battery and performance for AC. Note that I do not know at this
time if other programs will interfere with this setting.

Note that there's also some options for turbo boost. I left them alone but
ubuntu studio GUI notes that turbo boost should be __off__ for audio work. Not
sure why, haven't read it elsewhere, will have to read up. But even so, we
might want to just change it manually if it becomes a problem, since I could
see benefitting from this feature for modeling and such.


### System-monitor
Not gnome system monitor. Requires GNOME Shell integration extension for chrome (or ff, if you even use that). So install the extensions, then run
sudo apt-get install chrome-gnome-shell

Also need (jeez louise)
sudo apt-get install gir1.2-gtop-2.0 gir1.2-networkmanager-1.0  gir1.2-clutter-1.0


### s-tui
Command line stress tester. Needs `stress` program to test. Was getting some errors from apt install, so I recommend direct 
clone of repo and manual install. To witness faulty throttling, simply stress test and note how core frequencies drop 
as soon as the core temps reach 80c. 

### throttled
sudo apt install git build-essential python3-dev libdbus-glib-1-dev libgirepository1.0-dev libcairo2-dev python3-venv python3-wheel


git clone https://github.com/erpalma/lenovo-throttling-fix.git
`sudo ./lenovo-throttling-fix/install.sh`

Configuration is in /etc/lenovo_fix.conf. Status can be checked with 
`systemctl status lenovo_fix`
and more directly with
`./lenovo_fix.py --monitor`

Note that even with the undervolting values set at zero trip temp for throttling is raised to 95c and the allowed wattage is
raised. Undervolting values can be set in the conf file (followed by a corresponding `systemctl restart...`) and effects 
can be monitored using s-tui and lenovo_fix.

Could undervolt to -110 on AC but nothing above -90 on battery seems to be stable. So rn I've got the core settings at -95 
for AC and -90 for battery. Will monitor and perhaps reduce the AC but I'm ?not sure how quick the new settings take 
effect?. In lenovo_fix.py it notes switch to battery right away though.

Note that thermald can also be disabled (doesn't look like it is needed since throttling is controlled at the bios level).

Also see:
https://forums.lenovo.com/t5/Other-Linux-Discussions/X1C6-T480s-low-cTDP-and-trip-temperature-in-Linux/td-p/4028489/page/28
