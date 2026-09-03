# Lenovo WWAN Unlock v4.0.0 for Arch Linux

Unofficial Arch Linux packaging for [Lenovo WWAN Unlock](https://github.com/lenovo/lenovo-wwan-unlock), based on the official [`v4.0.0`](https://github.com/lenovo/lenovo-wwan-unlock/releases/tag/v4.0.0) release.

This repository contains only the Arch packaging files. The Lenovo software is downloaded directly from the official upstream release during the build and verified with a SHA-256 checksum.

## Status

- Package version: `4.0.0-1`
- Successfully built and installed on Arch Linux
- Tested on a ThinkPad X1 Carbon Gen 14 with a Foxconn SDX61 (`17cb:0308`, T99W696)
- FCC unlock, SAR configuration and 5G connectivity verified with China Telecom

The package installs all FCC unlock hooks, libraries, SAR configuration files and DEV OTA files included in the upstream v4.0.0 release. Hardware other than the configuration listed above has not been personally tested.

## Installation

Install the tools required for building AUR-style packages:

```bash
sudo pacman -S --needed base-devel git
```

Clone and build the package as a regular user:

```bash
git clone https://github.com/Nk-YMZ/Arch-lenovo-wwan-unlock.git
cd Arch-lenovo-wwan-unlock
makepkg -si
```

Do not run `makepkg` as root.

After installation, enable the Lenovo SAR configuration service and restart ModemManager:

```bash
sudo systemctl enable --now lenovo-cfgservice.service
sudo systemctl restart ModemManager.service
sudo reboot
```

## Verification

After rebooting, check that ModemManager can see the modem:

```bash
mmcli -L
```

Check the package and service:

```bash
pacman -Q lenovo-wwan-unlock
systemctl status lenovo-cfgservice.service --no-pager
```

APN and mobile broadband connection profiles are managed separately by NetworkManager and are not created by this package.

## Optional upstream suspend fix

The upstream suspend workaround applies to the following modems and requires ModemManager 1.23.2 or newer:

- Fibocom L860-GL-16 (`8086:7560`)
- Fibocom FM350 (`14c3:4d75`)
- Quectel RM520N-GL (`1eac:1007`)
- Quectel EM160R-GL (`1eac:100d`)
- Foxconn SDX61 (`17cb:0308`)

First inspect the existing service definition:

```bash
systemctl cat ModemManager.service
```

Then create an override:

```bash
sudo systemctl edit ModemManager.service
```

For the standard Arch Linux ModemManager service, enter:

```ini
[Service]
ExecStart=
ExecStart=/usr/bin/ModemManager --test-low-power-suspend-resume
```

Apply it with:

```bash
sudo systemctl daemon-reload
sudo systemctl restart ModemManager.service
```

## Rolling Wireless RW101R-GL

The Rolling Wireless RW101R-GL modem (`33f8:0301` / `33f8:0302`) additionally requires `fastboot`:

```bash
sudo pacman -S android-tools
```

## Uninstallation

```bash
sudo pacman -Rns lenovo-wwan-unlock
```

Any NetworkManager connection profiles or manually created ModemManager systemd overrides remain user configuration and must be removed separately if no longer required.

## Disclaimer

This project is not affiliated with Lenovo, Arch Linux or the maintainer of the existing AUR package. The upstream Lenovo software remains subject to the license terms included in the official release.
