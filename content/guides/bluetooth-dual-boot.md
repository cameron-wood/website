+++
title = 'Bluetooth pairing on dual boot Windows & Linux'
date = 2024-03-03T14:45:09Z
draft = false
+++

I run both Windows and Linux on my desktop and sharing Bluetooth devices between them is _really_ annoying. Every time I switch operating system I have to re-pair devices I want to use.

{{<details "Why does this happen?">}}
When pairing a Bluetooth device, a unique set of pairing keys are generated and exchanged between the device and computer. Since dual-boot setups use the same hardware, each operating system generates unique keys for the same MAC address and thus overwrites the previously stored key on the Bluetooth device.
{{</details>}}

This guide documents how to copy the pairing keys from Windows and use them in Linux. For me that's [Pop!_OS](https://pop.system76.com/), but this should work on any Debian-based OS. 

## Pairing Devices

First, pair your devices with Linux. This will generate the necessary records of the devices which we will update with the Windows keys.

Then switch to Windows and re-pair your devices. Now you're ready to copy the pairing keys from Windows to Linux.

## Extracing the keys from Windows

1. Download and extract Microsoft's [PsTools](https://learn.microsoft.com/en-us/sysinternals/downloads/pstools)
1. Open a Command Prompt as Administrator
1. Launch the Registry Editor with `psexec64 -is regedit` 
1. Navigate to `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\BTHPORT\Parameters\Keys`
1. Each Bluetooth controller will be listed, containing entries for each pair bluetooth device listed by MAC address, with the pairing key stored as the 'data'
1. Export this key or copy down the value - this is the value we need in Linux

## Adding the keys to Linux

1. Start a root shell with `sudo -s`
1. Navigate to your Bluetooth controller directory: `cd /var/lib/bluetooth/00:00:00:00:00:00/`
1. List devices with `ls`
1. Edit the file corresponding to your device, e.g. `vim AA:AA:AA:AA:AA:AA/info`, and update the pairing key with the value exported from Windows
1. Restart the Bluetooth service with `systemctl restart bluetooth`

Now you can seamlessly share Bluetooth devices between Windows and Linux in your dual-boot setup.
