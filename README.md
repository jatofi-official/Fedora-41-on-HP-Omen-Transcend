# Fedora-41-on-HP-Omen-Transcend
First off, don't buy this laptop if you want to install linux on it. HP has buggy BIOS, the company doesn't support other operating systems than the one, which came preinstalled. 

However If you've made the same mistake of buying a HP Omen for linux, here's how you can set it up:

## Creating a custom DSDT table
THe main issue with HP Omen is, that you can't boot into it properly. The system bios is broken, you need to patch DSDT in order to boot. Unfortunately there are many different HP Omen laptops and many different BIOS verisons, so you need to patch it manually, installing someone else's DSDT most likely won't work 

1. First boot into usb with noapic parameter. Select current boot entry, predd `e` and write `noapic` into the line that starts with "linux".

2. Then install fedora normally. After booting into the installation, you need to install acpica-tools in order to decompile the dsdt table. Simply type `iasl` into the terminal then `y` to install.

3. After that you should upgrade the kernel with `sudo dnf upgrade`. This will take a while.

4. Open the terminal in a folder. Type `sudo cat /sys/firmware/acpi/tables/DSDT >dsdt.dat`. This will dump the broken DSDT into the folder.

5. Decompile the DSDT with `iasl -d dsdt.dat` This will create a dsdt.dsl wile.

6. Open the dsdt.dsl file and edit it with changes listed [here](https://github.com/j0hnwang/OMEN-Transcend-16-ACPI-fix/commit/2e4feda9529c09133f5f7e9623ec11226db581a5).

7. Recompile the file with `iasl -tc dsdt.dsl`. This should create two files, "dsdt.aml" and "dsdt.hex".

8. Copy the resulting dsdt.aml file to /boot. You will need sudo privileges for this. `sudo cp dsdt.aml /boot`

Now you can test, if the patch works.

## Testing the patch

1. Reboot.

2. In the grub menu, edit the linux boot parameters. Add `acpi /dsdt.aml` directly above the line, which starts with "linux". Remove `noacpi` from the line, which starts with "linux". Now boot.

3. Check if everything works. For me the super key didn't work, but it got fixed when I made the patch permanent

##Making the patch permanent
1. Edit `/etc/grub.d/40_custom`. Add `acpi /dsdt.aml` to tail.

2. If you've installed Fedora with the `noapic` parameter, you need to remove it from the grub config template. Open

### What doesn't work?

Brightness settings don't work, that is a different fix. Let me know if you've managed to fix it
