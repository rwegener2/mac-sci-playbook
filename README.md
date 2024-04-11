# Mac Ansible Setup with Scientific Programs

This repository was built from @geerlingguy's [Mac Development Ansible Playbook](https://github.com/geerlingguy/mac-dev-playbook).

This playbook installs and configures most of the software I use on my Mac for my day to day as an earth science researcher. My language of choice is Python, and it also includes Zotero and QGIS.

## Installation
  0. (If using an existing computer) Factory Reset the computer: General > Transfer or Reset > Erase all Content and Settings.

  1. Ensure Apple's command line tools are installed (`xcode-select --install` to launch the installer, or run `/usr/bin/xcodebuild -version` to check if installed). 
  2. Install Ansible. The Ansible docs [for mac](https://docs.ansible.com/ansible/2.9/installation_guide/intro_installation.html#installing-ansible-on-macos ) indicate that the recommended install method is [via pip](https://docs.ansible.com/ansible/2.9/installation_guide/intro_installation.html#from-pip).:

     1. Run the following command to add Python 3 to your $PATH: `export PATH="$PATH:$HOME/Library/Python/3.9/bin:/opt/homebrew/bin"` (If `PATH` warning is given check the Python version in the Path)
     2. Upgrade Pip: `pip3 install --upgrade pip`
     3. Install Ansible: `pip3 install --user ansible`


  Download the configuration repository

  3. Create a folder for the repository `mkdir $HOME/repos && mkdir $HOME/repos/Github && cd $HOME/repos/Github`
  3. Clone or download this repository to your local drive. `git clone https://github.com/rwegener2/mac-sci-playbook.git`

  Set up permissions and run the playbook!

  4. Run `ansible-galaxy install -r requirements.yml` inside this directory to install required Ansible roles. (Should be run from the repo directory: `cd ~/repos/Github/mac-sci-playbook`)
  5. The Python version for pyenv is hardcoded. Update this before running if desired.
  6. Run `ansible-playbook main.yml --ask-become-pass` inside this directory. Enter your macOS account password when prompted for the 'BECOME' password. Zoom and mactex also prompt for a password while installing.

> Note: If some Homebrew commands fail, you might need to agree to Xcode's license or fix some other Brew issue. Run `brew doctor` to see if this is the case.

> Note: The miniconda download link is specific for an Intel x86 chip Mac. If this is what you are using see the [miniconda installer links](https://docs.anaconda.com/free/miniconda/index.html#latest-miniconda-installer-links) and select the most appropriate one for your machine.

### Use with a remote Mac (not maintained)

You can use this playbook to manage other Macs as well; the playbook doesn't even need to be run from a Mac at all! If you want to manage a remote Mac, either another Mac on your network, or a hosted Mac like the ones from [MacStadium](https://www.macstadium.com), you just need to make sure you can connect to it with SSH:

  1. (On the Mac you want to connect to:) Go to System Preferences > Sharing.
  2. Enable 'Remote Login'.

> You can also enable remote login on the command line:
>
>     sudo systemsetup -setremotelogin on

Then edit the `inventory` file in this repository and change the line that starts with `127.0.0.1` to:

```
[ip address or hostname of mac]  ansible_user=[mac ssh username]
```

If you need to supply an SSH password (if you don't use SSH keys), make sure to pass the `--ask-pass` parameter to the `ansible-playbook` command.

### Running a specific set of tagged tasks

You can filter which part of the provisioning process to run by specifying a set of tags using `ansible-playbook`'s `--tags` flag. The tags available are `dotfiles`, `homebrew`, `mas`, `extra-packages` and `osx`.

    ansible-playbook main.yml -K --tags "dotfiles,homebrew"

## Overriding Defaults

Not everyone's development environment and preferred software configuration is the same.

You can override any of the defaults configured in `config.yml` by creating a new `config.yml` file and setting the overrides in that file. For example, you can customize the installed packages and apps with something like:

```yaml
homebrew_installed_packages:
  - cowsay
  - git
  - go

mas_installed_apps:
  - { id: 443987910, name: "1Password" }
  - { id: 498486288, name: "Quick Resizer" }
  - { id: 557168941, name: "Tweetbot" }
  - { id: 497799835, name: "Xcode" }

composer_packages:
  - name: hirak/prestissimo
  - name: drush/drush
    version: '^8.1'

gem_packages:
  - name: bundler
    state: latest

npm_packages:
  - name: webpack

pip_packages:
  - name: mkdocs

configure_dock: true
dockitems_remove:
  - Launchpad
  - TV
dockitems_persist:
  - name: "Sublime Text"
    path: "/Applications/Sublime Text.app/"
    pos: 5
```

Any variable can be overridden in `config.yml`; see the supporting roles' documentation for a complete list of available variables.

## Included Applications / Configuration (Default)

Applications (installed with Homebrew Cask):

  - [Adobe Acrobat Reader](https://get.adobe.com/reader/)
  - [Docker](https://www.docker.com/)
  - [Homebrew](http://brew.sh/)
  - [iTerm2](https://iterm2.com/)
  - [Google Chrome](https://www.google.com/chrome/)
  - [Notion](https://www.notion.so/)
  - [QGIS](https://www.qgis.org/en/site/)
  - [Slack](https://slack.com/)
  - [Spotify](https://open.spotify.com/)
  - [Visual Studio Code](https://code.visualstudio.com/)
  - [Zoom](https://zoom.us/)
  - [Zotero](https://www.zotero.org/)

Packages (installed with Homebrew):

  - openssl
  - readline
  - sqlite3
  - xz
  - zlib
  - tcl-tk
  - git
  - node
  - pyenv
  - wget

My [dotfiles](https://github.com/rwegener2/dotfiles) are also installed into the current user's home directory, including the `.osx` dotfile for configuring many aspects of macOS to my preference. You can disable dotfiles management by setting `configure_dotfiles: no` in your configuration.

## Testing the Playbook 

(Future work - re-enable this from @geerlingguy's original. GH Actions Workflow not currently running on merge)

~~Many people have asked me if I often wipe my entire workstation and start from scratch just to test changes to the playbook. Nope! This project is [continuously tested on GitHub Actions' macOS infrastructure](https://github.com/rwegener2/mac-dev-playbook/actions?query=workflow%3ACI).~~

## Credit to Prior Authors

This project was originally created by [Jeff Geerling](https://www.jeffgeerling.com/) (originally originally inspired by [MWGriffin/ansible-playbooks](https://github.com/MWGriffin/ansible-playbooks)).
