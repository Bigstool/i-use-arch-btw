# Arch Linux installation guide

Btrfs on LUKS, separate EFI partition on `/efi`, GNOME, systemd-boot, TPM2

Before you proceed, you're recommended to refer to https://github.com/Bigstool/i-use-arch-btw/blob/main/installation-guides.md first. Compare and choose a path or a combination of paths suitable for your use case.

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
Command (m for help): g

# Expected output
Created a new GPT disklabel (GUID: C1BCB28A-66E7-4DE9-89CC-B808E30243B3).
```

Create the EFI system partition, allocating 1G of space:

```
Command (m for help): n
Partition number (1-128, default 1): <ENTER>
First sector (2048-468862094, default 2048): <ENTER>
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-468862094, default 468860927): +1G

# Expected output
Created a new partition 1 of type 'Linux filesystem' and of size 1 GiB.
```

Change the partition type to "EFI System":

```
Command (m for help): t
Selected partition 1
Partition type or alias (type L to list all): 1

# Expected output
Changed type of partition 'Linux filesystem' to 'EFI System'.
```

Create the Linux root partition, allocating all remaining space:

```
Command (m for help): n
Partition number (2-128, default 2): <ENTER>
First sector (2099200-468862094, default 2099200): <ENTER>
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2099200-468862094, default 468860927): <ENTER>

# Expected output
Created a new partition 2 of type 'Linux filesystem' and of size 222.6 GiB.
```

Change the partition type to "Linux root (x86_64)"

```
Command (m for help): t
Partition number (1,2, default 2): <ENTER>
Partition type or alias (type L to list all): 23

# Expected output
Changed type of partition 'Linux filesystem' to 'Linux root (x86-64)'.
```

Check the provisional partitions:

```
Command (m for help): p
```

Save the changes, or discard if you want to start over:

```
# To save
Command (m for help): w

# To discard
Command (m for help): q
```

### Format the partitions

For the EFI system partition:

```sh
mkfs.fat -F 32 /dev/sda1
```

For the Linux root partition, create a LUKS volume with a strong password:

```sh
cryptsetup luksFormat /dev/sda2
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

