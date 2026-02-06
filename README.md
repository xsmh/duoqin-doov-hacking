# Duoqin / Doov Hacking
This is a documentation for hacking Duoqin / Doov brand phones.

# Overview
This documentation has been written to walk the owners of Duoqin / Doov devices through flashing Dumbdroid or any other compatible ROM of their choice.

The guide assumes that you are using Windows 10/11. If you are using Linux, I trust your ability to figure out the OS specific parts on your own. If you are using macOS, good luck.

## Tested devices 
The guide has been tested on the following devices:

**Duoqin:**
- F21 Pro
- F22 Pro
- F22[^F22]

**Doov:**
- R77 Pro (R77c)
- R77
- R17 (Z17) Pro (3.5 inch screen)

# Device button combinations 
| Model | BROM (bootrom) | Recovery |
| :---  | :--- | :--- |
| **F21 Pro** | `menu` + `back` (top two buttons) | `menu` + `power` + `*`, wait until android logo appears and then hold `power` + `up` |
| **F22 Pro** | `menu` + `back` (top two buttons) | - |
| **F22** | `menu` + `back` (top two buttons) | `power` + `up`|
| **R77 Pro** | `call` + `power`| `#` + `power` for 10 seconds (disabled on some units)|
| **R77** | `call` + `power`| - |
| **R17 Pro** | `call` + `power`| - |

# Warning  ⚠️ ⚠️ ⚠️
- Do **NOT** use AI chatbots for this unless you want a bricked device.
- Do **NOT** skip making a backup. I cannot help you if you brick your device and do not have a backup.

Flashing your device can **brick your phone** if done incorrectly.  
By following this guide, you **agree to proceed at your own risk**. I'm **not responsible** for any damage, data loss, or other issues that may occur.  

# Prerequisites
1. A Duoqin or Doov brand phone.
2. A computer with at least 8GB of RAM and three USB-A ports for running the flashing tools.[^Apple]
3. Two USB flash drives.[^Drive] Each having a capacity of 12GB or more.[^Capacity]
4. A data transfer USB A-C cable. Strictly A-C, **not** C-C. Make sure the cable you use is capable of transferring data, not just power. [^Cable]


# Install the flashing tools 
This has been by far the most difficult part of the process for most users.
To simplify the process I have created a customized Linux ISO that comes with the tools you need pre-installed. This does not include SN Writer, which you will only need if you are flashing the American bands. You will have to use Windows for it if you need it.

