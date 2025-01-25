# pacman

Tips and tricks for using the pacman package manager.



## Configuration

### Retrieve the latest mirrors, sort them by speed, and update `/etc/pacman.d/mirrorlist` using `reflector`

Replace `<country>` with the country where the Arch installation is located.

```sh
reflector --country <country> --sort rate --save /etc/pacman.d/mirrorlist
```

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

### Remove packages, along with dependencies no longer required and `.pacsave` files

```sh
sudo pacman -Rns <package_name1> <package_name2> ...
```

### Search for an already installed package

```sh
sudo pacman -Qs <string>
```

### Remove cached packages that are not currently installed

```sh
pacman -Sc
```

Pacman stores downloaded packages and does not remove old or uninstalled packages automatically, hence it may be desirable to remove the cached packages of uninstalled packages. (ref: https://wiki.archlinux.org/title/Pacman#Cleaning_the_package_cache)

Refer to the `pacman-contrib` section below to remove historical versions of a package from the cache while keeping the latest versions.

### Check for orphans

```sh
pacman -Qdt
```

### Remove orphans

Ref: https://wiki.archlinux.org/title/Pacman/Tips_and_tricks#Removing_unused_packages_(orphans)

```sh
pacman -Qdtq | pacman -Rns -
```



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
sudo sudo pacdiff --output --pacmandb
```

Scans the active config files from the pacman database and print the files instead of merging them with a merge program.



## Further Reading

https://wiki.archlinux.org/title/Pacman

https://wiki.archlinux.org/title/General_recommendations#Package_management

https://wiki.archlinux.org/title/System_maintenance

https://wiki.archlinux.org/title/Pacman/Tips_and_tricks
