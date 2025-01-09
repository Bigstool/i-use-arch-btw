# Arch installation packages list

A list of packages to consider for an Arch installation, or a Linux system in general.

Unverified: not sure whether this package helps or not.

Daemon: has a daemon which needs to be enabled

## Base installation

| Name                | Description                                                  | Comment                                                      | Unverified (x) | Daemon (x) | 3rd Party Repo URL                     |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- | ---------- | -------------------------------------- |
| base                | Minimal package set to define a basic Arch Linux installation |                                                              |                |            |                                        |
| base-devel          | Basic tools to build Arch Linux packages                     | Used when building AUR packages                              |                |            |                                        |
| linux-lts           | The LTS Linux kernel and modules                             |                                                              |                |            |                                        |
| linux-lts-headers   | Headers and scripts for building modules for the LTS Linux kernel |                                                              |                |            |                                        |
| linux               | The Linux kernel and modules                                 | Not necessary if linux-lts is installed                      |                |            |                                        |
| linux-headers       | Headers and scripts for building modules for the Linux kernel | Not necessary if linux-lts-headers is installed              |                |            |                                        |
| linux-firmware      | Firmware files for Linux                                     |                                                              |                |            |                                        |
| grub                | GNU GRand Unified Bootloader (2)                             |                                                              |                |            |                                        |
| efibootmgr          | Linux user-space application to modify the EFI Boot Manager  |                                                              |                |            |                                        |
| btrfs-progs         | Btrfs filesystem utilities                                   | Needed to harness the potential of btrfs                     | x              |            |                                        |
| dosfstools          | DOS filesystem utilities                                     |                                                              | x              |            |                                        |
| mtools              | A collection of utilities to access MS-DOS disks             |                                                              | x              |            |                                        |
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
| cronie              | Daemon that runs specified programs at scheduled times and related tools |                                                              |                | x          |                                        |
| timeshift           | A system restore utility for Linux                           |                                                              |                |            |                                        |
| zsh                 | A very advanced and programmable command interpreter (shell) for UNIX |                                                              |                |            |                                        |
| zsh-completions     | Additional completion definitions for Zsh                    |                                                              | x              |            |                                        |
| zsh-autosuggestions | Fish-like autosuggestions for zsh                            |                                                              | x              |            |                                        |
| pipewire            | Low-latency audio/video router and processor                 |                                                              |                |            |                                        |
| pipewire-alsa       | Low-latency audio/video router and processor - ALSA configuration |                                                              |                |            |                                        |
| pipewire-pulse      | Low-latency audio/video router and processor - PulseAudio replacement |                                                              |                |            |                                        |
| pipewire-jack       | Low-latency audio/video router and processor - JACK replacement |                                                              |                |            |                                        |
| wireplumber         | Session / policy manager implementation for PipeWire         |                                                              | x              |            |                                        |
| noto-fonts-cjk      | Google Noto CJK fonts                                        | For displaying Chinese, Japanese, and Korean fonts           |                |            |                                        |
| yay                 | Yet another yogurt. Pacman wrapper and AUR helper written in go. |                                                              |                |            | https://aur.archlinux.org/packages/yay |



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
| gdm                     | Display manager and login screen                             | May black screen when the Nvidia drivers are used. Try switching to `lightdm` if this happens. The `lightdm-slick-greeter` can be configured using [`lightdm-settings`](https://aur.archlinux.org/packages/lightdm-settings) from AUR. See below for more details. |                | x          |                    |
| gnome-browser-connector | Native browser connector for integration with extensions.gnome.org |                                                              |                |            |                    |

#### Gnome extensions

| Name                                         | Description                                                  | Comment               | URL                                                          |
| -------------------------------------------- | ------------------------------------------------------------ | --------------------- | ------------------------------------------------------------ |
| Dash to Dock                                 | A dock for the Gnome Shell                                   |                       | https://extensions.gnome.org/extension/307/dash-to-dock/     |
| AppIndicator and KStatusNotifierItem Support | Adds AppIndicator, KStatusNotifierItem and legacy Tray icons support to the Shell |                       | https://extensions.gnome.org/extension/615/appindicator-support/ |
| IBus Tweaker                                 | Tweaker of IBus for theme, font, input mode and clipboard history | TODO: guide page url? | https://extensions.gnome.org/extension/2820/ibus-tweaker/    |

### Cinnamon

| Name                  | Description                                                  | Comment                                                      | Unverified (x) | Daemon (x) | 3rd Party Repo URL                                     |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- | ---------- | ------------------------------------------------------ |
| cinnamon              | Linux desktop which provides advanced innovative features and a traditional user experience |                                                              |                |            |                                                        |
| cinnamon-screensaver  | Screensaver designed to integrate well with the Cinnamon desktop | No need to be manually installed. Comes with the `cinnamon` package. Can be manually activated by running `cinnamon-screensaver-command -l`. |                |            |                                                        |
| lightdm               | A lightweight display manager                                | Needs to be configured to use the installed greeter. (ref: https://wiki.archlinux.org/title/LightDM#Greeter) |                | x          |                                                        |
| lightdm-slick-greeter | A slick-looking LightDM greeter                              | Can be directly managed in Cinnamon settings                 |                |            |                                                        |
| numlockx              |                                                              |                                                              |                |            |                                                        |
| gnome-terminal        | The GNOME Terminal Emulator                                  |                                                              |                |            |                                                        |
| gnome-screenshot      | Take pictures of your screen                                 |                                                              |                |            |                                                        |
| nautilus              | Default file manager for GNOME                               | Optional replacement for `nemo`                              |                |            |                                                        |
| mint-l-icons          | A flat, colorful, and modern theme based on Paper and Moka (legacy) |                                                              |                |            | https://aur.archlinux.org/packages/mint-l-icons        |
| mint-l-theme          | A collection of legacy mint themes                           |                                                              |                |            | https://aur.archlinux.org/packages/mint-l-theme        |
| bibata-cursor-theme   | Material Based Cursor Theme                                  |                                                              |                |            | https://aur.archlinux.org/packages/bibata-cursor-theme |
| plank                 | Elegant, simple, clean dock                                  |                                                              |                |            |                                                        |

Workaround for `Super + Space` shortcut doesn't work at first press: https://github.com/ibus/ibus/issues/2474



## GUI applications

| Name           | Description                                             | Comment                                                      | Unverified (x) | Daemon (x) | 3rd Party Repo URL |
| -------------- | ------------------------------------------------------- | ------------------------------------------------------------ | -------------- | ---------- | ------------------ |
| guake          | Drop-down terminal for GNOME                            | TODO: In wayland, create a keyboard shortcut in settings with command `` to enable the shortcut |                |            |                    |
| ibus           | Intelligent input bus for Linux/Unix                    | TODO: how to enable daemon?                                  |                | x          |                    |
| ibus-sunpinyin | IBus Wrapper for sunpinyin                              | `AttributeError: module 'gettext' has no attribute 'bind_textdomain_codeset'`: https://github.com/sunpinyin/sunpinyin/pull/146 |                |            |                    |
| ibus-anthy     | Japanese input method Anthy IMEngine for IBus Framework |                                                              |                |            |                    |



## Additional CLI/GUI applications

| Name             | Description                                                  | Comment | Unverified (x) | Daemon (x) | 3rd Party Repo URL                                  |
| ---------------- | ------------------------------------------------------------ | ------- | -------------- | ---------- | --------------------------------------------------- |
| firefox          | Fast, Private & Safe Web Browser                             |         |                |            |                                                     |
| neofetch         | A CLI system information tool written in BASH that supports displaying images |         |                |            |                                                     |
| steam            | Valve's digital software delivery system                     |         |                |            |                                                     |
| proton-ge-custom | Compatibility tool for Steam Play based on Wine and additional components |         |                |            | https://github.com/GloriousEggroll/proton-ge-custom |
| carla            | Audio Plugin Host                                            |         |                |            |                                                     |



## References

https://wiki.archlinux.org

https://gist.github.com/mjkstra/96ce7a5689d753e7a6bdd92cdc169bae

https://www.youtube.com/watch?v=FxeriGuJKTM