# Arch Linux installation guide

Btrfs on LUKS, separate EFI partition on `/efi`, GNOME, systemd-boot, TPM2

## Essentials

### Keyboard layout

Boot the Arch Linux installation image, then configure the console keyboard layout with:

```sh
loadkeys us
```

Available keys can be listed with:

```sh
localectl list-keymaps
```

### Check the boot mode is UEFI

```sh
cat /sys/firmware/efi/fw_platform_size
```

You are in UEFI if the returned value is 64 or 32.

### Check the internet connection

```sh
ping -c 5 archlinux.org
```

### Check the system clock

Check that NTP service is active:

```sh
timedatectl
```

**[OPTIONAL]** Set the system to read the RTC time in the local time zone. This is useful when Windows will be run on the same computer:

```sh
timedatectl set-local-rtc 1
```

### Partition the disk

The partition layout being used is as follows:

```
+-----------------------+---------------------------------+
| EFI system partition  | LUKS encrypted root partition   |
|                       |                                 |
|                       |                                 |
| /efi                  | /                               |
|                       |                                 |
|                       | /dev/mapper/cryptroot           |
|                       |---------------------------------|
| /dev/sda1             | /dev/sda2                       |
+-----------------------+---------------------------------+
```

Find the right disk with:

```sh
fdisk -l
```

Partition the disk using `/dev/sda` as example:

```sh
fdisk /dev/sda
```

Create a new GPT partition table:

```
g
ENTER
```

Create the EFI system partition, allocating 1G of space:

```
n
ENTER
ENTER
ENTER
+1G
ENTER
# Change the partition type
t
ENTER

```

Change the partition type to "EFI System":

```
t
ENTER
1
ENTER
```

Create the Linux root partition, allocating all remaining space:

```
n
ENTER
ENTER
ENTER
ENTER
```

Change the partition type to "Linux root (x86_64)"

```
t
ENTER
2
ENTER
23
ENTER
```

Check the provisional partitions:

```
p
ENTER
```

Save the changes, or discard if you want to start over:

```
# To save
w
ENTER

# To discard
q
ENTER
```

### Format the partitions

For the EFI system partition:

```sh
mkfs.fat -F 32 /dev/sda1
```

For the Linux root partition, first create a LUKS volume with a blank password which will be wiped later$^\text{[verification needed]}$:

```sh
cryptsetup lukeFormat /dev/sda2
cryptsetup open /dev/sda2 cryptroot
```

The volume will be available at `/dev/mapper/cryptroot`. Create the Btrfs file system with:

```sh
mkfs.btrfs /dev/mapper/cryptroot
```

### Create subvolumes for the Btrfs file system

Mount the file system to `/mnt`:

```sh
mount /dev/mapper/cryptroot /mnt
```

