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

## After connecting to the network

### Zsh

* Install Zsh and set it as a default shell:

    ```shell
    brew install zsh zsh-completions
    chsh -s /bin/zsh
    ```

    :warning: You may need to logout and login again to load Zsh shell.

* Install [Oh My Zsh](https://ohmyz.sh/):

    ```shell
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
    ```

* Install Powerline Fonts:

    ```shell
    sudo apt install fonts-powerline
    ```

* Edit `~/.zshrc` file and update `ZSH_THEME=` to:

    ```
    ZSH_THEME="agnoster"
    ```

* Edit `~/.zshrc` file and the following lines at the end:

    ```
    export EDITOR='vim'
    
    DEFAULT_USER=`whoami`
    
    HISTFILE=$HOME/.zsh_history
    HISTSIZE=10000
    SAVEHIST=10000
    
    setopt APPEND_HISTORY           # append rather than overwrite history file.
    setopt EXTENDED_HISTORY         # save timestamp and runtime information
    setopt HIST_EXPIRE_DUPS_FIRST   # allow dups, but expire old ones when I hit HISTSIZE
    setopt HIST_FIND_NO_DUPS        # don't find duplicates in history
    setopt HIST_IGNORE_ALL_DUPS     # ignore duplicate commands regardless of commands in between
    setopt HIST_IGNORE_DUPS         # ignore duplicate commands
    setopt HIST_REDUCE_BLANKS       # leave blanks out
    setopt HIST_SAVE_NO_DUPS        # don't save duplicates
    setopt INC_APPEND_HISTORY       # write after each command
    setopt SHARE_HISTORY            # share history between sessions
    ```

### Exa

* Install exa:

    ```shell
    sudo apt install exa
    ```

* Edit `~/.zshrc` file and the following lines at the end:

    ```
    # exa
    alias l="exa -a -l -H -S --time-style long-iso -m -g --color-scale --group-directories-first"
    alias lt="exa -a -l -H -S --time-style long-iso -m -g --color-scale --group-directories-first -T"
    ```

### Other

* Show weekday and seconds in GNOME top panel

    ```shell
    gsettings set org.gnome.desktop.interface clock-show-weekday true
    gsettings set org.gnome.desktop.interface clock-show-seconds true
    ```
