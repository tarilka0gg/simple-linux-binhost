# Simple Linux binhost

Binary packages for [Simple Linux](https://github.com/tarilka0gg/simple-linux) that Gentoo's own binhost does not have: the
Wayland desktop (`niri`, `noctalia`), `ghostty`, and the packages whose USE the installer changes (`mesa`, `gtk`, `pipewire`,
`libqalculate`, ...). With them an install compiles nothing: without them Portage builds `mesa` and its dependencies on the user's machine.

The installer ([gentoo-installer](https://github.com/tarilka0gg/gentoo-installer)) sets this up by itself. These are the steps for
doing it by hand on a Gentoo `amd64` (OpenRC, profile `default/linux/amd64/23.0`) system:

```
# /etc/portage/binrepos.conf/simple-linux.conf
[simple-linux]
priority = 10
sync-uri = https://github.com/tarilka0gg/simple-linux-binhost/releases/download/binhost
verify-signature = true
```

Trust the signing key (importing it is not enough: without recalculating the trust database Portage reports a good signature from an
untrusted key and rejects the package):

```
getuto
gpg --homedir /etc/portage/gnupg --batch --import binhost-signing.asc
echo 'F6307025824C538E8C43844590899877B0925EC1:6:' | gpg --homedir /etc/portage/gnupg --batch --import-ownertrust
gpg --homedir /etc/portage/gnupg --batch --check-trustdb
```

and set `FEATURES="getbinpkg"`. Key fingerprint: `F6307025824C538E8C43844590899877B0925EC1`. Every package carries its signature; the
`Packages` index is not signed.

## What binary packages need to match

Portage uses a binary only when its USE flags match yours. These were built with the flags in
[`iso/binhost/spec`](https://github.com/tarilka0gg/gentoo-installer/tree/master/iso/binhost/spec) and `VIDEO_CARDS="amdgpu intel nouveau radeon radeonsi"`, generic
x86-64 (no `-march=native`), `CPU_FLAGS_X86` left at the profile default. A machine configured differently compiles those packages instead; that is expected, not an error.
The packages for the overlay (GURU) need `~amd64`.

## Packages

- acct-group/gamemode-0-r2
- acct-group/seat-0-r1
- app-misc/fastfetch-2.65.2-r1
- app-misc/jq-1.8.2
- app-misc/yazi-26.1.22
- app-text/poppler-26.05.0-r1
- dev-cpp/sdbus-c++-2.3.1
- dev-libs/appstream-1.0.6
- dev-libs/libdbusmenu-16.04.0-r4
- dev-libs/libevdev-1.13.6
- dev-libs/libgrapheme-2.0.2
- dev-libs/libxml2-compat-2.13.9
- dev-libs/wayland-1.25.0-r1
- dev-python/pygments-2.21.0
- dev-qt/qt5compat-6.11.2
- dev-qt/qtbase-6.11.2-r2
- dev-qt/qtdeclarative-6.11.2-r1
- dev-qt/qttools-6.11.2
- dev-util/wayland-scanner-1.25.0
- games-util/gamemode-1.8.2
- games-util/mangohud-0.8.4
- gnome-base/gvfs-1.60.3
- gui-apps/fuzzel-1.14.1
- gui-apps/noctalia-5.2.1
- gui-apps/qt6ct-0.11
- gui-libs/gtk-4.20.4-r1
- gui-libs/gtk4-layer-shell-1.1.1-r1
- gui-wm/niri-26.04
- kde-frameworks/kwindowsystem-6.29.0
- media-gfx/exiv2-0.28.9
- media-gfx/gimp-3.2.4
- media-gfx/imv-5.0.1
- media-libs/gegl-0.4.70
- media-libs/imgui-1.91.6-r1
- media-libs/implot-0.16-r3
- media-libs/libjxl-0.12.0
- media-libs/mesa-26.1.8
- media-libs/vulkan-loader-1.4.350.0
- media-plugins/gst-plugins-meta-1.26.11
- media-video/ffmpeg-compat-7.1.5
- media-video/mpv-0.41.0-r2
- media-video/pipewire-1.6.8
- media-video/vlc-4.0.0_pre20260625
- media-video/wireplumber-0.5.15
- net-analyzer/nmap-7.991
- net-analyzer/traceroute-2.1.5
- net-im/telegram-desktop-bin-7.1.5
- net-wireless/iwd-3.12
- net-wireless/wpa_supplicant-2.11-r4
- sci-libs/libqalculate-5.10.0
- sys-apps/dbus-1.16.2
- sys-apps/ethtool-7.0
- sys-apps/usbutils-019
- sys-apps/xdg-desktop-portal-gtk-1.15.3
- sys-auth/seatd-0.9.3-r1
- sys-block/gparted-1.7.0
- sys-fs/btrfs-progs-7.1
- sys-fs/ddrescue-1.30
- sys-fs/exfatprogs-1.4.3
- sys-fs/lvm2-2.03.39
- sys-fs/mdadm-4.6-r1
- sys-fs/ntfs3g-2026.9.28
- sys-fs/udisks-2.11.2
- sys-process/lsof-4.99.7
- x11-libs/gtk+-3.24.52
- x11-libs/libX11-1.8.13
- x11-libs/libXi-1.8.3
- x11-libs/libXrender-0.9.12
- x11-libs/libXtst-1.2.5
- x11-libs/libxkbcommon-1.13.2
- x11-misc/compose-tables-1.8.13
- x11-terms/ghostty-1.3.1
- x11-terms/ghostty-terminfo-1.3.1
- x11-themes/adw-gtk3-6.5
- x11-themes/adwaita-icon-theme-50.0
- xfce-base/exo-4.20.0-r1
- xfce-base/libxfce4ui-4.20.2
- xfce-base/libxfce4windowing-4.20.7
- xfce-base/thunar-4.20.10
- xfce-base/tumbler-4.20.2
- xfce-base/xfce4-panel-4.20.8

The `libX11`, `libXi`, `libXrender`, `libXtst`, `wayland`, `wayland-scanner`, `libevdev`, `btrfs-progs` and `compose-tables` entries are the versions of the pinned tree snapshot without the 32-bit ABI: Gentoo's own binhost has dropped these versions, so without them an install compiled each of them (about 100 s of a 750 s install in a VM).

Two builds of `media-libs/mesa` are in the index: the default one (`VIDEO_CARDS` as above) and one with `virgl`, which a QEMU/virtio-gpu
machine asks for (a client takes the one whose flags match).

Excluded on purpose: packages whose licence does not allow redistributing binaries (`linux-firmware` is `RESTRICT=bindist`) and the
build-only toolchains (`rust-bin`, `zig-bin`).

## Kernels

The `kernels` release of this repository holds the kernels the Simple Linux installer picks from by hardware profile: `Packages` (one `CPV:` per build), `<combo>.vmlinuz`, `<combo>-modules.tar.xz` and, for NVIDIA builds, `<combo>-devel.tar.xz`, as flat assets (ten builds: generic x86-64 v2/v3 and Raptor Lake). The installer reads them by default; `GENTOO_STORE_BINHOST_URL` points it elsewhere (a web directory with the files under `kernels/` also works). `iso/binhost/publish-kernels.sh` in gentoo-installer publishes a store. The generic builds are made for real hardware (NVMe and SATA built in, no virtio): a VM disk has to be NVMe or SATA. For hardware that none of these ten fits, the installer can build a kernel on the new system from the [kernel-matrix](https://github.com/tarilka0gg/kernel-matrix) (an option on its Optimization page, off by default); that kernel is not published here.

## The tree snapshot

Binary packages are built against one Portage tree; a newer tree has newer versions, no binary for them, and Portage compiles them. So the
release also carries that tree (`gentoo/` and the GURU overlay, `tree.tar.zst`, from 2026-10-09). The installer unpacks it into `/var/db/repos`
instead of running `emerge-webrsync`, and falls back to webrsync if the file cannot be fetched. Using the day's tree on a machine installed by
hand works too, but whatever is newer than the snapshot is compiled.

## Building and licences

Built by [`iso/binhost/build.sh`](https://github.com/tarilka0gg/gentoo-installer/tree/master/iso/binhost) in a clean stage3 from the Gentoo tree
of 2026-10-09 and the GURU overlay, then signed. The packages are covered by their upstream licences (GPL, LGPL, MIT, MPL, Apache and
others, listed in the `Packages` index); the corresponding sources are the ebuilds of the Gentoo tree and GURU and the upstream
sources they fetch (`emerge --fetchonly` retrieves them), and can also be requested through an issue here.

The scripts and configuration in this repository are MIT-licensed; the binary packages are not.
