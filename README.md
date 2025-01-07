# Coreboot ThinkPad T400

Here are all the files for configuring your own coreboot rom or you can flash a precompiled one. My goal was to install coreboot to do the Core 2 Quad modification, currently waiting on the CPU.

# Compiled ROMs
All ROMs are intended for a T400 with an Intel GPU, 1280x800 screen. Extracted ME,Gbe and descriptor from original BIOS and added them in the config. Enabled Intel WiFi cards, although it seemed to work regardless of this option being checked. Load the config and verify all the selected options for your machine. Coreboot documentation goes over how to create toolchain and make your own ROM if you wish.

T400_EDK2 contains the flashable rom and config for that rom. Using EDK2 payload. This will only allow UEFI x64 booting, no BIOS. Windows 7 stock EFI did not boot, Windows 10 x64 runs fine. Windows 11's latest update requires SSE4.2, as a result it will NOT boot. Core 2 only has SSE4.1. 

You can adjust the config with your own payload. I tested SeaBIOS 1.16.3 and it flashed fine. Windows 7 would install but fail to boot on the second phase. Linux distros worked for the most part but were a little iffy and some took long to boot. I assume you need the VGA bios but I couldn't seem to find or extract it. I used libgfxinit instead.

# How to Flash?
You'll need to externally flash your BIOS chip, I used a CH341a programmer to do this. Unfortunately, the bios chip is beneath the magnesium chassis requiring you to disassemble the entire machine. Some people have cut out the part of the shell that covers the chip, it's up to you but be careful not to cut into the board. 

Following this you'll have a 8pin or 16pin chip, in my case I had a MX25L6405D(SOP16) chip. I soldered a wire to each leg rather than wait for a SOP16 clip to arrive. Look up your chip(writing should be on it) and check the pinout to be sure its hooked up to the programmer correctly. Remember to backup your original rom, then flash. T400 chip I have flashed fine at 5v(no 1.8v or 3.3v adapter). Any errors, recheck your wiring.

Using my M1 mac running AsahiLinux I ran, adjust accordingly to your chip/rom/programmer:

'sudo flashrom -p ch341a_spi -c MX25L6405D -w corebootedk2t400.rom'

Now that you've flashed your coreboot bios, you can flash internally using linux if you wish. I needed "iomem=relaxed" bootflag in /etc/default/grub to do so, then run sudo update-grub.

'sudo flashrom -p internal -c MX25L6405D -w updated.rom'

# Disclaimer
I'm not responsible for any issues you encounter, flashing your BIOS can be risky and result in bricking your device if done incorrectly. Although chances are if you've managed to do this, you'll have the means to flash it externally and fix it.
