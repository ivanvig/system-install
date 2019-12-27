This guide supposes that you have created, formatted and mounted a partition scheme already (with efi partition mounted in /boot). Let's begin

Change keymap:

loadkeys la-latin1

[If using WiFi]
Connect to internet using wifi-menu and dhcpd

Install base, kernel and some basic packages (iwd is only necessary for wifi connection):

# pacstrap /mnt base linux linux-firmware vim sudo iwd connman

Generate fstab file

# genfstab -U /mnt >> /mnt/etc/fstab

Remove the fstab entry for the root partition, [remember to add the rw flag in the kernel parameters later]
Add noauto,x-systemd.automount flags and to efi partition

Change root into the new system:

# arch-chroot /mnt

Set the time zone:

# ln -sf /usr/share/zoneinfo/Region/City /etc/localtime

Run hwclock(8) to generate /etc/adjtime:

# hwclock --systohc

Uncomment en_US.UTF-8 UTF-8 and other needed locales in /etc/locale.gen, and generate them with:

# locale-gen

Create the locale.conf file, and set the LANG variable accordingly:

/etc/locale.conf
LANG=en_US.UTF-8

make the changes in keymap persistent in vconsole.conf:

/etc/vconsole.conf
KEYMAP=la-latin1

Create the hostname file:

/etc/hostname
myhostname

Add matching entries to hosts:

/etc/hosts
127.0.0.1	localhost
::1		localhost
127.0.1.1	myhostname.localdomain	myhostname


Install package needed for mirror sorting

# pacman -S pacman-contrib

Back up the existing /etc/pacman.d/mirrorlist:

# cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup

To uncomment every mirror, run the following sed line:

# sed -i 's/^#Server/Server/' /etc/pacman.d/mirrorlist.backup

Finally, rank the mirrors, here with the operand -n 6 to only output the 6 fastest mirrors:

# rankmirrors -n 6 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist


[Only if you are using LVM] Edit the file and insert lvm2 between block and filesystems like so:

/etc/mkinitcpio.conf
HOOKS=(base udev ... block lvm2 filesystems)

Install lvm2:

# pacman -S lvm2

This will automatically create a new initramf, otherwise run

# mkinitcpio -P

Set the root password:

# passwd

Bootloader

We use efistub here

Install efibootmgr

# pacman -S efibootmgr

Install Intel microcode (use AMD microcode if you have that cpu, modify the efi entry accordingly)

# pacman -S intel-ucode

Create the efi entry:

efibootmgr --disk /dev/nvme0n1 --part 1 --create --label Arch Linux --loader /vmlinuz-linux --unicode 'root=/dev/mapper/vg0-root rw quiet vga=current resume=/dev/mapper/vg0-swap initrd=/intel-ucode.img initrd=/initramfs-linux.img' --verbose

efibootmgr --disk /dev/nvme0n1 --part 1 --create --label "Arch Linux" --loader /vmlinuz-linux --unicode 'root=/dev/mapper/vg0-root rootflags=rw,noatime quiet vga=current nowatchdog resume=/dev/mapper/vg0-swap initrd=/intel-ucode.img initrd=/initramfs-linux.img' --verbose

Blacklist the watchdog timer module

/etc/modprobe.d/blacklist.conf
blacklist iTCO_wdt

You are now in a bootable system


Install zsh:

# pacman -S zsh

Create a user:

useradd -m -s /bin/zsh user

Add user in sudoers with visudo
