# Linux Desktop setup

Notes for GNU/Linux desktop machine setup :penguin:

## Preparing installation media of Pop!_OS

* Images and checksums: https://pop.system76.com/
* ISO signing key fingerprint: https://github.com/pop-os/iso#requirements

```shell
mkdir pop-os_22.04_amd64_intel_21 && cd "$_"

wget https://iso.pop-os.org/22.04/amd64/intel/21/pop-os_22.04_amd64_intel_21.iso
wget https://iso.pop-os.org/22.04/amd64/intel/21/SHA256SUMS
wget https://iso.pop-os.org/22.04/amd64/intel/21/SHA256SUMS.gpg

gpg --keyserver hkps://keyserver.ubuntu.com --recv-keys 63C46DF0140D738961429F4E204DD8AEC33A7AFF
gpg --verify SHA256SUMS.gpg SHA256SUMS

sha256sum --check SHA256SUMS

sudo dd if=pop-os_22.04_amd64_intel_21.iso of=<device> bs=4M; sync
```

### Calculating checksum of already created media:

```shell
sudo /sbin/isosize -x <device>
sudo dd if=<device> count=<sector count> bs=<sector size> | sha256sum
```

## Before connecting to the network

### Firewall

```shell
sudo ufw default deny incoming
sudo ufw enable

sudo ufw status verbose
```

### DNS

Cloudflare’s public DNS resolver that blocks malicious content: https://developers.cloudflare.com/1.1.1.1/setup/linux/

* IPv4: `1.1.1.2, 1.0.0.2`
* IPv6: `2606:4700:4700::1112, 2606:4700:4700::1002`

## Other

* Show seconds in GNOME top panel

    ```shell
    gsettings set org.gnome.desktop.interface clock-show-seconds true
    ```
