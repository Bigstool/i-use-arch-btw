# Arch installation packages list

A list of packages to consider for an Arch Linux installation, or a Linux system in general.

Unverified: not sure whether this package helps or not.

Daemon: has a daemon that needs to be enabled

## Base installation

| Name                | Description                                                  | Comment                                                      | Unverified (x) | Daemon (x) | 3rd Party Repo URL                     |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- | ---------- | -------------------------------------- |
| base                | Minimal package set to define a basic Arch Linux installation |                                                              |                |            |                                        |
| base-devel          | Basic tools to build Arch Linux packages                     | Used when building AUR packages                              |                |            |                                        |
| linux-lts           | The LTS Linux kernel and modules                             |                                                              |                |            |                                        |
| linux-lts-headers   | Headers and scripts for building modules for the LTS Linux kernel |                                                              | x              |            |                                        |
| linux               | The Linux kernel and modules                                 | Not necessary if linux-lts is installed                      |                |            |                                        |
| linux-headers       | Headers and scripts for building modules for the Linux kernel | Not necessary if linux-lts-headers is installed              | x              |            |                                        |
| linux-firmware      | Firmware files for Linux                                     |                                                              |                |            |                                        |
| grub                | GNU GRand Unified Bootloader (2)                             | Add `--removable` option when running `grub-install` to allow for dual booting with Windows or booting on another machine |                |            |                                        |
| efibootmgr          | Linux user-space application to modify the EFI Boot Manager  |                                                              |                |            |                                        |
| btrfs-progs         | Btrfs filesystem utilities                                   | Refer to https://wiki.archlinux.org/title/File_systems       |                |            |                                        |
| mtools              | A collection of utilities to access MS-DOS disks             |                                                              | x              |            |                                        |
| dosfstools          | DOS filesystem utilities                                     |                                                              |                |            |                                        |
| exfatprogs          | exFAT filesystem userspace utilities for the Linux Kernel exfat driver |                                                              |                |            |                                        |
| ntfs-3g             | NTFS filesystem driver and utilities                         |                                                              |                |            |                                        |
| amd-ucode           | Microcode update image for AMD CPUs                          | Required if using an AMD CPU. Do refer to https://wiki.archlinux.org/title/Microcode for instructions. |                |            |                                        |
| intel-ucode         | Microcode update files for Intel CPUs                        | Required if using an Intel CPU. Do refer to https://wiki.archlinux.org/title/Microcode for instructions. |                |            |                                        |
| sudo                | Give certain users the ability to run some commands as root  |                                                              |                |            |                                        |
| man-db              | A utility for reading man pages                              |                                                              |                |            |                                        |
| vim                 | Vi Improved, a highly configurable, improved version of the vi text editor |                                                              |                |            |                                        |
| nano                | Pico editor clone with enhancements                          |                                                              |                |            |                                        |
| networkmanager      | Network connection manager and user applications             |                                                              |                | x          |                                        |
| bluez               | Daemons for the bluetooth protocol stack                     |                                                              |                | x          |                                        |
| ufw                 | Uncomplicated and easy to use CLI tool for managing a netfilter firewall |                                                              |                | x          |                                        |
| openssh             | SSH protocol implementation for remotelogin, command execution and file transfer |                                                              |                | x          |                                        |
| git                 | The fast distributed version control system                  |                                                              |                |            |                                        |
| reflector           | A Python 3 module and script to retrieve and filter the latest Pacman mirror list | `reflector --country France --sort rate --save /etc/pacman.d/mirrorlist` |                |            |                                        |
| bind                | A complete, highly portable implementation of the DNS protocol | `dig`, `host`, `nslookup`                                    |                |            |                                        |
| cronie              | Daemon that runs specified programs at scheduled times and related tools |                                                              |                | x          |                                        |
| zram-generator      | Systemd unit generator for zram devices                      | Refer to https://wiki.archlinux.org/title/Zram#Using_zram-generator when configuring. `zram-size` can be set to `ram` to make it as large as the physical memory ([ref](https://fedoraproject.org/wiki/Changes/Scale_ZRAM_to_full_memory_size)). |                | x          |                                        |
| snapper             | A tool for managing BTRFS and LVM snapshots                  | As a `timeshift` replacement when installed alongside `btrfs-assistant` |                |            |                                        |
| rsync               | A fast and versatile file copying tool for remote and local files |                                                              |                |            |                                        |
| zsh                 | A very advanced and programmable command interpreter (shell) for UNIX |                                                              |                |            |                                        |
| zsh-completions     | Additional completion definitions for Zsh                    |                                                              | x              |            |                                        |
| zsh-autosuggestions | Fish-like autosuggestions for zsh                            |                                                              | x              |            |                                        |
| pipewire            | Low-latency audio/video router and processor                 |                                                              |                |            |                                        |
| pipewire-alsa       | Low-latency audio/video router and processor - ALSA configuration |                                                              |                |            |                                        |
| pipewire-pulse      | Low-latency audio/video router and processor - PulseAudio replacement |                                                              |                |            |                                        |
| pipewire-jack       | Low-latency audio/video router and processor - JACK replacement |                                                              |                |            |                                        |
| wireplumber         | Session / policy manager implementation for PipeWire         |                                                              | x              |            |                                        |
| noto-fonts-cjk      | Google Noto CJK fonts                                        | For displaying Chinese, Japanese, and Korean fonts           |                |            |                                        |
| noto-fonts          | Google Noto TTF fonts                                        |                                                              |                |            |                                        |
| fuse2               | Interface for userspace programs to export a filesystem to the Linux kernel | Required to run AppImages                                    |                |            |                                        |
| cryptsetup          | Userspace setup tool for transparent encryption of block devices using dm-crypt |                                                              |                |            |                                        |
| sbctl               | Secure Boot key manager                                      |                                                              |                |            |                                        |
| yay                 | Yet another yogurt. Pacman wrapper and AUR helper written in go. | AUR helpers are not supported by Arch Linux (Ref: https://wiki.archlinux.org/title/AUR_helpers). Add the `--aur` flag to target only AUR packages (ref: https://www.reddit.com/r/ManjaroLinux/comments/cst4ou/yay_cache_cleaning/). |                |            | https://aur.archlinux.org/packages/yay |



## 32-bit support

Uncomment the `[multilib]` section in `/etc/pacman.conf` then `pacman -Syu`

| Name           | Description                                           | Comment | Unverified (x) | Daemon (x) | 3rd Party Repo URL |
| -------------- | ----------------------------------------------------- | ------- | -------------- | ---------- | ------------------ |
| lib32-pipewire | Low-latency audio/video router and processor - 32-bit |         | x              |            |                    |



## Graphics drivers

https://wiki.archlinux.org/title/Xorg#Driver_installation

### AMD

| Name                | Description                                     | Comment | Unverified (x) | Daemon (x) | 3rd Party Repo URL |
| ------------------- | ----------------------------------------------- | ------- | -------------- | ---------- | ------------------ |
| mesa                | Open-source OpenGL drivers                      |         |                |            |                    |
| vulkan-radeon       | Open-source Vulkan driver for AMD GPUs          |         |                |            |                    |
| xf86-video-amdgpu   | X.org amdgpu video driver                       |         |                |            |                    |
| xf86-video-ati      | X.org ati video driver                          |         | x              |            |                    |
| lib32-mesa          | Open-source OpenGL drivers - 32-bit             |         | x              |            |                    |
| lib32-vulkan-radeon | Open-source Vulkan driver for AMD GPUs - 32-bit |         | x              |            |                    |

### Nvidia

Do refer to https://wiki.archlinux.org/title/NVIDIA

| Name               | Description                       | Comment                    | Unverified (x) | Daemon (x) | 3rd Party Repo URL |
| ------------------ | --------------------------------- | -------------------------- | -------------- | ---------- | ------------------ |
| nvidia-lts         | NVIDIA drivers for linux-lts      | For the `linux-lts` kernel |                |            |                    |
| nvidia             | NVIDIA kernel modules             | For the `linux` kernel     |                |            |                    |
| nvidia-utils       | NVIDIA drivers utilities          |                            |                |            |                    |
| lib32-nvidia-utils | NVIDIA drivers utilities (32-bit) |                            |                |            |                    |



## Desktop environments

### GNOME

| Name                    | Description                                                  | Comment                                                      | Unverified (x) | Daemon (x) | 3rd Party Repo URL |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- | ---------- | ------------------ |
| gnome                   | Package group containing the base GNOME desktop and the well-integrated [core applications](https://apps.gnome.org/) |                                                              |                |            |                    |
| gnome-tweaks            | Graphical interface for advanced GNOME 3 settings (Tweak Tool) |                                                              |                |            |                    |
| gnome-shell-extensions  | Extensions for GNOME shell, including classic mode           |                                                              |                |            |                    |
| gnome-themes-extra      | Extra Themes for GNOME Applications                          | Enable dark theme for legacy applications by setting `Appearance` > `Legacy Applications` to `Adwaita-dark` in GNOME Tweaks |                |            |                    |
| gnome-browser-connector | Native browser connector for integration with extensions.gnome.org |                                                              |                |            |                    |
| seahorse                | A graphical interface for managing and using    encryption keys, passwords and certificates | Also manages SMB credentials                                 |                |            |                    |
| dconf-editor            | GSettings editor for GNOME                                   |                                                              |                |            |                    |

#### GNOME extensions

| Name                                         | Description                                                  | Comment                                                     | URL                                                          |
| -------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------------- | ------------------------------------------------------------ |
| Dash to Dock                                 | A dock for the Gnome Shell                                   |                                                             | https://extensions.gnome.org/extension/307/dash-to-dock/     |
| AppIndicator and KStatusNotifierItem Support | Adds AppIndicator, KStatusNotifierItem and legacy Tray icons support to the Shell |                                                             | https://extensions.gnome.org/extension/615/appindicator-support/ |
| Customize IBus                               | Tweaker of IBus for theme, font, input mode and clipboard history | Guide: https://github.com/openSUSE/Customize-IBus/tree/main | https://extensions.gnome.org/extension/4112/customize-ibus/  |
| Native Window Placement                      | Arrange windows in overview in a more compact way.           |                                                             | https://extensions.gnome.org/extension/18/native-window-placement/ |
| Clipboard Indicator                          | The most popular clipboard manager for GNOME, with over 1M downloads |                                                             | https://extensions.gnome.org/extension/779/clipboard-indicator/ |
| Desktop Icons NG (DING)                      | Adds icons to the desktop. Fork of the original Desktop Icons extension, with several enhancements. |                                                             | https://extensions.gnome.org/extension/2087/desktop-icons-ng-ding/ |

### Cinnamon

| Name                  | Description                                                  | Comment                                                      | Unverified (x) | Daemon (x) | 3rd Party Repo URL                                     |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- | ---------- | ------------------------------------------------------ |
| cinnamon              | Linux desktop which provides advanced innovative features and a traditional user experience |                                                              |                |            |                                                        |
| cinnamon-screensaver  | Screensaver designed to integrate well with the Cinnamon desktop | No need to be manually installed. Comes with the `cinnamon` package. Can be manually activated by running `cinnamon-screensaver-command -l`. |                |            |                                                        |
| lightdm               | A lightweight display manager                                | Needs to be configured to use the installed greeter. (ref: https://wiki.archlinux.org/title/LightDM#Greeter) |                | x          |                                                        |
| lightdm-slick-greeter | A slick-looking LightDM greeter                              | Can be configured in System Settings -> Administration -> Login Window, or using [lightdm-settings](https://aur.archlinux.org/packages/lightdm-settings) |                |            |                                                        |
| numlockx              | Turns on the numlock key in X11                              | Can be configured in System Settings -> Administration -> Login Window -> Settings -> Settings -> Activate numlock |                |            |                                                        |
| gnome-terminal        | The GNOME Terminal Emulator                                  |                                                              |                |            |                                                        |
| gnome-screenshot      | Take pictures of your screen                                 |                                                              |                |            |                                                        |
| nautilus              | Default file manager for GNOME                               | Optional replacement for `nemo`                              |                |            |                                                        |
| mint-l-icons          | A flat, colorful, and modern theme based on Paper and Moka (legacy) |                                                              |                |            | https://aur.archlinux.org/packages/mint-l-icons        |
| mint-l-theme          | A collection of legacy mint themes                           |                                                              |                |            | https://aur.archlinux.org/packages/mint-l-theme        |
| bibata-cursor-theme   | Material Based Cursor Theme                                  |                                                              |                |            | https://aur.archlinux.org/packages/bibata-cursor-theme |
| plank                 | Elegant, simple, clean dock                                  |                                                              |                |            |                                                        |

Workaround for `Super + Space` shortcut doesn't work at first press: https://github.com/ibus/ibus/issues/2474



## GUI applications

| Name            | Description                                                  | Comment                                                      | Unverified (x) | Daemon (x) | 3rd Party Repo URL |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- | ---------- | ------------------ |
| guake           | Drop-down terminal for GNOME                                 | In wayland, create a keyboard shortcut in settings with command `guake-toggle` to enable the shortcut for toggling `guake` |                |            |                    |
| timeshift       | A system restore utility for Linux                           | Timeshift only supports the GRUB2 bootloader. For other bootloaders, consider using `snapper` and `btrfs-assistant` instead (ref: https://github.com/linuxmint/timeshift). |                |            |                    |
| btrfs-assistant | An application for managing BTRFS subvolumes and Snapper snapshots | As a `timeshift` replacement when installed alongside `snapper` |                |            |                    |
| ibus            | Intelligent input bus for Linux/Unix                         | In GNOME, the ibus daemon will be automatically started. For other DE, add an autostart entry with command `ibus-daemon -rxRd`. |                | x          |                    |
| ibus-rime       | Rime input method engine for ibus                            | Switch between Chinese (Traditional) and Chinese (Simplified) with `F4` or ``` Ctrl+` ```. Edit the number of candidates: https://wiki.archlinux.org/title/Rime#Input_candidates |                |            |                    |
| ibus-anthy      | Japanese input method Anthy IMEngine for IBus Framework      |                                                              |                |            |                    |



## Additional CLI/GUI applications

| Name                      | Description                                                  | Comment                                                      | Unverified (x) | Daemon (x) | 3rd Party Repo URL                                           |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- | ---------- | ------------------------------------------------------------ |
| xorg-server               | Xorg X server                                                | For using Xorg instead of Wayland backend                    |                |            |                                                              |
| firefox                   | Fast, Private & Safe Web Browser                             |                                                              |                |            |                                                              |
| fastfetch                 | A feature-rich and performance oriented neofetch like system information tool |                                                              |                |            |                                                              |
| steam                     | Valve's digital software delivery system                     |                                                              |                |            |                                                              |
| gamemode                  | A daemon/lib combo that allows games to request a set of optimisations be temporarily applied to the host OS | Do refer to https://wiki.archlinux.org/title/GameMode for installation. To make Steam start a game with GameMode, set its launch command: `gamemoderun %command% [additional launch options]` |                |            |                                                              |
| lib32-gamemode            | A daemon/lib combo that allows games to request a set of optimisations be temporarily applied to the host OS | Refer to `gamemode` above                                    |                |            |                                                              |
| heroic-games-launcher-bin | An Open source Launcher for Epic, Amazon and GOG Games       | For running Windows applications                             |                |            | https://aur.archlinux.org/packages/heroic-games-launcher-bin |
| proton-ge-custom          | Compatibility tool for Steam Play based on Wine and additional components |                                                              |                |            | https://github.com/GloriousEggroll/proton-ge-custom          |
| qpwgraph                  | PipeWire Graph Qt GUI Interface                              | For routing pipewire audio                                   |                |            |                                                              |
| pavucontrol               | PulseAudio Volume Control                                    |                                                              |                |            |                                                              |
| gthumb                    | Image browser and viewer for the GNOME Desktop               | For viewing RAW images                                       |                |            |                                                              |
| raw-thumbnailer           | A lightweight and fast raw image thumbnailer that can be used by file managers. |                                                              |                |            | https://aur.archlinux.org/packages/raw-thumbnailer           |
| mission-center            | Monitor your CPU, Memory, Disk, Network and GPU usage        |                                                              |                |            |                                                              |



## Additional `systemd` Units

Enable using `sudo systemctl enable <Name>`.

| Name         | Provided by                     | Description                                  | Comment                                                      | 3rd Party Repo URL |
| ------------ | ------------------------------- | -------------------------------------------- | ------------------------------------------------------------ | ------------------ |
| fstrim.timer | util-linux (required by `base`) | Discard unused filesystem blocks once a week | Trims SSD. Do refer to https://wiki.archlinux.org/title/Solid_state_drive, especially verify TRIM support, before proceeding. |                    |



## References

https://wiki.archlinux.org

https://gist.github.com/mjkstra/96ce7a5689d753e7a6bdd92cdc169bae

https://www.youtube.com/watch?v=FxeriGuJKTM

https://wiki.archlinux.org/title/GRUB#Installation