Create the subvolumes as desired (refer to https://wiki.archlinux.org/title/Snapper#Suggested_filesystem_layout for other layouts):

```sh
btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@home
btrfs subvolume create /mnt/@log
btrfs subvolume create /mnt/@cache
btrfs subvolume create /mnt/@tmp
btrfs subvolume create /mnt/@snapshots
```

Unmount:

```sh
umount /mnt
```

### Mount the file systems and subvolumes

Mount the root:

```sh
mount -o compress=zstd,subvol=@ /dev/mapper/cryptroot /mnt
```

Create the directories to mount to:

```sh
mkdir -p /mnt/{home,var/log,var/cache,var/tmp,.snapshots,efi}
```

Mount the rest:

```sh
mount -o compress=zstd,subvol=@home /dev/mapper/cryptroot /mnt/home
mount -o compress=zstd,subvol=@log /dev/mapper/cryptroot /mnt/var/log
mount -o compress=zstd,subvol=@cache /dev/mapper/cryptroot /mnt/var/cache
mount -o compress=zstd,subvol=@tmp /dev/mapper/cryptroot /mnt/var/tmp
mount -o compress=zstd,subvol=@snapshots /dev/mapper/cryptroot /mnt/.snapshots
mount /dev/sda1 /mnt/efi
```

### Install the system

Select the mirrors:

```sh
reflector --country <country> --sort rate --save /etc/pacman.d/mirrorlist
```

Install packages (for more details, refer to https://github.com/Bigstool/i-use-arch-btw/blob/main/packages.md):

```sh
pacstrap -K /mnt base base-devel linux-lts linux-lts-headers linux linux-headers linux-firmware btrfs-progs dosfstools mtools amd-ucode sudo man-db vim nano networkmanager bluez ufw openssh git reflector cronie zram-generator snapper rsync zsh zsh-completions zsh-autosuggestions pipewire pipewire-alsa pipewire-pulse pipewire-jack wireplumber noto-fonts-cjk noto-fonts fuse2 gnome gnome-tweaks gnome-themes-extra gdm gnome-browser-connector guake ibus ibus-rime ibus-anthy firefox carla cryptsetup sbctl
```

### Fstab

Generate Fstab config w.r.t. how the file systems and subvolumes are mounted now:

```sh
genfstab -U /mnt >> /mnt/etc/fstab
```

### Chroot into the new system

```sh
arch-chroot /mnt
```

### Configure time

Set the timezone:

```sh
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
```

Sync to the hardware clock:

```sh
hwclock --systohc
```

### Locale and keymap

Uncomment the locales that you wish to generate:

```sh
nano /etc/locale.gen
```

Generate the selected locales by:

```sh
locale-gen
```

Set the system language in `/etc/locale.conf`:

```ini
LANG=en_US.UTF-8
```

Set the keymap in `/etc/vconsole.conf`:

```ini
KEYMAP=us
```

### Hostname

Put the hostname in the first line of `/etc/hostname`:

```ini
arch
```

Edit `/etc/hosts` to include the hostname:

```ini
127.0.0.1 localhost
::1 localhost
127.0.1.1 arch
```

### Create user

Create a new user in the administration group:

```sh
useradd -mG wheel bigstool
```

Create a password for the new user:

```sh
passwd bigstool
```

Grant sudo privilege to the new user:

```sh
EDITOR=nano visudo
```

Add the following line under `# User privilege specification`:

```ini
bigstool ALL=(ALL) NOPASSWD: ALL
```

Verify that the privilege has been granted:

```sh
# As root
su - bigstool
# As the new user
sudo echo hello  # Should print "hello" to the console
exit
```

### Configure the default Btrfs subvolume

Specifically for the Btrfs file system, find the subvolume ID of the root subvolume of the Btrfs file system with:

```sh
btrfs subvolume list -t /
```

The output should include something like this:

```
ID	gen	top level	path	
--	---	---------	----	
256	370	5		    @
```

The ID of path `@` is what to look for. In this case, it is `256`.

Change the default subvolume, replace \<id\> with the real value:

```sh
btrfs subvolume set-default <id> /
```

### Configure mkinitcpio and unified kernel image

Build a working systemd based initramfs by modifying the `HOOKS` of `/etc/mkinitcpio.conf`:

```ini
HOOKS=(base *systemd* autodetect microcode modconf kms *keyboard* *sd-vconsole* block *sd-encrypt* filesystems fsck)
```

Pay attention to the items surrounded with \***asterisks**\* and add them **without** the asterisks.

Find the UUID of the encrypted `/dev/sda2` partition with:

```sh
blkid
```

The output should include something like this:

```
/dev/sda2: UUID="06b34979-42f7-4033-95bd-6587b49191a0" TYPE="crypto_LUKS" PARTUUID="642251ea-aa7c-4a7c-ba49-3e41ccebeb3e"
```

The `UUID` (not the `PARTUUID`) is what to look for. Edit the kernel command line with:

```sh
nano /etc/kernel/cmdline
```

Add the following, then save and exit:

```ini
rd.luks.name=<UUID>=cryptroot root=/dev/mapper/cryptroot
```

In the example, it is:

```ini
rd.luks.name=06b34979-42f7-4033-95bd-6587b49191a0=cryptroot root=/dev/mapper/cryptroot
```

Next, edit the `/etc/mkinitcpio.d/linux.preset` and `/etc/mkinitcpio.d/linux-lts.preset` files, comment out the `_image` fields, uncomment the `_uki` and `_options` fields. Then, change the paths of the `.efi` files to `/efi/EFI/...`.

As an example, the `linux.preset` file should now look somewhat like this:

```ini
# mkinitcpio preset file for the 'linux' package

#ALL_config="/etc/mkinitcpio.conf"
ALL_kver="/boot/vmlinuz-linux"

PRESETS=('default' 'fallback')

#default_config="/etc/mkinitcpio.conf"
#default_image="/boot/initramfs-linux.img"
default_uki="/efi/EFI/Linux/arch-linux.efi"
default_options="--splash /usr/share/systemd/bootctl/splash-arch.bmp"

#fallback_config="/etc/mkinitcpio.conf"
#fallback_image="/boot/initramfs-linux-fallback.img"
fallback_uki="/efi/EFI/Linux/arch-linux-fallback.efi"
fallback_options="-S autodetect"
```

Additional note: https://wiki.archlinux.org/title/Unified_kernel_image#pacman_hook

### Boot loader

Install the systemd-boot boot loader with:

```sh
bootctl install
```

Optionally, configure the boot loader to display a menu for kernel selection at boot by editing `/efi/loader/loader.conf` (ref: https://wiki.archlinux.org/title/Systemd-boot#Loader_configuration, https://www.freedesktop.org/software/systemd/man/latest/loader.conf.html):

```ini
default @saved
timeout 3
console-mode max
editor no
```

### Finalizing the installation

Regenerate initramfs with:

```sh
mkinitcpio -P
```

Enable services with:

```sh
systemctl enable systemd-resolved systemd-timesyncd NetworkManager ufw sshd cronie gdm
```

Exit from chroot:

```sh
exit
```

Sync and unmount everything:

```sh
sync
umount -R /mnt
```

Shut down:

```sh
poweroff
```

### First boot

Try booting the new system. If it works, shut down the PC.

**NOTE**: It is assumed that all shell commands from this point onward are run as the newly created user with sudo privilege.

### Secure boot

(Ref: https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#Putting_firmware_in_%22Setup_Mode%22, https://man.archlinux.org/man/sbctl.8#USAGE)

Set secure boot mode to setup mode by booting the PC into firmware setup and clear the secure boot keys, then reboot into the new system. Verify that setup mode is indeed activated with:

```sh
sbctl status
```

If yes, create the custom secure boot keys:

```sh
sudo sbctl create-keys
```

Enroll the keys with Microsoft's keys to the UEFI:

**NOTE**: Do NOT omit the `-m` flag below, otherwise it might brick the device.

```sh
sudo sbctl enroll-keys -m
```

Check the status again, sbctl should be installed now:

```sh
sbctl status
```

Check what files need to be signed:

```sh
sudo sbctl verify
```

Sign all the unsigned files. For example:

```sh
sudo sbctl sign -s /efi/EFI/Linux/arch-linux.efi
sudo sbctl sign -s /efi/EFI/Linux/arch-linux-fallback.efi
```

For systemd-boot, sign the boot loader directly in `/usr/lib` as well (ref: https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#Automatic_signing_with_the_pacman_hook):

```sh
sudo sbctl sign -s -o /usr/lib/systemd/boot/efi/systemd-bootx64.efi.signed /usr/lib/systemd/boot/efi/systemd-bootx64.efi
```

Reboot with secure boot turned back on in the firmware settings, and check that secure boot is working with:

```sh
sbctl status
```

### Enroll TPM

Create a recovery key:

```sh
sudo systemd-cryptenroll /dev/sda2 --recovery-key
```

Enroll the key:

```sh
sudo systemd-cryptenroll /dev/sda2 --wipe-slot=empty --tpm2-device=auto --tpm2-pcrs=7
```

Reboot to see if the drive is automatically unlocked. The installation is complete.

#### On systems without secure boot

(Ref: https://wiki.archlinux.org/title/Trusted_Platform_Module#Accessing_PCR_registers, https://wiki.archlinux.org/title/Systemd-cryptenroll#Trusted_Platform_Module)

Consider other PCR slots instead of 7. To change the enrolled TPM key, wipe the existing slot first with:

```sh
sudo systemd-cryptenroll /dev/sda2 --wipe-slot=tpm2
```

Then, enroll the key again with modified PCR.



## Optionals

### Post install considerations

Refer to https://barn.bigstool.com/post/server-config/

### Further configurations for pacman

Refer to https://github.com/Bigstool/i-use-arch-btw/blob/main/pacman.md

### Install and configure additional packages

Take a further look at https://github.com/Bigstool/i-use-arch-btw/blob/main/packages.md

### Configure Snapper snapshots

Install Snapper:

```sh
sudo pacman -S snapper
```

Unmount and remove the `/.snapshots` directory since Snapper assumes that `/.snapshots` is not mounted and does not exist as a folder:

```sh
sudo umount /.snapshots
sudo rm -r /.snapshots
```

Create a new configuration for `/`:

```sh
sudo snapper -c root create-config /
```

Delete the subvolume `.snapshots` newly created by Snapper:

```sh
sudo btrfs subvolume delete /.snapshots
```

Recreate the `/.snapshots` directory:

```sh
sudo mkdir /.snapshots
```

Mount `@snapshots` to `/.snapshots` utilizing the existing fstab entry:

```sh
sudo mount -a
```

Verify the mount with:

```sh
findmnt -nt btrfs
```

Give the folder `750` permissions:

```sh
sudo chmod 750 /.snapshots/
```

Automatic timeline snapshots is enabled by default with a cron daemon correctly set up. Edit the configurations in `/etc/snapper/configs/root` to liking. For example:

```ini
TIMELINE_MIN_AGE="1800"
TIMELINE_LIMIT_HOURLY="5"
TIMELINE_LIMIT_DAILY="7"
TIMELINE_LIMIT_WEEKLY="0"
TIMELINE_LIMIT_MONTHLY="0"
TIMELINE_LIMIT_YEARLY="0"
```

#### Backup `/efi`

The unified kernel images are stored in the EFI system partition and will not be included in the Btrfs snapshots. In case of kernel updates, returning to a snapshot with older kernel version would draw the system unbootable (ref: https://wiki.archlinux.org/title/EFI_system_partition#Typical_mount_points). Therefore, we create a post hook in `mkinitcpio` to make a backup of `/efi` every time the unified kernel image is generated to be later restored with the snapshot.

Create the target directory of backup:

```sh
sudo mkdir /efibak
```

Create the following script as `/etc/initcpio/post/efibackup.sh`:

```sh
#!/bin/bash

echo "Backing up /efi to /efibak..."
rsync -a --delete /efi/ /efibak/
echo "Done!"
```

Give the script permissions to execute:

```sh
sudo chmod +x /etc/initcpio/post/efibackup.sh
```

Regenerate initramfs with:

```sh
sudo mkinitcpio -P
```

TODO: run the post hook after sbctl

#### Preparations for restoring snapshots

Install Btrfs Assistant with:

```sh
yay -Syu btrfs-assistant
```

Create a temporary mount point for the restored root for later restoration of `/efi`:

```sh
sudo mkdir /newroot
```

### Restore a Snapper snapshot

#### Restore the snapshot

In Btrfs Assisatnt, restore the desired snapshot at Snapper > Browse/Restore.

#### Change the default Btrfs root to the new `@` subvolume

Refer to [Configure the default Btrfs subvolume](#configure-the-default-btrfs-subvolume). Note that the commands need to be run with `sudo` privilege.

#### Restore `/efi`

Mount the new root to `/newroot` with:

```sh
sudo mount -o compress=zstd,subvol=@ /dev/mapper/cryptroot /newroot
```

Overwrite `/efi` with the backup included in the snapshot:

```sh
sudo rsync -a --delete /newroot/efibak/ /efi/
```

Unmount:

```sh
sudo umount /newroot
```

#### Reboot and verify

Reboot:

```sh
sudo reboot
```

The system should boot without any problem. After logging in, verify that `/` is indeed mounted with `@` with:

```sh
findmnt -nt btrfs
```

The output should look something like this:

```
/                                 /dev/mapper/cryptroot[/@]       btrfs rw,relatime,compress=zstd:3,ssd,space_cache=v2,subvolid=300,subvol=/@
```

Make sure that `/dev/mapper/cryptroot[/@]` is mounted at `/`.



## Additional Notes

### On mounting the top-level Btrfs filesystem after installation

```sh
sudo mount -o subvolid=5 /dev/mapper/cryptroot /mnt
```

This prevents mounting `@` instead.



## References

https://wiki.archlinux.org/title/Installation_guide#Mount_the_file_systems

https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system#LUKS_on_a_partition

https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system#LUKS_on_a_partition_with_TPM2_and_Secure_Boot

https://gist.github.com/mjkstra/96ce7a5689d753e7a6bdd92cdc169bae

https://walian.co.uk/arch-install-with-secure-boot-btrfs-tpm2-luks-encryption-unified-kernel-images.html

https://itsfoss.com/wrong-time-dual-boot/

https://wiki.archlinux.org/title/Unified_kernel_image#mkinitcpio

https://wiki.archlinux.org/title/Btrfs#Mounting_subvolume_as_root

https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#Assisted_process_with_sbctl

ChatGPT

https://wiki.archlinux.org/title/Snapper#Suggested_filesystem_layout

https://wiki.archlinux.org/title/Mkinitcpio#Post_hooks



