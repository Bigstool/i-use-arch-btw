# pacman

Tips and tricks for using the pacman package manager.



## Configuration

### Retrieve the latest mirrors, sort them by speed, and update `/etc/pacman.d/mirrorlist` using `reflector`

Refer to the [Select the mirrors](installation-guide.md#install-the-system) part of the installation guide.

### Enable parallel downloads

Set the `ParallelDownloads` under `[options]` in `/etc/pacman.conf` (ref: https://wiki.archlinux.org/title/Pacman#Enabling_parallel_downloads).



## Usage

Ref: https://wiki.archlinux.org/title/Pacman#Usage

### Full upgrade

```sh
sudo pacman -Syu
```

Always perform a full update even if you only want to refresh the package list (ref: https://wiki.archlinux.org/title/System_maintenance#Partial_upgrades_are_unsupported).

### Install packages

```sh
sudo pacman -S <package_name1> <package_name2> ...
```

### Install a package as dependency

```sh
sudo pacman -S --asdeps <package_name>
```

Alternatively, to change the installation reason of an already installed package:

```sh
sudo pacman -D --asdeps <package_name>
```

### Remove packages, along with dependencies no longer required and `.pacsave` files

```sh
sudo pacman -Rns <package_name1> <package_name2> ...
```

### Search for an already installed package

```sh
pacman -Qs <string>
```

### Get information of an installed package

```sh
pacman -Qi <package_name>
```

### Check for orphans

```sh
pacman -Qdt
```

To remove the orphans:

Ref: https://wiki.archlinux.org/title/Pacman/Tips_and_tricks#Removing_unused_packages_(orphans)

```sh
pacman -Qdtq | pacman -Rns -
```

### List packages explicitly installed

```sh
pacman -Qet
```

### Check to which installed package a file belongs

```sh
pacman -Qo </path/to/file>
```

### Remove cached packages that are not currently installed

```sh
sudo pacman -Sc
```

Pacman stores downloaded packages and does not remove old or uninstalled packages automatically, hence it may be desirable to remove the cached packages of uninstalled packages. (ref: https://wiki.archlinux.org/title/Pacman#Cleaning_the_package_cache)

Refer to the `pacman-contrib` section below to remove historical versions of a package from the cache while keeping the latest versions.



## `pacman-contrib`

`pacman-contrib` is a package of contributed scripts and tools for pacman systems. Install by running:

```sh
sudo pacman -S pacman-contrib
```

### Check for upgrades without running a system update

Ref: https://wiki.archlinux.org/title/System_maintenance#Partial_upgrades_are_unsupported

```sh
sudo checkupdates
```

### Keep only the three most recent cached versions for every package

Ref: https://wiki.archlinux.org/title/Pacman#Cleaning_the_package_cache

```sh
sudo paccache -r
```

To run this weekly:

```sh
sudo systemctl enable paccache.timer
```

### Find and manage `.pacnew` and `.pacsave` files

Ref: https://wiki.archlinux.org/title/Pacman/Pacnew_and_Pacsave

```sh
sudo pacdiff --output --find
```

Scans the active config files using find and prints the files instead of merging them with a merge program.



## Additional Notes

### Upgrade after a long time (untested)

Check the [Arch Linux News](https://archlinux.org/news/) for required manual intervention.

Make updates in small time skips following https://wiki.archlinux.org/title/Arch_Linux_Archive#How_to_restore_all_packages_to_a_specific_date.

If there are errors about PGP signature, update the keyring first with: (ref: https://www.reddit.com/r/linuxquestions/comments/1adyj27/updating_arch_after_1_year_without_updates_should/)

```sh
sudo pacman -Sy archlinux-keyring
```

### Manually building a package with a PKGBUILD file

Refer to https://wiki.archlinux.org/title/Arch_User_Repository#Installing_and_upgrading_packages

To install make dependencies before building and remove them afterwards, use `makepkg -rs` instead.



## Further Reading

https://wiki.archlinux.org/title/Pacman

https://wiki.archlinux.org/title/General_recommendations#Package_management

https://wiki.archlinux.org/title/System_maintenance

https://wiki.archlinux.org/title/Pacman/Tips_and_tricks