## Create bootable USB stick
1. Download the [Linux ISO](https://drive.google.com/file/d/1CvvSBCKkm-XuoGigf3GqxbLbz0_Ti-_j) that comes pre-installed with the tools.
2. Downloads and install [Rufus](https://rufus.ie/en/#download).
3. Launch Rufus and insert a USB stick. Your USB drive should show up in the `Device` field.
4. Click `SELECT`. Choose the Linux ISO and click `Open` to confirm.
5. If you’ve already tried Rufus and the USB stick failed to boot on your system, change `Partitioning scheme` to `GPT` and check that `Target system` is set to `UEFI (non CSM)`. This works on modern systems that disable legacy compatibility.
6. Click `START`. Click `OK` or `Yes` on any prompts and popups.
7. The image will now be written to the drive. Once it is done, the status bar will say `READY`. You can then click `CLOSE` to finish the process.

## Boot from USB stick 
There are two ways you could go about this.

**Option 1:** 
Hold the `Shift` key while pressing the `Restart` button and keep holding the key until Windows prompts you to choose an option. Click `Use a device` and then click `Removable Device`. Windows should now reboot into the USB drive.

**Option 2:** Reboot your computer and go into the BIOS. Disable `Secure Boot` and change the boot order to make the USB drive the first option. Save and reboot. These are some general instructions. This method will depend on your computer model, so you will have to look it up if you don't know how to do it. 

**Finally:** When the computer reboots, you will be greeted with a few options. Pick the one that says "Start Linux Mint". This is normally the first option. Once you have booted into Linux, you will be shown a login screen. Insert the password `user` and hit enter.

## General info about the Linux ISO
- There is no persistence. Meaning that any data you store on the Linux ISO itself will be lost after a reboot.
- Wi-Fi may not work on some computer models due to unavaialble proprietary drivers. In which case you will have either use an ethernet cable or transfer data via an external drive.
- There are 4 pre-installed programs that you can run with the following commands: 
 1. `adb`
 2. `fastboot`
 3. `ghex`
 4. `mtk` for CLI mode of mtkclient & `mtk_gui` for the graphical interface

 Going forward, whenever I mention **Run**, it means type the command that follows in the terminal and press enter. 

# Make a backup

This is the most important step in the guide. It is cruical that you do not skip it.  
Do note that this will only backup the firmware, it will not backup personal user data if you have any stored on your device.

1. While booted into the live Linux image, connect your 2nd USB stick. We will use this one for storing the backup. Do **not** unplug the 1st USB stick that has the Linux image on it.
2. Open the terminal in the Linux ISO.
3. Type `lsblk` and hit enter. Under `MOUNTPOINTS` you will see an entry similar to  
`/media/user/exampleName`. In your case `exampleName` will be whatever your USB drive name is. Take note of this path as we will use it in the next step.
4. Run `mkdir "/media/user/exampleName/stock_rom"` but replace `exampleName` in the path with whatever your drive name was from the previous step.
5. Run `mtk rl --skip userdata "/media/user/exampleName/stock_rom"` but don't forget to replace `exampleName` and wait until it has finished making the backup.
6. Run `mtk r preloader "/media/user/exampleName/stock_rom/preloader.bin" --parttype=boot1`. Don't forget to replace `exampleName` here too. After this has finished, you should now be able to see a bunch of files with .bin extension inside the stock_rom folder of your USB drive.


**Alternative Method:**
If your computer has +16GB of RAM, you could skip using the 2nd drive and store the backup directly on the Linux image and upload it to a cloud storage service (like Google Drive) once it's done. You would then skip step 3 and remove the `/media/user/exampleName/` part from the commands and follow the rest as is. I do not recommend this method as it uses RAM as storage and the live image can crash if you run out of it. But it should be safe if you have +32GB RAM.

# Unlock the bootloader
This will factory reset your phone and you will lose your data!

### For most models:
1. [Enter fastboot](#enter-fastboot).
2. Run `fastboot flashing unlock`.
3. Run `fastboot --disable-verity --disable-verification flash vbmeta vbmeta_a.bin`.  

### For the F21 Pro and other models that ask you to press volume-up but no button works:
1. Turn the phone off.
2. Run `mtk da seccfg unlock`.
3. [Enter fastboot](#enter-fastboot).
4. Run `fastboot --disable-verity --disable-verification flash vbmeta vbmeta_a.bin`.


## dm-verity corruption
A common issue that many run into is the following message appearing on boot and not letting them go past the bootloader after unlocking the device.
The fix is following step 4-6 from previous section. If that doesn't work, you can try this:

1. Turn the phone off.
2. Run `mtk w vbmeta vbmeta_a.bin`.
3. Connect the cable and wait for the command to finish. Then unplug and reboot the phone to see if the message is gone.

```
dm-verity corruption
Your device is corrupt.
It can't be trusted and may not work properly
Press power button to continue.
Or, device will power off in 5s
```
## Orange state warning 
Your device may show this message on boot. This is normal as long as your device boots after you press the power button and wait 5 seconds.
You don't need to remove it but you can if you wish to, although it may require some effort. Follow [this guide](https://github.com/AlikornSause/Notes-on-QIN-F21-PRO?tab=readme-ov-file#changing-the-orange-state-warning-text) if you are interested.

```
Orange State
Your device has been unlocked and can't be trusted
Your device will boot in 5 seconds
```

# Flash the new ROM

There are a few LineageOS ROMs available that you can try. I'm going to flash Dumbdroid as it's currently the best option for these keypad phones.

1. Erase user data if you are upgrading from the stock ROM. Updating Dumbdroid doesn’t require this step. Run the following commands.  
`fastboot erase userdata`  
`fastboot erase metadata`
2. Download the appropriate *.img.gz from the [latest build](https://github.com/miki151/dumbdroid_build/releases/latest) of Dumbdroid onto the Linux ISO or the 2nd USB drive. Choose between G-apps and Vanilla (Micro-g). For the F21 pro, use the "30" version, for all other phones, use "31". 
3. Uncompress (unzip) the file after the download has finished. Do **NOT** simply rename it to .img from .img.gz.
4. [Enter fastboot](#enter-fastboot).
5. Run `fastboot reboot fastboot` to enter fastboot**D**.
6. Once the phone has rebooted into fastboot**D** (colored text on black background) run this command.
`fastboot flash system ???.img` but replace `???` with the actual filename.


# Enter fastboot
1. Turn the phone off if it is not already.
2. Run `python3 mtkfastboot.py`.
3. Conncect the cable and wait until the command forces the device to reboot into fastboot. You should see a text that says "fastboot" at the bottom left of the screen.

# Common errors

### FAILED (remote: 'Not enough space to resize partition')

[^F22]: Dumbdroid does not work with the F22 non-pro, it uses a 32-bit system and you will have to find a compatible ROM on your own.
[^Apple]: No Apple junk. Unless it has an Intel CPU, then it is somewhat workable.
[^Drive]: Any other type of external storage device works.
[^Capacity]: 8 + 12 GB is also fine.
[^Cable]: The one included in the box should normally work fine.
