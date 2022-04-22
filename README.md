<img src="https://raw.githubusercontent.com/othyn/dotfiles-playbook/master/files/Mac-Dev-Playbook-Logo.png" width="250" height="156" alt="Mac Dev Playbook Logo" />

# Mac Development Ansible Playbook

[![CI][badge-gh-actions]][link-gh-actions]

This playbook installs and configures most of the software I use on my Mac for web and software development. Some things in macOS are slightly difficult to automate, so I still have a few manual installation steps, but at least it's all documented here.

This repo is based from Jeff Geerling's incredible [mac-dev-playbook](https://github.com/geerlingguy/mac-dev-playbook) repo.

## TODO

- Ansible sudo softwareupdate --install-rosetta
- Install crontab

## Pre-requirements

These were the steps that I _personally_ had to undertake prior to running any of the following setup steps to get the
machine how I like it. These may differ based on your preferences.

1. Go through initial OOTB setup and create a new macOS account!
2. (Setup account)
   Set Password
   Touch ID setup, all options checked
   Logged in to Apple ID
   Tamed iCloud
   Set Monterey user profile picture to Memoji so its animated!

Battery > Optimise video streaming while on battery (cannot set via defaults)

Users & Groups > Ben Tindall > Allow user to reset password using Apple ID (cannot set via defaults)

Software Update > Advanced > [x] Check for updates (cannot set via defaults)
Software Update > Advanced > [x] Download new updates when available (cannot set via defaults)
Software Update > Advanced > [x] Install system data files and security updates (cannot set via defaults)

Security & Privacy > General > Use your Apple Watch to unlock apps and your Mac (cannot set via defaults)
Security & Privacy > FileVault > Turn On FileVault… (allow iCloud account to unlock the disk) (cannot set via defaults)

Displays > Night Shift… > Schedule: (Sunset to Sunrise) (cannot set via defaults)

Installed brew from brew.sh (Xcode tools + shitty 1.2MB/s download…)
Set FireFox as default browser
Logged into FireFox
AirDrop’d ~/.ssh over to this new machine from the old one
mkdir ~/git
git clone git@github.com:othyn/mac-dev-playbook.git ~/git/mac-dev-playbook
brew install --cask visual-studio-code
code ~/git/mac-dev-playbook
Turned on settings sync with my GitHub account
Installed the MonoLisa font via AirDrop
git clone git@github.com:othyn/dotfiles.git ~/git/dotfiles
Cleaned out the forked dot files repo, copying across old dot files via AirDrop and placing them into the new repo
Copied across .aws via AirDrop
brew install --cask sublime-merge
Brew install --cask rocket
Set licence key for Rocket
Copied across iStat config under File > Import settings… from stat.ismp
Turned on iCloud messsage sync in Messages > Preferences > iMessage > Enable Messages in iCloud
Turned off iCloud Photos
Battery > Power Adaptor > Prevent your Mac from automatically sleeping when the display is off

## Installation

