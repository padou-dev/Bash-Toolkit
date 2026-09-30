# Bash-Toolkit

A collection of bash tools for Linux and macOS.

## hash_ls

Lists the SHA256 hash and size of every file in the current folder, then lets you check any file against VirusTotal.

    #  SHA256                                                                  Size  FileName
    1  275a021bbfb6489e54d471899f7db9d1663fc695ec2fe2a2c4538aabf651fd0f        68 B  eicar.com
    2  28197c5ac7a9c4c4a3a4b2b08bdbdff1a53cd5a79a37c687833c65f3d9612929      4.7 MB  mb.bin

    Enter a file # to check on VirusTotal (Q to quit): 1
    Checking eicar.com...
    65/68 engines flagged malicious, 0 suspicious

> [!NOTE]
> Only the file's **hash** is sent to VirusTotal, never the file itself.

### Features

- SHA256 hash and human-readable size for every file in the folder
- On-demand VirusTotal lookup per file, color-coded: red (malicious), yellow (suspicious or unknown), green (clean)
- Files VirusTotal has never seen are reported as **unknown**, which is not the same as clean
- Option to open the full VirusTotal report in your browser
- Asks for your API key once and stores it privately (readable only by your user)

### Requirements

- bash 3.2 or newer (the version built into macOS works)
- `curl` and `jq` for VirusTotal lookups. Without them, the hash table still works.
- A free VirusTotal API key: sign up at [virustotal.com](https://www.virustotal.com), then go to your profile and open **API key**

Install `curl` and `jq` if they're missing:

    sudo pacman -S curl jq    # Arch / Omarchy
    sudo apt install curl jq  # Debian / Ubuntu
    brew install jq           # macOS (curl is built in)

### Install

    git clone git@github.com:padou-dev/Bash-Toolkit.git ~/Projects/Bash-Toolkit
    mkdir -p ~/.local/bin
    ln -s ~/Projects/Bash-Toolkit/bin/hash_ls ~/.local/bin/hash_ls

`~/.local/bin` must be in your `PATH`. Check with:

    echo "$PATH" | tr ':' '\n' | grep -x "$HOME/.local/bin"

If that prints nothing, add this line to `~/.bashrc` (or `~/.zshrc` on macOS) and open a new terminal:

    export PATH="$HOME/.local/bin:$PATH"

Because it's a symlink, a `git pull` in the repo updates the installed command too.

### Usage

    cd ~/Downloads
    hash_ls

On the first lookup, you'll be asked for your API key. It's saved to `~/.config/bash-toolkit/vt_api_key`.

> [!TIP]
> Setting the `VT_API_KEY` environment variable overrides the saved key.

> [!WARNING]
> The free VirusTotal API allows **4 lookups per minute**. Going over that shows a "Rate limited" message; wait a minute and retry.

### Troubleshooting

| Problem | Fix |
|---|---|
| `hash_ls: command not found` | `~/.local/bin` isn't in your `PATH`. See [Install](#install). |
| `API key rejected` | Delete `~/.config/bash-toolkit/vt_api_key` and run `hash_ls` again to re-enter the key. |
| `Lookups disabled (curl or jq missing)` | Install the missing tool (see [Requirements](#requirements)). |
| `Lookup failed (HTTP status 000)` | No internet connection, or VirusTotal is unreachable. |
| `ACCESS DENIED` in the table | You don't have permission to read that file. |
