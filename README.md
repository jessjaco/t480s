# Things I want

- 3 finger swipe to go back in browser
- Permanent gamma of .85 or so in daylight mode
- Way to check displays without opening 'Displays' (needed when monitor unplugs)
- Way to just scale Reaper (like on the Mac)

# Things to check

- What's going on with python packages, a lotta this stuff installs them


# Things to do
- Setup the undervolting (see github.com/erpalma/throttled)

# Setup

## Display

### Fractional scaling

On gnome 3.32 fractional scaling settings under wayland looked fuzzy in some programs - notably chrome and firefox. Seems to be a common problem. Monitor handling seemed good though. Under X11, see https://www.linuxuprising.com/2019/04/how-to-enable-hidpi-fractional-scaling.html. Basically, just set `gsettings set org.gnome.mutter experimental-features "['x11-randr-fractional-scaling']"` and the menu options should appear in display. I set 175% and it looks pretty good. Monitor handling is not bad as well, but sometimes you need to re-check (not necessarily change) display settings when you plug/unplug.

Note that if you change from X to wayland on login you'll have to reset this stuff.

### Washed out colors

This seems to fix washed out colors on external monitor (haven't tested locally yet):

`xrandr --output HDMI-2 --set "Broadcast RGB" "Full"`

Added to ~/.xinitrc


## Crappy wifi

edit /etc/NetworkManager/conf.d/default-wifi-powersave-on.conf 
and change 

    wifi.powersave = 3

to

    wifi.powersave = 2


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

3. Setup JACK via qjackctl (or otherwise):

  1. Select firewire
  2. Check start on startup, save config to, enable sequencer support,
     enable D-Bus interface, enable Jack D-Bus, single instance
  3. They say 128 frames /period and 3 periods / buffer but I was getting
     skips. Currently at 512 & 3. This increase latency but works better (and
     skipping may be due to pulseaudio integration).


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

### System-monitor
Not gnome system monitor. Requires GNOME Shell integration extension for chrome (or ff, if you even use that). So install the extensions, then run
sudo apt-get install chrome-gnome-shell

Also need (jeez louise)
sudo apt-get install gir1.2-gtop-2.0 gir1.2-networkmanager-1.0  gir1.2-clutter-1.0


### s-tui
Command line stress tester. Doesn't appear to be able to stress rn though (maybe needs to be plugged in?)

sudo apt install s-tui

### throttled
sudo apt install git build-essential python3-dev libdbus-glib-1-dev libgirepository1.0-dev libcairo2-dev python3-venv python3-wheel
git clone https://github.com/erpalma/lenovo-throttling-fix.git
sudo ./lenovo-throttling-fix/install.sh

