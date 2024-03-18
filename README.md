# This Is a Fork

> This fork of Scawp's still functional auto(un)mounting script makes the following changes:

- Disables handling NVMe drives
- Actually unmounts devices when they are disconnected to solve devices disconnected without unmounting first not working until a reboot
  - This is done because udisks doesn't unmount suddenly disconnected devices, but also won't try to reconnect them once plugged in, _or_ let us remount them since the stale deteministically generated mountpoint would still exist...
  - The only solution to this issue is to then reboot, letting the OS discard the stale mount
  - Or just manually run `sudo umount /run/media/deck/<LABEL>` before plugging the device back in
- And of course, repoint the install script to this fork instead of the original
  - Does not rename `scawp` from the install paths though

---

# Steam-Deck.Mount-External-Drive 3.5
Script to Auto-Mount NTFS, BTRFS & exFat SDCards, External USB Drives (or SSD Docks) & Internal Partitions (If you Dual-Boot) on the Steam Deck

NTFS & BTRFS Partitions containing a SteamLibrary at root level or in a folder named `SteamLibrary` will automatically be added to Steam, exFAT isn't supported as a SteamLibrary but will be Mounted for use with other Launchers or for Media/ROMs etc.

# "This is cool! How can I thank you?"
### Why not drop me a sub over on my youtube channel ;) [Chinballs Gaming](https://www.youtube.com/chinballsTV?sub_confirmation=1)

### Also [Check out all these other things I'm making](https://github.com/scawp/Steam-Deck.Tools-List)


# Steam OS 3.5 Now supports Ext4 external Drives out the box so see **Uninstall** if thats all you need!

# How does this work?

This script is basically a mirror of Valves own Auto-Mount script (which lives on SteamOS at `/usr/lib/hwsupport/steamos-automount.sh` ) adding in support for `ntfs`, `btrfs` & `exFAT` and adding rules for Internal Partitions.

Additional RegEx has been added to the rules to allow he mounting of "Full Disk" Formatted drives (eg ones that don't have a partitions table) so even drives that are eg `sda` or `mmcblk0` as well as `sda1` or `mmcblk0p1` can be mounted.

SteamOS's rule for this lives at `/usr/lib/udev/rules.d/99-steamos-automount.rules` and because SteamOS has a Read-Only File System, files in `/usr/` cannot be changed without removing the Read-Onlyness, however systemd rules can be overwritten due to how systemd prioritieses directories, so by adding a rule with the same name in `/etc/udev/rules.d/` we can override the rule without making changes to SteamOS.

Looking for the old code? see https://github.com/scawp/Steam-Deck.Mount-External-Drive/tree/pre-3.5

a `udev` rule is added to `/etc/udev/rules.d/99-steamos-automount.rules` which takes priority over `/usr/lib/udev/rules.d/99-steamos-automount.rules` 
this then calls systemd `/etc/systemd/system/external-drive-mount@[sda|sda1|sda2|sdd1|etc].service`
that then runs `/home/deck/.local/share/scawp/SDMED/automount.sh` to Auto Mount any supported SD/External USB/Internal Partitions.

`/etc/fstab` is not required for mounting in this way, (however if a Device has an `fstab` entry these scripts will still work)

# Video Guide

https://www.youtube.com/watch?v=Yglf1EKBv2A

# Operation

The Drive(s) will be Auto-Mounted to `/run/media/deck/[LABEL]` eg `/run/media/deck/External-ssd/` if the Device has no `label` then the Devices `UUID` will be used eg `/run/media/deck/a12332-12bf-a33ab-eef/`

# Installation

## Via Curl (One Line Install)

In Konsole type `curl -sSL https://raw.githubusercontent.com/5310/Steam-Deck.Mount-External-Drive/main/curl_install.sh | bash`

a `sudo` password is required (run `passwd` if required first)

# Uninstall

`sudo rm /etc/udev/rules.d/99-steamos-automount.rules`

`sudo rm /etc/systemd/system/external-drive-mount@.service`

`sudo rm -r /home/deck/.local/share/scawp/SDMED`

`sudo udevadm control --reload`

`sudo systemctl daemon-reload`

# WORK IN PROGRESS!

This will probably have bugs, so beware! log bugs under [issues](https://github.com/scawp/Steam-Deck.Mount-External-Drive/issues)! 

> Unless it has anything to do with the changes I (@5310) made tothe scripts. In which case add the issues right [here](https://github.com/5310/Steam-Deck.Mount-External-Drive/issues)!
