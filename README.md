# Omada Installer
A script to perform a new install of the TP-Link Omada Software Controller on Ubuntu.

This script was created due to convoluted or outdated guides on the web for installing the Omada Controller. The goal was to create a clean, simple script that anyone can run for ease of deployment.

Special thanks to @willquill for his Omada Ubuntu 16.04 guide :)

### Supported releases
- Ubuntu 26.04 LTS
- Ubuntu 24.04 LTS
- Ubuntu 22.04 LTS
- Ubuntu 20.04 LTS

### Recommended specs
- Ubuntu 26.04 LTS
- x86_64 CPU which supports AVX ( Intel Sandy Bridge / AMD Bulldozer or later )
- 1+ CPU cores ( You may want to use 2 cores for the inital install )
- 4+ GB memory
- 8+ GB disk ( You'll need min 4 GB of free space for MongoDB as found by /u/axel2230 )

## Install
Connect via SSH or console, run the following command and enjoy a sip of coffee ;)
> You'll need curl installed which can be installed by running `sudo apt install curl`

```
curl -sS https://raw.githubusercontent.com/Teufelhunden-0311/omada-installer/main/install-omada-controller.sh | sudo bash
```

Once finished, complete the inital setup wizard in your web browser via the URL in the final output.

### A note on Ubuntu 26.04 and MongoDB
As of this writing, MongoDB's apt repo doesn't yet publish a full `mongodb-org` package set for Ubuntu 26.04 (`resolute`). The script detects this and falls back to installing from the `jammy` (22.04) repo instead, which is the same trick that's long been used to run MongoDB on Ubuntu releases newer than its official repo support.

This fallback only happens once, at install time. Your `/etc/apt/sources.list.d/mongodb-org-8.0.list` will stay pinned to `jammy` from then on, and `apt upgrade` will keep pulling MongoDB updates from there indefinitely — it will not automatically switch over once MongoDB does publish native `resolute` packages. A fresh install run after that point will pick up the native repo on its own, but an already-installed system won't. To move an existing install over manually once it's available:

```
sudo sed -i 's/jammy\/mongodb-org/resolute\/mongodb-org/' /etc/apt/sources.list.d/mongodb-org-8.0.list
sudo apt-get update
sudo apt-get install --only-upgrade mongodb-org
```

### Uninstall
To remove the controller, MongoDB, and their associated repos/config/data, run the script with `--uninstall`:

```
curl -sS https://raw.githubusercontent.com/Teufelhunden-0311/omada-installer/main/install-omada-controller.sh | sudo bash -s -- --uninstall
```

This leaves OpenJDK and jsvc installed in case other software on the box depends on them; remove those manually if you don't need them.

### Ansible playbook
As an alternative to using the script, you can run the Ansible playbook instead
> You'll need to have Ansible configured in advance

```
ansible-playbook --ask-become-pass omada-installer-playbook.yaml
```

## Usage
To manage the controller service, use the `tpeap` script as root.
The script is located as a symlink in `/usr/bin`

```
usage: tpeap help
       tpeap (start|stop|status|version)

help       - this screen
start      - start the service(s)
stop       - stop  the service(s)
status     - show the status of the service(s)
version    - show the version of the service(s)
```

## Links
Offical guide: https://www.tp-link.com/us/support/faq/3272/

Guide by @willquill : https://www.reddit.com/r/HomeNetworking/comments/mv1v9d/guide_how_to_set_up_omada_controller_in_ubuntu/ / https://github.com/willquill/omada-ubuntu

Upgrade guide: https://www.tp-link.com/en/omada-sdn/controller-upgrade/