1. Install [Homebrew](https://brew.sh/):

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

2. Install [Ansible](https://formulae.brew.sh/formula/ansible):

```sh
brew install ansible
```

3. Clone or download this repository to your local drive.
4. Run `ansible-galaxy install -r requirements.yml` inside this directory to install required Ansible roles.
5. Run `ansible-playbook main.yml --ask-become-pass` inside this directory. Enter your macOS account password when prompted for the 'BECOME' password.

> Note: If some Homebrew commands fail, you might need to agree to Xcode's license or fix some other Brew issue. Run `brew doctor` to see if this is the case.

### Running a specific set of tagged tasks

You can filter which part of the provisioning process to run by specifying a set of tags using `ansible-playbook`'s `--tags` flag. The tags available are `ohmyzsh`, `dotfiles`, `homebrew`, `mas`, `composer`, `yarn`, `pip` and `macos`.

```sh
ansible-playbook main.yml -K --tags "dotfiles,homebrew"
```

## Overriding Defaults

Not everyone's development environment and preferred software configuration is the same.

You can override any of the defaults configured in `default.config.yml` by creating a `config.yml` file and setting the overrides in that file. For example, you can customize the installed packages and apps with something like:

```yaml
homebrew_installed_packages:
  - cowsay
  - git
  - go

mas_installed_apps:
  - {id: 443987910, name: '1Password'}
  - {id: 498486288, name: 'Quick Resizer'}
  - {id: 557168941, name: 'Tweetbot'}
  - {id: 497799835, name: 'Xcode'}

composer_packages:
  - name: hirak/prestissimo
  - name: drush/drush
    version: '^8.1'

gem_packages:
  - name: bundler
    state: latest

yarn_packages:
  - name: webpack

pip_packages:
  - name: mkdocs

configure_dock: true
dockitems_remove:
  - Launchpad
  - TV
dockitems_persist:
  - name: 'Sublime Text'
    path: '/Applications/Sublime Text.app/'
    pos: 5
```

Any variable can be overridden in `config.yml`; see the supporting roles' documentation for a complete list of available variables.

## Included Applications / Configuration (Default)

Applications (installed with Homebrew Cask):

- [ChromeDriver](https://sites.google.com/chromium.org/driver/)
- [Docker](https://www.docker.com/)
- [Dropbox](https://www.dropbox.com/)
- [Firefox](https://www.mozilla.org/en-US/firefox/new/)
- [Google Chrome](https://www.google.com/chrome/)
- [Handbrake](https://handbrake.fr/)
- [Homebrew](http://brew.sh/)
- [LICEcap](http://www.cockos.com/licecap/)
- [LimeChat](http://limechat.net/mac/)
- [MacVim](http://macvim-dev.github.io/macvim/)
- [nvALT](http://brettterpstra.com/projects/nvalt/)
- [Sequel Ace](https://sequel-ace.com) (MySQL client)
- [Skitch](https://evernote.com/skitch/)
- [Slack](https://slack.com/)
- [Sublime Text](https://www.sublimetext.com/)
- [Transmit](https://panic.com/transmit/) (S/FTP client)
- [Vagrant](https://www.vagrantup.com/)

Packages (installed with Homebrew):

- autoconf
- bash-completion
- doxygen
- gettext
- gifsicle
- git
- go
- gpg
- hub
- httpie
- iperf
- libevent
- sqlite
- mcrypt
- nmap
- node
- nvm
- php
- ssh-copy-id
- cowsay
- readline
- openssl
- pv
- wget

My [dotfiles](https://github.com/othyn/dotfiles) are also installed into the current user's home directory, including the `.macos` dotfile for configuring many aspects of macOS for better performance and ease of use. You can disable dotfiles management by setting `configure_dotfiles: no` in your configuration.

Finally, there are a few other preferences and settings added on for various apps and services.

## Future additions

### Things that still need to be done manually

It's my hope that I can get the rest of these things wrapped up into Ansible playbooks soon, but for now, these steps need to be completed manually (assuming you already have Xcode and Ansible installed, and have run this playbook).

1. Set JJG-Term as the default Terminal theme (it's installed, but not set as default automatically).
2. Install all the apps that aren't yet in this setup (see below).
3. Remap Caps Lock to Escape (requires macOS Sierra 10.12.1+).
4. Set trackpad tracking rate.
5. Set mouse tracking rate.
6. Configure extra Mail and/or Calendar accounts (e.g. Google, Exchange, etc.).

### Configuration to be added:

- I have vim configuration in the repo, but I still need to add the actual installation:
  ```
  mkdir -p ~/.vim/autoload
  mkdir -p ~/.vim/bundle
  cd ~/.vim/autoload
  curl https://raw.githubusercontent.com/tpope/vim-pathogen/master/autoload/pathogen.vim > pathogen.vim
  cd ~/.vim/bundle
  git clone git://github.com/scrooloose/nerdtree.git
  ```

## Testing the Playbook

Many people have asked me if I often wipe my entire workstation and start from scratch just to test changes to the playbook. Nope! Instead, I posted instructions for how I build a [Mac OS X VirtualBox VM](https://github.com/geerlingguy/mac-osx-virtualbox-vm), on which I can continually run and re-run this playbook to test changes and make sure things work correctly.

Additionally, this project is [continuously tested on GitHub Actions' macOS infrastructure](https://github.com/geerlingguy/mac-dev-playbook/actions?query=workflow%3ACI).

## Ansible for DevOps

Check out [Ansible for DevOps](https://www.ansiblefordevops.com/), which teaches you how to automate almost anything with Ansible.

## Author

This project was created by [Jeff Geerling](https://www.jeffgeerling.com/) (originally inspired by [MWGriffin/ansible-playbooks](https://github.com/MWGriffin/ansible-playbooks)).
