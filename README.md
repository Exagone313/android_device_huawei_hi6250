# LineageOS 14.1 for the Huawei P9 Lite (hi6250)

### Kernel
This device tree is intended to be used with this kernel repo:

See: https://github.com/Exagone313/android_kernel_huawei_hi6250

branch: master
### Vendor
This device tree is intented to be used with this vendor repo

https://github.com/Exagone313/android_vendor_huawei_hi6250

branch: master
# How To Build

### Step 1: Setting up the Build Environment.

This "How to" is scoped for new builders with moderate PC skills.

You'll need Linux to be able to build LineageOS. You have three choices here; you can:

1. Install Linux natively on your computer.
2. Dual boot Linux and Windows.
3. Use virtual machine software ( virtual box, vmware ) to run linux.

NOTE: I recommend you use Ubuntu 14.04 LTS to build. That's what I use.

Now read this: http://source.android.com/source/initializing.html

Update OpenJava for Ubuntu 14.04: The OpenJava files listed on the above page are out-dated. Refer to this post on XDA: 
https://forum.xda-developers.com/oneplus-3/how-to/guide-complete-guide-building-t3477198/post70646310#post70646310

```bash
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt-get install openjdk-8-jdk
```
LineageOS 14.1 uses ImageMagick in building the bootanimation.zip
```bash
sudo apt-get install ImageMagick
```

### Step 2: Downloading the Source.

NOTE: Some say that it is better to download the ROM source and put in your local manifest later. I don't know if that's best but that's what we are going to do.

BEFORE YOU BEGIN: You are about to start downloading 15 - 20 Gigs of data. That could take a very long time, so plan accordingly. I like to start just before I go to sleep and let it go overnight! If you have to work, perhaps start it before work and let it go through out the day.

Execute the following commands in a linux terminal:
```bash
mkdir /home/$USER/los
cd /home/$USER/los
repo init -u git://github.com/LineageOS/android.git -b cm-14.1
repo sync
```
WARNING: There may be times, towards the end when it seem like, the download is stuck and not making any progress because there are no updates on the screen. BE PATIENT!, open a program that will show how much bandwidth you are using to be sure!

### Step 3: Set up local manifest.

The local manifest is different for every device. It contains those repos that are device specific, where as the ROM code you just "repo sync'd" is code that is general to any device.

Execute the following commands in a linux terminal:
```bash
mkdir /home/$USER/los/.repo/local_manifests
vim /home/$USER/los/.repo/local_manifests/hi6250.xml
```
Copy the following into hi6250.xml, save and close.
```bash
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
  <project name="Exagone313/android_kernel_huawei_hi6250" path="kernel/huawei/hi6250" remote="github" revision="master"/>
  <project name="Exagone313/android_device_huawei_hi6250" path="device/huawei/hi6250" remote="github" revision="cm-14.1"/>
  <project name="Exagone313/android_vendor_huawei_hi6250" path="vendor/huawei/hi6250" remote="github" revision="master"/>
  <project name="LineageOS/android_vendor_nxp-nfc_opensource_Nfc" path="vendor/nxp-nfc/opensource/Nfc" remote="github" revision="cm-14.1"/>
  <project name="LineageOS/android_vendor_nxp-nfc_opensource_libnfc-nci" path="vendor/nxp-nfc/opensource/libnfc-nci" remote="github" revision="cm-14.1"/>
  <project name="LineageOS/android_vendor_nxp-nfc_opensource_frameworks" path="vendor/nxp-nfc/opensource/frameworks" remote="github" revision="cm-14.1"/>
</manifest>
```

Execute the following commands in a linux terminal:
```bash
cd /home/$USER/los
repo sync
```

NOTE: Yes we are syncing again and No, it shouldn't take quite as long. Every time you repo sync just new data is downloaded. So we are downloading the 4 repo's we just put in and any updates that may have occured to the repo's we already have.

### Step 4: Building

Turn off Ninja (optional)
Ninja is supposed to help make incremental updates more efficient but you aren't doing an incremental update; are you?
```bash
export USE_NINJA=false
```

Make sure there is enough memory for Jack(recommended).
This may not be neccessary depending on your PC's specs. On my previous laptop ( only 4Gig of mem ), I could not build without this and I keep it just in case.
```bash
export ANDROID_JACK_VM_ARGS="-Xmx4g -Dfile.encoding=UTF-8 -XX:+TieredCompilation"
```

Bake su, "root ", inside bacon (optional)
```bash
export WITH_SU=true
```

Turn on autopatch (recommended)
autopatch will apply device specific patches everytime you lunch.
```bash
export AUTOPATCH=true
```

If you did not turn on autopatch, you will want to apply the repo patches manually. These patches modify code in the ROM to work with this device.
Execute the following commands in a linux terminal:
```bash
cd /home/$USER/los/device/huawei/hi6250/patches && ./patch.sh
```

NOTE: Now you have everything that you need to build LineageOS 14.1 for your hi6250 device. Build times depend on your PC's performance specifications. In the following terminal command "-j8" represents the number of concurrent tasks to execute. For low specs machines (such as mine) lowering the value to "-j3" may help speed things up. For high spec'd machines raising the value may speed things up.

NOTE: It may take anywhere from 1 hours to 15 hours depending on system specs for a complete build.
Execute the following commands in a linux terminal:
```bash
cd /home/$USER/los
. build/envsetup.sh
lunch lineage_hi6250-userdebug
make -j8 bacon
```

NOTE: These trees can also build Resurection Remix (nougat), SlimRoms (nougat), and OmniRom (nougat) without modification but with logical changes to these instructions.
1. When running "repo init ...", you will obviously want to download the code for the respective ROM.
2. When building SlimRoms (nougat) or OmniRom (nougat) and running "lunch ...", you want slim_hi6250-userdebug or omni_hi6260-userdebug respectively.


### Please let me know if there are mistakes,typos, mis/outdated - information in these instructions by creating an "issue".
