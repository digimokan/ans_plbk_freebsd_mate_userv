# ans_plbk_freebsd_mate_userv

Ansible playbook to configure a Thelio Prime Custom PC with FreeBSD and the
MATE DE.

[![Release](https://img.shields.io/github/release/digimokan/ans_plbk_freebsd_mate_userv.svg?label=release)](https://github.com/digimokan/ans_plbk_freebsd_mate_userv/releases/latest "Latest Release Notes")
[![License](https://img.shields.io/badge/license-MIT-blue.svg?label=license)](LICENSE.txt "Project License")

## Table Of Contents

* [Purpose](#purpose)
* [Hardware Parts List](#hardware-parts-list)
* [Hardware Connections](#hardware-connections)
* [PC BIOS Configuration](#pc-bios-configuration)
* [Quick Start](#quick-start)
    * [Install Base System](#install-base-system)
    * [Configure Workstation](#configure-workstation)
    * [Update Workstation](#update-workstation)
* [Vault Variables](#vault-variables)
* [Source Code Layout](#source-code-layout)
* [Integration Tests](#integration-tests)
* [Contributing](#contributing)

## Purpose

Set up a workstation/desktop-PC on a [System76 Thelio Prime Custom](https://system76.com/desktops/thelio-r5-n1/configure)
for normal daily use, for one user:

* Install FreeBSD on two mirrored hard drives.
* Configure the MATE desktop environment.
* Configure a basic set of applications.

## Hardware Parts List

* [System76 Thelio Prime Custom Desktop PC (thelio-r5-n1)](https://system76.com/desktops/thelio-r5-n1/configure)
    * Ryzen 5 9600X 5.4 GHz CPU (6 Cores, 12 Threads)
    * 2x 32 GB DDR5 4800 MHz UDIMM RAM
    * Silent Wings 4 120mm GPU Fan
* [2x Samsung 870 EVO 4TB 2.5 Inch Sata III SSD](https://www.amazon.com/dp/B08QBL36GF)
* [AMD Radeon RX 580 Graphics Card](https://www.amazon.com/dp/B06Y66K3XD)
* [HP Pavilion 22CWA 21.5 in 1080p IPS LED Monitor (Left)](https://www.amazon.com/dp/B015WCV70W)
* [HP Pavilion 22CWA 21.5 in 1080p IPS LED Monitor (Right)](https://www.amazon.com/dp/B015WCV70W)
* [Ergotron LX Dual Monitor Desk Mount](https://www.amazon.com/dp/B07Q1NJ15Q)
* [USX Mount Universal Sound Bar Mount](https://www.amazon.com/dp/B081N42KV3)
    * Attaches to left monitor
* [EMEET C980 Pro Webcam With Built-In Speakers And Mic](https://www.amazon.com/dp/B088BY9PJG)
    * Placed on left monitor
* [Logitech Z207 Powered Speakers](https://www.amazon.com/dp/B074KJ6JQW)
* [90-Degree USB-A Adapter](https://www.amazon.com/dp/B0BZ7NWL2Z)
    * Inserted into Thelio's top-facing USB-A receptacle
* [90-Degree USB-C Adapter](https://www.amazon.com/dp/B0D33RWK75)
    * Inserted into Thelio's top-facing USB-C receptacle
* [Logitech G413 SE Full Size Mechanical Gaming Keyboard](https://www.amazon.com/dp/B08Z6X4NK3)

## Hardware Connections

```
                 BACK OF PC
┌──────────────────────────────────────────────┐
│                                              │
│                                              │
│                                              │
│  ┌──┐ ┌──┐ ┌────┐                            │
│  │U │ │U │ │    │                            │
│  │S │ │S │ │ETH │                            │
│  │B │ │B │ │    │                            │
│  └──┘ └──┘ └────┘                            │
│   C1   A1                                    │
│                                              │
│                                              │
│                                              │
│  ┌──┐    ┌──┐ ┌──┐                           │
│  │H │    │U │ │U │                           │
│  │D │    │S │ │S │                           │
│  │M │    │B │ │B │                           │
│  └I─┘    └──┘ └──┘                           │
│ HDMI-1-3  A2   A3                            │
│                                              │
│  ┌──┐ ┌──┐ ┌──┐ ┌──┐                         │
│  │U │ │U │ │U │ │U │                         │
│  │S │ │S │ │S │ │S │                         │
│  │B │ │B │ │B │ │B │                         │
│  └──┘ └──┘ └──┘ └──┘                         │
│   A4   A5   A6   A7                          │
│                                              │
│  ┌─┐    ┌┐     ┌┐                            │
│  └─┘    └┘     └┘                            │
│ OPTICAL LINE   MIC                           │
│         OUT    IN                            │
│                                              │
│  ┌─┐                                         │
│  └─┘                                         │
│ BIOS FLBK                                    │
│                                              │
│  ┌┐ WIFI   ┌┐ WIFI                           │
│  └┘ ANT    └┘ ANT                            │
│                                              │
│                                              │
│                                              │
│  ┌────────────────────────────────────────┐  │
│  │             GRAPHICS CARD              │  │
│  │ ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐ │  │
│  │ │ HDMI │  │DSPORT│  │DSPORT│  │DSPORT│ │  │
│  │ └──────┘  └──────┘  └──────┘  └──────┘ │  │
│  │  HDMI-1     DP-3      DP-2      DP-1   │  │
│  │ ┌──────┐                               │  │
│  │ │ VGA  │                               │  │
│  │ └──────┘                               │  │
│  └────────────────────────────────────────┘  │
│                                              │
│                                              │
│                                              │
│                                              │
│                                              │
│                                              │
│  ┌────┐ ┌────────┐                           │
│  │PWR │ │  AC    │                           │
│  │SW  │ │ADAPTER │                           │
│  │    │ │        │                           │
│  └────┘ └────────┘                           │
│                                              │
└──────────────────────────────────────────────┘
```

* Top Row
    * `C1 (USB-C 3.2 Gen2, DisplayPort)`: N/A
    * `A1 (USB-A 3.2)`: N/A
    * `ETH`: ethernet to LAN
* Second Row
    * `HDMI-1-3`: N/A
    * `A2 (USB-A 3.2 Gen2)`: N/A
    * `A3 (USB-A 3.2 Gen1)`: webcam
* Third Row
    * `A4 (USB-A 2.0)`: N/A
    * `A5 (USB-A 2.0)`: N/A
    * `A6 (USB-A 2.0)`: mouse
    * `A7 (USB-A 2.0)`: keyboard
* Fourth Row
    * `OPTICAL`: N/A
    * `LINE OUT (3.5 mm)`: speaker data
    * `MIC IN (3.5 mm)`: N/A
* Graphics Card
    * `HDMI-1`: N/A
    * `DP-3 (DisplayPort)`: N/A
    * `DP-2 (DisplayPort)`: right monitor
    * `DP-1 (DisplayPort)`: left monitor

## PC BIOS Configuration

* `Advanced` -> `Onboard Devices Configuration`
    * `WAN Radio`: "Disabled"
    * `BT On/Off`: "Disabled"
* `H/W Monitor`
    * `CPU Fan 1 Setting`: "Silent Mode"
    * `AIO_PUMP Setting`: "Silent Mode"
    * `Chassis Fan 1 Setting`: "Silent Mode"

## Quick Start

### Install Base System

#### Obtain Latest Quarterly Package Files For Realtek NIC

1. Download the latest packages for the following packages onto a USB stick, by
   constructing a URL with the appropriate FreeBSD version and package version.

    * `pkg`
    * `realtek-re-kmod`

Note: find the package version in the "Packages" matrix of the package's
FreshPorts page, where "FreeBSD:nn:quarterly" and "amd64" intersect. The final
URL to access via a browser should look like this:

    ```
    https://pkg.freebsd.org/FreeBSD:14:amd64/latest/All/realtek-re-kmod-1100.00.1403000_1.pkg
    ```

#### Install FreeBSD To The Mirrored Drives

1. Download the latest _RELEASE_ installer image for _amd64_ ("disc1") on the
   [FreeBSD Download Page](https://www.freebsd.org/where/).

2. Write the installer image to a USB stick, as described in
   [FreeBSD Handbook Installation Chapter](https://docs.freebsd.org/en/books/handbook/bsdinstall/#bsdinstall-pre).

3. Insert the USB stick into the target workstation PC, and boot from the
   FreeBSD installer image.

4. Start the guided installation. Select/enable __only__ these options:

    * Host Name: _tempname.machine_.
        * Note: [playbook `machine_hostname` var](../playbook.yml)) sets persistent hostname.
    * ZFS guided installation: _mirror_ (for 2 disks).

#### Install Realtek NIC And Configure DHCP

1. Reboot into the newly-installed system.

2. Insert the USB stick that contains the realtek NIC packages.

3. Create a directory to mount the USB stick on:

    ```shell
    $ mkdir /mnt/usbstick
    ```

4. Mount the USB stick:

    ```shell
    $ mount -t msdosfs /dev/da0s1 /mnt/usbstick
    ```

5. Add all of the realtek packages (starting with the `pkg` package):

    ```shell
    $ pkg add /mnt/usbstick/some-pkg-n.n.n_n.pkg
    ```

6. Configure the newly-installed NIC kernel module to load at boot, per
   [the FreeBSD Handbook](https://docs.freebsd.org/en/books/handbook/network/#config-identify-network-adapter),
   in `/boot/loader.conf`:

    ```text
    if_re_load="YES"
    if_re_name="/boot/modules/if_re.ko"
    ```

7. Reboot the system.

8. Run `ifconfig` and verify that the realtek ethernet NIC is listed as `re0`.

9. Configure the realtek NIC for IPv4 DHCP, per
   [the FreeBSD Handbook](https://docs.freebsd.org/en/books/handbook/network/#config-identify-network-adapter):

    ```text
    sysrc ifconfig_re0="DHCP"
    ```

10. Reboot the system.

11. Verify connectivity to router and internet.

### Configure Workstation

1. Install _git_ package at the root prompt:

   ```shell
   $ pkg install git
   ```

2. Clone project into a local project directory:

   ```shell
   $ git clone --recurse-submodules https://github.com/digimokan/ans_plbk_freebsd_mate_userv.git
   ```

3. Change to the local project directory:

   ```shell
   $ cd ans_plbk_freebsd_mate_userv
   ```

4. Ensure `vault_password.txt` has been created, as desribed in
   [Vault Variables](#vault-variables).

5. For the first run of `configure.sh`, temporarily set the
   [`user_password` vars](../playbook.yml#L6) in `playbook.yml`.

6. Run the [`configure.sh`](../configure.sh) script to configure the workstation:

   ```shell
   $ ./configure.sh -u
   ```
   _NOTE: -u option required for initial run, and after system/package updates._

7. Re-label the two disks in the mirrored install pool:

   ```shell
   $ do_zfs_hw.sh --label-devices-in-install-mirror --zfs-vdev=mirror-0
   ```

8. Reboot the system into the MATE DE.

### Update Workstation

To update the workstation PC at a later time, use the `admin` user account, which
was set up during initial configuration.

1. Log in to the `admin` user account.

2. Change to the pre-provisioned project directory:

   ```shell
   $ cd ans_plbk_freebsd_mate_userv
   ```

3. Optionally, pull in changes to this playbook and its submodules:

   ```shell
   $ git pull --ff-only --recurse-submodules
   ```

4. Run the [`configure.sh`](../configure.sh) script to update the workstation:

   ```shell
   $ ./configure.sh
   ```

## Vault Variables

* The encrypted vault variables are stored in [`vault_enc.yml`](../host_vars/vault_enc.yml).

* Prior to encrypting or decrypting vault variables, the vault password string
  needs to be put into the `vault_password.txt` file
  ([at the root of this repo directory](#source-code-layout)).

* [`playbook.yml`](../playbook.yml) automatically uses the vault password file to
  decrypt vars in [`vault_enc.yml`](../host_vars/vault_enc.yml), via a setting in
  [`ansible.cfg`](../ansible.cfg).

* [`playbook.yml`](../playbook.yml) uses the proxy vars in
  [`vault_clear.yml`](../host_vars/vault_clear.yml), which point to encrypted vars in
  [`vault_enc.yml`](../host_vars/vault_enc.yml).

* To start, encrypt a plaintext-edited vault_enc.yml file:

   ```shell
   $ ansible-vault encrypt host_vars/vault_enc.yml
   ```

* Once vault_enc.yml has been encrypted, do edits/updates in place:

   ```shell
   $ ansible-vault edit host_vars/vault_enc.yml
   ```

* To view the vault_enc.yml vars in read-only mode:

   ```shell
   $ ansible-vault view host_vars/vault_enc.yml
   ```

## Source Code Layout

```
├─┬ ans_plbk_freebsd_mate_userv/
│ │
│ ├─┬ host_vars/
│ │ │
│ │ ├── vault_clear.yml   # proxy vars used by pb, point to vault_enc.yml vars
│ │ └── vault_enc.yml     # encrypted vault variables used by playbook.yml
│ │
│ ├─┬ roles/
│ │ │
│ │ └── ext/              # external (third-party, downloaded) roles
│ │
│ ├── ansible.cfg         # play-wide Ansible meta-config
│ ├── configure.sh        # configures the workstation, post-installation
│ ├── hosts               # Ansible inventory (configured for local host)
│ ├── playbook.yml        # main Ansible playbook
│ ├── requirements.yml    # list of roles (on github/galaxy) to download
│ └── vault_password.txt  # password-string to encrypt and decrypt vault vars
│
```

## Integration Tests

* Play a video in browser(s).
* Check builtin headphone jack on speakers.
* Check microphone input in browser(s): https://www.onlinemictest.com/.
* Check webcam in browser(s): https://www.onlinemictest.com/webcam-test/.
* Check mic and webcam in a google meeting.
* Check mic and webcam in a zoom meeting.
* Print a web page to the printer(s).
* Print a text document to the printer(s).
* Print a PDF document to the printer(s).
* Scan a color image to the scanner(s).
* Scan a text document to the scanner(s).
* List, check, and delete an overnight backup.
* In OBS, record a video with mic and webcam.
* In OBS, record a video with mic and screen capture.
* Check USB storage drive insertion and removal.

## Contributing

* Feel free to report a bug or propose a feature by opening a new
  [Issue](https://github.com/digimokan/ans_plbk_freebsd_mate_userv/issues).
* Follow the project's [Contributing](CONTRIBUTING.md) guidelines.
* Respect the project's [Code Of Conduct](CODE_OF_CONDUCT.md).