Refer to [Select the mirrors](https://github.com/Bigstool/i-use-arch-btw/blob/main/pacman.md#select-the-mirrors) to configure pacman mirrors.

Install base packages (for more details, refer to [Packages](https://github.com/Bigstool/i-use-arch-btw/blob/main/packages.md)):

```sh
pacstrap -K /mnt base base-devel linux-lts linux-lts-headers linux-firmware btrfs-progs dosfstools mtools cryptsetup sudo man-db vim nano cronie networkmanager reflector openssh git
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

### Install packages

> [!TIP]
> Take a look at the packages below and make adjustments according to preference and hardware. For example, Intel users should install `intel-ucode` instead of `amd-ucode`. For more details, refer to https://github.com/Bigstool/i-use-arch-btw/blob/main/packages.md.

```sh
pacman -Syu amd-ucode bluez fuse2 zsh pipewire wireplumber rsync noto-fonts-cjk noto-fonts gnome gnome-tweaks gnome-themes-extra gdm gnome-browser-connector guake firefox ibus ibus-rime ibus-anthy
```

### Configure time

Set the timezone:

```sh
ln -sf /usr/share/zoneinfo/<Area>/<Location> /etc/localtime
```

> [!TIP]
> Optionally, set the system to read the RTC time in the local time zone. This is useful when Windows will be run on the same computer:
>
> ```sh
> timedatectl set-local-rtc 1
> ```

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

Put the hostname in the first line of `/etc/hostname`. For example, if the desired hostname is `arch`:

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

Next, edit `/etc/mkinitcpio.d/linux-lts.preset` (or `/etc/mkinitcpio.d/linux.preset` if using the `linux` kernel), comment out the `_image` fields, uncomment the `_uki` and `_options` fields. Then, change the paths of the `.efi` files to `/efi/EFI/...`.

The `linux-lts.preset` file should now look somewhat like this:

```ini
# mkinitcpio preset file for the 'linux-lts' package

#ALL_config="/etc/mkinitcpio.conf"
ALL_kver="/boot/vmlinuz-linux-lts"
#ALL_kerneldest="/boot/vmlinuz-linux-lts"

#PRESETS=('default')
PRESETS=('default' 'fallback')

#default_config="/etc/mkinitcpio.conf"
#default_image="/boot/initramfs-linux-lts.img"
default_uki="/efi/EFI/Linux/arch-linux-lts.efi"
default_options="--splash /usr/share/systemd/bootctl/splash-arch.bmp"

#fallback_config="/etc/mkinitcpio.conf"
#fallback_image="/boot/initramfs-linux-lts-fallback.img"
fallback_uki="/efi/EFI/Linux/arch-linux-lts-fallback.efi"
fallback_options="-S autodetect"
```

If the initramfs was generated previously, remove the residual initramfs files in `/boot` as they are no longer relevant with UKI:

```sh
rm /boot/initramfs-linux-*
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
systemctl enable NetworkManager sshd cronie gdm bluetooth
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

> [!NOTE]
> It is assumed that all shell commands from this point onward are run as the newly created user with sudo privilege.

### Secure boot

(Ref: https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#Assisted_process_with_sbctl)

Install sbctl:

```sh
sudo pacman -Syu sbctl
```

Reboot the PC into firmware setup. Under the secure boot settings, put secure boot into enter setup mode by deleting the Platform Key (PK). If this is not possible, delete/clear the secure boot keys. If the firmware offers an "OS type" option, choose Windows instead of other OS, as this setup will be Microsoft secure boot compatible. (ref: https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#Putting_firmware_in_%22Setup_Mode%22, https://man.archlinux.org/man/sbctl.8#USAGE)

Boot back into Arch and verify that setup mode is indeed activated with:

```sh
sbctl status
```

If yes, create the custom secure boot keys:

```sh
sudo sbctl create-keys
```

The keys are stored in `/var/lib/sbctl/keys`.

Enroll the keys alongside Microsoft's keys and the OEM firmware's built-in certificates to the UEFI:

> [!WARNING]
> Do NOT omit the `-m` flag below, otherwise it might brick the device.

```sh
sudo sbctl enroll-keys -m -f
```

> [!TIP]
> If the command above runs into the following error:
>
> ```
> ‼ File is immutable: /sys/firmware/efi/efivars/KEK-732f2b7e-7013-4e43-be44-d0de168a3d92
> ‼ File is immutable: /sys/firmware/efi/efivars/db-141c4108-7a45-4de4-929a-4b27d558b59c
> You need to chattr -i files in efivarfs
> ```
>
> Temporarily remove the immutable flag of the UEFI secure boot variables with:
>
> ```sh
> sudo chattr -i /sys/firmware/efi/efivars/KEK-732f2b7e-7013-4e43-be44-d0de168a3d92
> sudo chattr -i /sys/firmware/efi/efivars/db-141c4108-7a45-4de4-929a-4b27d558b59c
> ```
>
> Then, try enrolling the keys again.

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
sudo sbctl sign -s /efi/EFI/Linux/arch-linux-lts.efi
sudo sbctl sign -s /efi/EFI/Linux/arch-linux-lts-fallback.efi
sudo sbctl sign -s /efi/EFI/BOOT/BOOTX64.EFI
sudo sbctl sign -s /efi/EFI/systemd/systemd-bootx64.efi
```

> [!tip]
> If an undesired file was accidentally signed, remove the file from the signing database with:
>
> ```sh
> sudo sbctl remove-file <file>
> ```

For systemd-boot, sign the boot loader directly in `/usr/lib` as well (ref: https://wiki.archlinux.org/title/Unified_Extensible_Firmware_Interface/Secure_Boot#Automatic_signing_with_the_pacman_hook):

```sh
sudo sbctl sign -s -o /usr/lib/systemd/boot/efi/systemd-bootx64.efi.signed /usr/lib/systemd/boot/efi/systemd-bootx64.efi
```

Reboot, and check that secure boot is working with:

```sh
sbctl status
```

### Enroll TPM

Enroll the key, replace `<UUID>` with the one used in [Configure mkinitcpio and unified kernel image](#configure-mkinitcpio-and-unified-kernel-image):

```sh
sudo systemd-cryptenroll /dev/disk/by-uuid/<UUID> --wipe-slot=empty --tpm2-device=auto --tpm2-pcrs=0+2+5+7+15:sha256=0000000000000000000000000000000000000000000000000000000000000000
```

`0`: Core System Firmware executable code (aka Firmware). May change if you upgrade your UEFI.

`2`: Extended or pluggable executable code; includes option ROMs on pluggable hardware.

`5`: GPT/Partition table; changes when the partitions are added, modified, or removed.

`7`: Secure Boot state; changes when UEFI SecureBoot mode is enabled/disabled, or firmware certificates (PK, KEK, db, dbx, ...) changes.

`15`: Root LUKS volume key, machine ID, mount points, file system UUIDs, labels, partition UUIDs; starts being all zero at boot.

> [!WARNING]
> Only binding to PCRs 0-7 can introduce vulnerabilities. Refer to sources including https://wiki.archlinux.org/title/Systemd-cryptenroll#Trusted_Platform_Module, https://wiki.archlinux.org/title/Trusted_Platform_Module#Accessing_PCR_registers, https://uapi-group.org/specifications/specs/linux_tpm_pcr_registry/, and https://man.archlinux.org/man/systemd-cryptenroll.1 for more information, then choose the combination that works for the particular setup and threat model.

Append the following to `/etc/kernel/cmdline`:

```ini
rd.luks.options=cryptroot:tpm2-measure-pcr=yes
```

Regenerate initramfs:

```sh
sudo mkinitcpio -P
```

Reboot to see if the drive is automatically unlocked. The installation is complete.

To change the enrolled TPM key, wipe the existing slot first with:

```sh
sudo systemd-cryptenroll /dev/disk/by-uuid/<UUID> --wipe-slot=tpm2
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

#### Install Snapper

```sh
sudo pacman -Syu snapper
```

#### Configure mount point

Ref: https://wiki.archlinux.org/title/Snapper#Suggested_filesystem_layout

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

#### Configure automatic snapshots

Automatic timeline snapshots is enabled by default with a cron daemon correctly set up. Edit the configurations in `/etc/snapper/configs/root` to liking. For example:

```ini
TIMELINE_MIN_AGE="1800"
TIMELINE_LIMIT_HOURLY="5"
TIMELINE_LIMIT_DAILY="7"
TIMELINE_LIMIT_WEEKLY="0"
TIMELINE_LIMIT_MONTHLY="0"
TIMELINE_LIMIT_YEARLY="0"
```

#### GUI helper

Btrfs Assistant is a GUI tool that can manage Btrfs subvolumes and Snapper snapshots. Install Btrfs Assistant with:

```sh
sudo pacman -Syu btrfs-assistant
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

ChatGPT

https://wiki.archlinux.org/title/Mkinitcpio#Post_hooks

Doubao

