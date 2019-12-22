# patching-64-bit-bootloaders

**Overview**

This is a guide for patching 64-bit iOS bootloaders manually for booting with checkm8, since iBoot64Patcher does not work with pre-iOS 10 bootloaders. 

**Requirements**

* The desired ipsw, and firmware keys

* A Mac or a macOS/OS X dual boot/virtual machine

* IDA Pro

* [img4lib](https://github.com/xerub/img4lib)

* [img4tool](https://github.com/tihmstar/img4tool)

**Decrypting files** 

Step 1: Download the desired iOS firmware for which you want to decrypt the bootloaders. You can download the ipsws from ipsw.me or TheiPhoneWiki.

Step 2: Once you have downloaded the iOS firmware, rename the extension to `.zip` and unzip it.

Step 3: Decrypt the bootloader you are trying to patch with img4lib with this command: `img4 -i <path-to-bootloader.im4p> -o <path-to-bootloader.dec> -k [iv][k]`. The IV and keys are combined. You can find firmware keys in [TheiPhoneWiki](https://www.theiphonewiki.com/wiki/Firmware).

Step 4: Open the decrypted bootloader in IDA Pro 64 and set Processor type to `ARM Little-endian`. Once that is done, click Select all in Edit toolbar and type "C" to convert to readable disassembly. 

Step 5: Find `image4_property_callback_interposer` by going to Search > immediate value..., and find the immediate value `0x4348`. In graph mode, go to the top of the function, click on `STP X29, X30` in the beginning of the function, then click on Edit > Patch program > Change byte and change `FD 7B BF A9 FD 03 00 91` to `00 00 80 D2 C0 03 5F D6`.

Step 6: On Stage 2 bootloaders (iBoot and iBEC), you also need to patch `debug-enabled` and `development-cert`. Search for `debug-enabled`, click on the first `BL` for both `debug-enabled` and `development-cert`, and change the first 4 bytes to `20 00 80 D2`. Click Program > Apply patches to input file... to save the patched iBoot. 

Step 7: See [tihmstar's tweet](https://twitter.com/tihmstar/status/1178988030708916224) on how to repack bootloaders into an IMG4 file. NOTE: On newer img4tool versions, use `-d` instead of `--info`, and `-t` instead of `--tag`.

Step 8: Now you can test the patched bootloader by uploading a decrypted, and repacked file, i.e. a custom boot logo, device tree, ramdisk, and/or a kernel.

**Special thanks to:**

@xerub for img4lib

@tihmstar for img4tool

@axi0mX for checkm8
