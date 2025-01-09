# `pacman-contrib`

Tips and tricks for using `pacman-contrib`, the contributed scripts and tools for pacman systems.

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

