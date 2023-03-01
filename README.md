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
    sudo apt install zsh
    chsh -s $(which zsh)
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

    ```shell
    ZSH_THEME="agnoster"
    ```

* Edit `~/.zshrc` file and the following lines at the end:

    ```shell
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

### Git

Set you username and email in Git:

```shell
git config --global user.name "Orrin the Knight"
git config --global user.email "email@example.com"
```

:warning: Don't forget to replace _"Orrin the Knight"_ and _"email@example.com"_ with your own data.

#### GitHub

* Assign a specific key for authentication in `~/.ssh/config`:

    ```
    Host github.com
      IdentitiesOnly yes
      IdentityFile ~/.ssh/id_rsa_yubikey.pub
    ```

* Verify and pin the GitHub's public SSH key:

    ```shell
    ssh git@github.com -vvv
    ```

    If you are doing this for the first time, you should be asked for the server public key fingerprint.

    You can find it on the official GitHub page: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints:


    ```
    SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8 (RSA)
    SHA256:br9IjFspm1vxR3iA35FWE+4VTyz1hYVLIE2t1/CeyWQ (DSA - deprecated)
    SHA256:p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM (ECDSA)
    SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU (Ed25519)
    ```

    Copy-paste the relevant fingerprint and press <kbd>Enter</kbd>.

### Exa

* Install exa:

    ```shell
    sudo apt install exa
    ```

* Edit `~/.zshrc` file and the following lines at the end:

    ```shell
    # exa
    alias l="exa -a -l -H -S --time-style long-iso -m -g --color-scale --group-directories-first"
    alias lt="exa -a -l -H -S --time-style long-iso -m -g --color-scale --group-directories-first -T"
    ```

### asdf

* Install [asdf](https://asdf-vm.com/guide/getting-started.html):

    ```shell
    git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.11.2
    ```

* Add the following to `~/.zshrc`:

    ```shell
    # asdf
    . "$HOME/.asdf/asdf.sh"
    # append completions to fpath
    fpath=(${ASDF_DIR}/completions $fpath)
    # initialise completions with ZSH's compinit
    autoload -Uz compinit && compinit
    ```

* Install plugins:

    ```shell
    # necessary if you have already installed asdf plugins some time ago
    asdf plugin-update --all

    # install last version of Erlang (required for Elixir)
    sudo apt install libwxgtk-webview3.0-gtk3-dev libssh-dev build-essential autoconf libncurses5-dev xsltproc fop libxml2-utils
    asdf plugin add erlang https://github.com/asdf-vm/asdf-erlang.git
    asdf list-all erlang    # check the latest version
    asdf install erlang 25.2.3
    asdf global erlang 25.2.3

    # install last version of Elixir
    asdf plugin-add elixir https://github.com/asdf-vm/asdf-elixir.git
    asdf list-all elixir    # check the latest version
    asdf install elixir 1.14.3
    asdf global elixir 1.14.3

    # install last version of Rust
    asdf plugin-add rust https://github.com/asdf-community/asdf-rust.git
    asdf list-all rust    # check the latest version
    asdf install rust 1.67.1
    asdf global rust 1.67.1
    ```

### Other

* Show weekday and seconds in GNOME top panel

    ```shell
    gsettings set org.gnome.desktop.interface clock-show-weekday true
    gsettings set org.gnome.desktop.interface clock-show-seconds true
    ```

### VS Code

* Install VS Code: https://code.visualstudio.com/#alt-downloads.

* Press <kbd>Cmd</kbd> + <kbd>p</kbd>, type `> Preferences: Open User Settings (JSON)`, press <kbd>Enter</kbd>, and paste the following settings to `settings.json`:

    ```json
    {
        "workbench.startupEditor": "none",
        "telemetry.telemetryLevel": "off",
        "settingsSync.keybindingsPerPlatform": false,
        "json.schemaDownload.enable": false,
        "update.showReleaseNotes": false,
        "workbench.settings.enableNaturalLanguageSearch": false,
        "workbench.enableExperiments": false,
        "workbench.cloudChanges.continueOn": "off",
        "workbench.cloudChanges.autoResume": "off",
        "editor.tabSize": 2,
        "editor.autoClosingBrackets": "never",
        "editor.rulers": [
            120
        ],
        "explorer.confirmDragAndDrop": false,
        "explorer.confirmDelete": false,
        "files.insertFinalNewline": true,
        "files.trimTrailingWhitespace": true,
        "files.trimFinalNewlines": true,
        "editor.autoClosingOvertype": "never",
        "editor.autoClosingQuotes": "never",
        "editor.minimap.enabled": false
    }
    ```
