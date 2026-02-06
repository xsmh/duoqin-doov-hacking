# Duoqin / Doov Hacking
This is a documentation for hacking Duoqin / Doov brand phones.

# Overview
This documentation has been written to walk the owners of Duoqin / Doov devices through flashing Dumbdroid or any other compatible ROM of their choice.

The guide assumes that you are using Windows 10/11. If you are using Linux, I trust your ability to figure out the OS specific parts on your own. If you are using Apple, good luck.

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
2. A computer with at least 8GB of RAM for running the flashing tools.[^Apple] Preferably having at least three USB-A ports.
3. Two USB flash drives.[^Drive] Each having a capacity of 12GB or more.[^Capacity]
4. A data transfer capable USB A-C cable. The one included in the box should normally work fine. [^Cable]


# Install the flashing tools 
This has been by far the most difficult part of the process for most users.
To simplify the process I have created a customized Linux ISO that comes with the tools you need pre-installed. This does not include SN Writer, which you will only need if you are flashing the American bands. You will have to use Windows for it if you need it.

## Create bootale USB stick
1. Download the [Linux ISO](https://mega.nz/file/S5gSVY5Y#WPGum-d7_GwofKBpXnCi_4aPqy9bXSXdNF1fqQy02O0) that comes pre-installed with the tools.
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





[^F22]: Dumbdroid does not work with the F22 non-pro, it uses a 32-bit system and you will have to find a compatible ROM on your own.
[^Apple]: No Apple junk. Unless it has an Intel CPU, then it is somewhat workable.
[^Drive]: Any other type of external storage device works.
[^Capacity]: 8 + 12 GB is also fine.
[^Cable]: Make sure the cable you use is capable of transferring data, not just power. Strictly A-C, **not** C-C.

# 
